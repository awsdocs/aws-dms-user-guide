# Components of AWS Database Migration Service<a name="CHAP_Introduction.Components"></a>

This section describes the internal components of AWS DMS and how they function together to accomplish your data migration\. Understanding the underlying components of AWS DMS can help you migrate data more efficiently and provide better insight when troubleshooting or investigating issues\.

An AWS DMS migration consists of three components: a replication instance, source and target endpoints, and a replication task\. You create an AWS DMS migration by creating the necessary replication instance, endpoints, and tasks in an AWS Region\.

** Replication instance **  
At a high level, an AWS DMS replication instance is simply a managed Amazon Elastic Compute Cloud \(Amazon EC2\) instance that hosts one or more replication tasks\.   
The figure following shows an example replication instance running several associated replication tasks\.  

![\[Get started with AWS DMS\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-intro-rep-instance1.png)
A single replication instance can host one or more replication tasks, depending on the characteristics of your migration and the capacity of the replication server\. AWS DMS provides a variety of replication instances so you can choose the optimal configuration for you use case\. For more information about the various classes of replication instances, see [Selecting the Right AWS DMS Replication Instance for Your Migration](CHAP_ReplicationInstance.md#CHAP_ReplicationInstance.InDepth)\.   
AWS DMS creates the replication instance on an Amazon Elastic Compute Cloud \(Amazon EC2\) instance\. Some of the smaller instance classes are sufficient for testing the service or for small migrations\. If your migration involves a large number of tables, or if you intend to run multiple concurrent replication tasks, you should consider using one of the larger instances\. We recommend this approach because AWS DMS can consume a significant amount of memory and CPU\.   
Depending on the Amazon EC2 instance class you select, your replication instance comes with either 50 GB or 100 GB of data storage\. This amount is usually sufficient for most customers\. However, if your migration involves large transactions or a high\-volume of data changes then you may want to increase the base storage allocation\. Change data capture \(CDC\) may cause data to be written to disk, depending on how fast the target can write the changes\.   
AWS DMS can provide high availability and failover support using a Multi\-AZ deployment\. In a Multi\-AZ deployment, AWS DMS automatically provisions and maintains a standby replica of the replication instance in a different Availability Zone\. The primary replication instance is synchronously replicated to the standby replica\. If the primary replication instance fails or becomes unresponsive, the standby resumes any running tasks with minimal interruption\. Because the primary is constantly replicating its state to the standby, Multi\-AZ deployment does incur some performance overhead\.  
For more detailed information about the AWS DMS replication instance, see [Working with an AWS DMS Replication Instance](CHAP_ReplicationInstance.md)\.

** Endpoints **  
AWS DMS uses an endpoint to access your source or target data store\. The specific connection information is different, depending on your data store, but in general you supply the following information when you create an endpoint\.  
+ Endpoint type — Source or target\.
+ Engine type — Type of database engine, such as Oracle, Postgres, or Amazon S3\.
+ Server name — Server name or IP address, reachable by AWS DMS
+ Port — Port number used for database server connections\.
+ Encryption — SSL mode, if used to encrypt the connection\.
+ Credentials — User name and password for an account with the required access rights\.
When you create an endpoint using the AWS DMS console, the console requires that you test the endpoint connection\. The test must be successful before using the endpoint in a DMS task\. Like the connection information, the specific test criteria are different for different engine types\. In general, AWS DMS verifies that the database exists at the given server name and port, and that the supplied credentials can be used to connect to the database with the necessary privileges to perform a migration\. If the connection test is successful, AWS DMS downloads and stores schema information, including table definitions and primary/unique key definitions, that can be used later during task configuration\.  
A single endpoint can be used by more than one replication task\. For example, you may have two logically distinct applications hosted on the same source database that you want to migrate separately\. You would create two replication tasks, one for each set of application tables, but you can use the same AWS DMS endpoint in both tasks\.   
You can customize the behavior of an endpoint by using *extra connection attributes*\. These attributes can control various behavior such as logging detail, file size, and other parameters\. Each data store engine type has different extra connection attributes available\. You can find the specific extra connection attributes for each data store in the source or target section for that data store\. For a list of supported source and target data stores, see [Sources for AWS Database Migration Service](CHAP_Introduction.Sources.md) and [Targets for AWS Database Migration Service](CHAP_Introduction.Targets.md)\.  
For more detailed information about AWS DMS endpoints, see [Working with AWS DMS Endpoints](CHAP_Endpoints.md)\.

**Replication Tasks**  
You use an AWS DMS replication task to move a set of data from the source endpoint to the target endpoint\. Creating a replication task is the last step you need to take before you start a migration\.   
When you create a replication task, you specify the following task settings:  
+ Replication instance – the instance that will host and run the task
+ Source endpoint
+ Target endpoint
+ Migration type options – a migration type can be one of the following:
  +  Full load \(Migrate existing data\) – If you can afford an outage long enough to copy your existing data, this option is a good one to choose\. This option simply migrates the data from your source database to your target database, creating tables when necessary\. 
  +  Full load \+ CDC \(Migrate existing data and replicate ongoing changes\) – This option performs a full data load while capturing changes on the source\. Once the full load is complete, captured changes are applied to the target\. Eventually the application of changes reaches a steady state\. At this point you can shut down your applications, let the remaining changes flow through to the target, and then restart your applications pointing at the target\. 
  +  CDC only \(Replicate data changes only\) – In some situations it might be more efficient to copy existing data using a method other than AWS DMS\. For example, in a homogeneous migration, using native export/import tools might be more efficient at loading the bulk data\. In this situation, you can use AWS DMS to replicate changes starting when you start your bulk load to bring and keep your source and target databases in sync\. 

  For a full explanation of the migration type options, see [Creating a Task](CHAP_Tasks.Creating.md)\.
+ Target table preparation mode options\. For a full explanation of target table modes, see [Creating a Task](CHAP_Tasks.Creating.md)\.
  + Do nothing – AWS DMS assumes that the target tables are pre\-created on the target\.
  + Drop tables on target – AWS DMS drops and recreates the target tables\.
  + Truncate – If you created tables on the target, AWS DMS truncates them before the migration starts\. If no tables exist and you select this option, AWS DMS creates any missing tables\.
+ LOB mode options\. For a full explanation of LOB modes, see [Setting LOB Support for Source Databases in the AWS DMS task](CHAP_Tasks.LOBSupport.md)\.
  + Don't include LOB columns – LOB columns are excluded from the migration\.
  + Full LOB mode – Migrate complete LOBs regardless of size\. AWS DMS migrates LOBs piecewise in chunks controlled by the **Max LOB Size** parameter\. This mode is slower than using Limited LOB mode\.
  + Limited LOB mode – Truncate LOBs to the value specified by the **Max LOB Size** parameter\. This mode is faster than using Full LOB mode\.
+ Table mappings – indicates the tables to migrate
+ Data transformations – changing schema, table, and column names
+ data validation
+ CloudWatch logging
 You use the task to migrate data from the source endpoint to the target endpoint, and the task processing is done on the replication instance\. You specify what tables and schemas to migrate and any special processing, such as logging requirements, control table data, and error handling\.  
Conceptually, an AWS DMS replication task performs two distinct functions as shown in the diagram following:  

![\[Get started with AWS DMS\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-intro-rep-task1.png)
The full load process is straight\-forward to understand\. Data is extracted from the source in a bulk extract manner and loaded directly into the target\. You can specify the number of tables to extract and load in parallel on the AWS DMS console under **Advanced Settings**\.  
For more information about AWS DMS tasks, see [Working with AWS DMS Tasks](CHAP_Tasks.md)\.

**Ongoing replication, or change data capture \(CDC\)**  
You can also use an AWS DMS task to capture ongoing changes to the source data store while you are migrating your data to a target\. The change capture process that AWS DMS uses when replicating ongoing changes from a source endpoint collects changes to the database logs by using the database engine's native API\.   
In the CDC process, the replication task is designed to stream changes from the source to the target, using in\-memory buffers to hold data in\-transit\. If the in\-memory buffers become exhausted for any reason, the replication task will spill pending changes to the Change Cache on disk\. This could occur, for example, if AWS DMS is capturing changes from the source faster than they can be applied on the target\. In this case, you will see the task’s *target latency* exceed the task’s *source latency*\.   
You can check this by navigating to your task on the AWS DMS console, and opening the Task Monitoring tab\. The CDCLatencyTarget and CDCLatencySource graphs are shown at the bottom of the page\. If you have a task that is showing target latency then there is likely some tuning on the target endpoint needed to increase the application rate\.  
The replication task also uses storage for task logs as discussed above\. The disk space that comes pre\-configured with your replication instance is usually sufficient for logging and spilled changes\. If you need additional disk space, for example, when using detailed debugging to investigate a migration issue, you can modify the replication instance to allocate more space\.

**Schema and code migration**  
AWS DMS doesn't perform schema or code conversion\. You can use tools such as Oracle SQL Developer, MySQL Workbench, or pgAdmin III to move your schema if your source and target are the same database engine\. If you want to convert an existing schema to a different database engine, you can use AWS SCT\. It can create a target schema and also can generate and create an entire schema: tables, indexes, views, and so on\. You can also use AWS SCT to convert PL/SQL or TSQL to PgSQL and other formats\. For more information on AWS SCT, see [ AWS Schema Conversion Tool](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_SchemaConversionTool.Installing.html)\.  
Whenever possible, AWS DMS attempts to create the target schema for you\. Sometimes, AWS DMS can't create the schema—for example, AWS DMS doesn't create a target Oracle schema for security reasons\. For MySQL database targets, you can use extra connection attributes to have AWS DMS migrate all objects to the specified database and schema or create each database and schema for you as it finds the schema on the source\. 