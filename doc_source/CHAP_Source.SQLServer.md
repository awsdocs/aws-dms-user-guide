# Using a Microsoft SQL Server Database as a Source for AWS DMS<a name="CHAP_Source.SQLServer"></a>

You can migrate data from one or many Microsoft SQL Server databases using AWS DMS \(AWS DMS\)\. With a SQL Server database as a source, you can migrate data to either another SQL Server database or one of the other supported databases\.

AWS DMS supports, as a source, on\-premises and Amazon EC2 instance databases for Microsoft SQL Server versions 2005, 2008, 2008R2, 2012, 2014, and 2016\. The Enterprise, Standard, Workgroup, and Developer editions are supported\. The Web and Express editions are not supported\.

AWS DMS supports, as a source, Amazon RDS DB instance databases for SQL Server versions 2008R2, 2012, 2014, and 2016\. The Enterprise and Standard editions are supported\. CDC is supported for all versions of Enterprise Edition\. CDC is only supported for Standard Edition version 2016 SP1 and later\. The Web, Workgroup, Developer, and Express editions are not supported\.

You can have the source SQL Server database installed on any computer in your network\. A SQL Server account with the appropriate access privileges to the source database for the type of task you chose is also required for use with AWS DMS\.

AWS DMS supports migrating data from named instances of SQL Server\. You can use the following notation in the server name when you create the source endpoint\.

```
IPAddress\InstanceName
```

For example, the following is a correct source endpoint server name\. Here, the first part of the name is the IP address of the server, and the second part is the SQL Server instance name \(in this example, SQLTest\)\.

```
10.0.0.25\SQLTest
```

You can use SSL to encrypt connections between your SQL Server endpoint and the replication instance\. For more information on using SSL with a SQL Server endpoint, see [Using SSL With AWS Database Migration Service](CHAP_Security.SSL.md)\.

To capture changes from a source SQL Server database, the database must be configured for full backups and must be either the Enterprise, Developer, or Standard Edition\. 

For additional details on working with SQL Server source databases and AWS DMS, see the following\.

