# Using a PostgreSQL database as an AWS DMS source<a name="CHAP_Source.PostgreSQL"></a>

You can migrate data from one or many PostgreSQL databases using AWS DMS\. With a PostgreSQL database as a source, you can migrate data to either another PostgreSQL database or one of the other supported databases\. AWS DMS supports a PostgreSQL version 9\.4 and later \(for versions 9\.x\), 10\.x, 11\.x, 12\.x, 13\.x and 14\.x database as a source for these types of databases: 
+ On\-premises databases
+ Databases on an Amazon EC2 instance
+ Databases on an Amazon RDS DB instance
+ Databases on an DB instance based on Amazon Aurora PostgreSQL\-Compatible Edition
+ Databases on an DB instance based on Amazon Aurora PostgreSQL\-Compatible Serverless Edition

**Note**  
DMS supports Amazon Aurora PostgreSQL—Serverless V1 as a source for Full load only\. But you can use Amazon Aurora PostgreSQL—Serverless V2 as a source for Full load, Full load \+ CDC, and CDC only tasks\.


|  PostgreSQL source version  |  AWS DMS version to use  | 
| --- | --- | 
|  9\.x, 10\.x, 11\.x, 12\.x  |  Use any available AWS DMS version\.  | 
|  13\.x  |  Use AWS DMS version 3\.4\.3 and above\.  | 
|  14\.x  |  Use AWS DMS version 3\.4\.7 and above\.  | 

You can use Secure Socket Layers \(SSL\) to encrypt connections between your PostgreSQL endpoint and the replication instance\. For more information on using SSL with a PostgreSQL endpoint, see [Using SSL with AWS Database Migration Service](CHAP_Security.SSL.md)\.

As an additional security requirement when using PostgreSQL as a source, the user account specified must be a registered user in the PostgreSQL database\.

