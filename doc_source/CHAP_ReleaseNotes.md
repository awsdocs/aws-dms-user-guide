# AWS DMS Release Notes<a name="CHAP_ReleaseNotes"></a>

Following, you can find release notes for versions of AWS Database Migration Service \(AWS DMS\)\.

## AWS Database Migration Service \(AWS DMS\) 2\.4\.3 Release Notes<a name="CHAP_ReleaseNotes.DMS242"></a>

The following tables shows the features and bug fixes for version 2\.4\.3 of AWS Database Migration Service \(AWS DMS\)\.


| New Feature or Enhancement | Description | 
| --- | --- | 
| Table metadata recreation on mismatch | Added a new extra connect attribute for MySQL endpoints – `CleanSrcMetadataOnMismatch` This is a Boolean attribute that cleans and recreates table metadata information on the replication instance when a mismatch occurs\. For example, in a situation where running an alter DDL on the table could result in different information about the table cached in the replication instance\. By default, this attribute is set to false\.  | 
| Performance improvements for Data Validation | [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_ReleaseNotes.html)  | 


**Issues Resolved**  

| Date Reported | Description | 
| --- | --- | 
| February 12, 2018 | Fixed an issue in ongoing replication using batch apply where AWS DMS was missing some inserts as a unique constraint in the table was being updated\. | 
| March 16, 2018 | Fixed an issue where an Oracle to PostgreSQL migration task was crashing during the on\-going replication phase due to Multi\-AZ failover on the source Oracle RDS instance\. | 

## AWS Database Migration Service \(AWS DMS\) 2\.4\.2 Release Notes<a name="CHAP_ReleaseNotes.DMS242"></a>

The following tables shows the features and bug fixes for version 2\.4\.2 of AWS Database Migration Service \(AWS DMS\)\.


