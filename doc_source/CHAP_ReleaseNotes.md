# AWS DMS release notes<a name="CHAP_ReleaseNotes"></a>

Following, you can find release notes for current and previous versions of AWS Database Migration Service \(AWS DMS\)\.

AWS DMS uses a semantic versioning scheme to identify a service release\. A version consists of a three\-component number in the format of X\.Y\.Z where X represents an *epic* version, X\.Y represents a *major* version, and Z represents a *minor* version \(**Epic**\.**Major**\.**Minor**\)\.

## AWS Database Migration Service 3\.4\.7 release notes<a name="CHAP_ReleaseNotes.DMS347"></a>

The following table shows the new features and enhancements introduced in AWS Database Migration Service \(AWS DMS\) version 3\.4\.7\. 


| New feature or enhancement | Description | 
| --- | --- | 
| Support Babelfish as a target |  AWS DMS now supports Babelfish as a target\. Using AWS DMS, you can now migrate live data from any AWS DMS supported source to a Babelfish, with minimal downtime\. For more information, see [Using Babelfish as a target for AWS Database Migration Service](CHAP_Target.Babelfish.md)\.  | 
| Support IBM Db2 z/OS databases as a source for full load only |  AWS DMS now supports IBM Db2 z/OS databases as a source\. Using AWS DMS, you can now perform live migrations from Db2 mainframes to any AWS DMS supported target\. For more information, see [Using IBM Db2 for z/OS databases as a source for AWS DMS](CHAP_Source.DB2zOS.md)\.  | 
| Support SQL Server read replica as a source |  AWS DMS now supports SQL Server read replica as a source\. Using AWS DMS, you can now perform live migrations from SQL Server read replica to any AWS DMS supported target\. For more information, see [Using a Microsoft SQL Server database as a source for AWS DMS](CHAP_Source.SQLServer.md)\.  | 
| Support EventBridge DMS events |  AWS DMS supports managing event subscriptions using EventBridge for DMS events\. For more information, see [Working with Amazon EventBridge events and notifications in AWS Database Migration Service](CHAP_EventBridge.md)\.  | 
| Support VPC source and target endpoints |  AWS DMS now supports virtual private cloud \(VPC\) endpoints as sources and targets\. AWS DMS can now connect to any AWS service with VPC endpoints when explicitly defined routes to the services are defined in their AWS DMS VPC\. For more information, see [Configuring VPC endpoints as AWS DMS source and target endpoints](CHAP_VPC_Endpoints.md)\.  | 
| New PostgreSQL version | PostgreSQL version 14\.x is now supported as a source\. | 
| Support Aurora Serverless v2 as a target |  AWS DMS now supports Aurora Serverless v2 as a target\. Using AWS DMS, you can now perform live migrations to Aurora Serverless v2\. For information about supported AWS DMS targets, see [Targets for data migration](CHAP_Target.md)\.  | 
|  New IBM Db2 for LUW versions  |  AWS DMS now supports IBM Db2 for LUW versions 11\.5\.6 and 11\.5\.7 as a source\. Using AWS DMS, you can now perform live migrations from the latest versions of IBM DB2 for LUW\. For information about AWS DMS sources, see [Sources for data migration](CHAP_Source.md)\. For information about supported AWS DMS targets, see [Targets for data migration](CHAP_Target.md)\.  | 

