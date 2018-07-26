# Best Practices for AWS Database Migration Service<a name="CHAP_BestPractices"></a>

To use AWS Database Migration Service \(AWS DMS\) most effectively, see this section's recommendations on the most efficient way to migrate your data\. 

**Topics**
+ [Improving the Performance of an AWS DMS Migration](#CHAP_BestPractices.Performance)
+ [Choosing the Optimum Size for a Replication Instance](#CHAP_BestPractices.SizingReplicationInstance)
+ [Reducing the Load on Your Source Database](#CHAP_BestPractices.ReducingLoad)
+ [Using the Task Log to Troubleshoot Migration Issues](#CHAP_BestPractices.TaskLog)
+ [Converting Schema](#CHAP_BestPractices.SchemaConversion)
+ [Migrating Large Binary Objects \(LOBs\)](#CHAP_BestPractices.LOBS)
+ [Ongoing Replication](#CHAP_BestPractices.OnGoingReplication)
+ [Changing the User and Schema for an Oracle Target](#CHAP_BestPractices.ChangeOracleSchema)
+ [Improving Performance When Migrating Large Tables](#CHAP_BestPractices.LargeTables)

## Improving the Performance of an AWS DMS Migration<a name="CHAP_BestPractices.Performance"></a>

 A number of factors affect the performance of your AWS DMS migration:
+ Resource availability on the source
+ The available network throughput
+ The resource capacity of the replication server
+ The ability of the target to ingest changes
+ The type and distribution of source data
+ The number of objects to be migrated

In our tests, we've migrated a terabyte of data in approximately 12 to 13 hours using a single AWS DMS task and under ideal conditions\. These ideal conditions included using source databases running on Amazon EC2 and in Amazon RDS with target databases in Amazon RDS, all in the same Availability Zone\. Our source databases contained a representative amount of relatively evenly distributed data with a few large tables containing up to 250 GB of data\. The source data didn't contain complex data types such as LOB data\.

You can improve performance by using some or all of the best practices mentioned following\. Whether you can use one of these practices or not depends in large part on your specific use case\. We mention limitations as appropriate\. 

** Loading Multiple Tables in Parallel **  
 By default, AWS DMS loads eight tables at a time\. You might see some performance improvement by increasing this slightly when using a very large replication server, such as a dms\.c4\.xlarge or larger instance\. However, at some point, increasing this parallelism reduces performance\. If your replication server is relatively small, such as a dms\.t2\.medium, we recommend that you reduce the number of tables loaded in parallel\.   
To change this number in the AWS Management Console, open the console, choose **Tasks**, choose to create or modify a task, and then choose **Advanced Settings**\. Under **Tuning Settings**, change the **Maximum number of tables to load in parallel** option\.   
To change this number using the AWS CLI, change the `MaxFullLoadSubTasks` parameter under `TaskSettings`\.

**Working with Indexes, Triggers and Referential Integrity Constraints**  
 Indexes, triggers, and referential integrity constraints can affect your migration performance and cause your migration to fail\. How these affect migration depends on whether your replication task is a full load task or an ongoing replication \(CDC\) task\.  
For a full load task, we recommend that you drop primary key indexes, secondary indexes, referential integrity constraints, and data manipulation language \(DML\) triggers\. Alternatively, you can delay their creation until after the full load tasks are complete\. You don't need indexes during a full load task and indexes will incur maintenance overhead if they are present\. Because the full load task loads groups of tables at a time, referential integrity constraints are violated\. Similarly, insert, update, and delete triggers can cause errors, for example, if a row insert is triggered for a previously bulk loaded table\. Other types of triggers also affect performance due to added processing\.  
You can build primary key and secondary indexes before a full load task if your data volumes are relatively small and the additional migration time doesn't concern you\. Referential integrity constraints and triggers should always be disabled\.  
For a full load \+ CDC task, we recommend that you add secondary indexes before the CDC phase\. Because AWS DMS uses logical replication, secondary indexes that support DML operations should be in\-place to prevent full table scans\. You can pause the replication task before the CDC phase to build indexes, create triggers, and create referential integrity constraints before you restart the task\.

** Disable Backups and Transaction Logging **  
 When migrating to an Amazon RDS database, it’s a good idea to disable backups and Multi\-AZ on the target until you’re ready to cut over\. Similarly, when migrating to non\-Amazon RDS systems, disabling any logging on the target until after cut over is usually a good idea\. 

** Use Multiple Tasks **  
 Sometimes using multiple tasks for a single migration can improve performance\. If you have sets of tables that don’t participate in common transactions, you might be able to divide your migration into multiple tasks\. Transactional consistency is maintained within a task, so it’s important that tables in separate tasks don't participate in common transactions\. Additionally, each task independently reads the transaction stream, so be careful not to put too much stress on the source database\.  
You can use multiple tasks to create separate streams of replication to parallelize the reads on the source, the processes on the replication instance, and the writes to the target database\.

** Optimizing Change Processing **  
 By default, AWS DMS processes changes in a transactional mode, which preserves transactional integrity\. If you can afford temporary lapses in transactional integrity, you can use the batch optimized apply option instead\. This option efficiently groups transactions and applies them in batches for efficiency purposes\. Using the batch optimized apply option almost always violates referential integrity constraints, so you should disable these during the migration process and enable them again as part of the cut over process\. 

## Choosing the Optimum Size for a Replication Instance<a name="CHAP_BestPractices.SizingReplicationInstance"></a>

Choosing the appropriate replication instance depends on several factors of your use case\. To help understand how replication instance resources are used, see the following discussion\. It covers the common scenario of a full load \+ CDC task\. 

During a full load task, AWS DMS loads tables individually\. By default, eight tables are loaded at a time\. AWS DMS captures ongoing changes to the source during a full load task so the changes can be applied later on the target endpoint\. The changes are cached in memory; if available memory is exhausted, changes are cached to disk\. When a full load task completes for a table, AWS DMS immediately applies the cached changes to the target table\.

After all outstanding cached changes for a table have been applied, the target endpoint is in a transactionally consistent state\. At this point, the target is in\-sync with the source endpoint with respect to the last cached changes\. AWS DMS then begins ongoing replication between the source and target\. To do so, AWS DMS takes change operations from the source transaction logs and applies them to the target in a transactionally consistent manner \(assuming batch optimized apply is not selected\)\. AWS DMS streams ongoing changes through memory on the replication instance, if possible\. Otherwise, AWS DMS writes changes to disk on the replication instance until they can be applied on the target\.

You have some control over how the replication instance handles change processing, and how memory is used in that process\. For more information on how to tune change processing, see [Change Processing Tuning Settings](CHAP_Tasks.CustomizingTasks.TaskSettings.ChangeProcessingTuning.md)\. 

From the preceding explanation, you can see that total available memory is a key consideration\. If the replication instance has sufficient memory that AWS DMS can stream cached and ongoing changes without writing them to disk, migration performance increases greatly\. Similarly, configuring the replication instance with enough disk space to accommodate change caching and log storage also increases performance\. The maximum IOPs possible depend on the selected disk size\.

Consider the following factors when choosing a replication instance class and available disk storage:
+ Table size – Large tables take longer to load and so transactions on those tables must be cached until the table is loaded\. After a table is loaded, these cached transactions are applied and are no longer held on disk\.
+ Data manipulation language \(DML\) activity – A busy database generates more transactions\. These transactions must be cached until the table is loaded\. Transactions to an individual table are applied as soon as possible after the table is loaded, until all tables are loaded\. 
+ Transaction size – Long\-running transactions can generate many changes\. For best performance, if AWS DMS applies changes in transactional mode, sufficient memory must be available to stream all changes in the transaction\. 
+ Total size of the migration – Large migrations take longer and they generate a proportionally large number of log files\.
+ Number of tasks – The more tasks, the more caching is likely to be required, and the more log files are generated\.
+ Large objects – Tables with LOBs take longer to load\.

Anecdotal evidence shows that log files consume the majority of space required by AWS DMS\. The default storage configurations are usually sufficient\. 

However, replication instances that run several tasks might require more disk space\. Additionally, if your database includes large and active tables, you might need to increase disk space for transactions that are cached to disk during a full load task\. For example, if your load takes 24 hours and you produce 2GB of transactions each hour, you might want to ensure that you have 48GB of space for cached transactions\. Also, the more storage space you allocate to the replication instance, the higher the IOPS you get\.

The guidelines preceding don’t cover all possible scenarios\. It’s critically important to consider the specifics of your particular use case when you determine the size of your replication instance\. After your migration is running, monitor the CPU, freeable memory, storage free, and IOPS of your replication instance\. Based on the data you gather, you can size your replication instance up or down as needed\.

## Reducing the Load on Your Source Database<a name="CHAP_BestPractices.ReducingLoad"></a>

AWS DMS uses some resources on your source database\. During a full load task, AWS DMS performs a full table scan of the source table for each table processed in parallel\. Additionally, each task you create as part of a migration queries the source for changes as part of the CDC process\. For AWS DMS to perform CDC for some sources, such as Oracle, you might need to increase the amount of data written to your database's change log\. 

If you find you are overburdening your source database, you can reduce the number of tasks or tables for each task for your migration\. Each task gets source changes independently, so consolidating tasks can decrease the change capture workload\.

## Using the Task Log to Troubleshoot Migration Issues<a name="CHAP_BestPractices.TaskLog"></a>

In some cases, AWS DMS can encounter issues for which warnings or error messages appear only in the task log\. In particular, data truncation issues or row rejections due to foreign key violations are only written in the task log\. Therefore, be sure to review the task log when migrating a database\. To enable viewing of the task log, configure Amazon CloudWatch as part of task creation\.

## Converting Schema<a name="CHAP_BestPractices.SchemaConversion"></a>

AWS DMS doesn't perform schema or code conversion\. If you want to convert an existing schema to a different database engine, you can use the AWS Schema Conversion Tool \(AWS SCT\)\. AWS SCT converts your source objects, table, indexes, views, triggers, and other system objects into the target data definition language \(DDL\) format\. You can also use AWS SCT to convert most of your application code, like PL/SQL or TSQL, to the equivalent target language\. 

You can get AWS SCT as a free download from AWS\. For more information on AWS SCT, see the [ AWS Schema Conversion Tool User Guide](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_SchemaConversionTool.Installing.html)\.

If your source and target endpoints are on the same database engine, you can use tools such as Oracle SQL Developer, MySQL Workbench, or PgAdmin4 to move your schema\.

## Migrating Large Binary Objects \(LOBs\)<a name="CHAP_BestPractices.LOBS"></a>

In general, AWS DMS migrates LOB data in two phases\. 

1. AWS DMS creates a new row in the target table and populates the row with all data except the associated LOB value\. 

1. AWS DMS updates the row in the target table with the LOB data\.

This migration process for LOBs requires that, during the migration, all LOB columns on the target table must be nullable\. This is so even if the LOB columns aren't nullable on the source table\. If AWS DMS creates the target tables, it sets LOB columns to nullable by default\. If you create the target tables using some other mechanism, such as import or export, you must ensure that the LOB columns are nullable before you start the migration task\.

This requirement has one exception\. Suppose that you perform a homogeneous migration from an Oracle source to an Oracle target, and you choose **Limited Lob mode**\. In this case, the entire row is populated at once, including any LOB values\. For such a case, AWS DMS can create the target table LOB columns with not\-nullable constraints, if needed\.

### Using Limited LOB Mode<a name="CHAP_BestPractices.LOBS.LimitedLOBMode"></a>

 AWS DMS uses two methods that balance performance and convenience when your migration contains LOB values\.
+ **Limited LOB mode** migrates all LOB values up to a user\-specified size limit \(default is 32 KB\)\. LOB values larger than the size limit must be manually migrated\. **Limited LOB mode**, the default for all migration tasks, typically provides the best performance\. However you need to ensure that the **Max LOB size** parameter setting is correct\. This parameter should be set to the largest LOB size for all your tables\.
+ **Full LOB mode** migrates all LOB data in your tables, regardless of size\. **Full LOB mode** provides the convenience of moving all LOB data in your tables, but the process can have a significant impact on performance\.

For some database engines, such as PostgreSQL, AWS DMS treats JSON data types like LOBs\. Make sure that if you have chosen **Limited LOB mode** the **Max LOB size** option is set to a value that doesn't cause the JSON data to be truncated\. 

AWS DMS provides full support for using large object data types \(BLOBs, CLOBs, and NCLOBs\)\. The following source endpoints have full LOB support:
+ Oracle
+ Microsoft SQL Server
+ ODBC

The following target endpoints have full LOB support:
+ Oracle
+ Microsoft SQL Server

The following target endpoint has limited LOB support\. You can't use an unlimited LOB size for this target endpoint\.
+ Amazon Redshift

For endpoints that have full LOB support, you can also set a size limit for LOB data types\. 

## Ongoing Replication<a name="CHAP_BestPractices.OnGoingReplication"></a>

AWS DMS provides ongoing replication of data, keeping the source and target databases in sync\. It replicates only a limited amount of data definition language \(DDL\)\. AWS DMS doesn't propagate items such as indexes, users, privileges, stored procedures, and other database changes not directly related to table data\. 

If you plan to use ongoing replication, you should enable the **Multi\-AZ** option when you create your replication instance\. By choosing the **Multi\-AZ** option you get high availability and failover support for the replication instance\. However, this option can have an impact on performance\.

## Changing the User and Schema for an Oracle Target<a name="CHAP_BestPractices.ChangeOracleSchema"></a>

When using Oracle as a target, AWS DMS assumes that the data should be migrated into the schema and user that is used to connect to the target\. If you want to migrate data to a different schema, use a schema transformation to do so\. Schema in Oracle is connected to the username that is used in the endpoint connection\. In order to read from an X schema and write into X schema on the target, we need to rename the X schema \(being read from source\) and instruct AWS DMS to write data into X schema on target\.

For example, if you want to migrate from the user source schema PERFDATA to the target data PERFDATA, you'll need to create a transformation as follows:

```
{
   "rule-type": "transformation",
   "rule-id": "2",
   "rule-name": "2",
   "rule-action": "rename",
   "rule-target": "schema",
   "object-locator": {
   "schema-name": "PERFDATA"
},
"value": "PERFDATA"
}
```

For more information about transformations, see [ Selection and Transformation Table Mapping using JSON](CHAP_Tasks.CustomizingTasks.TableMapping.md#CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation)\.

## Improving Performance When Migrating Large Tables<a name="CHAP_BestPractices.LargeTables"></a>

If you want to improve the performance when migrating a large table, you can break the migration into more than one task\. To break the migration into multiple tasks using row filtering, use a key or a partition key\. For example, if you have an integer primary key ID from 1 to 8,000,000, you can create eight tasks using row filtering to migrate 1 million records each\.

To apply row filtering in the AWS Management Console, open the console, choose **Tasks**, and create a new task\. In the **Table mappings** section, add a value for **Selection Rule**\. You can then add a column filter with either a less than or equal to, greater than or equal to, equal to, or range condition \(between two values\)\. For more information about column filtering, see [ Selection and Transformation Table Mapping Using the AWS Management Console ](CHAP_Tasks.CustomizingTasks.TableMapping.md#CHAP_Tasks.CustomizingTasks.TableMapping.Console)\.

Alternatively, if you have a large partitioned table that is partitioned by date, you can migrate data based on date\. For example, suppose that you have a table partitioned by month, and only the current month’s data is updated\. In this case, you can create a full load task for each static monthly partition and create a full load \+ CDC task for the currently updated partition\.