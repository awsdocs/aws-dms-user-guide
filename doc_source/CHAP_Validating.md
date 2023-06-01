# AWS DMS data validation<a name="CHAP_Validating"></a>

**Topics**
+ [Replication task statistics](#CHAP_Validating.TaskStatistics)
+ [Replication task statistics with Amazon CloudWatch](#CHAP_Validating.TaskStatistics.CloudWatch)
+ [Revalidating tables during a task](#CHAP_Validating.Revalidating)
+ [Using JSON editor to modify validation rules](#CHAP_Validating.JSONEditor)
+ [Validation only tasks](#CHAP_Validating.ValidationOnly)
+ [Troubleshooting](#CHAP_Validating.Troubleshooting)
+ [Limitations](#CHAP_Validating.Limitations)
+ [Amazon S3 target data validation](CHAP_Validating_S3.md)

AWS DMS provides support for data validation to ensure that your data was migrated accurately from the source to the target\. If enabled, validation begins immediately after a full load is performed for a table\. Validation compares the incremental changes for a CDC\-enabled task as they occur\.

During data validation, AWS DMS compares each row in the source with its corresponding row at the target, verifies the rows contain the same data, and reports any mismatches\. To accomplish this AWS DMS issues appropriate queries to retrieve the data\. Note that these queries will consume additional resources at the source and target as well as additional network resources\. 

For a CDC only task with validation enabled, all pre\-existing data in a table is validated before starting validation of new data\.

Data validation works with the following source databases wherever AWS DMS supports them as source endpoints:
+ Oracle
+ PostgreSQL\-compatible database \(PostgreSQL, Aurora PostgreSQL, or Aurora Serverless for PostgreSQL\)
+ MySQL\-compatible database \(MySQL, MariaDB, Aurora MySQL, or Aurora Serverless for MySQL\)
+ Microsoft SQL Server
+ IBM Db2 LUW

Data validation works with the following target databases wherever AWS DMS supports them as target endpoints:
+ Oracle
+ PostgreSQL\-compatible database \(PostgreSQL, Aurora PostgreSQL, or Aurora Serverless for PostgreSQL\)
+ MySQL\-compatible database \(MySQL, MariaDB, Aurora MySQL, or Aurora Serverless for MySQL\)
+ Microsoft SQL Server
+ IBM Db2 LUW
+ Amazon S3\. For information about validating Amazon S3 target data, see [Amazon S3 target data validation](CHAP_Validating_S3.md)\.

For more information about the supported endpoints, see [Working with AWS DMS endpoints](CHAP_Endpoints.md)\.

Data validation requires additional time, beyond the amount required for the migration itself\. The extra time required depends on how much data was migrated\.

For more information about these settings, see [ Data validation task settings](CHAP_Tasks.CustomizingTasks.TaskSettings.DataValidation.md)\.

For an example of `ValidationSettings` task settings in a JSON file, see [Task settings example](CHAP_Tasks.CustomizingTasks.TaskSettings.md#CHAP_Tasks.CustomizingTasks.TaskSettings.Example)\.

## Replication task statistics<a name="CHAP_Validating.TaskStatistics"></a>

When data validation is enabled, AWS DMS provides the following statistics at the table level:
+ **ValidationState**—The validation state of the table\. The parameter can have the following values:
  + **Not enabled**—Validation is not enabled for the table in the migration task\.
  + **Pending records**—Some records in the table are waiting for validation\.
  + **Mismatched records**—Some records in the table don't match between the source and target\. A mismatch might occur for a number of reasons; For more information, check the `awsdms_control.awsdms_validation_failures_v1` table on the target endpoint\.
  + **Suspended records**—Some records in the table can't be validated\.
  + **No primary key**—The table can't be validated because it had no primary key\.
  + **Table error**—The table wasn't validated because it was in an error state and some data wasn't migrated\.
  + **Validated**—All rows in the table are validated\. If the table is updated, the status can change from Validated\.
  + **Error**—The table can't be validated because of an unexpected error\.
  + **Pending validation**—The table is waiting validation\.
  + **Preparing table**—Preparing the table enabled in the migration task for validation\.
  + **Pending revalidation**—All rows in the table are pending validation after the table was updated\.
+ **ValidationPending**—The number of records that have been migrated to the target, but that haven't yet been validated\.
+ **ValidationSuspended**—The number of records that AWS DMS can't compare\. For example, if a record at the source is constantly being updated, AWS DMS can't compare the source and the target\. 
+ **ValidationFailed**—The number of records that didn't pass the data validation phase\. 

For an example of `ValidationSettings` task settings in a JSON file, see [Task settings example](CHAP_Tasks.CustomizingTasks.TaskSettings.md#CHAP_Tasks.CustomizingTasks.TaskSettings.Example)\.

You can view the data validation information using the console, the AWS CLI, or the AWS DMS API\.
+ On the console, you can choose to validate a task when you create or modify the task\. To view the data validation report using the console, choose the task on the **Tasks** page and choose the **Table statistics** tab in the details section\.
+ Using the CLI, set the `EnableValidation` parameter to `true` when creating or modifying a task to begin data validation\. The following example creates a task and enables data validation\.

  ```
  create-replication-task  
    --replication-task-settings '{"ValidationSettings":{"EnableValidation":true}}' 
    --replication-instance-arn arn:aws:dms:us-east-1:5731014:
       rep:36KWVMB7Q  
    --source-endpoint-arn arn:aws:dms:us-east-1:5731014:
       endpoint:CSZAEFQURFYMM  
    --target-endpoint-arn arn:aws:dms:us-east-1:5731014:
       endpoint:CGPP7MF6WT4JQ 
    --migration-type full-load-and-cdc 
    --table-mappings '{"rules": [{"rule-type": "selection", "rule-id": "1", 
       "rule-name": "1", "object-locator": {"schema-name": "data_types", "table-name": "%"}, 
       "rule-action": "include"}]}'
  ```

  Use the `describe-table-statistics` command to receive the data validation report in JSON format\. The following command shows the data validation report\.

  ```
  aws dms  describe-table-statistics --replication-task-arn arn:aws:dms:us-east-1:5731014:
  rep:36KWVMB7Q
  ```

  The report would be similar to the following\.

  ```
  {
      "ReplicationTaskArn": "arn:aws:dms:us-west-2:5731014:task:VFPFTYKK2RYSI", 
      "TableStatistics": [
          {
              "ValidationPendingRecords": 2, 
              "Inserts": 25, 
              "ValidationState": "Pending records", 
              "ValidationSuspendedRecords": 0, 
              "LastUpdateTime": 1510181065.349, 
              "FullLoadErrorRows": 0, 
              "FullLoadCondtnlChkFailedRows": 0, 
              "Ddls": 0, 
              "TableName": "t_binary", 
              "ValidationFailedRecords": 0, 
              "Updates": 0, 
              "FullLoadRows": 10, 
              "TableState": "Table completed", 
              "SchemaName": "d_types_s_sqlserver", 
              "Deletes": 0
          }
  }
  ```
+ Using the AWS DMS API, create a task using the **CreateReplicationTask** action and set the `EnableValidation` parameter to **true** to validate the data migrated by the task\. Use the **DescribeTableStatistics** action to receive the data validation report in JSON format\.

## Replication task statistics with Amazon CloudWatch<a name="CHAP_Validating.TaskStatistics.CloudWatch"></a>

When Amazon CloudWatch is enabled, AWS DMS provides the following replication task statistics:
+  **ValidationSucceededRecordCount**— Number of rows that AWS DMS validated, per minute\. 
+  **ValidationAttemptedRecordCount**— Number of rows that validation was attempted, per minute\. 
+  **ValidationFailedOverallCount**— Number of rows where validation failed\. 
+  **ValidationSuspendedOverallCount**— Number of rows where validation was suspended\. 
+  **ValidationPendingOverallCount**— Number of rows where the validation is still pending\. 
+  **ValidationBulkQuerySourceLatency**— AWS DMS can do data validation in bulk, especially in certain scenarios during a full\-load or on\-going replication when there are many changes\. This metric indicates the latency required to read a bulk set of data from the source endpoint\. 
+  **ValidationBulkQueryTargetLatency**— AWS DMS can do data validation in bulk, especially in certain scenarios during a full\-load or on\-going replication when there are many changes\. This metric indicates the latency required to read a bulk set of data on the target endpoint\. 
+  **ValidationItemQuerySourceLatency**— During on\-going replication, data validation can identify on\-going changes and validate those changes\. This metric indicates the latency in reading those changes from the source\. Validation can run more queries than required, based on number of changes, if there are errors during validation\. 
+  **ValidationItemQueryTargetLatency**— During on\-going replication, data validation can identify on\-going changes and validate the changes row by row\. This metric gives us the latency in reading those changes from the target\. Validation may run more queries than required, based on number of changes, if there are errors during validation\. 

To collect data validation information from CloudWatch enabled statistics, select **Enable CloudWatch logs** when you create or modify a task using the console\. Then, to view the data validation information and ensure that your data was migrated accurately from source to target, do the following\.

1. Choose the task on the **Database migration tasks** page\.

1. Choose the **CloudWatch metrics** tab\.

1. Select **Validation** from the drop down menu\. 

## Revalidating tables during a task<a name="CHAP_Validating.Revalidating"></a>

While a task is running, you can request AWS DMS to perform data validation\.

### AWS Management Console<a name="CHAP_Validating.Revalidating.CON"></a>

1. Sign in to the AWS Management Console and open the AWS DMS console at [https://console\.aws\.amazon\.com/dms/v2/](https://console.aws.amazon.com/dms/v2/)\. 

   If you're signed in as an AWS Identity and Access Management \(IAM\) user, make sure that you have the appropriate permissions to access AWS DMS\. the permissions required, see [IAM permissions needed to use AWS DMS](security-iam.md#CHAP_Security.IAMPermissions)\.

1. Choose **Tasks** from the navigation pane\. 

1. Choose the running task that has the table you want to revalidate\. 

1. Choose the **Table Statistics** tab\.

1. Choose the table you want to revalidate \(you can choose up to 10 tables at one time\)\. If the task is no longer running, you can't revalidate the table\(s\)\.

1. Choose **Revalidate**\.

## Using JSON editor to modify validation rules<a name="CHAP_Validating.JSONEditor"></a>

To add a validation rule to a task using the JSON editor from the AWS DMS Console, do the following:

1. Select **Database migration tasks**\.

1. Select your task from the list of migration tasks\.

1. If your task is running, select **Stop** from the **Actions** drop down menu\.

1. Once the task has stopped, to modify your task, select **Modify** from the **Actions** drop down menu\. 

1. In the **Table mappings** section, select **JSON editor** and add your validation rule to your table mappings\.

For example, you can add the following validation rule to run a replace function on the source\. In this case, if the validation rule encounters a null byte, it validates it as a space\.

```
{
	"rule-type": "validation",
	"rule-id": "1",
	"rule-name": "1",
	"rule-target": "column",
	"object-locator": {
		"schema-name": "Test-Schema",
		"table-name": "Test-Table",
		"column-name": "Test-Column"
	},
	"rule-action": "override-validation-function",
	"source-function": "REPLACE(${column-name}, chr(0), chr(32))",
	"target-function": "${column-name}"
}
```

## Validation only tasks<a name="CHAP_Validating.ValidationOnly"></a>

You can create validation only tasks to preview and validate data without performing any migration or data replication\. To create a validation only task, set the `EnableValidation` and `ValidationOnly` settings to `true`\. When enabling `ValidationOnly`, additional requirements apply\. For more information, see [ Data validation task settings](CHAP_Tasks.CustomizingTasks.TaskSettings.DataValidation.md)\.

For a full load only migration type, a validation only task completes much faster than its CDC equivalent when many failures are reported\. But changes to the source or target endpoint are reported as failures for full load mode, a possible disadvantage\.

A CDC validation only task delays validation based on average latency, and retries failures multiple times before reporting them\. If the majority of data comparisons result in failures, a validation only task for CDC mode is very slow, a potential drawback\.

A validation\-only task must be set up in the same direction as the replication task, especially for CDC\. This is because a CDC Validation Only task detects which rows have changed and need to be revalidated based on the change log on the source\. If the target is specified as the source, then it only knows about changes sent to the target by DMS and is not guaranteed to catch replication errors\.

### Full load validation only<a name="CHAP_Validating.ValidationOnly.FL"></a>

Beginning with AWS DMS version 3\.4\.6 and higher, a full load validation only task quickly compares all rows from the source and target tables in a single pass, immediately reports any failures, and then shuts down\. Validation never is suspended due to failures in this mode, it is optimized for speed\. But changes to the source or target endpoint are reported as failures\.

**Note**  
Beginning with AWS DMS version 3\.4\.6 and higher, this validation behavior also applies to full load migration task with validation enabled\.

### CDC validation only<a name="CHAP_Validating.ValidationOnly.CDC"></a>

A CDC validation only task validates all existing rows between the source and target tables on a fresh start\. In addition, a CDC validation only task runs continuously, re\-validates ongoing replication changes, limits the number of failures reported each pass, and retries mismatched rows before failing them\. It is optimized to prevent false positives\.

Validation for a table \(or the entire task\) is suspended if the` FailureMaxCount` or `TableFailureMaxCount` thresholds are breached\. This also applies for a CDC or Full Load\+CDC migration task with validation enabled\. And a CDC task with validation enabled delays re\-validation for each changed row based on average source and target latency\.

But a CDC *validation only task* doesn't migrate data and has no latency\. It sets `ValidationQueryCdcDelaySeconds` to 180 by default\. And you can increase the amount to account for high latency environments and help prevent false positives\.

### Validation only use cases<a name="CHAP_Validating.ValidationOnly.Cases"></a>

Use cases for splitting the data validation portion of a migration or replication task into a separate *validation only task* includes, but is not limited to, the following:
+ *Control exactly when validation occurs* — Validation queries add an additional load to both source and target endpoints\. So, migrating or replicating data in one task first, then validating the results in another task can be beneficial\.
+ *Reduce load on the replication instance* — Splitting data validation to run on its own instance can be advantageous\.
+ *Quickly obtain how many rows don't match at a given moment in time *— For example, just before or during a maintenance window production cut–over to a target endpoint, you can create a Full Load validation only task to get an answer to your question\.
+ *When validation failures are expected for a migration task with a CDC component* — For example, if migrating Oracle `varchar2` to PostgreSQL `jsonb`, CDC validation keeps retrying these failed rows and limits the number of failures reported each time\. But, you can create a Full Load validation only task and obtain a quicker answer\.
+ *You've developed a data repair script/utility that reads the validation failure table* — \(See also, [Troubleshooting](#CHAP_Validating.Troubleshooting)\)\. A Full Load validation only task quickly reports failures for the data repair script to act upon\.

For an example of `ValidationSettings` task settings in a JSON file, see [Task settings example](CHAP_Tasks.CustomizingTasks.TaskSettings.md#CHAP_Tasks.CustomizingTasks.TaskSettings.Example)\)\.

## Troubleshooting<a name="CHAP_Validating.Troubleshooting"></a>

During validation, AWS DMS creates a new table at the target endpoint: `awsdms_control.awsdms_validation_failures_v1`\. If any record enters the *ValidationSuspended* or the *ValidationFailed* state, AWS DMS writes diagnostic information to `awsdms_control.awsdms_validation_failures_v1`\. You can query this table to help troubleshoot validation errors\.

For information about changing the default schema the table is created in on the target, see [Control table task settings](CHAP_Tasks.CustomizingTasks.TaskSettings.ControlTable.md)\.

Following is a description of the `awsdms_control.awsdms_validation_failures_v1` table:


| Column name | Data type | Description | 
| --- | --- | --- | 
|  `TASK_NAME`  |  `VARCHAR(128) NOT NULL`  |  AWS DMS task identifier\.  | 
| TABLE\_OWNER | VARCHAR\(128\) NOT NULL |  Schema \(owner\) of the table\.  | 
|  `TABLE_NAME`  | VARCHAR\(128\) NOT NULL |  Table name\.  | 
| FAILURE\_TIME | DATETIME\(3\) NOT NULL |  Time when the failure occurred\.  | 
| KEY\_TYPE | VARCHAR\(128\) NOT NULL |  Reserved for future use \(value is always 'Row'\)  | 
| KEY | TEXT NOT NULL |  This is the primary key for row record type\.  | 
| FAILURE\_TYPE | VARCHAR\(128\) NOT NULL |   Severity of validation error\. Can be either `RECORD_DIFF`, `MISSING_SOURCE` or `MISSING_TARGET`\.   | 
| DETAILS | VARCHAR\(8000\) NOT NULL |  JSON formatted string of all source/target column values which do not match for the given key\.  | 

The following query will show you all the failures for a task by querying the `awsdms_control.awsdms_validation_failures_v1` table\. The task name should be the external resource ID of the task\. The external resource ID of the task is the last value in the task ARN\. For example, for a task with an ARN value of arn:aws:dms:us\-west\-2:5599:task: VFPFKH4FJR3FTYKK2RYSI, the external resource ID of the task would be VFPFKH4FJR3FTYKK2RYSI\.

```
select * from awsdms_validation_failures_v1 where TASK_NAME = 'VFPFKH4FJR3FTYKK2RYSI'

TASK_NAME       VFPFKH4FJR3FTYKK2RYSI
TABLE_OWNER     DB2PERF
TABLE_NAME      PERFTEST
FAILURE_TIME    2020-06-11 21:58:44
KEY_TYPE        Row
KEY             {"key":  ["3451491"]}
FAILURE_TYPE    RECORD_DIFF
DETAILS         [[{'MYREAL': '+1.10106036e-01'}, {'MYREAL': '+1.10106044e-01'}],]
```

You can look at the `DETAILS` field to determine which columns don’t match\. Since you have the primary key of the failed record, you can query the source and target endpoints to see what part of the record does not match\.

## Limitations<a name="CHAP_Validating.Limitations"></a>
+ Data validation requires that the table has a primary key or unique index\.
  + Primary key columns can't be of type `CLOB`, `BLOB`, or `BYTE`\.
  + For primary key columns of type `VARCHAR` or `CHAR`, the length must be less than 1024\.
  + An Oracle key created with the `NOVALIDATE` clause is *not* considered a primary key or unique index\.
  + For an Oracle table with no primary key and only a unique key, the columns with the unique constraint must also have a `NOT NULL` constraint\.
+ Validation of NULL PK/UK values aren't supported\.
+ If the collation of the primary key column in the target PostgreSQL instance isn't set to "C", the sort order of the primary key is different compared to the sort order in Oracle\. If the sort order is different between PostgreSQL and Oracle, data validation fails to validate the records\.
+ Data validation generates additional queries against the source and target databases\. You must ensure that both databases have enough resources to handle this additional load\. 
+ Data validation isn't supported when consolidating several databases into one\.
+ For a source or target Oracle endpoint, AWS DMS uses DBMS\_CRYPTO to validate LOBs\. If your Oracle endpoint uses LOBs, then you must grant the execute permission on dbms\_crypto to the user account used to access the Oracle endpoint\. You can do this by running the following statement:

  ```
  grant execute on sys.dbms_crypto to dms_endpoint_user;
  ```
+ If the target database is modified outside of AWS DMS during validation, then discrepancies might not be reported accurately\. This result can occur if one of your applications writes data to the target table, while AWS DMS is performing validation on that same table\.
+ If one or more rows are being continuously modified during validation, then AWS DMS can't validate those rows\.
+ If AWS DMS detects more than 10,000 failed or suspended records, it stops the validation\. Before you proceed further, resolve any underlying problems with the data\.
+ AWS DMS doesn't support data validation of views\.
+ AWS DMS doesn't support data validation when character substitution task settings are used\.

For limitations when using S3 target validation, see [Limitations for using S3 target validation](CHAP_Validating_S3.md#CHAP_Validating_S3_limitations)\.