# How AWS Database Migration Service Works<a name="CHAP_Introduction"></a>

AWS Database Migration Service \(AWS DMS\) is a web service that you can use to migrate data from a source data store to a target data store\. These two data stores are called endpoints\. You can migrate between source and target endpoints that use the same database engine, such as from an Oracle database to an Oracle database\. You can also migrate between source and target endpoints that use different database engines, such as from an Oracle database to a PostgresSQL database\. The only requirement to use AWS DMS is that one of your endpoints must be on an AWS service\. You can't use AWS DMS to migrate from an on\-premises database to another on\-premises database\. 

For information on the cost of database migration, see the [AWS Database Migration Service pricing page](https://aws.amazon.com/dms/pricing/)\.

Use the following topics to better understand AWS DMS\.

**Topics**
+ [High\-Level View of AWS DMS](#CHAP_Introduction.HighLevelView)
+ [Components of AWS Database Migration Service](CHAP_Introduction.Components.md)
+ [Sources for AWS Database Migration Service](CHAP_Introduction.Sources.md)
+ [Targets for AWS Database Migration Service](CHAP_Introduction.Targets.md)
+ [Using AWS DMS with Other AWS Services](CHAP_Introduction.AWS.md)

## High\-Level View of AWS DMS<a name="CHAP_Introduction.HighLevelView"></a>

To perform a database migration, AWS DMS connects to the source data store, reads the source data, and formats the data for consumption by the target data store\. It then loads the data into the target data store\. Most of this processing happens in memory, though large transactions might require some buffering to disk\. Cached transactions and log files are also written to disk\. 

 At a high level, when using AWS DMS you do the following:
+ Create a replication server\.
+ Create source and target endpoints that have connection information about your data stores\.
+ Create one or more migration tasks to migrate data between the source and target data stores\.

A task can consist of three major phases:
+ The full load of existing data
+ The application of cached changes
+ Ongoing replication

During a full load migration, where existing data from the source is moved to the target, AWS DMS loads data from tables on the source data store to tables on the target data store\. While the full load is in progress, any changes made to the tables being loaded are cached on the replication server; these are the cached changes\. It’s important to note that AWS DMS doesn't capture changes for a given table until the full load for that table is started\. In other words, the point when change capture starts is different for each individual table\. 

When the full load for a given table is complete, AWS DMS immediately begins to apply the cached changes for that table\. When all tables have been loaded, AWS DMS begins to collect changes as transactions for the ongoing replication phase\. After AWS DMS applies all cached changes, tables are transactionally consistent\. At this point, AWS DMS moves to the ongoing replication phase, applying changes as transactions\. 

At the start of the ongoing replication phase, a backlog of transactions generally causes some lag between the source and target databases\. The migration eventually reaches a steady state after working through this backlog of transactions\. At this point, you can shut down your applications, allow any remaining transactions to be applied to the target, and bring your applications up, now pointing at the target database\. 

AWS DMS creates the target schema objects necessary to perform the migration\. However, AWS DMS takes a minimalist approach and creates only those objects required to efficiently migrate the data\. In other words, AWS DMS creates tables, primary keys, and in some cases unique indexes, but doesn't create any other objects that are not required to efficiently migrate the data from the source\. For example, it doesn't create secondary indexes, nonprimary key constraints, or data defaults\. 

In most cases, when performing a migration, you also migrate most or all of the source schema\. If you are performing a homogeneous migration \(between two databases of the same engine type\), you migrate the schema by using your engine’s native tools to export and import the schema itself, without any data\. 

If your migration is heterogeneous \(between two databases that use different engine types\), you can use the AWS Schema Conversion Tool \(AWS SCT\) to generate a complete target schema for you\. If you use the tool, any dependencies between tables such as foreign key constraints need to be disabled during the migration's "full load" and "cached change apply" phases\. If performance is an issue, removing or disabling secondary indexes during the migration process helps\. For more information on the AWS SCT, see [AWS Schema Conversion Tool](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_SchemaConversionTool.Installing.html) in the AWS SCT documentation\.