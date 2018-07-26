# Control Table Task Settings<a name="CHAP_Tasks.CustomizingTasks.TaskSettings.ControlTable"></a>

Control tables provide information about the AWS DMS task, as well as useful statistics that you can use to plan and manage both the current migration task and future tasks\. You can apply these task settings in a JSON file or using the **Advanced Settings** link on the **Create task** page in the AWS DMS console\. In addition to the **Apply Exceptions \(dmslogs\.awsdms\_apply\_exceptions\)** table, which is always created, you can choose to create additional tables including the following:
+ **Replication Status \(dmslogs\.awsdms\_status\)** – This table provides details about the current task including task status, amount of memory consumed by the task, number of changes not yet applied to the target, and the position in the source database from which AWS DMS is currently reading\. It also indicates if the task is a full load or change data capture \(CDC\)\.
+ **Suspended Tables \(dmslogs\.awsdms\_suspended\_tables\)** – This table provides a list of suspended tables as well as the reason they were suspended\.
+ **Replication History \(dmslogs\.awsdms\_history\)** – This table provides information about the replication history including the number and volume of records processed during the task, latency at the end of a CDC task, and other statistics\.

The **Apply Exceptions \(dmslogs\.awsdms\_apply\_exceptions\)** table contains the following parameters:


| Column | Type | Description | 
| --- | --- | --- | 
|  TASK\_NAME  |  nvchar  |  The name of the AWS DMS task\.  | 
|  TABLE\_OWNER  |  nvchar  |  The table owner\.  | 
|  TABLE\_NAME  |  nvchar  |  The table name\.  | 
|  ERROR\_TIME  |  timestamp  |  The time the exception \(error\) occurred\.  | 
|  STATEMENT  |  nvchar  |  The statement that was being run when the error occurred\.  | 
|  ERROR  |  nvchar  |  The error name and description\.  | 

The **Replication History \(dmslogs\.awsdms\_history\)** table contains the following parameters:


| Column | Type | Description | 
| --- | --- | --- | 
|  SERVER\_NAME  |  nvchar  |  The name of the machine where the replication task is running\.  | 
|  TASK\_NAME  |  nvchar  |  The name of the AWS DMS task\.  | 
|  TIMESLOT\_TYPE  |  varchar  |  One of the following values: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Tasks.CustomizingTasks.TaskSettings.ControlTable.html) If the task is running both full load and CDC, two history records are written to the time slot\.  | 
| TIMESLOT |  timestamp  |  The ending timestamp of the time slot\.  | 
|  TIMESLOT\_DURATION  |  int  |  The duration of the time slot\.  | 
|  TIMESLOT\_LATENCY  |  int  |  The target latency at the end of the time slot\. This value is only applicable to CDC time slots\.  | 
| RECORDS |  int  |  The number of records processed during the time slot\.  | 
|  TIMESLOT\_VOLUME  |  int  |  The volume of data processed in MB\.  | 

The **Replication Status \(dmslogs\.awsdms\_status\)** table contains the current status of the task and the target database\. It has the following settings:


| Column | Type | Description | 
| --- | --- | --- | 
|  SERVER\_NAME  |  nvchar  |  The name of the machine where the replication task is running\.  | 
|  TASK\_NAME  |  nvchar  |  The name of the AWS DMS task\.  | 
|  TASK\_STATUS  |  varchar  |  One of the following values: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Tasks.CustomizingTasks.TaskSettings.ControlTable.html) Task status is set to FULL LOAD as long as there is at least one table in full load\. After all tables have been loaded, the task status changes to CHANGE PROCESSING if CDC is enabled\.  | 
| STATUS\_TIME |  timestamp  |  The timestamp of the task status\.  | 
|  PENDING\_CHANGES  |  int  |  The number of change records that were not applied to the target\.  | 
|  DISK\_SWAP\_SIZE  |  int  |  The amount of disk space used by old or offloaded transactions\.  | 
| TASK\_MEMORY |  int  |  Current memory used, in MB\.  | 
|  SOURCE\_CURRENT \_POSITION  |  varchar  |  The position in the source database that AWS DMS is currently reading from\.  | 
|  SOURCE\_CURRENT \_TIMESTAMP  |  timestamp  |  The timestamp in the source database that AWS DMS is currently reading from\.  | 
|  SOURCE\_TAIL \_POSITION  |  varchar  |  The position of the oldest start transaction that is not committed\. This value is the newest position that you can revert to without losing any changes\.  | 
|  SOURCE\_TAIL \_TIMESTAMP  |  timestamp  |  The timestamp of the oldest start transaction that is not committed\. This value is the newest timestamp that you can revert to without losing any changes\.\.  | 
|  SOURCE\_TIMESTAMP \_APPLIED  |  timestamp  |  The timestamp of the last transaction commit\. In a bulk apply process, this value is the timestamp for the commit of the last transaction in the batch\.  | 

Additional control table settings include the following:
+ `ControlSchema` – Use this option to indicate the database schema name for the AWS DMS target Control Tables\. If you do not enter any information in this field, then the tables are copied to the default location in the database\.
+ `HistoryTimeslotInMinutes` – Use this option to indicate the length of each time slot in the Replication History table\. The default is 5 minutes\.