AWS DMS 3\.4\.7 includes the following new or changed behavior and resolved issues:
+ You can now use a date format from the table definition to parse a data string into a date object when using Amazon S3 as a source\.
+ New table statistics counters are now available: `AppliedInserts`, `AppliedDdls`, `AppliedDeletes`, and `AppliedUpdates.`
+ You can now choose the default mapping type when using OpenSearch as a target\.
+ The new `TrimSpaceInChar` endpoint setting for Oracle, PostgreSQL, and SQLServer sources allows you to specify whether to trim data on CHAR and NCHAR data types\.
+ The new `ExpectedBucketOwner` endpoint setting for Amazon S3 prevents sniping when using S3 as a source or target\.
+ For RDS SQL Server, Azure SQL Server, and self\-managed SQL Server — DMS now provides automated setup of MS\-CDC on all tables selected for a migration task that are with or without a PRIMARY KEY, or with a unique index considering the enablement priority for MS\-REPLICATION on self\-managed SQL Server tables with PRIMARY KEY\.
+ Added support for replication of Oracle Partition and sub\-partition DDL Operations during Oracle homogenous migrations\.
+ Fixed an issue where a data validation task crashes with a composite primary key while using Oracle as a source and target\.
+ Fixed an issue with correctly casting a varying character type to a boolean while the target column was pre\-created as a boolean when using Redshift as a target\.
+ Fixed an issue that was causing data truncation for `varchar` data types migrated as `varchar(255)` due to a known ODBC issue when using PostgreSQL as a target\.
+ Fixed an issue where Parallel Hint for the DELETE operation wasn't respected with `BatchApplyEnabled` set to `true` and `BatchApplyPreserveTransaction` set to `false` when using Oracle as a target\.
+ The new `AddTrailingPaddingCharacter` endpoint setting for an Amazon S3 adds padding on string data when using S3 as a target\.
+ The new `max_statement_timeout_seconds` task setting extends the default timeout of endpoint queries\. This setting is currently used by MySQL endpoint metadata queries\.
+ When using PostgreSQL as a target, fixed an issue where a CDC task wasn't properly utilizing the error handling task settings\.
+ Fixed an issue where DMS was unable to correctly identify Redis mode for a Redis Enterprise instance\.
+ Extended the support of `includeOpForFullLoad` extra connection attribute \(ECA\) for the S3 target parquet format\.
+ Introduced a new PostgreSQL endpoint setting `migrateBooleanAsBoolean`\. When this setting is set to `true` for a PostgreSQL to Redshift migration, a boolean will be migrated as varchar\(1\)\. When it is set to `false`, a boolean is migrated as varchar\(15\), which is the default behavior\.
+ When using SQL Server source, fixed a migration issue with `datetime` datatype\. This fix addresses the issue of inserting `Null` when precision is in milliseconds\.
+ For PostgresSQL source with PGLOGICAL, fixed a migration issue when using pglogical and removing a field from the source table during the CDC phase, where the value after the removed field wasn't migrated to the target table\.
+ Fixed a SQL Server Loopback migration issue with Bidirectional replication getting repeated records\.
+ Added a new ECA `mapBooleanAsBoolean` for PostgresSQL as a source\. Using this extra connection attribute , you can override default data type mapping of a PostgresSQL Boolean to a RedShift Boolean data type\.
+ Fixed a migration issue when using SQL Server as source that addresses the ALTER DECIMAL/NUMERIC SCALE not replicating to targets\.
+ Fixed connection issue with SQL Server 2005\.

## AWS Database Migration Service 3\.4\.6 release notes<a name="CHAP_ReleaseNotes.DMS346"></a>

The following table shows the new features and enhancements introduced in AWS Database Migration Service \(AWS DMS\) version 3\.4\.6\. 