| New Feature or Enhancement | Description | 
| --- | --- | 
| Binary Reader support for Amazon RDS for Oracle during change data capture | Added support for using Binary Reader in change data capture scenarios from an Amazon RDS for Oracle source during on\-going replication\. | 
| Additional copy command parameters for Amazon Redshift as a target |  Introduced support for the following [additional Redshift copy parameters](https://docs.aws.amazon.com/redshift/latest/dg/r_COPY-parameters.html) using extra connection attributes\. For more information, see [Extra Connection Attributes When Using Amazon Redshift as a Target for AWS DMS](CHAP_Target.Redshift.md#CHAP_Target.Redshift.ConnectionAttrib)\. [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_ReleaseNotes.html)  | 
| Option to fail migration task when a table is truncated in PostgreSQL source | Introduced support to fail a task when a truncate is encountered in a PostgreSQL source via a new task setting\. For more information, see the ApplyErrorFailOnTruncationDdl setting in the section [Error Handling Task Settings](CHAP_Tasks.CustomizingTasks.TaskSettings.ErrorHandling.md)\.  | 
| Validation support for JSON/JSONB/HSTORE in PostgreSQL endpoints | Introduced data validation support for JSON/JSONB/HSTORE columns for PostgreSQL as a source and target\. | 
| Improved logging for MySQL sources | Improved log visibility for issues when reading MySQL binlogs during change data capture \(CDC\)\. Logs will clearly show a error/warning if there are issues accessing MySQL source binlogs during CDC\. | 
| Additional data validation statistics | Added more replication table statistics\. For more information, see [Replication Task Statistics](CHAP_Validating.md#CHAP_Validating.TaskStatistics)\. | 


**Issues Resolved**  

| Date Reported | Description | 
| --- | --- | 
| January 14, 2018 | Fixed all issues with respect to handling zero dates \(0000\-00\-00\) to MySQL targets during full load and CDC\. MySQL does not accept 0000\-00\-00 \(invalid in MySQL\) while some engines do\. All these dates will become 0101\-01\-01 for a MySQL target\. | 
| January 21, 2018 | Fixed an issue where migration fails when migrating a table with table name containing a $ sign\. | 
| February 3, 2018 | Fixed an issue where a JSON column from a PostgreSQL source was truncated when migrated to any supported target\. | 
| February 12, 2018 | Fixed an issue where migration task was failing after a failover in RDS Aurora MySQL target\.  | 
| February 21, 2018 | Fixed an issue where migration task was not able to start on\-going replication phase after a network connectivity issue\. | 
| February 23, 2018 | Fixed an issue where certain transformation rules in table mappings were causing migration task crashes during on\-going replication to Redshift targets\. | 
| Reported on multiple dates |  Fixed various data validation issues  • Fixed wide string handling issues in Oracle source/target validation\. • Handle validation when a column is removed for a table in table mappings\. • Improved validation performance for sources with high rate of change\.  | 

## AWS Database Migration Service \(AWS DMS\) 2\.4\.1 Release Notes<a name="CHAP_ReleaseNotes.DMS241"></a>

The following tables shows the features and bug fixes for version 2\.4\.1 of AWS Database Migration Service \(AWS DMS\)\.


| New Feature or Enhancement | Description | 
| --- | --- | 
| JSONB support for PostgreSQL sources | Introduced support for JSONB migration from PostgreSQL as a source\. JSONB is treated as a LOB data type and will require appropriate LOB settings to be used\. | 
| HSTORE support for PostgreSQL sources |  Introduced support for HSTORE data type migration from PostgreSQL as a source\. HSTORE is treated as a LOB data type and will require appropriate LOB settings to be used\. | 
| Additional copy command parameters for Redshift as a target |  Introduced support for the following [additional copy parameters](https://docs.aws.amazon.com/redshift/latest/dg/r_COPY-parameters.html) via the extra connection attributes –  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_ReleaseNotes.html)  | 


**Issues Resolved**  

| Date Reported | Description | 
| --- | --- | 
| July 12, 2017 | Fixed an issue where migration task is hung before the full load phase starts when reading from an Oracle table with TDE column encryption enabled\. | 
| October 3, 2017 | Fixed an issue where a JSON column from a PostgreSQL source would not migrate as expected\.  | 
| October 5, 2017 | Fixed an issue when DMS migration task shows 0 source latency when archive redo log file is not found on the source Oracle instance\. This fix will linearly increase source latency under such conditions\.  | 
|  November 20, 2017  | Fixed an issue with LOB migration where a TEXT column in PostgreSQL was migrating to a CLOB column in Oracle with extra spaces after each character in the LOB entry\. | 
|  November 20, 2017  | Fixed an issue with a migration task not stopping as expected after an underlying replication instance upgrade from version 1\.9\.0 to 2\.4\.0\. | 
| November 30, 2017 | Fixed an issue where a DMS migration task does not properly capture changes made by a copy command run on a source PostgreSQL instance\. | 
| December 11, 2017 | Fixed an issue where a migration task failed when reading change data from a non\-existent bin log from a MySQL source\.  | 
| December 11, 2017 | Fixed an issue where DMS is reading change data from a non\-existent table from a MySQL source\.  | 
| December 20, 2017 | Includes several fixes and enhancements for the data validation feature\. | 
| December 22, 2017 | Fixed an issue with maxFileSize parameter for Redshift targets\. This parameter was wrongly being interpreted as bytes instead of kilobytes\.  | 
| January 4, 2018 | Fixed a memory allocation bug for a DynamoDB as a target migration tasks\. In certain conditions, AWS DMS was not allocating enough memory if the object mapping being used contained a sort key\. | 
| January 10, 2018 | Fixed an issue with Oracle 12\.2 as a source where DMLs were not captured as expected when ROWDEPENDENCIES are used\.  | 

## AWS Database Migration Service \(AWS DMS\) 2\.4\.0 Release Notes<a name="CHAP_ReleaseNotes.DMS240"></a>

The following tables shows the features and bug fixes for version 2\.4\.0 of AWS Database Migration Service \(AWS DMS\)\.


| New Feature or Enhancement | Description | 
| --- | --- | 
| Replicating Oracle index tablespaces | Adds functionality to support replication of Oracle index tablespaces\. More details about index tablespaces can be seen [here](https://docs.oracle.com/cd/B28359_01/server.111/b28310/indexes003.htm#ADMIN11724)\.  | 
| Support for cross\-account Amazon S3 access |  Adds functionality to support canned ACLs to support cross account access with S3 endpoints\. More details about canned ACLs can be seen [here](http://docs.aws.amazon.com/AmazonS3/latest/dev/acl-overview.html#canned-acl)\.  Usage: Set extra connect attribute in S3 endpoint – `CannedAclForObjects=value` Possible values:  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_ReleaseNotes.html)  | 


**Issues Resolved**  

| Date Reported | Description | 
| --- | --- | 
| July 19, 2017 | Fixed an issue where replication task runs in a retry loop forever when source PostgreSQL instance runs out of replication slots\. With this fix, the task will fail with an error reporting that DMS could not create a logical replication slot\.  | 
| July 27, 2017 | Fixed an issue in the replication engine where the enum MySQL data type caused task failure with a memory allocation error\. | 
| August 7, 2017 | Fixed an issue which caused unexpected behavior with migration tasks with Oracle as a source when the source is down for more than 5 minutes\. This issue caused the on\-going replication phase to hang even after the source became available\. | 
| August 24, 2017 | Fixed an issue with PostgreSQL target where the fraction part in the TIME datatype was handled incorrectly\. | 
| September 14, 2017 | Fixed an issue were incorrect values were being written to TOAST fields in PostgreSQL based targets during updates in the CDC phase\. | 
| October 8, 2017 | Fixed an issue from version 2\.3\.0 where on\-going replication with MySQL 5\.5 sources would not work as expected\.  | 
| October 12, 2017 |  Fixed an issue with reading changes from a SQL Server 2016 source during the on\-going replication phase\. This fix needs to be used in conjunction with the following extra connect attribute in the source SQL Server endpoint – `IgnoreTxnCtxValidityCheck=true`  | 

## AWS Database Migration Service \(AWS DMS\) 2\.3\.0 Release Notes<a name="CHAP_ReleaseNotes.DMS230"></a>

The following tables shows the features and bug fixes for version 2\.3\.0 of AWS Database Migration Service \(AWS DMS\)\.


| New Feature or Enhancement | Description | 
| --- | --- | 
| S3 as a source | [Using Amazon S3 as a Source for AWS DMS](CHAP_Source.S3.md) | 
| SQL Azure as a source | [ Using Microsoft Azure SQL Database as a Source for AWS DMS](CHAP_Source.AzureSQL.md) | 
| Platform – AWS SDK update |  Update to AWS SDK in the replication instance to 1\.0\.113\. AWS SDK is used for certain endpoints like Redshift and S3 to upload data on customer’s behalf into these endpoints\.  Usage \- Unrestricted  | 
| Oracle source – Support replication of tablespace in Oracle |  Ability to migrate tablespaces from an Oracle source eliminating the need to pre\-create tablespaces in the target before migration\. Usage – Use the `ReadTableSpaceName` setting in the extra connect attributes in the Oracle source endpoint and set it to true to support tablespace replication\. This is set to false by default\.   | 
| Oracle source – CDC support for Oracle active data guard standby as a source |  Ability to use Oracle active data guard standby instance as a source for replicating on\-going changes to a supported target eliminating the need to connect to an active database which may be in production\. Usage – Use the `StandbyDelayTime` setting in the extra connect attributes in the Oracle source endpoint and specify time in minutes to specify the delay in standby sync\.  | 
| PostgreSQL source – add WAL heartbeat |  Added a WAL heartbeat \(run dummy queries\) for replication from a PostgreSQL source so idle logical replication slots do not hold on to old WAL logs which may result in storage full situations on the source\. This heart beat keeps restart\_lsn moving and prevents storage full scenarios\. Usage \- wherever applicable –  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_ReleaseNotes.html)  | 
| All endpoints – Maintain homogeneous replication with transformation |  Ability to do like to like migrations for homogeneous migration tasks \(from a table structure/data type perspective\) came in 2\.2\.0\. However, DMS would still convert datatypes internally when a task was launched with table transformations\. This feature maintains datatypes from source on target for homogeneous lift and shift migrations even when transformations are used\.  Usage – Unrestricted for all homogeneous migrations\.   | 
| All endpoints – fail task when no tables are found  |  Ability to force Replication Task Failure When Include Transformation Rules Finds No Matches Usage – Flip `FailOnNoTablesCaptured` task setting to true\.  | 
| Oracle source – stop task when archive redo log is missing |  Ability to come out of a retry loop and stop task when archive redo log on the source is missing\. Usage – Use the `RetryTimeoutInMinutes` extra connect attribute to specify stop timeout in minutes\.  | 


**Issues resolved**  

| Date Reported | Description | 
| --- | --- | 
| January 5, 2017 |  ***MySQL: Server ID collision when using multiple tasks on same MySQL source:*** Fixed a server ID collision issue when launching multiple DMS tasks to the same MySQL instance \(version 5\.6\+\)   | 
| February 21, 2017 |  ***MongoDB: Issue with create table when \_id is a string:*** Fixed an issue where create table fails for nestingLevel=ONE when \_id in MongoDB is a String in the document\. Before this fix, \_id \(when it is a String\) was being created as a LONGTEXT \(MySQL\) or CLOB \(Oracle\) which causes a failure when it tries to make it a primary key\.  | 
| May 5, 2017 | Oracle: NULL LOBs migrating as empty:Fixed an issue where NULL LOBs were migrating as empty when using full LOB mode with an Oracle source\. | 
| May 5, 2017 |  ***MySQL: Too many connections error when starting CDC from timestamp:*** Fixed an issue where a MySQL as a source task fails with a too many connections error when custom CDC start time is older than 24 hours\.   | 
| May 24, 2017 | DMS: Task starting for too long:Fixed an issue where task was starting for too long when multiple tasks were launched on the replication instance at once\.  | 
| July 7, 2017 |  ***PostgreSQL: Error message about using all available connection slots not showing up*** Fixed an issue to log an error in the default logging level when all available connection slots to PostgreSQL are used up and DMS cannot get more slots to continue with replication\.   | 
| July 19, 2017 |  ***DynamoDB: Issue with updates/deletes to DynamoDB from Oracle:*** Fixed an issue where updates/deletes from Oracle to DynamoDB were not being migrated well   | 
| August 8, 2017 |  ***Oracle: Oracle as a source – unexpected behavior during CDC:*** Fixed an issue which caused unexpected behavior during CDC when an Oracle source database instance went down for more than 5 minutes during a migration\.   | 
| August 12, 2017 |  ***Redshift: Nulls being migrated as amazon\_null:*** Fixed an issue where nulls from any source where being migrated as amazon\_null causing issues when inserted into non\-varchar datatypes in Redshift\.  | 
| August 27, 2017 |  ***MongoDB: Full load task crash:*** Fixed an issue where full load task crashes when nestingLevel=NONE and \_id is not ObjectId\.   | 