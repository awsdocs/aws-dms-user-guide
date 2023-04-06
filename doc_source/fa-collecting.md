# Collecting data for AWS DMS Fleet Advisor<a name="fa-collecting"></a>

To start collecting data, select the objects on the **Monitored objects** page, and choose **Run data collection**\. DMS data collector can collect from up to 100 databases at one time\. Also, DMS data collector can use up to eight parallel threads to connect to databases in your environment\. From these eight threads, DMS data collector can use up to five parallel threads to connect to a single database instance\.

**Important**  
Before starting to collect data, view the **Software check** section on the DMS data collector home page\. Verify that all database engines that you want to monitor have the **Passed** status\. If some database engines have the **Failed** status, and you have database servers with corresponding engines in your monitored objects list, fix the issue before proceeding\. You can find tips next to the **Failed** status listed in the **Software check** section\.

DMS data collector can work in two modes: single run or ongoing monitoring\. After you start data collection, the **Run data collection** dialog box opens\. Next, choose one of the two following options\.

**Metadata and database capacity**  
DMS data collector collects information from the database or OS servers\. It includes schemas, versions, editions, CPU, memory, and disk capacity\. You can compute target recommendations in DMS Fleet Advisor based on this information\. If the source database is over\- or underprovisioned, then the target recommendations also will be over\- or underprovisioned\.  
This is the default option\.

**Metadata, database capacity, and resource utilization**  
In addition to metadata and database capacity information, DMS data collector collects actual utilization metrics of CPU, memory, and disk capacity for the databases or OS servers\. Target recommendations provided will be more accurate because they are based on the actual database workloads\.  
If you choose this option, then you set the period of data collection\. You can collect data during the **Next 7 days** or set the **Custom range** of 1–60 days\.

After data collection begins, you're redirected to the **Data collection** page, where you can see how the collection queries run and monitor the live progress\. Here, you can view overall collection health or on the DMS data collector home page\. If overall data collection health is less than 100 percent, you might need to fix issues related to the collection\.

If you run the DMS data collector in the **Metadata and database capacity** mode, then you can see the number of completed queries on the **Data collection** page\.

If you run the DMS data collector in the **Metadata, database capacity, and resource utilization** mode, then you can see the remaining time before your DMS data collector completes the monitoring\.

On the **Data collection** page, you can see the collection status for each object\. If something isn't working properly, a message appears showing how many issues occurred\. To help determine a fix to an issue, you can check details\. The following tabs list potential issues: 
+ **Summary by query** – Shows status of tests like the Ping test\. You can filter results in the **Status** column\. The **Status** column provides a message indicating how many failures occurred during data collection\.
+ **Summary by a monitored object** – Shows overall status per object\.
+ **Summary by query type** – Shows status for type of collector query, such as SQL, Secure Shell \(SSH\), or Windows Management Instrumentation \(WMI\) calls\.
+ **Summary by issue** – Shows all unique issues that occurred, with issue names and the number of times that each issue occurs\.

