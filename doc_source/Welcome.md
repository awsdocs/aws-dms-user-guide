# What is AWS Database Migration Service?<a name="Welcome"></a>

AWS Database Migration Service \(AWS DMS\) is a cloud service that makes it possible to migrate relational databases, data warehouses, NoSQL databases, and other types of data stores\. You can use AWS DMS to migrate your data into the AWS Cloud or between combinations of cloud and on\-premises setups\.

With AWS DMS, you can discover your source data stores, convert your source schemas, and migrate your data\.
+ To discover your source data infrastructure, you can use DMS Fleet Advisor\. This service collects data from your on\-premises database and analytic servers, and builds an inventory of servers, databases, and schemas that you can migrate to the AWS Cloud\.
+ To migrate to a different database engine, you can use DMS Schema Conversion\. This service automatically assesses and converts your source schemas to a new target engine\. Alternatively, you can download the AWS Schema Conversion Tool \(AWS SCT\) to your local PC to convert your source schemas\.
+ After you convert your source schemas and apply the converted code to your target database, you can use AWS DMS to migrate your data\. You can perform one\-time migrations or replicate ongoing changes to keep sources and targets in sync\. Because AWS DMS is a part of the AWS Cloud, you get the cost efficiency, speed to market, security, and flexibility that AWS services offer\.

At a basic level, AWS DMS is a server in the AWS Cloud that runs replication software\. You create a source and target connection to tell AWS DMS where to extract from and load to\. Then you schedule a task that runs on this server to move your data\. AWS DMS creates the tables and associated primary keys if they don't exist on the target\. You can create the target tables yourself if you prefer\. Or you can use AWS Schema Conversion Tool \(AWS SCT\) to create some or all of the target tables, indexes, views, triggers, and so on\. 

The following diagram illustrates the AWS DMS replication process\.

![\[Getting started with AWS DMS\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-Welcome.png)

**References**
+ **AWS Regions that support AWS DMS** – For information about what AWS Regions support AWS DMS, see [Working with an AWS DMS replication instance](CHAP_ReplicationInstance.md)\.
+ **Cost of database migration** – For information on the cost of database migration, see the [AWS Database Migration Service pricing page](https://aws.amazon.com/dms/pricing/)\.
+ **AWS DMS features and benefits** – For information about AWS DMS features and benefits, see [AWS Database Migration Service Features](http://aws.amazon.com/dms/features/)\.
+ **Available database options** – To learn more about the variety of database options available on Amazon Web Services, see [Choosing the right database for your organization](http://aws.amazon.com/getting-started/decision-guides/databases-on-aws-how-to-choose/)\.

## Migration tasks that AWS DMS performs<a name="Welcome.Tasks"></a>

AWS DMS takes over many of the difficult or tedious tasks involved in a migration project:
+ In a traditional solution, you need to perform capacity analysis, procure hardware and software, install and administer systems, and test and debug the installation\. AWS DMS automatically manages the deployment, management, and monitoring of all hardware and software needed for your migration\. Your migration can be up and running within minutes of starting the AWS DMS configuration process\.
+ With AWS DMS, you can scale up \(or scale down\) your migration resources as needed to match your actual workload\. For example, if you determine that you need additional storage, you can easily increase your allocated storage and restart your migration, usually within minutes\.
+ AWS DMS uses a pay\-as\-you\-go model\. You only pay for AWS DMS resources while you use them, as opposed to traditional licensing models with up\-front purchase costs and ongoing maintenance charges\.
+ AWS DMS automatically manages all of the infrastructure that supports your migration server, including hardware and software, software patching, and error reporting\.
+ AWS DMS provides automatic failover\. If your primary replication server fails for any reason, a backup replication server can take over with little or no interruption of service\.
+ AWS DMS Fleet Advisor automatically inventories your data infrastructure\. It creates reports that help you identify migration candidates and plan your migration\.
+ AWS DMS Schema Conversion automatically assesses the complexity of your migration for your source data provider\. It also converts database schemas and code objects to a format compatible with the target database and then applies the converted code\.
+ AWS DMS can help you switch to a modern, perhaps more cost\-effective, database engine than the one you are running now\. For example, AWS DMS can help you take advantage of the managed database services provided by Amazon Relational Database Service \(Amazon RDS\) or Amazon Aurora\. Or it can help you move to the managed data warehouse service provided by Amazon Redshift, NoSQL platforms like Amazon DynamoDB, or low\-cost storage platforms like Amazon Simple Storage Service \(Amazon S3\)\. Conversely, if you want to migrate away from old infrastructure but continue to use the same database engine, AWS DMS also supports that process\.
+ AWS DMS supports nearly all of today's most popular DBMS engines as source endpoints\. For more information, see [Sources for data migration](CHAP_Source.md)\.
+ AWS DMS provides a broad coverage of available target engines\. For more information, see [Targets for data migration](CHAP_Target.md)\.
+ You can migrate from any of the supported data sources to any of the supported data targets\. AWS DMS supports fully heterogeneous data migrations between the supported engines\.
+ AWS DMS ensures that your data migration is secure\. Data at rest is encrypted with AWS Key Management Service \(AWS KMS\) encryption\. During migration, you can use Secure Socket Layers \(SSL\) to encrypt your in\-flight data as it travels from source to target\.