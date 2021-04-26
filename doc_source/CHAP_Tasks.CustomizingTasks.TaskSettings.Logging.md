# Logging task settings<a name="CHAP_Tasks.CustomizingTasks.TaskSettings.Logging"></a>

Logging uses Amazon CloudWatch to log information during the migration process\. Using logging task settings, you can specify which component activities are logged and what amount of information is written to the log\. Logging task settings are written to a JSON file\. 

You can enable CloudWatch logging in several ways\. You can select the `EnableLogging` option on the AWS Management Console when you create a migration task\. Or, you can set the `EnableLogging` option to `true` when creating a task using the AWS DMS API\. You can also specify `"EnableLogging": true` in the JSON of the logging section of task settings\.

CloudWatch integrates with AWS Identity and Access Management \(IAM\), and you can specify which CloudWatch actions a user in your AWS account can perform\. For more information about working with IAM in CloudWatch, see [Identity and access management for amazon CloudWatch](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/auth-and-access-control-cw.html) and [Logging Amazon CloudWatch API calls](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/logging_cw_api_calls.html) in the *Amazon CloudWatch User Guide\.*

To delete the task logs, you can set `DeleteTaskLogs` to true in the JSON of the logging section of the task settings\.

You can specify logging for the following actions:
+ SOURCE\_UNLOAD – Data is unloaded from the source database\.
+ SOURCE\_CAPTURE – Data is captured from the source database\.
+ TARGET\_LOAD – Data is loaded into the target database\.
+ TARGET\_APPLY – Data and data definition language \(DDL\) statements are applied to the target database\.
+ TASK\_MANAGER – The task manager triggers an event\.

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
    "LogComponents": [{
        "Id": "SOURCE_UNLOAD",
        "Severity": "LOGGER_SEVERITY_DEFAULT"
    },{
        "Id": "SOURCE_CAPTURE",
        "Severity": "LOGGER_SEVERITY_DEFAULT"
    },{
        "Id": "TARGET_LOAD",
        "Severity": "LOGGER_SEVERITY_DEFAULT"
    },{
        "Id": "TARGET_APPLY",
        "Severity": "LOGGER_SEVERITY_INFO"
    },{
        "Id": "TASK_MANAGER",
        "Severity": "LOGGER_SEVERITY_DEBUG"
    }]
  }, 
…
```