# Using an Oracle Database as a Source for AWS DMS<a name="CHAP_Source.Oracle"></a>

You can migrate data from one or many Oracle databases using AWS DMS\. With an Oracle database as a source, you can migrate data to any of the targets supported by AWS DMS\.

For self\-managed Oracle databases, AWS DMS supports all Oracle database editions for versions 10\.2 and later, 11g, and up to 12\.2 for self\-managed databases as sources\. For Amazon\-managed Oracle databases provided by Amazon RDS, AWS DMS supports all Oracle database editions for versions 11g \(versions 11\.2\.0\.3\.v1 and later\) and up to 12\.2\.

You can use SSL to encrypt connections between your Oracle endpoint and the replication instance\. For more information on using SSL with an Oracle endpoint, see [Using SSL With AWS Database Migration Service](CHAP_Security.SSL.md)\.

The steps to configure an Oracle database as a source for AWS DMS source are as follows:

1. If you want to create a CDC\-only or full load plus CDC task, then you must choose either Oracle LogMiner or Oracle Binary Reader to capture data changes\. Choosing LogMiner or Binary Reader determines some of the subsequent permission and configuration tasks\. For a comparison of LogMiner and Binary Reader, see the next section\.

1. Create an Oracle user with the appropriate permissions for AWS DMS\. If you are creating a full\-load\-only task, then no further configuration is needed\.

1. If you are creating a full load plus CDC task or a CDC\-only task, configure Oracle for LogMiner or Binary Reader\.

1. Create a DMS endpoint that conforms with your chosen configuration\.

For additional details on working with Oracle databases and AWS DMS, see the following sections\. 

