# Using a PostgreSQL Database as a Source for AWS DMS<a name="CHAP_Source.PostgreSQL"></a>

You can migrate data from one or many PostgreSQL databases using AWS DMS \(AWS DMS\)\. With a PostgreSQL database as a source, you can migrate data to either another PostgreSQL database or one of the other supported databases\. AWS DMS supports a PostgreSQL version 9\.4 database as a source for on\-premises databases, databases on an EC2 instance, and databases on an Amazon RDS DB instance\. 

You can use SSL to encrypt connections between your PostgreSQL endpoint and the replication instance\. For more information on using SSL with a PostgreSQL endpoint, see [Using SSL With AWS Database Migration Service](CHAP_Security.SSL.md)\.

For a homogeneous migration from a PostgreSQL database to a PostgreSQL database on AWS, the following is true:

+ JSONB columns on the source are migrated to JSONB columns on the target\. 

+ JSON columns are migrated as JSON columns on the target\. 

+ HSTORE columns are migrated as HSTORE columns on the target\. 

For a heterogeneous migration with PostgreSQL as the source and a different database engine as the target, the situation is different\. In this case, JSONB, JSON, and HSTORE columns are converted to the AWS DMS intermediate type of NCLOB and then translated to the corresponding NCLOB column type on the target\. In this case, AWS DMS treats JSONB data as if it were a LOB column\. During the full load phase of a migration, the target column must be nullable\.

AWS DMS supports change data capture \(CDC\) for PostgreSQL tables with primary keys\. If a table doesn't have a primary key, the write\-ahead logs \(WAL\) don't include a before image of the database row and AWS DMS can't update the table\. 

AWS DMS supports CDC on Amazon RDS PostgreSQL databases when the DB instance is configured to use logical replication\. Amazon RDS supports logical replication for a PostgreSQL DB instance version 9\.4\.9 and higher and 9\.5\.4 and higher\.

For additional details on working with PostgreSQL databases and AWS DMS, see the following sections\. 


