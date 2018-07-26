# Creating Multiple Tasks<a name="CHAP_Tasks.ReplicationTasks.MultipleTasks"></a>

In some migration scenarios, you might have to create several migration tasks\. Note that tasks work independently and can run concurrently; each task has its own initial load, CDC, and log reading process\. Tables that are related through data manipulation language \(DML\) must be part of the same task\.

Some reasons to create multiple tasks for a migration include the following:
+ The target tables for the tasks reside on different databases, such as when you are fanning out or breaking a system into multiple systems\.
+ You want to break the migration of a large table into multiple tasks by using filtering\.

**Note**  
Because each task has its own change capture and log reading process, changes are *not* coordinated across tasks\. Therefore, when using multiple tasks to perform a migration, make sure that source transactions are wholly contained within a single task\.