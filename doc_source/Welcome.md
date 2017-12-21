# What Is AWS Database Migration Service?<a name="Welcome"></a>

AWS Database Migration Service \(AWS DMS\) can migrate your data to and from most widely used commercial and open\-source databases such as Oracle, PostgreSQL, Microsoft SQL Server, Amazon Redshift, Amazon Aurora, Amazon DynamoDB, Amazon S3, MariaDB, and MySQL\. The service supports homogeneous migrations such as Oracle to Oracle, and also heterogeneous migrations between different database platforms, such as Oracle to MySQL or MySQL to Amazon Aurora with MySQL compatibility\. The source or target database must be on an AWS service\.

To perform a database migration, AWS DMS connects to the source database, reads the source data, formats the data for consumption by the target database, and loads the data into the target database\. Most of this processing happens in memory, though large transactions might require some buffering to disk\. Cached transactions and log files are also written to disk\.

AWS DMS creates the target schema objects necessary to perform the migration\. However, AWS DMS takes a minimalist approach and creates only those objects required to efficiently migrate the data\. In other words, AWS DMS creates tables, primary keys, and in some cases unique indexes, but it doesn't create any other objects that are not required to efficiently migrate the data from the source\. For example, it doesn't create secondary indexes, non\-primary key constraints, or data defaults\. 

In most cases, when performing a migration, you will also want to migrate most or all of the source schema\. If you are performing a homogeneous migration \(between two databases of the same engine type\), you migrate the schema by using your engine’s native tools to export and import the schema itself, without any data\. If your migration is heterogeneous \(between two databases that use different engine types\), you can use the AWS Schema Conversion Tool to generate a complete target schema for you\. If you use the tool, any dependencies between tables such as foreign key constraints need to be disabled during the migration's "full load" and "cached change apply" phases\. If performance is an issue, removing or disabling secondary indexes during the migration process will help\. For more information on the AWS Schema Conversion Tool, see [AWS Schema Conversion Tool](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_SchemaConversionTool.Installing.html)\.

For information on the cost of database migration, go to the [AWS Database Migration Service pricing page](https://aws.amazon.com/dms/pricing/)\.

AWS DMS is currently available in the following regions:

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/Welcome.html)

## Migration Planning for AWS Database Migration Service<a name="CHAP_Introduction.MigrationPlanning"></a>

When planning a database migration using AWS Database Migration Service, consider the following:

+ You will need to configure a network that connects your source and target databases to a AWS DMS replication instance\. This can be as simple as connecting two AWS resources in the same VPC as the replication instance to more complex configurations such as connecting an on\-premises database to an Amazon RDS DB instance over VPN\. For more information, see [Network Configurations for Database Migration](CHAP_ReplicationInstance.md#CHAP_ReplicationInstance.VPC.Configurations)

+ **Source and Target Endpoints** – You will need to know what information and tables in the source database need to be migrated to the target database\. AWS DMS supports basic schema migration, including the creation of tables and primary keys\. However, AWS DMS doesn't automatically create secondary indexes, foreign keys, user accounts, and so on in the target database\. Note that, depending on your source and target database engine, you may need to set up supplemental logging or modify other settings for a source or target database\. See the [Sources for Data Migration](CHAP_Source.md) and [Targets for Data Migration](CHAP_Target.md) sections for more information\.

+ **Schema/Code Migration** – AWS DMS doesn't perform schema or code conversion\. You can use tools such as Oracle SQL Developer, MySQL Workbench, or pgAdmin III to convert your schema\. If you want to convert an existing schema to a different database engine, you can use the AWS Schema Conversion Tool\. It can create a target schema and also can generate and create an entire schema: tables, indexes, views, and so on\. You can also use the tool to convert PL/SQL or TSQL to PgSQL and other formats\. For more information on the AWS Schema Conversion Tool, see [ AWS Schema Conversion Tool ](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_SchemaConversionTool.Installing.html)\.

+ **Unsupported Data Types** – Some source data types need to be converted into the parallel data types for the target database\. For tables listing conversions between database data types, see [Reference for AWS Database Migration Service Including Data Conversion Reference and Additional Topics](CHAP_Reference.md)\. 