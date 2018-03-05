# Using a MySQL\-Compatible Database as a Source for AWS DMS<a name="CHAP_Source.MySQL"></a>

You can migrate data from any MySQL\-compatible database \(MySQL, MariaDB, or Amazon Aurora MySQL\) using AWS Database Migration Service\. MySQL versions 5\.5, 5\.6, and 5\.7, and also MariaDB and Amazon Aurora MySQL, are supported for on\-premises\. All AWS\-managed MySQL databases \(Amazon RDS for MySQL, Amazon RDS for MariaDB, Amazon Aurora MySQL\) are supported as sources for AWS DMS\. 

You can use SSL to encrypt connections between your MySQL\-compatible endpoint and the replication instance\. For more information on using SSL with a MySQL\-compatible endpoint, see [Using SSL With AWS Database Migration Service](CHAP_Security.SSL.md)\.

In the following sections, the term "self\-managed" applies to any database that is installed either on\-premises or on Amazon EC2\. The term "Amazon\-managed" applies to any database on Amazon RDS, Amazon Aurora, or Amazon S3\.

For additional details on working with MySQL\-compatible databases and AWS DMS, see the following sections\.


+ [Using Any MySQL\-Compatible Database as a Source for AWS DMS](#CHAP_Source.MySQL.Prerequisites)
+ [Using a Self\-Managed MySQL\-Compatible Database as a Source for AWS DMS](#CHAP_Source.MySQL.CustomerManaged)
+ [Using a Amazon\-Managed MySQL\-Compatible Database as a Source for AWS DMS](#CHAP_Source.MySQL.AmazonManaged)
+ [Limitations on Using a MySQL Database as a Source for AWS DMS](#CHAP_Source.MySQL.Limitations)
+ [Extra Connection Attributes When Using MySQL as a Source for AWS DMS](#CHAP_Source.MySQL.ConnectionAttrib)
+ [Source Data Types for MySQL](#CHAP_Source.MySQL.DataTypes)

## Using Any MySQL\-Compatible Database as a Source for AWS DMS<a name="CHAP_Source.MySQL.Prerequisites"></a>

Before you begin to work with a MySQL database as a source for AWS DMS, make sure that you have the following prerequisites\. These prerequisites apply to either self\-managed or Amazon\-managed sources\.

You must have an account for AWS DMS that has the Replication Admin role\. The role needs the following privileges:

+ **REPLICATION CLIENT** – This privilege is required for change data capture \(CDC\) tasks only\. In other words, full\-load\-only tasks don't require this privilege\.

+ **REPLICATION SLAVE** – This privilege is required for change data capture \(CDC\) tasks only\. In other words, full\-load\-only tasks don't require this privilege\.

+ **SUPER** – This privilege is required only in MySQL versions before 5\.6\.6\.

The AWS DMS user must also have SELECT privileges for the source tables designated for replication\.

## Using a Self\-Managed MySQL\-Compatible Database as a Source for AWS DMS<a name="CHAP_Source.MySQL.CustomerManaged"></a>

You can use the following self\-managed MySQL\-compatible databases as sources for AWS DMS:

+ MySQL Community Edition

+ MySQL Standard Edition

+ MySQL Enterprise Edition

+ MySQL Cluster Carrier Grade Edition

+ MariaDB Community Edition

+ MariaDB Enterprise Edition

+ MariaDB Column Store

You must enable binary logging if you plan to use change data capture \(CDC\)\. To enable binary logging, the following parameters must be configured in MySQL’s `my.ini` \(Windows\) or `my.cnf` \(UNIX\) file\.


| Parameter | Value | 
| --- | --- | 
| `server_id` | Set this parameter to a value of 1 or greater\. | 
| `log-bin` | Set the path to the binary log file, such as `log-bin=E:\MySql_Logs\BinLog`\. Don't include the file extension\. | 
| `binlog_format` | Set this parameter to `ROW`\. | 
| `expire_logs_days` | Set this parameter to a value of 1 or greater\. To prevent overuse of disk space, we recommend that you don't use the default value of 0\. | 
| `binlog_checksum` | Set this parameter to `NONE`\. | 
| `binlog_row_image` | Set this parameter to `FULL`\. | 
| `log_slave_updates` | Set this parameter to `TRUE` if you are using a MySQL or MariaDB read\-replica as a source\. | 

If your source uses the NDB \(clustered\) database engine, the following parameters must be configured to enable CDC on tables that use that storage engine\. Add these changes in MySQL’s `my.ini` \(Windows\) or `my.cnf` \(UNIX\) file\.


| Parameter | Value | 
| --- | --- | 
| `ndb_log_bin` | Set this parameter to `ON`\. This value ensures that changes in clustered tables are logged to the binary log\. | 
| `ndb_log_update_as_write` | Set this parameter to `OFF`\. This value prevents writing UPDATE statements as INSERT statements in the binary log\. | 
| `ndb_log_updated_only` | Set this parameter to `OFF`\. This value ensures that the binary log contains the entire row and not just the changed columns\. | 

## Using a Amazon\-Managed MySQL\-Compatible Database as a Source for AWS DMS<a name="CHAP_Source.MySQL.AmazonManaged"></a>

You can use the following Amazon\-managed MySQL\-compatible databases as sources for AWS DMS:

+ MySQL Community Edition

+ MariaDB Community Edition

+ Amazon Aurora MySQL

When using an Amazon\-managed MySQL\-compatible database as a source for AWS DMS, make sure that you have the following prerequisites:

+ You must enable automatic backups\. For more information on setting up automatic backups, see [Working with Automated Backups](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithAutomatedBackups.html) in the *Amazon Relational Database Service User Guide*\.

+ You must enable binary logging if you plan to use change data capture \(CDC\)\. For more information on setting up binary logging for an Amazon RDS MySQL database, see [Working with Automated Backups](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithAutomatedBackups.html) in the *Amazon Relational Database Service User Guide*\.

+ You must ensure that the binary logs are available to AWS DMS\. Because Amazon\-managed MySQL\-compatible databases purge the binary logs as soon as possible, you should increase the length of time that the logs remain available\. For example, to increase log retention to 24 hours, run the following command\. 

  ```
   call mysql.rds_set_configuration('binlog retention hours', 24);
  ```

+ The `binlog_format` parameter should be set to "ROW\."

+ The `binlog_checksum `parameter should be set to "NONE"\. For more information about setting parameters in Amazon RDS MySQL, see [Working with Automated Backups](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithAutomatedBackups.html) in the *Amazon Relational Database Service User Guide*\.

+ If you are using an Amazon RDS MySQL or Amazon RDS MariaDB read replica as a source, then backups must be enabled on the read replica\.

## Limitations on Using a MySQL Database as a Source for AWS DMS<a name="CHAP_Source.MySQL.Limitations"></a>

When using a MySQL database as a source, AWS DMS doesn't support the following:

+  Change data capture \(CDC\) is not supported for Amazon RDS MySQL 5\.5 or lower\. For Amazon RDS MySQL, you must use version 5\.6 or higher to enable CDC\.

+  The date definition language \(DDL\) statements TRUNCATE PARTITION, DROP TABLE, and RENAME TABLE are not supported\.

+  Using an ALTER TABLE<table\_name> ADD COLUMN <column\_name> statement to add columns to the beginning \(FIRST\) or the middle of a table \(AFTER\) is not supported\. Columns are always added to the end of the table\.

+ CDC is not supported when a table name contains uppercase and lowercase characters, and the source engine is hosted on an operating system with case\-insensitive file names\. An example is Windows or OS X using HFS\+\.

+  The AR\_H\_USER header column is not supported\.

+  The AUTO\_INCREMENT attribute on a column is not migrated to a target database column\.

+  Capturing changes when the binary logs are not stored on standard block storage is not supported\. For example, CDC doesn't work when the binary logs are stored on Amazon S3\.

+  AWS DMS creates target tables with the InnoDB storage engine by default\. If you need to use a storage engine other than InnoDB, you must manually create the table and migrate to it using [“do nothing” mode](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_GettingStarted.html)\.

+ You can't use Aurora MySQL read replicas as a source for AWS DMS\.

+  If the MySQL\-compatible source is stopped during full load, the AWS DMS task doesn't stop with an error\. The task ends successfully, but the target might be out of sync with the source\. If this happens, either restart the task or reload the affected tables\.

+  Indexes created on a portion of a column value aren't migrated\. For example, the index CREATE INDEX first\_ten\_chars ON customer \(name\(10\)\) isn't created on the target\.

+ In some cases, the task is configured to not replicate LOBs \("SupportLobs" is false in task settings or “Don't include LOB columns” is checked in the task console\)\. In these cases, AWS DMS doesn't migrate any MEDIUMBLOB, LONGBLOB, MEDIUMTEXT, and LONGTEXT columns to the target\.

  BLOB, TINYBLOB, TEXT, and TINYTEXT columns are not affected and are migrated to the target\.

## Extra Connection Attributes When Using MySQL as a Source for AWS DMS<a name="CHAP_Source.MySQL.ConnectionAttrib"></a>

You can use extra connection attributes to configure a MySQL source\. You specify these settings when you create the source endpoint\. Multiple extra connection attribute settings should be separated from each other by semicolons\.

The following table shows the extra connection attributes available when using Amazon RDS MySQL as a source for AWS DMS\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Source.MySQL.html)

## Source Data Types for MySQL<a name="CHAP_Source.MySQL.DataTypes"></a>

The following table shows the MySQL database source data types that are supported when using AWS DMS and the default mapping from AWS DMS data types\.

**Note**  
The UTF\-8 4\-byte character set \(utf8mb4\) is not supported and can cause unexpected behavior in a source database\. Plan to convert any data using the UTF\-8 4\-byte character set before migrating\.

For information on how to view the data type that is mapped in the target, see the section for the target endpoint you are using\.

For additional information about AWS DMS data types, see [Data Types for AWS Database Migration Service](CHAP_Reference.DataTypes.md)\.


|  MySQL Data Types  |  AWS DMS Data Types  | 
| --- | --- | 
|  INT  |  INT4  | 
|  MEDIUMINT  |  INT4  | 
|  BIGINT  |  INT8  | 
|  TINYINT  |  INT1  | 
|  DECIMAL\(10\)  |  NUMERIC \(10,0\)  | 
|  BINARY  |  BYTES\(1\)  | 
|  BIT  |  BOOLEAN  | 
|  BIT\(64\)  |  BYTES\(8\)  | 
|  BLOB  |  BYTES\(66535\)  | 
|  LONGBLOB  |  BLOB  | 
|  MEDIUMBLOB  |  BLOB  | 
|  TINYBLOB  |  BYTES\(255\)  | 
|  DATE  |  DATE  | 
|  DATETIME  |  DATETIME  | 
|  TIME  |  STRING  | 
|  TIMESTAMP  |  DATETIME  | 
|  YEAR  |  INT2  | 
|  DOUBLE  |  REAL8  | 
|  FLOAT  |  REAL\(DOUBLE\) The supported FLOAT range is \-1\.79E\+308 to \-2\.23E\-308, 0 and 2\.23E\-308 to 1\.79E\+308 If FLOAT values aren't in this range, map the FLOAT data type to the STRING data type\.  | 
|  VARCHAR \(45\)  |  WSTRING \(45\)  | 
|  VARCHAR \(2000\)  |  WSTRING \(2000\)  | 
|  VARCHAR \(4000\)  |  WSTRING \(4000\)  | 
|  VARBINARY \(4000\)  |  BYTES \(4000\)  | 
|  VARBINARY \(2000\)  |  BYTES \(2000\)  | 
|  CHAR  |  WSTRING  | 
|  TEXT  |  WSTRING \(65535\)  | 
|  LONGTEXT  |  NCLOB  | 
|  MEDIUMTEXT  |  NCLOB  | 
|  TINYTEXT  |  WSTRING \(255\)  | 
|  GEOMETRY  |  BLOB  | 
|  POINT  |  BLOB  | 
|  LINESTRING  |  BLOB  | 
|  POLYGON  |  BLOB  | 
|  MULTIPOINT  |  BLOB  | 
|  MULTILINESTRING  |  BLOB  | 
|  MULTIPOLYGON  |  BLOB  | 
|  GEOMETRYCOLLECTION  |  BLOB  | 

**Note**  
If the DATETIME and TIMESTAMP data types are specified with a "zero" value \(that is, 0000\-00\-00\), make sure that the target database in the replication task supports "zero" values for the DATETIME and TIMESTAMP data types\. Otherwise, these values are recorded as null on the target\.

The following MySQL data types are supported in full load only\.


|  MySQL Data Types  |  AWS DMS Data Types  | 
| --- | --- | 
|  ENUM  |  STRING  | 
|  SET  |  STRING  | 