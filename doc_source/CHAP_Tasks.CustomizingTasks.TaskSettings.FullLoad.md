# Full\-load task settings<a name="CHAP_Tasks.CustomizingTasks.TaskSettings.FullLoad"></a>

Full\-load settings include the following\. For information about how to use a task configuration file to set task settings, see [Task settings example](CHAP_Tasks.CustomizingTasks.TaskSettings.md#CHAP_Tasks.CustomizingTasks.TaskSettings.Example)\.
+ To indicate how to handle loading the target at full\-load startup, specify one of the following values for the `TargetTablePrepMode` option: 
  +  `DO_NOTHING` – Data and metadata of the existing target table aren't affected\. 
  +  `DROP_AND_CREATE` – The existing table is dropped and a new table is created in its place\. 
  +  `TRUNCATE_BEFORE_LOAD` – Data is truncated without affecting the table metadata\.
+ To delay primary key or unique index creation until after a full load completes, set the `CreatePkAfterFullLoad` option to `true`\.
+ For full\-load and CDC\-enabled tasks, you can set the following options for `Stop task after full load completes`: 
  + `StopTaskCachedChangesApplied` – Set this option to `true` to stop a task after a full load completes and cached changes are applied\. 
  + `StopTaskCachedChangesNotApplied` – Set this option to `true` to stop a task before cached changes are applied\. 
+ To indicate the maximum number of tables to load in parallel, set the `MaxFullLoadSubTasks` option\. The default is 8; the maximum value is 49\.
+ Set the `ParallelLoadThreads` option to indicate how many concurrent threads DMS will employ during a full\-load process to push data records to a target endpoint\. Zero is the default value \(0\)\.
**Important**  
`MaxFullLoadSubTasks` controls the number of tables or table segments to load in parallel\. `ParallelLoadThreads` controls the number of threads that are used by a migration task to execute the loads in parallel\. *These settings are multiplicative*\. As such, the total number of threads that are used during a full load task is approximately the result of the value of `ParallelLoadThreads `multiplied by the value of `MaxFullLoadSubTasks` \(`ParallelLoadThreads` **\*** `MaxFullLoadSubtasks)`\.  
If you create tasks with a high number of Full Load sub tasks and a high number of parallel load threads, your task can consume too much memory and fail\.
+ You can set the number of seconds that AWS DMS waits for transactions to close before beginning a full\-load operation\. To do so, if transactions are open when the task starts set the `TransactionConsistencyTimeout` option\. The default value is 600 \(10 minutes\)\. AWS DMS begins the full load after the timeout value is reached, even if there are open transactions\. A full\-load\-only task doesn't wait for 10 minutes but instead starts immediately\.
+ To indicate the maximum number of records that can be transferred together, set the `CommitRate` option\. The default value is 10000, and the maximum value is 50000\.