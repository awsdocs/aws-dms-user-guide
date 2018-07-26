# Modifying a Task<a name="CHAP_Tasks.Modifying"></a>

You can modify a task if you need to change the task settings, table mapping, or other settings\. You modify a task in the DMS console by selecting the task and choosing **Modify**\. You can also use the AWS CLI or AWS DMS API command `ModifyReplicationTask`\.

There are a few limitations to modifying a task\. These include:
+ You cannot modify the source or target endpoint of a task\.
+ You cannot change the migration type of a task\.
+ A task that have been run must have a status of **Stopped** or **Failed** to be modified\.