# Using a Microsoft SQL Server Database as a Source for AWS DMS<a name="CHAP_Source.SQLServer"></a>

You can migrate data from one or many Microsoft SQL Server databases using AWS DMS \(AWS DMS\)\. With a SQL Server database as a source, you can migrate data to either another SQL Server database or one of the other supported databases\.

AWS DMS supports, as a source, on\-premises and Amazon EC2 instance databases for Microsoft SQL Server versions 2005, 2008, 2008R2, 2012, 2014, and 2016\. The Enterprise, Standard, Workgroup, and Developer editions are supported\. The Web and Express editions are not supported\.

AWS DMS supports, as a source, Amazon RDS DB instance databases for SQL Server versions 2008R2, 2012, and 2014, and 2016\. The Enterprise and Standard editions are supported\. Change data capture \(CDC\) is not supported for source databases on Amazon RDS\. The Web, Workgroup, Developer, and Express editions are not supported\.

You can have the source SQL Server database installed on any computer in your network\. A SQL Server account with the appropriate access privileges to the source database for the type of task you chose is also required for use with AWS DMS\.

You can use SSL to encrypt connections between your SQL Server endpoint and the replication instance\. For more information on using SSL with a SQL Server endpoint, see [Using SSL With AWS Database Migration Service](CHAP_Security.SSL.md)\.

