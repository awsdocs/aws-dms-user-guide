# Using a MySQL\-compatible database as a source for AWS DMS<a name="CHAP_Source.MySQL"></a>

You can migrate data from any MySQL\-compatible database \(MySQL, MariaDB, or Amazon Aurora MySQL\) using AWS Database Migration Service\. 

For information about versions of MySQL that AWS DMS supports as a source, see [Sources for AWS DMS](CHAP_Introduction.Sources.md)\.  

You can use SSL to encrypt connections between your MySQL\-compatible endpoint and the replication instance\. For more information on using SSL with a MySQL\-compatible endpoint, see [Using SSL with AWS Database Migration Service](CHAP_Security.SSL.md)\.

In the following sections, the term "self\-managed" applies to any database that is installed either on\-premises or on Amazon EC2\. The term "AWS\-managed" applies to any database on Amazon RDS, Amazon Aurora, or Amazon S3\.

For additional details on working with MySQL\-compatible databases and AWS DMS, see the following sections\.

**Topics**
+ [Migrating from MySQL to MySQL using AWS DMS](#CHAP_Source.MySQL.Homogeneous)
+ [Using any MySQL\-compatible database as a source for AWS DMS](#CHAP_Source.MySQL.Prerequisites)
+ [Using a self\-managed MySQL\-compatible database as a source for AWS DMS](#CHAP_Source.MySQL.CustomerManaged)
+ [Using an AWS\-managed MySQL\-compatible database as a source for AWS DMS](#CHAP_Source.MySQL.AmazonManaged)
+ [Limitations on using a MySQL database as a source for AWS DMS](#CHAP_Source.MySQL.Limitations)
+ [Support for XA transactions](#CHAP_Source.MySQL.XA)
+ [Endpoint settings when using MySQL as a source for AWS DMS](#CHAP_Source.MySQL.ConnectionAttrib)
+ [Source data types for MySQL](#CHAP_Source.MySQL.DataTypes)

## Migrating from MySQL to MySQL using AWS DMS<a name="CHAP_Source.MySQL.Homogeneous"></a>

For a heterogeneous migration, where you are migrating from a database engine other than MySQL to a MySQL database, AWS DMS is almost always the best migration tool to use\. But for a homogeneous migration, where you are migrating from a MySQL database to a MySQL database, native tools can be more effective\.

We recommend that you use native MySQL database migration tools such as `mysqldump` under the following conditions: 
+ You have a homogeneous migration, where you are migrating from a source MySQL database to a target MySQL database\. 
+ You are migrating an entire database\.
+ The native tools allow you to migrate your data with minimal downtime\. 

You can import data from an existing MySQL or MariaDB database to an Amazon RDS MySQL or MariaDB DB instance\. You do so by copying the database with [mysqldump](http://dev.mysql.com/doc/refman/5.6/en/mysqldump.html) and piping it directly into the Amazon RDS MySQL or MariaDB DB instance\. The `mysqldump` command\-line utility is commonly used to make backups and transfer data from one MySQL or MariaDB server to another\. It is included with MySQL and MariaDB client software\.

For more information about importing a MySQL database into Amazon RDS for MySQL or Amazon Aurora MySQL\-Compatible Edition, see [ Importing data into a MySQL DB instance ](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/MySQL.Procedural.Importing.Other.html) and [ Importing data from a MySQL or MariaDB DB to an Amazon RDS MySQL or MariaDB DB instance](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/MySQL.Procedural.Importing.SmallExisting.html)\. 

## Using any MySQL\-compatible database as a source for AWS DMS<a name="CHAP_Source.MySQL.Prerequisites"></a>

Before you begin to work with a MySQL database as a source for AWS DMS, make sure that you have the following prerequisites\. These prerequisites apply to either self\-managed or AWS\-managed sources\.

You must have an account for AWS DMS that has the Replication Admin role\. The role needs the following privileges:
+ **REPLICATION CLIENT** – This privilege is required for CDC tasks only\. In other words, full\-load\-only tasks don't require this privilege\.
+ **REPLICATION SLAVE** – This privilege is required for CDC tasks only\. In other words, full\-load\-only tasks don't require this privilege\.
+ **SUPER** – This privilege is required only in MySQL versions before 5\.6\.6\.

The AWS DMS user must also have SELECT privileges for the source tables designated for replication\.

## Using a self\-managed MySQL\-compatible database as a source for AWS DMS<a name="CHAP_Source.MySQL.CustomerManaged"></a>

You can use the following self\-managed MySQL\-compatible databases as sources for AWS DMS:
+ MySQL Community Edition
+ MySQL Standard Edition
+ MySQL Enterprise Edition
+ MySQL Cluster Carrier Grade Edition
+ MariaDB Community Edition
+ MariaDB Enterprise Edition
+ MariaDB Column Store

To use CDC, make sure to enable binary logging\. To enable binary logging, the following parameters must be configured in MySQL's `my.ini` \(Windows\) or `my.cnf` \(UNIX\) file\.


| Parameter | Value | 
| --- | --- | 
| `server-id` | Set this parameter to a value of 1 or greater\. | 
| `log-bin` | Set the path to the binary log file, such as `log-bin=E:\MySql_Logs\BinLog`\. Don't include the file extension\. | 
| `binlog_format` | Set this parameter to `ROW`\. We recommend this setting during replication because in certain cases when `binlog_format` is set to `STATEMENT`, it can cause inconsistency when replicating data to the target\. The database engine also writes similar inconsistent data to the target when `binlog_format` is set to `MIXED`, because the database engine automatically switches to `STATEMENT`\-based logging which can result in writing inconsistent data on the target database\. | 
| `expire_logs_days` | Set this parameter to a value of 1 or greater\. To prevent overuse of disk space, we recommend that you don't use the default value of 0\. | 
| `binlog_checksum` | Set this parameter to `NONE`\. | 
| `binlog_row_image` | Set this parameter to `FULL`\. | 
| `log_slave_updates` | Set this parameter to `TRUE` if you are using a MySQL or MariaDB read\-replica as a source\. | 

If your source uses the NDB \(clustered\) database engine, the following parameters must be configured to enable CDC on tables that use that storage engine\. Add these changes in MySQL's `my.ini` \(Windows\) or `my.cnf` \(UNIX\) file\.


| Parameter | Value | 
| --- | --- | 
| `ndb_log_bin` | Set this parameter to `ON`\. This value ensures that changes in clustered tables are logged to the binary log\. | 
| `ndb_log_update_as_write` | Set this parameter to `OFF`\. This value prevents writing UPDATE statements as INSERT statements in the binary log\. | 
| `ndb_log_updated_only` | Set this parameter to `OFF`\. This value ensures that the binary log contains the entire row and not just the changed columns\. | 

## Using an AWS\-managed MySQL\-compatible database as a source for AWS DMS<a name="CHAP_Source.MySQL.AmazonManaged"></a>

You can use the following AWS\-managed MySQL\-compatible databases as sources for AWS DMS:
+ MySQL Community Edition
+ MariaDB Community Edition
+ Amazon Aurora MySQL\-Compatible Edition

When using an AWS\-managed MySQL\-compatible database as a source for AWS DMS, make sure that you have the following prerequisites for CDC:
+ To enable binary logs for RDS for MySQL and for RDS for MariaDB, enable automatic backups at the instance level\. To enable binary logs for an Aurora MySQL cluster, change the variable `binlog_format` in the parameter group\.

  For more information about setting up automatic backups, see [Working with automated backups](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithAutomatedBackups.html) in the *Amazon RDS User Guide*\.

  For more information about setting up binary logging for an Amazon RDS for MySQL database, see [ Setting the binary logging format](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.MySQL.BinaryFormat.html) in the *Amazon RDS User Guide*\. 

  For more information about setting up binary logging for an Aurora MySQL cluster, see [ How do I turn on binary logging for my Amazon Aurora MySQL cluster?](https://aws.amazon.com/premiumsupport/knowledge-center/enable-binary-logging-aurora/)\. 
+ If you plan to use CDC, turn on binary logging\. For more information on setting up binary logging for an Amazon RDS for MySQL database, see [Setting the binary logging format](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.MySQL.BinaryFormat.html) in the *Amazon RDS User Guide*\.
+ Ensure that the binary logs are available to AWS DMS\. Because AWS\-managed MySQL\-compatible databases purge the binary logs as soon as possible, you should increase the length of time that the logs remain available\. For example, to increase log retention to 24 hours, run the following command\. 

  ```
   call mysql.rds_set_configuration('binlog retention hours', 24);
  ```
+ Set the `binlog_format` parameter to `"ROW"`\.
**Note**  
For MariaDB, if the `binlog_format` parameter is switched to `ROW` for replication purposes, subsequent binary logs are still created in `MIXED` format\. This can prevent DMS from performing change data capture\. So, when switching the `binlog_format` parameter for MariaDB, perform a reboot or start then stop your replication task\.
+ Set the `binlog_row_image` parameter to `"Full"`\. 
+ Set the `binlog_checksum` parameter to `"NONE"`\. For more information about setting parameters in Amazon RDS MySQL, see [Working with automated backups](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithAutomatedBackups.html) in the *Amazon RDS User Guide*\.
+ If you are using an Amazon RDS MySQL or Amazon RDS MariaDB read replica as a source, enable backups on the read replica, and ensure the `log_slave_updates` parameter is set to `TRUE`\.

## Limitations on using a MySQL database as a source for AWS DMS<a name="CHAP_Source.MySQL.Limitations"></a>

When using a MySQL database as a source, consider the following:
+  Change data capture \(CDC\) isn't supported for Amazon RDS MySQL 5\.5 or lower\. For Amazon RDS MySQL, you must use version 5\.6, 5\.7, or 8\.0 to enable CDC\. CDC is supported for self\-managed MySQL 5\.5 sources\. 
+ For CDC, `CREATE TABLE`, `ADD COLUMN`, and `DROP COLUMN` changing the column data type, and `renaming a column` are supported\. However, `DROP TABLE`, `RENAME TABLE`, and updates made to other attributes, such as column default value, column nullability, character set and so on, are not supported\.
+  For partitioned tables on the source, when you set **Target table preparation mode** to **Drop tables on target**, AWS DMS creates a simple table without any partitions on the MySQL target\. To migrate partitioned tables to a partitioned table on the target, precreate the partitioned tables on the target MySQL database\.
+  Using an `ALTER TABLE table_name ADD COLUMN column_name` statement to add columns to the beginning \(FIRST\) or the middle of a table \(AFTER\) isn't supported\. Columns are always added to the end of the table\.
+ CDC isn't supported when a table name contains uppercase and lowercase characters, and the source engine is hosted on an operating system with case\-insensitive file names\. An example is Microsoft Windows or OS X using HFS\+\.
+ You can use Aurora MySQL\-Compatible Edition Serverless for full load, but you can't use it for CDC\. This is because you can't enable the prerequisites for MySQL\. For more information, see [ Parameter groups and Aurora Serverless v1](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/aurora-serverless.how-it-works.html#aurora-serverless.parameter-groups)\. 
+  The AUTO\_INCREMENT attribute on a column isn't migrated to a target database column\.
+  Capturing changes when the binary logs aren't stored on standard block storage isn't supported\. For example, CDC doesn't work when the binary logs are stored on Amazon S3\.
+  AWS DMS creates target tables with the InnoDB storage engine by default\. If you need to use a storage engine other than InnoDB, you must manually create the table and migrate to it using [do nothing](https://docs.aws.amazon.com/dms/latest/userguide/CHAP_GettingStarted.html) mode\.
+ You can't use Aurora MySQL replicas as a source for AWS DMS unless your DMS migration task mode is **Migrate existing data**—full load only\.
+  If the MySQL\-compatible source is stopped during full load, the AWS DMS task doesn't stop with an error\. The task ends successfully, but the target might be out of sync with the source\. If this happens, either restart the task or reload the affected tables\.
+  Indexes created on a portion of a column value aren't migrated\. For example, the index CREATE INDEX first\_ten\_chars ON customer \(name\(10\)\) isn't created on the target\.
+ In some cases, the task is configured to not replicate LOBs \("SupportLobs" is false in task settings or **Don't include LOB columns** is chosen in the task console\)\. In these cases, AWS DMS doesn't migrate any MEDIUMBLOB, LONGBLOB, MEDIUMTEXT, and LONGTEXT columns to the target\.

  BLOB, TINYBLOB, TEXT, and TINYTEXT columns aren't affected and are migrated to the target\.
+ Temporal data tables or system—versioned tables are not supported on MariaDB source and target databases\.
+ If migrating between two Amazon RDS Aurora MySQL clusters, the RDS Aurora MySQL source endpoint must be a read/write instance, not a replica instance\. 
+ AWS DMS currently doesn't support views migration for MariaDB\.
+ AWS DMS doesn't support DDL changes for partitioned tables for MySQL\. To skip table suspension for partition DDL changes during CDC, set `skipTableSuspensionForPartitionDdl` to `true`\.
+ AWS DMS only supports XA transactions in version 3\.5\.0 and higher\. Previous versions do not support XA transactions\. For more information, see [Support for XA transactions](#CHAP_Source.MySQL.XA) following\.
+ AWS DMS doesn't use GTIDs for replication, even if the source data contains them\. 
+ AWS DMS doesn't support binary log transaction compression\.

## Support for XA transactions<a name="CHAP_Source.MySQL.XA"></a>

An Extended Architecture \(XA\) transaction is a transaction that can be used to group a series of operations from multiple transactional resources into a single, reliable global transaction\. An XA transaction uses a two\-phase commit protocol\. In general, capturing changes while there are open XA transactions might lead to loss of data\. If your database doesn't use XA transactions, you can ignore this permission and the configuration `IgnoreOpenXaTransactionsCheck` by using the deafult value `TRUE`\. To start replicating from a source that has XA transactions, do the following:
+ Ensure that the AWS DMS endpoint user has the following permission:

  ```
  grant XA_RECOVER_ADMIN on *.* to 'userName'@'%';
  ```
+ Set the endpoint setting `IgnoreOpenXaTransactionsCheck` to `false`\.

## Endpoint settings when using MySQL as a source for AWS DMS<a name="CHAP_Source.MySQL.ConnectionAttrib"></a>

You can use endpoint settings to configure your MySQL source database similar to using extra connection attributes\. You specify the settings when you create the source endpoint using the AWS DMS console, or by using the `create-endpoint` command in the [AWS CLI](https://docs.aws.amazon.com/cli/latest/reference/dms/index.html), with the `--my-sql-settings '{"EndpointSetting": "value", ...}'` JSON syntax\.

The following table shows the endpoint settings that you can use with MySQL as a source\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Source.MySQL.html)

## Source data types for MySQL<a name="CHAP_Source.MySQL.DataTypes"></a>

The following table shows the MySQL database source data types that are supported when using AWS DMS and the default mapping from AWS DMS data types\.

For information on how to view the data type that is mapped in the target, see the section for the target endpoint you are using\.

For additional information about AWS DMS data types, see [Data types for AWS Database Migration Service](CHAP_Reference.DataTypes.md)\.


|  MySQL data types  |  AWS DMS data types  | 
| --- | --- | 
|  INT  |  INT4  | 
|  BIGINT  |  INT8  | 
|  MEDIUMINT  |  INT4  | 
|  TINYINT  |  INT1  | 
|  SMALLINT  |  INT2  | 
|  UNSIGNED TINYINT  |  UINT1  | 
|  UNSIGNED SMALLINT  |  UINT2  | 
|  UNSIGNED MEDIUMINT  |  UINT4  | 
|  UNSIGNED INT  |  UINT4  | 
|  UNSIGNED BIGINT  |  UINT8  | 
|  DECIMAL\(10\)  |  NUMERIC \(10,0\)  | 
|  BINARY  |  BYTES\(1\)  | 
|  BIT  |  BOOLEAN  | 
|  BIT\(64\)  |  BYTES\(8\)  | 
|  BLOB  |  BYTES\(65535\)  | 
|  LONGBLOB  |  BLOB  | 
|  MEDIUMBLOB  |  BLOB  | 
|  TINYBLOB  |  BYTES\(255\)  | 
|  DATE  |  DATE  | 
|  DATETIME  |  DATETIME DATETIME without a parenthetical value is replicated without milliseconds\. DATETIME with a parenthetical value of 1 to 5 \(such as `DATETIME(5)`\) is replicated with milliseconds\. When replicating a DATETIME column, the time remains the same on the target\. It is not converted to UTC\.  | 
|  TIME  |  STRING  | 
|  TIMESTAMP  |  DATETIME When replicating a TIMESTAMP column, the time is converted to UTC on the target\.  | 
|  YEAR  |  INT2  | 
|  DOUBLE  |  REAL8  | 
|  FLOAT  |  REAL\(DOUBLE\) If the FLOAT values are not in the range following, use a transformation to map FLOAT to STRING\. For more information about transformations, see [ Transformation rules and actions](CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Transformations.md)\. The supported FLOAT range is \-1\.79E\+308 to \-2\.23E\-308, 0, and 2\.23E\-308 to 1\.79E\+308  | 
|  VARCHAR \(45\)  |  WSTRING \(45\)  | 
|  VARCHAR \(2000\)  |  WSTRING \(2000\)  | 
|  VARCHAR \(4000\)  |  WSTRING \(4000\)  | 
|  VARBINARY \(4000\)  |  BYTES \(4000\)  | 
|  VARBINARY \(2000\)  |  BYTES \(2000\)  | 
|  CHAR  |  WSTRING  | 
|  TEXT  |  WSTRING  | 
|  LONGTEXT  |  NCLOB  | 
|  MEDIUMTEXT  |  NCLOB  | 
|  TINYTEXT  |  WSTRING\(255\)  | 
|  GEOMETRY  |  BLOB  | 
|  POINT  |  BLOB  | 
|  LINESTRING  |  BLOB  | 
|  POLYGON  |  BLOB  | 
|  MULTIPOINT  |  BLOB  | 
|  MULTILINESTRING  |  BLOB  | 
|  MULTIPOLYGON  |  BLOB  | 
|  GEOMETRYCOLLECTION  |  BLOB  | 
|  ENUM  |  WSTRING \(*length*\) Here, *length* is the length of the longest value in the ENUM\.  | 
|  SET  |  WSTRING \(*length*\) Here, *length* is the total length of all values in the SET, including commas\.  | 
|  JSON  |  CLOB  | 

**Note**  
In some cases, you might specify the DATETIME and TIMESTAMP data types with a "zero" value \(that is, 0000\-00\-00\)\. If so, make sure that the target database in the replication task supports "zero" values for the DATETIME and TIMESTAMP data types\. Otherwise, these values are recorded as null on the target\.