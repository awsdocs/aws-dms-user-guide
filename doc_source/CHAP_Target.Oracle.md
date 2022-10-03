# Using an Oracle database as a target for AWS Database Migration Service<a name="CHAP_Target.Oracle"></a>

You can migrate data to Oracle database targets using AWS DMS, either from another Oracle database or from one of the other supported databases\. You can use Secure Sockets Layer \(SSL\) to encrypt connections between your Oracle endpoint and the replication instance\. For more information on using SSL with an Oracle endpoint, see [Using SSL with AWS Database Migration Service](CHAP_Security.md#CHAP_Security.SSL)\. AWS DMS also supports the use of Oracle transparent data encryption \(TDE\) to encrypt data at rest in the target database because Oracle TDE does not require an encryption key or password to write to the database\.

AWS DMS supports Oracle versions 10g, 11g, 12c, 18c, and 19c for on\-premises and EC2 instances for the Enterprise, Standard, Standard One, and Standard Two editions as targets\. AWS DMS supports Oracle versions 11g \(version 11\.2\.0\.3\.v1 and later\), 12c, 18c, and 19c for Amazon RDS instance databases for the Enterprise, Standard, Standard One, and Standard Two editions\.

When you use Oracle as a target, we assume that the data is to be migrated into the schema or user that is used for the target connection\. If you want to migrate data to a different schema, use a schema transformation to do so\. For example, suppose that your target endpoint connects to the user `RDSMASTER` and you want to migrate from the user `PERFDATA1` to `PERFDATA2`\. In this case, create a transformation like the following\.

```
{
   "rule-type": "transformation",
   "rule-id": "2",
   "rule-name": "2",
   "rule-action": "rename",
   "rule-target": "schema",
   "object-locator": {
   "schema-name": "PERFDATA1"
},
"value": "PERFDATA2"
}
```

When using Oracle as a target, AWS DMS migrates all tables and indexes to default table and index tablespaces in the target\. If you want to migrate tables and indexes to different table and index tablespaces, use a tablespace transformation to do so\. For example, suppose that you have a set of tables in the `INVENTORY` schema assigned to some tablespaces in the Oracle source\. For the migration, you want to assign all of these tables to a single `INVENTORYSPACE` tablespace in the target\. In this case, create a transformation like the following\.

```
{
   "rule-type": "transformation",
   "rule-id": "3",
   "rule-name": "3",
   "rule-action": "rename",
   "rule-target": "table-tablespace",
   "object-locator": {
      "schema-name": "INVENTORY",
      "table-name": "%",
      "table-tablespace-name": "%"
   },
   "value": "INVENTORYSPACE"
}
```

For more information about transformations, see [ Specifying table selection and transformations rules using JSON](CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.md)\.

If Oracle is both source and target, you can preserve existing table or index tablespace assignments by setting the Oracle source extra connection attribute, `enableHomogenousTablespace=true`\. For more information, see [Extra connection attributes when using Oracle as a source for AWS DMS](CHAP_Source.Oracle.md#CHAP_Source.Oracle.ConnectionAttrib)

For additional details on working with Oracle databases as a target for AWS DMS, see the following sections: 

**Topics**
+ [Limitations on Oracle as a target for AWS Database Migration Service](#CHAP_Target.Oracle.Limitations)
+ [User account privileges required for using Oracle as a target](#CHAP_Target.Oracle.Privileges)
+ [Configuring an Oracle database as a target for AWS Database Migration Service](#CHAP_Target.Oracle.Configuration)
+ [Extra connection attributes when using Oracle as a target for AWS DMS](#CHAP_Target.Oracle.ConnectionAttrib)
+ [Target data types for Oracle](#CHAP_Target.Oracle.DataTypes)

## Limitations on Oracle as a target for AWS Database Migration Service<a name="CHAP_Target.Oracle.Limitations"></a>

Limitations when using Oracle as a target for data migration include the following:
+ AWS DMS doesn't create schema on the target Oracle database\. You have to create any schemas you want on the target Oracle database\. The schema name must already exist for the Oracle target\. Tables from source schema are imported to the user or schema, which AWS DMS uses to connect to the target instance\. To migrate multiple schemas, you can create multiple replication tasks\. You can also migrate data to different schemas on a target\. To do this, you need to use schema transformation rules on the AWS DMS table mappings\.
+ AWS DMS doesn't support the `Use direct path full load` option for tables with INDEXTYPE CONTEXT\. As a workaround, you can use array load\. 
+ With the batch optimized apply option, loading into the net changes table uses a direct path, which doesn't support XML type\. As a workaround, you can use transactional apply mode\.
+ Empty strings migrated from source databases can be treated differently by the Oracle target \(converted to one\-space strings, for example\)\. This can result in AWS DMS validation reporting a mismatch\.
+ You can express the total number of columns per table supported in Batch optimized apply mode, using the following formula:

  ```
  2 * columns_in_original_table + columns_in_primary_key <= 999
  ```

  For example, if the original table has 25 columns and its Primary Key consists of 5 columns, then the total number of columns is 55\. If a table exceeds the supported number of columns, then all of the changes are applied in one\-by\-one mode\.

## User account privileges required for using Oracle as a target<a name="CHAP_Target.Oracle.Privileges"></a>

To use an Oracle target in an AWS Database Migration Service task, grant the following privileges in the Oracle database\. You grant these to the user account specified in the Oracle database definitions for AWS DMS\.
+ SELECT ANY TRANSACTION 
+ SELECT on V$NLS\_PARAMETERS 
+ SELECT on V$TIMEZONE\_NAMES 
+ SELECT on ALL\_INDEXES 
+ SELECT on ALL\_OBJECTS 
+ SELECT on DBA\_OBJECTS
+ SELECT on ALL\_TABLES 
+ SELECT on ALL\_USERS 
+ SELECT on ALL\_CATALOG 
+ SELECT on ALL\_CONSTRAINTS 
+ SELECT on ALL\_CONS\_COLUMNS 
+ SELECT on ALL\_TAB\_COLS 
+ SELECT on ALL\_IND\_COLUMNS 
+ DROP ANY TABLE 
+ SELECT ANY TABLE
+ INSERT ANY TABLE 
+ UPDATE ANY TABLE
+ CREATE ANY VIEW
+ DROP ANY VIEW
+ CREATE ANY PROCEDURE
+ ALTER ANY PROCEDURE
+ DROP ANY PROCEDURE
+ CREATE ANY SEQUENCE
+ ALTER ANY SEQUENCE
+ DROP ANY SEQUENCE 
+ DELETE ANY TABLE

For the following requirements, grant these additional privileges:
+ To use a specific table list, grant SELECT on any replicated table and also ALTER on any replicated table\.
+ To allow a user to create a table in a default tablespace, grant the privilege GRANT UNLIMITED TABLESPACE\.
+ For logon, grant the privilege CREATE SESSION\.
+ If you are using a direct path \(which is the default for full load\), `GRANT LOCK ANY TABLE to dms_user;`\.
+ If schema is different when using “DROP and CREATE” table prep mode, `GRANT CREATE ANY INDEX to dms_user;`\.
+ For some full load scenarios, you might choose the "DROP and CREATE table" or "TRUNCATE before loading" option where a target table schema is different from the DMS user's\. In this case, grant DROP ANY TABLE\.
+ To store changes in change tables or an audit table where the target table schema is different from the DMS user's, grant CREATE ANY TABLE and CREATE ANY INDEX\.

### Read privileges required for AWS Database Migration Service on the target database<a name="CHAP_Target.Oracle.Privileges.Read"></a>

The AWS DMS user account must be granted read permissions for the following DBA tables:
+ SELECT on DBA\_USERS
+ SELECT on DBA\_TAB\_PRIVS
+ SELECT on DBA\_OBJECTS
+ SELECT on DBA\_SYNONYMS
+ SELECT on DBA\_SEQUENCES
+ SELECT on DBA\_TYPES
+ SELECT on DBA\_INDEXES
+ SELECT on DBA\_TABLES
+ SELECT on DBA\_TRIGGERS
+ SELECT on SYS\.DBA\_REGISTRY

If any of the required privileges cannot be granted to V$xxx, then grant them to V\_$xxx\.

## Configuring an Oracle database as a target for AWS Database Migration Service<a name="CHAP_Target.Oracle.Configuration"></a>

Before using an Oracle database as a data migration target, you must provide an Oracle user account to AWS DMS\. The user account must have read/write privileges on the Oracle database, as specified in [User account privileges required for using Oracle as a target](#CHAP_Target.Oracle.Privileges)\.

## Extra connection attributes when using Oracle as a target for AWS DMS<a name="CHAP_Target.Oracle.ConnectionAttrib"></a>

You can use extra connection attributes to configure your Oracle target\. You specify these settings when you create the target endpoint\. If you have multiple connection attribute settings, separate them from each other by semicolons with no additional white space\.

The following table shows the extra connection attributes available when using Oracle as a target\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Target.Oracle.html)

## Target data types for Oracle<a name="CHAP_Target.Oracle.DataTypes"></a>

A target Oracle database used with AWS DMS supports most Oracle data types\. The following table shows the Oracle target data types that are supported when using AWS DMS and the default mapping from AWS DMS data types\. For more information about how to view the data type that is mapped from the source, see the section for the source you are using\.


|  AWS DMS data type  |  Oracle data type  | 
| --- | --- | 
|  BOOLEAN  |  NUMBER \(1\)  | 
|  BYTES  |  RAW \(length\)  | 
|  DATE  |  DATETIME  | 
|  TIME  | TIMESTAMP \(0\) | 
|  DATETIME  |  TIMESTAMP \(scale\)  | 
|  INT1  | NUMBER \(3\) | 
|  INT2  |  NUMBER \(5\)  | 
|  INT4  | NUMBER \(10\) | 
|  INT8  |  NUMBER \(19\)  | 
|  NUMERIC  |  NUMBER \(p,s\)  | 
|  REAL4  |  FLOAT  | 
|  REAL8  | FLOAT | 
|  STRING  |  With date indication: DATE  With time indication: TIMESTAMP  With timestamp indication: TIMESTAMP  With timestamp\_with\_timezone indication: TIMESTAMP WITH TIMEZONE  With timestamp\_with\_local\_timezone indication: TIMESTAMP WITH LOCAL TIMEZONE With interval\_year\_to\_month indication: INTERVAL YEAR TO MONTH  With interval\_day\_to\_second indication: INTERVAL DAY TO SECOND  If length > 4000: CLOB In all other cases: VARCHAR2 \(length\)  | 
|  UINT1  |  NUMBER \(3\)  | 
|  UINT2  |  NUMBER \(5\)  | 
|  UINT4  |  NUMBER \(10\)  | 
|  UINT8  |  NUMBER \(19\)  | 
|  WSTRING  |  If length > 2000: NCLOB In all other cases: NVARCHAR2 \(length\)  | 
|  BLOB  |  BLOB To use this data type with AWS DMS, you must enable the use of BLOBs for a specific task\. BLOB data types are supported only in tables that include a primary key  | 
|  CLOB  |  CLOB To use this data type with AWS DMS, you must enable the use of CLOBs for a specific task\. During change data capture \(CDC\), CLOB data types are supported only in tables that include a primary key\. STRING An Oracle VARCHAR2 data type on the source with a declared size greater than 4000 bytes maps through the AWS DMS CLOB to a STRING on the Oracle target\.  | 
|  NCLOB  |  NCLOB To use this data type with AWS DMS, you must enable the use of NCLOBs for a specific task\. During CDC, NCLOB data types are supported only in tables that include a primary key\. WSTRING An Oracle VARCHAR2 data type on the source with a declared size greater than 4000 bytes maps through the AWS DMS NCLOB to a WSTRING on the Oracle target\.   | 
| XMLTYPE |  The XMLTYPE target data type is only relevant in Oracle\-to\-Oracle replication tasks\. When the source database is Oracle, the source data types are replicated as\-is to the Oracle target\. For example, an XMLTYPE data type on the source is created as an XMLTYPE data type on the target\.  | 