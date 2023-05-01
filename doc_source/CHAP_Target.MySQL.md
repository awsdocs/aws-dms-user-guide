# Using a MySQL\-compatible database as a target for AWS Database Migration Service<a name="CHAP_Target.MySQL"></a>

You can migrate data to any MySQL\-compatible database using AWS DMS, from any of the source data engines that AWS DMS supports\. If you are migrating to an on\-premises MySQL\-compatible database, then AWS DMS requires that your source engine reside within the AWS ecosystem\. The engine can be on an AWS\-managed service such as Amazon RDS, Amazon Aurora, or Amazon S3\. Or the engine can be on a self\-managed database on Amazon EC2\. 

You can use SSL to encrypt connections between your MySQL\-compatible endpoint and the replication instance\. For more information on using SSL with a MySQL\-compatible endpoint, see [Using SSL with AWS Database Migration Service](CHAP_Security.SSL.md)\. 

For information about versions of MySQL that AWS DMS supports as a target, see [Targets for AWS DMS](CHAP_Introduction.Targets.md)\.

You can use the following MySQL\-compatible databases as targets for AWS DMS:
+ MySQL Community Edition
+ MySQL Standard Edition
+ MySQL Enterprise Edition
+ MySQL Cluster Carrier Grade Edition
+ MariaDB Community Edition
+ MariaDB Enterprise Edition
+ MariaDB Column Store
+ Amazon Aurora MySQL

**Note**  
Regardless of the source storage engine \(MyISAM, MEMORY, and so on\), AWS DMS creates a MySQL\-compatible target table as an InnoDB table by default\.   
If you need a table in a storage engine other than InnoDB, you can manually create the table on the MySQL\-compatible target and migrate the table using the **Do nothing** option\. For more information, see [Full\-load task settings](CHAP_Tasks.CustomizingTasks.TaskSettings.FullLoad.md)\.

For additional details on working with a MySQL\-compatible database as a target for AWS DMS, see the following sections\. 

