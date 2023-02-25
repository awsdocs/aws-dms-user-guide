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
+ You can set the number of seconds that AWS DMS waits for transactions to close before beginning a full\-load operation\. To do so, if transactions are open when the task starts set the `TransactionConsistencyTimeout` option\. The default value is 600 \(10 minutes\)\. AWS DMS begins the full load after the timeout value is reached, even if there are open transactions\. A full\-load\-only task doesn't wait for 10 minutes but instead starts immediately\.
+ To indicate the maximum number of records that can be transferred together, set the `CommitRate` option\. The default value is 10000, and the maximum value is 50000\.