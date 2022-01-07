# Choosing the optimum size for a replication instance<a name="CHAP_BestPractices.SizingReplicationInstance"></a>

Choosing the appropriate replication instance depends on several factors of your use case\. To help understand how replication instance resources are used, see the following discussion\. It covers the common scenario of a full load \+ CDC task\. 

During a full load task, AWS DMS loads tables individually\. By default, eight tables are loaded at a time\. AWS DMS captures ongoing changes to the source during a full load task so the changes can be applied later on the target endpoint\. The changes are cached in memory; if available memory is exhausted, changes are cached to disk\. When a full load task completes for a table, AWS DMS immediately applies the cached changes to the target table\.

After all outstanding cached changes for a table have been applied, the target endpoint is in a transactionally consistent state\. At this point, the target is in\-sync with the source endpoint with respect to the last cached changes\. AWS DMS then begins ongoing replication between the source and target\. To do so, AWS DMS takes change operations from the source transaction logs and applies them to the target in a transactionally consistent manner\. \(This process assumes batch optimized apply isn't selected\)\. AWS DMS streams ongoing changes through memory on the replication instance, if possible\. Otherwise, AWS DMS writes changes to disk on the replication instance until they can be applied on the target\.

You have some control over how the replication instance handles change processing, and how memory is used in that process\. For more information on how to tune change processing, see [Change processing tuning settings](CHAP_Tasks.CustomizingTasks.TaskSettings.ChangeProcessingTuning.md)\. 

From the preceding explanation, you can see that total available memory is a key consideration\. If the replication instance has sufficient memory that AWS DMS can stream cached and ongoing changes without writing them to disk, migration performance increases greatly\. Similarly, configuring the replication instance with enough disk space to accommodate change caching and log storage also increases performance\. The maximum IOPS possible depend on the selected disk size\.

Consider the following factors when choosing a replication instance class and available disk storage:
+ Table size – Large tables take longer to load and so transactions on those tables must be cached until the table is loaded\. After a table is loaded, these cached transactions are applied and are no longer held on disk\.
+ Data manipulation language \(DML\) activity – A busy database generates more transactions\. These transactions must be cached until the table is loaded\. Transactions to an individual table are applied as soon as possible after the table is loaded, until all tables are loaded\. 
+ Transaction size – Long\-running transactions can generate many changes\. For best performance, if AWS DMS applies changes in transactional mode, sufficient memory must be available to stream all changes in the transaction\. 
+ Total size of the migration – Large migrations take longer and they generate a proportionally large number of log files\.
+ Number of tasks – The more tasks, the more caching is likely to be required, and the more log files are generated\.
+ Large objects – Tables with LOBs take longer to load\.

Anecdotal evidence shows that log files consume the majority of space required by AWS DMS\. The default storage configurations are usually sufficient\. 

However, replication instances that run several tasks might require more disk space\. Additionally, if your database includes large and active tables, you might need to increase disk space for transactions that are cached to disk during a full load task\. For example, suppose that your load takes 24 hours and you produce 2 GB of transactions each hour\. In this case, you might want to ensure that you have 48 GB of space for cached transactions\. Also, the more storage space that you allocate to the replication instance, the higher the IOPS you get\. 

The guidelines preceding don't cover all possible scenarios\. It's critically important to consider the specifics of your particular use case when you determine the size of your replication instance\. After your migration is running, monitor the CPU, freeable memory, storage free, and IOPS of your replication instance\. Based on the data you gather, you can size your replication instance up or down as needed\.