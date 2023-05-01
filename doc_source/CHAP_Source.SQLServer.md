# Using a Microsoft SQL Server database as a source for AWS DMS<a name="CHAP_Source.SQLServer"></a>

Migrate data from one or many Microsoft SQL Server databases using AWS DMS\. With a SQL Server database as a source, you can migrate data to another SQL Server database, or to one of the other AWS DMS supported databases\.  

For information about versions of SQL Server that AWS DMS supports as a source, see [Sources for AWS DMS](CHAP_Introduction.Sources.md)\.

The source SQL Server database can be installed on any computer in your network\. A SQL Server account with appropriate access privileges to the source database for the type of task you chose is required for use with AWS DMS\. This account must have the `view definition` and `view server state` permissions\. You add this permission using the following command:

```
grant view definition to [user]
grant view server state to [user]
```

AWS DMS supports migrating data from named instances of SQL Server\. You can use the following notation in the server name when you create the source endpoint\.

```
IPAddress\InstanceName
```

For example, the following is a correct source endpoint server name\. Here, the first part of the name is the IP address of the server, and the second part is the SQL Server instance name \(in this example, SQLTest\)\.

```
10.0.0.25\SQLTest
```

Also, obtain the port number that your named instance of SQL Server listens on, and use it to configure your AWS DMS source endpoint\. 

**Note**  
Port 1433 is the default for Microsoft SQL Server\. But dynamic ports that change each time SQL Server is started, and specific static port numbers used to connect to SQL Server through a firewall are also often used\. So, you want to know the actual port number of your named instance of SQL Server when you create the AWS DMS source endpoint\.

You can use SSL to encrypt connections between your SQL Server endpoint and the replication instance\. For more information on using SSL with a SQL Server endpoint, see [Using SSL with AWS Database Migration Service](CHAP_Security.SSL.md)\.

For additional details on working with SQL Server source databases and AWS DMS, see the following\.

