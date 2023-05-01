# AWS DMS release notes<a name="CHAP_ReleaseNotes"></a>

Following, you can find release notes for current and previous versions of AWS Database Migration Service \(AWS DMS\)\.

AWS DMS doesn't differentiate between major and minor versions\. For example, upgrading from version 3\.4\.x to 3\.5\.x isn't considered a major upgrade, so all changes should be backward\-compatible\.

## AWS Database Migration Service 3\.5\.0 Beta release notes<a name="CHAP_ReleaseNotes.DMS350"></a>

**Important**  
AWS DMS 3\.5\.0 is a beta version of the replication instance engine\. AWS DMS supports this version the same as all previous releases\. But we recommend that you test AWS DMS 3\.5\.0 Beta before using it for production purposes\.

The following table shows the new features and enhancements introduced in AWS Database Migration Service \(AWS DMS\) version 3\.5\.0 Beta\. 


| New feature or enhancement | Description | 
| --- | --- | 
| Time Travel for Oracle and Microsoft SQL Server | You can now use Time Travel in all AWS Regions with DMS\-supported Oracle, Microsoft SQL Server, and PostgreSQL source endpoints, and DMS\-supported PostgreSQL and MySQL target endpoints\. | 
| S3 validation | AWS DMS now supports validating replicated data in Amazon S3 target endpoints\. For information about validating Amazon S3 target data, see [Amazon S3 target data validation](CHAP_Validating_S3.md)\. | 
| Glue Catalog Integration | AWS Glue is a service that provides simple ways to categorize data, and consists of a metadata repository known as AWS Glue Data Catalog\. You can now integrate an AWS Glue Data Catalog with your Amazon S3 target endpoint and query Amazon S3 data through other AWS services such as Amazon Athena\. For more information, see [Using AWS Glue Data Catalog with an Amazon S3 target for AWS DMS](CHAP_Target.S3.md#CHAP_Target.S3.GlueCatalog)\.  | 
| Parallel apply for DocumentDB as a target | Using DocumentDB as the target with new ParallelApply\* task settings, AWS DMS now supports a maximum of 5000 records per second during CDC replication\. For more information, see [Using Amazon DocumentDB as a target for AWS Database Migration Service](CHAP_Target.DocumentDB.md)\.  | 
| Customer Centric Logging | You can now examine and manage task logs more effectively with AWS DMS version 3\.5\.0\. For information about viewing and managing AWS DMS task logs, see [Viewing and managing AWS DMS task logs](CHAP_Monitoring.md#CHAP_Monitoring.ManagingLogs)\.  | 
| SASL\_PLAIN mechanism for Kafka target endpoints | You can now use SASL\_PLAIN authentication to support Kafka MSK target endpoints\. | 
| Replication of XA transactions in MySQL  | You can now use XA transactions on your MySQL DMS source\. Prior to DMS 3\.5\.0, DML changes applied as part of XA transactions weren’t replicated correctly\. | 
| Oracle Extended Data Types  | AWS DMS now supports replication of extended data types in Oracle version 12\.2 and later\.  | 
| PostgreSQL Source JSONB Data Type  | AWS DMS now supports JSONB data types for a PostgreSQL source\. | 
| Db2 LUW PureScale Environment  | AWS DMS now supports replication from a Db2 LUW PureScale environment\. This functionality is only supported using the Start processing changes from source change position option\. | 
| Oracle Source Open Transactions  | AWS DMS 3\.5\.0 improves the methodology of handling open transactions when starting a CDC\-Only task from the Start Position for an Oracle source\. | 
| SQL Server source with READ\_COMMITTED\_SNAPSHOT option | When using a Microsoft SQL Server source database with the READ\_COMMITTED\_SNAPSHOT option set to TRUE, you can replicate DML changes correctly by setting the forceDataRowLookup connection attribute\.  | 

AWS DMS 3\.5\.0 includes the following resolved issues:


**Issues resolved in AWS DMS 3\.5\.0 launched on 17\-March\-2023**  

| Topic | Resolution | 
| --- | --- | 
| Oracle—comparing special case for string that was converted from numeric | Fixed an issue for Oracle source where filtering rules weren't working as expected for a numeric column when data type transformation to string existed for the same column\. | 
| On\-premises SQL Server AG enhancements | Improved efficiency of connection handling with SQL Server source in AlwaysOn configuration by eliminating unnecessary connections to replicas that aren't used by DMS\. | 
| SQL Server HIERARCHYID internal conversion | Fixed an issues with SQL Server Source where HIERARCHYID data type was replicated as VARCHAR\(250\) instead of HIERARCHYID to SQL Server target\. | 
| S3 target move task fix | Fixed an issue when moving a task with an S3 target would take a very long time, appear frozen or never complete\. | 
| SASL Plain mechanism of Kafka | Introduced support for SASL Plain authentication method for Kafka MSK target endpoint\. | 
| Parallel Load/Apply fails due to \_type parameter with Opensearch 2\.x | Fixed an issue for Opensearch 2\.x target where parallel load or parallel apply would fail due to lack of support for \_type parameter\. | 
| Support table mapping filter with mixed operators | Removed a limitation where only one filter could be applied on a column\. | 
| S3, Kinesis, Kafka endpoints — alter\-based lob columns migration in CDC phase | Fixed an issue for Kinesis, Kafka and S3 targets where data in LOB columns added during CDC wasn't replicated\. | 
| MongoDB driver upgrade | Upgraded the MongoDB driver to v1\.23\.2\. | 
| Kafka driver update | Upgraded the Kafka driver from 1\.5\.3 to 1\.9\.2\. | 
| S3 endpoint setting was not working properly | Fixed an issue for S3 target where the AddTrailingPaddingCharacter endpoint setting was not working when data contained the character specified as the delimiter for the S3 target\. | 
| Kinesis target task would crash | Fixed an issue for Kinesis target where a task would crash when PK value was empty and detailed debug was enabled\. | 
| When S3 targets' column names were shifted by one position | Fixed an issue for an S3 target where column names where shifted by one position when AddColumnName was set to true and TimestampColumnName was set to ""\.  | 
| Improved logging LOB truncation warning | Improved warning logging for LOB truncation for SQL Server source to include the select statement used to retrieve the LOB\. | 
| Add Fatal error to avoid DMS task crashes if TDE password is wrong\. | Introduced meaningful error message and eliminated task crash issue in situations where DMS task was failing with no error message due incorrect TDE password for Oracle as a source\. | 
| Allows PostgreSQL CTAS\(Create table as selected\) DDL's migration during CDC\. | Removed limitations of DMS not being able to replicate PostgreSQL CTAS \(create table as selected\) DDLs during CDC\. | 
| Fix pg\_logical task crash when table columns are dropped in CDC\. | Fixed an issue for PostgreSQL source with S3 target where columns were misaligned on the target when support for LOBs was disabled and LOBs were present\. | 
| Fix memory leak in MySQL connection handling | Fixed an issue for MySQL source where task memory consumption was increasing continuously\. | 
| Oracle source endpoint setting – ConvertTimestampWithZoneToUTC | Set this attribute to true to convert the timestamp value of 'TIMESTAMP WITH TIME ZONE' and 'TIMESTAMP WITH LOCAL TIME ZONE' columns to UTC\. By default the value of this attribute is 'false' and data is replicated using the source database timezone\. | 
| Oracle source \- DataTruncationErrorPolicy to SUSPEND\_TABLE not working | Fixed an issue for Oracle source with S3 target where tables were not suspended while the DataTruncationErrorPolicy task setting was set to SUSPEND\_TABLE\. | 
| SQL Server fail on long schema/table while building query clause | Fixed an issues for SQL Server source where task would fail or become unresponsive when selection rule contained comma separated list of tables\. | 
| Secret Manager authentication with MongoDB endpoint | Fixed an issue for MongoDB and DocumentDB endpoints where secret manager based authentication wasn't working\. | 
| DMS truncating the data during CDC for a multi\-byte varchar column when NLS\_NCHAR\_CHARACTERSET is set to UTF8 | Fixed an issues for Oracle source with Oracle target where data was being truncated for multi\-byte VARCHAR columns with NLS\_NCHAR\_CHARACTERSET set to UTF8\. | 
| filterTransactionsOfUser ECA for Oracle LogMiner | Added an Extra Connection Attribute \(ECA\) filterTransactionsOfUser to allow DMS to ignore transactions from a specified user when replicating from Oracle using LogMiner\.  | 
| SQL Server Setting recoverable error when lsn missing from backup | Fixed an issue for Azure SQL Database where task would not fail on missing LSN\. | 

## AWS Database Migration Service 3\.4\.7 release notes<a name="CHAP_ReleaseNotes.DMS347"></a>

The following table shows the new features and enhancements introduced in AWS Database Migration Service \(AWS DMS\) version 3\.4\.7\. 


| New feature or enhancement | Description | 
| --- | --- | 
| Support Babelfish as a target |  AWS DMS now supports Babelfish as a target\. Using AWS DMS, you can now migrate live data from any AWS DMS supported source to a Babelfish, with minimal downtime\. For more information, see [Using Babelfish as a target for AWS Database Migration Service](CHAP_Target.Babelfish.md)\.  | 
| Support IBM Db2 z/OS databases as a source for full load only |  AWS DMS now supports IBM Db2 z/OS databases as a source\. Using AWS DMS, you can now perform live migrations from Db2 mainframes to any AWS DMS supported target\. For more information, see [Using IBM Db2 for z/OS databases as a source for AWS DMS](CHAP_Source.DB2zOS.md)\.  | 
| Support SQL Server read replica as a source |  AWS DMS now supports SQL Server read replica as a source\. Using AWS DMS, you can now perform live migrations from SQL Server read replica to any AWS DMS supported target\. For more information, see [Using a Microsoft SQL Server database as a source for AWS DMS](CHAP_Source.SQLServer.md)\.  | 
| Support EventBridge DMS events |  AWS DMS supports managing event subscriptions using EventBridge for DMS events\. For more information, see [Working with Amazon EventBridge events and notifications in AWS Database Migration Service](CHAP_EventBridge.md)\.  | 
| Support VPC source and target endpoints |  AWS DMS now supports Amazon Virtual Private Cloud \(VPC\) endpoints as sources and targets\. AWS DMS can now connect to any AWS service with VPC endpoints when explicitly defined routes to the services are defined in their AWS DMS VPC\. Upgrades to AWS DMS versions 3\.4\.7 and higher require that you first configure AWS DMS to use VPC endpoints or to use public routes\. This requirement applies to source and target endpoints for Amazon S3, Amazon Kinesis Data Streams, AWS Secrets Manager, Amazon DynamoDB, Amazon Redshift, and Amazon OpenSearch Service\.  For more information, see [Configuring VPC endpoints as AWS DMS source and target endpoints](CHAP_VPC_Endpoints.md)\.  | 
| New PostgreSQL version | PostgreSQL version 14\.x is now supported as a source and as a target\. | 
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
+ As of October 17, 2022, DMS 3\.4\.7 now supports Generation 6 Amazon EC2 instance classes for replication instances\.
+ As of November 25, 2022, with DMS 3\.4\.7 you can convert database schemas and code objects using **DMS Schema Conversion**, and discover databases in your network environment that are good candidates for migration using **DMS Fleet Advisor**\.
+ As of November 25, 2022, DMS Studio is retired\.
+ As of January 31, 2023, DMS Schema Conversion supports Aurora MySQL and Aurora PostgreSQL as a target data provider\.
+ As of March 6, 2023, you can generate right sized target recommendations for your source databases with DMS Fleet Advisor\. 
+ As of March 6, 2023, AWS DMS supports the AWS managed policy that allows publishing metric data points to Amazon CloudWatch\.


**Issues resolved in the DMS 3\.4\.7 maintenance release dated 22\-February\-2023**  

| Topic | Resolution | 
| --- | --- | 
| SQL Server AG replicas as a source | Added support for SQL Server source in AlwaysOn configuration where the listener TCP port differed from replica TCP port\. | 
| Data loss with Amazon Redshift as a target | Fixed an issue for Redshift target where in some rare cases unexpected Redshift restart could have caused missing data on target\. | 
| SQL Server source safeguard support | Fixed an issue for SQL Server source where the DMS task could fail with an error indicating inability to read transaction log backups when Endpoint Setting "SafeguardPolicy": "EXCLUSIVE\_AUTOMATIC\_TRUNCATION" is specified\. | 
| Data validation task failure for Oracle as a source | Fixed an issue for Oracle source where DMS task could fail on data validation due to incorrectly identified Primary Key values\. | 
| Kinesis before image data issue | Fixed an issue for streaming targets \(Kinesis, Kafka\) where the "EnableBeforeImage" task setting was working only for character data types\. | 
| Time Travel log files | Fixed an issue for the Time Travel feature where DMS was creating zero byte time travel log files when the source is idle\. | 


**Issues resolved in the DMS 3\.4\.7 maintenance release dated 16\-December\-2022**  

| Topic | Resolution | 
| --- | --- | 
| BatchApplyEnabled | Fixed an issue of excessive logging when BatchApplyEnabled is set to True\.  | 
| New MongoDB endpoint setting–FullLoadNoCursorTimeout | MongoDB endpoint setting FullLoadNoCursorTimeout specifies NoCursorTimeout for the full load cursor\. NoCursorTimeout is a MongoDB connection setting that prevents the server from closing the cursor if idle\. | 
| MongoDB—Filter function for single column segmentation | New filter function improves performance for migrating MongoDB databases using a single column for segmentation\. | 
| MongoDB to Redshift | When migrating from MongoDB to Redshift, if the MongoDB collection has binary data type, fixed an issue where DMS was not creating the target table on Redshift\. | 
| New MongoDB SocketTimeoutMS connection attribute | New MongoDB SocketTimeoutMS extra connection attribute configures the connection timeout for MongoDB clients in units of milliseconds\. If the value is less than or equal to zero, then the MongoDB client default is used\. | 
| Fixed issue causing an Amazon Kinesis task to crash | When migrating to Amazon Kinesis Data Streams as a target, fixed an issue handling null values if a primary key wasn't present in the table\. | 
| Oracle NULL PK/UK data validation supported | Removed the limitation that data validation of NULL PK/UK values aren't supported\. | 
| Oracle to Amazon S3 | When migrating from Oracle to Amazon S3, fixed an issue where a few records were incorrectly migrated as NULL\. | 
| Oracle Standby | When using Oracle Standby as a source, added the ability for DMS to handle open transactions\. | 
| Oracle to Oracle migration with SDO\_GEOMETRY spatial data type | When migrating from Oracle to Oracle, fixed an issue where the task failed if the table had an SDO\_GEOMERY column present in the DDL\. | 
| Oracle as source | When using Oracle as a source, fixed an issue where DMS occasionally skips an Oracle redo log sequence number\. | 
| Oracle as source—missing archive/online redo logs | When using Oracle as a source, fixed an issue so that the DMS task fails when archive logs are missing\.  | 
| Fixed—DMS occasionally skips Oracle standby redo log | When using Oracle as a source, fixed an issue where DMS occasionally skips an Oracle redo log sequence number\. | 
| Fixed—Oracle to Oracle spatial datatypes not replicating during CDC | When replicating from Oracle to Oracle, fixed an issue where spatial datatypes were not replicating during CDC\.  | 
| Oracle as target | When using Oracle as a target, fixed an issue where the target apply was failing with an ORA\-01747 error\. | 
| Amazon S3—Fixed reload table data loss | When using Amazon S3 as a target, fixed an issue where a table reload operation wasn't generating CDC files\. | 
| Fixed—SQL Server Always On context initialization in case primary server as a source | When using SQL Server Always On as a source, fixed an issue to not initialize Availability Groups \(AG\) if the source is primary and AlwaysOnSharedSyncedBackupIsEnabled is set to true\.  | 
| Updated SQL Server endpoint setting | When a source endpoint is SQL Server Always On Availability Group and is a secondary replica, fixed an issue where the replication task fails if AlwaysOnSharedSynchedBackupsIsEnabled is set to True\. | 
| PostgreSQL as source | Fixed an issue where CDC fails to migrate delete/update operations on the PostgreSQL source, which was introduced in 3\.4\.7 in supporting mapBooleanAsBoolean\.  | 

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
+ Fixed an issue where AWS DMS instances ran out of memory under large data volume when using OpenSearch Service as a target\. 
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


**Issues resolved in the DMS 3\.4\.6 maintenance release dated 13\-December\-2022**  

| Topic | Resolution | 
| --- | --- | 
| SAP ASE odbc driver | Fixed an issue for SAP ASE as a source so that the ODBC driver can support character sets\.  | 
|  Sqlserver datetime primary key bug for lob lookup  |  Fixed an issue for SQL Server as a source where LOB lookup was not working properly, when primary key has a datetime datatype, with precision in milliseconds\.  | 
| SQL Server to Redshift–'datetimeoffset' mapped to 'timestamptz' |  For migrations from SQL Server to Redshift, improved mapping so that the SQL Server 'datetimeoffset' format is mapped to the Redshift 'timestamptz' format\.  | 
| Data Validation \- SkipLobColumns is True |  Fixed an issue where DMS task crashes when SkipLobColumns is True, there is a LOB on the source, the Primary Key is on the last column, and a data difference is detected by validation\.  | 
| Data Validation with MySQL as source |  Fixed an issue for MySQL as a source with data validation enabled, where a DMS task crash occurs while using a table that has a composite unique key that has null values\.  | 
| MySQL as source |  Fixed an issue for MySQL as a source, where a table is getting suspended with Overflow error when the columns are altered to add precision\.  | 
|  Upgrade MySQL ODBC driver to 8\.0\.23  |  Fixed an issue for MySQL as a source, where the collation ‘utf8mb4\_0900\_bin' was incompatible with the mysql driver used by DMS\.  | 
| MySQL–support DDL changes for partitioned tables |  Introduced a new MySQL endpoint setting skipTableSuspensionForPartitionDdl to allow the user to skip table suspension for partition DDL changes during CDC, so that DMS can now support DDL changes for partitioned MySQL tables\.  | 
| MongoDB to Redshift migration |  Fixed an issue for MongoDB to Redshift migrations, where DMS fails to create the target table on Redshift if the MongoDB collection has binary data type\.  | 
| Redshift target–Time Travel Segfault in Bulk Apply |  Fixed an issue for Redshift as a target, where DMS task crashes with BatchApplyEnabled set to true\.  | 
| Redshift as target |  Fixed an issue for Redshift as a target, where with parallel\-load set to type=partitions\-auto, parallel segments were writing bulk CSV files to the same table directory and interfering with each other\.  | 
| Redshift as target |  Fixed an issue with Redshift as a target, where during CDC the target column is of type boolean while the source is of type character varying\.  | 
| Redshift as target |  Improved the task log to identify a DDL change that fails to replicate to Redshift as a target\.  | 
| Data validation with PostgreSQL  |  Fixed an issue for validation with PostgreSQL, where the validation fails when boolean data types are present\.  | 
| PostgreSQL as source |  Fixed an issue for PostgreSQL as a source, so that the full load uses the ExecuteTimeout field in Extra connection attributes\.  | 
| PostgreSQL as source |  Fixed an issue for PostgreSQL as a source, so that a task will fail if it's reading LSNs which are greater than the requested task resume LSN for more than 60 min to indicate that the is a problem with the replication slot being used\.  | 
| PostgreSQL as a source–timestamptz before 1970\-01\-01 |  Fixed an issue for PostgreSQL as a source, where timestamptz before 1970\-01\-01 were not migrated correctly during CDC\.  | 
| PostgreSQL as source |  Fixed an issue for PostgreSQL as a source, where DMS was truncating character varying datatype values during CDC\.  | 
| PostgreSQL as a source–resuming stopped task |  Fixed an issue for PostgreSQL as a source where resuming a previously stopped task replay misses one or more transactions during CDC\.  | 
| Amazon S3 as target |  Fixed an issue for S3 as a target, where the resultant CSV file header is off by one column when AddColumnName is true and TimestampColumnName is ""\.  | 
|  Amazon S3 as source–memory usage behavior in full load phase for task  |  Fixed an issue for S3 as a source, where a DMS task in full load was only releasing the used memory after the entire table was loaded to the target database\.  | 
| Amazon S3 as target–table reload operation |  Fixed an issue for S3 as a target, where a table reload operation missed generating CDC files\.  | 

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
+ Added an option to remove the hexadecimal “0x” prefix to `RAW` data type values when using Kinesis, Kafka, or OpenSearch as a target\.
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
| Support for AWS Secrets Manager integration | You can store the database connection details \(user credentials\) for supported endpoints securely in AWS Secrets Manager\. You can then submit the corresponding secret instead of plain\-text credentials to AWS DMS when you create or modify an endpoint\. AWS DMS then connects to the endpoint databases using the secret\. For more information on creating secrets for AWS DMS endpoints, see [Using secrets to access AWS Database Migration Service endpoints](security_iam_secretsmanager.md)\. | 
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
| Support for privately connecting your Amazon Virtual Private Cloud \(Amazon VPC\) to AWS Database Migration Service \(DMS\) without requiring an internet gateway, NAT device, VPN connection, or AWS Direct Connect connection\.  | You can now connect to and access AWS DMS from your Amazon VPC through a VPC interface endpoint that you create\. This interface endpoint allows you to isolate all network activity of your AWS DMS replication instance within the Amazon network infrastructure\. By including a reference to this interface endpoint in all API calls to AWS DMS using the AWS CLI or an SDK, you ensure that all AWS DMS activity remains invisible to the public Internet\. For more information, see [Infrastructure security in AWS Database Migration Service](infrastructure-security.md)\. This feature is available using all supported AWS DMS engine versions\.  | 
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
+ Supports multithreaded CDC load task settings, including `ParallelApplyThreads`, `ParallelApplyBufferSize`, and `ParallelApplyQueuesPerThread`, for Amazon OpenSearch Service \(OpenSearch Service\) as a target\.
+ Improved OpenSearch Service as a target by supporting its use of composite primary keys\.
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
| Support for composite primary key with Amazon OpenSearch Service as target  |  As of AWS DMS 3\.3\.3, use of a composite primary key is supported by OpenSearch Service targets\.  | 
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