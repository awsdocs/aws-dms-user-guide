# Choosing the best size for a replication instance<a name="CHAP_BestPractices.SizingReplicationInstance"></a>

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

## Performance tests<a name="CHAP_BestPractices.SizingReplicationInstance.PerformanceTests"></a>

 We used r5\.4xlarge and r5\.8xlarge instances in the following tests\. We migrated data from an Amazon EC2 Oracle instance to an Amazon Aurora PostgreSQL instance\. We located both databases in the same virtual private cloud \(VPC\) to remove network bottlenecks\. We used the default DMS task settings, including [ MaxFullLoadSubtasks](CHAP_Tasks.CustomizingTasks.TaskSettings.FullLoad.md) = 8\. This parameter limits the number of tables during the full load phase to eight\. 

 We ran separate tests for full load and CDC\. 

### Full load performance tests<a name="CHAP_BestPractices.SizingReplicationInstance.PerformanceTests.FullLoad"></a>

 The first test of the full load section shows how an increase in the number of tasks during a full load affects CPU consumption\. One major reason to have multiple tasks is the size of the databases and tables of the workload\. In this test, we ran up to seven tasks on the r5\.4xlarge and r5\.8xlarge replication instances and monitored the growth of CPU usage\. Each task in this test has nine tables with 500 GB of data split among them\. 

 We used the default DMS task settings\. This means that when we ran one task, we loaded a minimum of eight tables in parallel\. When we ran seven tasks, we loaded a maximum of 56 tables in parallel\. 

The following graph demonstrates the growth of CPU consumption with the growth of tables in the migration scope\. 

![\[CPU consumption\]](http://docs.aws.amazon.com/dms/latest/userguide/images/sizing-replication-instance-cpu-consumption.png)

 Considering the metrics from the first test, we made the following considerations:
+  Use the r5\.8xlarge instance when you have seven parallel tasks\. The CPU utilization of 72 percent means that the CPU is neither peaking or underutilized\.
+  Use the r5\.4xlarge instance when you have four parallel tasks\. The CPU utilization of 62 percent means that the CPU is neither peaking or underutilized\. The r5\.8xlarge instance consumes 46 percent of the CPU resources, which means that here the instance is underutilized\. 
+  Use a smaller instance than r5\.4xlarge when you have from one to three parallel tasks\. 

 The second test of the full load section shows how an increase of the migration scope affects memory consumption\. We added a couple of LOBs to the tables, which we migrated in the previous test\. We then measured memory consumption during full load\. For different test runs, we incrementally increased the number of LOB columns in the tables, which resulted in an increase in the LOB size\. 

The following graph demonstrates the growth of memory consumption with the growth of the number of LOB columns in the migration scope\. 

![\[Memory consumption\]](http://docs.aws.amazon.com/dms/latest/userguide/images/sizing-replication-instance-memory-consumption.png)

 When the r5\.4xlarge replication instance ran out of memory, AWS DMS started writing to the disk, and reading from the disk caused latency\. 

 Considering the metrics from the second test, we make the following recommendations:
+  Use an r5\.8xlarge instance when your migration scope includes more than 15 LOB columns of LOBs sized 500 MB or greater, to keep memory consumption from peaking\. 
+  Use an r5\.4xlarge instance when your migration scope includes up to 15 LOB columns of LOBs sized 500 MB\. In this case, the migration task consumes 80 GB out of the total of 128 GB in the r5\.4xlarge replication instance\. 

### CDC performance tests<a name="CHAP_BestPractices.SizingReplicationInstance.PerformanceTests.CDC"></a>

 The first test of the CDC section shows how the growth of transactions per second affects the disk space and latency\. In this test, we ran inserts, updates, and deletes on nine tables with an overall TPS of 2,000\. In this test, our source and target databases and the replication instance are located in the same AWS Region\. The DMS task includes nine tables, we didn't split them into separate tasks\. 

 The following graph demonstrates an increase in latency because all the tables are in the same task with a high TPS\. Unlike the full load, the AWS DMS CDC process is single\-threaded and doesn't provide parallel processing for most engines\. Some engines, such as Amazon Redshift, support multithreaded processing during CDC\. Because we use PostgreSQL as a target in this test, we can't use parallel processing during CDC\. 

![\[CDC Latency\]](http://docs.aws.amazon.com/dms/latest/userguide/images/sizing-replication-instance-cdc-latency.png)

 The increase in latency can lead to *swapping*, which is writing data to disk\. Reading from disk is slower than reading from memory, and this can add to the latency even more\. We recommend increasing the disk space for a replication instance\. This helps with the available IOPS too\. 

 An upgrade of the replication instance from an r5\.4xlarge to an r5\.8xlarge demonstrated the same growth in latency\. The latency has nothing to do with resource contention on the replication instance; the upgrade didn't help here\. You can control latency by tuning the task or reducing the TPS\. For more information on how to tune change processing, see [Change processing tuning settings](CHAP_Tasks.CustomizingTasks.TaskSettings.ChangeProcessingTuning.md)\. 

 The second test of the CDC section shows the difference between transactional apply and batch apply for CDC\. As with the previous test, we ran inserts, updates, and deletes on nine tables with an overall TPS of 2,000\. First, we ran the task for 30 minutes using transactional apply for CDC\. Then we ran the same task for 30 minutes using batch apply\. 

 The following graph demonstrates an increase in latency when using transactional apply\. For the batch apply with the default settings `BatchApplyTimeoutMin : 1` and `BatchApplyTimeoutMax : 30`, the latency was around 45 seconds all the time\. 

![\[Transactional apply and batch apply\]](http://docs.aws.amazon.com/dms/latest/userguide/images/sizing-replication-instance-transactional-batch-apply.png)

 The latency increase for transactional apply is expected behavior because AWS DMS migrates transactions one by one in this mode\. 

 In the batch apply mode, AWS DMS uses batches to quickly process changes on the target\. Use this mode for high workloads on the source database and tasks with high target CDC latency\. The disadvantage of the batch apply mode is that it doesn't support referential integrity\. To ensure referential integrity, use the default transactional apply\. 

 The latency increase forces AWS DMS to hold the changes in memory or write them to disk when the memory fills up\. Reading from disk is slower and causes more increase in latency\. To avoid this, size the instance to ensure sufficient memory\. In this test case, use the r5\.8xlarge instance for transactional apply because it provides more memory, and choose the r5\.4xlarge instance for batch apply\. 

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

 The guidelines preceding don't cover all possible scenarios\. It's important to consider the specifics of your particular use case when you determine the size of your replication instance\. 

 The preceding tests show CPU and memory vary with different workloads\. Particularly, LOBs affect memory, and task count or parallelism affect the CPU\. After your migration is running, monitor the CPU, freeable memory, free storage, and IOPS of your replication instance\. Based on the data you gather, you can size your replication instance up or down as needed\. 