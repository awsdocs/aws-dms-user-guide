# Logging task settings<a name="CHAP_Tasks.CustomizingTasks.TaskSettings.Logging"></a>

Logging uses Amazon CloudWatch to log information during the migration process\. Using logging task settings, you can specify which component activities are logged and what amount of information is written to the log\. Logging task settings are written to a JSON file\. For information about how to use a task configuration file to set task settings, see [Task settings example](CHAP_Tasks.CustomizingTasks.TaskSettings.md#CHAP_Tasks.CustomizingTasks.TaskSettings.Example)\. 

You can turn on CloudWatch logging in several ways\. You can select the `EnableLogging` option on the AWS Management Console when you create a migration task\. Or, you can set the `EnableLogging` option to `true` when creating a task using the AWS DMS API\. You can also specify `"EnableLogging": true` in the JSON of the logging section of task settings\.

CloudWatch integrates with AWS Identity and Access Management \(IAM\), and you can specify which CloudWatch actions a user in your AWS account can perform\. For more information about working with IAM in CloudWatch, see [Identity and access management for amazon CloudWatch](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/auth-and-access-control-cw.html) and [Logging Amazon CloudWatch API calls](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/logging_cw_api_calls.html) in the *Amazon CloudWatch User Guide\.*

To delete the task logs, you can set `DeleteTaskLogs` to true in the JSON of the logging section of the task settings\.

You can specify logging for the following types of events:
+ `FILE_FACTORY` – The file factory manages files used for batch apply and batch load, and manages Amazon S3 endpoints\.
+ `METADATA_MANAGER` – The metadata manager manages source and target metadata, partitioning, and table state during replication\.
+ `SORTER` – The `SORTER` receives incoming events from the `SOURCE_CAPTURE` process\. The events are batched in transactions, and passed to the `TARGET_APPLY` service component\. If the `SOURCE_CAPTURE` process produces events faster than the `TARGET_APPLY` component can consume them, the `SORTER` component caches the backlogged events to disk or to a swap file\. Cached events are a common cause for running out of storage in replication instances\.

  The `SORTER` service component manages cached events, gathers CDC statistics, and reports task latency\.
+ `SOURCE_CAPTURE` – Ongoing replication \(CDC\) data is captured from the source database or service, and passed to the SORTER service component\.
+ `SOURCE_UNLOAD` – Data is unloaded from the source database or service during Full Load\.
+ `TABLES_MANAGER` — The table manager tracks captured tables, manages the order of table migration, and collects table statistics\.
+ `TARGET_APPLY` – Data and data definition language \(DDL\) statements are applied to the target database\.
+ `TARGET_LOAD` – Data is loaded into the target database\.
+ `TASK_MANAGER` – The task manager manages running tasks, and breaks tasks down into sub\-tasks for parallel data processing\.
+ `TRANSFORMATION` – Table\-mapping transformation events\. For more information, see [Using table mapping to specify task settings](CHAP_Tasks.CustomizingTasks.TableMapping.md)\.
+ `VALIDATOR/ VALIDATOR_EXT` – The `VALIDATOR` service component verifies that data was migrated accurately from the source to the target\. For more information, see [Data validation](CHAP_Validating.md)\. 

The following logging components generate a large amount of logs when using the `LOGGER_SEVERITY_DETAILED_DEBUG` log severity level:
+ `COMMON`
+ `ADDONS`
+ `DATA_STRUCTURE`
+ `COMMUNICATION`
+ `FILE_TRANSFER`
+ `FILE_FACTORY`

Logging levels other than `DEFAULT` are rarely needed for these components during troubleshooting\. We do not recommend changing the logging level from `DEFAULT` for these components unless specifically requested by AWS Support\.

After you specify one of the preceding, you can then specify the amount of information that is logged, as shown in the following list\. 

The levels of severity are in order from lowest to highest level of information\. The higher levels always include information from the lower levels\. 
+ LOGGER\_SEVERITY\_ERROR – Error messages are written to the log\.
+ LOGGER\_SEVERITY\_WARNING – Warnings and error messages are written to the log\.
+ LOGGER\_SEVERITY\_INFO – Informational messages, warnings, and error messages are written to the log\.
+ LOGGER\_SEVERITY\_DEFAULT – Informational messages, warnings, and error messages are written to the log\.
+ LOGGER\_SEVERITY\_DEBUG – Debug messages, informational messages, warnings, and error messages are written to the log\.
+ LOGGER\_SEVERITY\_DETAILED\_DEBUG – All information is written to the log\.

The following JSON example shows task settings for logging all actions and levels of severity\.

```
…
  "Logging": {
    "EnableLogging": true,
    "LogComponents": [
      {
        "Id": "FILE_FACTORY",
        "Severity": "LOGGER_SEVERITY_DEFAULT"
      },{
        "Id": "METADATA_MANAGER",
        "Severity": "LOGGER_SEVERITY_DEFAULT"
      },{
        "Id": "SORTER",
        "Severity": "LOGGER_SEVERITY_DEFAULT"
      },{
        "Id": "SOURCE_CAPTURE",
        "Severity": "LOGGER_SEVERITY_DEFAULT"
      },{
        "Id": "SOURCE_UNLOAD",
        "Severity": "LOGGER_SEVERITY_DEFAULT"
      },{
        "Id": "TABLES_MANAGER",
        "Severity": "LOGGER_SEVERITY_DEFAULT"
      },{
        "Id": "TARGET_APPLY",
        "Severity": "LOGGER_SEVERITY_DEFAULT"
      },{
        "Id": "TARGET_LOAD",
        "Severity": "LOGGER_SEVERITY_INFO"
      },{
        "Id": "TASK_MANAGER",
        "Severity": "LOGGER_SEVERITY_DEBUG"
      },{
        "Id": "TRANSFORMATION",
        "Severity": "LOGGER_SEVERITY_DEBUG"
      },{
        "Id": "VALIDATOR",
        "Severity": "LOGGER_SEVERITY_DEFAULT"
      }
    ],
    "CloudWatchLogGroup": null,
    "CloudWatchLogStream": null
  }, 
…
```