# Specifying, starting, and viewing premigration assessment runs<a name="CHAP_Tasks.AssessmentReport1"></a>

A premigration assessment run specifies one or more individual assessments to run based on a new or existing migration task configuration\. Each individual assessment evaluates a specific element of the source or target database depending on considerations such as the migration type, supported objects, index configuration, and other task settings, such as table mappings to identify the schemas and tables to migrate\. For example, an individual assessment might evaluate what source data types or primary key formats can and cannot be migrated, possibly based on the AWS DMS engine version\.

## Specifying individual assessments<a name="CHAP_Tasks.AssessmentReport1.Individual"></a>

The following table provides a brief overview of the individual assessments that are applicable to a given migration task based on its configuration\. You can choose from among the individual assessments to include in a new assessment run that are applicable to your task configuration\.


| Assessment name \(console and API\) | Description | Source must be relational | Target must be relational | Target must be Amazon ES | Target must be DynamoDB | Migration type must perform CDC | Applicable AWS DMS engine versions | 
| --- | --- | --- | --- | --- | --- | --- | --- | 
|  Console – Unsupported data types API – `unsupported-data-types-in-source`  |  Checks for data types unsupported by AWS DMS in the source endpoint\. Not all data types can be migrated between engines\.  |  –  |  –  |  –  |  –  |  –  |  All supported versions  | 
|  Console – Large objects \(LOBs\) are used but target LOB columns are not nullable API – `full-lob-not-nullable-at-target`  |  Checks for the nullability of a LOB column in the target when full LOB mode or inline LOB mode is used\. AWS DMS requires a LOB column to be null when using these LOB modes\.  |  X  |  X  |  –  |  –  |  –  |  All supported versions  | 
|  Console – Source table with LOBs but without primary keys or unique constraints API – `table-with-lob-but-without-primary-key-or-unique-constraint`  |  Checks for the presence of source tables with LOBs but without a primary key or a unique key\. Currently, a table must have a primary key or a unique key for AWS DMS to migrate LOBs\.  |  X  |  –  |  –  |  –  |  –  |  All supported versions  | 
|  Console – Source table without primary key for CDC or full load and CDC tasks only API – `table-with-no-primary-key-or-unique-constraint`  |  Checks for the presence of a primary key or a unique key in source tables for a full\-load and change data capture \(CDC\) migration or a CDC\-only migration\. Lack of a primary key or a unique key can cause performance issues during the CDC migration\.  |  X  |  –  |  –  |  –  |  X  |  All supported versions  | 
|  Console – Target table without primary keys for CDC tasks only API – `target-table-has-unique-key-or-primary-key-for-cdc`  |  Checks for the presence of a primary key or a unique key in already\-created target tables for a CDC\-only migration\. Lack of a primary key or a unique key can cause full table scans in the target when AWS DMS applies updates and deletes resulting in performance issues during the CDC migration\.  |  –  |  X  |  –  |  –  |  X  |  All supported versions  | 
|  Console – Unsupported source primary key types \- composite primary keys API – `unsupported-source-pk-type-for-dynamodb-target`  |  Checks for the presence of composite primary keys in source tables when migrating to Amazon DynamoDB\. The primary key of the source table must consist of a single column\.  |  X  |  –  |  –  |  X  |  –  |  All supported versions  | 
|  Console – Unsupported source primary key types \- composite primary keys API – `unsupported-source-pk-type-for-elasticsearch-target`  |  Checks for the presence of composite primary keys in source tables when migrating to Amazon Elasticsearch Service \(Amazon ES\)\. The primary key of the source table must consist of a single column\.  |  X  |  –  |  X  |  –  |  –  |  Versions 3\.3\.2 and earlier  | 

**Note**  
AWS DMS versions 3\.3\.3 and later support migrating a source database to an Amazon ES target where the source primary key consists of multiple columns\.

AWS DMS supports premigration assessment runs for the following relational databases:
+ Oracle
+ SQL Server 
+ PostgreSQL
+ MySQL
+ MariaDB
+ Amazon Aurora

## Starting and viewing premigration assessment runs<a name="CHAP_Tasks.AssessmentReport1.AssessmentRun"></a>

