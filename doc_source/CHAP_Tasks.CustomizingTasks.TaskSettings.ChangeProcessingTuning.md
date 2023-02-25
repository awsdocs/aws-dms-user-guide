# Change processing tuning settings<a name="CHAP_Tasks.CustomizingTasks.TaskSettings.ChangeProcessingTuning"></a>

The following settings determine how AWS DMS handles changes for target tables during change data capture \(CDC\)\. Several of these settings depend on the value of the target metadata parameter `BatchApplyEnabled`\. For more information on the `BatchApplyEnabled` parameter, see [Target metadata task settings](CHAP_Tasks.CustomizingTasks.TaskSettings.TargetMetadata.md)\. For information about how to use a task configuration file to set task settings, see [Task settings example](CHAP_Tasks.CustomizingTasks.TaskSettings.md#CHAP_Tasks.CustomizingTasks.TaskSettings.Example)\.

Change processing tuning settings include the following:

The following settings apply only when the target metadata parameter `BatchApplyEnabled` is set to `true`\.
+ `BatchApplyPreserveTransaction` – If set to `true`, transactional integrity is preserved and a batch is guaranteed to contain all the changes within a transaction from the source\. The default value is `true`\. This setting applies only to Oracle target endpoints\.

  If set to `false`, there can be temporary lapses in transactional integrity to improve performance\. There is no guarantee that all the changes within a transaction from the source are applied to the target in a single batch\. 

  By default, AWS DMS processes changes in a transactional mode, which preserves transactional integrity\. If you can afford temporary lapses in transactional integrity, you can use the batch optimized apply option instead\. This option efficiently groups transactions and applies them in batches for efficiency purposes\. Using the batch optimized apply option almost always violates referential integrity constraints\. So we recommend that you turn these constraints off during the migration process and turn them on again as part of the cutover process\. 
+ `BatchApplyTimeoutMin` – Sets the minimum amount of time in seconds that AWS DMS waits between each application of batch changes\. The default value is 1\.
+ `BatchApplyTimeoutMax` – Sets the maximum amount of time in seconds that AWS DMS waits between each application of batch changes before timing out\. The default value is 30\.
+ `BatchApplyMemoryLimit` – Sets the maximum amount of memory in \(MB\) to use for pre\-processing in **Batch optimized apply mode**\. The default value is 500\.
+ `BatchSplitSize` – Sets the maximum number of changes applied in a single batch\. The default value 0, meaning there is no limit applied\.

The following settings apply only when the target metadata parameter `BatchApplyEnabled` is set to `false`\.
+ `MinTransactionSize` – Sets the minimum number of changes to include in each transaction\. The default value is 1000\.
+ `CommitTimeout` – Sets the maximum time in seconds for AWS DMS to collect transactions in batches before declaring a timeout\. The default value is 1\.

For bidirectional replication, the following setting applies only when the target metadata parameter `BatchApplyEnabled` is set to `false`\.
+ `LoopbackPreventionSettings` – These settings provide loopback prevention for each ongoing replication task in any pair of tasks involved in bidirectional replication\. *Loopback prevention* prevents identical changes from being applied in both directions of the bidirectional replication, which can corrupt data\. For more information about bidirectional replication, see [Performing bidirectional replication](CHAP_Task.CDC.md#CHAP_Task.CDC.Bidirectional)\.

AWS DMS attempts to keep transaction data in memory until the transaction is fully committed to the source, the target, or both\. However, transactions that are larger than the allocated memory or that aren't committed within the specified time limit are written to disk\.

The following settings apply to change processing tuning regardless of the change processing mode\.
+ `MemoryLimitTotal` – Sets the maximum size \(in MB\) that all transactions can occupy in memory before being written to disk\. The default value is 1024\.
+ `MemoryKeepTime` – Sets the maximum time in seconds that each transaction can stay in memory before being written to disk\. The duration is calculated from the time that AWS DMS started capturing the transaction\. The default value is 60\. 
+ `StatementCacheSize` – Sets the maximum number of prepared statements to store on the server for later execution when applying changes to the target\. The default value is 50\. The maximum value is 200\. 

Following is an example of how task settings that handle Change Processing Tuning appear in a task setting JSON file:

```
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
}
```

To control the frequency of writes to an Amazon S3 target during a data replication task, you can configure the `cdcMaxBatchInterval` and `cdcMinFileSize` extra connection attributes\. This can result in better performance when analyzing the data without any additional overhead operations\. For more information, see [Endpoint settings when using Amazon S3 as a target for AWS DMS](CHAP_Target.S3.md#CHAP_Target.S3.Configuring)\.