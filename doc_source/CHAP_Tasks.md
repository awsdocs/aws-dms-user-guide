# Working with AWS DMS tasks<a name="CHAP_Tasks"></a>

An AWS Database Migration Service \(AWS DMS\) task is where all the work happens\. You specify what tables \(or views\) and schemas to use for your migration and any special processing, such as logging requirements, control table data, and error handling\. 

A task can consist of three major phases:
+ Migration of existing data \(Full load\)
+ The application of cached changes
+ Ongoing replication \(Change Data Capture\)

For more information and an overview of how AWS DMS migration tasks migrate data, see [High\-level view of AWS DMS](CHAP_Introduction.HighLevelView.md)

When creating a migration task, you need to know several things:
+ Before you can create a task, make sure that you create a source endpoint, a target endpoint, and a replication instance\. 
+ You can specify many task settings to tailor your migration task\. You can set these by using the AWS Management Console, AWS Command Line Interface \(AWS CLI\), or AWS DMS API\. These settings include specifying how migration errors are handled, error logging, and control table information\. For information about how to use a task configuration file to set task settings, see [Task settings example](CHAP_Tasks.CustomizingTasks.TaskSettings.md#CHAP_Tasks.CustomizingTasks.TaskSettings.Example)\.
+ After you create a task, you can run it immediately\. The target tables with the necessary metadata definitions are automatically created and loaded, and you can specify ongoing replication\.
+ By default, AWS DMS starts your task as soon as you create it\. However, in some situations, you might want to postpone the start of the task\. For example, when using the AWS CLI, you might have a process that creates a task and a different process that starts the task based on some triggering event\. As needed, you can postpone your task's start\. 
+ You can monitor, stop, or restart tasks using the console, AWS CLI, or AWS DMS API\. For information about stopping a task using the AWS DMS API, see [StopReplicationTask](https://docs.aws.amazon.com/dms/latest/APIReference/API_StopReplicationTask.html) in the [AWS DMS API Reference](https://docs.aws.amazon.com/dms/latest/APIReference/)\.

The following are actions that you can do when working with an AWS DMS task\.


| Task | Relevant documentation | 
| --- | --- | 
|   **Creating a task**  When you create a task, you specify the source, target, and replication instance, along with any migration settings\.  |  [Creating a task](CHAP_Tasks.Creating.md)  | 
|   **Creating an ongoing replication task**  You can set up a task to provide continuous replication between the source and target\.   |  [Creating tasks for ongoing replication using AWS DMS](CHAP_Task.CDC.md)  | 
|   **Applying task settings**  Each task has settings that you can configure according to the needs of your database migration\. You create these settings in a JSON file or, with some settings, you can specify the settings using the AWS DMS console\. For information about how to use a task configuration file to set task settings, see [Task settings example](CHAP_Tasks.CustomizingTasks.TaskSettings.md#CHAP_Tasks.CustomizingTasks.TaskSettings.Example)\.  |  [Specifying task settings for AWS Database Migration Service tasks](CHAP_Tasks.CustomizingTasks.TaskSettings.md)  | 
|   **Using table mapping**  Table mapping specifies additional task settings for tables using several types of rules\. These rules allows you to specify the data source, source schema, tables and views, data, any table and data transformations that are to occur during the task, and settings for how these tables and columns are migrated from the source to the target\.  |  Selection rules  [Selection rules and actions](CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Selections.md) Transformation rules  [ Transformation rules and actions](CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Transformations.md)Table\-settings rules [Table and collection settings rules and operations](CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Tablesettings.md) | 
|   **Running premigration task assessments**  You can enable and run premigration task assessments showing issues with a supported source and target database that can cause problems during a migration\. This can include issues such as unsupported data types, mismatched indexes and primary keys, and other conflicting task settings\. These premigration assessments run before you run the task to identify potential issues before they occur during a migration\.  |  [Enabling and working with premigration assessments for a task](CHAP_Tasks.AssessmentReport.md)  | 
|   **Data validation**  Data validation is a task setting you can use to have AWS DMS compare the data on your target data store with the data from your source data store\.  |  [AWS DMS data validation](CHAP_Validating.md)\.  | 
|   **Modifying a task**  When a task is stopped, you can modify the settings for the task\.  |  [Modifying a task](CHAP_Tasks.Modifying.md)  | 
|   **Moving a task**  When a task is stopped, you can move the task to a different replication instance\.  |  [Moving a task](CHAP_Tasks.Moving.md)  | 
|   **Reloading tables during a task**  You can reload a table during a task if an error occurs during the task\.  |  [Reloading tables during a task](CHAP_Tasks.ReloadTables.md)  | 
|   **Applying filters**  You can use source filters to limit the number and type of records transferred from your source to your target\. For example, you can specify that only employees with a location of headquarters are moved to the target database\. You apply filters on a column of data\.  |  [Using source filters](CHAP_Tasks.CustomizingTasks.Filters.md)  | 
| Monitoring a task There are several ways to get information on the performance of a task and the tables used by the task\.  |  [Monitoring AWS DMS tasks](CHAP_Monitoring.md)  | 
| Managing task logs You can view and delete task logs using the AWS DMS API or AWS CLI\.   |  [Viewing and managing AWS DMS task logs](CHAP_Monitoring.md#CHAP_Monitoring.ManagingLogs)  | 

**Topics**
+ [Creating a task](CHAP_Tasks.Creating.md)
+ [Creating tasks for ongoing replication using AWS DMS](CHAP_Task.CDC.md)
+ [Modifying a task](CHAP_Tasks.Modifying.md)
+ [Moving a task](CHAP_Tasks.Moving.md)
+ [Reloading tables during a task](CHAP_Tasks.ReloadTables.md)
+ [Using table mapping to specify task settings](CHAP_Tasks.CustomizingTasks.TableMapping.md)
+ [Using source filters](CHAP_Tasks.CustomizingTasks.Filters.md)
+ [Enabling and working with premigration assessments for a task](CHAP_Tasks.AssessmentReport.md)
+ [Specifying supplemental data for task settings](CHAP_Tasks.TaskData.md)