**Topics**
+ [Limitations on using SQL Server as a source for AWS DMS](#CHAP_Source.SQLServer.Limitations)
+ [Permissions for full load only tasks](#CHAP_Source.SQLServer.Permissions)
+ [Prerequisites for using ongoing replication \(CDC\) from a SQL Server source](#CHAP_Source.SQLServer.Prerequisites)
+ [Capturing data changes for self\-managed SQL Server on\-premises or on Amazon EC2](#CHAP_Source.SQLServer.CDC)
+ [Setting up ongoing replication on a cloud SQL Server DB instance](#CHAP_Source.SQLServer.Configuration)
+ [Recommended settings when using Amazon RDS for SQL Server as a source for AWS DMS](#CHAP_Source.SQLServer.OptionalSettings)
+ [Supported compression methods for SQL Server](#CHAP_Source.SQLServer.Compression)
+ [Working with self\-managed SQL Server AlwaysOn availability groups](#CHAP_Source.SQLServer.AlwaysOn)
+ [Endpoint settings when using SQL Server as a source for AWS DMS](#CHAP_Source.SQLServer.ConnectionAttrib)
+ [Source data types for SQL Server](#CHAP_Source.SQLServer.DataTypes)

## Limitations on using SQL Server as a source for AWS DMS<a name="CHAP_Source.SQLServer.Limitations"></a>

The following limitations apply when using a SQL Server database as a source for AWS DMS:
+ The identity property for a column isn't migrated to a target database column\.
+ The SQL Server endpoint doesn't support the use of sparse tables\.
+ Windows Authentication isn't supported\.
+ Changes to computed fields in a SQL Server aren't replicated\.
+ Temporal tables aren't supported\.
+ SQL Server partition switching isn't supported\.
+ When using the WRITETEXT and UPDATETEXT utilities, AWS DMS doesn't capture events applied on the source database\.
+ The following data manipulation language \(DML\) pattern isn't supported\. 

  ```
  SELECT * INTO new_table FROM existing_table
  ```
+ When using SQL Server as a source, column\-level encryption isn't supported\.
+ Transparent Data Encryption \(TDE\) enabled at the database level is supported\.
+ AWS DMS doesn't support server level audits on SQL Server 2008 or SQL Server 2008 R2 as sources\. This is because of a known issue with SQL Server 2008 and 2008 R2\. For example, running the following command causes AWS DMS to fail\.

  ```
  USE [master]
  GO 
  ALTER SERVER AUDIT [my_audit_test-20140710] WITH (STATE=on)
  GO
  ```
+ Geometry columns aren't supported in full lob mode when using SQL Server as a source\. Instead, use limited lob mode or set the `InlineLobMaxSize` task setting to use inline lob mode\.
+ When using a Microsoft SQL Server source database in a replication task, the SQL Server Replication Publisher definitions aren't removed if you remove the task\. A Microsoft SQL Server system administrator must delete those definitions from Microsoft SQL Server\.
+ Migrating data from schema\-bound and non\-schema\-bound views is supported for full\-load only tasks\. 
+ Renaming tables using sp\_rename isn't supported \(for example, `sp_rename 'Sales.SalesRegion', 'SalesReg;)`
+ Renaming columns using sp\_rename isn't supported \(for example, `sp_rename 'Sales.Sales.Region', 'RegID', 'COLUMN';`\)
+ AWS DMS doesn't support change processing to set and unset column default values \(using the `ALTER COLUMN SET DEFAULT` clause with `ALTER TABLE` statements\)\.
+ AWS DMS doesn't support change processing to set column nullability \(using the `ALTER COLUMN [SET|DROP] NOT NULL` clause with `ALTER TABLE` statements\)\.
+ With SQL Server 2012 and SQL Server 2014, when using DMS replication with Availability Groups, the distribution database can't be placed in an availability group\. SQL 2016 supports placing the distribution database into an availability group, except for distribution databases used in merge, bidirectional, or peer\-to\-peer replication topologies\.
+ For partitioned tables, AWS DMS doesn't support different data compression settings for each partition\.
+ When inserting a value into SQL Server spatial data types \(GEOGRAPHY and GEOMETRY\), you can either ignore the spatial reference system identifier \(SRID\) property or specify a different number\. When replicating tables with spatial data types, AWS DMS replaces the SRID with the default SRID \(0 for GEOMETRY and 4326 for GEOGRAPHY\)\.
+ If your database isn't configured for MS\-REPLICATION or MS\-CDC, you can still capture tables that do not have a Primary Key, but only INSERT/DELETE DML events are captured\. UPDATE and TRUNCATE TABLE events are ignored\.
+ Columnstore indexes aren't supported\.
+ Memory\-optimized tables \(using In\-Memory OLTP\) aren't supported\.
+ When replicating a table with a primary key that consists of multiple columns, updating the primary key columns during full load isn't supported\.
+ Delayed durability isn't supported\.
+ The `readBackupOnly=Y` endpoint setting \(extra connection attribute\) doesn't work on RDS for SQL Server source instances because of the way RDS performs backups\.
+ `EXCLUSIVE_AUTOMATIC_TRUNCATION` doesn’t work on Amazon RDS SQL Server source instances because RDS users don't have access to run the SQL Server stored procedure, `sp_repldone`\.
+ AWS DMS doesn't capture truncate commands\.
+ AWS DMS doesn't support replication from databases with accelerated database recovery \(ADR\) turned on\.
+ AWS DMS doesn't support capturing data definition language \(DDL\) and data manipulation language \(DML\) statements within a single transaction\.
+ AWS DMS doesn't support the replication of data\-tier application packages \(DACPAC\)\.
+ UPDATE statements that involve primary keys or unique indexes and update multiple data rows, can cause conflicts when you apply changes to the target database\. This might happen, for example, when the target database applies updates as INSERT and DELETE statements instead of a single UPDATE statement\. With the batch optimized apply mode, the table might be ignored\. With the transactional apply mode, the UPDATE operation might result in constraint violations\. To avoid this issue, reload the relevant table\. Alternatively, locate the problematic records in the Apply Exceptions control table \(`dmslogs.awsdms_apply_exceptions`\) and edit them manually in the target database\. For more information, see [Change processing tuning settings](CHAP_Tasks.CustomizingTasks.TaskSettings.ChangeProcessingTuning.md)\.
+ AWS DMS doesn't support the replication of tables and schemas, where the name includes a special character from the following set\.

  `\\ -- \n \" \b \r ' \t ;` 
+ Data masking isn't supported\. AWS DMS migrates masked data without masking\.
+ AWS DMS replicates up to 32,767 tables with primary keys and up to 1,000 columns for each table\. This is because AWS DMS creates a SQL Server replication article for each replicated table, and SQL Server replication articles have these limitations\.
+ When using Change Data Capture \(CDC\), you must define all columns that make up a unique index as `NOT NULL`\. If this requirement is not met, SQL Server system error 22838 will result\. 

The following limitations apply when accessing the backup transaction logs:
+ Encrypted backups aren't supported\.
+ Backups stored at a URL or on Windows Azure aren't supported\.

The following limitations apply when accessing the backup transaction logs at file level:
+ The backup transaction logs must reside in a shared folder with the appropriate permissions and access rights\.
+ Active transaction logs are accessed through the Microsoft SQL Server API \(and not at file\-level\)\.
+ UNIX platforms aren't supported\.
+ Reading the backup logs from multiple stripes isn't supported\.
+ Microsoft SQL Server backup to multiple disks \(for example, `MIRROR TO DISK`\) isn't supported\.

## Permissions for full load only tasks<a name="CHAP_Source.SQLServer.Permissions"></a>

The following permissions are required to perform full load only tasks\. Note that AWS DMS does not create the `dms_user` login\. For information about creating a login for SQL Server, see [Creating a database user with Microsoft SQL Server](fa-database-users.md#fa-database-users-sql-server)\.

```
USE db_name;
                
                CREATE USER dms_user FOR LOGIN dms_user; 
                ALTER ROLE [db_datareader] ADD MEMBER dms_user; 
                GRANT VIEW DATABASE STATE to dms_user ; 
                
                USE master;
                
                GRANT VIEW SERVER STATE TO dms_user;
```

## Prerequisites for using ongoing replication \(CDC\) from a SQL Server source<a name="CHAP_Source.SQLServer.Prerequisites"></a>

You can use ongoing replication \(change data capture, or CDC\) for a self\-managed SQL Server database on\-premises or on Amazon EC2, or a cloud database such as Amazon RDS or a Microsoft Azure SQL managed instance\.

The following requirements apply specifically when using ongoing replication with a SQL Server database as a source for AWS DMS:
+ SQL Server must be configured for full backups, and you must perform a backup before beginning to replicate data\.
+ The recovery model must be set to **Bulk logged** or **Full**\.
+ SQL Server backup to multiple disks isn't supported\. If the backup is defined to write the database backup to multiple files over different disks, AWS DMS can't read the data and the AWS DMS task fails\.
+ For self\-managed SQL Server sources, SQL Server Replication Publisher definitions for the source used in a DMS CDC task aren't removed when you remove the task\. A SQL Server system administrator must delete these definitions from SQL Server for self\-managed sources\.
+ During CDC, AWS DMS needs to look up SQL Server transaction log backups to read changes\. AWS DMS doesn't support SQL Server transaction log backups created using third\-party backup software that* aren't *in native format\. To support transaction log backups that *are* in native format and created using third\-party backup software, add the `use3rdPartyBackupDevice=Y` connection attribute to the source endpoint\.
+ For self\-managed SQL Server sources, be aware that SQL Server doesn't capture changes on newly created tables until they've been published\. When tables are added to a SQL Server source, AWS DMS manages creating the publication\. However, this process might take several minutes\. Operations made to newly created tables during this delay aren't captured or replicated to the target\. 
+ AWS DMS change data capture requires full transaction logging to be turned on in SQL Server\. To turn on full transaction logging in SQL Server, either enable MS\-REPLICATION or CHANGE DATA CAPTURE \(CDC\)\.
+ SQL Server *tlog* entries won't be marked for re\-use until the MS CDC capture job processes those changes\.
+ CDC operations aren't supported on memory\-optimized tables\. This limitation applies to SQL Server 2014 \(when the feature was first introduced\) and later\.

## Capturing data changes for self\-managed SQL Server on\-premises or on Amazon EC2<a name="CHAP_Source.SQLServer.CDC"></a>

To capture changes from a source Microsoft SQL Server database, make sure that the database is configured for full backups\. Configure the database either in full recovery mode or bulk\-logged mode\.

For a self\-managed SQL Server source, AWS DMS uses the following:

**MS\-Replication**  
To capture changes for tables with primary keys\. You can configure this automatically by giving sysadmin privileges to the AWS DMS endpoint user on the source SQL Server instance\. Or you can follow the steps in this section to prepare the source and use a user that doesn't have sysadmin privileges for the AWS DMS endpoint\.

**MS\-CDC**  
To capture changes for tables without primary keys\. Enable MS\-CDC at the database level and for all of the tables individually\.

When setting up a SQL Server database for ongoing replication \(CDC\), you can do one of the following:
+ Set up ongoing replication using the sysadmin role\.
+ Set up ongoing replication to not use the sysadmin role\.

### Setting up ongoing replication using the sysadmin role with self\-managed SQL Server<a name="CHAP_Source.SQLServer.CDC.MSCDC"></a>

AWS DMS ongoing replication for SQL Server uses native SQL Server replication for tables with primary keys, and change data capture \(CDC\) for tables without primary keys\.

Before setting up ongoing replication, see [Prerequisites for using ongoing replication \(CDC\) from a SQL Server source](#CHAP_Source.SQLServer.Prerequisites)\. 

For tables with primary keys, AWS DMS can generally configure the required artifacts on the source\. However, for SQL Server source instances that are self\-managed, make sure to first configure the SQL Server distribution manually\. After you do so, AWS DMS source users with sysadmin permission can automatically create the publication for tables with primary keys\.

To check if distribution has already been configured, run the following command\.

```
sp_get_distributor
```

If the result is `NULL` for column distribution, distribution isn't configured\. You can use the following procedure to set up distribution\.<a name="CHAP_Source.SQLServer.CDC.MSCDC.Setup"></a>

**To set up distribution**

1. Connect to your SQL Server source database using the SQL Server Management Studio \(SSMS\) tool\.

1. Open the context \(right\-click\) menu for the **Replication** folder, and choose **Configure Distribution**\. The Configure Distribution wizard appears\. 

1. Follow the wizard to enter the default values and create the distribution\.<a name="CHAP_Source.SQLServer.CDC.MSCDC.Setup.CDC"></a>

**To set up CDC**

AWS DMS version 3\.4\.7 and greater can set up MS CDC for your database and all of your tables automatically if you aren't using a read\-only replica\. To use this feature, set the `SetUpMsCdcForTables` ECA to true\. For information about ECAs, see [Endpoint settings](#CHAP_Source.SQLServer.ConnectionAttrib)\.

For versions of AWS DMS earlier than 3\.4\.7, or for a read\-only replica as a source, perform the following steps:

1. For tables without primary keys, set up MS\-CDC for the database\. To do so, use an account that has the sysadmin role assigned to it, and run the following command\.

   ```
   use [DBname]
   EXEC sys.sp_cdc_enable_db
   ```

1. Next, set up MS\-CDC for each of the source tables\. For each table with unique keys but no primary key, run the following query to set up MS\-CDC\.

   ```
   exec sys.sp_cdc_enable_table
   @source_schema = N'schema_name',
   @source_name = N'table_name',
   @index_name = N'unique_index_name',
   @role_name = NULL,
   @supports_net_changes = 1
   GO
   ```

1. For each table with no primary key or no unique keys, run the following query to set up MS\-CDC\.

   ```
   exec sys.sp_cdc_enable_table
   @source_schema = N'schema_name',
   @source_name = N'table_name',
   @role_name = NULL
   GO
   ```

For more information on setting up MS\-CDC for specific tables, see the [SQL Server documentation](https://msdn.microsoft.com/en-us/library/cc627369.aspx)\. 

### Setting up ongoing replication without the sysadmin role on self\-managed SQL Server<a name="CHAP_Source.SQLServer.CDC.Publication"></a>

You can set up ongoing replication for a SQL Server database source that doesn't require the user account to have sysadmin privileges\. You still need a user with sysadmin privileges to configure your SQL Server database for ongoing replication\.

**Note**  
You can perform this procedure while the DMS task is running\. If the DMS task is stopped, you can perform this procedure only if there are no transaction log or database backups in progress\. This is because SQL Server requires the SYSADMIN privilege to query the backups for the log sequence number \(LSN\) position\.

Before setting up ongoing replication, see [Prerequisites for using ongoing replication \(CDC\) from a SQL Server source](#CHAP_Source.SQLServer.Prerequisites)\. 

For tables with primary keys, perform the following procedure\. 

**To set up a SQL Server database source for ongoing replication without using the sysadmin role**

1. Create a new SQL Server account with password authentication using SQL Server Management Studio \(SSMS\)\. In this example, we use an account called `dmstest`\.

1. In the **User Mappings** section of SSMS, choose the MSDB and MASTER databases \(which gives public permission\) and assign the DB\_OWNER role for the database you want to use for ongoing replication\.

   ```
   USE [master]
   CREATE LOGIN [dmstest] WITH PASSWORD=N'xxxxxxx';
   CREATE USER [dmstest] FOR LOGIN [dmstest];
   
   USE [msdb];
   CREATE USER [dmstest] FOR LOGIN [dmstest];
   
   USE [DB name used by DMS];
   CREATE USER [dmstest] FOR LOGIN [dmstest];
   ALTER ROLE [db_owner] ADD MEMBER [dmstest];
   ```

1. Open the context \(right\-click\) menu for the new account, choose **Security** and explicitly grant the Connect SQL privilege\.

1. Run the following grant commands\. 

   ```
   GRANT SELECT ON FN_DBLOG TO dmstest;
   GRANT VIEW SERVER STATE TO dmstest;
   use msdb;
   GRANT EXECUTE ON MSDB.DBO.SP_STOP_JOB TO dmstest;
   GRANT EXECUTE ON MSDB.DBO.SP_START_JOB TO dmstest;
   GRANT SELECT ON MSDB.DBO.BACKUPSET TO dmstest;
   GRANT SELECT ON MSDB.DBO.BACKUPMEDIAFAMILY TO dmstest;
   GRANT SELECT ON MSDB.DBO.BACKUPFILE TO dmstest;
   ```

1. In SSMS, open the context \(right\-click\) menu for the **Replication** folder, and then choose **Configure Distribution**\. Follow all default steps and configure this SQL Server instance for distribution\. A distribution database is created under databases\.

1. Create a publication for SQL Server ongoing replication as follows:

   1. Log in to SSMS using the SYSADMIN user account\.

   1. Expand **Replication**\.

   1. Open the context \(right\-click\) menu for **Local Publications**\.

   1.  In the New Publication wizard, choose **Next**\.

   1. Choose the database where you want to create the publication\.

   1.  Choose **Transactional publication**, and then choose **Next**\.

   1.  Expand **Tables** and choose the tables with PK and the tables that you want to publish\. Choose **Next**\.

   1. Choose **Next**, because you don't need to create a filter\.

   1. In the **Snapshot Agent** screen, choose the first option to **Create a snapshot immediately and keep the snapshot available to initialize subscriptions**\. Choose **Next**\.

   1. Choose **Security Settings**, and then choose **Run under the SQL Server Agent service account**\. Make sure to choose **By impersonating the process account** for a publisher connection\. Choose **OK**\.

   1. Choose **Next**\.

   1. Choose **Create the publication**\.

   1. Provide a name of the publication in the format `AR_PUBLICATION_000DBID`\.

      For example, if your `DBID` is less than 10, name the publication `AR_PUBLICATION_0000DBID`> \(4 zeros\)\. If your `DBID` is greater than or equal to 10, name the publication` AR_PUBLICATION_000DBID` \(3 zeros\)\. You can also use the `DB_ID` function in SQL Server\. For more information on the `DB_ID` function, see the [SQL Server documentation](https://docs.microsoft.com/en-us/sql/t-sql/functions/db-id-transact-sql?view=sql-server-2017)\.

1. Create a new AWS DMS task with SQL Server as the source endpoint using the user account that you created\. 

For tables without primary keys, set up MS\-CDC for the database\. To do so, use an account that has the sysadmin role assigned to it, and run the following command\.

```
use [DBname]
EXEC sys.sp_cdc_enable_db
```

Next, set up MS\-CDC for each of the source tables\. For each table with unique keys but no primary key, run the following query to set up MS\-CDC\.

```
exec sys.sp_cdc_enable_table
@source_schema = N'schema_name',
@source_name = N'table_name',
@index_name = N'unique_index_name',
@role_name = NULL,
@supports_net_changes = 1
GO
```

For each table with no primary key or no unique keys, run the following query to set up MS\-CDC\.

```
exec sys.sp_cdc_enable_table
@source_schema = N'schema_name',
@source_name = N'table_name',
@role_name = NULL
GO
```

For more information on setting up MS\-CDC for specific tables, see the [SQL Server documentation](https://msdn.microsoft.com/en-us/library/cc627369.aspx)\.

## Setting up ongoing replication on a cloud SQL Server DB instance<a name="CHAP_Source.SQLServer.Configuration"></a>

This section describes how to set up CDC on a cloud\-hosted SQL Server database instance\. A cloud\-hosted SQL server instance is an instance running on Amazon RDS for SQL Server, an Azure SQL Manged Instance, or any other managed cloud SQL Server instance\. For information about limitations for ongoing replication for each database type, see [Limitations on using SQL Server as a source for AWS DMS](#CHAP_Source.SQLServer.Limitations)\. 

Before setting up ongoing replication, see [Prerequisites for using ongoing replication \(CDC\) from a SQL Server source](#CHAP_Source.SQLServer.Prerequisites)\. 

Unlike self\-managed Microsoft SQL Server sources, Amazon RDS for SQL Server doesn't support MS\-Replication\. Therefore, AWS DMS needs to use MS\-CDC for tables with or without primary keys\.

Amazon RDS doesn't grant sysadmin privileges for setting replication artifacts that AWS DMS uses for ongoing changes in a source SQL Server instance\. Make sure to turn on MS\-CDC for the Amazon RDS instance \(using master user privileges\) as in the following procedure\.

**To turn on MS\-CDC for a cloud SQL Server DB instance**

1. Run one of the following queries at the database level\.

   For an RDS for SQL Server DB instance, use this query\.

   ```
   exec msdb.dbo.rds_cdc_enable_db 'DB_name'
   ```

   For an Azure SQL managed DB instance, use this query\.

   ```
   USE DB_name 
   GO 
   EXEC sys.sp_cdc_enable_db 
   GO
   ```

1. For each table with a primary key, run the following query to turn on MS\-CDC\.

   ```
   exec sys.sp_cdc_enable_table
   @source_schema = N'schema_name',
   @source_name = N'table_name',
   @role_name = NULL,
   @supports_net_changes = 1
   GO
   ```

   For each table with unique keys but no primary key, run the following query to turn on MS\-CDC\.

   ```
   exec sys.sp_cdc_enable_table
   @source_schema = N'schema_name',
   @source_name = N'table_name',
   @index_name = N'unique_index_name',
   @role_name = NULL,
   @supports_net_changes = 1
   GO
   ```

    For each table with no primary key nor unique keys, run the following query to turn on MS\-CDC\.

   ```
   exec sys.sp_cdc_enable_table
   @source_schema = N'schema_name',
   @source_name = N'table_name',
   @role_name = NULL
   GO
   ```

1.  Set the retention period for changes to be available on the source using the following commands\.

   ```
   use dbname
   EXEC sys.sp_cdc_change_job @job_type = 'capture' ,@pollinginterval = 86399
   exec sp_cdc_stop_job 'capture'
   exec sp_cdc_start_job 'capture'
   ```

   The parameter `@pollinginterval` is measured in seconds with a recommended value set to 86399\. This means that the transaction log retains changes for 86,399 seconds \(one day\) when `@pollinginterval = 86399`\. The procedure `exec sp_cdc_start_job 'capture'` initiates the settings\.
**Note**  
With some versions of SQL Server, if the value of `pollinginterval` is set to more than 3599 seconds, the value resets to the default five seconds\. When this happens, T\-Log entries are purged before AWS DMS can read them\. To determine which SQL Server versions are affected by this known issue, see [this Microsoft KB article](https://support.microsoft.com/en-us/topic/kb4459220-fix-incorrect-results-occur-when-you-convert-pollinginterval-parameter-from-seconds-to-hours-in-sys-sp-cdc-scan-in-sql-server-dac8aefe-b60b-7745-f987-582dda2cfa78)\.

   If you are using Amazon RDS with Multi\-AZ, make sure that you also set your secondary to have the right values in case of failover\.

   ```
   exec rdsadmin..rds_set_configuration 'cdc_capture_pollinginterval' , 86399
   ```

If an AWS DMS replication task that captures ongoing changes to your SQL Server source stops for more than one hour, use the following procedure\.

**To maintain the retention period during an AWS DMS replication task**

1. Stop the job truncating the transaction logs by using the following command\. 

   ```
   exec sp_cdc_stop_job 'capture'
   ```

1. Find your task on the AWS DMS console and resume the task\.

1. Choose the **Monitoring** tab, and check the `CDCLatencySource` metric\. 

1. After the `CDCLatencySource` metric equals 0 \(zero\) and stays there, restart the job truncating the transaction logs using the following command\.

   ```
   exec sp_cdc_start_job 'capture'
   ```

Remember to start the job that truncates SQL Server transaction logs\. Otherwise, storage on your SQL Server instance might fill up\.

### Limitations for ongoing replication on a cloud SQL Server DB instance<a name="CHAP_Source.SQLServer.Configuration.Limitations"></a>
+ AWS DMS supports ongoing replication \(CDC\) with the active transaction log only\. You can't use the backup log with CDC\.
+ You may lose events if you move them from the active transaction log to the backup log, or truncate them from the active transaction log\.

## Recommended settings when using Amazon RDS for SQL Server as a source for AWS DMS<a name="CHAP_Source.SQLServer.OptionalSettings"></a>

When you work with Amazon RDS for SQL Server as a source, the capture job relies on the parameters `maxscans` and `maxtrans`\. These parameters govern the maximum number of scans that the capture does on the transaction log and the number of transactions that are processed for each scan\.

For databases, where a number of transactions is greater than `maxtrans*maxscans`, increasing the `polling_interval` value can cause an accumulation of active transaction log records\. In turn, this accumulation can lead to an increase in the size of the transaction log\.

Note that AWS DMS does not rely on the MS\-CDC capture job\. The MS\-CDC capture job marks the transaction log entries as having been processed\. This allows the transaction log backup job to remove the entries from the transaction log\.

We recommend that you monitor the size of the transaction log and the success of the MS\-CDC jobs\. If the MS\-CDC jobs fail, the transaction log could grow excessively and cause AWS DMS replication failures\. You can monitor MS\-CDC capture job errors using the `sys.dm_cdc_errors` dynamic management view in the source database\. You can monitor the transaction log size using the `DBCC SQLPERF(LOGSPACE)` management command\.

**To address the transaction log increase that is caused by MS\-CDC**

1. Check the `Log Space Used %` for the database AWS DMS is replicating from and validate that it increases continuously\.

   ```
   DBCC SQLPERF(LOGSPACE)
   ```

1. Identify what is blocking the transaction log backup process\.

   ```
   Select log_reuse_wait, log_reuse_wait_desc, name from sys.databases where name = db_name();
   ```

   If the `log_reuse_wait_desc` value equals `REPLICATION`, the log backup retention is caused by the latency in MS\-CDC\.

1. Increase the number of events processed by the capture job by increasing the `maxtrans` and `maxscans` parameter values\.

   ```
   EXEC sys.sp_cdc_change_job @job_type = 'capture' ,@maxtrans = 5000, @maxscans = 20 
   exec sp_cdc_stop_job 'capture'
   exec sp_cdc_start_job 'capture'
   ```

To address this issue, set the values of `maxscans` and `maxtrans` so that `maxtrans*maxscans` is equal to the average number of events generated for tables that AWS DMS replicates from the source database for each day\.

If you set these parameters higher than the recommended value, the capture jobs process all events in the transaction logs\. If you set these parameters below the recommended value, MS\-CDC latency increases and your transaction log grows\.

Identifying appropriate values for `maxscans` and `maxtrans` can be difficult because changes in workload produce varying number of events\. In this case, we recommend that you set up monitoring on MS\-CDC latency\. For more information, see [ Monitor the process](https://docs.microsoft.com/en-us/sql/relational-databases/track-changes/administer-and-monitor-change-data-capture-sql-server?view=sql-server-ver15#Monitor) in SQL Server documentation\. Then configure `maxtrans` and `maxscans` dynamically based on the monitoring results\.

If the AWS DMS task is unable to find the log sequence numbers \(LSNs\) needed to resume or continue the task, the task may fail and require a complete reload\.

## Supported compression methods for SQL Server<a name="CHAP_Source.SQLServer.Compression"></a>

The following table shows the compression methods that AWS DMS supports for each SQL Server version\. 


| SQL Server version  |  Row/Page compression \(at partition level\)  |  Vardecimal storage format | 
| --- | --- | --- | 
| 2005 | No | No | 
| 2008 | Yes | No | 
| 2012 | Yes | No | 
| 2014 | Yes | No | 

**Note**  
Sparse columns and columnar structure compression aren't supported\.

## Working with self\-managed SQL Server AlwaysOn availability groups<a name="CHAP_Source.SQLServer.AlwaysOn"></a>

SQL Server Always On availability groups provide high availability and disaster recovery as an enterprise\-level alternative to database mirroring\.   

In AWS DMS, you can migrate changes from a single primary or secondary availability group replica\.

### Working with the primary availability group replica<a name="CHAP_Source.SQLServer.AlwaysOn.Primary"></a>

 

**To use the primary availability group as a source in AWS DMS, do the following:**

1. Turn on the distribution option for all SQL Server instances in your availability replicas\. For more information, see [Setting up ongoing replication using the sysadmin role with self\-managed SQL Server](#CHAP_Source.SQLServer.CDC.MSCDC)\.

1. In the AWS DMS console, open the SQL Server source database settings\. For **Server Name**, specify the Domain Name Service \(DNS\) name or IP address that was configured for your availability group listener\. 

When you start an AWS DMS task for the first time, it might take longer than usual to start\. This slowness occurs because the creation of the table articles is being duplicated by the availability group server\. 

### Working with a secondary availability group replica<a name="CHAP_Source.SQLServer.AlwaysOn.Secondary"></a>

**To use a secondary availability group as a source in AWS DMS, do the following:**

1. Use the same credentials for connecting to individual replicas as those used by the AWS DMS source endpoint user\.

1. Ensure that your AWS DMS replication instance can resolve DNS names for all existing replicas, and connect to them\. You can use the following SQL query to get DNS names for all of your replicas\.

   ```
   select ar.replica_server_name, ar.endpoint_url from sys.availability_replicas ar
   JOIN sys.availability_databases_cluster adc
   ON adc.group_id = ar.group_id AND adc.database_name = '<source_database_name>';
   ```

1. When you create the source endpoint, specify the DNS name of the availability group listener for the endpoint's **Server name** or for the endpoint secret's **Server address**\. For more information about availability group listeners, see [What is an availability group listener?](https://docs.microsoft.com/en-us/sql/database-engine/availability-groups/windows/availability-group-listener-overview?view=sql-server-ver15) in the SQL Server documentation\.

   You can use either a public DNS server or an on\-premises DNS server to resolve the availability group listener, the primary replica, and the secondary replicas\. To use an on\-premises DNS server, configure the Amazon Route 53 Resolver\. For more information, see [ Using your own on\-premises name server](CHAP_BestPractices.md#CHAP_BestPractices.Rte53DNSResolver)\.

1. Add the following extra connection attributes to your source endpoint\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Source.SQLServer.html)

1. Enable the distribution option on all replicas in your availability group\. Add all nodes to the distributors list\. For more information, see [To set up distribution](#CHAP_Source.SQLServer.CDC.MSCDC.Setup)\.

1. Run the following query on the primary read\-write replica to enable publication of your database\. You run this query only once for your database\. 

   ```
   sp_replicationdboption @dbname = N'<source DB name>', @optname = N'publish', @value = N'true';
   ```



#### Limitations<a name="CHAP_Source.SQLServer.AlwaysOn.Secondary.limitations"></a>

Following are limitations for working with a secondary availability group replica:
+ AWS DMS doesn't support Safeguard when using a read\-only availability group replica as a source\. For more information, see [Endpoint settings when using SQL Server as a source for AWS DMS](#CHAP_Source.SQLServer.ConnectionAttrib)\.
+ AWS DMS doesn't support the `setUpMsCdcForTables` extra connection attribute when using a read\-only availability group replica as a source\. For more information, see [Endpoint settings when using SQL Server as a source for AWS DMS](#CHAP_Source.SQLServer.ConnectionAttrib)\.
+ AWS DMS can use a self\-managed secondary availability group replica as a source database for ongoing replication \(change data capture, or CDC\) starting from version 3\.4\.7\. Cloud SQL Server Multi\-AZ read replicas are not supported\. If you use previous versions of AWS DMS, make sure that you use the primary availability group replica as a source database for CDC\.

#### Failover to other nodes<a name="CHAP_Source.SQLServer.AlwaysOn.Secondary.failover"></a>

If you set the `ApplicationIntent` extra connection attribute for your endpoint to `ReadOnly`, your AWS DMS task connects to the read\-only node with the highest read\-only routing priority\. It then fails over to other read\-only nodes in your availability group when the highest priority read\-only node is unavailable\. If you don't set `ApplicationIntent`, your AWS DMS task only connects to the primary \(read/write\) node in your availability group\.

## Endpoint settings when using SQL Server as a source for AWS DMS<a name="CHAP_Source.SQLServer.ConnectionAttrib"></a>

You can use endpoint settings to configure your SQL Server source database similar to using extra connection attributes\. You specify the settings when you create the source endpoint using the AWS DMS console, or by using the `create-endpoint` command in the [AWS CLI](https://docs.aws.amazon.com/cli/latest/reference/dms/index.html), with the `--microsoft-sql-server-settings '{"EndpointSetting": "value", ...}'` JSON syntax\.

The following table shows the endpoint settings that you can use with SQL Server as a source\.


| Name | Description | 
| --- | --- | 
| AlwaysOnSharedSynchedBackupIsEnabled |  This attribute adjusts the behavior of AWS DMS when migrating from an SQL Server source database that is hosted as part of an Always On availability group cluster\.  AWS DMS has enhanced support for SQL Server source databases that are configured to run in an Always On cluster\. In this case, AWS DMS attempts to track if transaction backups are happening from nodes in the Always On cluster other than the node where the source database instance is hosted\. At migration task start\-up, AWS DMS tries to connect to each node in the cluster, but fails if it can't connect to any one of the nodes\.  If you need AWS DMS to poll all the nodes in the Always On cluster for transaction backups, set this attribute to `false`\. Default value: `true` Valid values: `true` or `false` Example: `'{"AlwaysOnSharedSynchedBackupIsEnabled": false}'`  | 
|  `ActivateSafeguard`  |  This attribute turns Safeguard on or off\. For information about Safeguard, see `SafeguardPolicy` following\. Default value: `true`  Valid values: \{`false`, `true`\}  Example: `'{"ActivateSafeguard": true}'`  | 
|  `SafeguardPolicy`  |  For optimal performance, AWS DMS tries to capture all unread changes from the active transaction log \(TLOG\)\. However, sometimes due to truncation, the active TLOG might not contain all of the unread changes\. When this occurs, AWS DMS accesses the backup log to capture the missing changes\. To minimize the need to access the backup log, AWS DMS prevents truncation using one of the following methods:  1\. **Start transactions in the database:** This is the default method\. When this method is used, AWS DMS prevents TLOG truncation by mimicking a transaction in the database\. As long as such a transaction is open, changes that appear after the transaction started aren't truncated\. If you need Microsoft Replication to be enabled in your database, then you must choose this method\.  The purpose of the `EXCLUSIVE_AUTOMATIC_TRUNCATION` setting is to prevent truncation of unread changes from the transaction log\. When you choose this option, AWS DMS creates a table in the source database called `awsdms_truncation_safeguard`\. This table prevents the truncation of the transaction log by mimicking a transaction in the database\. It prevents the events being truncated and moved to the backup log for five minutes \(by default\)\. AWS DMS can then read all of the events from the transaction log without needing to read events from the backup log\. You can safely delete the `awsdms_truncation_safeguard` table if there are no tasks with `SafeguardPolicy` set to "Start transactions in the database"\. 2\.** Exclusively use sp\_repldone within a single task:** When this method is used, AWS DMS reads the changes and then uses sp\_repldone to mark the TLOG transactions as ready for truncation\. Although this method doesn't involve any transactional activities, it can only be used when Microsoft Replication isn't running\. Also, when using this method, only one AWS DMS task can access the database at any given time\. Therefore, if you need to run parallel AWS DMS tasks against the same database, use the default method\.  Default value: `RELY_ON_SQL_SERVER_REPLICATION_AGENT`  Valid values: \{`EXCLUSIVE_AUTOMATIC_TRUNCATION`, `RELY_ON_SQL_SERVER_REPLICATION_AGENT`\}  Example: `'{"SafeguardPolicy": "RELY_ON_SQL_SERVER_REPLICATION_AGENT"}'` **Notes:** [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Source.SQLServer.html)  | 
| `SetUpMsCdcForTables` | This attribute turns on MS\-CDC for the source database and for tables in the task mapping that don't have MS\-Replication enabled\. Setting this value to `true` runs the `sp_cdc_enable_db` stored procedure on the source database, and runs the `sp_cdc_enable_table` stored procedure on each table in the task that doesn't have MS\-Replication enabled in the source database\. For more information about turning on distribution, see [Setting up ongoing replication using the sysadmin role with self\-managed SQL Server](#CHAP_Source.SQLServer.CDC.MSCDC)\. Valid values: \{`true`, `false`\}  Example: `'{"SetUpMsCdcForTables": true}'`  | 
| `ReadBackupOnly` | Use of this attribute requires **sysadmin** privileges\. When this attribute is set to `Y`, during ongoing replication AWS DMS reads changes only from transaction log backups and doesn't read from the active transaction log file\. Setting this parameter to `Y` enables you to control active transaction log file growth during full load and ongoing replication tasks\. However, it can add some source latency to ongoing replication\.  Valid values: `N` or `Y`\. The default is `N`\.  Example: `'{"ReadBackupOnly": Y}'` **Note:** This parameter doesn't work on Amazon RDS SQL Server source instances because of the way RDS performs backups\.  | 
| `Use3rdPartyBackupDevice` | When this attribute is set to `Y`, AWS DMS processes third\-party transaction log backups if they are created in native format\. | 
| `"MultiSubnetFailover": "Yes"` | This ODBC driver attribute helps DMS to connect to the new primary in case of an Availability Group failover\. This attribute is designed for situations when the connection is broken or the listener IP address is incorrect\. In these situations, AWS DMS attempts to connect to all IP addresses associated with the Availability Group listener\. | 
| `"ApplicationIntent": "readonly"` | This ODBC driver attribute setting causes SQL Server to route your replication task to the highest priority read\-only node\. Without this setting, SQL Server routes your replication task to the primary read\-write node\. | 
| `FatalOnSimpleModel` | When set to `true`, this setting generates a fatal error when SQL Server database recovery model is set to `simple`\. Default value: `false` Valid values: `true` or `false` Example: `'{"FatalOnSimpleModel": true}'`  | 
|  `TlogAccessMode`  |  Indicates the mode used to fetch CDC data\. Default value: `PreferTlog`  Valid values: `BackupOnly`, `PreferBackup`, `PreferTlog`, `TlogOnly` Example: `'{"TlogAccessMode": "PreferTlog"}'`  | 
|  `ForceLobLookup`  |  Forces LOB lookup on inline LOB\. Default value: `false`  Valid values: `true`, `false` Example: `'{"ForceLobLookup": false}'`  | 

## Source data types for SQL Server<a name="CHAP_Source.SQLServer.DataTypes"></a>

Data migration that uses SQL Server as a source for AWS DMS supports most SQL Server data types\. The following table shows the SQL Server source data types that are supported when using AWS DMS and the default mapping from AWS DMS data types\.

For information on how to view the data type that is mapped in the target, see the section for the target endpoint you are using\.

For additional information about AWS DMS data types, see [Data types for AWS Database Migration Service](CHAP_Reference.DataTypes.md)\.


|  SQL Server data types  |  AWS DMS data types  | 
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
|  NVARCHAR \(max\)  |  NCLOB NTEXT To use this data type with AWS DMS, you must enable the use of SupportLobs for a specific task\. For more information about enabling Lob support, see [Setting LOB support for source databases in an AWS DMS task](CHAP_Tasks.LOBSupport.md)\.  For SQL Server tables, AWS DMS updates LOB columns in the target even for UPDATE statements that don't change the value of the LOB column in SQL Server\. During CDC, AWS DMS supports CLOB data types only in tables that include a primary key\.  | 
|  BINARY  |  BYTES  | 
|  VARBINARY  |  BYTES  | 
|  VARBINARY \(max\)  |  BLOB IMAGE For SQL Server tables, AWS DMS updates LOB columns in the target even for UPDATE statements that don't change the value of the LOB column in SQL Server\. To use this data type with AWS DMS, you must enable the use of BLOB data types for a specific task\. AWS DMS supports BLOB data types only in tables that include a primary key\.  | 
|  TIMESTAMP  |  BYTES  | 
|  UNIQUEIDENTIFIER  |  STRING  | 
|  HIERARCHYID   |  Use HIERARCHYID when replicating to a SQL Server target endpoint\. Use WSTRING \(250\) when replicating to all other target endpoints\.  | 
|  XML  |  NCLOB For SQL Server tables, AWS DMS updates LOB columns in the target even for UPDATE statements that don't change the value of the LOB column in SQL Server\. To use this data type with AWS DMS, you must enable the use of NCLOB data types for a specific task\. During CDC, AWS DMS supports NCLOB data types only in tables that include a primary key\.  | 
|  GEOMETRY  |  Use GEOMETRY when replicating to target endpoints that support this data type\. Use CLOB when replicating to target endpoints that don't support this data type\.  | 
|  GEOGRAPHY  |  Use GEOGRAPHY when replicating to target endpoints that support this data type\. Use CLOB when replicating to target endpoints that don't support this data type\.  | 

AWS DMS doesn't support tables that include fields with the following data types\. 
+ CURSOR
+ SQL\_VARIANT
+ TABLE

**Note**  
User\-defined data types are supported according to their base type\. For example, a user\-defined data type based on DATETIME is handled as a DATETIME data type\.