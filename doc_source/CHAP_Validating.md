# Validating AWS DMS Tasks<a name="CHAP_Validating"></a>

AWS DMS provides support for data validation, to ensure that your data was migrated accurately from the source to the target\. If you enable it for a task, then AWS DMS begins comparing the source and target data immediately after a full load is performed for a table\.

Data validation is optional\. AWS DMS compares the source and target records, and reports any mismatches\. In addition, for a CDC\-enabled task, AWS DMS compares the incremental changes and reports any mismatches\. 

During data validation, AWS DMS compares each row in the source with its corresponding row at the target, and verifies that those rows contain the same data\. To accomplish this, AWS DMS issues appropriate queries to retrieve the data\. Note that these queries will consume additional resources at the source and the target, as well as additional network resources\.

Data validation works with the following databases:
+ Oracle
+ PostgreSQL
+ MySQL
+ MariaDB
+ Microsoft SQL Server
+ Amazon Aurora \(MySQL\)
+ Amazon Aurora \(PostgreSQL\)

Data validation requires additional time, beyond the amount required for the migration itself\. The extra time required depends on how much data was migrated\.

 Data validation settings include the following:
+ To enable data validation, set the `EnableValidation` setting to `true`\.
+ To adjust the number of execution threads that AWS DMS uses during validation, set the `ThreadCount` value\. The default value for `ThreadCount` is 5\. If you set `ThreadCount` to a higher number, AWS DMS will be able to complete the validation faster—however, it will also execute more simultaneous queries, consuming more resources on the source and the target\.

For example, the following JSON turns on validation and increases the number of threads from the default setting of 5 to 8\.

```
ValidationSettings":   {
       "EnableValidation":true,
       "ThreadCount":8
}
```

## Replication Task Statistics<a name="CHAP_Validating.TaskStatistics"></a>

When data validation is enabled, AWS DMS provides the following statistics at the table level:
+ **ValidationState**—The validation state of the table\. The parameter can have the following values:
  + **Not enabled**—Validation is not enabled for the table in the migration task\.
  + **Pending records**—Some records in the table are waiting for validation\.
  + **Mismatched records**—Some records in the table don't match between the source and target\. A mismatch might occur for a number of reasons; For more information, check the `awsdms_validation_failures` table on the target endpoint\.
  + **Suspended records**—Some records in the table can't be validated\.
  + **No primary key**—The table can't be validated because it had no primary key\.
  + **Table error**—The table wasn't validated because it was in an error state and some data wasn't migrated\.
  + **Validated**—All rows in the table are validated\. If the table is updated, the status can change from Validated\.
  + **Error**—The table can't be validated because of an unexpected error\.
+ **ValidationPending**—The number of records that have been migrated to the target, but that haven't yet been validated\.

  **ValidationSuspended**—The number of records that AWS DMS can't compare\. For example, if a record at the source is constantly being updated, AWS DMS can't compare the source and the target\. For more information, see [Error Handling Task Settings](CHAP_Tasks.CustomizingTasks.TaskSettings.ErrorHandling.md) 
+ **ValidationFailed**—The number of records that didn't pass the data validation phase\. For more information, see [Error Handling Task Settings](CHAP_Tasks.CustomizingTasks.TaskSettings.ErrorHandling.md)\.
+  **ValidationSucceededRecordCount**— Number of rows that AWS DMS validated, per minute\. 
+  **ValidationAttemptedRecordCount**— Number of rows that validation was attempted, per minute\. 
+  **ValidationFailedOverallCount**— Number of rows where validation failed\. 
+  **ValidationSuspendedOverallCount**— Number of rows where validation was suspended\. 
+  **ValidationPendingOverallCount**— Number of rows where the validation is still pending\. 
+  **ValidationBulkQuerySourceLatency**— AWS DMS can do data validation in bulk, especially in certain scenarios during a full\-load or on\-going replication when there are many changes\. This metric indicates the latency required to read a bulk set of data from the source endpoint\. 
+  **ValidationBulkQueryTargetLatency**— AWS DMS can do data validation in bulk, especially in certain scenarios during a full\-load or on\-going replication when there are many changes\. This metric indicates the latency required to read a bulk set of data on the target endpoint\. 
+  **ValidationItemQuerySourceLatency**— During on\-going replication, data validation can identify on\-going changes and validate those changes\. This metric indicates the latency in reading those changes from the source\. Validation can run more queries than required, based on number of changes, if there are errors during validation\. 
+  **ValidationItemQueryTargetLatency**— During on\-going replication, data validation can identify on\-going changes and validate the changes row by row\. This metric gives us the latency in reading those changes from the target\. Validation may run more queries than required, based on number of changes, if there are errors during validation\. 

