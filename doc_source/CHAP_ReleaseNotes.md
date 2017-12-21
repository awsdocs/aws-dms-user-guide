# Release Notes for AWS Database Migration Service<a name="CHAP_ReleaseNotes"></a>

This topic describes the release notes for versions of AWS Database Migration Service \(AWS DMS\) and the Schema Conversion Tool \(SCT\)\.

## AWS Database Migration Service \(AWS DMS\) 2\.4\.0 Release Notes<a name="CHAP_ReleaseNotes.DMS240"></a>

The following table shows the features and bug fixes for version 2\.4\.0 of AWS Database Migration Service \(AWS DMS\)\.


| New feature or enhancement | Description | 
| --- | --- | 
| Replicating Oracle index tablespaces | Adds functionality to support replication of Oracle index tablespaces\. More details about index tablespaces can be seen [here](https://docs.oracle.com/cd/B28359_01/server.111/b28310/indexes003.htm#ADMIN11724)\.  | 
| Cross account S3 access support |  Adds functionality to support canned ACLs to support cross account access with S3 endpoints\. More details about canned ACLs can be seen [here](http://docs.aws.amazon.com/AmazonS3/latest/dev/acl-overview.html#canned-acl)\.  Usage: Set extra connect attribute in S3 endpoint – `CannedAclForObjects=value` Possible values –  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_ReleaseNotes.html)  | 


**Issues Resolved**  

| Date reported | Description | 
| --- | --- | 
| 24th August 2017 | Fixed an issue with PostgreSQL target where the fraction part in the TIME datatype was handled incorrectly\. | 
| 7th August 2017 | Fixed an issue which caused unexpected behavior with migration tasks with Oracle as a source when the source is down for more than 5 minutes\. This issue caused the on\-going replication phase to hang even after the source became available\. | 
| 19th July 2017 | Fixed an issue where replication task runs in a retry loop forever when source PostgreSQL instance runs out of replication slots\. With this fix, the task will fail with an error reporting that DMS could not create a logical replication slot\.  | 
| 27th July 2017 | Fixed an issue in the replication engine where the enum MySQL data type caused task failure with a memory allocation error\. | 
| 14th September 2017 | Fixed an issue were incorrect values were being written to TOAST fields in PostgreSQL based targets during updates in the CDC phase\. | 
| 8th October 2017 | Fixed an issue from version 2\.3\.0 where on\-going replication with MySQL 5\.5 sources would not work as expected\.  | 
| 12th October 2017 |  Fixed an issue with reading changes from a SQL Server 2016 source during the on\-going replication phase\. This fix needs to be used in conjunction with the following extra connect attribute in the source SQL Server endpoint – `IgnoreTxnCtxValidityCheck=true`  | 

## AWS Database Migration Service \(AWS DMS\) 2\.3\.0 Release Notes<a name="CHAP_ReleaseNotes.DMS230"></a>

The following table shows the features and bug fixes for version 2\.3\.0 of AWS Database Migration Service \(AWS DMS\)\.


| New feature or enhancement | Description | 
| --- | --- | 
| S3 as a source | [Using Amazon S3 as a Source for AWS DMS](CHAP_Source.S3.md) | 
| SQL Azure as a source | [Using Microsoft Azure SQL Database as a Source for AWS DMS](CHAP_Source.AzureSQL.md) | 
| Platform – AWS SDK update |  Update to AWS SDK in the replication instance to 1\.0\.113\. AWS SDK is used for certain endpoints like Redshift and S3 to upload data on customer’s behalf into these endpoints\.  Usage \- Unrestricted  | 
| Oracle Source – Support replication of tablespace in Oracle |  Ability to migrate tablespaces from an Oracle source eliminating the need to pre\-create tablespaces in the target before migration\. Usage – Use the `ReadTableSpaceName` setting in the extra connect attributes in the Oracle source endpoint and set it to true to support tablespace replication\. This is set to false by default\.   | 
| Oracle Source – CDC support for Oracle active data guard standby as a source |  Ability to use Oracle active data guard standby instance as a source for replicating on\-going changes to a supported target eliminating the need to connect to an active database which may be in production\. Usage – Use the `StandbyDelayTime` setting in the extra connect attributes in the Oracle source endpoint and specify time in minutes to specify the delay in standby sync\.  | 
| PostgreSQL Source – add WAL heartbeat |  Added a WAL heartbeat \(run dummy queries\) for replication from a PostgreSQL source so idle logical replication slots do not hold on to old WAL logs which may result in storage full situations on the source\. This heart beat keeps restart\_lsn moving and prevents storage full scenarios\. Usage \- wherever applicable –  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_ReleaseNotes.html)  | 
| All endpoints – Maintain homogeneous replication with transformation |  Ability to do like to like migrations for homogeneous migration tasks \(from a table structure/data type perspective\) came in 2\.2\.0\. However, DMS would still convert datatypes internally when a task was launched with table transformations\. This feature maintains datatypes from source on target for homogeneous lift and shift migrations even when transformations are used\.  Usage – Unrestricted for all homogeneous migrations\.   | 
| All endpoints – fail task when no tables are found  |  Ability to force Replication Task Failure When Include Transformation Rules Finds No Matches Usage – Flip `FailOnNoTablesCaptured` task setting to true\.  | 
| Oracle source – stop task when archive redo log is missing |  Ability to come out of a retry loop and stop task when archive redo log on the source is missing\. Usage – Use the `RetryTimeoutInMinutes` extra connect attribute to specify stop timeout in minutes\.  | 


**Issues resolved**  

| Date reported | Description | 
| --- | --- | 
| 5th January 2017 |  ***MySQL: Server ID collision when using multiple tasks on same MySQL source:*** Fixed a server ID collision issue when launching multiple DMS tasks to the same MySQL instance \(version 5\.6\+\)   | 
| 21st February 2017 |  ***MongoDB: Issue with create table when \_id is a string:*** Fixed an issue where create table fails for nestingLevel=ONE when \_id in MongoDB is a String in the document\. Before this fix, \_id \(when it is a String\) was being created as a LONGTEXT \(MySQL\) or CLOB \(Oracle\) which causes a failure when it tries to make it a primary key\.  | 
| 5th May 2017 | Oracle: NULL LOBs migrating as empty:Fixed an issue where NULL LOBs were migrating as empty when using full LOB mode with an Oracle source\. | 
| 5th May 2017 |  ***MySQL: Too many connections error when starting CDC from timestamp:*** Fixed an issue where a MySQL as a source task fails with a too many connections error when custom CDC start time is older than 24 hours\.   | 
| 24th May 2017 | DMS: Task starting for too long:Fixed an issue where task was starting for too long when multiple tasks were launched on the replication instance at once\.  | 
| 19th July 2017 |  ***DynamoDB: Issue with updates/deletes to DynamoDB from Oracle:*** Fixed an issue where updates/deletes from Oracle to DynamoDB were not being migrated well   | 
| 8th August 2017 |  ***Oracle: Oracle as a source – unexpected behavior during CDC:*** Fixed an issue which caused unexpected behavior during CDC when an Oracle source database instance went down for more than 5 minutes during a migration\.   | 
| 27th August 2017 |  ***MongoDB: Full load task crash:*** Fixed an issue where full load task crashes when nestingLevel=NONE and \_id is not ObjectId\.   | 
| 7th July 2017 |  ***PostgreSQL: Error message about using all available connection slots not showing up*** Fixed an issue to log an error in the default logging level when all available connection slots to PostgreSQL are used up and DMS cannot get more slots to continue with replication\.   | 
| 12th August 2017 |  ***Redshift: Nulls being migrated as amazon\_null:*** Fixed an issue where nulls from any source where being migrated as amazon\_null causing issues when inserted into non\-varchar datatypes in Redshift\.  | 