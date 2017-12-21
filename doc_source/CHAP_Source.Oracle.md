# Using an Oracle Database as a Source for AWS DMS<a name="CHAP_Source.Oracle"></a>

You can migrate data from one or many Oracle databases using AWS DMS \(AWS DMS\)\. With an Oracle database as a source, you can migrate data to either another Oracle database or one of the other supported databases\.

AWS DMS supports as a migration source all Oracle database editions for versions 10\.2 and later, 11g, and up to 12\.1 on an on\-premises or EC2 instance, and all Oracle database editions for versions 11g \(versions 11\.2\.0\.3\.v1 and later\) and up to 12\.1 for an Amazon RDS DB instance\. An Oracle account with the specific access privileges is required\.

You can use SSL to encrypt connections between your Oracle endpoint and the replication instance\. For more information on using SSL with an Oracle endpoint, see [Using SSL With AWS Database Migration Service](CHAP_Security.SSL.md)\.

If you plan to use change data capture \(CDC\) from your Oracle database, you need to set up supplemental logging\. For information on setting up supplemental logging, see [Configuring Oracle as a Source for AWS DMS](#CHAP_Source.Oracle.Configuration)\. 

For additional details on working with Oracle databases and AWS DMS, see the following sections\. 


+ [Using Oracle LogMiner or Oracle Binary Reader for Change Data Capture \(CDC\)](#CHAP_Source.Oracle.LogMiner)
+ [Limitations on Using Oracle as a Source for AWS DMS](#CHAP_Source.Oracle.Limitations)
+ [Supported Oracle Compression Methods for AWS DMS](#CHAP_Source.Oracle.Compression)
+ [User Account Privileges Required for Using Oracle as a Source for AWS DMS](#CHAP_Source.Oracle.Privileges)
+ [Configuring Oracle as a Source for AWS DMS](#CHAP_Source.Oracle.Configuration)
+ [Configuring Oracle on an Amazon RDS DB Instance as a Source for AWS DMS](#CHAP_Source.Oracle.Configuration.AmazonRDS)
+ [Extra Connection Attributes When Using Oracle as a Source for AWS DMS](#CHAP_Source.Oracle.ConnectionAttrib)

## Using Oracle LogMiner or Oracle Binary Reader for Change Data Capture \(CDC\)<a name="CHAP_Source.Oracle.LogMiner"></a>

Oracle offers two methods for reading the logs when doing change processing: Oracle LogMiner and Oracle Binary Reader\.

By default, AWS DMS uses Oracle LogMiner for change data capture \(CDC\)\. Alternatively, you can choose to use the Oracle Binary Reader\. The Oracle Binary Reader bypasses LogMiner and reads the logs directly\. To enable the Binary Reader, you need to modify your source connection to include the following extra connection parameters:

```
useLogminerReader=N; useBfile=Y
```

To enable LogMiner, you need to modify your source connection to include the following extra connection parameter or leave the Extra Connection Attribute blank \(LogMiner is the default\):

```
useLogminerReader=Y
```

If the Oracle source database is using Oracle ASM \(Automatic Storage Management\), the extra connection parameter needs to include the ASM user name and ASM server address\. The password field also needs to have both passwords, the source user password and the ASM password\.

```
useLogminerReader=N;asm_user=<asm_username>;asm_server=<first_RAC_server_ip_address>/+ASM
```

If the Oracle source database is using Oracle ASM \(Automatic Storage Management\), the endpoint password field needs to have both the Oracle user password and the ASM password, separated by a comma\. For example, you might have `<oracle_user_password>,<asm_user_password>`

To use LogMiner or Binary Reader, you must set the correct permissions\. For information on setting these permissions, see the following section, [Access Privileges Required for Change Data Capture \(CDC\) on an Oracle Source Database](#CHAP_Source.Oracle.LogMiner.Privileges)

The advantages to using LogMiner with AWS DMS, instead of Binary Reader, include the following:

+ LogMiner supports most Oracle options, such as encryption options and compression options\. Binary Reader doesn't support all Oracle options, in particular options for encryption and compression\.

+ LogMiner offers a simpler configuration, especially compared to Oracle Binary Reader's direct access setup or if the redo logs are on Automatic Storage Management \(ASM\)\.

+ LogMiner can be used with Oracle sources that use Oracle transparent data encryption \(TDE\)\.

+ LogMiner supports the following HCC compression types for both full load and continuous replication \(CDC\):

  + QUERY HIGH

  + ARCHIVE HIGH

  + ARCHIVE LOW

  + QUERY LOW

The advantages to using Binary Reader with AWS DMS, instead of LogMiner, include the following:

+ For migrations with a high volume of changes, LogMiner might have some I/O or CPU impact on the computer hosting the Oracle source database\. Binary Reader has less chance of having I/O or CPU impact\.

+ For migrations with a high volume of changes, CDC performance is usually much better when using Binary Reader compared with using Oracle LogMiner\.

+ Binary Reader supports CDC for LOBS in Oracle version 12c\. LogMiner does not\.

+ Binary Reader supports the following HCC compression types for both full load and continuous replication \(CDC\):

  + QUERY HIGH

  + ARCHIVE HIGH

  + ARCHIVE LOW

  The QUERY LOW compression type is only supported for full load migrations\.

In general, use Oracle LogMiner for migrating your Oracle database unless you have one of the following situations:

+ You need to run several migration tasks on the source Oracle database\.

+ The volume of changes or the REDO log volume on the source Oracle database is large\.

+ You need to propagate changes to LOBs in Oracle 12c\.

### Access Privileges Required for Change Data Capture \(CDC\) on an Oracle Source Database<a name="CHAP_Source.Oracle.LogMiner.Privileges"></a>

The access privileges required depend on whether you use Oracle LogMiner or Oracle Binary Reader for CDC\.

The following privileges must be granted to the user account used for data migration when using Oracle LogMiner for change data capture \(CDC\) with an Oracle source database:

For Oracle versions prior to version 12c, grant the following:

+ CREATE SESSION

+ EXECUTE on DBMS\_LOGMNR

+ SELECT ANY TRANSACTION

+ SELECT on V\_$LOGMNR\_LOGS

+ SELECT on V\_$LOGMNR\_CONTENTS

For Oracle versions 12c and higher, grant the following:

+ LOGMINING \(for example, GRANT LOGMINING TO *<user account>*\)

+ CREATE SESSION

+ EXECUTE on DBMS\_LOGMNR

+ SELECT on V\_$LOGMNR\_LOGS

+ SELECT on V\_$LOGMNR\_CONTENTS

The following privileges must be granted to the user account used for data migration when using Oracle Binary Reader for change data capture \(CDC\) with an Oracle source database:

+ SELECT on v\_$transportable\_platform

  Grant the SELECT on v\_$transportable\_platform privilege if the Redo logs are stored in Automatic Storage Management \(ASM\) and accessed by AWS DMS from ASM\.

+ BFILE read \- Used when AWS DMS does not have file\-level access to the Redo logs, and the Redo logs are not accessed from ASM\.

+ DBMS\_FILE\_TRANSFER package \- Used to copy the Redo log files to a temporary folder \(in which case the EXECUTE ON DBMS\_FILE\_TRANSFER privilege needs to be granted as well\)

+ DBMS\_FILE\_GROUP package \- Used to delete the Redo log files from a temporary/alternate folder \(in which case the EXECUTE ON DBMS\_FILE\_ GROUP privilege needs to be granted as well\)\.

+ CREATE ANY DIRECTORY

Oracle file features work together with Oracle directories\. Each Oracle directory object includes the name of the folder containing the files which need to be processed\.

If you want AWS DMS to create and manage the Oracle directories, you need to grant the CREATE ANY DIRECTORY privilege specified preceding\. Directory names are prefixed with `amazon_`\. If you don't grant this privilege, you need to create the corresponding directories manually\. If you create the directories manually and the Oracle user specified in the Oracle Source endpoint is not the user that created the Oracle Directories, also grant the READ on DIRECTORY privilege\. 

If the Oracle source endpoint is configured to copy the redo log files to a temporary folder, and the Oracle user specified in the Oracle source endpoint is not the user that created the Oracle directories, the following additional privileges are required:

+ READ on the Oracle directory object specified as the source directory

+ WRITE on the directory object specified as the destination directory in the copy process

### Limitations for Change Data Capture \(CDC\) on an Oracle Source Database<a name="CHAP_Source.Oracle.LogMiner.Limitations"></a>

The following limitations apply when using an Oracle database as a source for AWS DMS \(AWS DMS\) change data capture\.

+ Oracle LogMiner, which AWS DMS uses for change data capture \(CDC\), doesn't support updating large binary objects \(LOBs\) when the UPDATE statement updates only LOB columns\. 

+ For Oracle 11, Oracle LogMiner doesn't support the UPDATE statement for XMLTYPE and LOB columns\. 

+ On Oracle 12c, LogMiner does not support LOB columns\. 

+ AWS DMS doesn't capture changes made by the Oracle DBMS\_REDEFINITION package, such as changes to table metadata and the OBJECT\_ID value\. 

+ AWS DMS doesn't support index\-organized tables with an overflow segment in change data capture \(CDC\) mode when using BFILE\. An example is when you access the redo logs without using LogMiner\.

+ AWS DMS doesn't support table clusters when you use Oracle Binary Reader\.

+  AWS DMS doesn't support the QUERY LOW compression type when using Binary Reader for change data capture \(CDC\)\.

+ AWS DMS doesn't support virtual columns\.

## Limitations on Using Oracle as a Source for AWS DMS<a name="CHAP_Source.Oracle.Limitations"></a>

The following limitations apply when using an Oracle database as a source for AWS DMS \(AWS DMS\)\. If you are using Oracle LogMiner or Oracle Binary Reader for change data capture \(CDC\), see [ Limitations for Change Data Capture \(CDC\) on an Oracle Source Database ](#CHAP_Source.Oracle.LogMiner.Limitations) for additional limitations\.

+ AWS DMS supports Oracle transparent data encryption \(TDE\) tablespace encryption and AWS Key Management Service \(AWS KMS\) encryption when used with Oracle LogMiner\. All other forms of encryption are not supported\.

+ Tables with LOBs must have a primary key in order to use change data capture \(CDC\)\.

+ AWS DMS supports the `rename table <table name> to <new table name>` syntax with Oracle version 11 and higher\. 

+ Oracle source databases columns created using explicit CHAR Semantics are transferred to a target Oracle database using BYTE semantics\. You must create tables containing columns of this type on the target Oracle database before migrating\.

+ AWS DMS doesn't replicate data changes resulting from partition or subpartition operations \(ADD, DROP, EXCHANGE, and TRUNCATE\)\. To replicate such changes, you need to reload the table being replicated\. AWS DMS replicates any future data changes to newly added partitions without your needing to reload the table again\. However, UPDATE operations on old data records in these partitions fail and generate a `0 rows affected` warning\. 

+ The data definition language \(DDL\) statement `ALTER TABLE ADD <column> <data_type> DEFAULT <>` doesn't replicate the default value to the target, and the new column in the target is set to NULL\. If the new column is nullable, Oracle updates all the table rows before logging the DDL itself\. As a result, AWS DMS captures the changes to the counters but doesn't update the target\. Because the new column is set to NULL, if the target table has no primary key or unique index, subsequent updates generate a `0 rows affected` warning\. 

+ Data changes resulting from the `CREATE TABLE AS` statement are not supported\. However, the new table is created on the target\. 

+ When limited\-size LOB mode is enabled, AWS DMS replicates empty LOBs on the Oracle source as NULL values in the target\. 

+  When AWS DMS begins CDC, it maps a timestamp to the Oracle system change number \(SCN\)\. By default, Oracle keeps only five days of the timestamp to SCN mapping\. Oracle generates an error if the timestamp specified is too old \(greater than the five day retention\)\. For more information, see the [ Oracle documentation](https://docs.oracle.com/cd/E11882_01/server.112/e41084/functions195.htm#SQLRF06326)\. 

+ AWS DMS does not support connections to an Oracle source via an ASM proxy\. 

## Supported Oracle Compression Methods for AWS DMS<a name="CHAP_Source.Oracle.Compression"></a>

AWS DMS supports the following HCC compression types for both full load and continuous replication \(CDC\) when using Binary Reader:

+ QUERY HIGH

+ ARCHIVE HIGH

+ ARCHIVE LOW

The QUERY LOW compression type is only supported for full load migrations\.

AWS DMS supports the following HCC compression types for both full load and continuous replication \(CDC\) when using LogMiner:

+ QUERY HIGH

+ ARCHIVE HIGH

+ ARCHIVE LOW

+ QUERY LOW

## User Account Privileges Required for Using Oracle as a Source for AWS DMS<a name="CHAP_Source.Oracle.Privileges"></a>

To use an Oracle database as a source in an AWS DMS task, the user specified in the AWS DMS Oracle database definitions must be granted the following privileges in the Oracle database\. To grant privileges on Oracle databases on Amazon RDS, use the stored procedure `rdsadmin.rdsadmin_util.grant_sys_object`\. For more information, see [ Granting SELECT or EXECUTE privileges to SYS Objects](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Appendix.Oracle.CommonDBATasks.html#Appendix.Oracle.CommonDBATasks.TransferPrivileges)\. 

**Note**  
When granting privileges, use the actual name of objects \(for example, `V_$OBJECT` including the underscore\), not the synonym for the object \(for example, `V$OBJECT` without the underscore\)\. 

+ SELECT ANY TRANSACTION 

+ SELECT on V\_$ARCHIVED\_LOG 

+ SELECT on V\_$LOG 

+ SELECT on V\_$LOGFILE

+ SELECT on V\_$DATABASE 

+ SELECT on V\_$THREAD 

+ SELECT on V\_$PARAMETER 

+ SELECT on V\_$NLS\_PARAMETERS 

+ SELECT on V\_$TIMEZONE\_NAMES 

+ SELECT on V\_$TRANSACTION 

+ SELECT on ALL\_INDEXES 

+ SELECT on ALL\_OBJECTS 

+ SELECT on DBA\_OBJECTS \(required if the Oracle version is earlier than 11\.2\.0\.3\)

+ SELECT on ALL\_TABLES 

+ SELECT on ALL\_USERS 

+ SELECT on ALL\_CATALOG 

+ SELECT on ALL\_CONSTRAINTS 

+ SELECT on ALL\_CONS\_COLUMNS 

+ SELECT on ALL\_TAB\_COLS 

+ SELECT on ALL\_IND\_COLUMNS 

+ SELECT on ALL\_LOG\_GROUPS 

+ SELECT on SYS\.DBA\_REGISTRY 

+ SELECT on SYS\.OBJ$ 

+ SELECT on DBA\_TABLESPACES 

+ SELECT on ALL\_TAB\_PARTITIONS 

+ SELECT on ALL\_ENCRYPTED\_COLUMNS 

For the requirements specified following, grant the additional privileges named:

+ If views are exposed, grant SELECT on ALL\_VIEWS\.

+ When you use a specific table list, for each replicated table grant SELECT\.

+ When you use a pattern for a specific table list, grant SELECT ANY TABLE\.

+ When you add supplemental logging, grant ALTER ANY TABLE\.

+ When you add supplemental logging and you use a specific table list, grant ALTER for each replicated table\.

+ When migrating from Oracle RAC, grant permission to materialized views with the prefixes g\_$ and v\_$\.

## Configuring Oracle as a Source for AWS DMS<a name="CHAP_Source.Oracle.Configuration"></a>

Before using an Oracle database as a data migration source, you need to perform several tasks\. For an Oracle database on Amazon RDS, see the following section\. You can also use extra connection attributes to configure the Oracle source\. For more information about extra connection attributes, see [Using Extra Connection Attributes with AWS Database Migration Service](CHAP_Introduction.ConnectionAttributes.md)\.

For an Oracle database on premises or on an Amazon EC2 instance, you should do the following:

+ **Provide Oracle Account Access** – You must provide an Oracle user account for AWS DMS\. The user account must have read/write privileges on the Oracle database, as specified in [User Account Privileges Required for Using Oracle as a Source for AWS DMS](#CHAP_Source.Oracle.Privileges)\. 

+ **Ensure that ARCHIVELOG Mode Is On** – Oracle can run in two different modes, the ARCHIVELOG mode and the NOARCHIVELOG mode\. To use Oracle with AWS DMS, the database in question must be in ARCHIVELOG mode\. 

+ **Set Up Supplemental Logging ** – The following steps, required only when using change data capture \(CDC\), show how to set up supplemental logging for an Oracle database\. For information on setting up supplemental logging on a database on an Amazon RDS DB instance, see [Configuring Oracle on an Amazon RDS DB Instance as a Source for AWS DMS](#CHAP_Source.Oracle.Configuration.AmazonRDS)\. 

**To set up supplemental logging for an Oracle database**

  1. Determine if supplemental logging is enabled for the database:

     + Run the following query:

       ```
       SELECT name, value, description FROM v$parameter WHERE 
       name = 'compatible';
       ```

       The return result should be from `GE to 9.0.0`\.

     + Run the following query:

       ```
       SELECT supplemental_log_data_min FROM v$database;
       ```

       The returned result should be `YES` or `IMPLICIT`\.

     + Enable supplemental logging by running the following query:

       ```
       ALTER DATABASE ADD SUPPLEMENTAL LOG DATA
       ```

  1. Make sure that the required supplemental logging is added for each table:

     +  If a primary key exists, supplemental logging must be added for the primary key, either by using the format to add supplemental logging on the primary key or by adding supplemental logging on the primary key columns\. 

     +  If no primary key exists and the table has a single unique index, then all of the unique index’s columns must be added to the supplemental log\. Using `SUPPLEMENTAL LOG DATA (UNIQUE INDEX) COLUMNS` doesn't add the unique index columns to the log\. 

     +  If no primary key exists and the table has multiple unique indexes, AWS DMS selects the first unique index\. AWS DMS uses the first index in an alphabetically ordered ascending list in this case\. Supplemental logging must be added on the selected index's columns\. Using SUPPLEMENTAL LOG DATA \(UNIQUE INDEX\) COLUMNS doesn't add the unique index columns to the log\. 

     +  If there is no primary key and no unique index, supplemental logging must be added on all columns\. 

       When the target table primary key or unique index is different than the source table primary key or unique index, you should add supplemental logging manually on the source table columns that make up the target table primary key or unique index\. 

     +  If you change the target table primary key, you should add supplemental logging on the selected index's columns, instead of the columns of the original primary key or unique index\. 

  1. Perform additional logging if necessary, for example if a filter is defined for a table\. 

     If a table has a unique index or a primary key, you need to add supplemental logging on each column that is involved in a filter if those columns are different than the primary key or unique index columns\. However, if ALL COLUMNS supplemental logging has been added to the table, you don't need to add any additional logging\. 

     ```
     ALTER TABLE EXAMPLE.TABLE ADD SUPPLEMENTAL LOG GROUP example_log_group (ID,NAME) 
     ALWAYS;
     ```
**Note**  
You can also turn on supplemental logging using a connection attribute\. If you use this option, you still need to enable supplemental logging at the database level using the following statement:  

     ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA                    
     ```
For more information on setting a connection attribute, see [Oracle](CHAP_Introduction.ConnectionAttributes.md#CHAP_Introduction.ConnectionAttributes.Oracle)

## Configuring Oracle on an Amazon RDS DB Instance as a Source for AWS DMS<a name="CHAP_Source.Oracle.Configuration.AmazonRDS"></a>

Using an Oracle database on an Amazon RDS DB instance as a data migration source requires several settings on the DB instance, including the following:

+ **Set Up Supplemental Logging** – AWS DMS requires database\-level supplemental logging to be enabled\. To enable database\-level supplemental logging, run the following command:

  ```
  exec rdsadmin.rdsadmin_util.alter_supplemental_logging('ADD');
  ```
**Note**  
You can also turn on supplemental logging using a connection attribute\. If you use this option, you still need to enable supplemental logging at the database level using the following statement:  

  ```
   exec rdsadmin.rdsadmin_util.alter_supplemental_logging('ADD');                    
  ```
For more information on setting a connection attribute, see [Oracle](CHAP_Introduction.ConnectionAttributes.md#CHAP_Introduction.ConnectionAttributes.Oracle)

  In addition to running this command, we recommend that you turn on PRIMARY KEY logging at the database level to enable change capture for tables that have primary keys\. To turn on PRIMARY KEY logging, run the following command:

  ```
  exec rdsadmin.rdsadmin_util.alter_supplemental_logging('ADD','PRIMARY KEY');
  ```

  If you want to capture changes for tables that don't have primary keys, you should alter the table to add supplemental logging using the following command:

  ```
  alter table table_name add supplemental log data (ALL) columns;
  ```

  Additionally, when you create new tables without specifying a primary key, you should either include a supplemental logging clause in the create statement or alter the table to add supplemental logging\. The following command creates a table and adds supplemental logging:

  ```
  create table table_name(column data type, supplemental log data(ALL) columns);                        
  ```

  If you create a table and later add a primary key, you need to add supplemental logging to the table\. The following command alters the table to include supplemental logging:

  ```
  alter table table_name add supplemental log data (PRIMARY KEY) columns;                        
  ```

+ **Enable Automatic Backups** – For information on setting up automatic backups, see the *[Amazon RDS User Guide](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithAutomatedBackups.html)*\.

+ **Set Up Archiving** – To retain archived redo logs of your Oracle database instance, which lets AWS DMS retrieve the log information using LogMiner, execute the following command\. In this example, logs are kept for 24 hours\. 

  ```
  exec rdsadmin.rdsadmin_util.set_configuration('archivelog retention hours',24);                    
  ```

  Make sure that your storage has sufficient space for the archived redo logs during the specified period\.

## Extra Connection Attributes When Using Oracle as a Source for AWS DMS<a name="CHAP_Source.Oracle.ConnectionAttrib"></a>

You can use extra connection attributes to configure your Oracle source\. You specify these settings when you create the source endpoint\.

The following table shows the extra connection attributes you can use to configure an Oracle database as a source for AWS DMS:


| Name | Description | 
| --- | --- | 
| `addSupplementalLogging ` | Set this attribute to automatically set up supplemental logging for the Oracle database\.  Default value: N  Valid values: Y/N  Example: `addSupplementalLogging=Y`   If you use this option, you still need to enable supplemental logging at the database level using the following statement: 

```
 ALTER DATABASE ADD SUPPLEMENTAL LOG DATA                    
```   | 
| `additionalArchivedLogDestId` | Set this attribute in conjunction with `archivedLogDestId` in a primary/standby setup\. This attribute is useful when a failover happens and AWS DMS needs to know which destination to get archive redo logs from to read changes because the previous primary instance is now a standby instance after failover\.  | 
| `useLogminerReader` | Set this attribute to Y to capture change data using the LogMiner utility \(the default\)\. Set this option to N if you want AWS DMS to access the redo logs as a binary file\. When set to N, you must also add the setting useBfile=Y\. For more information, see [Using Oracle LogMiner or Oracle Binary Reader for Change Data Capture \(CDC\)](#CHAP_Source.Oracle.LogMiner)\. Default value: Y  Valid values: Y/N  Example: `useLogminerReader=N; useBfile=Y`  If the Oracle source database is using Oracle ASM \(Automatic Storage Management\), the extra connection parameter needs to include the asm username and asm server address\. The password field will also need to have both passwords, the source user password, as well as the ASM password\. Example: `useLogminerReader=N;asm_user=<asm_username>; asm_server=<first_RAC_server_ip_address>/+ASM` If the Oracle source database is using Oracle ASM \(Automatic Storage Management\), the endpoint password field needs to have both the Oracle user password and the ASM password, separated by a comma\. Example: <oracle\_user\_password>,<asm\_user\_password>  | 
| ` retryInterval ` | Specifies the number of seconds that the system waits before resending a query\.  Default value: 5  Valid values: number starting from 1  Example: `retryInterval=6 ` | 
| `archivedLogDestId` | Specifies the destination of the archived redo logs\. The value should be the same as the DEST\_ID number in the $archived\_log table\. When working with multiple log destinations \(DEST\_ID\), we recommended that you to specify an Archived redo logs location identifier\. This will improve performance by ensuring that the correct logs are accessed from the outset\.  Default value:0  Valid values: Number  Example: `archivedLogDestId=1 ` | 
| `archivedLogsOnly` | When this field is set to Y, AWS DMS will only access the archived redo logs\. If the archived redo logs ares stored on ASM only, the AWS DMS user needs to be granted the ASM privileges\.  Default value: N  Valid values: Y/N  Example: `archivedLogsOnly=Y ` | 
| `numberDataTypeScale` | Specifies the Number scale\. You can select a scale up to 38 or you can select FLOAT\. By default the NUMBER data type is converted to precision 38, scale 10\.  Default value: 10  Valid values: \-1 to 38 \(\-1 for FLOAT\)  Example: `numberDataTypeScale =12 ` | 
| afterConnectScript |  Specifies a script to run immediately after AWS DMS connects to the endpoint\.  Valid values: Any SQL statement separated by a semi\-colon\.  Example: `afterConnectScript=ALTER SESSION SET CURRENT_SCHEMA = system; `  | 
| `failTasksOnLobTruncation` | When set to `true`, causes a task to fail if the actual size of an LOB column is greater than the specified `LobMaxSize`\. If task is set to Limited LOB mode and this option is set to true, the task will fail instead of truncating the LOB data\. Default value: false  Valid values: Boolean  Example: `failTasksOnLobTruncation=true ` | 