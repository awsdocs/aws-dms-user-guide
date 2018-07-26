# Change Processing Tuning Settings<a name="CHAP_Tasks.CustomizingTasks.TaskSettings.ChangeProcessingTuning"></a>

The following settings determine how AWS DMS handles changes for target tables during change data capture \(CDC\)\. Several of these settings depend on the value of the target metadata parameter `BatchApplyEnabled`\. For more information on the `BatchApplyEnabled` parameter, see [Target Metadata Task Settings](CHAP_Tasks.CustomizingTasks.TaskSettings.TargetMetadata.md)\.

Change processing tuning settings include the following:

The following settings apply only when the target metadata parameter `BatchApplyEnabled` is set to `true`\.
+ `BatchApplyPreserveTransaction` – If set to `true`, transactional integrity is preserved and a batch is guaranteed to contain all the changes within a transaction from the source\. The default value is `true`\. This setting applies only to Oracle target endpoints\.

  If set to `false`, there can be temporary lapses in transactional integrity to improve performance\. There is no guarantee that all the changes within a transaction from the source will be applied to the target in a single batch\. 
+ `BatchApplyTimeoutMin` – Sets the minimum amount of time in seconds that AWS DMS waits between each application of batch changes\. The default value is 1\.
+ `BatchApplyTimeoutMax` – Sets the maximum amount of time in seconds that AWS DMS waits between each application of batch changes before timing out\. The default value is 30\.
+ `BatchApplyMemoryLimit` – Sets the maximum amount of memory in \(MB\) to use for pre\-processing in **Batch optimized apply mode**\. The default value is 500\.
+ `BatchSplitSize` – Sets the maximum number of changes applied in a single batch\. The default value 0, meaning there is no limit applied\.

The following settings apply only when the target metadata parameter `BatchApplyEnabled` is set to `false`\.
+ `MinTransactionSize` – Sets the minimum number of changes to include in each transaction\. The default value is 1000\.
+ `CommitTimeout` – Sets the maximum time in seconds for AWS DMS to collect transactions in batches before declaring a timeout\. The default value is 1\.
+ `HandleSourceTableAltered` – Set this option to `true` to alter the target table when the source table is altered\.

AWS DMS attempts to keep transaction data in memory until the transaction is fully committed to the source and/or the target\. However, transactions that are larger than the allocated memory or that are not committed within the specified time limit are written to disk\.

The following settings apply to change processing tuning regardless of the change processing mode\.
+ `MemoryLimitTotal` – Sets the maximum size \(in MB\) that all transactions can occupy in memory before being written to disk\. The default value is 1024\.
+ `MemoryKeepTime` – Sets the maximum time in seconds that each transaction can stay in memory before being written to disk\. The duration is calculated from the time that AWS DMS started capturing the transaction\. The default value is 60\. 
+ `StatementCacheSize` – Sets the maximum number of prepared statements to store on the server for later execution when applying changes to the target\. The default value is 50\. The maximum value is 200\. 