+ [Prerequisites for Using a PostgreSQL Database as a Source for AWS DMS](#CHAP_Source.PostgreSQL.Prerequisites)
+ [Security Requirements When Using a PostgreSQL Database as a Source for AWS DMS](#CHAP_Source.PostgreSQL.Security)
+ [Limitations on Using a PostgreSQL Database as a Source for AWS DMS](#CHAP_Source.PostgreSQL.Limitations)
+ [Setting Up an Amazon RDS PostgreSQL DB Instance as a Source](#CHAP_Source.PostgreSQL.RDSPostgreSQL)
+ [Removing AWS DMS Artifacts from a PostgreSQL Source Database](#CHAP_Source.PostgreSQL.CleanUp)
+ [Additional Configuration Settings When Using a PostgreSQL Database as a Source for AWS DMS](#CHAP_Source.PostgreSQL.Advanced)
+ [Extra Connection Attributes When Using PostgreSQL as a Source for AWS DMS](#CHAP_Source.PostgreSQL.ConnectionAttrib)
+ [Source Data Types for PostgreSQL](#CHAP_Source.PostgreSQL.DataTypes)

## Prerequisites for Using a PostgreSQL Database as a Source for AWS DMS<a name="CHAP_Source.PostgreSQL.Prerequisites"></a>

For a PostgreSQL database to be a source for AWS DMS, you should do the following: 

+ Use a PostgreSQL database that is version 9\.4\.x or later\.

+ Grant superuser permissions for the user account specified for the PostgreSQL source database\.

+ Add the IP address of the AWS DMS replication server to the `pg_hba.conf` configuration file\.

+ Set the following parameters and values in the `postgresql.conf` configuration file:

  + Set `wal_level = logical`

  + Set `max_replication_slots >=1`

    The `max_replication_slots` value should be set according to the number of tasks that you want to run\. For example, to run five tasks you need to set a minimum of five slots\. Slots open automatically as soon as a task starts and remain open even when the task is no longer running\. You need to manually delete open slots\.

  + Set `max_wal_senders >=1`

    The `max_wal_senders` parameter sets the number of concurrent tasks that can run\.

  + Set `wal_sender_timeout =0`

    The `wal_sender_timeout` parameter terminates replication connections that are inactive longer than the specified number of milliseconds\. Although the default is 30 seconds, we recommend that you set this parameter to zero, which disables the timeout mechanism\.

## Security Requirements When Using a PostgreSQL Database as a Source for AWS DMS<a name="CHAP_Source.PostgreSQL.Security"></a>

The only security requirement when using PostgreSQL as a source is that the user account specified must be a registered user in the PostgreSQL database\.

## Limitations on Using a PostgreSQL Database as a Source for AWS DMS<a name="CHAP_Source.PostgreSQL.Limitations"></a>

The following change data capture \(CDC\) limitations apply when using PostgreSQL as a source for AWS DMS:

+ A captured table must have a primary key\. If a table doesn't have a primary key, AWS DMS ignores DELETE and UPDATE record operations for that table\.

+ AWS DMS ignores an attempt to update a primary key segment\. In these cases, the target identifies the update as one that didn't update any rows\. However, because the results of updating a primary key in PostgreSQL are unpredictable, no records are written to the exceptions table\.

+ AWS DMS doesn't support the **Start Process Changes from Timestamp** run option\.

+ AWS DMS supports full load and change processing on Amazon RDS for PostgreSQL\. For information on how to prepare a PostgreSQL DB instance and to set it up for using CDC, see [Setting Up an Amazon RDS PostgreSQL DB Instance as a Source](#CHAP_Source.PostgreSQL.RDSPostgreSQL)\.

+ Replication of multiple tables with the same name but where each name has a different case \(for example table1, TABLE1, and Table1\) can cause unpredictable behavior, and therefore AWS DMS doesn't support it\.

+ AWS DMS supports change processing of CREATE, ALTER, and DROP DDL statements for tables unless the tables are held in an inner function or procedure body block or in other nested constructs\.

  For example, the following change is not captured:

  ```
  CREATE OR REPLACE FUNCTION attu.create_distributors1() RETURNS void
  LANGUAGE plpgsql
  AS $$
  BEGIN
  create table attu.distributors1(did serial PRIMARY KEY,name
  varchar(40) NOT NULL);
  END;
  $$;
  ```

+ AWS DMS doesn't support change processing of TRUNCATE operations\.

+ AWS DMS doesn’t support change processing to set column default values \(using the ALTER COLUMN SET DEFAULT clause on ALTER TABLE statements\)\.

+ AWS DMS doesn’t support change processing to set column nullability \(using the ALTER COLUMN \[SET|DROP\] NOT NULL clause on ALTER TABLE statements\)\.

+ AWS DMS doesn't support replication of partitioned tables\. When a partitioned table is detected, the following occurs:

  + The endpoint reports a list of parent and child tables\.

  + AWS DMS creates the table on the target as a regular table with the same properties as the selected tables\.

  + If the parent table in the source database has the same primary key value as its child tables, a "duplicate key" error is generated\.

**Note**  
To replicate partitioned tables from a PostgreSQL source to a PostgreSQL target, you first need to manually create the parent and child tables on the target\. Then you define a separate task to replicate to those tables\. In such a case, you set the task configuration to **Truncate before loading**\.

## Setting Up an Amazon RDS PostgreSQL DB Instance as a Source<a name="CHAP_Source.PostgreSQL.RDSPostgreSQL"></a>

You can use an Amazon RDS for PostgreSQL DB instance or Read Replica as a source for AWS DMS\. A DB instance can be used for both full\-load and CDC \(ongoing replication\); a Read Replica can only be used for full\-load tasks and cannot be used for CDC\.

You use the AWS master user account for the PostgreSQL DB instance as the user account for the PostgreSQL source endpoint for AWS DMS\. The master user account has the required roles that allow it to set up change data capture \(CDC\)\. If you use an account other than the master user account, the account must have the rds\_superuser role and the rds\_replication role\. The rds\_replication role grants permissions to manage logical slots and to stream data using logical slots\.

If you don't use the master user account for the DB instance, you must create several objects from the master user account for the account that you use\. For information about creating the needed objects, see [Migrating an Amazon RDS for PostgreSQL Database Without Using the Master User Account](#CHAP_Source.PostgreSQL.RDSPostgreSQL.NonMasterUser)\.

### Using CDC with an RDS for PostgreSQL DB Instance<a name="CHAP_Source.PostgreSQL.RDSPostgreSQL.CDC"></a>

You can use PostgreSQL's native logical replication feature to enable CDC during a database migration of an Amazon RDS PostgreSQL DB instance\. This approach reduces downtime and ensures that the target database is in sync with the source PostgreSQL database\. Amazon RDS supports logical replication for a PostgreSQL DB instance version 9\.4\.9 and higher and 9\.5\.4 and higher\.

**Note**  
Amazon RDS for PostgreSQL Read Replicas cannot be used for CDC \(ongoing replication\)\.

To enable logical replication for an RDS PostgreSQL DB instance, do the following:

+ In general, use the AWS master user account for the PostgreSQL DB instance as the user account for the PostgreSQL source endpoint\. The master user account has the required roles that allow it to set up CDC\. If you use an account other than the master user account, you must create several objects from the master account for the account that you use\. For more information, see [Migrating an Amazon RDS for PostgreSQL Database Without Using the Master User Account](#CHAP_Source.PostgreSQL.RDSPostgreSQL.NonMasterUser)\.

+ Set the `rds.logical_replication` parameter in your DB parameter group to 1\. This is a static parameter that requires a reboot of the DB instance for the parameter to take effect\. As part of applying this parameter, AWS DMS sets the `wal_level`, `max_wal_senders`, `max_replication_slots`, and `max_connections` parameters\. These parameter changes can increase WAL generation, so you should only set the `rds.logical_replication` parameter when you are using logical slots\.

### Migrating an Amazon RDS for PostgreSQL Database Without Using the Master User Account<a name="CHAP_Source.PostgreSQL.RDSPostgreSQL.NonMasterUser"></a>

If you don't use the master user account for the Amazon RDS PostgreSQL DB instance that you are using as a source, you need to create several objects to capture data definition language \(DDL\) events\. You create these objects in the account other than the master account and then create a trigger in the master user account\.

**Note**  
If you set the `captureDDL` parameter to *N* on the source endpoint, you don't have to create the following table and trigger on the source database\.

Use the following procedure to create these objects\. The user account other than the master account is referred to as the `NoPriv` account in this procedure\.

**To create objects**

1. Choose the schema where the objects are to be created\. The default schema is `public`\. Ensure that the schema exists and is accessible by the `NoPriv` account\. 

1. Log in to the PostgreSQL DB instance using the `NoPriv` account\.

1. Create the table `awsdms_ddl_audit` by running the following command, replacing `<objects_schema>` in the code following with the name of the schema to use\.

   ```
   create table <objects_schema>.awsdms_ddl_audit
   (
     c_key    bigserial primary key,
     c_time   timestamp,    -- Informational
     c_user   varchar(64),  -- Informational: current_user
     c_txn    varchar(16),  -- Informational: current transaction
     c_tag    varchar(24),  -- Either 'CREATE TABLE' or 'ALTER TABLE' or 'DROP TABLE'
     c_oid    integer,      -- For future use - TG_OBJECTID
     c_name   varchar(64),  -- For future use - TG_OBJECTNAME
     c_schema varchar(64),  -- For future use - TG_SCHEMANAME. For now - holds current_schema
     c_ddlqry  text         -- The DDL query associated with the current DDL event
   )
   ```

1. Create the function `awsdms_intercept_ddl` by running the following command, replacing `<objects_schema>` in the code following with the name of the schema to use\.

   ```
   CREATE OR REPLACE FUNCTION <objects_schema>.awsdms_intercept_ddl()
     RETURNS event_trigger
   LANGUAGE plpgsql
   SECURITY DEFINER
     AS $$
     declare _qry text;
   BEGIN
     if (tg_tag='CREATE TABLE' or tg_tag='ALTER TABLE' or tg_tag='DROP TABLE') then
            SELECT current_query() into _qry;
            insert into <objects_schema>.awsdms_ddl_audit
            values
            (
            default,current_timestamp,current_user,cast(TXID_CURRENT()as varchar(16)),tg_tag,0,'',current_schema,_qry
            );
            delete from <objects_schema>.awsdms_ddl_audit;
   end if;
   END;
   $$;
   ```

1. Log out of the `NoPriv` account and log in with an account that has the `rds_superuser` role assigned to it\.

1. Create the event trigger `awsdms_intercept_ddl` by running the following command\.

   ```
   CREATE EVENT TRIGGER awsdms_intercept_ddl ON ddl_command_end 
   EXECUTE PROCEDURE <objects_schema>.awsdms_intercept_ddl();
   ```

When you have completed the procedure preceding, you can create the AWS DMS source endpoint using the `NoPriv` account\.

### Using CDC with an Amazon RDS for PostgreSQL DB Instance<a name="CHAP_Source.PostgreSQL.RDSPostgreSQL.CDC"></a>

You can use PostgreSQL's native logical replication feature to enable CDC during a database migration of an Amazon RDS PostgreSQL DB instance\. This approach reduces downtime and ensures that the target database is in sync with the source PostgreSQL database\. Amazon RDS supports logical replication for a PostgreSQL DB instance version 9\.4\.9 and higher and 9\.5\.4 and higher\.

To enable logical replication for an RDS PostgreSQL DB instance, do the following:

+ In general, use the AWS master user account for the PostgreSQL DB instance as the user account for the PostgreSQL source endpoint\. The master user account has the required roles that allow the it to set up CDC\. If you use an account other than the master user account, you must create several objects from the master account for the account that you use\. For more information, see [Migrating an Amazon RDS for PostgreSQL Database Without Using the Master User Account](#CHAP_Source.PostgreSQL.RDSPostgreSQL.NonMasterUser)\.

+ Set the `rds.logical_replication` parameter in your DB parameter group to 1\. This is a static parameter that requires a reboot of the DB instance for the parameter to take effect\. As part of applying this parameter, AWS DMS sets the `wal_level`, `max_wal_senders`, `max_replication_slots`, and `max_connections` parameters\. These parameter changes can increase WAL generation, so you should only set the `rds.logical_replication` parameter when you are using logical slots\.

## Removing AWS DMS Artifacts from a PostgreSQL Source Database<a name="CHAP_Source.PostgreSQL.CleanUp"></a>

To capture DDL events, AWS DMS creates various artifacts in the PostgreSQL database when a migration task starts\. When the task completes, you might want to remove these artifacts\. To remove the artifacts, issue the following statements \(in the order they appear\), where `{AmazonRDSMigration}` is the schema in which the artifacts were created:

```
drop event trigger awsdms_intercept_ddl;
```

The event trigger doesn't belong to a specific schema\.

```
drop function {AmazonRDSMigration}.awsdms_intercept_ddl()
drop table {AmazonRDSMigration}.awsdms_ddl_audit
drop schema {AmazonRDSMigration}
```

**Note**  
Dropping a schema should be done with extreme caution, if at all\. Never drop an operational schema, especially not a public one\.

## Additional Configuration Settings When Using a PostgreSQL Database as a Source for AWS DMS<a name="CHAP_Source.PostgreSQL.Advanced"></a>

You can add additional configuration settings when migrating data from a PostgreSQL database in two ways:

+ You can add values to the extra connection attribute to capture DDL events and to specify the schema in which the operational DDL database artifacts are created\. For more information, see [Extra Connection Attributes When Using PostgreSQL as a Source for AWS DMS](#CHAP_Source.PostgreSQL.ConnectionAttrib)\.

+ You can override connection string parameters\. Select this option if you need to do either of the following:

  + Specify internal AWS DMS parameters\. Such parameters are rarely required and are therefore not exposed in the user interface\.

  + Specify pass\-through \(passthru\) values for the specific database client\. AWS DMS includes pass\-through parameters in the connection sting passed to the database client\.

## Extra Connection Attributes When Using PostgreSQL as a Source for AWS DMS<a name="CHAP_Source.PostgreSQL.ConnectionAttrib"></a>

You can use extra connection attributes to configure your PostgreSQL source\. You specify these settings when you create the source endpoint\. Multiple extra connection attribute settings should be separated by a semicolon\.

The following table shows the extra connection attributes you can use when using PostgreSQL as a source for AWS DMS:

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Source.PostgreSQL.html)

## Source Data Types for PostgreSQL<a name="CHAP_Source.PostgreSQL.DataTypes"></a>

The following table shows the PostgreSQL source data types that are supported when using AWS DMS and the default mapping to AWS DMS data types\.

For information on how to view the data type that is mapped in the target, see the section for the target endpoint you are using\.

For additional information about AWS DMS data types, see [Data Types for AWS Database Migration Service](CHAP_Reference.DataTypes.md)\.


|  PostgreSQL Data Types  |  AWS DMS Data Types  | 
| --- | --- | 
|  INTEGER  |  INT4  | 
|  SMALLINT  |  INT2  | 
|  BIGINT  |  INT8  | 
|  NUMERIC \(p,s\)  |  If precision is from 0 through 38, then use NUMERIC\. If precision is 39 or greater, then use STRING\.  | 
|  DECIMAL\(P,S\)  |  If precision is from 0 through 38, then use NUMERIC\. If precision is 39 or greater, then use STRING\.  | 
|  REAL  |  REAL4  | 
|  DOUBLE  |  REAL8  | 
|  SMALLSERIAL  |  INT2  | 
|  SERIAL  |  INT4  | 
|  BIGSERIAL  |  INT8  | 
|  MONEY  |  NUMERIC\(38,4\) Note: The MONEY data type is mapped to FLOAT in SQL Server\.  | 
|  CHAR  |  WSTRING \(1\)  | 
|  CHAR\(N\)  |  WSTRING \(n\)  | 
|  VARCHAR\(N\)  |  WSTRING \(n\)  | 
|  TEXT  |  NCLOB  | 
|  BYTEA  |  BLOB  | 
|  TIMESTAMP  |  TIMESTAMP  | 
|  TIMESTAMP \(z\)  |  TIMESTAMP  | 
|  TIMESTAMP with time zone  |  Not supported  | 
|  DATE  |  DATE  | 
|  TIME  |  TIME  | 
|  TIME \(z\)  |  TIME  | 
|  INTERVAL  |  STRING \(128\)—1 YEAR, 2 MONTHS, 3 DAYS, 4 HOURS, 5 MINUTES, 6 SECONDS  | 
|  BOOLEAN  |  CHAR \(5\) false or true  | 
|  ENUM  |  STRING \(64\)  | 
|  CIDR  |  STRING \(50\)  | 
|  INET  |  STRING \(50\)  | 
|  MACADDR  |  STRING \(18\)  | 
|  BIT \(n\)  |  STRING \(n\)  | 
|  BIT VARYING \(n\)  |  STRING \(n\)  | 
|  UUID  |  STRING  | 
|  TSVECTOR  |  CLOB  | 
|  TSQUERY  |  CLOB  | 
|  XML  |  CLOB  | 
|  POINT  |  STRING \(255\) "\(x,y\)"  | 
|  LINE  |  STRING \(255\) "\(x,y,z\)"  | 
|  LSEG  |  STRING \(255\) "\(\(x1,y1\),\(x2,y2\)\)"  | 
|  BOX  |  STRING \(255\) "\(\(x1,y1\),\(x2,y2\)\)"  | 
|  PATH  |  CLOB "\(\(x1,y1\),\(xn,yn\)\)"  | 
|  POLYGON  |  CLOB "\(\(x1,y1\),\(xn,yn\)\)"  | 
|  CIRCLE  |  STRING \(255\) "\(x,y\),r"  | 
|  JSON  |  NCLOB  | 
|  JSONB  |  NCLOB  | 
|  ARRAY  |  NCLOB  | 
|  COMPOSITE  |  NCLOB  | 
|  HSTORE  |  NCLOB  | 
|  INT4RANGE  |  STRING \(255\)  | 
|  INT8RANGE  |  STRING \(255\)  | 
|  NUMRANGE  |  STRING \(255\)  | 
|  STRRANGE  |  STRING \(255\)  | 