**Topics**
+ [Limitations on Using SQL Server as a Source for AWS DMS](#CHAP_Source.SQLServer.Limitations)
+ [Using Ongoing Replication \(CDC\) from a SQL Server Source](#CHAP_Source.SQLServer.CDC)
+ [Supported Compression Methods](#CHAP_Source.SQLServer.Compression)
+ [Working with SQL Server AlwaysOn Availability Groups](#CHAP_Source.SQLServer.AlwaysOn)
+ [Configuring a SQL Server Database as a Replication Source for AWS DMS](#CHAP_Source.SQLServer.Configuration)
+ [Extra Connection Attributes When Using SQL Server as a Source for AWS DMS](#CHAP_Source.SQLServer.ConnectionAttrib)
+ [Source Data Types for SQL Server](#CHAP_Source.SQLServer.DataTypes)

## Limitations on Using SQL Server as a Source for AWS DMS<a name="CHAP_Source.SQLServer.Limitations"></a>

The following limitations apply when using a SQL Server database as a source for AWS DMS:
+ The identity property for a column is not migrated to a target database column\.
+  In AWS DMS engine versions before version 2\.4\.x, changes to rows with more than 8000 bytes of information, including header and mapping information, are not processed correctly due to limitations in the SQL Server TLOG buffer size\. Use the latest AWS DMS version to avoid this issue\.
+ The SQL Server endpoint does not support the use of sparse tables\.
+ Windows Authentication is not supported\.
+ Changes to computed fields in a SQL Server are not replicated\.
+ Temporal tables are not supported\.
+ SQL Server partition switching is not supported\.
+ A clustered index on the source is created as a nonclustered index on the target\.
+ When using the WRITETEXT and UPDATETEXT utilities, AWS DMS does not capture events applied on the source database\.
+ The following data manipulation language \(DML\) pattern is not supported: 

  ```
  SELECT <*> INTO <new_table> FROM <existing_table>
  ```
+ When using SQL Server as a source, column\-level encryption is not supported\.
+ Due to a known issue with SQL Server 2008 and 2008 R2, AWS DMS doesn't support server level audits on SQL Server 2008 and SQL Server 2008 R2 as a source endpoint\.

  For example, running the following command causes AWS DMS to fail:

  ```
  USE [master]
  GO 
  ALTER SERVER AUDIT [my_audit_test-20140710] WITH (STATE=on)
  GO
  ```

## Using Ongoing Replication \(CDC\) from a SQL Server Source<a name="CHAP_Source.SQLServer.CDC"></a>

You can use ongoing replication \(change data capture, or CDC\) for a self\-managed SQL Server database on\-premises or on Amazon EC2, or an Amazon\-managed database on Amazon RDS\. 

AWS DMS supports ongoing replication for these SQL Server configurations:
+ For source SQL Server instances that are on\-premises or on Amazon EC2, AWS DMS supports ongoing replication for SQL Server Enterprise, Standard, and Developer Edition\.
+ For source SQL Server instances running on Amazon RDS, AWS DMS supports ongoing replication for SQL Server Enterprise through SQL Server 2016 SP1\. Beyond this version, AWS DMS supports CDC for both SQL Server Enterprise and Standard editions\.

If you want AWS DMS to automatically set up the ongoing replication, the AWS DMS user account that you use to connect to the source database must have the sysadmin fixed server role\. If you don't want to assign the sysadmin role to the user account you use, you can still use ongoing replication by following the series of manual steps discussed following\.

The following requirements apply specifically when using ongoing replication with a SQL Server database as a source for AWS DMS:
+ SQL Server must be configured for full backups, and you must perform a backup before beginning to replicate data\.
+ The recovery model must be set to **Bulk logged** or **Full**\.
+ SQL Server backup to multiple disks isn't supported\. If the backup is defined to write the database backup to multiple files over different disks, AWS DMS can't read the data and the AWS DMS task fails\.
+ For self\-managed SQL Server sources, be aware that SQL Server Replication Publisher definitions for the source database used in a DMS CDC task aren't removed when you remove a task\. A SQL Server system administrator must delete these definitions from SQL Server for self\-managed sources\.
+ During CDC, AWS DMS needs to look up SQL Server transaction log backups to read changes\. AWS DMS doesn't support using SQL Server transaction log backups that were created using third\-party backup software\.
+ For self\-managed SQL Server sources, be aware that SQL Server doesn't capture changes on newly created tables until they've been published\. When tables are added to a SQL Server source, AWS DMS manages creating the publication\. However, this process might take several minutes\. Operations made to newly created tables during this delay aren't captured or replicated to the target\. 
+ AWS DMS change data capture requires FULLOGGING to be turned on in SQL Server\. To turn on FULLLOGGING in SQL Server, either enable MS\-REPLICATION or CHANGE DATA CAPTURE \(CDC\)\.
+ You can't reuse the SQL Server *tlog* until the changes have been processed\.
+ CDC operations aren't supported on memory\-optimized tables\. This limitation applies to SQL Server 2014 \(when the feature was first introduced\) and later\.

### Capturing Data Changes for SQL Server<a name="CHAP_Source.SQLServer.CDC.MSCDC"></a>

For a self\-managed SQL Server source, AWS DMS uses the following:
+ MS\-Replication, to capture changes for tables with primary keys\. You can configure this automatically by giving the AWS DMS endpoint user sysadmin privileges on the source SQL Server instance\. Alternatively, you can follow the steps provided in this section to prepare the source and use a non\-sysadmin user for the AWS DMS endpoint\.
+ MS\-CDC, to capture changes for tables without primary keys\. MS\-CDC must be enabled at the database level, and for all of the tables individually\.

For a SQL Server source running on Amazon RDS, AWS DMS uses MS\-CDC to capture changes for tables, with or without primary keys\. MS\-CDC must be enabled at the database level, and for all of the tables individually, using the Amazon RDS\-specific stored procedures described in this section\.

There are several ways you can use a SQL Server database for ongoing replication \(CDC\):
+ Set up ongoing replication using the sysadmin role\. \(This applies only to self\-managed SQL Server sources\.\)
+ Set up ongoing replication to not use the sysadmin role\. \(This applies only to self\-managed SQL Server sources\.\)
+ Set up ongoing replication for an Amazon RDS for SQL Server DB instance\.

### Setting Up Ongoing Replication Using the sysadmin Role<a name="CHAP_Source.SQLServer.CDC.MSCDC.SettingUp"></a>

For tables with primary keys, AWS DMS can configure the required artifacts on the source\. For tables without primary keys, you need to set up MS\-CDC\.

First, enable MS\-CDC for the database by running the following command\. Use an account that has the sysadmin role assigned to it\.

```
use [DBname]
EXEC sys.sp_cdc_enable_db
```

Next, enable MS\-CDC for each of the source tables by running the following command\.

```
EXECUTE sys.sp_cdc_enable_table @source_schema = N'MySchema', @source_name =
N'MyTable', @role_name = NULL;
```

For more information on setting up MS\-CDC for specific tables, see the [SQL Server documentation](https://msdn.microsoft.com/en-us/library/cc627369.aspx)\. 

### Setting Up Ongoing Replication Without Assigning the sysadmin Role<a name="CHAP_Source.SQLServer.CDC.NoAdmin.SettingUp"></a>

You can set up ongoing replication for a SQL Server database source that doesn't require the user account to have sysadmin privileges\. 

**To set up a SQL Server database source for ongoing replication without using the sysadmin role**

1. Create a new SQL Server account with password authentication using SQL Server Management Studio \(SSMS\)\. In this example, we use an account called `dmstest`\.

1.  In the **User Mappings** section of SSMS, choose the MSDB and MASTER databases \(which gives public permission\) and assign the DB\_OWNER role for the database you want to use ongoing replication\.

1. Open the context \(right\-click\) menu for the new account, choose **Security** and explicitly grant the Connect SQL privilege\.

1. Run the following grant commands\. 

   ```
   GRANT SELECT ON FN_DBLOG TO dmstest;
   GRANT SELECT ON FN_DUMP_DBLOG TO dmstest;
   GRANT VIEW SERVER STATE TO dmstest;
   use msdb;
   GRANT EXECUTE ON MSDB.DBO.SP_STOP_JOB TO dmstest;
   GRANT EXECUTE ON MSDB.DBO.SP_START_JOB TO dmstest;
   GRANT SELECT ON MSDB.DBO.BACKUPSET TO dmstest;
   GRANT SELECT ON MSDB.DBO.BACKUPMEDIAFAMILY TO dmstest;
   GRANT SELECT ON MSDB.DBO.BACKUPFILE TO dmstest;
   ```

1. In SSMS, open the context \(right\-click\) menu for the **Replication** folder, and then choose **Configure Distribution**\. Follow all default steps and configure this SQL Server instance for distribution\. A distribution database is created under databases\.

1. Create a publication using the procedure following\.

1. Create a new AWS DMS task with SQL Server as the source endpoint using the user account you created\. 

**Note**  
The steps in this procedure apply only for tables with primary keys\. You still need to enable MS\-CDC for tables without primary keys\.

### Creating a SQL Server Publication for Ongoing Replication<a name="CHAP_Source.SQLServer.CDC.Publication"></a>

To use CDC with SQL Server, you must create a publication for each table that is participating in ongoing replication\.

**To create a publication for SQL Server ongoing replication**

1.  Login to SSMS using the SYSADMIN user account\.

1. Expand **Replication**\.

1. Right click **Local Publications**\.

1.  In the **New Publication Wizard**, choose **Next**\.

1. Select the database where you want to create the publication\.

1.  Choose **Transactional publication**\. Choose **Next**\.

1.  Expand **Tables** and select the tables with PK \(also these tables you want to publish\)\. Choose **Next**\.

1. You don't need to create a filter, so choose **Next**\.

1.  You don't need to create a Snapshot Agent, so choose **Next**\.

1. Choose **Security Settings** and choose **Run under the SQL Server Agent service account**\. Make sure to choose **By impersonating the process account** for publisher connection\. Choose **OK**\.

1. Choose **Next**\.

1. Choose **Create the publication**\.

1. Provide a name of the publication in the following format: 

   `AR_PUBLICATION_000<DBID>`\. For example, you could name the publication `AR_PUBLICATION_00018`\. You can also use the DB\_ID function in SQL Server\. For more information on the DB\_ID function, see [ the SQL Server documentation\. ](https://docs.microsoft.com/en-us/sql/t-sql/functions/db-id-transact-sql?view=sql-server-2017)\.

### Setting Up Ongoing Replication on an Amazon RDS for SQL Server DB Instance<a name="CHAP_Source.SQLServer.CDC.RDS.SettingUp"></a>

Amazon RDS for SQL Server supports MS\-CDC for all versions of Amazon RDS for SQL Server Enterprise editions up to SQL Server 2016 SP1\. Standard editions of SQL Server 2016 SP1 and later versions support MS\-CDC for Amazon RDS for SQL Server\. 

Unlike self\-managed SQL Server sources, Amazon RDS for SQL Server doesn't support MS\-Replication\. Therefore, AWS DMS needs to use MS\-CDC for tables with or without primary keys\.

Amazon RDS does not grant sysadmin privileges for setting replication artifacts that AWS DMS uses for on\-going changes in a source SQL Server instance\. You must enable MS\-CDC on the Amazon RDS instance using master user privileges in the following procedure\.

**To enable MS\-CDC on an RDS for SQL Server DB instance**

1. Run the following query at the database level\.

   ```
   exec msdb.dbo.rds_cdc_enable_db '<DB instance name>'
   ```

1. For each table with a primary key, run the following query to enable MS\-CDC\.

   ```
   exec sys.sp_cdc_enable_table
   @source_schema = N'db_name',
   @source_name = N'table_name',
   @role_name = NULL,
   @supports_net_changes = 1
   GO
   ```

   For each table with unique keys but no primary key, run the following query to enable MS\-CDC\.

   ```
   exec sys.sp_cdc_enable_table
   @source_schema = N'db_name',
   @source_name = N'table_name',
   @index_name = N'unique_index_name'
   @role_name = NULL,
   @supports_net_changes = 1
   GO
   ```

    For each table with no primary key nor unique keys, run the following query to enable MS\-CDC\.

   ```
   exec sys.sp_cdc_enable_table
   @source_schema = N'db_name',
   @source_name = N'table_name',
   @role_name = NULL
   GO
   ```

1.  Set the retention period for changes to be available on the source using the following command\.

   ```
   EXEC sys.sp_cdc_change_job @job_type = 'capture' ,@pollinginterval = 86400
   ```

   The parameter `@pollinginterval` is measured in seconds\. The preceding command retains changes for one day\. AWS recommends a one day retention period when using MS\-CDC with AWS DMS\.

## Supported Compression Methods<a name="CHAP_Source.SQLServer.Compression"></a>

The following table shows the compression methods that AWS DMS supports for each SQL Server version\. 


| SQL Server Version  |  Row/Page Compression \(at Partition Level\)  |  Vardecimal Storage Format | 
| --- | --- | --- | 
| 2005 | No | No | 
| 2008 | Yes | No | 
| 2012 | Yes | No | 
| 2014 | Yes | No | 

**Note**  
Sparse columns and columnar structure compression are not supported\.

## Working with SQL Server AlwaysOn Availability Groups<a name="CHAP_Source.SQLServer.AlwaysOn"></a>

The SQL Server AlwaysOn Availability Groups feature is a high\-availability and disaster\-recovery solution that provides an enterprise\-level alternative to database mirroring\. 

To use AlwaysOn Availability Groups as a source in AWS DMS, do the following:
+ Enable the Distribution option on all SQL Server instances in your Availability Replicas\.
+ In the AWS DMS console, open the SQL Server source database settings\. For **Server Name**, specify the Domain Name Service \(DNS\) name or IP address that was configured for the Availability Group Listener\. 

When you start an AWS DMS task for the first time, it might take longer than usual to start because the creation of the table articles is being duplicated by the Availability Groups Server\. 

## Configuring a SQL Server Database as a Replication Source for AWS DMS<a name="CHAP_Source.SQLServer.Configuration"></a>

You can configure a SQL Server database as a replication source for AWS DMS \(AWS DMS\)\. For the most complete replication of changes, we recommend that you use the Enterprise, Standard, or Developer edition of SQL Server\. One of these versions is required because these are the only versions that include MS\-Replication\(EE,SE\) and MS\-CDC\(EE,DEV\)\. The source SQL Server must also be configured for full backups\. In addition, AWS DMS must connect with a user \(a SQL Server instance login\) that has the sysadmin fixed server role on the SQL Server database you are connecting to\. 

Following, you can find information about configuring SQL Server as a replication source for AWS DMS\.

## Extra Connection Attributes When Using SQL Server as a Source for AWS DMS<a name="CHAP_Source.SQLServer.ConnectionAttrib"></a>

You can use extra connection attributes to configure your SQL Server source\. You specify these settings when you create the source endpoint\. Multiple extra connection attribute settings should be separated by a semicolon\.

The following table shows the extra connection attributes you can use with SQL Server as a source:


| Name | Description | 
| --- | --- | 
| `safeguardPolicy` | For optimal performance, AWS DMS tries to capture all unread changes from the active transaction log \(TLOG\)\. However, sometimes due to truncation, the active TLOG might not contain all of the unread changes\. When this occurs, AWS DMS accesses the backup log to capture the missing changes\. To minimize the need to access the backup log, AWS DMS prevents truncation using one of the following methods:  1\. **Start transactions in the database:** This is the default method\. When this method is used, AWS DMS prevents TLOG truncation by mimicking a transaction in the database\. As long as such a transaction is open, changes that appear after the transaction started aren't truncated\. If you need Microsoft Replication to be enabled in your database, then you must choose this method\.  2\.** Exclusively use sp\_repldone within a single task:** When this method is used, AWS DMS reads the changes and then uses sp\_repldone to mark the TLOG transactions as ready for truncation\. Although this method does not involve any transactional activities, it can only be used when Microsoft Replication is not running\. Also, when using this method, only one AWS DMS task can access the database at any given time\. Therefore, if you need to run parallel AWS DMS tasks against the same database, use the default method\.  Default value: `RELY_ON_SQL_SERVER_REPLICATION_AGENT`  Valid values: \{`EXCLUSIVE_AUTOMATIC_TRUNCATION`, `RELY_ON_SQL_SERVER_REPLICATION_AGENT`\}  Example: `safeguardPolicy= RELY_ON_SQL_SERVER_REPLICATION_AGENT ` | 
| `readBackupOnly` | When this parameter is set to `Y`, AWS DMS only reads changes from transaction log backups and does not read from the active transaction log file during ongoing replication\. Setting this parameter to `Y` can add up some source latency to ongoing replication but it lets you control active transaction log file growth during full load and ongoing replication tasks\.  Valid values: `N` or `Y`\. The default is `N`\.  Example: `readBackupOnly=Y`  | 

## Source Data Types for SQL Server<a name="CHAP_Source.SQLServer.DataTypes"></a>

Data migration that uses SQL Server as a source for AWS DMS supports most SQL Server data types\. The following table shows the SQL Server source data types that are supported when using AWS DMS and the default mapping from AWS DMS data types\.

For information on how to view the data type that is mapped in the target, see the section for the target endpoint you are using\.

For additional information about AWS DMS data types, see [Data Types for AWS Database Migration Service](CHAP_Reference.DataTypes.md)\.


|  SQL Server Data Types  |  AWS DMS Data Types  | 
| --- | --- | 
|  BIGINT  |  INT8  | 
|  BIT  |  BOOLEAN  | 
|  DECIMAL  |  NUMERIC  | 
|  INT  |  INT4  | 
|  MONEY  |  NUMERIC  | 
|  NUMERIC \(p,s\)  |  NUMERIC   | 
|  SMALLINT  |  INT2  | 
|  SMALLMONEY  |  NUMERIC  | 
|  TINYINT  |  UINT1  | 
|  REAL  |  REAL4  | 
|  FLOAT  |  REAL8  | 
|  DATETIME  |  DATETIME  | 
|  DATETIME2 \(SQL Server 2008 and later\)  |  DATETIME  | 
|  SMALLDATETIME  |  DATETIME  | 
|  DATE  |  DATE  | 
|  TIME  |  TIME  | 
|  DATETIMEOFFSET  |  WSTRING  | 
|  CHAR  |  STRING  | 
|  VARCHAR  |  STRING  | 
|  VARCHAR \(max\)  |  CLOB TEXT To use this data type with AWS DMS, you must enable the use of CLOB data types for a specific task\. For SQL Server tables, AWS DMS updates LOB columns in the target even for UPDATE statements that don't change the value of the LOB column in SQL Server\. During CDC, AWS DMS supports CLOB data types only in tables that include a primary key\.  | 
|  NCHAR  |  WSTRING  | 
|  NVARCHAR \(length\)  |  WSTRING  | 
|  NVARCHAR \(max\)  |  NCLOB NTEXT To use this data type with AWS DMS, you must enable the use of NCLOB data types for a specific task\. For SQL Server tables, AWS DMS updates LOB columns in the target even for UPDATE statements that don't change the value of the LOB column in SQL Server\. During CDC, AWS DMS supports CLOB data types only in tables that include a primary key\.  | 
|  BINARY  |  BYTES  | 
|  VARBINARY  |  BYTES  | 
|  VARBINARY \(max\)  |  BLOB IMAGE For SQL Server tables, AWS DMS updates LOB columns in the target even for UPDATE statements that don't change the value of the LOB column in SQL Server\. To use this data type with AWS DMS, you must enable the use of BLOB data types for a specific task\. AWS DMS supports BLOB data types only in tables that include a primary key\.  | 
|  TIMESTAMP  |  BYTES  | 
|  UNIQUEIDENTIFIER  |  STRING  | 
|  HIERARCHYID   |  Use HIERARCHYID when replicating to a SQL Server target endpoint\. Use WSTRING \(250\) when replicating to all other target endpoints\.  | 
|  XML  |  NCLOB For SQL Server tables, AWS DMS updates LOB columns in the target even for UPDATE statements that don't change the value of the LOB column in SQL Server\. To use this data type with AWS DMS, you must enable the use of NCLOB data types for a specific task\. During CDC, AWS DMS supports NCLOB data types only in tables that include a primary key\.  | 
|  GEOMETRY  |  Use GEOMETRY when replicating to target endpoints that support this data type\. Use CLOB when replicating to target endpoints that don't support this data type\.  | 
|  GEOGRAPHY  |  Use GEOGRAPHY when replicating to target endpoints that support this data type\. Use CLOB when replicating to target endpoints that don't support this data type\.  | 

AWS DMS doesn't support tables that include fields with the following data types: 
+ CURSOR
+ SQL\_VARIANT
+ TABLE

**Note**  
User\-defined data types are supported according to their base type\. For example, a user\-defined data type based on DATETIME is handled as a DATETIME data type\.