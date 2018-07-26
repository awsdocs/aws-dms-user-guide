# Full Load Task Settings<a name="CHAP_Tasks.CustomizingTasks.TaskSettings.FullLoad"></a>

Full load settings include the following:
+ To indicate how to handle loading the target at full\-load startup, specify one of the following values for the `TargetTablePrepMode` option: 
  +  `DO_NOTHING` – Data and metadata of the existing target table are not affected\. 
  +  `DROP_AND_CREATE` – The existing table is dropped and a new table is created in its place\. 
  +  `TRUNCATE_BEFORE_LOAD` – Data is truncated without affecting the table metadata\.
+ To delay primary key or unique index creation until after full load completes, set the `CreatePkAfterFullLoad` option\.  When this option is selected, you cannot resume incomplete full load tasks\. 
+ For full load and CDC\-enabled tasks, you can set the following `Stop task after full load completes` options: 
  + `StopTaskCachedChangesApplied` – Set this option to `true` to stop a task after a full load completes and cached changes are applied\. 
  + `StopTaskCachedChangesNotApplied` – Set this option to `true` to stop a task before cached changes are applied\. 
+ `MaxFullLoadSubTasks` – Set this option to indicate the maximum number of tables to load in parallel\. The default is 8; the maximum value is 50\.
+ To set the number of seconds that AWS DMS waits for transactions to close before beginning a full\-load operation, if transactions are open when the task starts, set the `TransactionConsistencyTimeout` option\. The default value is 600 \(10 minutes\)\. AWS DMS begins the full load after the timeout value is reached, even if there are open transactions\. Note that a full\-load only task does not wait for 10 minutes and will start immediately\.
+ To indicate the maximum number of events that can be transferred together, set the `CommitRate` option\. 