To capture changes from a source SQL Server database, the database must be configured for full backups and must be either the Enterprise, Developer, or Standard Edition\. For a list of requirements and limitations when using CDC with SQL Server, see [Special Limitations When Capturing Data Changes \(CDC\) from a SQL Server Source](#CHAP_Source.SQLServer.CDCLimitations)\.

For additional details on working with SQL Server source databases and AWS DMS, see the following\.


+ [General Limitations on Using SQL Server as a Source for AWS DMS](#CHAP_Source.SQLServer.Limitations)
+ [Special Limitations When Capturing Data Changes \(CDC\) from a SQL Server Source](#CHAP_Source.SQLServer.CDCLimitations)
+ [Supported Compression Methods](#CHAP_Source.SQLServer.Compression)
+ [Working with Microsoft SQL Server AlwaysOn Availability Groups](#CHAP_Source.SQLServer.AlwaysOn)
+ [Configuring a Microsoft SQL Server Database as a Replication Source for AWS DMS](#CHAP_Source.SQLServer.Configuration)
+ [Using MS\-Replication to Capture Data Changes in Microsoft SQL Server](#CHAP_Source.SQLServer.MSREPLICATION)
+ [Using MS\-CDC to Capture Data Changes in Microsoft SQL Server](#CHAP_Source.SQLServer.MSCDC)
+ [Capturing Changes If You Can't Use MS\-Replication or MS\-CDC](#CHAP_Source.SQLServer.CDCDML)
+ [Extra Connection Attributes When Using SQL Server as a Source for AWS DMS](#CHAP_Source.SQLServer.ConnectionAttrib)

## General Limitations on Using SQL Server as a Source for AWS DMS<a name="CHAP_Source.SQLServer.Limitations"></a>

The following limitations apply when using a SQL Server database as a source for AWS DMS:

+ The identity property for a column is not migrated to a target database column\.

+  Changes to rows with more than 8000 bytes of information, including header and mapping information, are not processed correctly due to limitations in the SQL Server TLOG buffer size\.

+ The Microsoft SQL Server endpoint does not support the use of sparse tables\.

+ Windows Authentication is not supported\.

+ Changes to computed fields in a Microsoft SQL Server are not replicated\.

+ Microsoft SQL Server partition switching is not supported\.

+ A clustered index on the source is created as a nonclustered index on the target\.

+ When using the WRITETEXT and UPDATETEXT utilities, AWS DMS does not capture events applied on the source database\.

+ The following data manipulation language \(DML\) pattern is not supported: 

  ```
  SELECT <*> INTO <new_table> FROM <existing_table>
  ```

+ When using Microsoft SQL Server as a source, column\-level encryption is not supported\.

+ Due to a known issue with Microsoft SQL Server 2008 and Microsoft SQL Server 2008 R2, AWS DMS doesn't support server level audits on Microsoft SQL Server 2008 and Microsoft SQL Server 2008 R2 as a source endpoint\.

  For example, running the following command causes AWS DMS to fail:

  ```
  USE [master]
  GO 
  ALTER SERVER AUDIT [my_audit_test-20140710] WITH (STATE=on)
  GO
  ```

## Special Limitations When Capturing Data Changes \(CDC\) from a SQL Server Source<a name="CHAP_Source.SQLServer.CDCLimitations"></a>

The following limitations apply specifically when trying to capture changes from a SQL Server database as a source for AWS DMS:

+ You must use either the Enterprise, Standard, or Developer Edition\.

+ SQL Server must be configured for full backups and a backup must be made before beginning to replicate data\.

+ Recovery Model must be set to **Bulk logged** or **Full**\.

+ Microsoft SQL Server backup to multiple disks is not supported\. If the backup is defined to write the database backup to multiple files over different disks, AWS DMS can't read the data and the AWS DMS task fails\.

+ Microsoft SQL Server Replication Publisher definitions for the source database used in a DMS CDC task are not removed when you remove a task\. A Microsoft SQL Server system administrator must delete these definitions from Microsoft SQL Server\.

+ During CDC, AWS DMS needs to look up SQL Server transaction log backups to read changes\. AWS DMS does not support using SQL Server transaction log backups that were created using third party backup software\.

+ SQL Server doesn't capture changes on newly created tables until they've been published\. When tables are added to a SQL Server source, AWS DMS manages the creation of the publication\. However, this process might take several minutes\. Operations made to newly created tables during this delay aren't captured or replicated to the target\.

+ The AWS DMS user account must have the **sysAdmin** fixed server role on the SQL Server database you are connecting to\.

+ AWS DMS change data capture requires FULLOGGING to be turned on in SQL Server; the only way to turn on FULLLOGGING in SQL Server is to either enable MS\-REPLICATION or CHANGE DATA CAPTURE \(CDC\)\.

+ The SQL Server *tlog* cannot be reused until the changes have been processed\.

+ CDC operations are not supported on memory optimized tables\. This limitation applies to SQL Server 2014 \(when the feature was first introduced\) and later\.

## Supported Compression Methods<a name="CHAP_Source.SQLServer.Compression"></a>

The following table shows the compression methods AWS DMS supports for each Microsoft SQL Server version\. 


| Microsoft SQL Server Version  |  Row/Page Compression \(at Partition Level\)  |  Vardecimal Storage Format | 
| --- | --- | --- | 
| 2005 | No | No | 
| 2008 | Yes | No | 
| 2012 | Yes | No | 
| 2014 | Yes | No | 

**Note**  
Sparse columns and columnar structure compression are not supported\.

## Working with Microsoft SQL Server AlwaysOn Availability Groups<a name="CHAP_Source.SQLServer.AlwaysOn"></a>

The Microsoft SQL Server AlwaysOn Availability Groups feature is a high\-availability and disaster\-recovery solution that provides an enterprise\-level alternative to database mirroring\. 

To use AlwaysOn Availability Groups as a source in AWS DMS, do the following:

+ Enable the Distribution option on all Microsoft SQL Server instances in your Availability Replicas\.

+ In the AWS DMS console, open the Microsoft SQL Server source database settings\. For **Server Name**, specify the Domain Name Service \(DNS\) name or IP address that was configured for the Availability Group Listener\. 

When you start an AWS DMS task for the first time, it might take longer than usual to start because the creation of the table articles is being duplicated by the Availability Groups Server\. 

## Configuring a Microsoft SQL Server Database as a Replication Source for AWS DMS<a name="CHAP_Source.SQLServer.Configuration"></a>

You can configure a Microsoft SQL Server database as a replication source for AWS DMS \(AWS DMS\)\. For the most complete replication of changes, we recommend that you use the Enterprise, Standard, or Developer edition of Microsoft SQL Server\. One of these versions is required because these are the only versions that include MS\-Replication\(EE,SE\) and MS\-CDC\(EE,DEV\)\. The source SQL Server must also be configured for full backups\. In addition, AWS DMS must connect with a user \(a SQL Server instance login\) that has the **sysAdmin** fixed server role on the SQL Server database you are connecting to\. 

Following, you can find information about configuring SQL Server as a replication source for AWS DMS\.

## Using MS\-Replication to Capture Data Changes in Microsoft SQL Server<a name="CHAP_Source.SQLServer.MSREPLICATION"></a>

 To use MS\-REPLICATION to replicate changes, each source table must have a primary key\. If a source table doesn't have a primary key, you can use MS\-CDC for capturing changes\. If you haven't previously enabled MS\-REPLICATION, you must enable your SQL Server database to use MS\-REPLICATION\. To enable MS\-REPLICATION, take the steps following or see the [Microsoft SQL Server documentation](https://msdn.microsoft.com/en-us/library/ms151772.aspx)\. Setting up MS\_REPLICATION adds a new SYSTEM database called **Distribution** to your source SQL Server database\. 

**To open the Configure Distribution wizard from Microsoft SQL Server**

1. In Microsoft SQL Server Management Studio, open the context \(right\-click\) menu for the **Replication** folder, and then choose **Configure Distribution**\.

1. In the **Distributor** step, choose **<Microsoft SQL Server Name> will act as its own distributor**\. SQL Server creates a distribution database and log\.

## Using MS\-CDC to Capture Data Changes in Microsoft SQL Server<a name="CHAP_Source.SQLServer.MSCDC"></a>

If you need to replicate tables that don't have a primary key, the **Use MS\-CDC** and **Do Not Use MS\-Replication or MS\-CDC** options are available, as described following\.

**Important**  
Replicating tables that don't have a primary key or a unique index can adversely affect performance, because additional database resources are required to capture the changes\. However, you can prevent performance issues related to the absence of primary keys or a unique index by manually adding indexes to the target tables\.

**Note**  
SQL Server might not delete log entries\. Log entries are not reused unless they are replicated and backed up\.

### Setting Up MS\-CDC<a name="CHAP_Source.SQLServer.MSCDC.SettingUp"></a>

To set up MS\-CDC, you first need to enable MS\-CDC for the database by running the following command\.

```
use [DBname]
EXEC sys.sp_cdc_enable_db
```

Next, you need to enable MS\-CDC for each of the source tables by running the following command\.

```
EXECUTE sys.sp_cdc_enable_table @source_schema = N'MySchema', @source_name =
N'MyTable', @role_name = NULL;
```

For more information on setting up MS\-CDC for specific tables, see the [Microsoft SQL Server documentation](https://msdn.microsoft.com/en-us/library/cc627369.aspx)\. 

## Capturing Changes If You Can't Use MS\-Replication or MS\-CDC<a name="CHAP_Source.SQLServer.CDCDML"></a>

If your database is not set up for MS\-REPLICATION or MS\-CDC, and it is listed as a supported configuration for CDC as discussed at the beginning of this section, you can still capture some changes to tables\. However, such a setup only captures INSERT and DELETE data manipulation language \(DML\) events\. UPDATE and TRUNCATE TABLE events are ignored\. Depending on what operations occur, this setup can result in the target database no longer being consistent with the source\.

Also in this setup, a DELETE event isn't applied to a row that has previously been modified on the source but not on the target \(a row where a previous UPDATE event that was ignored\)\. 

## Extra Connection Attributes When Using SQL Server as a Source for AWS DMS<a name="CHAP_Source.SQLServer.ConnectionAttrib"></a>

You can use extra connection attributes to configure your SQL Server source\. You specify these settings when you create the source endpoint\.

The following table shows the extra connection attributes you can use with SQL Server as a source:


| Name | Description | 
| --- | --- | 
| `safeguardPolicy` | For optimal performance, AWS DMS tries to capture all unread changes from the active transaction log \(TLOG\)\. However, sometimes due to truncation, the active TLOG may not contain all of the unread changes\. When this occurs, AWS DMS accesses the backup log to capture the missing changes\. To minimize the need to access the backup log, AWS DMS prevents truncation using one of the following methods:  1\. **Start transactions in the database:** This is the default method\. When this method is used, AWS DMS prevents TLOG truncation by mimicking a transaction in the database\. As long as such a transaction is open, changes that appear after the transaction started will not be truncated\. If you need Microsoft Replication to be enabled in your database, then you must choose this method\.  2\.** Exclusively use sp\_repldone within a single task:** When this method is used, AWS DMS reads the changes and then uses sp\_repldone to mark the TLOG transactions as ready for truncation\. Although this method does not involve any transactional activities, it can only be used when Microsoft Replication is not running\. Also, when using this method, only one AWS DMS task can access the database at any given time\. Therefore, if you need to run parallel AWS DMS tasks against the same database, use the default method\.  Default value: `RELY_ON_SQL_SERVER_REPLICATION_AGENT`  Valid values: \{`EXCLUSIVE_AUTOMATIC_TRUNCATION`, `RELY_ON_SQL_SERVER_REPLICATION_AGENT`\}  Example: `safeguardPolicy= RELY_ON_SQL_SERVER_REPLICATION_AGENT ` | 