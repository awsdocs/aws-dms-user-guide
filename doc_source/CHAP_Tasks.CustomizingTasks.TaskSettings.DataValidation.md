# Data validation task settings<a name="CHAP_Tasks.CustomizingTasks.TaskSettings.DataValidation"></a>

You can ensure that your data was migrated accurately from the source to the target\. If you enable validation for a task, AWS DMS begins comparing the source and target data immediately after a full load is performed for a table\. For more information about task data validation, its requirements, the scope of its database support, and the metrics it reports, see [AWS DMS data validation](CHAP_Validating.md)\. For information about how to use a task configuration file to set task settings, see [Task settings example](CHAP_Tasks.CustomizingTasks.TaskSettings.md#CHAP_Tasks.CustomizingTasks.TaskSettings.Example)\.

 The data validation settings and their values include the following:
+ `EnableValidation` – Enables data validation when set to true\. Otherwise, validation is disabled for the task\. The default value is false\.
+ `ValidationMode` – Controls how DMS validates the data in the target table compared to source table\. AWS DMS provides this setting for future extensibility\. Currently, the default and only valid value is `ROW_LEVEL`\. AWS DMS validates all rows between the source and target tables\.
+ `FailureMaxCount` – Specifies the maximum number of records that can fail validation before validation is suspended for the task\. The default value is 10,000\. If you want the validation to continue regardless of the number of records that fail validation, set this value higher than the number of records in the source\.
+ `HandleCollationDiff` – When this option is set to `true`, the validation accounts for column collation differences in PostgreSQL and SQL Server endpoints when identifying source and target records to compare\. Otherwise, any such differences in column collation are ignored for validation\. Column collations can dictate the order of rows, which is important for data validation\. Setting `HandleCollationDiff` to true resolves those collation differences automatically and prevents false positives in data validation\. The default value is `false`\.
+ `RecordFailureDelayInMinutes` – Specifies the delay time in minutes before reporting any validation failure details\.
+ `RecordFailureDelayLimitInMinutes` – Specifies the delay before reporting any validation failure details\. Normally, AWS DMS uses the task latency to recognize actual delay for changes to make it to the target in order to prevent false positives\. This setting overrides the actual delay value and enables you to set a higher delay before reporting any validation metrics\. The default value is 0\.
+ `RecordSuspendDelayInMinutes` – Specifies the delay time in minutes before tables are suspended from validation due to error threshold set in `FailureMaxCount`\.
+ `SkipLobColumns` – When this option is set to `true`, AWS DMS skips data validation for all the LOB columns in the table's part of the task validation\. The default value is `false`\.
+ `TableFailureMaxCount` – Specifies the maximum number of rows in one table that can fail validation before validation is suspended for the table\. The default value is 1,000\. 
+ `ThreadCount` – Specifies the number of execution threads that AWS DMS uses during validation\. Each thread selects not\-yet\-validated data from the source and target to compare and validate\. The default value is 5\. If you set `ThreadCount` to a higher number, AWS DMS can complete the validation faster\. However, AWS DMS then runs more simultaneous queries, consuming more resources on the source and the target\.
+ `ValidationOnly` – When this option is set to `true`, the task previews data validation without performing any migration or data replication\. To set the `ValidationOnly` option, first set the task's **Migration type** to **Replicate data changes only** in the AWS DMS console\. Alternatively, using the AWS DMS API, set the migration type to `cdc`\. The default value is `false`\. But `ValidationOnly` set to `true` is particularly useful for the following use cases:
  + To validate data retroactively by allowing data validation without performing another full load\. You can create and run another task with `ValidationOnly` set to `true`\.
  + For a full load task, validation only begins once the initial load is complete\. However, by creating a separate `ValidationOnly` task you can see early validation results, and resolve any failures prior to actually moving *all* the data\. This approach saves time while migrating large amounts of data\.
  + To preview differences in records between the source and target before restarting an AWS DMS task\.
**Note**  
The `ValidationOnly` setting is immutable\. It can't be modified for a task after that task is created\.
+ `ValidationPartialLobSize` – Specifies if you want to do partial validation for LOB columns instead of validating all of the data stored in the column\. This is something you might find useful when you are migrating just part of the LOB data and not the whole LOB data set\. The value is in KB units\. The default value is 0, which means AWS DMS validates all the LOB column data\. For example, `"ValidationPartialLobSize": 32` means that AWS DMS only validates the first 32KB of the column data in both the source and target\.
+ `PartitionSize` – Specifies the batch size of records to read for comparison from both source and target\. The default is 10,000\.
+ `ValidationQueryCdcDelaySeconds` – The amount of time the first validation query is delayed on both source and target for each CDC update\. This might help reduce resource contention when migration latency is high\. A validation only task automatically sets this option to 180 seconds\. The default is 0\.

For example, the following JSON turns on data validation with twice the default number of threads\. It also accounts for differences in record order caused by column collation differences in PostgreSQL endpoints\. Also, it provides a validation reporting delay to account for additional time to process any validation failures\.

```
"ValidationSettings": {
     "EnableValidation": true,
     "ThreadCount": 10,
     "HandleCollationDiff": true,
     "RecordFailureDelayLimitInMinutes": 30
  }
```

**Note**  
For an Oracle endpoint, AWS DMS uses DBMS\_CRYPTO to validate BLOBs\. If your Oracle endpoint uses BLOBs, grant the `execute` permission for DBMS\_CRYPTO to the user account that accesses the Oracle endpoint\. To do this, run the following statement\.  

```
grant execute on sys.dbms_crypto to dms_endpoint_user;
```