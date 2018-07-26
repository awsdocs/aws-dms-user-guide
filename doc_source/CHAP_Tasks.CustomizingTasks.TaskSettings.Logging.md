# Logging Task Settings<a name="CHAP_Tasks.CustomizingTasks.TaskSettings.Logging"></a>

Logging task settings are written to a JSON file and they let you specify which component activities are logged and what amount of information is written to the log\. The logging feature uses Amazon CloudWatch to log information during the migration process\.

There are several ways to enable Amazon CloudWatch logging\. You can select the `EnableLogging` option on the AWS Management Console when you create a migration task or set the `EnableLogging` option to `true` when creating a task using the AWS DMS API\. You can also specify `"EnableLogging": true` in the JSON of the logging section of task settings\.

To delete the task logs, you can specify `"DeleteTaskLogs": true` in the JSON of the logging section of task settings\.

You can specify logging for the following component activities:
+ SOURCE\_UNLOAD — Data is unloaded from the source database\.
+ SOURCE\_CAPTURE — Data is captured from the source database\.
+ TARGET\_LOAD — Data is loaded into the target database\.
+ TARGET\_APPLY — Data and DDL are applied to the target database\.
+ TASK\_MANAGER — The task manager triggers an event\.

Once you specify a component activity, you can then specify the amount of information that is logged\. The following list is in order from the lowest level of information to the highest level of information\. The higher levels always include information from the lower levels\. These severity values include:
+ LOGGER\_SEVERITY\_ERROR — Error messages are written to the log\.
+ LOGGER\_SEVERITY\_WARNING — Warnings and error messages are written to the log\.
+ LOGGER\_SEVERITY\_INFO — Informational messages, warnings, and error messages are written to the log\.
+ LOGGER\_SEVERITY\_DEFAULT — Informational messages, warnings, and error messages are written to the log\.
+ LOGGER\_SEVERITY\_DEBUG — Debug messages, informational messages, warnings, and error messages are written to the log\.
+ LOGGER\_SEVERITY\_DETAILED\_DEBUG — All information is written to the log\.

For example, the following JSON section gives task settings for logging for all component activities:

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