![\[Data collection page\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-dmsstudio-summary2.png)

To export the collection results, choose **Export to CSV**\.

After identifying issues and resolving them, choose **Start collection** and rerun the data collection process\. After performing data collection, the data collector uses secure connections to upload collected data to a DMS Fleet Advisor inventory\. DMS Fleet Advisor stores information in your Amazon S3 bucket\. For information about configuring credentials for data forwarding, see [Configuring credentials for data forwarding](fa-data-collectors-install.md#fa-data-collectors-configure)\.

## Collecting capacity and resource utilization metrics with AWS DMS Fleet Advisor<a name="fa-data-collectors-metrics"></a>

You can collect metadata and performance metrics in two modes: single run or ongoing monitoring\. Depending on the option that you select, your DMS data collector tracks different metrics in your data environment\. During a single run, your DMS data collector tracks only metadata metrics from your database and OS servers\. During ongoing monitoring, your DMS data collector tracks the actual utilization of your resources\.

AWS DMS gathers the following metrics during a single run of your DMS data collector\.
+ Available memory on your OS servers
+ Available storage on your OS servers
+ Database version and edition
+ Number of CPUs on your OS servers
+ Number of schemas
+ Number of stored procedures
+ Number of tables
+ Number of triggers
+ Number of views
+ Schema structure

DMS Fleet Advisor uses these metrics to build an inventory of your database and OS servers\. Also, DMS Fleet Advisor uses these metrics to analyze your source database schemas\.

DMS Fleet Advisor can generate target recommendations using only these metrics\. In this case, for your overprovisioned source databases, the target recommendation is also overprovisioned\. Thus, you spend additional costs on the maintenance of your resources in the AWS Cloud\. For underprovisioned source databases, the target recommendation is also underprovisioned, which might lead to performance issues\.

After you launch the schema analysis, AWS DMS requests the following metrics from your DMS data collector\.
+ Database support dates
+ Number of lines of code
+ Schema complexity
+ Similarity of schemas

AWS DMS gathers the following metrics during ongoing monitoring\. You can run your DMS data collector for a period of 1 to 60 days\.
+ I/O throughput on your database servers
+ Input/output operations per second \(IOPS\) on your database servers
+ Number of CPUs that your OS servers use
+ Memory usage on your OS servers
+ Storage usage on your OS servers

DMS Fleet Advisor uses these metrics to generate accurate target recommendations, so your target databases meet your performance needs\. Also, you won't spend additional costs on the maintenance of your resources in the AWS Cloud\.

## How does the AWS DMS Fleet Advisor collect capacity and resource utilization metrics?<a name="fa-data-collectors-how-it-works"></a>

DMS Fleet Advisor collects performance metrics every minute\.

For Oracle and SQL Server, DMS Fleet Advisor runs SQL queries to capture values for each database metric\.

For MySQL and PostgreSQL, DMS Fleet Advisor collects performance metrics from the OS server where your database runs\. In Windows, DMS Fleet Advisor runs WMI Query Language \(WQL\) scripts and receives WMI data\. In Linux, DMS Fleet Advisor runs commands that capture the OS server metrics\.

**Important**  
Running remote SQL scripts can impact the performance of your production databases\. However, the data collection queries don't contain any calculation logic\. Thus, the data collection process isn't likely to use more than 1 percent of your database resources\.

You can view all queries that the data collector runs to collect metrics\. To do so, open the `DMSCollector.Collections.json` file\. You can find this file in the `etc` folder located in the same folder where you installed the data collector\. The default path is `C:\ProgramData\Amazon\AWS DMS Collector\etc\DMSCollector.Collections.json`\.

The DMS data collector uses the local file system as the temporary storage for all collected data\. The DMS data collector stores the collected data in JSON format\. You can use the local collector in an offline mode and manually check or verify the collected files before you configure data forwarding\. You can see all collected files in the `out` folder located in the same folder where you installed the DMS data collector\. The default path is `C:\ProgramData\Amazon\AWS DMS Collector\out`\.

**Important**  
If you run your DMS data collector in an offline mode and store the collected data on your server for more that 14 days, then you can't use Amazon CloudWatch to display these metrics\. However, DMS Fleet Advisor still uses this data to generate recommendations\. For more information about CloudWatch charts, see [Recommendation details](fa-recommendations-view.md)\.

You can also check or verify the collected data files in an online mode\. The DMS data collector forwards all data to the Amazon S3 bucket that you specified in the DMS data collector settings\.

You can use your DMS data collector to collect data from on\-premises databases\. Also, you can collect data from Amazon RDS and Aurora databases\. However, you can't successfully run all DMS data collector queries in the cloud because of the differences between Amazon RDS or Aurora and on\-premises DB instances\. Because the DMS data collector gathers utilization metrics for MySQL and PostgreSQL databases from the host OS, this approach won't work with Amazon RDS and Aurora\.

## Collecting database features with AWS DMS Fleet Advisor<a name="fa-data-collectors-database-features"></a>

You can use the DMS data collector to discover database features that your target engine doesn't support\. To choose the right migration target, you should consider these limitations\.

Depending on your target engine, the DMS data collector discovers specific source database features\. Then, DMS Fleet Advisor provides additional information about the limitation, and includes recommended actions that you should take to address or avoid this limitation\. Also, DMS Fleet Advisor calculates the impact of these limitations\.

The following table includes MySQL database features that Amazon RDS for MySQL doesn't support\.


| Limitation | Description | Impact | 
| --- | --- | --- | 
| Authentication plugins | Amazon RDS doesn't support MySQL authentication plugins\. | Low | 
| Error logging to the system log | Amazon RDS doesn't support writing the error log to the system log\. | Low | 
| Global transaction identifiers | You can use global transaction identifiers with all RDS for MySQL 5\.7 versions, and RDS for MySQL version 8\.0\.26 and higher MySQL 8\.0 versions\. | Low | 
| Group Replication | Amazon RDS doesn't support the MySQL Group Replication plugin\. | Low | 
| InnoDB tablespace encryption | Amazon RDS doesn't support the InnoDB tablespace encryption\. | Low | 
| InnoDB reserved word | InnoDB is a reserved word for Amazon RDS for MySQL\. You can't use this name for a MySQL database\.  | Low | 
| Keyring plugin | Amazon RDS doesn't support the MySQL keyring plugin\. | Low | 
| Password validation plugin | Amazon RDS doesn't support the MySQL `validate_password` plugin\. | Low | 
| Persisted system variables | Amazon RDS doesn't support MySQL persisted system variables\. | Low | 
| Restricted access | Amazon RDS restricts access to certain system procedures and tables that require advanced permissions\. Also, Amazon RDS doesn't allow direct host access to a DB instance by using Telnet, Secure Shell \(SSH\), or Windows Remote Desktop Connection\. | Low | 
| Rewriter query rewrite plugin | Amazon RDS doesn't support the MySQL Rewriter query rewrite plugin\. | Low | 
| Semisynchronous replication | Amazon RDS doesn't support the MySQL semisynchronous replication\. | Low | 
| Transportable Tablespaces | Amazon RDS doesn't support the MySQL Transportable Tablespaces feature\. | Low | 
| X Plugin | Amazon RDS doesn't support the MySQL X Plugin\. | Low | 

The following table includes Oracle database features that Amazon RDS for Oracle doesn't support\.


| Limitation | Description | Impact | 
| --- | --- | --- | 
| Active Data Guard | You can't use Active Data Guard with Oracle multitenant container databases \(CDB\)\. | Medium | 
| Automatic Storage Management | Amazon RDS doesn't support Oracle Automatic Storage Management \(Oracle ASM\)\. | Medium | 
| Database Activity Streams | Amazon RDS doesn't support Oracle Database Activity Streams for the single\-tenant architecture\. | High | 
| File size limit | The maximum size of a single file on RDS for Oracle DB instances is 16 TiB\. | Medium | 
| FTP and SFTP | Amazon RDS doesn't support FTP and SFTP\. | Medium | 
| Hybrid partitioned tables | Amazon RDS doesn't support Oracle hybrid partitioned tables\. | High | 
| Oracle Data Guard | Amazon RDS doesn't Oracle Data Guard for the single\-tenant architecture\. | High | 
| Oracle Database Vault | Amazon RDS doesn't support Oracle Database Vault\. | High | 
| Oracle DBA privileges Vault | Amazon RDS has limitations for Oracle DBA privileges\. For more information, see [Limitations for Oracle DBA privileges](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Oracle.Concepts.limitations.html#Oracle.Concepts.dba-limitations)\. | High | 
| Oracle Enterprise Manager | Amazon RDS doesn't Oracle Enterprise Manager for the single\-tenant architecture\. | High | 
| Oracle Enterprise Manager Agent | Amazon RDS doesn't Oracle Enterprise Manager Agent for the single\-tenant architecture\. | Medium | 
| Oracle Enterprise Manager Cloud Control Management Repository | You can't use an Amazon RDS for Oracle DB instance for Oracle Enterprise Manager Cloud Control Management Repository\. | High | 
| Oracle Flashback Database | Amazon RDS doesn't support the Oracle Flashback Database feature\. | High | 
| Oracle Label Security | Amazon RDS doesn't support Oracle Label Security for the single\-tenant architecture\. You can use Oracle Label Security only with multitenant container databases \(Oracle CDB\)\. | High | 
| Oracle Messaging Gateway | Amazon RDS doesn't support Oracle Messaging Gateway\. | High | 
| Oracle Real Application Clusters | Amazon RDS doesn't support Oracle Real Application Clusters \(Oracle RAC\)\. | High | 
| Oracle Real Application Testing | Amazon RDS doesn't support Oracle Real Application Testing\. | High | 
| Oracle Snapshot Standby databases | Amazon RDS doesn't support Oracle Snapshot Standby databases\. | High | 
| Public synonyms | Amazon RDS doesn't support public synonyms for Oracle\-supplied schemas\. | Medium | 
| Schemas for unsupported features | Amazon RDS doesn't support schemas for Oracle features and components that require system privileges\. | High | 
| Pure unified auditing | Amazon RDS doesn't support the pure unified auditing\. You can use the unified auditing in mixed mode\. | Medium | 
| Workspace Manager | Amazon RDS doesn't support the Oracle Database Workspace Manager `WMSYS` schema\. | High | 

The following table includes PostgreSQL database features that Amazon RDS for PostgreSQL doesn't support\.


| Limitation | Description | Impact | 
| --- | --- | --- | 
| Concurrent connections | The maximum number of concurrent connections to your RDS for PostgreSQL instance is limited by the `max_connections` parameter\. | Low | 
| Newest versions | Amazon RDS doesn't apply major version upgrades automatically\. To perform a major version upgrade, modify your DB instance manually\. For more information, see [Choosing a major version upgrade for PostgreSQL](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_UpgradeDBInstance.PostgreSQL.html#USER_UpgradeDBInstance.PostgreSQL.MajorVersion)\. | Low | 
| Reserved connections | Amazon RDS reserves up to 3 connections for system maintenance\. If you specify a value for the user connections parameter, add 3 to the number of connections that you expect to use\. | Low | 
| Supported extensions | RDS for PostgreSQL supports a limited number of extensions for the PostgreSQL database engine\. You can find a list of supported extensions in the default DB parameter group for your PostgreSQL version\. You can also see the current extensions list using `psql` by showing the `rds.extensions` parameter\. | Low | 
| Tablespace splitting or isolation | You can't use tablespaces for I/O splitting or isolation\. In RDS for PostgreSQL, all storage is on a single logical volume\. | Low | 

The following table includes SQL Server database features that Amazon RDS for SQL Server doesn't support\.


| Limitation | Description | Impact | 
| --- | --- | --- | 
| Backing up to Microsoft Azure Blob Storage | RDS for SQL Server doesn't support backing up to Microsoft Azure Blob Storage\. | Medium | 
| Buffer pool extension | RDS for SQL Server doesn't support the buffer pool extension\. | High | 
| Custom password policies | RDS for SQL Server doesn't support custom password policies\. | Medium | 
| Data Quality Services | RDS for SQL Server doesn't support SQL Server Data Quality Services \(DQS\)\. | High | 
| Database Log Shipping | RDS for SQL Server doesn't support database Log Shipping\. | High | 
| Database names |  Database names have the following limitations: can't start with rdsadmin; can't start or end with a space or a tab; can't contain any of the characters that create a new line; can't contain a single quotation mark \('\)\.  | Medium | 
| Database snapshots | RDS for SQL Server doesn't support database snapshots\. You can use only DB instance snapshots in Amazon RDS\. | Medium | 
| Extended stored procedures | RDS for SQL Server doesn't support extended stored procedures, including `xp_cmdshell`\. | High | 
| File tables | RDS for SQL Server doesn't support file tables\. | Medium | 
| FILESTREAM support | RDS for SQL Server doesn't provide FILESTREAM support\. | Medium | 
| Linked servers | RDS for SQL Server provides limited support for linked servers\. | High | 
| Machine Learning and R Services | RDS for SQL Server doesn't support Machine Learning and R Services because you need OS access to install these services\. | High | 
| Maintenance plans | RDS for SQL Server doesn't support maintenance plans\. | High | 
| Performance Data Collector | RDS for SQL Server doesn't support Performance Data Collector\. | High | 
| Policy\-Based Management | RDS for SQL Server doesn't support Policy\-Based Management\. | Medium | 
| PolyBase | RDS for SQL Server doesn't support PolyBase\. | High | 
| Replication | RDS for SQL Server doesn't support replication\. | Medium | 
| Resource Governor | RDS for SQL Server doesn't support Resource Governor\. | High | 
| Server\-level triggers | RDS for SQL Server doesn't support server\-level triggers\. | Medium | 
| Service Broker endpoints | RDS for SQL Server doesn't support Service Broker endpoints\. | High | 
| SSAS | Consider the limitations that apply to running SQL Server Analysis Services \(SSAS\) on RDS for SQL Server\. For more information, see [Limitations](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Appendix.SQLServer.Options.SSAS.html#SSAS.Limitations)\.  | Low | 
| SSIS | Consider the limitations that apply to running SQL Server Integration Services \(SSIS\) on RDS for SQL Server\. For more information, see [Limitations](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Appendix.SQLServer.Options.SSIS.html#SSIS.Limitations)\.  | Low | 
| SSRS | Consider the limitations that apply to running SQL Server Reporting Services \(SSRS\) on RDS for SQL Server\. For more information, see [Limitations](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Appendix.SQLServer.Options.SSRS.html#SSRS.Limitations)\.  | Low | 
| Storage size for SQL Server DB instances | The maximum storage size for SQL Server General Purpose \(SSD\) storage and Provisioned IOPS storage instances is 16 TiB\. The maximum storage size for SQL Server Magnetic storage instances is 1 TiB\. | High | 
| Stretch Database | RDS for SQL Server doesn't support the SQL Server Stretch Database feature\. | Medium | 
| T\-SQL endpoints | RDS for SQL Server doesn't support all operations that use `CREATE ENDPOINT`\. | High | 
| TRUSTWORTHY database property | RDS for SQL Server doesn't support the `TRUSTWORTHY` database property because it requires the `sysadmin` role\. | Medium | 