# Selecting the best size for a replication instance<a name="CHAP_BestPractices.SizingReplicationInstance"></a>

Choosing the appropriate replication instance depends on several factors of your use case\. To help understand how replication instance resources are used, see the following discussion\. It covers the common scenario of a full load \+ CDC task\. 

During a full load task, AWS DMS loads tables individually\. By default, eight tables are loaded at a time\. AWS DMS captures ongoing changes to the source during a full load task so the changes can be applied later on the target endpoint\. The changes are cached in memory; if available memory is exhausted, changes are cached to disk\. When a full load task completes for a table, AWS DMS immediately applies the cached changes to the target table\.

After all outstanding cached changes for a table have been applied, the target endpoint is in a transactionally consistent state\. At this point, the target is in\-sync with the source endpoint with respect to the last cached changes\. AWS DMS then begins ongoing replication between the source and target\. To do so, AWS DMS takes change operations from the source transaction logs and applies them to the target in a transactionally consistent manner\. \(This process assumes batch optimized apply isn't selected\)\. AWS DMS streams ongoing changes through memory on the replication instance, if possible\. Otherwise, AWS DMS writes changes to disk on the replication instance until they can be applied on the target\.

You have some control over how the replication instance handles change processing, and how memory is used in that process\. For more information on how to tune change processing, see [Change processing tuning settings](CHAP_Tasks.CustomizingTasks.TaskSettings.ChangeProcessingTuning.md)\. 

## Factors to consider<a name="CHAP_BestPractices.SizingReplicationInstance.Factors"></a>

 Memory and disk space are key factors in selecting an appropriate replication instance for your use case\. Following, you can find a discussion of the use case characteristics to analyze to choose a replication instance\. 
+ Database and table size

   Data volume helps determine the task configuration to optimize full load performance\. For example, for two 1 TB schemas, you can partition tables into four tasks of 500 GB and run them in parallel\. The possible parallelism depends on the CPU resource available in the replication instance\. That's why it's a good idea understand the size of your database and tables to optimize full load performance\. It helps determine the number of tasks that you can possibly have\. 
+ Large objects

   The data types that are present in your migration scope can affect performance\. Particularly, large objects \(LOBs\) impact performance and memory consumption\. To migrate a LOB value, AWS DMS performs a two\-step process\. First, AWS DMS inserts the row into the target without the LOB value\. Second, AWS DMS updates the row with the LOB value\. This has an impact on the memory, so it's important to identify LOB columns in the source and analyze their size\. 
+ Load frequency and transaction size

   Load frequency and transactions per second \(TPS\) influence memory usage\. A high number of TPS or data manipulation language \(DML\) activities leads to high usage of memory\. This happens because DMS caches the changes until they are applied to the target\. During CDC, this leads to swapping \(writing to the physical disk due to memory overflow\), which causes latency\. 
+ Table keys and referential integrity

   Information about the keys of the table determines the CDC mode \(batch apply or transactional apply\) that you use to migrate data\. In general, transactional apply is slower than batch apply\. For long\-running transactions, there can be many changes to migrate\. When you use transactional apply, AWS DMS might require more memory to store the changes compared to batch apply\. If you migrate tables without primary keys, batch apply will fail and the DMS task moves to transactional apply mode\. When referential integrity is active between tables during CDC, AWS DMS uses transactional apply by default\. For more information about batch apply compared to transactional apply, see [How can I use the DMS batch apply feature to improve CDC replication performance? ](http://aws.amazon.com/premiumsupport/knowledge-center/dms-batch-apply-cdc-replication/)\. 

 Use these metrics to determine if you need the replication instance to be compute optimized or memory optimized\. 

## Common issues<a name="CHAP_BestPractices.SizingReplicationInstance.Issues"></a>

 You might face the following common issues that cause resource contention on the replication instance during migration\. For information on the replication instance metrics, see [Replication instance metrics](CHAP_Monitoring.md#CHAP_Monitoring.Metrics.CloudWatch)\. 
+  If the memory in a replication instance becomes insufficient, this results in writing data to the disk\. Reading from the disk can cause latency, which you can avoid by sizing the replication instance with enough memory\. 
+  The disk size assigned to the replication instance can be smaller than required\. The disk size is used when data in memory spills over; it's also used to store the task logs\. The maximum IOPS depends on it too\. 
+  Running multiple tasks or tasks with high parallelism affects CPU consumption of the replication instance\. This slows down the processing of the tasks and results in latency\. 

## Best practices<a name="CHAP_BestPractices.SizingReplicationInstance.BestPractices"></a>

 Consider these two most common best practices when sizing a replication instance\. For more information, see [Best practices for AWS Database Migration Service](CHAP_BestPractices.md)\. 

1.  Size your workload and understand if it's computer\-intensive or memory\-intensive\. Based on this, you can determine the class and size of the replication instance:
   +  AWS DMS processes LOBs in memory\. This operation requires a fair amount of memory\. 
   +  The number of tasks and the number of threads impact CPU consumption\. Avoid using more than eight `MaxFullLoadSubTasks` during the full load operation\. 

1.  Increase the disk space assigned to the replication instance when you have a high workload during full load\. Doing this lets the replication instance use the maximum IOPS assigned to it\. 

 The preceding guidelines don't cover all possible scenarios\. It's important to consider the specifics of your particular use case when you determine the size of your replication instance\. 

 The preceding tests show CPU and memory vary with different workloads\. Particularly, LOBs affect memory, and task count or parallelism affect the CPU\. After your migration is running, monitor the CPU, freeable memory, free storage, and IOPS of your replication instance\. Based on the data you gather, you can size your replication instance up or down as needed\. 