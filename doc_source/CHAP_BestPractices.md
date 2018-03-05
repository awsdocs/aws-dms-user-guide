# Best Practices<a name="CHAP_BestPractices"></a>

To use AWS Database Migration Service \(AWS DMS\) most effectively, see this section's recommendations on the most efficient way to migrate your data\. 


+ [Improving the Performance of an AWS Database Migration Service Migration](#CHAP_BestPractices.Performance)
+ [Determining the Optimum Size for a Replication Instance](#CHAP_BestPractices.SizingReplicationInstance)
+ [Reducing Load on Your Source Database](#CHAP_BestPractices.ReducingLoad)
+ [Using the Task Log to Troubleshoot Migration Issues](#CHAP_BestPractices.TaskLog)
+ [Schema Conversion](#CHAP_BestPractices.SchemaConversion)
+ [Migrating Large Binary Objects \(LOBs\)](#CHAP_BestPractices.LOBS)
+ [Ongoing Replication](#CHAP_BestPractices.OnGoingReplication)
+ [Changing the User/Schema for an Oracle Target](#CHAP_BestPractices.ChangeOracleSchema)

## Improving the Performance of an AWS Database Migration Service Migration<a name="CHAP_BestPractices.Performance"></a>

 A number of factors affect the performance of your AWS DMS migration:

+ Resource availability on the source

+ The available network throughput

+ The resource capacity of the replication server

+ The ability of the target to ingest changes

+ The type and distribution of source data

+ The number of objects to be migrated

In our tests, we've migrated a terabyte of data in approximately 12 to 13 hours under ideal conditions\. These ideal conditions included using source databases running on Amazon Elastic Compute Cloud \(Amazon EC2\) and in Amazon Relational Database Service \(Amazon RDS\) with target databases in Amazon RDS\. Our source databases contained a representative amount of relatively evenly distributed data with a few large tables containing up to 250 GB of data\. 

Your migration's performance can be limited by one or more bottlenecks long the way\. The following list shows a few things you can do to increase performance: 

** Load multiple tables in parallel **  
 By default, AWS DMS loads eight tables at a time\. You might see some performance improvement by increasing this slightly when using a very large replication server, such as a dms\.c4\.xlarge or larger instance\. However, at some point increasing this parallelism reduces performance\. If your replication server is relatively small, such as a dms\.t2\.medium, you'll want to reduce this number\. 

** Remove bottlenecks on the target **  
 During the migration, try to remove any processes that might compete with each other for write resources on your target database\. As part of this process, disable unnecessary triggers, validation, and secondary indexes\. When migrating to an Amazon RDS database, it’s a good idea to disable backups and Multi\-AZ on the target until you’re ready to cut\-over\. Similarly, when migrating to non\-Amazon RDS systems, disabling any logging on the target until cut over is usually a good idea\. 

** Use multiple tasks **  
 Sometimes using multiple tasks for a single migration can improve performance\. If you have sets of tables that don’t participate in common transactions, you might be able to divide your migration into multiple tasks\. Transactional consistency is maintained within a task, so it’s important that tables in separate tasks don't participate in common transactions\. Additionally, each task independently reads the transaction stream, so be careful not to put too much stress on the source system\. 

**Improving LOB performance**  
For information about improving LOB migration, see [Migrating Large Binary Objects \(LOBs\)](#CHAP_BestPractices.LOBS)\.

** Optimizing change processing **  
 By default, AWS DMS processes changes in a *transactional mode,* which preserves transactional integrity\. If you can afford temporary lapses in transactional integrity, you can use the *batch optimized apply* option instead\. This option efficiently groups transactions and applies them in batches for efficiency purposes\. Note that using the *batch optimized apply* option almost certainly violates any referential integrity constraints, so you should disable these during the migration process and enable them again as part of the cut\-over process\. 

## Determining the Optimum Size for a Replication Instance<a name="CHAP_BestPractices.SizingReplicationInstance"></a>

Determining the correct size of your replication instance depends on several factors\. The following information can help you understand the migration process and how memory and storage are used\. 

Tables are loaded individually; by default, eight tables are loaded at a time\. While each table is loaded, the transactions for that table are cached in memory\. After the available memory is used, transactions are cached to disk\. When the table for those transactions is loaded, the transactions and any further transactions on that table are immediately applied to the table\.

When all tables have been loaded and all outstanding cached transactions for the individual tables have been applied by AWS DMS, the source and target tables will be in sync\. At this point, AWS DMS will apply transactions in a way that maintains transactional consistency\. \(As you can see, tables will be out of sync during the full load and while cached transactions for individual tables are being applied\.\) 

From the preceding explanation, you can see that relatively little disk space is required to hold cached transactions\. The amount of disk space used for a given migration will depend on the following:

+ Table size – Large tables take longer to load and so transactions on those tables must be cached until the table is loaded\. Once a table is loaded, these cached transactions are applied and are no longer held on disk\.

+ Data manipulation language \(DML\) activity – A busy database generates more transactions\. These transactions must be cached until the table is loaded\. Remember, though, that transactions to an individual table are applied as soon as possible after the table is loaded, until all tables are loaded\. At that point, AWS DMS applies all the transactions\.

+ Transaction size – Data generated by large transactions must be cached\. If a table accumulates 10 GB of transactions during the full load process, those transactions will need to be cached until the full load is complete\.

+ Total size of the migration – Large migrations take longer and the log files that are generated are large\.

+ Number of tasks – The more tasks, the more caching will likely be required, and the more log files will be generated\.

Anecdotal evidence shows that log files consume the majority of space required by AWS DMS\. The default storage configurations are usually sufficient\. Replication instances that run several tasks might require more disk space\. Additionally, if your database includes large and active tables, you may need to account for transactions that are cached to disk during the full load\. For example, if your load will take 24 hours and you produce 2GB of transactions per hour, you may want to ensure you have 48GB of space to accommodate cached transactions\.

## Reducing Load on Your Source Database<a name="CHAP_BestPractices.ReducingLoad"></a>

During a migration, AWS DMS performs a full table scan of the source table for each table processed in parallel\. Additionally, each task will periodically query the source for change information\. To perform change processing, you might be required to increase the amount of data written to your databases change log\. If you find you are overburdening your source database you can reduce the number of tasks and/or tables per task for your migration\. If you prefer not to add load to your source, you might consider performing the migration from a read copy of your source system\. However, using a read copy does increase the replication lag\.

## Using the Task Log to Troubleshoot Migration Issues<a name="CHAP_BestPractices.TaskLog"></a>

At times DMS may encounter issues \(warnings or errors\) which are only currently visible when viewing the task log\. In particular, data truncation issues or row rejections due to foreign key violations are currently only visible via the task log\. Therefore, it is important to review the task log when migrating a database\.

## Schema Conversion<a name="CHAP_BestPractices.SchemaConversion"></a>

AWS DMS doesn't perform schema or code conversion\. You can use tools such as Oracle SQL Developer, MySQL Workbench, or pgAdmin III to move your schema if your source and target are the same database engine\. If you want to convert an existing schema to a different database engine, you can use the AWS Schema Conversion Tool\. It can create a target schema and also can generate and create an entire schema: tables, indexes, views, and so on\. You can also use the tool to convert PL/SQL or TSQL to PgSQL and other formats\. For more information on the AWS Schema Conversion Tool, see [ AWS Schema Conversion Tool ](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_SchemaConversionTool.Installing.html)\.

## Migrating Large Binary Objects \(LOBs\)<a name="CHAP_BestPractices.LOBS"></a>

Migration of LOB data is done in two phases\. First, the row in the LOB column is created in the target table without the LOB data\. Next, the row in the target table is updated with the LOB data\. This means that during the migration, the LOB columns of a table must be NULLABLE on the target database\. If AWS DMS creates the target tables, it sets LOB columns to NULLABLE, even if they are NOT NULLABLE on the source table\. If you create the target tables using some other mechanism, such as Import/Export, the LOB columns must be NULLABLE\.

 Replication tasks, by default, are set to run in **Limited LOB mode** support mode\. **Limited LOB mode** typically provides the best performance but you need to ensure that the **Max LOB size** parameter setting is correct\. This parameter should be set to the largest LOB size for all your tables\. **Full LOB** mode support moves all of the LOBS in your tables, but the process can be slow\. 

If you have a table that contains a few large LOBs and mostly smaller LOBs, consider breaking up the table before migration and consolidating the table fragments as part of migration\. Note that for some database engines, such as PostgreSQL, AWS DMS treats JSON data types as LOBs\. Make sure that if you are using **Limited LOB mode** support mode that the **Max LOB size** parameter is set to a value that will not cause the JSON data to be truncated\. 

AWS Database Migration Service provides full support for using large object data types \(BLOBs, CLOBs, and NCLOBs\)\. The following source endpoints have full LOB support:

+ Oracle

+ Microsoft SQL Server

+ ODBC

The following target endpoints have full LOB support:

+ Oracle

+ Microsoft SQL Server

The following target endpoints have limited LOB support\. You cannot use an unlimited LOB size for these target endpoints\.

+ Amazon Redshift

For endpoints that have full LOB support, you can also set a size limit for LOB data types\. 

## Ongoing Replication<a name="CHAP_BestPractices.OnGoingReplication"></a>

AWS DMS provides comprehensive ongoing replication of data, although it replicates only a limited amount of data definition language \(DDL\)\. AWS DMS doesn't propagate items such as indexes, users, privileges, stored procedures, and other database changes not directly related to table data\. 

If you want to use ongoing replication, you must enable the **Multi\-AZ** option on your replication instance\. The **Multi\-AZ** option provides high availability and failover support for the replication instance\.

## Changing the User/Schema for an Oracle Target<a name="CHAP_BestPractices.ChangeOracleSchema"></a>

When using Oracle as a target, we assume the data should be migrated into the schema/user which is used for the target connection\. If you want to migrate data to a different schema, you'll need to use a schema transformation to do so\. For example, if my target endpoint connects to the user RDSMASTER and you wish to migrate from the user PERFDATA to PERFDATA, you'll need to create a transformation as follows:

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