You can start a premigration assessment run for a new or existing migration task using the AWS DMS Management Console, the AWS CLI, and the AWS DMS API\.

**To start a premigration assessment run for a new or existing task**

1. From the **Database migration tasks** page in the AWS DMS Management Console, do one of the following:
   + Choose **Create task**\. The **Create database migration task page** opens:

     1. Enter the task settings required to create your task, including table mapping\.

     1. In the **Premigration assessment** section, select **Enable premigration assessment run**\. The section expands with options to specify an assessment run for the new task\.
**Note**  
When creating a new task, enabling a premigration assessment run disables the option to start the task automatically on task creation\. You can start the task manually after the assessment run completes\.
   + Choose the **Identifier** for an existing task on the **Database migration tasks** page\. The task page for the chosen existing task opens:

     1. Choose **Actions** and select **Create premigration assessment**\. A **Create premigration assessment** page opens with options to specify an assessment run for the existing task\. 

   These options include:

1. Enter a unique name for your assessment run\.

1. Select the available individual assessments that you want to include in this assessment run\. You can only select the available individual assessments based on your current task settings\. By default, all available individual assessments are enabled and selected\. All other individual assessments are disabled for this assessment run\.

1. Search for and choose an Amazon S3 bucket and folder in your account to store your assessment result report\.

1. Select or enter an IAM role with full account access to your chosen Amazon S3 bucket and folder\.

1. Optionally choose a setting to encrypt the assessment result report in your Amazon S3 bucket\.

1. Choose **Create task** for a new task or choose **Create** for an existing task\.

   The **Database migration tasks** page opens listing your new or modified task with a **Status** of **Creating\.\.\.** and a banner message indicating that your premigration assessment run will start once the task is created\.

AWS DMS provides access to the latest and all prior premigration assessment runs using the AWS DMS Management Console, the AWS CLI, or the AWS DMS API\. 

**To view results for the latest assessment run**

1. From the AWS DMS Management Console, choose the **Identifier** for your existing task on the **Database migration tasks** page\. The task page for the existing task opens\.

1. Choose the **Premigration assessments** tab on the existing task page\. This opens a **Latest assessment results** section on that page showing results of the latest assessment run for this task\.

These assessment run results start with the name of the latest assessment run and an overview of its status followed by a listing of the specified individual assessments and their status\. You can then explore details of the status of each individual assessment by choosing its name in the list, with results available down to the table column level\.

Both the status overview for an assessment run and each individual assessment shows a **Status** value\. This value indicates the overall status of the assessment run and a similar status for each individual assessment\. Following is a list of the **Status** values for the assessment run:
+ `"cancelling"` – The assessment run was cancelled\.
+ `"deleting"` – The assessment run was deleted\.
+ `"failed"` – At least one individual assessment completed with a `failed` status\.
+ `"error-provisioning"` – An internal error occurred while resources were provisioned \(during `provisioning` status\)\.
+ `"error-executing"` – An internal error occurred while individual assessments ran \(during `running` status\)\.
+ `"invalid state"` – The assessment run is in an unknown state\.
+ `"passed"` – All individual assessments have completed, and none has a `failed` status\.
+ `"provisioning"` – Resources required to run individual assessments are being provisioned\.
+ `"running"` – Individual assessments are being run\.
+ `"starting"` – The assessment run is starting, but resources are not yet being provisioned for individual assessments\.

Following is a list of the **Status** values for each individual assessment of the assessment run:
+ `"cancelled"` – The individual assessment was cancelled as part of cancelling the assessment run\.
+ `"error"` – The individual assessment did not complete successfully\.
+ `"failed"` – The individual assessment completed successfully with a failed validation result: view the details of the result for more information\.
+ `"invalid state"` – The individual assessment is in an unknown state\.
+ `"passed"` – The individual assessment completed with a successful validation result\.
+ `"pending"` – The individual assessment is waiting to run\.
+ `"running"` – The individual assessment is running\.
+ `"warning"` – The individual assessment completed successfully with a warning validation result: view the details of the result for more information\.

You can also view the JSON files for the assessment run results on Amazon S3\.

**To view the JSON files for the assessment run on Amazon S3**

