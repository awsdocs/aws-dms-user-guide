# Working with AWS DMS Tasks<a name="CHAP_Tasks"></a>

An AWS Database Migration Service \(AWS DMS\) task is where all the work happens\. You use tasks to migrate data from the source endpoint to the target endpoint, and the task processing is done on the replication instance\. You specify what tables and schemas to use for your migration and any special processing, such as logging requirements, control table data, and error handling\. 

There are several things you need to know when creating a task:
+ You must create a source endpoint, a target endpoint, and a replication instance before you can create a migration task\. 
+ There are a significant number of task settings you can specify to tailor your migration task\. You can set these when using the AWS DMS console, AWS CLI, or DMS API\. These settings include specifying how migration errors are handled, error logging, and control table information\. 
+ Once you create a task, you can run it immediately\. The target tables with the necessary metadata definitions are automatically created and loaded, and you can specify ongoing replication\.
+ By default AWS DMS starts your task as soon as you create it\. However, in some situations, you might want to postpone the start of the task\. For example, when using the AWS Command Line Interface \(AWS CLI\), you might have a process that creates a task and a different process that starts the task based on some triggering event\. As needed, you can postpone your task's start\.
+ You can monitor, stop, or restart tasks using the AWS DMS console, AWS CLI, or AWS DMS API\.

The following are actions you can do when working with an AWS DMS task


| Task | Relevant Documentation | 
| --- | --- | 
|   **Creating a Task Assessment Report**  You can create a task assessment report that shows any unsupported data types that could cause problems during migration\. You can run this report on your task before running the task to find out potential issues\.  |  [Creating a Task Assessment Report](CHAP_Tasks.AssessmentReport.md)  | 
|   **Creating a Task**  When you create a task, you specify the source, target, and replication instance, along with any migration settings\.  |  [Creating a Task](CHAP_Tasks.Creating.md)  | 
|   **Creating an Ongoing Replication Task**  You can setup a task to provide continuous replication between the source and target\.   |  [Creating Tasks for Ongoing Replication Using AWS DMS ](CHAP_Task.CDC.md)  | 
|   **Applying Task Settings**  Each task has settings that you can configure according to the needs of your database migration\. You create these settings in a JSON file or, with some settings, you can specify the settings using the AWS DMS console\.  |  [Specifying Task Settings for AWS Database Migration Service Tasks](CHAP_Tasks.CustomizingTasks.TaskSettings.md)  | 
|   **Data Validation**  Data validation is a task setting you can use to have AWS DMS compare the data on your target data store with the data from your source data store\.  |  [Validating AWS DMS Tasks](CHAP_Validating.md)\.  | 
|   **Modifying a Task**  When a task is stopped, you can modify the settings for the task\.  |  [Modifying a Task](CHAP_Tasks.Modifying.md)  | 
|   **Reloading Tables During a Task**  You can reload a table during a task if an error occurs during the task\.  |  [Reloading Tables During a Task](CHAP_Tasks.ReloadTables.md)  | 
|   **Using Table Mapping**  Table mapping uses several types of rules to specify the data source, source schema, data, and any transformations that should occur during the task\.  |  Selection Rules [ Selection Rules and Actions ](CHAP_Tasks.CustomizingTasks.TableMapping.md#CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Selections) Transformation Rules [ Transformation Rules and Actions ](CHAP_Tasks.CustomizingTasks.TableMapping.md#CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Transformations)  | 
|   **Applying Filters**  You can use source filters to limit the number and type of records transferred from your source to your target\. For example, you can specify that only employees with a location of headquarters are moved to the target database\. You apply filters on a column of data\.  |  [Using Source Filters](CHAP_Tasks.CustomizingTasks.TableMapping.md#CHAP_Tasks.CustomizingTasks.Filters)  | 
| Monitoring a Task There are several ways to get information on the performance of a task and the tables used by the task\.  |  [Monitoring AWS DMS Tasks](CHAP_Monitoring.md)  | 
| Managing Task Logs You can view and delete task logs using the AWS DMS API or AWS CLI\.   |  [Managing AWS DMS Task Logs](CHAP_Monitoring.md#CHAP_Monitoring.ManagingLogs)  | 