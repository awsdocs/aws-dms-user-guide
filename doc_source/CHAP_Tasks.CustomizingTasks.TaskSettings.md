# Specifying Task Settings for AWS Database Migration Service Tasks<a name="CHAP_Tasks.CustomizingTasks.TaskSettings"></a>

Each task has settings that you can configure according to the needs of your database migration\. You create these settings in a JSON file or, with some settings, you can specify the settings using the AWS DMS console\. 

There are several main types of task settings:

**Topics**
+ [Target Metadata Task Settings](CHAP_Tasks.CustomizingTasks.TaskSettings.TargetMetadata.md)
+ [Full Load Task Settings](CHAP_Tasks.CustomizingTasks.TaskSettings.FullLoad.md)
+ [Logging Task Settings](CHAP_Tasks.CustomizingTasks.TaskSettings.Logging.md)
+ [Control Table Task Settings](CHAP_Tasks.CustomizingTasks.TaskSettings.ControlTable.md)
+ [Stream Buffer Task Settings](CHAP_Tasks.CustomizingTasks.TaskSettings.StreamBuffer.md)
+ [Change Processing Tuning Settings](CHAP_Tasks.CustomizingTasks.TaskSettings.ChangeProcessingTuning.md)
+ [Data Validation Task Settings](CHAP_Tasks.CustomizingTasks.TaskSettings.DataValidation.md)
+ [Change Processing DDL Handling Policy Task Settings](CHAP_Tasks.CustomizingTasks.TaskSettings.DDLHandling.md)
+ [Error Handling Task Settings](CHAP_Tasks.CustomizingTasks.TaskSettings.ErrorHandling.md)
+ [Saving Task Settings](CHAP_Tasks.CustomizingTasks.TaskSettings.Saving.md)


| Task Settings | Relevant Documentation | 
| --- | --- | 
|   **Creating a Task Assessment Report**  You can create a task assessment report that shows any unsupported data types that could cause problems during migration\. You can run this report on your task before running the task to find out potential issues\.  |  [Creating a Task Assessment Report](CHAP_Tasks.AssessmentReport.md)  | 
|   **Creating a Task**  When you create a task, you specify the source, target, and replication instance, along with any migration settings\.  |  [Creating a Task](CHAP_Tasks.Creating.md)  | 
|   **Creating an Ongoing Replication Task**  You can setup a task to provide continuous replication between the source and target\.   |  [Creating Tasks for Ongoing Replication Using AWS DMS ](CHAP_Task.CDC.md)  | 
|   **Applying Task Settings**  Each task has settings that you can configure according to the needs of your database migration\. You create these settings in a JSON file or, with some settings, you can specify the settings using the AWS DMS console\.  |  [Specifying Task Settings for AWS Database Migration Service Tasks](#CHAP_Tasks.CustomizingTasks.TaskSettings)  | 
|   **Data Validation**  Data validation is a task setting you can use to have AWS DMS compare the data on your target data store with the data from your source data store\.  |  [Validating AWS DMS Tasks](CHAP_Validating.md)\.  | 
|   **Modifying a Task**  When a task is stopped, you can modify the settings for the task\.  |  [Modifying a Task](CHAP_Tasks.Modifying.md)  | 
|   **Reloading Tables During a Task**  You can reload a table during a task if an error occurs during the task\.  |  [Reloading Tables During a Task](CHAP_Tasks.ReloadTables.md)  | 
|   **Using Table Mapping**  Table mapping uses several types of rules to specify task settings for the data source, source schema, data, and any transformations that should occur during the task\.  |  Selection Rules [ Selection Rules and Actions ](CHAP_Tasks.CustomizingTasks.TableMapping.md#CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Selections) Transformation Rules [ Transformation Rules and Actions ](CHAP_Tasks.CustomizingTasks.TableMapping.md#CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Transformations)  | 
|   **Applying Filters**  You can use source filters to limit the number and type of records transferred from your source to your target\. For example, you can specify that only employees with a location of headquarters are moved to the target database\. You apply filters on a column of data\.  |  [Using Source Filters](CHAP_Tasks.CustomizingTasks.TableMapping.md#CHAP_Tasks.CustomizingTasks.Filters)  | 
| Monitoring a Task There are several ways to get information on the performance of a task and the tables used by the task\.  |  [Monitoring AWS DMS Tasks](CHAP_Monitoring.md)  | 
| Managing Task Logs You can view and delete task logs using the AWS DMS API or AWS CLI\.   |  [Managing AWS DMS Task Logs](CHAP_Monitoring.md#CHAP_Monitoring.ManagingLogs)  | 

A task settings JSON file can look like this:

```
{
  "TargetMetadata": {
    "TargetSchema": "",
    "SupportLobs": true,
    "FullLobMode": false,
    "LobChunkSize": 64,
    "LimitedSizeLobMode": true,
    "LobMaxSize": 32,
    "BatchApplyEnabled": true
  },
  "FullLoadSettings": {
    "TargetTablePrepMode": "DO_NOTHING",
    "CreatePkAfterFullLoad": false,
    "StopTaskCachedChangesApplied": false,
    "StopTaskCachedChangesNotApplied": false,
    "MaxFullLoadSubTasks": 8,
    "TransactionConsistencyTimeout": 600,
    "CommitRate": 10000
  },
  "Logging": {
    "EnableLogging": false
  },
  "ControlTablesSettings": {
    "ControlSchema":"",
    "HistoryTimeslotInMinutes":5,
    "HistoryTableEnabled": false,
    "SuspendedTablesTableEnabled": false,
    "StatusTableEnabled": false
  },
  "StreamBufferSettings": {
    "StreamBufferCount": 3,
    "StreamBufferSizeInMB": 8
  },
  "ChangeProcessingTuning": { 
    "BatchApplyPreserveTransaction": true, 
    "BatchApplyTimeoutMin": 1, 
    "BatchApplyTimeoutMax": 30, 
    "BatchApplyMemoryLimit": 500, 
    "BatchSplitSize": 0, 
    "MinTransactionSize": 1000, 
    "CommitTimeout": 1, 
    "MemoryLimitTotal": 1024, 
    "MemoryKeepTime": 60, 
    "StatementCacheSize": 50 
  },
  "ChangeProcessingDdlHandlingPolicy": {
    "HandleSourceTableDropped": true,
    "HandleSourceTableTruncated": true,
    "HandleSourceTableAltered": true
  },
  "ValidationSettings": {
     "EnableValidation": false,
     "ThreadCount": 5
  },
  "ErrorBehavior": {
    "DataErrorPolicy": "LOG_ERROR",
    "DataTruncationErrorPolicy":"LOG_ERROR",
    "DataErrorEscalationPolicy":"SUSPEND_TABLE",
    "DataErrorEscalationCount": 50,
    "TableErrorPolicy":"SUSPEND_TABLE",
    "TableErrorEscalationPolicy":"STOP_TASK",
    "TableErrorEscalationCount": 50,
    "RecoverableErrorCount": 0,
    "RecoverableErrorInterval": 5,
    "RecoverableErrorThrottling": true,
    "RecoverableErrorThrottlingMax": 1800,
    "ApplyErrorDeletePolicy":"IGNORE_RECORD",
    "ApplyErrorInsertPolicy":"LOG_ERROR",
    "ApplyErrorUpdatePolicy":"LOG_ERROR",
    "ApplyErrorEscalationPolicy":"LOG_ERROR",
    "ApplyErrorEscalationCount": 0,
    "FullLoadIgnoreConflicts": true
  }
}
```