| New feature or enhancement | Description | 
| --- | --- | 
| AWS DMS Time Travel | AWS DMS introduces [Time Travel](https://docs.aws.amazon.com/dms/latest/userguide/CHAP_Monitoring.html#CHAP_Monitoring.TimeTravel), a feature granting customers flexibility on their logging capabilities, and enhancing their troubleshooting experience\. With Time Travel, you can store and encrypt AWS DMS logs using Amazon S3, and view, download and obfuscate the logs within a certain time frame\.  | 
| Support Microsoft Azure SQL Managed Instance as a source | AWS DMS now supports Microsoft Azure SQL Managed Instance as a source\. Using AWS DMS, you can now perform live migrations from Microsoft Azure SQL Managed Instance to any AWS DMS supported target\.  For information about AWS DMS sources, see [Sources for data migration](CHAP_Source.md)\. For information about supported AWS DMS targets, see [Targets for data migration](CHAP_Target.md)\. | 
| Support Google Cloud SQL for MySQL as a source | AWS DMS now supports Google Cloud SQL for MySQL as a source\. Using AWS DMS, you can now perform live migrations from Google Cloud SQL for MySQL to any AWS DMS supported target\. For information about AWS DMS sources, see [Sources for data migration](CHAP_Source.md)\. For information about supported AWS DMS targets, see [Targets for data migration](CHAP_Target.md)\. | 
| Support parallel load for partitioned data to S3 | AWS DMS now supports parallel load for partitioned data to Amazon S3, improving the load times for migrating partitioned data from supported database engine source data to Amazon S3\. This feature creates Amazon S3 sub\-folders for each partition of the table in the database source, allowing AWS DMS to run parallel processes to populate each sub\-folder\.  | 
| Support multiple Apache Kafka target topics in a single task |  AWS DMS now supports Apache Kafka multi\-topic targets with a single task\. Using AWS DMS, you can now replicate multiple schemas from a single database to different Apache Kafka target topics using the same task\. This eliminates the need to create multiple separate tasks in situations where many tables from the same source database need to be migrated to different Kafka target topics\.  | 

The issues resolved in AWS DMS 3\.4\.6 include the following:
+ Fixed an issue where columns from UPDATE statements were populated to incorrect columns if the primary key column is not the first column when using Amazon S3 as a target with CSV format\.
+ Fixed an issue where AWS DMS tasks might crash when using the pglogical plugin with `NULL` values in `BYTEA` columns under limited LOB mode when using PostgreSQL as a source\.
+ Fixed an issue where AWS DMS tasks might crash when a large number of source tables are deleted when using PostgreSQL as a source\. 
+ Improved Amazon S3 date\-based folder partitioning by introducing a new Amazon S3 setting `DatePartitionTimezone` to allow partitioning on non\-UTC dates\. 
+ Supported the mapping between data type `TIMESTAMP WITH TIME ZONE` from sources to `TIMESTAMPTZ` when using Redshift as a target
+ Improved the performance of CDC for tasks without wildcard selection rules when using MongoDB or Amazon DocumentDB as a source\. 
+ Fixed an issue where schema names with underscore wildcard and length less than 8 were not captured by AWS DMS tasks when using Db2 LUW as a source\. 
+ Fixed an issue where AWS DMS instances ran out of memory under large data volume when using Amazon ES as a target\. 
+ Improved the performance of data validation by supporting full load validation only tasks\. 
+ Fixed an issue where AWS DMS tasks failed to resume after forced failover when using Sybase as a source\.
+ Fixed an issue where AWS DMS sent warning `Invalid BC timestamp was encountered in column` incorrectly\.

Issues resolved in the DMS 3\.4\.6 maintenance release include the following:
+ Fixed an issue of a task crashing when bulk apply mode is enabled when using Oracle as the source and target\.
+ Fixed an issue so that a full load task properly uses the `ExecuteTimeout` endpoint setting with PostgreSQL as source\.
+ Fixed an issue with migrating Array data type columns when the task is set to limited LOB mode while using PostgreSQL as a source\.
+ Fixed an issue with migrating timestamps with time zone before 1970\-01\-01 when using PostgreSQL as a source\.
+ Fixed an issue where DMS was treating an empty string as null during replication when using SQL Server as a source and target\.
+ Fixed an issue to honor session read and write timeout endpoint settings when using MySQL source/target\.
+ Fixed an issue where a DMS CDC task was downloading full load related files when using Amazon S3 as a source\.
+ Fixed a log crashing issue when `CdcInsertsAndUpdates` and `PreserveTransactions` are both set to `true` when using Amazon S3 as a target\. 
+ Fixed an issue where a task crashed when the ParallelApply\* feature is enabled, but some tables don't have a default primary key when using Amazon Kinesis Data Streams as a source\.
+ Fixed an issue where an error wasn't given for an incorrect StreamArn when using Amazon Kinesis Data Streams as a source\.
+ Fixed an issue where a primary key value as an empty string would cause a task to crash when using OpenSearch as a target\.
+ Fixed an issue where too much disk space was used by data validation\.

## AWS Database Migration Service 3\.4\.5 release notes<a name="CHAP_ReleaseNotes.DMS345"></a>

The following table shows the new features and enhancements introduced in AWS Database Migration Service \(AWS DMS\) version 3\.4\.5\. 


| New feature or enhancement | Description | 
| --- | --- | 
| Support for Redis as a target | AWS DMS now supports Redis as a target\. Using AWS DMS, you can now migrate live data from any AWS DMS supported source to a Redis data store, with minimal downtime\. For information about AWS DMS targets, see [Targets for data migration](CHAP_Target.md)\. | 
| Support for MongoDB 4\.2 and 4\.4 as sources | AWS DMS now supports MongoDB 4\.2 and 4\.4 as sources\. Using AWS DMS, you can now migrate data from MongoDB 4\.2 and 4\.4 clusters to any AWS DMS supported target including Amazon DocumentDB \(with MongoDB compatibility\), with minimal downtime\. For information about AWS DMS sources, see [Sources for data migration](CHAP_Source.md)\.  | 
| Support for multiple databases using MongoDB as a source | AWS DMS now supports migrating multiple databases in one task using MongoDB as a source\. Using AWS DMS, you can now group multiple databases of a MongoDB cluster, and migrate them using one database migration task\. You can migrate to any AWS DMS supported target, including Amazon DocumentDB \(with MongoDB compatibility\), with minimal downtime\.  | 
| Support for automatic segmentation using MongoDB or Amazon DocumentDB \(with MongoDB compatibility\) as a source | AWS DMS now supports automatic segmentation using MongoDB or Amazon DocumentDB as a source\. Using AWS DMS, you can configure database migration tasks to segment the collection of a MongoDB or DocumentDB cluster automatically\. You can then migrate the segments in parallel to any AWS DMS supported target, including Amazon DocumentDB, with minimal downtime\.  | 
| Amazon Redshift full load performance improvement | AWS DMS now supports using parallel threads when using Amazon Redshift as a target during full load\. By taking advantage of the multithreaded full load task settings, you can improve the performance of your initial migration from any AWS DMS supported source to Amazon Redshift\. For information about AWS DMS targets, see [Targets for data migration](CHAP_Target.md)\. | 

The issues resolved in AWS DMS 3\.4\.5 include the following:
+ Fixed an issue where data could potentially be missing or duplicated after resuming, when using PostgreSQL as a source with high transaction concurrency\.
+ Fixed an issue where database migration tasks fail with error **Could not find relation id …** when using PostgreSQL as a source, with the pglogical plugin enabled\. 
+ Fixed an issue where `VARCHAR` columns are not replicated correctly when using PostgreSQL as a source and Oracle as a target\. 
+ Fixed an issue where delete operations are not properly captured when the primary key is not the first column in the table definition, when using PostgreSQL as a source\. 
+ Fixed an issue where database migration tasks miss LOB updates in a special metadata setting when using MySQL as a source\.
+ Fixed an issue where `TIMESTAMP` columns are treated as `DATETIME` in full LOB mode when using MySQL version 8 as a source\. 
+ Fixed an issue where database migration tasks fail when parsing `NULL DATETIME` records when using MySQL 5\.6\.4 and above as a source\.
+ Fixed an issue where database migration tasks get stuck after encountering a **Thread is exiting** error when using Amazon Redshift as a target with parallel apply\. 
+ Fixed an issue where data could potentially be lost, when database migration tasks disconnect with a Amazon Redshift target endpoint during batch\-apply CDC\. 
+ Improved the performance of full load by calling `ACCEPTINVCHARS` when using Amazon Redshift as a target\. 
+ Fixed an issue where duplicated records are replicated when reverting from one\-by\-one mode to parallel apply mode using Amazon Redshift as a target\.
+ Fixed an issue where database migration tasks do not switch Amazon S3 object ownership to bucket owner with `cannedAclForObjects=bucket_owner_full_control` when using Amazon S3 as a target\.
+ Improved AWS DMS by supporting multiple archive destinations with ECA `additionalArchivedLogDestId` when using Oracle as a source\.
+ Fixed an issue where database migration tasks fail with error `OCI_INVALID_HANDLE` while updating a LOB column in full LOB mode\. 
+ Fixed an issue where `NVARCHAR2` columns are not migrated properly during CDC when using Oracle as a source\.
+ Improved AWS DMS by enabling `SafeguardPolicy` when using RDS for SQL Server as a source\. 
+ Fixed an issue where database migration tasks report error on `rdsadmin` when using a non\-RDS SQL Server source\.
+ Fixed an issue where data validation fails with UUID as the primary key in a partition setting when using SQL Server as a source\. 
+ Fixed an issue where full load plus CDC tasks might fail if the required LSN cannot be found in the database log when using Db2 LUW as a source\.
+ Improved AWS DMS by supporting custom CDC timestamps when using MongoDB as a source\. 
+ Fixed an issue where database migration tasks get stuck when stopping, using MongoDB as a source, when the MongoDB driver errors on `endSessions`\. 
+ Fixed an issue where AWS DMS fails to update non\-primary fields when using DynamoDB as a target 
+ Fixed an issue where data validation reports false positive mismatches on `CLOB` and `NCLOB` columns\.
+ Fixed an issue where data validation fails on whitespace\-only records when using Oracle as a source\.
+ Fixed an issue where database migration tasks crash when truncating a partitioned table\.
+ Fixed an issue where database migration tasks fail when creating the `awsdms_apply_exceptions` control table\. 
+ Extended support of the `caching_sha2_password` authentication plugin when using MySQL version 8\.

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
| Amazon Redshift performance improvement | AWS DMS now supports parallel apply when using Redshift as a target to improve the performance of on\-going replication\. For more information, see [Multithreaded task settings for Amazon Redshift](CHAP_Target.Redshift.md#CHAP_Target.Redshift.ParallelApply)\. | 

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
+ Fixed an issue for MySQL and MariaDB when migrating the `TIME` data type\.
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