# High\-level view of AWS DMS<a name="CHAP_Introduction.HighLevelView"></a>

To perform a database migration, AWS DMS connects to the source data store, reads the source data, and formats the data for consumption by the target data store\. It then loads the data into the target data store\. Most of this processing happens in memory, though large transactions might require some buffering to disk\. Cached transactions and log files are also written to disk\. 

 At a high level, when using AWS DMS you do the following:
+ Discover databases in your network environment that are good candidates for migration\.
+ Automatically convert your source database schemas and most of the database code objects to a format compatible with the target database\.
+ Create a replication server\.
+ Create source and target endpoints that have connection information about your data stores\.
+ Create one or more migration tasks to migrate data between the source and target data stores\.

A task can consist of three major phases:
+ Migration of existing data \(Full load\)
+ The application of cached changes
+ Ongoing replication \(Change Data Capture\)

During a full load migration, where existing data from the source is moved to the target, AWS DMS loads data from tables on the source data store to tables on the target data store\. While the full load is in progress, any changes made to the tables being loaded are cached on the replication server; these are the cached changes\. It's important to note that AWS DMS doesn't capture changes for a given table until the full load for that table is started\. In other words, the point when change capture starts is different for each individual table\. 

When the full load for a given table is complete, AWS DMS immediately begins to apply the cached changes for that table\. Once the table is loaded and the cached changes applied, AWS DMS begins to collect changes as transactions for the ongoing replication phase\. If a transaction has tables not yet fully loaded, the changes are stored locally on the replication instance\. After AWS DMS applies all cached changes, tables are transactionally consistent\. At this point, AWS DMS moves to the ongoing replication phase, applying changes as transactions\.

At the start of the ongoing replication phase, a backlog of transactions generally causes some lag between the source and target databases\. The migration eventually reaches a steady state after working through this backlog of transactions\. At this point, you can shut down your applications, allow any remaining transactions to be applied to the target, and bring your applications up, now pointing at the target database\. 

AWS DMS creates the target schema objects necessary to perform a data migration\. You can use AWS DMS to take a minimalist approach and create only those objects required to efficiently migrate the data\. Using this approach, AWS DMS creates tables, primary keys, and in some cases unique indexes, but it won't create any other objects that are not required to efficiently migrate the data from the source\.

Alternatively, you can use DMS Schema Conversion within AWS DMS to automatically convert your source database schemas and most of the database code objects to a format compatible with the target database\. This conversion includes tables, views, stored procedures, functions, data types, synonyms, and so on\. Any objects that DMS Schema Conversion can't convert automatically are clearly marked\. To complete the migration, you can convert these objects manually\.

