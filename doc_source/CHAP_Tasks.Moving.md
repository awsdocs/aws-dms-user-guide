# Moving a task<a name="CHAP_Tasks.Moving"></a>

You can move a task to a different replication instance when any of the following situations apply to your use case\.
+ You're currently using an instance of a certain type and you want to switch to a different instance type\.
+ Your current instance is overloaded by many replication tasks, and you want to split the load across multiple instances\.
+ Your instance storage is full, and you want to move tasks off that instance to a more powerful instance as an alternative to scaling storage or compute\.
+ You want to use a newly released feature of AWS DMS, but don’t want to create a new task and restart the migration\. Instead, you prefer to spin up a replication instance with a new AWS DMS version that supports the feature, and move the existing task to that instance\.

You can move a task in the console by selecting the task and choosing **Move**\. You can also run the CLI command or API operation `MoveReplicationTask` to move the task\. 

Make sure that the target replication instance has enough storage space to accommodate the task that's being moved\. Otherwise, scale the storage to make space for your target replication instance before moving the task\.

Also, make sure that your target replication instance is created with the same or later AWS DMS engine version as the current replication instance\. 

**Note**  
You can't move a task to the same replication instance where it currently resides\.
You can't modify the settings of a task while it's moving\.
A task you have run must have a status of **Stopped**, **Failed**, or **Failed\-move** before you can move it\.
You can't move a task that has Amazon S3 as its target endpoint\.
You can move a task to DMS version 3\.4\.2 or above without any issues\. But you can't move a task to DMS version 3\.4\.1 due to compatibility issues\.

There are two task statuses that relate to moving a DMS task, **Moving **and **Failed\-move**\. For more information about those task status, see [Task status](CHAP_Monitoring.md#CHAP_Tasks.Status)\. 

After moving a task, you can enable and run premigration assessments to check for blocking issues before running the moved task\.