**Topics**
+ [Using Oracle LogMiner or Oracle Binary Reader for Change Data Capture \(CDC\)](#CHAP_Source.Oracle.CDC)
+ [Working with a Self\-Managed Oracle Database as a Source for AWS DMS](#CHAP_Source.Oracle.Self-Managed)
+ [Working with an Amazon\-Managed Oracle Database as a Source for AWS DMS](#CHAP_Source.Oracle.Amazon-Managed)
+ [Limitations on Using Oracle as a Source for AWS DMS](#CHAP_Source.Oracle.Limitations)
+ [Extra Connection Attributes When Using Oracle as a Source for AWS DMS](#CHAP_Source.Oracle.ConnectionAttrib)
+ [Source Data Types for Oracle](#CHAP_Source.Oracle.DataTypes)

## Using Oracle LogMiner or Oracle Binary Reader for Change Data Capture \(CDC\)<a name="CHAP_Source.Oracle.CDC"></a>

Oracle offers two methods for reading the redo logs when doing change processing: Oracle LogMiner and Oracle Binary Reader\. Oracle LogMiner provides a SQL interface to Oracle’s online and archived redo log files\. Binary Reader is an AWS DMS feature that reads and parses the raw redo log files directly\.

By default, AWS DMS uses Oracle LogMiner for change data capture \(CDC\)\. The advantages of using LogMiner with AWS DMS include the following:
+ LogMiner supports most Oracle options, such as encryption options and compression options\. Binary Reader doesn't support all Oracle options, in particular options for encryption and compression\.
+ LogMiner offers a simpler configuration, especially compared to Oracle Binary Reader's direct access setup or if the redo logs are on Automatic Storage Management \(ASM\)\.
+ LogMiner fully supports most Oracle encryption options, including Oracle Transparent Data Encryption \(TDE\)\.
+ LogMiner supports the following HCC compression types for both full load and on\-going replication \(CDC\):
  + QUERY HIGH
  + ARCHIVE HIGH
  + ARCHIVE LOW
  + QUERY LOW

  Binary Reader supports QUERY LOW compression only for full load replications, not ongoing \(CDC\) replications\.
+ LogMiner supports table clusters for use by AWS DMS\. Binary Reader does not\.

The advantages to using Binary Reader with AWS DMS, instead of LogMiner, include the following:
+ For migrations with a high volume of changes, LogMiner might have some I/O or CPU impact on the computer hosting the Oracle source database\. Binary Reader has less chance of having I/O or CPU impact because the archive logs are copied to the replication instance and minded there\.
+ For migrations with a high volume of changes, CDC performance is usually much better when using Binary Reader compared with using Oracle LogMiner\.
+ Binary Reader supports CDC for LOBS in Oracle version 12c\. LogMiner does not\.
+ Binary Reader supports the following HCC compression types for both full load and continuous replication \(CDC\):
  + QUERY HIGH
  + ARCHIVE HIGH
  + ARCHIVE LOW

    The QUERY LOW compression type is only supported for full load migrations\.

In general, use Oracle LogMiner for migrating your Oracle database unless you have one of the following situations:
+ You need to run several migration tasks on the source Oracle database\.
+ The volume of changes or the redo log volume on the source Oracle database is high\.
+ You are migrating LOBs from an Oracle 12\.2 or later source endpoint\.
+ If your workload includes UPDATE statements that update only LOB columns you must use Binary Reader\. These update statements are not supported by Oracle LogMiner\.
+ If your source is Oracle version 11 and you perform UPDATE statements on XMLTYPE and LOB columns, you must use Binary Reader\. These statements are not supported by Oracle LogMiner\.
+ On Oracle 12c, LogMiner does not support LOB columns\. You must use Binary Reader if you are migrating LOB columns from Oracle 12c\.

### Configuration for Change Data Capture \(CDC\) on an Oracle Source Database<a name="CHAP_Source.Oracle.CDC.Configuration"></a>

When you use Oracle as a source endpoint either for full\-load and change data capture \(CDC\) or just for CDC, you must set an extra connection attribute\. This attribute specifies whether to use LogMiner or Binary Reader to access the transaction logs\. You specify an extra connection attribute when you create the source endpoint\. Multiple extra connection attribute settings should be separated by a semicolon\.

LogMiner is used by default, so you don't have to explicitly specify its use\. The enable Binary Reader to access the transaction logs, add the following extra connection attribute\.

```
useLogMinerReader=N; useBfile=Y
```

If the Oracle source database is using Oracle Automatic Storage Management \(ASM\), the extra connection attribute needs to include the ASM user name and ASM server address\. When you create the source endpoint, the password field needs to have both passwords, the source user password and the ASM password\.

For example, the following extra connection attribute format is used to access a server that uses Oracle ASM\.

```
useLogMinerReader=N;asm_user=<asm_username>;asm_server=<first_RAC_server_ip_address>:<port_number>/+ASM
```

If the Oracle source database is using Oracle ASM, the source endpoint password field must have both the Oracle user password and the ASM password, separated by a comma\. For example, the following works in the password field\.

```
<oracle_user_password>,<asm_user_password>
```

### Limitations for CDC on an Oracle Source Database<a name="CHAP_Source.Oracle.CDC.Limitations"></a>

The following limitations apply when using an Oracle database as a source for AWS DMS change data capture:
+ AWS DMS doesn't capture changes made by the Oracle DBMS\_REDEFINITION package, such as changes to table metadata and the OBJECT\_ID value\. 
+ AWS DMS doesn't support index\-organized tables with an overflow segment in CDC mode when using BFILE\. An example is when you access the redo logs without using LogMiner\.

## Working with a Self\-Managed Oracle Database as a Source for AWS DMS<a name="CHAP_Source.Oracle.Self-Managed"></a>

A self\-managed database is a database that you configure and control, either a local on\-premises database instance or a database on Amazon EC2\. Following, you can find out about the privileges and configurations you need to set up when using a self\-managed Oracle database with AWS DMS\.

### User Account Privileges Required on a Self\-Managed Oracle Source for AWS DMS<a name="CHAP_Source.Oracle.Self-Managed.Privileges"></a>

To use an Oracle database as a source in an AWS DMS task, the user specified in the AWS DMS Oracle database definitions must be granted the following privileges in the Oracle database\. When granting privileges, use the actual name of objects \(for example, V\_$OBJECT including the underscore\), not the synonym for the object \(for example, V$OBJECT without the underscore\)\.

```
                   
GRANT SELECT ANY TRANSACTION to <dms_user>
GRANT SELECT on V_$ARCHIVED_LOG to <dms_user>
GRANT SELECT on V_$LOG to <dms_user>
GRANT SELECT on V_$LOGFILE to <dms_user>
GRANT SELECT on V_$DATABASE to <dms_user>
GRANT SELECT on V_$THREAD to <dms_user>
GRANT SELECT on V_$PARAMETER to <dms_user>
GRANT SELECT on V_$NLS_PARAMETERS to <dms_user>
GRANT SELECT on V_$TIMEZONE_NAMES to <dms_user>
GRANT SELECT on V_$TRANSACTION to <dms_user>
GRANT SELECT on ALL_INDEXES to <dms_user>
GRANT SELECT on ALL_OBJECTS to <dms_user>
GRANT SELECT on DBA_OBJECTS to <dms_user> (required if the Oracle version is earlier than 11.2.0.3)
GRANT SELECT on ALL_TABLES to <dms_user>
GRANT SELECT on ALL_USERS to <dms_user>
GRANT SELECT on ALL_CATALOG to <dms_user>
GRANT SELECT on ALL_CONSTRAINTS to <dms_user>
GRANT SELECT on ALL_CONS_COLUMNS to <dms_user>
GRANT SELECT on ALL_TAB_COLS to <dms_user>
GRANT SELECT on ALL_IND_COLUMNS to <dms_user>
GRANT SELECT on ALL_LOG_GROUPS to <dms_user>
GRANT SELECT on SYS.DBA_REGISTRY to <dms_user>
GRANT SELECT on SYS.OBJ$ to <dms_user>
GRANT SELECT on DBA_TABLESPACES to <dms_user>
GRANT SELECT on ALL_TAB_PARTITIONS to <dms_user>
GRANT SELECT on ALL_ENCRYPTED_COLUMNS to <dms_user>
GRANT SELECT on V_$LOGMNR_LOGS to <dms_user>
GRANT SELECT on V_$LOGMNR_CONTENTS to <dms_user>
```

When using ongoing replication \(CDC\), you need these additional permissions\.
+ The following permission is required when using CDC so that AWS DMS can add to Oracle LogMiner redo logs for both 11g and 12c\.

  ```
  Grant EXECUTE ON dbms_logmnr TO <dms_user>;
  ```
+ The following permission is required when using CDC so that AWS DMS can add to Oracle LogMiner redo logs for 12c only\.

  ```
  Grant LOGMINING TO <dms_user>;
  ```

If you are using any of the additional features noted following, the given additional permissions are required:
+ If views are exposed, grant SELECT on ALL\_VIEWS to <dms\_user>\.
+ If you use a pattern to match table names in your replication task, grant SELECT ANY TABLE\.
+ If you specify a table list in your replication task, grant SELECT on each table in the list\.
+ If you add supplemental logging, grant ALTER ANY TABLE\.
+ If you add supplemental logging and you use a specific table list, grant ALTER for each table in the list\.
+ If you are migrating from Oracle RAC, grant SELECT permissions on materialized views with the prefixes g\_$ and v\_$\.

### Configuring a Self\-Managed Oracle Source for AWS DMS<a name="CHAP_Source.Oracle.Self-Managed.Configuration"></a>

Before using a self\-managed Oracle database as a source for AWS DMS, you need to perform several tasks:
+ Provide Oracle account access – You must provide an Oracle user account for AWS DMS\. The user account must have read/write privileges on the Oracle database, as specified in the previous section\.
+ Ensure that ARCHIVELOG mode is on – Oracle can run in two different modes, the ARCHIVELOG mode and the NOARCHIVELOG mode\. To use Oracle with AWS DMS, the source database must be in ARCHIVELOG mode\.
+ Set up supplemental logging – If you are planning to use the source in a CDC or full\-load plus CDC task, then you need to set up supplemental logging to capture the changes for replication\.

There are two steps to enable supplemental logging for Oracle\. First, you need to enable database\-level supplemental logging\. Doing this ensures that the LogMiner has the minimal information to support various table structures such as clustered and index\-organized tables\. Second, you need to enable table\-level supplemental logging for each table to be migrated\. 

**To enable database\-level supplemental logging**

1. Run the following query to determine if database\-level supplemental logging is already enabled\. The return result should be from `GE to 9.0.0`\.

   ```
   SELECT name, value, description FROM v$parameter WHERE name = 'compatible';
   ```

1. Run the following query\. The returned result should be `YES` or `IMPLICIT`\.

   ```
   SELECT supplemental_log_data_min FROM v$database;
   ```

1. Run the following query to enable database\-level supplemental logging\.

   ```
   ALTER DATABASE ADD SUPPLEMENTAL LOG DATA;
   ```

There are two methods to enable table\-level supplemental logging\. In the first one, if your database user account has ALTER TABLE privileges on all tables to be migrated, you can use the extra connection parameter `addSupplementalLogging` as described following\. Otherwise, you can use the steps following for each table in the migration\.

**To enable table\-level supplemental logging**

1. If the table has a primary key, add PRIMARY KEY supplemental logging for the table by running the following command\.

   ```
   ALTER TABLE <table_name> ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY) COLUMNS;
   ```

1. If no primary key exists and the table has multiple unique indexes, then AWS DMS uses the first unique index in alphabetical order of index name\. 

   Create a supplemental log group as shown preceding on that index’s columns\.

1. If there is no primary key and no unique index, supplemental logging must be added on all columns\. Run the following query to add supplemental logging to all columns\.

   ```
   ALTER TABLE <table_name> ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;
   ```

In some cases, the target table primary key or unique index is different than the source table primary key or unique index\. In these cases, add supplemental logging on the source table columns that make up the target table primary key or unique index\. If you change the target table primary key, you should add supplemental logging on the selected index's columns, instead of the columns of the original primary key or unique index\.

Add additional logging if needed, such as if a filter is defined for a table\. If a table has a unique index or a primary key, you need to add supplemental logging on each column that is involved in a filter if those columns are different than the primary key or unique index columns\. However, if ALL COLUMNS supplemental logging has been added to the table, you don't need to add any additional logging\.

```
ALTER TABLE <table_name> ADD SUPPLEMENTAL LOG GROUP <group_name> (<column_list>) ALWAYS;
```

## Working with an Amazon\-Managed Oracle Database as a Source for AWS DMS<a name="CHAP_Source.Oracle.Amazon-Managed"></a>

An Amazon\-managed database is a database that is on an Amazon service such as Amazon RDS, Amazon Aurora, or Amazon S3\. Following, you can find the privileges and configurations you need to set up when using an Amazon\-managed Oracle database with AWS DMS\.

### User Account Privileges Required on an Amazon\-Managed Oracle Source for AWS DMS<a name="CHAP_Source.Oracle.Amazon-Managed.Privileges"></a>

To grant privileges on Oracle databases on Amazon RDS, use the stored procedure `rdsadmin.rdsadmin_util.grant_sys_object`\. For more information, see [Granting SELECT or EXECUTE privileges to SYS Objects](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Appendix.Oracle.CommonDBATasks.html#Appendix.Oracle.CommonDBATasks.TransferPrivileges)\.

Grant the following to the AWS DMS user account used to access the source Oracle endpoint\.
+  `GRANT SELECT ANY TABLE to <dms_user>;`
+  `GRANT SELECT on ALL_VIEWS to <dms_user>;`
+  `GRANT SELECT ANY TRANSACTION to <dms_user>;`
+  Run the following: `exec rdsadmin.rdsadmin_util.grant_sys_object('V_$ARCHIVED_LOG','<dms_user>','SELECT');`
+  Run the following: `exec rdsadmin.rdsadmin_util.grant_sys_object('V_$LOG','<dms_user>','SELECT');`
+  Run the following: `exec rdsadmin.rdsadmin_util.grant_sys_object('V_$LOGFILE','<dms_user>','SELECT');`
+  Run the following: `exec rdsadmin.rdsadmin_util.grant_sys_object('V_$DATABASE','<dms_user>','SELECT');`
+  Run the following: `exec rdsadmin.rdsadmin_util.grant_sys_object('V_$THREAD','<dms_user>','SELECT');`
+  Run the following: `exec rdsadmin.rdsadmin_util.grant_sys_object('V_$PARAMETER','<dms_user>','SELECT');`
+  Run the following: `exec rdsadmin.rdsadmin_util.grant_sys_object('V_$NLS_PARAMETERS','<dms_user>','SELECT');`
+  Run the following: `exec rdsadmin.rdsadmin_util.grant_sys_object('V_$TIMEZONE_NAMES','<dms_user>','SELECT');`
+  Run the following: `exec rdsadmin.rdsadmin_util.grant_sys_object('V_$TRANSACTION','<dms_user>','SELECT');`
+ Run the following: 

  ```
  GRANT SELECT on ALL_INDEXES to <dms_user>;
  GRANT SELECT on ALL_OBJECTS to <dms_user>;
  GRANT SELECT on ALL_TABLES to <dms_user>;
  GRANT SELECT on ALL_USERS to <dms_user>;
  GRANT SELECT on ALL_CATALOG to <dms_user>;
  GRANT SELECT on ALL_CONSTRAINTS to <dms_user>;
  GRANT SELECT on ALL_CONS_COLUMNS to <dms_user>;
  GRANT SELECT on ALL_TAB_COLS to <dms_user>;
  GRANT SELECT on ALL_IND_COLUMNS to <dms_user>;
  GRANT SELECT on ALL_LOG_GROUPS to <dms_user>;
  ```
+  Run the following: `exec rdsadmin.rdsadmin_util.grant_sys_object('DBA_REGISTRY','<dms_user>','SELECT');`
+  Run the following: `exec rdsadmin.rdsadmin_util.grant_sys_object('OBJ$','<dms_user>','SELECT');`
+  `GRANT SELECT on DBA_TABLESPACES to <dms_user>;`
+  `GRANT SELECT on ALL_TAB_PARTITIONS to <dms_user>;`
+ `GRANT LOGMINING TO <dms_user>;`
+  Run the following: `exec rdsadmin.rdsadmin_util.grant_sys_object('ALL_ENCRYPTED_COLUMNS','<dms_user>','SELECT');`
+  Run the following: `exec rdsadmin.rdsadmin_util.grant_sys_object('V_$LOGMNR_LOGS','<dms_user>','SELECT');`
+  Run the following: `exec rdsadmin.rdsadmin_util.grant_sys_object('V_$LOGMNR_CONTENTS','<dms_user>','SELECT');`
+ Run the following: `exec rdsadmin.rdsadmin_util.grant_sys_object('DBMS_LOGMNR','<dms_user>','EXECUTE');` 

### Configuring an Amazon\-Managed Oracle Source for AWS DMS<a name="CHAP_Source.Oracle.Amazon-Managed.Configuration"></a>

Before using an Amazon\-managed Oracle database as a source for AWS DMS, you need to perform several tasks:
+ Provide Oracle account access – You must provide an Oracle user account for AWS DMS\. The user account must have read/write privileges on the Oracle database, as specified in the previous section\.
+ Set the backup retention period for your Amazon RDS database to one day or longer – Setting the backup retention period ensures that the database is running in ARCHIVELOG mode\. For more information about setting the backup retention period, see the [Working with Automated Backups](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithAutomatedBackups.html) in the* Amazon RDS User Guide\.*
+ Set up archive retention – Run the following to retain archived redo logs of your Oracle database instance\. Running this command lets AWS DMS retrieve the log information using LogMiner\. Make sure that you have enough storage for the archived redo logs during the migration period\. 

  In the following example, logs are kept for 24 hours\.

  ```
  exec rdsadmin.rdsadmin_util.set_configuration('archivelog retention hours',24);
  ```
+ Set up supplemental logging – If you are planning to use the source in a CDC or full\-load plus CDC task, then set up supplemental logging to capture the changes for replication\.

  There are two steps to enable supplemental logging for Oracle\. First, you need to enable database\-level supplemental logging\. Doing this ensures that the LogMiner has the minimal information to support various table structures such as clustered and index\-organized tables\. Second, you need to enable table\-level supplemental logging for each table to be migrated\. 

**To enable database\-level supplemental logging**
  + Run the following query to enable database\-level supplemental logging\.

    ```
    exec rdsadmin.rdsadmin_util.alter_supplemental_logging('ADD');
    ```

**To enable table\-level supplemental logging**
  + Run the following command to enable PRIMARY KEY logging for tables that have primary keys\.

    ```
    exec rdsadmin.rdsadmin_util.alter_supplemental_logging('ADD','PRIMARY KEY');
    ```

  For tables that don’t have primary keys, use the following command to add supplemental logging\.

  ```
  alter table <table_name> add supplemental log data (ALL) columns;
  ```

  If you create a table without a primary key, you should either include a supplemental logging clause in the create statement or alter the table to add supplemental logging\. The following command creates a table and adds supplemental logging\.

  ```
  create table <table_name> (<column_list>, supplemental log data (ALL) columns);
  ```

  If you create a table and later add a primary key, you need to add supplemental logging to the table\. Add supplemental logging to the table using the following command\.

  ```
  alter table <table_name> add supplemental log data (PRIMARY KEY) columns;
  ```

### Configuring Change Data Capture \(CDC\) for an Amazon RDS for Oracle Source for AWS DMS<a name="CHAP_Source.Oracle.Amazon-Managed.CDC"></a>

You can configure AWS DMS to use an Amazon RDS for Oracle instance as a source of CDC\. You use Oracle Binary Reader with an Amazon RDS for Oracle source \(Oracle versions 11\.2\.0\.4\.v11 and later, and 12\.1\.0\.2\.v7 and later\)\. 

The following extra connection attributes should be included with the connection information when you create the Amazon RDS for Oracle source endpoint:

```
useLogminerReader=N;useBfile=Y;accessAlternateDirectly=false;
useAlternateFolderForOnline=true;

oraclePathPrefix=/rdsdbdata/db/ORCL_A/;usePathPrefix=/rdsdbdata/log/;
replacePathPrefix=true
```

## Limitations on Using Oracle as a Source for AWS DMS<a name="CHAP_Source.Oracle.Limitations"></a>

The following limitations apply when using an Oracle database as a source for AWS DMS:
+ AWS DMS supports Oracle transparent data encryption \(TDE\) tablespace encryption and AWS Key Management Service \(AWS KMS\) encryption when used with Oracle LogMiner\. All other forms of encryption are not supported\.
+ Tables with LOBs must have a primary key to use CDC\.
+ AWS DMS supports the `rename table <table name> to <new table name>` syntax with Oracle version 11 and higher\. 
+ Oracle source databases columns created using explicit CHAR semantics are transferred to a target Oracle database using BYTE semantics\. You must create tables containing columns of this type on the target Oracle database before migrating\.
+ AWS DMS doesn't replicate data changes resulting from partition or subpartition operations—data definition language \(DDL\) operations such as ADD, DROP, EXCHANGE, or TRUNCATE\. To replicate such changes, you must reload the table being replicated\. AWS DMS replicates any future data changes to newly added partitions without you having to reload the table\. However, UPDATE operations on old data records in partitions fail and generate a `0 rows affected` warning\. 
+ The DDL statement `ALTER TABLE ADD <column> <data_type> DEFAULT <>` doesn't replicate the default value to the target\. The new column in the target is set to NULL\. If the new column is nullable, Oracle updates all the table rows before logging the DDL itself\. As a result, AWS DMS captures the changes to the counters but doesn't update the target\. Because the new column is set to NULL, if the target table has no primary key or unique index, subsequent updates generate a `0 rows affected` warning\. 
+ Data changes resulting from the `CREATE TABLE AS` statement are not supported\. However, the new table is created on the target\. 
+ When limited\-size LOB mode is enabled, AWS DMS replicates empty LOBs on the Oracle source as NULL values in the target\. 
+  When AWS DMS begins CDC, it maps a timestamp to the Oracle system change number \(SCN\)\. By default, Oracle keeps only five days of the timestamp to SCN mapping\. Oracle generates an error if the timestamp specified is too old \(greater than the five\-day retention period\)\. For more information, see the [Oracle documentation](https://docs.oracle.com/cd/E11882_01/server.112/e41084/functions195.htm#SQLRF06326)\. 
+ AWS DMS doesn't support connections to an Oracle source by using an ASM proxy\.
+ AWS DMS doesn't support virtual columns\.

## Extra Connection Attributes When Using Oracle as a Source for AWS DMS<a name="CHAP_Source.Oracle.ConnectionAttrib"></a>

You can use extra connection attributes to configure your Oracle source\. You specify these settings when you create the source endpoint\. Multiple extra connection attribute settings should be separated from each other by semicolons\.

The following table shows the extra connection attributes you can use to configure an Oracle database as a source for AWS DMS\.


| Name | Description | 
| --- | --- | 
| `addSupplementalLogging ` | Set this attribute to set up table\-level supplemental logging for the Oracle database\. This attribute enables PRIMARY KEY supplemental logging on all tables selected for a migration task\. Default value: N  Valid values: Y/N  Example: `addSupplementalLogging=Y`   If you use this option, you still need to enable database\-level supplemental logging as discussed previously\.    | 
| `additionalArchivedLogDestId` | Set this attribute with `archivedLogDestId` in a primary/standby setup\. This attribute is useful in the case of a failover\. In this case, AWS DMS needs to know which destination to get archive redo logs from to read changes, because the previous primary instance is now a standby instance after failover\.  | 
| `useLogminerReader` | Set this attribute to Y to capture change data using the LogMiner utility \(the default\)\. Set this option to N if you want AWS DMS to access the redo logs as a binary file\. When set to N, you must also add the setting useBfile=Y\. For more information, see [Using Oracle LogMiner or Oracle Binary Reader for Change Data Capture \(CDC\)](#CHAP_Source.Oracle.CDC)\. Default value: Y  Valid values: Y/N  Example: `useLogminerReader=N; useBfile=Y`  If the Oracle source database is using Oracle Automatic Storage Management \(ASM\), the extra connection parameter needs to include the ASM user name and ASM server address\. The password field also needs to have both passwords, the source user password and the ASM password, separated from each other by a comma\. Example: `useLogminerReader=N;asm_user=<asm_username>; asm_server=<first_RAC_server_ip_address>:<port_number>/+ASM`  | 
| ` retryInterval ` | Specifies the number of seconds that the system waits before resending a query\.  Default value: 5  Valid values: Numbers starting from 1  Example: `retryInterval=6 ` | 
| `archivedLogDestId` | Specifies the destination of the archived redo logs\. The value should be the same as the DEST\_ID number in the $archived\_log table\. When working with multiple log destinations \(DEST\_ID\), we recommend that you to specify an archived redo logs location identifier\. Doing this improves performance by ensuring that the correct logs are accessed from the outset\.  Default value:0  Valid values: Number  Example: `archivedLogDestId=1 ` | 
| `archivedLogsOnly` | When this field is set to Y, AWS DMS only accesses the archived redo logs\. If the archived redo logs are stored on Oracle ASM only, the AWS DMS user account needs to be granted ASM privileges\.  Default value: N  Valid values: Y/N  Example: `archivedLogsOnly=Y ` | 
| `numberDataTypeScale` | Specifies the number scale\. You can select a scale up to 38, or you can select FLOAT\. By default, the NUMBER data type is converted to precision 38, scale 10\.  Default value: 10  Valid values: \-1 to 38 \(\-1 for FLOAT\)  Example: `numberDataTypeScale =12 ` | 
| afterConnectScript |  Specifies a script to run immediately after AWS DMS connects to the endpoint\.  Valid values: A SQL statement set off by a semicolon\. Not all SQL statements are supported\. Example: `afterConnectScript=ALTER SESSION SET CURRENT_SCHEMA = system; `  | 
| `failTasksOnLobTruncation` | When set to `true`, this attribute causes a task to fail if the actual size of an LOB column is greater than the specified `LobMaxSize`\. If a task is set to limited LOB mode and this option is set to `true`, the task fails instead of truncating the LOB data\. Default value: false  Valid values: Boolean  Example: `failTasksOnLobTruncation=true ` | 
| `readTableSpaceName` | When set to `true`, this attribute supports tablespace replication\. Default value: false  Valid values: Boolean  Example: `readTableSpaceName =true ` | 
| `standbyDelayTime` | Use this attribute to specify a time in minutes for the delay in standby sync\. With AWS DMS version 2\.3\.0 and later, you can create an Oracle ongoing replication \(CDC\) task that uses an Oracle active data guard standby instance as a source for replicating on\-going changes to a supported target\. This eliminates the need to connect to an active database that may be in production\. Default value:0  Valid values: Number  Example: `standbyDelayTime=1 ` | 

## Source Data Types for Oracle<a name="CHAP_Source.Oracle.DataTypes"></a>

The Oracle endpoint for AWS DMS supports most Oracle data types\. The following table shows the Oracle source data types that are supported when using AWS DMS and the default mapping to AWS DMS data types\.

For information on how to view the data type that is mapped in the target, see the section for the target endpoint you are using\.

For additional information about AWS DMS data types, see [Data Types for AWS Database Migration Service](CHAP_Reference.DataTypes.md)\.


|  Oracle Data Type  |  AWS DMS Data Type  | 
| --- | --- | 
|  BINARY\_FLOAT  |  REAL4  | 
|  BINARY\_DOUBLE  |  REAL8  | 
|  BINARY  |  BYTES  | 
|  FLOAT \(P\)  |  If precision is less than or equal to 24, use REAL4\. If precision is greater than 24, use REAL8\.  | 
|  NUMBER \(P,S\)  |  When scale is less than 0, use REAL8  | 
|  NUMBER according to the "Expose number as" property in the Oracle source database settings\.  |  When scale is 0: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Source.Oracle.html) In all other cases, use REAL8\. | 
|  DATE  |  DATETIME  | 
|  INTERVAL\_YEAR TO MONTH  |  STRING \(with interval year\_to\_month indication\)  | 
|  INTERVAL\_DAY TO SECOND  |  STRING \(with interval day\_to\_second indication\)  | 
|  TIME  |  DATETIME  | 
|  TIMESTAMP  |  DATETIME  | 
|  TIMESTAMP WITH TIME ZONE  |  STRING \(with timestamp\_with\_timezone indication\)  | 
|  TIMESTAMP WITH LOCAL TIME ZONE  |  STRING \(with timestamp\_with\_local\_ timezone indication\)  | 
|  CHAR  |  STRING  | 
|  VARCHAR2  |  STRING  | 
|  NCHAR  |  WSTRING  | 
|  NVARCHAR2  |  WSTRING  | 
|  RAW  |  BYTES  | 
|  REAL  |  REAL8  | 
|  BLOB  |  BLOB To use this data type with AWS DMS, you must enable the use of BLOB data types for a specific task\. AWS DMS supports BLOB data types only in tables that include a primary key\.  | 
|  CLOB  |  CLOB To use this data type with AWS DMS, you must enable the use of CLOB data types for a specific task\. During change data capture \(CDC\), AWS DMS supports CLOB data types only in tables that include a primary key\.  | 
|  NCLOB  |  NCLOB To use this data type with AWS DMS, you must enable the use of NCLOB data types for a specific task\. During CDC, AWS DMS supports NCLOB data types only in tables that include a primary key\.  | 
|  LONG  |  CLOB The LONG data type is not supported in batch\-optimized apply mode \(TurboStream CDC mode\)\. To use this data type with AWS DMS, you must enable the use of LOBs for a specific task\. During CDC, AWS DMS supports LOB data types only in tables that have a primary key\.  | 
|  LONG RAW  |  BLOB The LONG RAW data type is not supported in batch\-optimized apply mode \(TurboStream CDC mode\)\. To use this data type with AWS DMS, you must enable the use of LOBs for a specific task\. During CDC, AWS DMS supports LOB data types only in tables that have a primary key\.  | 
|  XMLTYPE  |  CLOB Support for the XMLTYPE data type requires the full Oracle Client \(as opposed to the Oracle Instant Client\)\. When the target column is a CLOB, both full LOB mode and limited LOB mode are supported \(depending on the target\)\.  | 

Oracle tables used as a source with columns of the following data types are not supported and cannot be replicated\. Replicating columns with these data types result in a null column\.
+ BFILE
+ ROWID
+ REF
+ UROWID
+ Nested Table
+ User\-defined data types
+ ANYDATA

**Note**  
Virtual columns are not supported\.