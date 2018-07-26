# What Is AWS Database Migration Service?<a name="Welcome"></a>

AWS Database Migration Service \(AWS DMS\) is a cloud service that makes it easy to migrate relational databases, data warehouses, NoSQL databases, and other types of data stores\. You can use AWS DMS to migrate your data into the AWS Cloud, between on\-premises instances, or between combinations of cloud and on\-premises setups\.

With AWS DMS, you can perform one\-time migrations, and you can replicate ongoing changes to keep sources and targets in sync\. If you want to change database engines, you can use the AWS Schema Conversion Tool \(AWS SCT\) to translate your database schema to the new platform\. You then use AWS DMS to migrate the data\. Because AWS DMS is a part of the AWS Cloud, you get the cost efficiency, speed to market, security, and flexibility that AWS services offer\.

For information about what AWS Regions support AWS DMS, see [Working with an AWS DMS Replication Instance](CHAP_ReplicationInstance.md)\. For information on the cost of database migration, see the [AWS Database Migration Service pricing page](https://aws.amazon.com/dms/pricing/)\. 

## Migration Tasks That AWS DMS Performs<a name="Welcome.Tasks"></a>

AWS DMS takes over many of the difficult or tedious tasks involved in a migration project:
+ In a traditional solution, you need to perform capacity analysis, procure hardware and software, install and administer systems, and test and debug the installation\. AWS DMS automatically manages the deployment, management, and monitoring of all hardware and software needed for your migration\. Your migration can be up and running within minutes of starting the AWS DMS configuration process\.
+ With AWS DMS, you can scale up \(or scale down\) your migration resources as needed to match your actual workload\. For example, if you determine that you need additional storage, you can easily increase your allocated storage and restart your migration, usually within minutes\. On the other hand, if you discover that you aren't using all of the resource capacity you configured, you can easily downsize to meet your actual workload\.
+ AWS DMS uses a pay\-as\-you\-go model\. You only pay for AWS DMS resources while you use them, as opposed to traditional licensing models with up\-front purchase costs and ongoing maintenance charges\.
+ AWS DMS automatically manages all of the infrastructure that supports your migration server, including hardware and software, software patching, and error reporting\.
+ AWS DMS provides automatic failover\. If your primary replication server fails for any reason, a backup replication server can take over with little or no interruption of service\.
+ AWS DMS can help you switch to a modern, perhaps more cost\-effective, database engine than the one you are running now\. For example, AWS DMS can help you take advantage of the managed database services provided by Amazon RDS or Amazon Aurora\. Or it can help you move to the managed data warehouse service provided by Amazon Redshift, NoSQL platforms like Amazon DynamoDB, or low\-cost storage platforms like Amazon S3\. Conversely, if you want to migrate away from old infrastructure but continue to use the same database engine, AWS DMS also supports that process\.
+ AWS DMS supports nearly all of today’s most popular DBMS engines as data sources, including Oracle, Microsoft SQL Server, MySQL, MariaDB, PostgreSQL, Db2 LUW, SAP, MongoDB, and Amazon Aurora\.
+ AWS DMS provides a broad coverage of available target engines including Oracle, Microsoft SQL Server, PostgreSQL, MySQL, Amazon Redshift, SAP ASE, S3, and Amazon DynamoDB\.
+ You can migrate from any of the supported data sources to any of the supported data targets\. AWS DMS supports fully heterogeneous data migrations between the supported engines\.
+ AWS DMS ensures that your data migration is secure\. Data at rest is encrypted with AWS Key Management Service \(AWS KMS\) encryption\. During migration, you can use Secure Socket Layers \(SSL\) to encrypt your in\-flight data as it travels from source to target\.

## How AWS DMS Works at the Basic Level<a name="Welcome.Works"></a>

At its most basic level, AWS DMS is a server in the AWS Cloud that runs replication software\. You create a source and target connection to tell AWS DMS where to extract from and load to\. Then you schedule a task that runs on this server to move your data\. AWS DMS creates the tables and associated primary keys if they don't exist on the target\. You can precreate the target tables manually, if you prefer\. Or you can use AWS SCT to create some or all of the target tables, indexes, views, triggers, and so on\.

The following diagram illustrates the AWS DMS process\.

![\[AWS DMS process\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-Welcome.png)

**To run the AWS DMS process, start to finish**

1. To start a migration project, identify your source and target data stores\. These data stores can reside on any of the data engines mentioned preceding\.

1. For both the source and target, configure endpoints within AWS DMS that specify the connection information to the databases\. The endpoints use the appropriate ODBC drivers to communicate with your source and target\.

1. Provision a *replication instance*, which is a server that AWS DMS automatically configures with replication software\.

1. Create a *replication task*, which specifies the actual data tables to migrate and data transformation rules to apply\. AWS DMS manages running the replication task and provides you status on the migration process\.

To learn more, see the following:
+ If you are new to AWS DMS but familiar with other AWS services, start with [How AWS Database Migration Service Works](CHAP_Introduction.md)\. This section dives into the key components of AWS DMS and the overall process of setting up and running a migration\.
+ If you want to switch database engines, the [AWS Schema Conversion Tool](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_SchemaConversionTool.Installing.html) can convert your existing database schema, including tables, indexes, and most application code, to the target platform\.
+ For information on related AWS services that you might need to design your migration strategy, see [AWS Cloud Products](https://aws.amazon.com/products/)\.
+ Amazon Web Services provides a number of database services\. For guidance on which service is best for your environment, see [Running Databases on AWS](http://aws.amazon.com/running_databases/)\.
+ For an overview of all AWS products, see [What is Cloud Computing?](http://aws.amazon.com/what-is-aws/)