1. From the AWS DMS Management Console, choose the Amazon S3 bucket link shown in the status overview\. This displays a list of bucket folders and other Amazon S3 objects stored in the bucket\. If your results are stored in a bucket folder, open the folder\.

1. You can find your assessment run results in several JSON files\. A `summary.json` file contains the overall results of the assessment run\. The remaining files are each named for an individual assessment that was specified for the assessment run, such as `unsupported-data-types-in-source.json`\. These files each contain the results for the corresponding individual assessment from the chosen assessment run\.

**To view the results for all previous assessment runs**

1. Choose **Previous assessment results** under the **Latest assessment results** section\. This shows a list of the previous assessment runs listed by name in reverse chronological order\.

1. Choose the name of the previous assessment run whose results you want to view\. The status overview and individual assessment results for your chosen assessment run display in place of the **Latest assessment results** section\.

1. You can then view the results for the chosen assessment run in the same way as for the latest assessment results shown initially\.

To start and view the results of premigration assessment runs for an existing migration task, you can run the following CLI commands and AWS DMS API operations:
+ CLI: [https://docs.aws.amazon.com/cli/latest/reference/dms/describe-applicable-individual-assessments](https://docs.aws.amazon.com/cli/latest/reference/dms/describe-applicable-individual-assessments), API: [https://docs.aws.amazon.com/dms/latest/APIReference/API_DescribeApplicableIndividualAssessments.html](https://docs.aws.amazon.com/dms/latest/APIReference/API_DescribeApplicableIndividualAssessments.html) – Provides a list of individual assessments that you can specify for a new premigration assessment run, given one or more task configuration parameters\.
+ CLI: [https://docs.aws.amazon.com/cli/latest/reference/dms/start-replication-task-assessment-run](https://docs.aws.amazon.com/cli/latest/reference/dms/start-replication-task-assessment-run), API: [https://docs.aws.amazon.com/dms/latest/APIReference/API_StartReplicationTaskAssessmentRun.html](https://docs.aws.amazon.com/dms/latest/APIReference/API_StartReplicationTaskAssessmentRun.html) – Starts a new premigration assessment run for one or more individual assessments of an existing migration task\.
+ CLI: [https://docs.aws.amazon.com/cli/latest/reference/dms/describe-replication-task-assessment-runs](https://docs.aws.amazon.com/cli/latest/reference/dms/describe-replication-task-assessment-runs), API: [https://docs.aws.amazon.com/dms/latest/APIReference/API_DescribeReplicationTaskAssessmentRuns.html](https://docs.aws.amazon.com/dms/latest/APIReference/API_DescribeReplicationTaskAssessmentRuns.html) – Returns a paginated list of premigration assessment runs based on filter settings\.
+ CLI: [https://docs.aws.amazon.com/cli/latest/reference/dms/describe-replication-task-individual-assessments](https://docs.aws.amazon.com/cli/latest/reference/dms/describe-replication-task-individual-assessments), API: [https://docs.aws.amazon.com/dms/latest/APIReference/API_DescribeReplicationTaskIndividualAssessments.html](https://docs.aws.amazon.com/dms/latest/APIReference/API_DescribeReplicationTaskIndividualAssessments.html) – Returns a paginated list of individual assessments based on filter settings\.
+ CLI: [https://docs.aws.amazon.com/cli/latest/reference/dms/cancel-replication-task-assessment-run](https://docs.aws.amazon.com/cli/latest/reference/dms/cancel-replication-task-assessment-run), API: [https://docs.aws.amazon.com/dms/latest/APIReference/API_CancelReplicationTaskAssessmentRun.html](https://docs.aws.amazon.com/dms/latest/APIReference/API_CancelReplicationTaskAssessmentRun.html) – Cancels, but does not delete, a single premigration assessment run\.
+ CLI: [https://docs.aws.amazon.com/cli/latest/reference/dms/delete-replication-task-assessment-run](https://docs.aws.amazon.com/cli/latest/reference/dms/delete-replication-task-assessment-run), API: [https://docs.aws.amazon.com/dms/latest/APIReference/API_DeleteReplicationTaskAssessmentRun.html](https://docs.aws.amazon.com/dms/latest/APIReference/API_DeleteReplicationTaskAssessmentRun.html) – Deletes the record of a single premigration assessment run\.