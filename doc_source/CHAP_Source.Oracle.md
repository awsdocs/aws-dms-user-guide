# Using an Oracle database as a source for AWS DMS<a name="CHAP_Source.Oracle"></a>

You can migrate data from one or many Oracle databases using AWS DMS\. With an Oracle database as a source, you can migrate data to any of the targets supported by AWS DMS\.

AWS DMS supports the following Oracle database editions:
+ Oracle Enterprise Edition
+ Oracle Standard Edition
+ Oracle Express Edition
+ Oracle Personal Edition

For self\-managed Oracle databases, AWS DMS supports all Oracle database editions for versions 10\.2 and later \(for versions 10\.x\), 11g and up to 12\.2, 18c, and 19c\. For Amazon RDS for Oracle databases that AWS manages, AWS DMS supports all Oracle database editions for versions 11g \(versions 11\.2\.0\.4 and later\) and up to 12\.2, 18c, and 19c\.

You can use Secure Sockets Layer \(SSL\) to encrypt connections between your Oracle endpoint and your replication instance\. For more information on using SSL with an Oracle endpoint, see [Using SSL with AWS Database Migration Service](CHAP_Security.md#CHAP_Security.SSL)\. AWS DMS also supports the use of Oracle transparent data encryption \(TDE\) to encrypt data at rest in the source database\. For more information on using Oracle TDE with an Oracle source endpoint, see [Supported encryption methods for using Oracle as a source for AWS DMS](#CHAP_Source.Oracle.Encryption)\.

Follow these steps to configure an Oracle database as an AWS DMS source endpoint:

1. Create an Oracle user with the appropriate permissions for AWS DMS to access your Oracle source database\.

1. Create an Oracle source endpoint that conforms with your chosen Oracle database configuration\. To create a full\-load\-only task, no further configuration is needed\.

1. To create a task that handles change data capture \(a CDC\-only or full\-load and CDC task\), choose Oracle LogMiner or AWS DMS Binary Reader to capture data changes\. Choosing LogMiner or Binary Reader determines some of the later permissions and configuration options\. For a comparison of LogMiner and Binary Reader, see the following section\.

**Note**  
For more information on full\-load tasks, CDC\-only tasks, and full\-load and CDC tasks, see [Creating a task](CHAP_Tasks.Creating.md)

For additional details on working with Oracle source databases and AWS DMS, see the following sections\. 

**Topics**
+ [Using Oracle LogMiner or AWS DMS Binary Reader for CDC](#CHAP_Source.Oracle.CDC)
+ [Workflows for configuring a self\-managed or AWS\-managed Oracle source database for AWS DMS](#CHAP_Source.Oracle.Workflows)
+ [Working with a self\-managed Oracle database as a source for AWS DMS](#CHAP_Source.Oracle.Self-Managed)
+ [Working with an AWS\-managed Oracle database as a source for AWS DMS](#CHAP_Source.Oracle.Amazon-Managed)
+ [Limitations on using Oracle as a source for AWS DMS](#CHAP_Source.Oracle.Limitations)
+ [SSL support for an Oracle endpoint](#CHAP_Security.SSL.Oracle)
+ [Supported encryption methods for using Oracle as a source for AWS DMS](#CHAP_Source.Oracle.Encryption)
+ [Supported compression methods for using Oracle as a source for AWS DMS](#CHAP_Source.Oracle.Compression)
+ [Replicating nested tables using Oracle as a source for AWS DMS](#CHAP_Source.Oracle.NestedTables)
+ [Extra connection attributes when using Oracle as a source for AWS DMS](#CHAP_Source.Oracle.ConnectionAttrib)
+ [Source data types for Oracle](#CHAP_Source.Oracle.DataTypes)

## Using Oracle LogMiner or AWS DMS Binary Reader for CDC<a name="CHAP_Source.Oracle.CDC"></a>

In AWS DMS, there are two methods for reading the redo logs when doing change data capture \(CDC\) for Oracle as a source: Oracle LogMiner and AWS DMS Binary Reader\. LogMiner is an Oracle API to read the online redo logs and archived redo log files\. Binary Reader is an AWS DMS method that reads and parses the raw redo log files directly\. These methods have the following features\.


| Feature | LogMiner | Binary Reader | 
| --- | --- | --- | 
| Easy to configure | Yes | No | 
| Lower impact on source system I/O and CPU | No | Yes | 
| Better CDC performance | No | Yes | 
| Supports Oracle table clusters | Yes | No | 
| Supports all types of Oracle Hybrid Columnar Compression \(HCC\) | Yes |  Partially Binary Reader doesn't support QUERY LOW for tasks with CDC\. All other HCC types are fully supported\.  | 
| LOB column support in Oracle 12c only | No | Yes | 
| Supports UPDATE statements that affect only LOB columns | No | Yes | 
| Supports Oracle transparent data encryption \(TDE\) | Yes |  Partially Binary Reader supports TDE only for self\-managed Oracle databases\.  | 
| Supports all Oracle compression methods | Yes | No | 
| Supports XA transactions | No | Yes | 
| RAC |  Yes Not recommended  |  Yes Highly recommended  | 

**Note**  
By default, AWS DMS uses Oracle LogMiner for \(CDC\)\. 

The main advantages of using LogMiner with AWS DMS include the following:
+ LogMiner supports most Oracle options, such as encryption options and compression options\. Binary Reader doesn't support all Oracle options, particularly compression and most options for encryption\.
+ LogMiner offers a simpler configuration, especially compared to Binary Reader direct\-access setup or when the redo logs are managed using Oracle Automatic Storage Management \(ASM\)\.
+ LogMiner supports table clusters for use by AWS DMS\. Binary Reader doesn't\.

The main advantages of using Binary Reader with AWS DMS include the following:
+ For migrations with a high volume of changes, LogMiner might have some I/O or CPU impact on the computer hosting the Oracle source database\. Binary Reader has less chance of having I/O or CPU impact because logs are mined directly rather than making multiple database queries\.
+ For migrations with a high volume of changes, CDC performance is usually much better when using Binary Reader compared with using Oracle LogMiner\.
+ Binary Reader supports CDC for LOBs in Oracle version 12c\. LogMiner doesn't\.

In general, use Oracle LogMiner for migrating your Oracle database unless you have one of the following situations:
+ You need to run several migration tasks on the source Oracle database\.
+ The volume of changes or the redo log volume on the source Oracle database is high, or you have changes and are also using Oracle ASM\.

**Note**  
If you change between using Oracle LogMiner and AWS DMS Binary Reader, make sure to restart the CDC task\. 

### Configuration for CDC on an Oracle source database<a name="CHAP_Source.Oracle.CDC.Configuration"></a>

For an Oracle source endpoint to connect to the database for a change data capture \(CDC\) task, you might need to specify extra connection attributes\. This can be true for either a full\-load and CDC task or for a CDC\-only task\. The extra connection attributes that you specify depend on the method you use to access the redo logs: Oracle LogMiner or AWS DMS Binary Reader\. 

You specify extra connection attributes when you create a source endpoint\. If you have multiple connection attribute settings, separate them from each other by semicolons with no additional white space \(for example, `oneSetting;thenAnother`\)\.

AWS DMS uses LogMiner by default\. You don't have to specify additional extra connection attributes to use it\. 

To use Binary Reader to access the redo logs, add the following extra connection attributes\.

```
useLogMinerReader=N;useBfile=Y;
```

Use the following format for the extra connection attributes to access a server that uses ASM with Binary Reader\.

```
useLogMinerReader=N;useBfile=Y;asm_user=asm_username;asm_server=RAC_server_ip_address:port_number/+ASM;
```

Set the source endpoint `Password` request parameter to both the Oracle user password and the ASM password, separated by a comma as follows\.

```
oracle_user_password,asm_user_password
```

Where the Oracle source uses ASM, you can work with high\-performance options in Binary Reader for transaction processing at scale\. These options include extra connection attributes to specify the number of parallel threads \(`parallelASMReadThreads`\) and the number of read\-ahead buffers \(`readAheadBlocks`\)\. Setting these attributes together can significantly improve the performance of the CDC task\. The following settings provide good results for most ASM configurations\.

```
useLogMinerReader=N;useBfile=Y;asm_user=asm_username;asm_server=RAC_server_ip_address:port_number/+ASM;
    parallelASMReadThreads=6;readAheadBlocks=150000;
```

For more information on values that extra connection attributes support, see [Extra connection attributes when using Oracle as a source for AWS DMS](#CHAP_Source.Oracle.ConnectionAttrib)\.

In addition, the performance of a CDC task with an Oracle source that uses ASM depends on other settings that you choose\. These settings include your AWS DMS extra connection attributes and the SQL settings to configure the Oracle source\. For more information on extra connection attributes for an Oracle source using ASM, see [Extra connection attributes when using Oracle as a source for AWS DMS](#CHAP_Source.Oracle.ConnectionAttrib)

You also need to choose an appropriate CDC start point\. Typically when you do this, you want to identify the point of transaction processing that captures the earliest open transaction to begin CDC from\. Otherwise, the CDC task can miss earlier open transactions\. For an Oracle source database, you can choose a CDC native start point based on the Oracle system change number \(SCN\) to identify this earliest open transaction\. For more information, see [Performing replication starting from a CDC start point](CHAP_Task.CDC.md#CHAP_Task.CDC.StartPoint)\.

For more information on configuring CDC for a self\-managed Oracle database as a source, see [Account privileges required when using Oracle LogMiner to access the redo logs](#CHAP_Source.Oracle.Self-Managed.LogMinerPrivileges), [Account privileges required when using AWS DMS Binary Reader to access the redo logs](#CHAP_Source.Oracle.Self-Managed.BinaryReaderPrivileges), and [Additional account privileges required when using Binary Reader with Oracle ASM](#CHAP_Source.Oracle.Self-Managed.ASMBinaryPrivileges)\.

For more information on configuring CDC for an AWS\-managed Oracle database as a source, see [ Configuring a CDC task to use Binary Reader with an RDS for Oracle source for AWS DMS](#CHAP_Source.Oracle.Amazon-Managed.CDC) and [Using an Amazon RDS Oracle Standby \(read replica\) as a source with Binary Reader for CDC in AWS DMS](#CHAP_Source.Oracle.Amazon-Managed.StandBy)\.

## Workflows for configuring a self\-managed or AWS\-managed Oracle source database for AWS DMS<a name="CHAP_Source.Oracle.Workflows"></a>

To configure a self\-managed source database instance, use the following workflow steps , depending on how you perform CDC\.  


| For this workflow step | If you perform CDC using LogMiner, do this | If you perform CDC using Binary Reader, do this | 
| --- | --- | --- | 
| Grant Oracle account privileges\. | See [User account privileges required on a self\-managed Oracle source for AWS DMS](#CHAP_Source.Oracle.Self-Managed.Privileges)\. | See [User account privileges required on a self\-managed Oracle source for AWS DMS](#CHAP_Source.Oracle.Self-Managed.Privileges)\. | 
| Prepare the source database for replication using CDC\. | See [Preparing an Oracle self\-managed source database for CDC using AWS DMS](#CHAP_Source.Oracle.Self-Managed.Configuration)\. | See [Preparing an Oracle self\-managed source database for CDC using AWS DMS](#CHAP_Source.Oracle.Self-Managed.Configuration)\. | 
| Grant additional Oracle user privileges required for CDC\. | See [Account privileges required when using Oracle LogMiner to access the redo logs](#CHAP_Source.Oracle.Self-Managed.LogMinerPrivileges)\. | See [Account privileges required when using AWS DMS Binary Reader to access the redo logs](#CHAP_Source.Oracle.Self-Managed.BinaryReaderPrivileges)\. | 
| For an Oracle instance with ASM, grant additional user account privileges required to access ASM for CDC\. | No additional action\. AWS DMS supports Oracle ASM without additional account privileges\. | See [Additional account privileges required when using Binary Reader with Oracle ASM](#CHAP_Source.Oracle.Self-Managed.ASMBinaryPrivileges)\. | 
| If you haven't already done so, configure the task to use LogMiner or Binary Reader for CDC\. | See [Using Oracle LogMiner or AWS DMS Binary Reader for CDC](#CHAP_Source.Oracle.CDC)\. | See [Using Oracle LogMiner or AWS DMS Binary Reader for CDC](#CHAP_Source.Oracle.CDC)\. | 
| Configure Oracle Standby as a source for CDC\. | AWS DMS doesn't support Oracle Standby as a source\. | See [Using a self\-managed Oracle Standby as a source with Binary Reader for CDC in AWS DMS](#CHAP_Source.Oracle.Self-Managed.BinaryStandby)\. | 

Use the following workflow steps to configure an AWS\-managed Oracle source database instance\.


| For this workflow step | If you perform CDC using LogMiner, do this | If you perform CDC using Binary Reader, do this | 
| --- | --- | --- | 
| Grant Oracle account privileges\. | For more information, see [User account privileges required on an AWS\-managed Oracle source for AWS DMS](#CHAP_Source.Oracle.Amazon-Managed.Privileges)\. | For more information, see [User account privileges required on an AWS\-managed Oracle source for AWS DMS](#CHAP_Source.Oracle.Amazon-Managed.Privileges)\. | 
| Prepare the source database for replication using CDC\. | For more information, see [ Configuring an AWS\-managed Oracle source for AWS DMS](#CHAP_Source.Oracle.Amazon-Managed.Configuration)\. | For more information, see [ Configuring an AWS\-managed Oracle source for AWS DMS](#CHAP_Source.Oracle.Amazon-Managed.Configuration)\. | 
| Grant additional Oracle user privileges required for CDC\. | No additional account privileges are required\. | For more information, see [ Configuring a CDC task to use Binary Reader with an RDS for Oracle source for AWS DMS](#CHAP_Source.Oracle.Amazon-Managed.CDC)\. | 
| If you haven't already done so, configure the task to use LogMiner or Binary Reader for CDC\. | For more information, see [Using Oracle LogMiner or AWS DMS Binary Reader for CDC](#CHAP_Source.Oracle.CDC)\. | For more information, see [Using Oracle LogMiner or AWS DMS Binary Reader for CDC](#CHAP_Source.Oracle.CDC)\. | 
| Configure Oracle Standby as a source for CDC\. | AWS DMS doesn't support Oracle Standby as a source\. | For more information, see [Using an Amazon RDS Oracle Standby \(read replica\) as a source with Binary Reader for CDC in AWS DMS](#CHAP_Source.Oracle.Amazon-Managed.StandBy)\. | 

## Working with a self\-managed Oracle database as a source for AWS DMS<a name="CHAP_Source.Oracle.Self-Managed"></a>

A *self\-managed database* is a database that you configure and control, either a local on\-premises database instance or a database on Amazon EC2\. Following, you can find out about the privileges and configurations you need when using a self\-managed Oracle database with AWS DMS\.

### User account privileges required on a self\-managed Oracle source for AWS DMS<a name="CHAP_Source.Oracle.Self-Managed.Privileges"></a>

To use an Oracle database as a source in AWS DMS, grant the following privileges to the Oracle user specified in the Oracle endpoint connection settings\.

**Note**  
When granting privileges, use the actual name of objects, not the synonym for each object\. For example, use `V_$OBJECT` including the underscore, not `V$OBJECT` without the underscore\.

```
GRANT CREATE SESSION TO db_user;
GRANT SELECT ANY TRANSACTION TO db_user;
GRANT SELECT ON V_$ARCHIVED_LOG TO db_user;
GRANT SELECT ON V_$LOG TO db_user;
GRANT SELECT ON V_$LOGFILE TO db_user;
GRANT SELECT ON V_$LOGMNR_LOGS TO db_user;
GRANT SELECT ON V_$LOGMNR_CONTENTS TO db_user;
GRANT SELECT ON V_$DATABASE TO db_user;
GRANT SELECT ON V_$THREAD TO db_user;
GRANT SELECT ON V_$PARAMETER TO db_user;
GRANT SELECT ON V_$NLS_PARAMETERS TO db_user;
GRANT SELECT ON V_$TIMEZONE_NAMES TO db_user;
GRANT SELECT ON V_$TRANSACTION TO db_user;
GRANT SELECT ON V_$CONTAINERS TO db_user;                   
GRANT SELECT ON ALL_INDEXES TO db_user;
GRANT SELECT ON ALL_OBJECTS TO db_user;
GRANT SELECT ON ALL_TABLES TO db_user;
GRANT SELECT ON ALL_USERS TO db_user;
GRANT SELECT ON ALL_CATALOG TO db_user;
GRANT SELECT ON ALL_CONSTRAINTS TO db_user;
GRANT SELECT ON ALL_CONS_COLUMNS TO db_user;
GRANT SELECT ON ALL_TAB_COLS TO db_user;
GRANT SELECT ON ALL_IND_COLUMNS TO db_user;
GRANT SELECT ON ALL_ENCRYPTED_COLUMNS TO db_user;
GRANT SELECT ON ALL_LOG_GROUPS TO db_user;
GRANT SELECT ON ALL_TAB_PARTITIONS TO db_user;
GRANT SELECT ON SYS.DBA_REGISTRY TO db_user;
GRANT SELECT ON SYS.OBJ$ TO db_user;
GRANT SELECT ON DBA_TABLESPACES TO db_user;
GRANT SELECT ON DBA_OBJECTS TO db_user; -– Required if the Oracle version is earlier than 11.2.0.3.
GRANT SELECT ON SYS.ENC$ TO db_user; -– Required if transparent data encryption (TDE) is enabled. For more information on using Oracle TDE with AWS DMS, see .
```

Grant the additional following privilege for each replicated table when you are using a specific table list\.

```
GRANT SELECT on any-replicated-table to db_user;
```

Grant the additional following privilege to validate LOB columns with the validation feature\.

```
GRANT EXECUTE ON SYS.DBMS_CRYPTO TO db_user;
```

Grant the additional following privilege if you use binary reader instead of LogMiner\.

```
GRANT SELECT ON SYS.DBA_DIRECTORIES TO db_user;
```

Grant the additional following privilege to expose views\.

```
GRANT SELECT on ALL_VIEWS to dms_user;
```

To expose views, you must also add the `exposeViews=true` extra connection attribute to your source endpoint\.

### Preparing an Oracle self\-managed source database for CDC using AWS DMS<a name="CHAP_Source.Oracle.Self-Managed.Configuration"></a>

Prepare your self\-managed Oracle database as a source to run a CDC task by doing the following: 
+ [Verifying that AWS DMS supports the source database version](#CHAP_Source.Oracle.Self-Managed.Configuration.DbVersion)\.
+ [Making sure that ARCHIVELOG mode is on](#CHAP_Source.Oracle.Self-Managed.Configuration.ArchiveLogMode)\.
+ [Setting up supplemental logging](#CHAP_Source.Oracle.Self-Managed.Configuration.SupplementalLogging)\.

#### Verifying that AWS DMS supports the source database version<a name="CHAP_Source.Oracle.Self-Managed.Configuration.DbVersion"></a>

Run a query like the following to verify that the current version of the Oracle source database is supported by AWS DMS\.

```
SELECT name, value, description FROM v$parameter WHERE name = 'compatible';
```

Here, `name`, `value`, and `description` are columns somewhere in the database that are being queried based on the value of `name`\. If this query runs without error, AWS DMS supports the current version of the database and you can continue with the migration\. If the query raises an error, AWS DMS doesn't support the current version of the database\. To proceed with migration, first convert the Oracle database to an version supported by AWS DMS\.

#### Making sure that ARCHIVELOG mode is on<a name="CHAP_Source.Oracle.Self-Managed.Configuration.ArchiveLogMode"></a>

You can run Oracle in two different modes: the `ARCHIVELOG` mode and the `NOARCHIVELOG` mode\. To run a CDC task, run the database in `ARCHIVELOG` mode\. To know if the database is in `ARCHIVELOG` mode, execute the following query\.

```
SQL> SELECT log_mode FROM v$database;
```

If `NOARCHIVELOG` mode is returned, set the database to `ARCHIVELOG` per Oracle instructions\. 

#### Setting up supplemental logging<a name="CHAP_Source.Oracle.Self-Managed.Configuration.SupplementalLogging"></a>

To capture ongoing changes, AWS DMS requires that you enable minimal supplemental logging on your Oracle source database\. In addition, you need to enable supplemental logging on each replicated table in the database\.

By default, AWS DMS adds `PRIMARY KEY` supplemental logging on all replicated tables\. To allow AWS DMS to add `PRIMARY KEY` supplemental logging, grant the following privilege for each replicated table\.

```
ALTER on any-replicated-table;
```

You can disable the default `PRIMARY KEY` supplemental logging added by AWS DMS using the extra connection attribute `addSupplementalLogging`\. For more information, see [Extra connection attributes when using Oracle as a source for AWS DMS](#CHAP_Source.Oracle.ConnectionAttrib)\.

Make sure to turn on supplemental logging if your replication task updates a table using a `WHERE` clause that doesn't reference a primary key column\.

**To manually set up supplemental logging**

1. Run the following query to verify if supplemental logging is already enabled for the database\.

   ```
   SELECT supplemental_log_data_min FROM v$database;
   ```

   If the result returned is `YES` or `IMPLICIT`, supplemental logging is enabled for the database\.

   If not, enable supplemental logging for the database by running the following command\.

   ```
   ALTER DATABASE ADD SUPPLEMENTAL LOG DATA;
   ```

1. Make sure that the required supplemental logging is added for each replicated table\.

   Consider the following:
   + If `ALL COLUMNS` supplemental logging is added to the table, you don't need to add more logging\.
   + If a primary key exists, add supplemental logging for the primary key\. You can do this either by using the format to add supplemental logging on the primary key itself, or by adding supplemental logging on the primary key columns on the database\.

     ```
     ALTER TABLE Tablename ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY) COLUMNS;
     ALTER DATABASE ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY) COLUMNS;
     ```
   + If no primary key exists and the table has a single unique index, add all of the unique index's columns to the supplemental log\.

     ```
     ALTER TABLE TableName ADD SUPPLEMENTAL LOG GROUP LogGroupName (UniqueIndexColumn1[, UniqueIndexColumn2] ...) ALWAYS;
     ```

     Using `SUPPLEMENTAL LOG DATA (UNIQUE INDEX) COLUMNS` doesn't add the unique index columns to the log\.
   + If no primary key exists and the table has multiple unique indexes, AWS DMS selects the first unique index in an alphabetically ordered ascending list\. You need to add supplemental logging on the selected index’s columns as in the previous item\.

     Using `SUPPLEMENTAL LOG DATA (UNIQUE INDEX) COLUMNS` doesn't add the unique index columns to the log\.
   + If no primary key exists and there is no unique index, add supplemental logging on all columns\.

     ```
     ALTER TABLE TableName ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;
     ```

     In some cases, the target table primary key or unique index is different than the source table primary key or unique index\. In such cases, add supplemental logging manually on the source table columns that make up the target table primary key or unique index\.

     Also, if you change the target table primary key, add supplemental logging on the target unique index's columns instead of the columns of the source primary key or unique index\.
   + When tables are updated using WHERE clauses not referencing primary key columns, use supplemental logging for all columns\.

If a filter or transformation is defined for a table, you might need to enable additional logging\.

Consider the following:
+ If `ALL COLUMNS` supplemental logging is added to the table, you don't need to add more logging\.
+ If the table has a unique index or a primary key, add supplemental logging on each column that is involved in a filter or transformation\. However, do so only if those columns are different from the primary key or unique index columns\.
+ If a transformation includes only one column, don't add this column to a supplemental logging group\. For example, for a transformation `A+B`, add supplemental logging on both columns `A` and `B`\. However, for a transformation `substring(A,10)` don't add supplemental logging on column `A`\.
+ To set up supplemental logging on primary key or unique index columns and other columns that are filtered or transformed, you can set up `USER_LOG_GROUP` supplemental logging\. Add this logging on both the primary key or unique index columns and any other specific columns that are filtered or transformed\.

  For example, to replicate a table named `TEST.LOGGING` with primary key `ID` and a filter by the column `NAME`, you can run a command similar to the following to create the log group supplemental logging\.

  ```
  ALTER TABLE TEST.LOGGING ADD SUPPLEMENTAL LOG GROUP TEST_LOG_GROUP (ID, NAME) ALWAYS;
  ```

### Account privileges required when using Oracle LogMiner to access the redo logs<a name="CHAP_Source.Oracle.Self-Managed.LogMinerPrivileges"></a>

To access the redo logs using the Oracle LogMiner, grant the following privileges to the Oracle user specified in the Oracle endpoint connection settings\.

```
GRANT EXECUTE on DBMS_LOGMNR to db_user
GRANT SELECT on V_$LOGMNR_LOGS to db_user
GRANT SELECT on V_$LOGMNR_CONTENTS to db_user
GRANT LOGMINING to db_user; -– Required only if the Oracle version is 12c or later.
```

### Account privileges required when using AWS DMS Binary Reader to access the redo logs<a name="CHAP_Source.Oracle.Self-Managed.BinaryReaderPrivileges"></a>

To access the redo logs using the AWS DMS Binary Reader, grant the following privileges to the Oracle user specified in the Oracle endpoint connection settings\.

```
SELECT on v_$transportable_platform -– Grant this privilege if the redo logs are stored in Oracle Automatic Storage Management (ASM) and AWS DMS accesses them from ASM.
CREATE ANY DIRECTORY -– Grant this privilege to allow AWS DMS to use Oracle BFILE read file access in certain cases. This access is required when the replication instance doesn't have file-level access to the redo logs and the redo logs are on non-ASM storage.
EXECUTE on DBMS_FILE_TRANSFER package -– Grant this privilege to copy the redo log files to a temporary folder using the CopyToTempFolder method.
EXECUTE on DBMS_FILE_GROUP
```

Binary Reader works with Oracle file features that include Oracle directories\. Each Oracle directory object includes the name of the folder containing the redo log files to process\. These Oracle directories aren't represented at the file system level\. Instead, they are logical directories that are created at the Oracle database level\. You can view them in the Oracle `ALL_DIRECTORIES` view\.

If you want AWS DMS to create these Oracle directories, grant the `CREATE ANY DIRECTORY` privilege specified preceding\. AWS DMS creates the directory names with the `DMS_` prefix\. If you don't grant the `CREATE ANY DIRECTORY` privilege, create the corresponding directories manually\. In some cases when you create the Oracle directories manually, the Oracle user specified in the Oracle source endpoint isn't the user that created these directories\. In these cases, also grant the `READ on DIRECTORY` privilege\.

If the Oracle source endpoint is in Active Dataguard Standby \(ADG\), see the [How to use Binary Reader with ADG](http://aws.amazon.com/blogs/database/aws-dms-now-supports-binary-reader-for-amazon-rds-for-oracle-and-oracle-standby-as-a-source/) post on the AWS Database Blog\.

**Note**  
AWS DMS CDC doesn't support Active Dataguard Standby that is not configured to use automatic redo transport service\.

In some cases, you might use Oracle Managed Files \(OMF\) for storing the logs\. Or your source endpoint is in ADG and thus you can't grant the CREATE ANY DIRECTORY privilege\. In these cases, manually create the directories with all the possible log locations before starting the AWS DMS replication task\. If AWS DMS doesn't find a precreated directory that it expects, the task stops\. Also, AWS DMS doesn't delete the entries it has created in the `ALL_DIRECTORIES` view, so manually delete them\.

### Additional account privileges required when using Binary Reader with Oracle ASM<a name="CHAP_Source.Oracle.Self-Managed.ASMBinaryPrivileges"></a>

To access the redo logs in Automatic Storage Management \(ASM\) using Binary Reader, grant the following privileges to the Oracle user specified in the Oracle endpoint connection settings\.

```
SELECT ON v_$transportable_platform
SYSASM -– To access the ASM account with Oracle 11g Release 2 (version 11.2.0.2) and later, grant the Oracle endpoint user the SYSASM privilege. For older supported Oracle versions, it's typically sufficient to grant the Oracle endpoint user the SYSDBA privilege.
```

You can validate ASM account access by opening a command prompt and invoking one of the following statements, depending on your Oracle version as specified preceding\.

If you need the `SYSDBA` privilege, use the following\.

```
sqlplus asmuser/asmpassword@+asmserver as sysdba
```

If you need the `SYSASM` privilege, use the following\. 

```
sqlplus asmuser/asmpassword@+asmserver as sysasm
```

### Using a self\-managed Oracle Standby as a source with Binary Reader for CDC in AWS DMS<a name="CHAP_Source.Oracle.Self-Managed.BinaryStandby"></a>

To configure an Oracle Standby instance as a source when using Binary Reader for CDC, start with the following prerequisites:
+ AWS DMS currently supports only Oracle Active Data Guard Standby\.
+ Make sure that the Oracle Data Guard configuration uses:
  + Redo transport services for automated transfers of redo data\.
  + Apply services to automatically apply redo to the standby database\.

**To configure an Oracle Standby instance as a source when using Binary Reader for CDC**

1. Grant additional privileges required to access standby log files\.

   ```
   GRANT SELECT ON v_$standby_log TO db_user;
   ```

1. Create a source endpoint for the Oracle Standby by using the AWS Management Console or AWS CLI\. When creating the endpoint, specify the following extra connection attributes\.

   ```
   useLogminerReader=N;useBfile=Y
   ```

**Note**  
In AWS DMS, you can use extra connection attributes to specify if you want to migrate from the archive logs instead of the redo logs\. For more information, see [Extra connection attributes when using Oracle as a source for AWS DMS](#CHAP_Source.Oracle.ConnectionAttrib)\.

## Working with an AWS\-managed Oracle database as a source for AWS DMS<a name="CHAP_Source.Oracle.Amazon-Managed"></a>

An AWS\-managed database is a database that is on an Amazon service such as Amazon RDS, Amazon Aurora, or Amazon S3\. Following, you can find the privileges and configurations that you need to set up when using an AWS\-managed Oracle database with AWS DMS\.

### User account privileges required on an AWS\-managed Oracle source for AWS DMS<a name="CHAP_Source.Oracle.Amazon-Managed.Privileges"></a>

Grant the following privileges to the Oracle user account specified in the Oracle source endpoint definition\.

**Important**  
For all parameter values such as `db_user` and `any-replicated-table`, Oracle assumes the value is all uppercase unless you specify the value with a case\-sensitive identifier\. For example, suppose that you create a `db_user` value without using quotation marks, as in `CREATE USER myuser` or `CREATE USER MYUSER`\. In this case, Oracle identifies and stores the value as all uppercase \(`MYUSER`\)\. If you use quotation marks, as in `CREATE USER "MyUser"` or `CREATE USER 'MyUser'`, Oracle identifies and stores the case\-sensitive value that you specify \(`MyUser`\)\.

```
GRANT CREATE SESSION to db_user;
GRANT SELECT ANY TRANSACTION to db_user;
GRANT SELECT on DBA_TABLESPACES to db_user;
GRANT SELECT ON any-replicated-table to db_user;
GRANT EXECUTE on rdsadmin.rdsadmin_util to db_user;
 -- For Oracle 12c only:
GRANT LOGMINING to db_user
```

In addition, grant `SELECT` and `EXECUTE` permissions on `SYS` objects using the Amazon RDS procedure `rdsadmin.rdsadmin_util.grant_sys_object` as shown\. For more information, see [Granting SELECT or EXECUTE privileges to SYS objects](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Appendix.Oracle.CommonDBATasks.html#Appendix.Oracle.CommonDBATasks.TransferPrivileges)\.

```
exec rdsadmin.rdsadmin_util.grant_sys_object('ALL_VIEWS', 'db_user', 'SELECT');
exec rdsadmin.rdsadmin_util.grant_sys_object('ALL_TAB_PARTITIONS', 'db_user', 'SELECT');
exec rdsadmin.rdsadmin_util.grant_sys_object('ALL_INDEXES', 'db_user', 'SELECT');
exec rdsadmin.rdsadmin_util.grant_sys_object('ALL_OBJECTS', 'db_user', 'SELECT');
exec rdsadmin.rdsadmin_util.grant_sys_object('ALL_TABLES', 'db_user', 'SELECT');
exec rdsadmin.rdsadmin_util.grant_sys_object('ALL_USERS', 'db_user', 'SELECT');
exec rdsadmin.rdsadmin_util.grant_sys_object('ALL_CATALOG', 'db_user', 'SELECT');
exec rdsadmin.rdsadmin_util.grant_sys_object('ALL_CONSTRAINTS', 'db_user', 'SELECT');
exec rdsadmin.rdsadmin_util.grant_sys_object('ALL_CONS_COLUMNS', 'db_user', 'SELECT');
exec rdsadmin.rdsadmin_util.grant_sys_object('ALL_TAB_COLS', 'db_user', 'SELECT');
exec rdsadmin.rdsadmin_util.grant_sys_object('ALL_IND_COLUMNS', 'db_user', 'SELECT');
exec rdsadmin.rdsadmin_util.grant_sys_object('ALL_LOG_GROUPS', 'db_user', 'SELECT');
exec rdsadmin.rdsadmin_util.grant_sys_object('V_$ARCHIVED_LOG', 'db_user', 'SELECT');
exec rdsadmin.rdsadmin_util.grant_sys_object('V_$LOG', 'db_user', 'SELECT');
exec rdsadmin.rdsadmin_util.grant_sys_object('V_$LOGFILE', 'db_user', 'SELECT');
exec rdsadmin.rdsadmin_util.grant_sys_object('V_$DATABASE', 'db_user', 'SELECT');
exec rdsadmin.rdsadmin_util.grant_sys_object('V_$THREAD', 'db_user', 'SELECT');
exec rdsadmin.rdsadmin_util.grant_sys_object('V_$PARAMETER', 'db_user', 'SELECT');
exec rdsadmin.rdsadmin_util.grant_sys_object('V_$NLS_PARAMETERS', 'db_user', 'SELECT');
exec rdsadmin.rdsadmin_util.grant_sys_object('V_$TIMEZONE_NAMES', 'db_user', 'SELECT');
exec rdsadmin.rdsadmin_util.grant_sys_object('V_$TRANSACTION', 'db_user', 'SELECT');
exec rdsadmin.rdsadmin_util.grant_sys_object('V_$CONTAINERS', 'db_user', 'SELECT');
exec rdsadmin.rdsadmin_util.grant_sys_object('DBA_REGISTRY', 'db_user', 'SELECT');
exec rdsadmin.rdsadmin_util.grant_sys_object('OBJ$', 'db_user', 'SELECT');
exec rdsadmin.rdsadmin_util.grant_sys_object('ALL_ENCRYPTED_COLUMNS', 'db_user', 'SELECT');
exec rdsadmin.rdsadmin_util.grant_sys_object('V_$LOGMNR_LOGS', 'db_user', 'SELECT');
exec rdsadmin.rdsadmin_util.grant_sys_object('V_$LOGMNR_CONTENTS','db_user','SELECT');
exec rdsadmin.rdsadmin_util.grant_sys_object('DBMS_LOGMNR', 'db_user', 'EXECUTE');

-- (as of Oracle versions 12.1 and later)
exec rdsadmin.rdsadmin_util.grant_sys_object('REGISTRY$SQLPATCH', 'db_user', 'SELECT');

-- (for Amazon RDS Active Dataguard Standby (ADG))
exec rdsadmin.rdsadmin_util.grant_sys_object('V_$STANDBY_LOG', 'db_user', 'SELECT'); 

-- (for transparent data encryption (TDE))

exec rdsadmin.rdsadmin_util.grant_sys_object('ENC$', 'db_user', 'SELECT'); 
               
-- (for validation with LOB columns)
exec rdsadmin.rdsadmin_util.grant_sys_object('DBMS_CRYPTO', 'db_user', 'EXECUTE');
                    
-- (for binary reader)
exec rdsadmin.rdsadmin_util.grant_sys_object('DBA_DIRECTORIES','db_user','SELECT');
```

For more information on using Amazon RDS Active Dataguard Standby \(ADG\) with AWS DMS see [Using an Amazon RDS Oracle Standby \(read replica\) as a source with Binary Reader for CDC in AWS DMS](#CHAP_Source.Oracle.Amazon-Managed.StandBy)\.

For more information on using Oracle TDE with AWS DMS, see [Supported encryption methods for using Oracle as a source for AWS DMS](#CHAP_Source.Oracle.Encryption)\.

### Configuring an AWS\-managed Oracle source for AWS DMS<a name="CHAP_Source.Oracle.Amazon-Managed.Configuration"></a>

Before using an AWS\-managed Oracle database as a source for AWS DMS, perform the following tasks for the Oracle database:
+ Enable automatic backups\. For more information about enabling automatic backups, see [Enabling automated backups](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithAutomatedBackups.html#USER_WorkingWithAutomatedBackups.Enabling) in the *Amazon RDS User Guide*\.
+ Set up supplemental logging\.
+ Set up archiving\. Archiving the redo logs for your Amazon RDS for Oracle DB instance allows AWS DMS to retrieve the log information using Oracle LogMiner or Binary Reader\. 

**To set up archiving**

1. Run the `rdsadmin.rdsadmin_util.set_configuration` command to set up archiving\.

   For example, to retain the archived redo logs for 24 hours, run the following command\.

   ```
   exec rdsadmin.rdsadmin_util.set_configuration('archivelog retention hours',24);
   commit;
   ```
**Note**  
The commit is required for a change to take effect\.

1. Make sure that your storage has enough space for the archived redo logs during the specified retention period\. For example, if your retention period is 24 hours, calculate the total size of your accumulated archived redo logs over a typical hour of transaction processing and multiply that total by 24\. Compare this calculated 24\-hour total with your available storage space and decide if you have enough storage space to handle a full 24 hours transaction processing\.

**To set up supplemental logging**

1. Run the following command to enable supplemental logging at the database level\.

   ```
   exec rdsadmin.rdsadmin_util.alter_supplemental_logging('ADD');
   ```

1. Run the following command to enable primary key supplemental logging\.

   ```
   exec rdsadmin.rdsadmin_util.alter_supplemental_logging('ADD','PRIMARY KEY');
   ```

1. \(Optional\) Enable key\-level supplemental logging at the table level\.

   Your source database incurs a small bit of overhead when key\-level supplemental logging is enabled\. Therefore, if you are migrating only a subset of your tables, you might want to enable key\-level supplemental logging at the table level\. To enable key\-level supplemental logging at the table level, run the following command\.

   ```
   alter table table_name add supplemental log data (PRIMARY KEY) columns;
   ```

### Configuring a CDC task to use Binary Reader with an RDS for Oracle source for AWS DMS<a name="CHAP_Source.Oracle.Amazon-Managed.CDC"></a>

You can configure AWS DMS to access the source Amazon RDS for Oracle instance redo logs using Binary Reader for CDC\. 

**Note**  
To use Oracle LogMiner, the minimum required user account privileges are sufficient\. For more information, see [User account privileges required on an AWS\-managed Oracle source for AWS DMS](#CHAP_Source.Oracle.Amazon-Managed.Privileges)\.

To use AWS DMS Binary Reader, specify additional settings and extra connection attributes for the Oracle source endpoint, depending on your AWS DMS version\.

Binary Reader support is available in the following versions of Amazon RDS for Oracle:
+ Oracle 11\.2 – Versions 11\.2\.0\.4V11 and later\.
+ Oracle 12\.1 – Versions 12\.1\.0\.2\.V7 and later\.
+ Oracle 12\.2 – All versions\.
+ Oracle 18\.0 – All versions\.
+ Oracle 19\.0 – All versions\.

**To configure CDC using Binary Reader**

1. Log in to your Amazon RDS for Oracle source database as the master user and run the following stored procedures to create the server\-level directories\.

   ```
   exec rdsadmin.rdsadmin_master_util.create_archivelog_dir;
   exec rdsadmin.rdsadmin_master_util.create_onlinelog_dir;
   ```

1. Grant the following privileges to the Oracle user account that is used to access the Oracle source endpoint\.

   ```
   GRANT READ ON DIRECTORY ONLINELOG_DIR TO db_user;
   GRANT READ ON DIRECTORY ARCHIVELOG_DIR TO db_user;
   ```

1. Set the following extra connection attributes on the Amazon RDS Oracle source endpoint:
   + For RDS Oracle versions 11\.2 and 12\.1, set the following\.

     ```
     useLogminerReader=N;useBfile=Y;accessAlternateDirectly=false;useAlternateFolderForOnline=true;
     oraclePathPrefix=/rdsdbdata/db/{$DATABASE_NAME}_A/;usePathPrefix=/rdsdbdata/log/;replacePathPrefix=true;
     ```
   + For RDS Oracle versions 12\.2, 18\.0, and 19\.0, set the following\.

     ```
     useLogminerReader=N;useBfile=Y
     ```

**Note**  
Make sure there's no white space following the semicolon separator \(;\) for multiple attribute settings, for example `oneSetting;thenAnother`\.

For more information configuring a CDC task, see [Configuration for CDC on an Oracle source database](#CHAP_Source.Oracle.CDC.Configuration)\.

### Using an Amazon RDS Oracle Standby \(read replica\) as a source with Binary Reader for CDC in AWS DMS<a name="CHAP_Source.Oracle.Amazon-Managed.StandBy"></a>

Verify the following prerequisites for using Amazon RDS for Oracle Standby as a source when using Binary Reader for CDC in AWS DMS:
+ Use the Oracle master user to set up Binary Reader\.
+ Make sure that AWS DMS currently supports using only Oracle Active Data Guard Standby\.

After you do so, use the following procedure to use RDS for Oracle Standby as a source when using Binary Reader for CDC\.

**To configure an RDS for Oracle Standby as a source when using Binary Reader for CDC**

1. Sign in to RDS for Oracle primary replica as the master user\.

1. Run the following stored procedures as documented in the Amazon RDS User Guide to create the server level directories\.

   ```
   exec rdsadmin.rdsadmin_master_util.create_archivelog_dir;
   exec rdsadmin.rdsadmin_master_util.create_onlinelog_dir;
   ```

1. Identify the directories created in step 2\.

   ```
   SELECT directory_name, directory_path FROM all_directories
   WHERE directory_name LIKE ( 'ARCHIVELOG_DIR_%' )
           OR directory_name LIKE ( 'ONLINELOG_DIR_%' )
   ```

   For example, the preceding code displays a list of directories like the following\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-rds-server-level-directories.png)

1. Grant the `Read` privilege on the preceding directories to the Oracle user account that is used to access the Oracle Standby\.

   ```
   GRANT READ ON DIRECTORY ARCHIVELOG_DIR_A TO db_user;
   GRANT READ ON DIRECTORY ARCHIVELOG_DIR_B TO db_user;
   GRANT READ ON DIRECTORY ONLINELOG_DIR_A TO db_user;
   GRANT READ ON DIRECTORY ONLINELOG_DIR_B TO db_user;
   ```

1. Perform an archive log switch on the primary instance\. Doing this makes sure that the changes to `ALL_DIRECTORIES` are also ported to the Oracle Standby\.

1. Run an `ALL_DIRECTORIES` query on the Oracle Standby to confirm that the changes were applied\.

1. Create a source endpoint for the Oracle Standby by using the AWS DMS Management Console or AWS Command Line Interface \(AWS CLI\)\. While creating the endpoint, specify the following extra connection attributes\.

   ```
   useLogminerReader=N;useBfile=Y;archivedLogDestId=1;additionalArchivedLogDestId=2
   ```

1. After creating the endpoint, use **Test endpoint connection** on the **Create endpoint** page of the console or the AWS CLI `test-connection` command to verify that connectivity is established\.

## Limitations on using Oracle as a source for AWS DMS<a name="CHAP_Source.Oracle.Limitations"></a>

The following limitations apply when using an Oracle database as a source for AWS DMS:
+ AWS DMS doesn't support Oracle Extended data types at this time\.
+ AWS DMS doesn't support long object names \(over 30 bytes\)\.
+ AWS DMS doesn't support function\-based indexes\.
+ If you manage supplemental logging and carry out transformations on any of the columns, make sure that supplemental logging is activated for all fields and columns\. For more information on setting up supplemental logging, see the following topics:
  + For a self\-managed Oracle source database, see [Setting up supplemental logging](#CHAP_Source.Oracle.Self-Managed.Configuration.SupplementalLogging)\.
  + For an AWS\-managed Oracle source database, see [ Configuring an AWS\-managed Oracle source for AWS DMS](#CHAP_Source.Oracle.Amazon-Managed.Configuration)\.
+ AWS DMS doesn't support the multi\-tenant container root database \(CDB$ROOT\)\. It does support a PDB using the Binary Reader\.
+ AWS DMS doesn't support deferred constraints\.
+ AWS DMS doesn't support Oracle SecureFile LOBs\.
+ AWS DMS supports the `rename table table-name to new-table-name` syntax for all supported Oracle versions 11 and later\. This syntax isn't supported for any Oracle version 10 source databases\.
+ AWS DMS doesn't replicate data changes that result from partition or subpartition operations \(`ADD`, `DROP`, `EXCHANGE`, and `TRUNCATE`\)\. Such updates might cause the following errors during replication:
  + For `ADD` operations, updates and deletes on the added data might raise a "0 rows affected" warning\.
  + For `DROP` and `TRUNCATE` operations, new inserts might raise "duplicates" errors\.
  + `EXCHANGE` operations might raise both a "0 rows affected" warning and "duplicates" errors\.

  To replicate changes that result from partition or subpartition operations, reload the tables in question\. After adding a new empty partition, operations on the newly added partition are replicated to the target as normal\.
+ AWS DMS doesn't support data changes on the target that result from running the `CREATE TABLE AS` statement on the source\. However, the new table is created on the target\.
+ AWS DMS doesn't capture changes made by the Oracle `DBMS_REDEFINITION` package, for example the table metadata and the `OBJECT_ID` field\.
+ AWS DMS maps empty BLOB and CLOB columns to `NULL` on the target\.
+ When capturing changes with Oracle 11 LogMiner, an update on a CLOB column with a string length greater than 1982 is lost, and the target is not updated\.
+ During change data capture \(CDC\), AWS DMS doesn't support batch updates to numeric columns defined as a primary key\.
+ AWS DMS doesn't support certain `UPDATE` commands\. The following example is an unsupported `UPDATE` command\.

  ```
  UPDATE TEST_TABLE SET KEY=KEY+1;
  ```

  Here, `TEST_TABLE` is the table name and `KEY` is a numeric column defined as a primary key\.
+ AWS DMS truncates any data in `LONG` or `LONG RAW` columns that is longer than 64 KB to 64 KB\.
+ AWS DMS doesn't replicate tables whose names contain apostrophes\.
+ AWS DMS doesn't support CDC from dynamic views\.
+ AWS DMS doesn't support CDC for index\-organized tables with an overflow segment\.
+ When you use Oracle LogMiner to access the redo logs, AWS DMS has the following limitations:
  + For Oracle 12 only, AWS DMS doesn't replicate any changes to LOB columns\.
  + For all Oracle versions, AWS DMS doesn't replicate the result of `UPDATE` operations on `XMLTYPE` and LOB columns\.
  + AWS DMS doesn't replicate results of the DDL statement `ALTER TABLE ADD column data_type DEFAULT default_value`\. Instead of replicating `default_value` to the target, it sets the new column to `NULL`\. Such a result can also happen even if the DDL statement that added the new column was run in a prior task\. 

    If the new column is nullable, Oracle updates all the table rows before logging the DDL itself\. As a result, AWS DMS captures the changes but doesn't update the target\. With the new column set to `NULL`, if the target table has no primary key or unique index subsequent updates raise a "zero rows affected" message\.
  + AWS DMS doesn't support XA transactions in replication while using Oracle LogMiner\. 
+ Oracle LogMiner doesn't support connections to a pluggable database \(PDB\)\. To connect to a PDB, access the redo logs using Binary Reader\.
+ When you use Binary Reader, AWS DMS has these limitations:
  + It doesn't support table clusters\.
  + It supports only table\-level `SHRINK SPACE` operations\. This level includes the full table, partitions, and subpartitions\.
  + It doesn't support changes to index\-organized tables with key compression\.
  + It doesn't support implementing online redo logs on raw devices\.
+ AWS DMS doesn't support connections to an Amazon RDS Oracle source using an Oracle Automatic Storage Management \(ASM\) proxy\.
+ AWS DMS doesn't support virtual columns\. 
+ AWS DMS doesn't support the `ROWID` data type or materialized views based on a `ROWID` column\.
+ AWS DMS doesn't load or capture global temporary tables\.
+ For S3 targets using replication, enable supplemental logging on every column so source row updates can capture every column value\. An example follows: `alter table yourtablename add supplemental log data (all) columns;`\.
+ An update for a row with a composite unique key that contains `null` can't be replicated at the target\.
+ AWS DMS doesn't support use of multiple Oracle TDE encryption keys on the same source endpoint\. Each endpoint can have only one attribute for TDE encryption Key Name "`securityDbEncryptionName`", and one TDE password for this key\.

## SSL support for an Oracle endpoint<a name="CHAP_Security.SSL.Oracle"></a>

AWS DMS Oracle endpoints support SSL V3 for the `none` and `verify-ca` SSL modes\. To use SSL with an Oracle endpoint, upload the Oracle wallet for the endpoint instead of \.pem certificate files\. 

**Topics**
+ [Using an existing certificate for Oracle SSL](#CHAP_Security.SSL.Oracle.Existing)
+ [Using a self\-signed certificate for Oracle SSL](#CHAP_Security.SSL.Oracle.SelfSigned)

### Using an existing certificate for Oracle SSL<a name="CHAP_Security.SSL.Oracle.Existing"></a>

To use an existing Oracle client installation to create the Oracle wallet file from the CA certificate file, do the following steps\.

**To use an existing oracle client installation for Oracle SSL with AWS DMS**

1. Set the `ORACLE_HOME` system variable to the location of your `dbhome_1` directory by running the following command\.

   ```
   prompt>export ORACLE_HOME=/home/user/app/user/product/12.1.0/dbhome_1                        
   ```

1. Append `$ORACLE_HOME/lib` to the `LD_LIBRARY_PATH` system variable\.

   ```
   prompt>export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$ORACLE_HOME/lib                        
   ```

1. Create a directory for the Oracle wallet at `$ORACLE_HOME/ssl_wallet`\.

   ```
   prompt>mkdir $ORACLE_HOME/ssl_wallet
   ```

1. Put the CA certificate `.pem` file in the `ssl_wallet` directory\. If you use Amazon RDS, you can download the `rds-ca-2015-root.pem` root CA certificate file hosted by Amazon RDS\. For more information about downloading this file, see [Using SSL/TLS to encrypt a connection to a DB instance](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/UsingWithRDS.SSL.html) in the *Amazon RDS User Guide*\.

1. Run the following commands to create the Oracle wallet\.

   ```
   prompt>orapki wallet create -wallet $ORACLE_HOME/ssl_wallet -auto_login_only
   
   prompt>orapki wallet add -wallet $ORACLE_HOME/ssl_wallet -trusted_cert -cert 
      $ORACLE_HOME/ssl_wallet/ca-cert.pem -auto_login_only
   ```

When you have completed the steps previous, you can import the wallet file with the `ImportCertificate` API call by specifying the certificate\-wallet parameter\. You can then use the imported wallet certificate when you select `verify-ca` as the SSL mode when creating or modifying your Oracle endpoint\.

**Note**  
 Oracle wallets are binary files\. AWS DMS accepts these files as\-is\. 

### Using a self\-signed certificate for Oracle SSL<a name="CHAP_Security.SSL.Oracle.SelfSigned"></a>

To use a self\-signed certificate for Oracle SSL, do the steps following, assuming an Oracle wallet password of `oracle123`\.

**To use a self\-signed certificate for Oracle SSL with AWS DMS**

1. Create a directory you will use to work with the self\-signed certificate\.

   ```
   mkdir -p /u01/app/oracle/self_signed_cert
   ```

1. Change into the directory you created in the previous step\.

   ```
   cd /u01/app/oracle/self_signed_cert
   ```

1. Create a root key\.

   ```
   openssl genrsa -out self-rootCA.key 2048
   ```

1. Self\-sign a root certificate using the root key you created in the previous step\.

   ```
   openssl req -x509 -new -nodes -key self-rootCA.key 
           -sha256 -days 3650 -out self-rootCA.pem
   ```

   Use input parameters like the following\.
   + `Country Name (2 letter code) [XX]`, for example: `AU`
   + `State or Province Name (full name) []`, for example: `NSW`
   + `Locality Name (e.g., city) [Default City]`, for example: `Sydney`
   + `Organization Name (e.g., company) [Default Company Ltd]`, for example: `AmazonWebService`
   + `Organizational Unit Name (e.g., section) []`, for example: `DBeng`
   + `Common Name (e.g., your name or your server's hostname) []`, for example: `aws`
   + `Email Address []`, for example: abcd\.efgh@amazonwebservice\.com

1. Create an Oracle wallet directory for the Oracle database\.

   ```
   mkdir -p /u01/app/oracle/wallet
   ```

1. Create a new Oracle wallet\.

   ```
   orapki wallet create -wallet "/u01/app/oracle/wallet" -pwd oracle123 -auto_login_local
   ```

1. Add the root certificate to the Oracle wallet\.

   ```
   orapki wallet add -wallet "/u01/app/oracle/wallet" -pwd oracle123 -trusted_cert 
   -cert /u01/app/oracle/self_signed_cert/self-rootCA.pem
   ```

1. List the contents of the Oracle wallet\. The list should include the root certificate\. 

   ```
   orapki wallet display -wallet /u01/app/oracle/wallet -pwd oracle123
   ```

   For example, this might display similar to the following\.

   ```
   Requested Certificates:
   User Certificates:
   Trusted Certificates:
   Subject:        CN=aws,OU=DBeng,O= AmazonWebService,L=Sydney,ST=NSW,C=AU
   ```

1. Generate the Certificate Signing Request \(CSR\) using the ORAPKI utility\.

   ```
   orapki wallet add -wallet "/u01/app/oracle/wallet" -pwd oracle123 
   -dn "CN=aws" -keysize 2048 -sign_alg sha256
   ```

1. Run the following command\.

   ```
   openssl pkcs12 -in /u01/app/oracle/wallet/ewallet.p12 -nodes -out /u01/app/oracle/wallet/nonoracle_wallet.pem
   ```

   This has output like the following\.

   ```
   Enter Import Password:
   MAC verified OK
   Warning unsupported bag type: secretBag
   ```

1. Put 'dms' as the common name\.

   ```
   openssl req -new -key /u01/app/oracle/wallet/nonoracle_wallet.pem -out certdms.csr
   ```

   Use input parameters like the following\.
   + `Country Name (2 letter code) [XX]`, for example: `AU`
   + `State or Province Name (full name) []`, for example: `NSW`
   + `Locality Name (e.g., city) [Default City]`, for example: `Sydney`
   + `Organization Name (e.g., company) [Default Company Ltd]`, for example: `AmazonWebService`
   + `Organizational Unit Name (e.g., section) []`, for example: `aws`
   + `Common Name (e.g., your name or your server's hostname) []`, for example: `aws`
   + `Email Address []`, for example: abcd\.efgh@amazonwebservice\.com

   Make sure this is not same as step 4\. You can do this, for example, by changing Organizational Unit Name to a different name as shown\.

   Enter the additional attributes following to be sent with your certificate request\.
   + `A challenge password []`, for example: `oracle123`
   + `An optional company name []`, for example: `aws`

1. Get the certificate signature\.

   ```
   openssl req -noout -text -in certdms.csr | grep -i signature
   ```

   The signature key for this post is `sha256WithRSAEncryption` \.

1. Run the command following to generate the certificate \(`.crt`\) file\.

   ```
   openssl x509 -req -in certdms.csr -CA self-rootCA.pem -CAkey self-rootCA.key 
   -CAcreateserial -out certdms.crt -days 365 -sha256
   ```

   This displays output like the following\.

   ```
   Signature ok
   subject=/C=AU/ST=NSW/L=Sydney/O=awsweb/OU=DBeng/CN=aws
   Getting CA Private Key
   ```

1. Add the certificate to the wallet\.

   ```
   orapki wallet add -wallet /u01/app/oracle/wallet -pwd oracle123 -user_cert -cert certdms.crt
   ```

1. View the wallet\. It should have two entries\. See the code following\.

   ```
   orapki wallet display -wallet /u01/app/oracle/wallet -pwd oracle123
   ```

1. Configure the `sqlnet.ora` file \(`$ORACLE_HOME/network/admin/sqlnet.ora`\)\.

   ```
   WALLET_LOCATION =
      (SOURCE =
        (METHOD = FILE)
        (METHOD_DATA =
          (DIRECTORY = /u01/app/oracle/wallet/)
        )
      ) 
   
   SQLNET.AUTHENTICATION_SERVICES = (NONE)
   SSL_VERSION = 1.0
   SSL_CLIENT_AUTHENTICATION = FALSE
   SSL_CIPHER_SUITES = (SSL_RSA_WITH_AES_256_CBC_SHA)
   ```

1. Stop the Oracle listener\.

   ```
   lsnrctl stop
   ```

1. Add entries for SSL in the `listener.ora` file \(`$ORACLE_HOME/network/admin/listener.ora`\)\.

   ```
   SSL_CLIENT_AUTHENTICATION = FALSE
   WALLET_LOCATION =
     (SOURCE =
       (METHOD = FILE)
       (METHOD_DATA =
         (DIRECTORY = /u01/app/oracle/wallet/)
       )
     )
   
   SID_LIST_LISTENER =
    (SID_LIST =
     (SID_DESC =
      (GLOBAL_DBNAME = SID)
      (ORACLE_HOME = ORACLE_HOME)
      (SID_NAME = SID)
     )
    )
   
   LISTENER =
     (DESCRIPTION_LIST =
       (DESCRIPTION =
         (ADDRESS = (PROTOCOL = TCP)(HOST = localhost.localdomain)(PORT = 1521))
         (ADDRESS = (PROTOCOL = TCPS)(HOST = localhost.localdomain)(PORT = 1522))
         (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
       )
     )
   ```

1. Configure the `tnsnames.ora` file \(`$ORACLE_HOME/network/admin/tnsnames.ora`\)\.

   ```
   <SID>=
   (DESCRIPTION=
           (ADDRESS_LIST = 
                   (ADDRESS=(PROTOCOL = TCP)(HOST = localhost.localdomain)(PORT = 1521))
           )
           (CONNECT_DATA =
                   (SERVER = DEDICATED)
                   (SERVICE_NAME = <SID>)
           )
   )
   
   <SID>_ssl=
   (DESCRIPTION=
           (ADDRESS_LIST = 
                   (ADDRESS=(PROTOCOL = TCPS)(HOST = localhost.localdomain)(PORT = 1522))
           )
           (CONNECT_DATA =
                   (SERVER = DEDICATED)
                   (SERVICE_NAME = <SID>)
           )
   )
   ```

1. Restart the Oracle listener\.

   ```
   lsnrctl start
   ```

1. Show the Oracle listener status\.

   ```
   lsnrctl status
   ```

1. Test the SSL connection to the database from localhost using sqlplus and the SSL tnsnames entry\.

   ```
   sqlplus -L ORACLE_USER@SID_ssl
   ```

1. Verify that you successfully connected using SSL\.

   ```
   SELECT SYS_CONTEXT('USERENV', 'network_protocol') FROM DUAL;
   
   SYS_CONTEXT('USERENV','NETWORK_PROTOCOL')
   --------------------------------------------------------------------------------
   tcps
   ```

1. Change directory to the directory with the self\-signed certificate\.

   ```
   cd /u01/app/oracle/self_signed_cert
   ```

1. Create a new client Oracle wallet for AWS DMS to use\.

   ```
   orapki wallet create -wallet ./ -auto_login_only
   ```

1. Add the self\-signed root certificate to the Oracle wallet\.

   ```
   orapki wallet add -wallet ./ -trusted_cert -cert rootCA.pem -auto_login_only
   ```

1. List the contents of the Oracle wallet for AWS DMS to use\. The list should include the self\-signed root certificate\.

   ```
   orapki wallet display -wallet ./
   ```

   This has output like the following\.

   ```
   Trusted Certificates:
   Subject:        CN=aws,OU=DBeng,O=AmazonWebService,L=Sydney,ST=NSW,C=AU
   ```

1. Upload the Oracle wallet that you just created to AWS DMS\.

## Supported encryption methods for using Oracle as a source for AWS DMS<a name="CHAP_Source.Oracle.Encryption"></a>

In the following table, you can find the transparent data encryption \(TDE\) methods that AWS DMS supports when working with an Oracle source database\. 


| Redo logs access method | TDE tablespace | TDE column | 
| --- | --- | --- | 
| Oracle LogMiner | Yes | Yes | 
| Binary Reader | Yes | Yes | 

AWS DMS supports Oracle TDE when using Binary Reader, on both the column level and the tablespace level\. To use TDE encryption with AWS DMS, first identify the Oracle wallet location where the TDE encryption key and TDE password are stored\. Then identify the correct TDE encryption key and password for your Oracle source endpoint\.

**To identify and specify encryption key and password for TDE encryption**

1. Run the following query to find the Oracle encryption wallet on the Oracle database host\.

   ```
   SQL> SELECT WRL_PARAMETER FROM V$ENCRYPTION_WALLET;
   
   WRL_PARAMETER
   --------------------------------------------------------------------------------
   /u01/oracle/product/12.2.0/dbhome_1/data/wallet/
   ```

   Here, `/u01/oracle/product/12.2.0/dbhome_1/data/wallet/` is the wallet location\.

1. Get the master key ID using one of the following encryption options, depending on which one returns this value\.

   1. For table or column\-level encryption, run the following queries\.

      ```
      SQL> SELECT OBJECT_ID FROM ALL_OBJECTS 
      WHERE OWNER='DMS_USER' AND OBJECT_NAME='TEST_TDE_COLUMN' AND OBJECT_TYPE='TABLE';
      
      OBJECT_ID
      ---------------
      81046
      SQL> SELECT MKEYID FROM SYS.ENC$ WHERE OBJ#=81046;
      
      MKEYID
      ------------
      AWGDC9glSk8Xv+3bVveiVSgAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
      ```

      Here, `AWGDC9glSk8Xv+3bVveiVSg` is the master key ID \(`MKEYID`\)\. If you get a value for `MKEYID`, you can continue with Step 3\. Otherwise, continue with Step 2\.2\.
**Note**  
The trailing string `'A'` characters \(`AAA...`\) is not part of the value\.

   1. For tablespace\-level encryption, run the following queries\.

      ```
      SQL> SELECT TABLESPACE_NAME, ENCRYPTED FROM dba_tablespaces;
      TABLESPACE_NAME                ENC
      ------------------------------ ---
      SYSTEM                         NO
      SYSAUX                         NO
      UNDOTBS1                       NO
      TEMP                           NO
      USERS                          NO
      TEST_ENCRYT                    YES
      SQL> SELECT name,utl_raw.cast_to_varchar2( utl_encode.base64_encode('01'||substr(mkeyid,1,4))) || utl_raw.cast_to_varchar2( utl_encode.base64_encode(substr(mkeyid,5,length(mkeyid)))) masterkeyid_base64
      FROM (SELECT t.name, RAWTOHEX(x.mkid) mkeyid FROM v$tablespace t, x$kcbtek x WHERE t.ts#=x.ts#)
      WHERE name = 'TEST_ENCRYT';
      
      NAME                           MASTERKEYID_BASE64
      ------------------------------ ----------------------------------
      TEST_ENCRYT                    AWGDC9glSk8Xv+3bVveiVSg=
      ```

      Here, `AWGDC9glSk8Xv+3bVveiVSg` is the master key ID \(`TEST_ENCRYT`\)\. If both steps 2\.1 and 2\.2 return a value, they are always identical\.

      The trailing `'='` character is not part of the value\.

1. From the command line, list the encryption wallet entries on the source Oracle database host\.

   ```
   $ mkstore -wrl /u01/oracle/product/12.2.0/dbhome_1/data/wallet/ -list
   Oracle Secret Store entries:
   ORACLE.SECURITY.DB.ENCRYPTION.AWGDC9glSk8Xv+3bVveiVSgAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
   ORACLE.SECURITY.DB.ENCRYPTION.AY1mRA8OXU9Qvzo3idU4OH4AAAAAAAAAAAAAAAAAAAAAAAAAAAAA
   ORACLE.SECURITY.DB.ENCRYPTION.MASTERKEY
   ORACLE.SECURITY.ID.ENCRYPTION.
   ORACLE.SECURITY.KB.ENCRYPTION.
   ORACLE.SECURITY.KM.ENCRYPTION.AY1mRA8OXU9Qvzo3idU4OH4AAAAAAAAAAAAAAAAAAAAAAAAAAAAA
   ```

   Find the entry containing the master key ID that you found in step 2 \(`AWGDC9glSk8Xv+3bVveiVSg`\)\. This entry is the TDE encryption key name\.

1. View the details of the entry that you found in the previous step\.

   ```
   $ mkstore -wrl /u01/oracle/product/12.2.0/dbhome_1/data/wallet/ -viewEntry ORACLE.SECURITY.DB.ENCRYPTION.AWGDC9glSk8Xv+3bVveiVSgAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
   Oracle Secret Store Tool : Version 12.2.0.1.0
   Copyright (c) 2004, 2016, Oracle and/or its affiliates. All rights reserved.
   Enter wallet password:
   ORACLE.SECURITY.DB.ENCRYPTION.AWGDC9glSk8Xv+3bVveiVSgAAAAAAAAAAAAAAAAAAAAAAAAAAAAA = AEMAASAASGYs0phWHfNt9J5mEMkkegGFiD4LLfQszDojgDzbfoYDEACv0x3pJC+UGD/PdtE2jLIcBQcAeHgJChQGLA==
   ```

   Enter the wallet password to see the result\.

   Here, the value to the right of `'='` is the TDE password\.

1. Specify the TDE encryption key name for the Oracle source endpoint by setting the `securityDbEncryptionName` extra connection attribute\.

   ```
   securityDbEncryptionName=ORACLE.SECURITY.DB.ENCRYPTION.AWGDC9glSk8Xv+3bVveiVSgAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
   ```

1. Provide the associated TDE password for this key on the console as part of the Oracle source's **Password** value\. Use the following order to format the comma\-separated password values, ended by the TDE password value\.

   ```
   Oracle_db_password,ASM_Password,AEMAASAASGYs0phWHfNt9J5mEMkkegGFiD4LLfQszDojgDzbfoYDEACv0x3pJC+UGD/PdtE2jLIcBQcAeHgJChQGLA==
   ```

   Specify the password values in this order regardless of your Oracle database configuration\. For example, if you're using TDE but your Oracle database isn't using ASM, specify password values in the following comma\-separated order\.

   ```
   Oracle_db_password,,AEMAASAASGYs0phWHfNt9J5mEMkkegGFiD4LLfQszDojgDzbfoYDEACv0x3pJC+UGD/PdtE2jLIcBQcAeHgJChQGLA==
   ```

If the TDE credentials you specify are incorrect, the AWS DMS migration task doesn't fail\. However, the task also doesn't read or apply ongoing replication changes to the target database\. After starting the task, monitor **Table statistics** on the console migration task page to make sure changes are replicated\.

If a DBA changes the TDE credential values for the Oracle database while the task is running, the task fails\. The error message contains the new TDE encryption key name\. To specify new values and restart the task, use the preceding procedure\.

**Important**  
You can’t manipulate a TDE wallet created in an Oracle Automatic Storage Management \(ASM\) location because OS level commands like `cp`, `mv`, `orapki`, and `mkstore` corrupt the wallet files stored in an ASM location\. This restriction is specific to TDE wallet files stored in an ASM location only, but not for TDE wallet files stored in a local OS directory\.  
To manipulate a TDE wallet stored in ASM with OS level commands, create a local keystore and merge the ASM keystore into the local keystore as follows:   
Create a local keystore\.  

   ```
   ADMINISTER KEY MANAGEMENT create keystore file system wallet location identified by wallet password;                          
   ```
Merge the ASM keystore into the local keystore\.  

   ```
   ADMINISTER KEY MANAGEMENT merge keystore ASM wallet location identified by wallet password into existing keystore file system wallet location identified by wallet password with backup;                     
   ```
Then, to list the encryption wallet entries and TDE password, run steps 3 and 4 against the local keystore\.

## Supported compression methods for using Oracle as a source for AWS DMS<a name="CHAP_Source.Oracle.Compression"></a>

In the following table, you can find which compression methods AWS DMS supports when working with an Oracle source database\. As the table shows, compression support depends both on your Oracle database version and whether DMS is configured to use Oracle LogMiner to access the redo logs\.


| Version | Basic | OLTP |  HCC \(from Oracle 11g R2 or newer\)  | Others | 
| --- | --- | --- | --- | --- | 
| Oracle 10 | No | N/A | N/A | No | 
| Oracle 11 or newer – Oracle LogMiner | Yes | Yes | Yes  | Yes – Any compression method supported by Oracle LogMiner\. | 
| Oracle 11 or newer – Binary Reader | Yes | Yes | Yes – For more information, see the following note \. | Yes | 

**Note**  
When the Oracle source endpoint is configured to use Binary Reader, the Query Low level of the HCC compression method is supported for full\-load tasks only\.

## Replicating nested tables using Oracle as a source for AWS DMS<a name="CHAP_Source.Oracle.NestedTables"></a>

AWS DMS supports the replication of Oracle tables containing columns that are nested tables or defined types\. To enable this functionality, add the following extra connection attribute setting to the Oracle source endpoint\.

```
allowSelectNestedTables=true;
```

AWS DMS creates the target tables from Oracle nested tables as regular parent and child tables on the target without a unique constraint\. To access the correct data on the target, join the parent and child tables\. To do this, first manually create a nonunique index on the `NESTED_TABLE_ID` column in the target child table\. You can then use the `NESTED_TABLE_ID` column in the join `ON` clause together with the parent column that corresponds to the child table name\. In addition, creating such an index improves performance when the target child table data is updated or deleted by AWS DMS\. For an example, see [Example join for parent and child tables on the target](#CHAP_Source.Oracle.NestedTables.JoinExample)\.

We recommend that you configure the task to stop after a full load completes\. Then, create these nonunique indexes for all the replicated child tables on the target and resume the task\.

If a captured nested table is added to an existing parent table \(captured or not captured\), AWS DMS handles it correctly\. However, the nonunique index for the corresponding target table isn't created\. In this case, if the target child table becomes extremely large, performance might be affected\. In such a case, we recommend that you stop the task, create the index, then resume the task\.

After the nested tables are replicated to the target, have the DBA run a join on the parent and corresponding child tables to flatten the data\.

### Prerequisites for replicating Oracle nested tables as a source<a name="CHAP_Source.Oracle.NestedTables.Prerequisites"></a>

Ensure that you replicate parent tables for all the replicated nested tables\. Include both the parent tables \(the tables containing the nested table column\) and the child \(that is, nested\) tables in the AWS DMS table mappings\.

### Supported Oracle nested table types as a source<a name="CHAP_Source.Oracle.NestedTables.Types"></a>

AWS DMS supports the following Oracle nested table types as a source:
+ Data type
+ User defined object

### Limitations of AWS DMS support for Oracle nested tables as a source<a name="CHAP_Source.Oracle.NestedTables.Limitations"></a>

AWS DMS has the following limitations in its support of Oracle nested tables as a source:
+ AWS DMS supports only one level of table nesting\.
+ AWS DMS table mapping doesn't check that both the parent and child table or tables are selected for replication\. That is, it's possible to select a parent table without a child or a child table without a parent\.

### How AWS DMS replicates Oracle nested tables as a source<a name="CHAP_Source.Oracle.NestedTables.HowReplicated"></a>

AWS DMS replicates parent and nested tables to the target as follows:
+ AWS DMS creates the parent table identical to the source\. It then defines the nested column in the parent as `RAW(16)` and includes a reference to the parent's nested tables in its `NESTED_TABLE_ID` column\.
+ AWS DMS creates the child table identical to the nested source, but with an additional column named `NESTED_TABLE_ID`\. This column has the same type and value as the corresponding parent nested column and has the same meaning\.

### Example join for parent and child tables on the target<a name="CHAP_Source.Oracle.NestedTables.JoinExample"></a>

To flatten the parent table, run a join between the parent and child tables, as shown in the following example:

1. Create the `Type` table\.

   ```
   CREATE OR REPLACE TYPE NESTED_TEST_T AS TABLE OF VARCHAR(50);
   ```

1. Create the parent table with a column of type `NESTED_TEST_T` as defined preceding\.

   ```
   CREATE TABLE NESTED_PARENT_TEST (ID NUMBER(10,0) PRIMARY KEY, NAME NESTED_TEST_T) NESTED TABLE NAME STORE AS NAME_KEY;
   ```

1. Flatten the table `NESTED_PARENT_TEST` using a join with the `NAME_KEY` child table where `CHILD.NESTED_TABLE_ID` matches `PARENT.NAME`\.

   ```
   SELECT … FROM NESTED_PARENT_TEST PARENT, NAME_KEY CHILD WHERE CHILD.NESTED_
   TABLE_ID = PARENT.NAME;
   ```

## Extra connection attributes when using Oracle as a source for AWS DMS<a name="CHAP_Source.Oracle.ConnectionAttrib"></a>

You can use extra connection attributes to configure your Oracle source\. You specify these settings when you create the source endpoint\. If you have multiple connection attribute settings, separate them from each other by semicolons with no additional white space \(for example, `oneSetting=oneValue;thenAnother=anotherValue`\)\.

The following table shows the extra connection attributes that you can use to configure an Oracle database as a source for AWS DMS\.


| Name | Description | 
| --- | --- | 
|  `addSupplementalLogging `  |  Set this attribute to set up table\-level supplemental logging for the Oracle database\. This attribute enables PRIMARY KEY supplemental logging on all tables selected for a migration task\. Default value: N  Valid values: Y/N  Example: `addSupplementalLogging=Y;`   If you use this option, you still need to enable database\-level supplemental logging as discussed previously\.    | 
|  `additionalArchivedLogDestId`  |  Set this attribute with `archivedLogDestId` in a primary\-Standby setup\. This attribute is useful in a switchover when Oracle Data Guard database is used as a source\. In this case, AWS DMS needs to know which destination to get archive redo logs from to read changes\. This is because the previous primary is now a Standby instance after switchover\. Although AWS DMS supports the use of the Oracle `RESETLOGS` option to open the database, never use `RESETLOGS` unless necessary\. For additional information about `RESETLOGS`, see [RMAN Data Repair Concepts](https://docs.oracle.com/en/database/oracle/oracle-database/19/bradv/rman-data-repair-concepts.html#GUID-1805CCF7-4AF2-482D-B65A-998192F89C2B) in the *Oracle® Database Backup and Recovery User's Guide*\.  | 
|  `allowSelectNestedTables`  |  Set this attribute to true to enable replication of Oracle tables containing columns that are nested tables or defined types\. For more information, see [Replicating nested tables using Oracle as a source for AWS DMS](#CHAP_Source.Oracle.NestedTables)\. Default value: false  Valid values: true/false Example: `allowSelectNestedTables=true;`  | 
|  `useLogminerReader`  |  Set this attribute to Y to capture change data using the LogMiner utility \(the default\)\. Set this option to N if you want AWS DMS to access the redo logs as a binary file\. When you set this option to N, also add the setting useBfile=Y\. For more information on this setting and using Oracle Automatic Storage Management \(ASM\), see [Using Oracle LogMiner or AWS DMS Binary Reader for CDC](#CHAP_Source.Oracle.CDC)\. Default value: Y  Valid values: Y/N  Example: `useLogminerReader=N;useBfile=Y;`   | 
| useBfile |  Set this attribute to Y in order to capture change data using the Binary Reader utility\. Set `useLogminerReader` to N to set this attribute to Y\. To use the Binary Reader with an Amazon RDS for Oracle as the source, you set additional attributes\. For more information on this setting and using Oracle Automatic Storage Management \(ASM\), see [Using Oracle LogMiner or AWS DMS Binary Reader for CDC](#CHAP_Source.Oracle.CDC)\. Default value: N  Valid values: Y/N Example: `useLogminerReader=N;useBfile=Y;`   | 
| parallelASMReadThreads |  Set this attribute to change the number of threads that DMS configures to perform change data capture \(CDC\) using Oracle Automatic Storage Management \(ASM\)\. You can specify an integer value between 2 \(the default\) and 8 \(the maximum\)\. Use this attribute together with the `readAheadBlocks` attribute\. For more information, see [ Configuring a CDC task to use Binary Reader with an RDS for Oracle source for AWS DMS](#CHAP_Source.Oracle.Amazon-Managed.CDC)\. Default value: 2  Valid values: An integer from 2 to 8 Example: `parallelASMReadThreads=6;readAheadBlocks=150000;`   | 
| readAheadBlocks |  Set this attribute to change the number of read\-ahead blocks that DMS configures to perform CDC using Oracle Automatic Storage Management \(ASM\)\. You can specify an integer value between 1000 \(the default\) and 200,000 \(the maximum\)\. Use this attribute together with the `parallelASMReadThreads` attribute\. For more information, see [ Configuring a CDC task to use Binary Reader with an RDS for Oracle source for AWS DMS](#CHAP_Source.Oracle.Amazon-Managed.CDC)\. Default value: 1000  Valid values: An integer from 1000 to 200,000 Example: `parallelASMReadThreads=6;readAheadBlocks=150000;`   | 
| accessAlternateDirectly |  Set this attribute to false in order to use the Binary Reader to capture change data for an Amazon RDS for Oracle as the source\. This tells the DMS instance to not access redo logs through any specified path prefix replacement using direct file access\. For more information, see [ Configuring a CDC task to use Binary Reader with an RDS for Oracle source for AWS DMS](#CHAP_Source.Oracle.Amazon-Managed.CDC)\. Default value: true  Valid values: true/false Example: `useLogminerReader=N;useBfile=Y;accessAlternateDirectly=false;`  | 
| useAlternateFolderForOnline | Set this attribute to true in order to use the Binary Reader to capture change data for an Amazon RDS for Oracle as the source\. This tells the DMS instance to use any specified prefix replacement to access all online redo logs\. For more information, see [ Configuring a CDC task to use Binary Reader with an RDS for Oracle source for AWS DMS](#CHAP_Source.Oracle.Amazon-Managed.CDC)\.Default value: false Valid values: true/falseExample: `useLogminerReader=N;useBfile=Y;accessAlternateDirectly=false; useAlternateFolderForOnline=true;` | 
| oraclePathPrefix | Set this string attribute to the required value in order to use the Binary Reader to capture change data for an Amazon RDS for Oracle as the source\. This value specifies the default Oracle root used to access the redo logs\. For more information, see [ Configuring a CDC task to use Binary Reader with an RDS for Oracle source for AWS DMS](#CHAP_Source.Oracle.Amazon-Managed.CDC)\.Default value: none Valid value: /rdsdbdata/db/ORCL\_A/Example: `useLogminerReader=N;useBfile=Y;accessAlternateDirectly=false; useAlternateFolderForOnline=true;oraclePathPrefix=/rdsdbdata/db/ORCL_A/;` | 
| usePathPrefix | Set this string attribute to the required value in order to use the Binary Reader to capture change data for an Amazon RDS for Oracle as the source\. This value specifies the path prefix used to replace the default Oracle root to access the redo logs\. For more information, see [ Configuring a CDC task to use Binary Reader with an RDS for Oracle source for AWS DMS](#CHAP_Source.Oracle.Amazon-Managed.CDC)\.Default value: none Valid value: /rdsdbdata/log/Example: `useLogminerReader=N;useBfile=Y;accessAlternateDirectly=false; useAlternateFolderForOnline=true;oraclePathPrefix=/rdsdbdata/db/ORCL_A/; usePathPrefix=/rdsdbdata/log/;` | 
| replacePathPrefix | Set this attribute to true in order to use the Binary Reader to capture change data for an Amazon RDS for Oracle as the source\. This setting tells DMS instance to replace the default Oracle root with the specified usePathPrefix setting to access the redo logs\. For more information, see [ Configuring a CDC task to use Binary Reader with an RDS for Oracle source for AWS DMS](#CHAP_Source.Oracle.Amazon-Managed.CDC)\.Default value: false Valid values: true/falseExample: `useLogminerReader=N;useBfile=Y;accessAlternateDirectly=false; useAlternateFolderForOnline=true;oraclePathPrefix=/rdsdbdata/db/ORCL_A/; usePathPrefix=/rdsdbdata/log/;replacePathPrefix=true;` | 
|  `retryInterval`  |  Specifies the number of seconds that the system waits before resending a query\.  Default value: 5  Valid values: Numbers starting from 1  Example: `retryInterval=6;`  | 
|  `archivedLogDestId`  |  Specifies the ID of the destination for the archived redo logs\. This value should be the same as a number in the dest\_id column of the v$archived\_log view\. If you work with an additional redo log destination, we recommend that you use the `additionalArchivedLogDestId` attribute to specify the additional destination ID\. Doing this improves performance by ensuring that the correct logs are accessed from the outset\.  Default value: 1 Valid values: Number  Example: `archivedLogDestId=1;`  | 
|  `archivedLogsOnly`  |  When this field is set to Y, AWS DMS only accesses the archived redo logs\. If the archived redo logs are stored on Oracle ASM only, the AWS DMS user account needs to be granted ASM privileges\.  Default value: N  Valid values: Y/N  Example: `archivedLogsOnly=Y;`  | 
|  `numberDataTypeScale`  |  Specifies the number scale\. You can select a scale up to 38, or you can select \-1 for FLOAT, or \-2 for VARCHAR\. By default, the NUMBER data type is converted to precision 38, scale 10\. Default value: 10  Valid values: \-2 to 38 \(\-2 for VARCHAR, \-1 for FLOAT\) Example: `numberDataTypeScale=12`  Select a precision\-scale combination, \-1 \(FLOAT\) or \-2 \(VARCHAR\)\. DMS supports any precision\-scale combination supported by Oracle\. If precision is 39 or above, select \-2 \(VARCHAR\)\. The numberDataTypeScale setting for the Oracle database is used for the NUMBER data type only \(without the explicit precision and scale definition\)\.    | 
|  `failTasksOnLobTruncation`  |  When set to `true`, this attribute causes a task to fail if the actual size of an LOB column is greater than the specified `LobMaxSize`\. If a task is set to limited LOB mode and this option is set to `true`, the task fails instead of truncating the LOB data\. Default value: false  Valid values: Boolean  Example: `failTasksOnLobTruncation=true;`  | 
|  `readTableSpaceName`  |  When set to `true`, this attribute supports tablespace replication\. Default value: false  Valid values: Boolean  Example: `readTableSpaceName=true;`  | 
|  `enableHomogenousTablespace`  |  Set this attribute to enable homogenous tablespace replication and create existing tables or indexes under the same tablespace on the target\. Default value: false Valid values: true/false Example: `enableHomogenousTablespace=true`  | 
|  `standbyDelayTime`  |  Use this attribute to specify a time in minutes for the delay in standby sync\. If the source is an Active Data Guard standby database, use this attribute to specify the time lag between primary and standby databases\. In AWS DMS, you can create an Oracle CDC task that uses an Active Data Guard standby instance as a source for replicating ongoing changes\. Doing this eliminates the need to connect to an active database that might be in production\. Default value:0  Valid values: Number  Example: `standbyDelayTime=1;`  | 
|  `securityDbEncryptionName`  |  Specifies the name of a key used for the transparent data encryption \(TDE\) of the columns and tablespace in the Oracle source database\. For more information on setting this attribute and its associated password on the Oracle source endpoint, see [Supported encryption methods for using Oracle as a source for AWS DMS](#CHAP_Source.Oracle.Encryption)\. Default value: ""  Valid values: String  `securityDbEncryptionName=ORACLE.SECURITY.DB.ENCRYPTION.Adg8m2dhkU/0v/m5QUaaNJEAAAAAAAAAAAAAAAAAAAAAAAAAAAAA`  | 
|  `spatialSdo2GeoJsonFunctionName`  |  For Oracle version 12\.1 or earlier sources migrating to PostgreSQL targets, use this attribute to convert SDO\_GEOMETRY to GEOJSON format\. By default, AWS DMS calls the `SDO2GEOJSON` custom function which must be present and accessible to the AWS DMS user\. Or you can create your own custom function that mimics the operation of `SDOGEOJSON` and set `spatialSdo2GeoJsonFunctionName` to call it instead\.  Default value: SDO2GEOJSON Valid values: String  Example: `spatialSdo2GeoJsonFunctionName=myCustomSDO2GEOJSONFunction;`  | 
|  `exposeViews`  |  Use this attribute to pull data once from a view; you can't use it for ongoing replication\. When you extract data from a view, the view is shown as a table on the target schema\. Default value: false Valid values: true/false Example: `exposeViews=true`  | 

## Source data types for Oracle<a name="CHAP_Source.Oracle.DataTypes"></a>

The Oracle endpoint for AWS DMS supports most Oracle data types\. The following table shows the Oracle source data types that are supported when using AWS DMS and the default mapping to AWS DMS data types\.

**Note**  
With the exception of the LONG and LONG RAW data types, when replicating from an Oracle source to an Oracle target \(a *homogeneous replication*\), all of the source and target data types will be identical\. But the LONG data type will be mapped to CLOB and the LONG RAW data type will be mapped to BLOB\. 

For information on how to view the data type that is mapped in the target, see the section for the target endpoint you are using\.

For additional information about AWS DMS data types, see [Data types for AWS Database Migration Service](CHAP_Reference.DataTypes.md)\.


|  Oracle data type  |  AWS DMS data type  | 
| --- | --- | 
|  BINARY\_FLOAT  |  REAL4  | 
|  BINARY\_DOUBLE  |  REAL8  | 
|  BINARY  |  BYTES  | 
|  FLOAT \(P\)  |  If precision is less than or equal to 24, use REAL4\. If precision is greater than 24, use REAL8\.  | 
|  NUMBER \(P,S\)  |  When scale is greater than 0, use REAL8  | 
|  NUMBER according to the `numberDataTypeScale` extra connection attribute\.  |  When scale is 0: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Source.Oracle.html) In all other cases, use REAL8\. | 
|  DATE  |  DATETIME  | 
|  INTERVAL\_YEAR TO MONTH  |  STRING \(with interval year\_to\_month indication\)  | 
|  INTERVAL\_DAY TO SECOND  |  STRING \(with interval day\_to\_second indication\)  | 
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
|  CLOB  |  CLOB To use this data type with AWS DMS, you must enable the use of CLOB data types for a specific task\. During CDC, AWS DMS supports CLOB data types only in tables that include a primary key\.  | 
|  NCLOB  |  NCLOB To use this data type with AWS DMS, you must enable the use of NCLOB data types for a specific task\. During CDC, AWS DMS supports NCLOB data types only in tables that include a primary key\.  | 
|  LONG  |  CLOB The LONG data type isn't supported in batch\-optimized apply mode \(TurboStream CDC mode\)\. To use this data type with AWS DMS, you must enable the use of LOBs for a specific task\. During CDC, AWS DMS supports LOB data types only in tables that have a primary key\.  | 
|  LONG RAW  |  BLOB The LONG RAW data type isn't supported in batch\-optimized apply mode \(TurboStream CDC mode\)\. To use this data type with AWS DMS, you must enable the use of LOBs for a specific task\. During CDC, AWS DMS supports LOB data types only in tables that have a primary key\.  | 
|  XMLTYPE  |  CLOB  | 
| SDO\_GEOMETRY | BLOB \(when an Oracle to Oracle migration\)CLOB \(when an Oracle to PostgreSQL migration\) | 

Oracle tables used as a source with columns of the following data types aren't supported and can't be replicated\. Replicating columns with these data types result in a null column\.
+ BFILE
+ ROWID
+ REF
+ UROWID
+ User\-defined data types
+ ANYDATA

**Note**  
Virtual columns aren't supported\.

### Migrating Oracle spatial data types<a name="CHAP_Source.Oracle.DataTypes.Spatial"></a>

*Spatial data* identifies the geometry information for an object or location in space\. In an Oracle database, the geometric description of a spatial object is stored in an object of type SDO\_GEOMETRY\. Within this object, the geometric description is stored in a single row in a single column of a user\-defined table\. 

AWS DMS supports migrating the Oracle type SDO\_GEOMETRY from an Oracle source to either an Oracle or PostgreSQL target\.

When you migrate Oracle spatial data types using AWS DMS, be aware of these considerations:
+ When migrating to an Oracle target, make sure to manually transfer USER\_SDO\_GEOM\_METADATA entries that include type information\. 
+ When migrating from an Oracle source endpoint to a PostgreSQL target endpoint, AWS DMS creates target columns\. These columns have default geometry and geography type information with a 2D dimension and a spatial reference identifier \(SRID\) equal to zero \(0\)\. An example is `GEOMETRY, 2, 0`\.
+ For Oracle version 12\.1 or earlier sources migrating to PostgreSQL targets, convert `SDO_GEOMETRY` objects to `GEOJSON` format by using the `SDO2GEOJSON` function, or the `spatialSdo2GeoJsonFunctionName` extra connection attribute\. For more information, see [Extra connection attributes when using Oracle as a source for AWS DMS](#CHAP_Source.Oracle.ConnectionAttrib)\.