To configure a PostgreSQL database as an AWS DMS source endpoint, do the following:
+ Create a PostgreSQL user with appropriate permissions to provide AWS DMS access to your PostgreSQL source database\.
**Note**  
If your PostgreSQL source database is self\-managed, see [Working with self\-managed PostgreSQL databases as a source in AWS DMS](#CHAP_Source.PostgreSQL.Prerequisites) for more information\.
If your PostgreSQL source database is managed by Amazon RDS, see [Working with AWS\-managed PostgreSQL databases as DMS sources](#CHAP_Source.PostgreSQL.RDSPostgreSQL) for more information\.
+ Create a PostgreSQL source endpoint that conforms with your chosen PostgreSQL database configuration\.
+ Create a task or set of tasks to migrate your tables\.

  To create a full\-load\-only task, no further endpoint configuration is needed\.

  Before you create a task for change data capture \(a CDC\-only or full\-load and CDC task\), see [Enabling CDC using a self\-managed PostgreSQL database as a AWS DMS source](#CHAP_Source.PostgreSQL.Prerequisites.CDC) or [Enabling CDC with an AWS\-managed PostgreSQL DB instance with AWS DMS](#CHAP_Source.PostgreSQL.RDSPostgreSQL.CDC)\.

**Topics**
+ [Working with self\-managed PostgreSQL databases as a source in AWS DMS](#CHAP_Source.PostgreSQL.Prerequisites)
+ [Working with AWS\-managed PostgreSQL databases as DMS sources](#CHAP_Source.PostgreSQL.RDSPostgreSQL)
+ [Enabling change data capture \(CDC\) using logical replication](#CHAP_Source.PostgreSQL.Security)
+ [Using native CDC start points to set up a CDC load of a PostgreSQL source](#CHAP_Source.PostgreSQL.v10)
+ [Migrating from PostgreSQL to PostgreSQL using AWS DMS](#CHAP_Source.PostgreSQL.Homogeneous)
+ [Removing AWS DMS artifacts from a PostgreSQL source database](#CHAP_Source.PostgreSQL.CleanUp)
+ [Additional configuration settings when using a PostgreSQL database as a DMS source](#CHAP_Source.PostgreSQL.Advanced)
+ [Using the MapBooleanAsBoolean PostgreSQL endpoint setting](#CHAP_Source.PostgreSQL.ConnectionAttrib.Endpointsetting)
+ [Endpoint settings when using PostgreSQL as a DMS source](#CHAP_Source.PostgreSQL.ConnectionAttrib)
+ [Limitations on using a PostgreSQL database as a DMS source](#CHAP_Source.PostgreSQL.Limitations)
+ [Source data types for PostgreSQL](#CHAP_Source-PostgreSQL-DataTypes)

## Working with self\-managed PostgreSQL databases as a source in AWS DMS<a name="CHAP_Source.PostgreSQL.Prerequisites"></a>

With a self\-managed PostgreSQL database as a source, you can migrate data to either another PostgreSQL database, or one of the other target databases supported by AWS DMS\. The database source can be an on\-premises database or a self\-managed engine running on an Amazon EC2 instance\. You can use a DB instance for both full\-load tasks and change data capture \(CDC\) tasks\.

### Prerequisites to using a self\-managed PostgreSQL database as a AWS DMS source<a name="CHAP_Source.PostgreSQL.Prerequisites.SelfManaged"></a>

Before migrating data from a self\-managed PostgreSQL source database, do the following: 
+ Make sure that you use a PostgreSQL database that is version 9\.4\.x or later\.
+ For full\-load plus CDC tasks or CDC\-only tasks, grant superuser permissions for the user account specified for the PostgreSQL source database\. The user account needs superuser permissions to access replication\-specific functions in the source\. For full\-load only tasks, the user account needs SELECT permissions on tables to migrate them\.
+ Add the IP address of the AWS DMS replication server to the `pg_hba.conf` configuration file and enable replication and socket connections\. An example follows\.

  ```
              # Replication Instance
              host all all 12.3.4.56/00 md5
              # Allow replication connections from localhost, by a user with the
              # replication privilege.
              host replication dms 12.3.4.56/00 md5
  ```

  PostgreSQL's `pg_hba.conf` configuration file controls client authentication\. \(HBA stands for host\-based authentication\.\) The file is traditionally stored in the database cluster's data directory\. 
+ If you're configuring a database as a source for logical replication using AWS DMS see [Enabling CDC using a self\-managed PostgreSQL database as a AWS DMS source](#CHAP_Source.PostgreSQL.Prerequisites.CDC)

**Note**  
Some AWS DMS transactions are idle for some time before the DMS engine uses them again\. By using the parameter `idle_in_transaction_session_timeout` in PostgreSQL versions 9\.6 and later, you can cause idle transactions to time out and fail\. Don't end idle transactions when you use AWS DMS\. 

### Enabling CDC using a self\-managed PostgreSQL database as a AWS DMS source<a name="CHAP_Source.PostgreSQL.Prerequisites.CDC"></a>

AWS DMS supports change data capture \(CDC\) using logical replication\. To enable logical replication of a self\-managed PostgreSQL source database, set the following parameters and values in the `postgresql.conf` configuration file:
+ Set `wal_level = logical`\.
+ Set `max_replication_slots` to a value greater than 1\.

  Set the `max_replication_slots` value according to the number of tasks that you want to run\. For example, to run five tasks you set a minimum of five slots\. Slots open automatically as soon as a task starts and remain open even when the task is no longer running\. Make sure to manually delete open slots\.
+ Set `max_wal_senders` to a value greater than 1\.

  The `max_wal_senders` parameter sets the number of concurrent tasks that can run\.
+ The `wal_sender_timeout` parameter ends replication connections that are inactive longer than the specified number of milliseconds\. The default is 60000 milliseconds \(60 seconds\)\. Setting the value to 0 \(zero\) disables the timeout mechanism, and is a valid setting for DMS\.

  When setting `wal_sender_timeout` to a non\-zero value, DMS requires a minimum of 10000 milliseconds \(10 seconds\), and fails if the value is less than 10000\. Keep the value less than 5 minutes to avoid causing a delay during a Multi\-AZ failover of a DMS replication instance\.

Some parameters are static, and you can only set them at server start\. Any changes to their entries in the configuration file \(for a self\-managed database\) or DB parameter group \(for an RDS for PostgreSQL database\) are ignored until the server is restarted\. For more information, see the [PostgreSQL documentation](https://www.postgresql.org/docs/current/intro-whatis.html)\.

For more information about enabling CDC, see [Enabling change data capture \(CDC\) using logical replication](#CHAP_Source.PostgreSQL.Security)\.

## Working with AWS\-managed PostgreSQL databases as DMS sources<a name="CHAP_Source.PostgreSQL.RDSPostgreSQL"></a>

You can use an AWS\-managed PostgreSQL DB instance as a source for AWS DMS\. You can perform both full\-load tasks and change data capture \(CDC\) tasks using an AWS\-managed PostgreSQL source\. 

### Prerequisites for using an AWS\-managed PostgreSQL database as a DMS source<a name="CHAP_Source.PostgreSQL.RDSPostgreSQL.Prerequisites"></a>

Before migrating data from an AWS\-managed PostgreSQL source database, do the following:
+ Use the AWS master user account for the PostgreSQL DB instance as the user account for the PostgreSQL source endpoint for AWS DMS\. The master user account has the required roles that allow it to set up CDC\. If you use an account other than the master user account, the account must have the `rds_superuser` role and the `rds_replication` role\. The `rds_replication` role grants permissions to manage logical slots and to stream data using logical slots\.

  If you don't use the master user account for the DB instance, make sure to create several objects from the master user account for the account that you use\. For information about creating these, see [Migrating an Amazon RDS for PostgreSQL database without using the master user account](#CHAP_Source.PostgreSQL.RDSPostgreSQL.NonMasterUser)\.
+ If your source database is in a virtual private cloud \(VPC\), choose the VPC security group that provides access to the DB instance where the database resides\. This is needed for the DMS replication instance to connect successfully to the source DB instance\. When the database and DMS replication instance are in same VPC, add the appropriate security group to its own inbound rules\.

**Note**  
Some AWS DMS transactions are idle for some time before the DMS engine uses them again\. By using the parameter `idle_in_transaction_session_timeout` in PostgreSQL versions 9\.6 and later, you can cause idle transactions to time out and fail\. Don't end idle transactions when you use AWS DMS\.

### Enabling CDC with an AWS\-managed PostgreSQL DB instance with AWS DMS<a name="CHAP_Source.PostgreSQL.RDSPostgreSQL.CDC"></a>

AWS DMS supports CDC on Amazon RDS PostgreSQL databases when the DB instance is configured to use logical replication\. The following table summarizes the logical replication compatibility of each AWS\-managed PostgreSQL version\. 

You can't use RDS PostgreSQL read replicas for CDC \(ongoing replication\)\.


|  PostgreSQL version  |  AWS DMS full load support   |  AWS DMS CDC support  | 
| --- | --- | --- | 
|  Aurora PostgreSQL version 2\.1 with PostgreSQL 10\.5 compatibility \(or lower\)  |  Yes  |  No  | 
|  Aurora PostgreSQL version 2\.2 with PostgreSQL 10\.6 compatibility \(or higher\)   |  Yes  |  Yes  | 
|  RDS for PostgreSQL with PostgreSQL 10\.21 compatibility \(or higher\)  |  Yes  |  Yes  | 

**To enable logical replication for an RDS PostgreSQL DB instance**

1. Use the AWS master user account for the PostgreSQL DB instance as the user account for the PostgreSQL source endpoint\. The master user account has the required roles that allow it to set up CDC\. 

   If you use an account other than the master user account, make sure to create several objects from the master account for the account that you use\. For more information, see [Migrating an Amazon RDS for PostgreSQL database without using the master user account](#CHAP_Source.PostgreSQL.RDSPostgreSQL.NonMasterUser)\.

1. Set the `rds.logical_replication` parameter in your DB CLUSTER parameter group to 1\. This static parameter requires a reboot of the DB instance to take effect\. As part of applying this parameter, AWS DMS sets the `wal_level`, `max_wal_senders`, `max_replication_slots`, and `max_connections` parameters\. These parameter changes can increase write ahead log \(WAL\) generation, so only set `rds.logical_replication` when you use logical replication slots\.

1. The `wal_sender_timeout` parameter ends replication connections that are inactive longer than the specified number of milliseconds\. The default is 60000 milliseconds \(60 seconds\)\. Setting the value to 0 \(zero\) disables the timeout mechanism, and is a valid setting for DMS\.

   When setting `wal_sender_timeout` to a non\-zero value, DMS requires a minimum of 10000 milliseconds \(10 seconds\), and fails if the value is between 0 and 10000\. Keep the value less than 5 minutes to avoid causing a delay during a Multi\-AZ failover of a DMS replication instance\.

1.  Ensure the value of the `max_worker_processes` parameter in your DB Cluster Parameter Group is equal to, or higher than the total combined values of `max_logical_replication_workers`, `autovacuum_max_workers`, and `max_parallel_workers`\. A high number of background worker processes might impact application workloads on small instances\. So, monitor performance of your database if you set `max_worker_processes` higher than the default value\.

### Migrating an Amazon RDS for PostgreSQL database without using the master user account<a name="CHAP_Source.PostgreSQL.RDSPostgreSQL.NonMasterUser"></a>

In some cases, you might not use the master user account for the Amazon RDS PostgreSQL DB instance that you are using as a source\. In these cases, you create several objects to capture data definition language \(DDL\) events\. You create these objects in the account other than the master account and then create a trigger in the master user account\.

**Note**  
If you set the `captureDDLs` endpoint setting to `false` on the source endpoint, you don't have to create the following table and trigger on the source database\.

Use the following procedure to create these objects\.

**To create objects**

1. Choose the schema where the objects are to be created\. The default schema is `public`\. Ensure that the schema exists and is accessible by the `OtherThanMaster` account\. 

1. Log in to the PostgreSQL DB instance using the user account other than the master account, here the `OtherThanMaster` account\.

1. Create the table `awsdms_ddl_audit` by running the following command, replacing `objects_schema` in the following code with the name of the schema to use\.

   ```
   CREATE TABLE objects_schema.awsdms_ddl_audit
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
   );
   ```

1. Create the function `awsdms_intercept_ddl` by running the following command, replacing `objects_schema` in the code following with the name of the schema to use\.

   ```
   CREATE OR REPLACE FUNCTION objects_schema.awsdms_intercept_ddl()
     RETURNS event_trigger
   LANGUAGE plpgsql
   SECURITY DEFINER
     AS $$
     declare _qry text;
   BEGIN
     if (tg_tag='CREATE TABLE' or tg_tag='ALTER TABLE' or tg_tag='DROP TABLE') then
            SELECT current_query() into _qry;
            insert into objects_schema.awsdms_ddl_audit
            values
            (
            default,current_timestamp,current_user,cast(TXID_CURRENT()as varchar(16)),tg_tag,0,'',current_schema,_qry
            );
            delete from objects_schema.awsdms_ddl_audit;
   end if;
   END;
   $$;
   ```

1. Log out of the `OtherThanMaster` account and log in with an account that has the `rds_superuser` role assigned to it\.

1. Create the event trigger `awsdms_intercept_ddl` by running the following command\.

   ```
   CREATE EVENT TRIGGER awsdms_intercept_ddl ON ddl_command_end 
   EXECUTE PROCEDURE objects_schema.awsdms_intercept_ddl();
   ```

1. Make sure that all users and roles that access these events have the necessary DDL permissions\. For example:

   ```
   grant all on public.awsdms_ddl_audit to public;
   grant all on public.awsdms_ddl_audit_c_key_seq to public;
   ```

When you have completed the procedure preceding, you can create the AWS DMS source endpoint using the `OtherThanMaster` account\.

**Note**  
These events are triggered by `CREATE TABLE`, `ALTER TABLE`, and `DROP TABLE` statements\.

## Enabling change data capture \(CDC\) using logical replication<a name="CHAP_Source.PostgreSQL.Security"></a>

You can use PostgreSQL's native logical replication feature to enable change data capture \(CDC\) during database migration for PostgreSQL sources\. You can use this feature with a self\-managed PostgreSQL and also an Amazon RDS for PostgreSQL SQL DB instance\. This approach reduces downtime and help ensure that the target database is in sync with the source PostgreSQL database\.

AWS DMS supports CDC for PostgreSQL tables with primary keys\. If a table doesn't have a primary key, the write\-ahead logs \(WAL\) don't include a before image of the database row\. In this case, DMS can't update the table\. Here, you can use additional configuration settings and use table replica identity as a workaround\. However, this approach can generate extra logs\. We recommend that you use table replica identity as a workaround only after careful testing\. For more information, see [Additional configuration settings when using a PostgreSQL database as a DMS source](#CHAP_Source.PostgreSQL.Advanced)\.

**Note**  
REPLICA IDENTITY FULL is supported with a logical decoding plugin, but isn't supported with a pglogical plugin\. For more information, see [pglogical documentation](https://github.com/2ndQuadrant/pglogical#primary-key-or-replica-identity-required)\.

For full load and CDC and CDC only tasks, AWS DMS uses logical replication slots to retain WAL logs for replication until the logs are decoded\. On restart \(not resume\) for a full load and CDC task or a CDC task, the replication slot gets recreated\.

**Note**  
For logical decoding, DMS uses either test\_decoding or pglogical plugin\. If the pglogical plugin is available on a source PostgreSQL database, DMS creates a replication slot using pglogical, otherwise a test\_decoding plugin is used\. For more information about the test\_decoding plugin, see [PostgreSQL Documentation](https://www.postgresql.org/docs/9.4/test-decoding.html)\.  
If the database parameter `max_slot_wal_keep_size` is set to a non default value, and the `restart_lsn` of a replication slot falls behind the current LSN by more than this size, the DMS task fails due to removal of required WAL files\.

### Configuring the pglogical plugin<a name="CHAP_Source.PostgreSQL.Security.Pglogical"></a>

Implemented as a PostgreSQL extension, the pglogical plugin is a logical replication system and model for selective data replication\. The following table identifies source PostgreSQL database versions that support the pglogical plugin\.


|  PostgreSQL source   |  Supports pglogical  | 
| --- | --- | 
|  Self\-managed PostgreSQL 9\.4 or higher  |  Yes  | 
|  Amazon RDS PostgreSQL 9\.5 or lower  |  No  | 
|  Amazon RDS PostgreSQL 9\.6 or higher  |  Yes  | 
|  Aurora PostgreSQL 1\.x till 2\.5\.x  |  No  | 
|  Aurora PostgreSQL 2\.6\.x or higher  |  Yes  | 
|  Aurora PostgreSQL 3\.3\.x or higher  |  Yes  | 

Before configuring pglogical for use with AWS DMS, first enable logical replication for change data capture \(CDC\) on your PostgreSQL source database\. 
+ For information about enabling logical replication for CDC on *self\-managed* PostgreSQL source databases, see [Enabling CDC using a self\-managed PostgreSQL database as a AWS DMS source](#CHAP_Source.PostgreSQL.Prerequisites.CDC)
+ For information about enabling logical replication for CDC on *AWS\-managed* PostgreSQL source databases, see [Enabling CDC with an AWS\-managed PostgreSQL DB instance with AWS DMS](#CHAP_Source.PostgreSQL.RDSPostgreSQL.CDC)\.

After logical replication is enabled on your PostgreSQL source database, use the following steps to configure pglogical for use with DMS\.

**To use the pglogical plugin for logical replication on a PostgreSQL source database with AWS DMS**

1. Create a pglogical extension on your source PostgreSQL database:

   1. Set the correct parameter:
      + For self\-managed PostgreSQL databases, set the database parameter `shared_preload_libraries= 'pglogical'`\.
      + For PostgreSQL on Amazon RDS and Amazon Aurora PostgreSQL\-Compatible Edition databases, set the parameter `shared_preload_libraries` to `pglogical` in the same RDS parameter group\.

   1. Restart your PostgreSQL source database\.

   1. On the PostgreSQL database, run the command, `create extension pglogical;`

1. Run the following command to verify that pglogical installed successfully:

   `select * FROM pg_catalog.pg_extension`

You can now create a AWS DMS task that performs change data capture for your PostgreSQL source database endpoint\.

**Note**  
If you don't enable pglogical on your PostgreSQL source database, AWS DMS uses the `test_decoding` plugin by default\. When pglogical is enabled for logical decoding, AWS DMS uses pglogical by default\. But you can set the extra connection attribute, `PluginName` to use the `test_decoding` plugin instead\.

## Using native CDC start points to set up a CDC load of a PostgreSQL source<a name="CHAP_Source.PostgreSQL.v10"></a>

To enable native CDC start points with PostgreSQL as a source, set the `slotName` extra connection attribute to the name of an existing logical replication slot when you create the endpoint\. This logical replication slot holds ongoing changes from the time of endpoint creation, so it supports replication from a previous point in time\. 

PostgreSQL writes the database changes to WAL files that are discarded only after AWS DMS successfully reads changes from the logical replication slot\. Using logical replication slots can protect logged changes from being deleted before they are consumed by the replication engine\. 

However, depending on rate of change and consumption, changes being held in a logical replication slot can cause elevated disk usage\. We recommend that you set space usage alarms in the source PostgreSQL instance when you use logical replication slots\. For more information on setting the `slotName` extra connection attribute, see [Endpoint settings when using PostgreSQL as a DMS source](#CHAP_Source.PostgreSQL.ConnectionAttrib)\.

The following procedure walks through this approach in more detail\.

**To use a native CDC start point to set up a CDC load of a PostgreSQL source endpoint**

1. Identify the logical replication slot used by an earlier replication task \(a parent task\) that you want to use as a start point\. Then query the `pg_replication_slots` view on your source database to make sure that this slot doesn't have any active connections\. If it does, resolve and close them before proceeding\.

   For the following steps, assume that your logical replication slot is `abc1d2efghijk_34567890_z0yx98w7_6v54_32ut_1srq_1a2b34c5d67ef`\. 

1. Create a new source endpoint that includes the following extra connection attribute setting\.

   ```
   slotName=abc1d2efghijk_34567890_z0yx98w7_6v54_32ut_1srq_1a2b34c5d67ef;
   ```

1. Create a new CDC\-only task using the AWS CLI or AWS DMS API\. For example, using the CLI you might run the following `create-replication-task` command\. 

   AWS DMS doesn't currently support creating a CDC task with a native start point using the console\.

   ```
   aws dms create-replication-task --replication-task-identifier postgresql-slot-name-test 
   --source-endpoint-arn arn:aws:dms:us-west-2:012345678901:endpoint:ABCD1EFGHIJK2LMNOPQRST3UV4 
   --target-endpoint-arn arn:aws:dms:us-west-2:012345678901:endpoint:ZYX9WVUTSRQONM8LKJIHGF7ED6 
   --replication-instance-arn arn:aws:dms:us-west-2:012345678901:rep:AAAAAAAAAAA5BB4CCC3DDDD2EE 
   --migration-type cdc --table-mappings "file://mappings.json" --cdc-start-position "4AF/B00000D0" 
   --replication-task-settings "file://task-pg.json"
   ```

   In the preceding command, the following options are set:
   + The `source-endpoint-arn` option is set to the new value that you created in step 2\.
   + The `replication-instance-arn` option is set to the same value as for the parent task from step 1\.
   + The `table-mappings` and `replication-task-settings` options are set to the same values as for the parent task from step 1\.
   + The `cdc-start-position` option is set to a start position value\. To find this start position, either query the `pg_replication_slots` view on your source database or view the console details for the parent task in step 1\. For more information, see [Determining a CDC native start point](CHAP_Task.CDC.md#CHAP_Task.CDC.StartPoint.Native)\.

   When this CDC task runs, AWS DMS raises an error if the specified logical replication slot doesn't exist\. It also raises an error if the task isn't created with a valid setting for `cdc-start-position`\.

When using native CDC start points with the pglogical plugin and you want to use a new replication slot, complete the setup steps following before creating a CDC task\. 

**To use a new replication slot not previously created as part of another DMS task**

1. Create a replication slot, as shown following:

   ```
   SELECT * FROM pg_create_logical_replication_slot('replication_slot_name', 'pglogical');
   ```

1. After the database creates the replication slot, get and note the **restart\_lsn** and **confirmed\_flush\_lsn** values for the slot:

   ```
   select * from pg_replication_slots where slot_name like 'replication_slot_name';
   ```

   Note that the Native CDC Start position for a CDC task created after the replication slot can't be older than the **confirmed\_flush\_lsn** value\.

   For information about the **restart\_lsn** and **confirmed\_flush\_lsn** values, see [pg\_replication\_slots](https://www.postgresql.org/docs/14/view-pg-replication-slots.html) 

1. Create a pglogical node\.

   ```
   SELECT pglogical.create_node(node_name := 'node_name', dsn := 'your_dsn_name');
   ```

1. Create two replication sets using the `pglogical.create_replication_set` function\. The first replication set tracks updates and deletes for tables that have primary keys\. The second replication set tracks only inserts, and has the same name as the first replication set, with the added prefix 'i'\.

   ```
   SELECT pglogical.create_replication_set('replication_slot_name', false, true, true, false);
   SELECT pglogical.create_replication_set('ireplication_slot_name', true, false, false, true);
   ```

1. Add a table to the replication set\.

   ```
   SELECT pglogical.replication_set_add_table('replication_slot_name', 'schemaname.tablename', true);
   SELECT pglogical.replication_set_add_table('ireplication_slot_name', 'schemaname.tablename', true);
   ```

1. Set the extra connection attribute \(ECA\) following when you create your source endpoint\.

   ```
   PluginName=PGLOGICAL;slotName=slot_name;
   ```

You can now create a CDC only task with a PostgreSQL native start point using the new replication slot\. For more information about the pglogical plugin, see the [pglogical 3\.7 documentation](https://www.enterprisedb.com/docs/pgd/3.7/pglogical/)

## Migrating from PostgreSQL to PostgreSQL using AWS DMS<a name="CHAP_Source.PostgreSQL.Homogeneous"></a>

When you migrate from a database engine other than PostgreSQL to a PostgreSQL database, AWS DMS is almost always the best migration tool to use\. But when you are migrating from a PostgreSQL database to a PostgreSQL database, PostgreSQL tools can be more effective\.

### Using PostgreSQL native tools to migrate data<a name="CHAP_Source.PostgreSQL.Homogeneous.Native"></a>

We recommend that you use PostgreSQL database migration tools such as `pg_dump` under the following conditions: 
+ You have a homogeneous migration, where you are migrating from a source PostgreSQL database to a target PostgreSQL database\. 
+ You are migrating an entire database\.
+ The native tools allow you to migrate your data with minimal downtime\. 

The pg\_dump utility uses the COPY command to create a schema and data dump of a PostgreSQL database\. The dump script generated by pg\_dump loads data into a database with the same name and recreates the tables, indexes, and foreign keys\. To restore the data to a database with a different name, use the `pg_restore` command and the `-d` parameter\.

If you are migrating data from a PostgreSQL source database running on EC2 to an Amazon RDS for PostgreSQL target, you can use the pglogical plugin\.

For more information about importing a PostgreSQL database into Amazon RDS for PostgreSQL or Amazon Aurora PostgreSQL\-Compatible Edition, see [https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/PostgreSQL.Procedural.Importing.html](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/PostgreSQL.Procedural.Importing.html)\.

### Using DMS to migrate data from PostgreSQL to PostgreSQL<a name="CHAP_Source.PostgreSQL.Homogeneous.DMS"></a>

 AWS DMS can migrate data, for example, from a source PostgreSQL database that is on premises to a target Amazon RDS for PostgreSQL or Aurora PostgreSQL instance\. Core or basic PostgreSQL data types most often migrate successfully\.

**Note**  
When replicating partitioned tables from a PostgreSQL source to PostgreSQL target, you don’t need to mention the parent table as part of the selection criteria in the DMS task\. Mentioning the parent table causes data to be duplicated in child tables on the target, possibly causing a PK violation\. By selecting child tables alone in the table mapping selection criteria, the parent table is automatically populated\.

Data types that are supported on the source database but aren't supported on the target might not migrate successfully\. AWS DMS streams some data types as strings if the data type is unknown\. Some data types, such as XML and JSON, can successfully migrate as small files but can fail if they are large documents\. 

When performing data type migration, be aware of the following:
+ In some cases, the PostgreSQL NUMERIC\(p,s\) data type doesn't specify any precision and scale\. For DMS versions 3\.4\.2 and earlier, DMS uses a precision of 28 and a scale of 6 by default, NUMERIC\(28,6\)\. For example, the value 0\.611111104488373 from the source is converted to 0\.611111 on the PostgreSQL target\.
+ A table with an ARRAY data type must have a primary key\. A table with an ARRAY data type missing a primary key gets suspended during full load\.

The following table shows source PostgreSQL data types and whether they can be migrated successfully\.


| Data type | Migrates successfully | Partially migrates | Doesn't migrate | Comments | 
| --- | --- | --- | --- | --- | 
| INTEGER | X |  |  |  | 
| SMALLINT | X |  |  |  | 
| BIGINT | X |  |  |  | 
| NUMERIC/DECIMAL\(p,s\) |  | X |  | Where 0<p<39 and 0<s | 
| NUMERIC/DECIMAL |  | X |  | Where p>38 or p=s=0 | 
| REAL | X |  |  |  | 
| DOUBLE | X |  |  |  | 
| SMALLSERIAL | X |  |  |  | 
| SERIAL | X |  |  |  | 
| BIGSERIAL | X |  |  |  | 
| MONEY | X |  |  |  | 
| CHAR |  | X |  | Without specified precision | 
| CHAR\(n\) | X |  |  |  | 
| VARCHAR |  | X |  | Without specified precision | 
| VARCHAR\(n\) | X |  |  |  | 
| TEXT | X |  |  |  | 
| BYTEA | X |  |  |  | 
| TIMESTAMP | X |  |  | Positive and negative infinity values are truncated to '9999\-12\-31 23:59:59' and '4713\-01\-01 00:00:00 BC' respectively\. | 
| TIMESTAMP\(Z\) |  | X |  |  | 
| DATE | X |  |  |  | 
| TIME | X |  |  |  | 
| TIME \(z\) |  | X |  |  | 
| INTERVAL |  | X |  |  | 
| BOOLEAN | X |  |  |  | 
| ENUM |  |  | X |  | 
| CIDR | X |  |  |  | 
| INET |  |  | X |  | 
| MACADDR |  |  | X |  | 
| TSVECTOR |  |  | X |  | 
| TSQUERY |  |  | X |  | 
| XML |  | X |  |  | 
| POINT | X |  |  | PostGIS spatial data type | 
| LINE |  |  | X |  | 
| LSEG |  |  | X |  | 
| BOX |  |  | X |  | 
| PATH |  |  | X |  | 
| POLYGON | X |  |  | PostGIS spatial data type | 
| CIRCLE |  |  | X |  | 
| JSON |  | X |  |  | 
| ARRAY | X |  |  | Requires Primary Key | 
| COMPOSITE |  |  | X |  | 
| RANGE |  |  | X |  | 
| LINESTRING | X |  |  | PostGIS spatial data type | 
| MULTIPOINT | X |  |  | PostGIS spatial data type | 
| MULTILINESTRING | X |  |  | PostGIS spatial data type | 
| MULTIPOLYGON | X |  |  | PostGIS spatial data type | 
| GEOMETRYCOLLECTION | X |  |  | PostGIS spatial data type | 

### Migrating PostGIS spatial data types<a name="CHAP_Source.PostgreSQL.DataTypes.Spatial"></a>

*Spatial data* identifies the geometry information of an object or location in space\. PostgreSQL object\-relational databases support PostGIS spatial data types\. 

Before migrating PostgreSQL spatial data objects, ensure that the PostGIS plugin is enabled at the global level\. Doing this ensures that AWS DMS creates the exact source spatial data columns for the PostgreSQL target DB instance\.

For PostgreSQL to PostgreSQL homogeneous migrations, AWS DMS supports the migration of PostGIS geometric and geographic \(geodetic coordinates\) data object types and subtypes such as the following:
+  POINT 
+  LINESTRING 
+  POLYGON 
+  MULTIPOINT 
+  MULTILINESTRING 
+  MULTIPOLYGON 
+  GEOMETRYCOLLECTION 

## Removing AWS DMS artifacts from a PostgreSQL source database<a name="CHAP_Source.PostgreSQL.CleanUp"></a>

To capture DDL events, AWS DMS creates various artifacts in the PostgreSQL database when a migration task starts\. When the task completes, you might want to remove these artifacts\.

To remove the artifacts, issue the following statements \(in the order they appear\), where `{AmazonRDSMigration}` is the schema in which the artifacts were created\. Dropping a schema should be done with extreme caution\. Never drop an operational schema, especially not a public one\.

```
drop event trigger awsdms_intercept_ddl;
```

The event trigger doesn't belong to a specific schema\.

```
drop function {AmazonRDSMigration}.awsdms_intercept_ddl()
drop table {AmazonRDSMigration}.awsdms_ddl_audit
drop schema {AmazonRDSMigration}
```

## Additional configuration settings when using a PostgreSQL database as a DMS source<a name="CHAP_Source.PostgreSQL.Advanced"></a>

You can add additional configuration settings when migrating data from a PostgreSQL database in two ways:
+ You can add values to the extra connection attribute to capture DDL events and to specify the schema in which the operational DDL database artifacts are created\. For more information, see [Endpoint settings when using PostgreSQL as a DMS source](#CHAP_Source.PostgreSQL.ConnectionAttrib)\.
+ You can override connection string parameters\. Choose this option to do either of the following:
  + Specify internal AWS DMS parameters\. Such parameters are rarely required so aren't exposed in the user interface\.
  + Specify pass\-through \(passthru\) values for the specific database client\. AWS DMS includes pass\-through parameters in the connection sting passed to the database client\.
+ By using the table\-level parameter `REPLICA IDENTITY` in PostgreSQL versions 9\.4 and higher, you can control information written to write\-ahead logs \(WALs\)\. In particular, it does so for WALs that identify rows that are updated or deleted\. `REPLICA IDENTITY FULL` records the old values of all columns in the row\. Use `REPLICA IDENTITY FULL` carefully for each table as `FULL` generates an extra number of WALs that might not be necessary\. For more information, see [ALTER TABLE\-REPLICA IDENTITY](https://www.postgresql.org/docs/devel/sql-altertable.html) 

## Using the MapBooleanAsBoolean PostgreSQL endpoint setting<a name="CHAP_Source.PostgreSQL.ConnectionAttrib.Endpointsetting"></a>

You can use PostgreSQL endpoint settings to map a boolean as a boolean from your PostgreSQL source to a Amazon Redshift target\. By default, a BOOLEAN type is migrated as varchar\(5\)\. You can specify `MapBooleanAsBoolean` to let PostgreSQL to migrate the boolean type as boolean as shown in the example following\.

```
--postgre-sql-settings '{"MapBooleanAsBoolean": true}'
```

## Endpoint settings when using PostgreSQL as a DMS source<a name="CHAP_Source.PostgreSQL.ConnectionAttrib"></a>

You can use endpoint settings to configure your PostgreSQL source database similar to using extra connection attributes\. You specify the settings when you create the source endpoint using the AWS DMS console, or by using the `create-endpoint` command in the [AWS CLI](https://docs.aws.amazon.com/cli/latest/reference/dms/index.html), with the `--postgre-sql-settings '{"EndpointSetting": "value", ...}'` JSON syntax\.

The following table shows the endpoint settings that you can use with PostgreSQL as a source\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Source.PostgreSQL.html)

## Limitations on using a PostgreSQL database as a DMS source<a name="CHAP_Source.PostgreSQL.Limitations"></a>

The following limitations apply when using PostgreSQL as a source for AWS DMS:
+ AWS DMS doesn't work with Amazon RDS for PostgreSQL 10\.4 or Amazon Aurora PostgreSQL 10\.4 either as source or target\.
+ A captured table must have a primary key\. If a table doesn't have a primary key, AWS DMS ignores DELETE and UPDATE record operations for that table\. As a workaround, see [Enabling change data capture \(CDC\) using logical replication](#CHAP_Source.PostgreSQL.Security)\. 

  **Note:** We don't recommend migrating without a Primary Key/Unique Index, otherwise additional limitations apply such as "NO" Batch apply capability, Full LOB capability, Data Validation and inability to replicate to Redshift target efficiently\.
+ AWS DMS ignores an attempt to update a primary key segment\. In these cases, the target identifies the update as one that didn't update any rows\. However, because the results of updating a primary key in PostgreSQL are unpredictable, no records are written to the exceptions table\.
+ AWS DMS doesn't support the **Start Process Changes from Timestamp** run option\.
+ AWS DMS doesn't replicate changes that result from partition or subpartition operations \(`ADD`, `DROP`, or `TRUNCATE`\)\.
+ Replication of multiple tables with the same name where each name has a different case \(for example, table1, TABLE1, and Table1\) can cause unpredictable behavior\. Because of this issue, AWS DMS doesn't support this type of replication\.
+ In most cases, AWS DMS supports change processing of CREATE, ALTER, and DROP DDL statements for tables\. AWS DMS doesn't support this change processing if the tables are held in an inner function or procedure body block or in other nested constructs\.

  For example, the following change isn't captured\.

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
+ Currently, `boolean` data types in a PostgreSQL source are migrated to a SQL Server target as `bit` data type with inconsistent values\. As a workaround, pre\-create the table with a `VARCHAR(1)` data type for the column \(or have AWS DMS create the table\)\. Then have downstream processing treat an "F" as False and a "T" as True\.
+ AWS DMS doesn't support change processing of TRUNCATE operations\.
+ The OID LOB data type isn't migrated to the target\.
+ AWS DMS supports the PostGIS data type for only homogeneous migrations\.
+ If your source is a PostgreSQL database that is on\-premises or on an Amazon EC2 instance, ensure that the test\_decoding output plugin is installed on your source endpoint\. You can find this plugin in the PostgreSQL contrib package\. For more information about the test\-decoding plugin, see the [PostgreSQL documentation](https://www.postgresql.org/docs/10/static/test-decoding.html)\.
+ AWS DMS doesn't support change processing to set and unset column default values \(using the ALTER COLUMN SET DEFAULT clause on ALTER TABLE statements\)\.
+ AWS DMS doesn't support change processing to set column nullability \(using the ALTER COLUMN \[SET\|DROP\] NOT NULL clause on ALTER TABLE statements\)\.
+ When logical replication is enabled, the maximum number of changes kept in memory per transaction is 4 MB\. After that, changes are spilled to disk\. As a result `ReplicationSlotDiskUsage` increases, and `restart_lsn` doesn’t advance until the transaction is completed or stopped and the rollback finishes\. Because it is a long transaction, it can take a long time to rollback\. So, avoid long running transactions when logical replication is enabled\. Instead, break the transaction into several smaller transactions\.
+ A table with an ARRAY data type must have a primary key\. A table with an ARRAY data type missing a primary key gets suspended during full load\.
+ AWS DMS doesn't support replication of partitioned tables\. When a partitioned table is detected, the following occurs:
  + The endpoint reports a list of parent and child tables\.
  + AWS DMS creates the table on the target as a regular table with the same properties as the selected tables\.
  + If the parent table in the source database has the same primary key value as its child tables, a "duplicate key" error is generated\.
+ To replicate partitioned tables from a PostgreSQL source to a PostgreSQL target, first manually create the parent and child tables on the target\. Then define a separate task to replicate to those tables\. In such a case, set the task configuration to **Truncate before loading**\.
+ The PostgreSQL `NUMERIC` data type isn't fixed in size\. When transferring data that is a `NUMERIC` data type but without precision and scale, DMS uses `NUMERIC(28,6)` \(a precision of 28 and scale of 6\) by default\. As an example, the value 0\.611111104488373 from the source is converted to 0\.611111 on the PostgreSQL target\.
+ AWS DMS supports Aurora PostgreSQL Serverless V1 as a source for full load tasks only\. AWS DMS supports Aurora PostgreSQL Serverless V2 as a source for full load, full load and CDC, and CDC\-only tasks\.
+ AWS DMS doesn't support replication of a table with a unique index created with a coalesce function\.
+ When using LOB mode, both the source table and the corresponding target table must have an identical Primary Key\. If one of the tables does not have a Primary Key, the result of DELETE and UPDATE record operations will be unpredictable\.
+ When using the Parallel Load feature, table segmentation according to partitions or sub\-partitions isn't supported\. For more information about Parallel Load, see [Using parallel load for selected tables, views, and collections](CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Tablesettings.md#CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Tablesettings.ParallelLoad) 
+ AWS DMS doesn't support Deferred Constraints\.
+ AWS DMS version 3\.4\.7 supports PostgreSQL 14\.x as a source with these limitations:
  + AWS DMS doesn't support change processing of two phase commits\.
  + AWS DMS doesn't support logical replication to stream long in\-progress transactions\.
+ AWS DMS doesn't support CDC for Amazon RDS Proxy for PostgreSQL as a source\.
+ When using [source filters](CHAP_Tasks.CustomizingTasks.Filters.md) that don't contain a Primary Key column, `DELETE` operations won't be captured\.
+ If your source database is also a target for another third–party replication system, DDL changes might not migrate during CDC\. Because that situation can prevent the `awsdms_intercept_ddl` event trigger from firing\. To work around the situation, modify that trigger on your source database as follows:

  ```
  alter event trigger awsdms_intercept_ddl enable always;
  ```

## Source data types for PostgreSQL<a name="CHAP_Source-PostgreSQL-DataTypes"></a>

The following table shows the PostgreSQL source data types that are supported when using AWS DMS and the default mapping to AWS DMS data types\.

For information on how to view the data type that is mapped in the target, see the section for the target endpoint you are using\.

For additional information about AWS DMS data types, see [Data types for AWS Database Migration Service](CHAP_Reference.DataTypes.md)\.


|  PostgreSQL data types  |  DMS data types  | 
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
|  MONEY  |  NUMERIC\(38,4\) The MONEY data type is mapped to FLOAT in SQL Server\.  | 
|  CHAR  |  WSTRING \(1\)  | 
|  CHAR\(N\)  |  WSTRING \(n\)  | 
|  VARCHAR\(N\)  |  WSTRING \(n\)  | 
|  TEXT  |  NCLOB  | 
|  BYTEA  |  BLOB  | 
|  TIMESTAMP  |  DATETIME  | 
|  TIMESTAMP \(z\)  |  DATETIME  | 
|  TIMESTAMP WITH TIME ZONE  |  DATETIME  | 
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

### Working with LOB source data types for PostgreSQL<a name="CHAP_Source-PostgreSQL-DataTypes-LOBs"></a>

PostgreSQL column sizes affect the conversion of PostgreSQL LOB data types to AWS DMS data types\. To work with this, take the following steps for the following AWS DMS data types:
+ BLOB – Set **Limit LOB size to** the **Maximum LOB size \(KB\)** value at task creation\.
+ CLOB – Replication handles each character as a UTF8 character\. Therefore, find the length of the longest character text in the column, shown here as `max_num_chars_text`\. Use this length to specify the value for **Limit LOB size to**\. If the data includes 4\-byte characters, multiply by 2 to specify the **Limit LOB size to** value, which is in bytes\. In this case, **Limit LOB size to** is equal to `max_num_chars_text` multiplied by 2\.
+ NCLOB – Replication handles each character as a double\-byte character\. Therefore, find the length of the longest character text in the column \(`max_num_chars_text`\) and multiply by 2\. You do this to specify the value for **Limit LOB size to**\. In this case, **Limit LOB size to** is equal to `max_num_chars_text` multiplied by 2\. If the data includes 4\-byte characters, multiply by 2 again\. In this case, **Limit LOB size to** is equal to `max_num_chars_text` multiplied by 4\.