**Topics**
+ [Using any MySQL\-compatible database as a target for AWS Database Migration Service](#CHAP_Target.MySQL.Prerequisites)
+ [Limitations on using a MySQL\-compatible database as a target for AWS Database Migration Service](#CHAP_Target.MySQL.Limitations)
+ [Endpoint settings when using a MySQL\-compatible database as a target for AWS DMS](#CHAP_Target.MySQL.ConnectionAttrib)
+ [Target data types for MySQL](#CHAP_Target.MySQL.DataTypes)

## Using any MySQL\-compatible database as a target for AWS Database Migration Service<a name="CHAP_Target.MySQL.Prerequisites"></a>

Before you begin to work with a MySQL\-compatible database as a target for AWS DMS, make sure that you have completed the following prerequisites:
+ Provide a user account to AWS DMS that has read/write privileges to the MySQL\-compatible database\. To create the necessary privileges, run the following commands\.

  ```
  CREATE USER '<user acct>'@'%' IDENTIFIED BY '<user password>';
  GRANT ALTER, CREATE, DROP, INDEX, INSERT, UPDATE, DELETE, SELECT ON <schema>.* TO 
  '<user acct>'@'%';
  GRANT ALL PRIVILEGES ON awsdms_control.* TO '<user acct>'@'%';
  ```
+ During the full\-load migration phase, you must disable foreign keys on your target tables\. To disable foreign key checks on a MySQL\-compatible database during a full load, you can add the following command to the **Extra connection attributes** section of the AWS DMS console for your target endpoint\.

  ```
  Initstmt=SET FOREIGN_KEY_CHECKS=0;
  ```
+ Set the database parameter `local_infile = 1` to enable AWS DMS to load data into the target database\.

## Limitations on using a MySQL\-compatible database as a target for AWS Database Migration Service<a name="CHAP_Target.MySQL.Limitations"></a>

When using a MySQL database as a target, AWS DMS doesn't support the following:
+ The data definition language \(DDL\) statements TRUNCATE PARTITION, DROP TABLE, and RENAME TABLE\.
+ Using an `ALTER TABLE table_name ADD COLUMN column_name` statement to add columns to the beginning or the middle of a table\.
+ When loading data to a MySQL\-compatible target in a full load task, AWS DMS doesn't report duplicate key errors in the task log\. This is because of the way that MySQL handles CSV load data\.
+ When you update a column's value to its existing value, MySQL\-compatible databases return a `0 rows affected` warning\. Although this behavior isn't technically an error, it is different from how the situation is handled by other database engines\. For example, Oracle performs an update of one row\. For MySQL\-compatible databases, AWS DMS generates an entry in the awsdms\_apply\_exceptions control table and logs the following warning\.

  ```
  Some changes from the source database had no impact when applied to
  the target database. See awsdms_apply_exceptions table for details.
  ```
+ Aurora Serverless is available as a target for Amazon Aurora version 1, compatible with MySQL version 5\.6\. Aurora Serverless is available as a target for Amazon Aurora version 2, compatible with MySQL version 5\.7\. \(Select Aurora MySQL version 2\.07\.1 to be able to use Aurora Serverless with MySQL 5\.7 compatibility\.\) For more information about Aurora Serverless, see [Using Amazon Aurora Serverless](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/aurora-serverless.html) in the *Amazon Aurora User Guide*\.
+ AWS DMS doesn't support using a reader endpoint for Aurora or Amazon RDS, unless the instances are in writable mode, that is, the `read_only` and `innodb_read_only` parameters are set to `0` or `OFF`\. For more information about using Amazon RDS and Aurora as targets, see the following:
  +  [ Determining which DB instance you are connected to](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraMySQL.BestPractices.html#AuroraMySQL.BestPractices.DeterminePrimaryInstanceConnection) 
  +  [ Updating read replicas with MySQL](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_MySQL.Replication.ReadReplicas.html#USER_MySQL.Replication.ReadReplicas.Updates) 

## Endpoint settings when using a MySQL\-compatible database as a target for AWS DMS<a name="CHAP_Target.MySQL.ConnectionAttrib"></a>

You can use endpoint settings to configure your MySQL\-compatible target database similar to using extra connection attributes\. You specify the settings when you create the target endpoint using the AWS DMS console, or by using the `create-endpoint` command in the [AWS CLI](https://docs.aws.amazon.com/cli/latest/reference/dms/index.html), with the `--my-sql-settings '{"EndpointSetting": "value", ...}'` JSON syntax\.

The following table shows the endpoint settings that you can use with MySQL as a target\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Target.MySQL.html)

You can also use extra connection attributes to configure your MySQL\-compatible target database\.

The following table shows the extra connection attributes that you can use with MySQL as a target\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Target.MySQL.html)

Alternatively, you can use the `AfterConnectScript` parameter of the `--my-sql-settings` command to disable foreign key checks and specify the time zone for your database\.

## Target data types for MySQL<a name="CHAP_Target.MySQL.DataTypes"></a>

The following table shows the MySQL database target data types that are supported when using AWS DMS and the default mapping from AWS DMS data types\.

For additional information about AWS DMS data types, see [Data types for AWS Database Migration Service](CHAP_Reference.DataTypes.md)\.


|  AWS DMS data types  |  MySQL data types  | 
| --- | --- | 
|  BOOLEAN  |  BOOLEAN  | 
|  BYTES  |  If the length is from 1 through 65,535, then use VARBINARY \(length\)\.  If the length is from 65,536 through 2,147,483,647, then use LONGLOB\.  | 
|  DATE  |  DATE  | 
|  TIME  |  TIME  | 
|  TIMESTAMP  |  "If scale is => 0 and =< 6, then: DATETIME \(Scale\) If scale is => 7 and =< 9, then: VARCHAR \(37\)"  | 
|  INT1  |  TINYINT  | 
|  INT2  |  SMALLINT  | 
|  INT4  |  INTEGER  | 
|  INT8  |  BIGINT  | 
|  NUMERIC  |  DECIMAL \(p,s\)  | 
|  REAL4  |  FLOAT  | 
|  REAL8  |  DOUBLE PRECISION  | 
|  STRING  |  If the length is from 1 through 21,845, then use VARCHAR \(length\)\. If the length is from 21,846 through 2,147,483,647, then use LONGTEXT\.  | 
|  UINT1  |  UNSIGNED TINYINT  | 
|  UINT2  |  UNSIGNED SMALLINT  | 
|  UINT4  |  UNSIGNED INTEGER  | 
|  UINT8  |  UNSIGNED BIGINT  | 
|  WSTRING  |  If the length is from 1 through 32,767, then use VARCHAR \(length\)\.  If the length is from 32,768 through 2,147,483,647, then use LONGTEXT\.  | 
|  BLOB  |  If the length is from 1 through 65,535, then use BLOB\. If the length is from 65,536 through 2,147,483,647, then use LONGBLOB\. If the length is 0, then use LONGBLOB \(full LOB support\)\.  | 
|  NCLOB  |  If the length is from 1 through 65,535, then use TEXT\. If the length is from 65,536 through 2,147,483,647, then use LONGTEXT with ucs2 for CHARACTER SET\. If the length is 0, then use LONGTEXT \(full LOB support\) with ucs2 for CHARACTER SET\.  | 
|  CLOB  |  If the length is from 1 through 65,535, then use TEXT\. If the length is from 65,536 through 2147483647, then use LONGTEXT\. If the length is 0, then use LONGTEXT \(full LOB support\)\.  | 