You can view the data validation information using the console, the AWS CLI, or the AWS DMS API\.
+ On the console, you can choose to validate a task when you create or modify the task\. To view the data validation report using the console, choose the task on the **Tasks** page and choose the **Table statistics** tab in the details section\.
+ Using the CLI, set the `EnableValidation` parameter to **true** when creating or modifying a task to begin data validation\. The following example creates a task and enables data validation\.

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

## Troubleshooting<a name="CHAP_Validating.Troubleshooting"></a>

During validation, AWS DMS creates a new table at the target endpoint: `awsdms_validation_failures_v1`\. If any record enters the *ValidationSuspended* or the *ValidationFailed* state, AWS DMS writes diagnostic information to `awsdms_validation_failures_v1`\. You can query this table to help troubleshoot validation errors\.

Following is a description of the `awsdms_validation_failures_v1` table:


| Column Name | Data Type | Description | 
| --- | --- | --- | 
|  `TASK_NAME`  |  `VARCHAR(128) NOT NULL`  |  AWS DMS task identifier\.  | 
| TABLE\_OWNER | VARCHAR\(128\) NOT NULL |  Schema \(owner\) of the table\.  | 
|  `TABLE_NAME`  | VARCHAR\(128\) NOT NULL |  Table name\.  | 
| FAILURE\_TIME | DATETIME\(3\) NOT NULL |  Time when the failure occurred\.  | 
| KEY | TEXT NOT NULL |  This is the primary key for row record type\.  | 
| FAILURE\_TYPE | VARCHAR\(128\) NOT NULL |   Severity of validation error\. Can be either `Failed` or `Suspended`\.  | 

The following query will show you all the failures for a task by querying the `awsdms_validation_failures_v1` table\. The task name should be the external resource ID of the task\. The external resource ID of the task is the last value in the task ARN\. For example, for a task with an ARN value of arn:aws:dms:us\-west\-2:5599:task: VFPFKH4FJR3FTYKK2RYSI, the external resource ID of the task would be VFPFKH4FJR3FTYKK2RYSI\.

```
select * from awsdms_validation_failures_v1 where TASK_NAME = ‘VFPFKH4FJR3FTYKK2RYSI’                    
```

Once you have the primary key of the failed record, you can query the source and target endpoints to see what part of the record does not match\.

## Limitations<a name="CHAP_Validating.Limitations"></a>
+ Data validation requires that the table has a primary key or unique index\.
  + Primary key columns can't be of type `CLOB`, `BLOB`, or `BYTE`\.
  + For primary key columns of type `VARCHAR` or `CHAR`, the length must be less than 1024\.
+ If the collation of the primary key column in the target PostgreSQL instance isn't set to "C", the sort order of the primary key is different compared to the sort order in Oracle\. If the sort order is different between PostgreSQL and Oracle, data validation fails to validate the records\.
+ Data validation generates additional queries against the source and target databases\. You must ensure that both databases have enough resources to handle this additional load\. 
+ Data validation isn't supported if a migration uses customized filtering or when consolidating several databases into one\.
+  For a source or target Oracle endpoint, AWS DMS uses DBMS\_CRYPTO to validate BLOBs\. If your Oracle endpoint uses BLOBs, then you must grant the execute permission on dbms\_crypto to the user account that is used to access the Oracle endpoint\. You can do this by running the following statement:

  ```
  grant execute on sys.dbms_crypto to <dms_endpoint_user>;
  ```
+ If the target database is modified outside of AWS DMS during validation, then discrepancies might not be reported accurately\. This result can occur if one of your applications writes data to the target table, while AWS DMS is performing validation on that same table\.
+ If one or more rows are being continuously modified during the validation, then AWS DMS can't validate those rows\. However, you can validate those rows manually, after the task completes\.
+ If AWS DMS detects more than 10,000 failed or suspended records, it stops the validation\. Before you proceed further, resolve any underlying problems with the data\.