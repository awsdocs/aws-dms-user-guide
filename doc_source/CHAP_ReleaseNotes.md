# AWS DMS release notes<a name="CHAP_ReleaseNotes"></a>

Following, you can find release notes for current and previous versions of AWS Database Migration Service \(AWS DMS\)\.

AWS DMS uses a semantic versioning scheme to identify a service release\. A version consists of a three\-component number in the format of X\.Y\.Z where X represents an *epic* version, X\.Y represents a *major* version, and Z represents a *minor* version \(**Epic**\.**Major**\.**Minor**\)\.

**Note**  
You can upgrade any version of AWS Database Migration Service to any later version\. To stay current with the latest features, enhancements, and performance improvements, upgrade to AWS DMS version 3\.4\.4\.

## AWS Database Migration Service 3\.4\.4 release notes<a name="CHAP_ReleaseNotes.DMS344"></a>

The following table shows the new features and enhancements introduced in AWS DMS version 3\.4\.4\. 


| New feature or enhancement | Description | 
| --- | --- | 
| Support TLS encryption and TLS or SASL authentication using Kafka as a target | AWS DMS now supports TLS encryption and TLS or SASL authentication using Amazon MSK and on\-premises Kafka cluster as a target\. For more information on using encryption and authentication for Kafka endpoints, see [Connecting to Kafka using Transport Layer Security \(TLS\)](CHAP_Target.Kafka.md#CHAP_Target.Kafka.TLS)\. | 

The issues resolved in AWS DMS 3\.4\.4 include the following:
+ Improved AWS DMS logging on task failures when using Oracle endpoints\.
+ Improved AWS DMS task execution continues processing when Oracle source endpoints switch roles after Oracle Data Guard fail over\.
+ Improved error handling treats ORA—12561 as a recoverable error when using Oracle endpoints\.
+ Fixed an issue where `EMPTY_BLOB()` and `EMPTY_CLOB()` columns are migrated as null when using Oracle as a source\.
+ Fixed an issue where AWS DMS tasks fail to update records after add column DDL changes when using SQL Server as a source\.
+ Improved PostgreSQL as a source migration by supporting the `TIMESTAMP WITH TIME ZONE` data type\.
+ Fixed an issue where the `afterConnectScript` setting does not work during a full load when using PostgreSQL as a target\.
+ Introduced a new `mapUnboundedNumericAsString` setting to better handle the `NUMERIC` date type without precision and scale when using PostgreSQL endpoints\.
+ Fixed an issue where AWS DMS tasks fail with “0 rows affected” after stopping and resuming the task when using PostgreSQL as a source\.
+ Fixed an issue where AWS DMS fails to migrate the `TIMESTAMP` data type with the `BC` suffix when using PostgreSQL as a source\.
+ Fixed an issue where AWS DMS fails to migrate the `TIMESTAMP` value “±infinity” when using PostgreSQL as a source\.
+ Fixed an issue where empty strings are treated as `NULL` when using S3 as a source with the `csvNullValue` setting set to other values\.
+ Improved the `timestampColumnName` extra connection attribute in a full load with CDC to be sortable during CDC when using S3 as a target\.
+ Improved the handling of binary data types in hex format such as `BYTE`, `BINARY`, and `BLOB` when using S3 as a source\.
+ Fixed an issue where deleted records are migrated with special characters when using S3 as a target\.
+ Fixed an issue to handle empty key values when using Amazon DocumentDB \(with MongoDB compatibility\) as a target\.
+ Fixed an issue where AWS DMS fails to replicate `NumberDecimal` or `Decimal128` columns when using MongoDB or Amazon DocumentDB \(with MongoDB compatibility\) as a source\.
+ Fixed an issue to allow CDC tasks to retry when there is a fail over on MongoDB or Amazon DocumentDB \(with MongoDB compatibility\) as a source\.
+ Added an option to remove the hexadecimal “0x” prefix to `RAW` data type values when using Kinesis, Kafka, or Elasticsearch as a target\.
+ Fixed an issue where validation fails on fixed length character columns when using Db2 LUW as a source\.
+ Fixed an issue where validation fails when only the source data type or the target data type is `FLOAT` or `DOUBLE`\.
+ Fixed an issue where validation fails on `NULL` characters when using Oracle as a source\.
+ Fixed an issue where validation fails on XML columns when using Oracle as a source\.
+ Fixed an issue where AWS DMS tasks crash when there are nullable columns in composite keys using MySQL as a source\.
+ Fixed an issue where AWS DMS fails to validate both `UNIQUEIDENTIFIER` columns from SQL Server source endpoints and UUID columns from PostgreSQL target endpoints\.
+ Fixed an issue where a CDC task does not use an updated source table definition after it is modified\.
+ Improved AWS DMS fail over to treat task failures caused by an invalid user name or password as recoverable errors\.
+ Fixed an issue where AWS DMS tasks fail because of missing LSNs when using RDS for SQL Server as a source\.

## AWS Database Migration Service 3\.4\.3 release notes<a name="CHAP_ReleaseNotes.DMS343"></a>

The following table shows the new features and enhancements introduced in AWS DMS version 3\.4\.3\. 


| New feature or enhancement | Description | 
| --- | --- | 
| New Amazon DocumentDB version | Amazon DocumentDB version 4\.0 is now supported as a source\. | 
| New MariaDB version | MariaDB version 10\.4 is now supported as both a source and target\. | 
| Support for AWS Secrets Manager integration | You can store the database connection details \(user credentials\) for supported endpoints securely in AWS Secrets Manager\. You can then submit the corresponding secret instead of plain\-text credentials to AWS DMS when you create or modify an endpoint\. AWS DMS then connects to the endpoint databases using the secret\. For more information on creating secrets for AWS DMS endpoints, see [Using secrets to access AWS Database Migration Service endpoints](CHAP_Security.md#security_iam_secretsmanager)\. | 
| Larger options for C5 and R5 replication instances | You can now create the following larger replication instance sizes: C5 sizes up to 96 vCPUs and 192 GiB of memory and R5 sizes up to 96 vCPUs and 768 GiB of memory\. | 
| Amazon Redshift performance improvement | AWS DMS now supports parallel apply when using Redshift as a target to improve the performance of on\-going replication\. For more information, see [Multithreaded CDC load task settings for Amazon Redshift](CHAP_Target.Redshift.md#CHAP_Target.Redshift.ParallelApply)\. | 

The issues resolved in AWS DMS 3\.4\.3 include the following:
+ Fixed an issue where commit timestamp became “1970\-01\-01 00:00:00” for deferred events when using Db2 LUW as a source\.
+ Fixed an issue where AWS DMS tasks failed with an `NVARCHAR` column as primary key when using SQL Server as a source with Full LOB mode\.
+ Fixed an issue of missing records during cached changes phase when using SQL Server as a source\.
+ Fixed an issue where records were skipped after AWS DMS tasks were resumed when using RDS for SQL Server as a source\.
+ Fixed an issue where AWS DMS ASSERTION logging component generates large logs for SQL Server\.
+ Fixed an issue where data validation failed during CDC phase due to column parsing overflow when using MySQL as a source\.
+ Fixed an issue where AWS DMS tasks crashed due to a segmentation fault during data validation when using PostgreSQL as a target\.
+ Fixed an issue where data validation failed on DOUBLE data type during CDC when using PostgreSQL as a source and a target\.
+ Fixed an issue where records inserted by copy command were not replicated correctly when using PostgreSQL as a source and Redshift as a target\.
+ Fixed a data loss issue during cached changes phase when using PostgreSQL as a source\.
+ Fixed an issue which could potentially cause either data loss or record duplicates when using PostgreSQL as a source\.
+ Fixed an issue where schemas with mixed cases failed to migrate with pglogical when using PostgreSQL as a source\.
+ Fixed an issue where the Last Failure Message did not contain the ORA error when using Oracle as a source\.
+ Fixed an issue where AWS DMS tasks failed to build UPDATE statements when using Oracle as a target\.
+ Fixed an issue where AWS DMS tasks did not replicate data when using Oracle 12\.2 as a source with ASM and Pluggable Database configuration\.
+ Improved record parsing by preserving quotes to be compliant with RFC 4180 when using S3 as a source\.
+ Improved the handling of `timestampColumnName` so that the column from Full Load is sortable with that from CDC\.
+ By introducing a new endpoint setting `MessageMaxBytes`, fixed an issue where AWS DMS tasks failed when there are LOB elements larger than 1MB \.
+ Fixed an issue where AWS DMS tasks crashed due to a segmentation fault when using Redshift as a target\.
+ Improved error logging for Redshift test connection\.
+ Fixed an issue where AWS DMS did not transfer all documents from MongoDB to DocumentDB during Full Load\.
+ Fixed an issue where AWS DMS tasks reported fatal error when no tables were included in the table mapping rules\.
+ Fixed an issue where schemas and tables created before restarting AWS DMS tasks did not replicate to the target when using MySQL as a source\.
+ Fixed an issue where wildcard escape \[\_\] cannot escape wildcard “\_” in exclude rule when using MySQL as a source\.
+ Fixed an issue where column of data type UNSIGNED BIGINT did not replicate correctly when using MySQL as a source\.

## AWS Database Migration Service 3\.4\.2 release notes<a name="CHAP_ReleaseNotes.DMS342"></a>

The following table shows the new features and enhancements introduced in AWS DMS version 3\.4\.2\. 


| New feature or enhancement | Description | 
| --- | --- | 
| Support for privately connecting your Amazon Virtual Private Cloud \(Amazon VPC\) to AWS Database Migration Service \(DMS\) without requiring an internet gateway, NAT device, VPN connection, or AWS Direct Connect connection\.  | You can now connect to and access AWS DMS from your Amazon VPC through a VPC interface endpoint that you create\. This interface endpoint allows you to isolate all network activity of your AWS DMS replication instance within the Amazon network infrastructure\. By including a reference to this interface endpoint in all API calls to AWS DMS using the AWS CLI or an SDK, you ensure that all AWS DMS activity remains invisible to the public Internet\. For more information, see [Infrastructure security in AWS Database Migration Service](CHAP_Security.md#infrastructure-security)\. This feature is available using all supported AWS DMS engine versions\.  | 
| CDC date\-based folder partitioning using Amazon S3 as a target |  AWS DMS now supports date\-based folder partitioning when replicating data using S3 as a target\. For more information, see [Using date\-based folder partitioning](CHAP_Target.S3.md#CHAP_Target.S3.DatePartitioning)\.  | 

The issues resolved in AWS DMS 3\.4\.2 include the following:
+ Added a `STATUPDATE` option when performing a migration using Redshift as a target\.
+ Improved validation tasks by introducing a new setting\. `ValidQueryCdcDelaySecond` delays the first validation query on both source and target endpoints to help reduce resource contention when migration latency is high\.
+ Fixed an issue where AWS DMS took a long time to start validation tasks\.
+ Fixed an issue where empty records were generated when starting or stopping replication tasks using S3 as a target\.
+ Fixed an issue where tasks got stuck after a full load completed\.
+ Fixed an issue where tasks got stuck when a source table has data errors while using S3 as a source \.
+ Fixed an issue where tasks got stuck while starting when the user account of the source endpoint is disabled\.
+ Fixed an issue where tasks crashed when using PostgreSQL as a source with `REPLICA IDENTITY FULL`\.
+ Fixed an issue where tasks missed transactions when using PostgreSQL as a source with **pglogical** plugin\.
+ Fixed an issue when AWS DMS didn't delete compressed source files when using Redshift as a target\.
+ Fixed an issue where validation tasks reported false negatives when using MySQL as both source and target with data type `BIGINT UNSIGNED`\.
+ Fixed an issue where validation tasks reported false positives when using SQL Server as a source with a primary key column as CHAR type\.
+ Fixed an issue where AWS DMS doesn't clear target objects when using `start-replication` to start replication tasks using S3 as a target\.
+ Fixed several issues on data validation when using Db2 as a source\.
+ Fixed an issue where validation tasks got stuck when using SQL Server as a source with VARCHAR column as primary key\.
+ Added support for data type TIMESTAMP WITH TIMEZONE when using PostgreSQL as a source

## AWS Database Migration Service 3\.4\.1 Beta release notes<a name="CHAP_ReleaseNotes.DMS341"></a>

The following table shows the new features and enhancements introduced in AWS DMS version 3\.4\.1 Beta\. 


| New feature or enhancement | Description | 
| --- | --- | 
| New MongoDB version |  MongoDB version 4\.0 is now supported as a source\.  | 
| TLS 1\.2 support for SQL Server |  AWS DMS now supports TLS 1\.2 for SQL Server endpoints\.  | 

The issues resolved in AWS DMS 3\.4\.1 Beta include the following:
+ Improved Oracle 19c TDE support\.
+ Improved support of utf8mb4 character set and identity data type using Redshift as a target\.
+ Improved replication task failure handling when using MySQL as a source and binary log is not present\.
+ Improved data validation support on various data types and character sets\.
+ Improved null value handling with a new endpoint setting `IncludeNullAndEmpty` when using Kinesis and Kafka as a target\.
+ Improved error logging and handling when using Kafka as a target\.
+ Improved DST time offset when using SQL Server as a source\.
+ Fixed an issue where replication tasks try to create existing tables for Oracle as a target\.
+ Fixed an issue where replication tasks get stuck after the database connection is killed when using Oracle as a source\.
+ Fixed an issue where replication tasks failed to detect and reconnect to the new primary when using SQL Server as a source with AlwaysON setting\.
+ Fixed an issue where replication tasks do not add a `"D"` for `"OP"` column under certain conditions for S3 as a target\.

## AWS Database Migration Service 3\.4\.0 Beta release notes<a name="CHAP_ReleaseNotes.DMS340"></a>

The following table shows the new features and enhancements introduced in AWS DMS version 3\.4\.0


| New feature or enhancement | Description | 
| --- | --- | 
| New MySQL version |  AWS DMS now supports MySQL version 8\.0 as a source, except when the transaction payload is compressed\.  | 
| TLS 1\.2 support for MySQL |  AWS DMS now supports TLS 1\.2 for MySQL endpoints\.  | 
| New MariaDB version |  AWS DMS now supports MariaDB version 10\.3\.13 as a source\.  | 
| Non\-SysAdmin access to self\-managed Microsoft SQL Server sources |  AWS DMS now supports access by non\-SysAdmin users to on\-premise and EC2\-hosted SQL Server source endpoints\.  This feature is currently in Beta\. If you want to try it out, contact AWS support for more information\.   | 
| CDC tasks and Oracle source tables created using CREATE TABLE AS |  AWS DMS now supports both full\-load and CDC and CDC\-only tasks running against Oracle source tables created using the `CREATE TABLE AS` statement\.  | 

The issues resolved in AWS DMS 3\.4\.0 include the following:
+ Improved premigration task assessments\. For more information, see [Enabling and working with premigration assessments for a task](CHAP_Tasks.AssessmentReport.md)\.
+ Improved data validation for float, real, and double data types\.
+ Improved Amazon Redshift as a target by better handling this error: "The specified key does not exist\."
+ Supports multithreaded CDC load task settings, including `ParallelApplyThreads`, `ParallelApplyBufferSize`, and `ParallelApplyQueuesPerThread`, for Amazon Elasticsearch Service \(Amazon ES\) as a target\.
+ Improved Amazon ES as a target by supporting its use of composite primary keys\.
+ Fixed an issue where test connection fails when using PostgreSQL as a source and the password has special characters in it\.
+ Fixed an issue with using SQL Server as a source when some `VARCHAR` columns are truncated\.
+ Fixed an issue where AWS DMS does not close open transactions when using Amazon RDS SQL Server as a source\. This can result in data loss if the polling interval parameter is set incorrectly\. For more information on how to setup a recommended polling interval value, see [Using a Microsoft SQL Server database as a source for AWS DMS](CHAP_Source.SQLServer.md)\. 
+ Fixed an issue for Oracle Standby as source where CDC tasks would stop unexpectedly when using Binary Reader\.
+ Fixed an issue for IBM DB2 for LUW where the task failed with the message "The Numeric literal 0 is not valid because its value is out of range\."
+ Fixed an issue for a PostgreSQL to PostgreSQL migration when a new column was added on the PostgreSQL source and the column was created with a different data type than the data type for which the column was originally created on the source\.
+ Fixed an issue with a MySQL source the migration task stopped unexpectedly when it was unable to fetch binlogs\.
+ Fixed an issue related to an Oracle target when `BatchApply` was being used\.
+ Fixed an issue for MySQL and MariaDb when migrating the `TIME` data type\.
+ Fixed an issue for an IBM DB2 LUW source where migrating tables with LOBs fail when the tables don't have a primary key or unique key\.

## AWS Database Migration Service 3\.3\.4 release notes<a name="CHAP_ReleaseNotes.DMS334"></a>

The issues resolved in AWS DMS 3\.3\.4 include the following:
+ Fixed an issue where transactions are dropped or duplicated when using PostgreSQL as a source\.
+ Improved the support of using dollar sign \($\) in schema names\.
+ Fixed an issue where replication instances do not close open transactions when using RDS SQL Server as a source\.
+ Fixed an issue where test connection fails when using PostgreSQL as a source and the password has special characters in it\.
+ Improved Amazon Redshift as a target by better handling this error: "The specified key does not exist\."
+ Improved data validation support on various data types and character sets\.
+ Fixed an issue where replication tasks try to create existing tables for Oracle as a target\.
+ Fixed an issue where replication tasks do not add a `"D"` for `"OP"` column under certain conditions for Amazon S3 as a target\.

## AWS Database Migration Service 3\.3\.3 release notes<a name="CHAP_ReleaseNotes.DMS333"></a>

The following table shows the new features and enhancements introduced in AWS DMS version 3\.3\.3\.


| New feature or enhancement | Description | 
| --- | --- | 
| New PostgreSQL version  |  PostgreSQL version 12 is now supported as a source and target\.  | 
| Support for composite primary key with Amazon Elasticsearch Service as target  |  As of AWS DMS 3\.3\.3, use of a composite primary key is supported by Amazon ES targets\.  | 
| Support for Oracle extended data types  |  Oracle extended data types for both Oracle source and targets are now supported\.  | 
| Increased number of AWS DMS resources per account | The limit on the number of AWS DMS resources you can create has increased\. For more information, see [Quotas for AWS Database Migration Service](CHAP_Limits.md)\. | 

The issues resolved in AWS DMS 3\.3\.3 include the following:
+ Fixed an issue where a task crashes using a specific update statement with Parallel Apply in Amazon Kinesis\.
+ Fixed an issue where a task crashes on the ALTER TABLE statement with Amazon S3 as a target\.
+ Fixed an issue where values on polygon columns are truncated when using Microsoft SQL Server as a source\.
+ Fixed an issue on Unicode converter of JA16SJISTILDE and JA16EUCTILDE when using Oracle as a source\.
+ Fixed an issue where MEDIUMTEXT and LONGTEXT columns failed to migrate from MySQL to S3 comma\-separated value \(CSV\) format\.
+ Fixed an issue where boolean columns were transformed to incorrect types with Apache Parquet output\.
+ Fixed an issue with extended varchar columns in Oracle\.
+ Fixed an issue where data validation tasks failed due to certain timestamp combinations\.
+ Fixed an issue with Sybase data definition language \(DDL\) replication\.
+ Fixed an issue involving an Oracle Real Application Clusters \(RAC\) source crashing with Oracle Binary Reader\.
+ Fixed an issue with validation for Oracle targets with schema names' case\.
+ Fixed an issue with validation of IBM Db2 versions 9\.7 and 10\.
+ Fixed an issue for a task not stopping two times with `StopTaskCachedChangesApplied` and `StopTaskCachedChangesNotApplied` enabled\.

## AWS Database Migration Service \(AWS DMS\) 3\.3\.2 release notes<a name="CHAP_ReleaseNotes.DMS332"></a>

The following table shows the features and bug fixes for version 3\.3\.2 of AWS Database Migration Service \(AWS DMS\)\.


| New feature or enhancement | Description | 
| --- | --- | 
| Neptune as a target |  Adds support to migrate data from an SQL relational database source to Amazon Neptune as a target\. In addition to any table mapping to select and transform the SQL source data, this AWS DMS version supports additional mechanisms to map the selected relational data to a graph\-mapping format used to load the target Neptune graph database\.  | 
| Data transformations |  Adds support in table mapping for using expressions with selected rule actions of the transformation rule type\. For more information, see [Using transformation rule expressions to define column content](CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Expressions.md)\. This also includes support for adding the before image of updated or deleted columns in the JSON written to either Kinesis Data Streams or Apache Kafka as a target\. This includes a choice of all columns from the source, the primary key column, or columns without LOBs as a separate field\. For more information, see [Before image task settings](CHAP_Tasks.CustomizingTasks.TaskSettings.BeforeImage.md)\.  | 
| New Oracle version |  Oracle version 19c is now supported as a source and target\. Note that this support currently doesn't include the use of Oracle transparent data encryption \(TDE\) with Oracle version 19c as a source\.  | 
| New SQL Server version |  SQL Server 2019 is now supported as a source and target\.  | 
| Support for LOBs in Kafka as a target |  Replicating LOB data to a Kafka target is now supported\.  | 
| Support for partial validation of LOBs |  Adds support for partial validation of LOBs for MySQL, Oracle, and PostgreSQL endpoints\.  | 

The issues resolved are as follows:
+ Multiple data validation related fixes:
  + Disabled validation for Oracle long types\.
  + Improved retry mechanisms based on driver error codes\.
  + Fixed an issue where validation hangs indefinitely while fetching data from source and target\.
  + Fixed an issue with PostgreSQL UUID type validation\.
  + Fixed multiple stuck validation issues\.
+ Fixed an issue where data loss occurs with S3 as a target when the replication task process crashes on the replication instance\.
+ Fixed an issue where columns of timestamp data types do not migrate when using attribute mappings in Kinesis and Kafka\.
+ Fixed an issue where replication tasks with Oracle or PostgreSQL as a source enter a "failed" state when you try to stop them\.
+ Fixed an issue where batch apply does not work as expected for replications to Redshift when using composite primary keys\.
+ Fixed an out of memory issue on the replication instance when using MariaDB as a source\.
+ Fixed an issue where AWS DMS inserts random values in an S3 target instead of null values as expected\.