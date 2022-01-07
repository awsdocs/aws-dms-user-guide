# Converting ETL processes to AWS Glue<a name="CHAP-converting-aws-glue"></a>

In addition to migrating your schema and data using the AWS Schema Conversion Tool \(AWS SCT\), you can also migrate extract, transform, and load \(ETL\) processes\. This type of migration includes the conversion of ETL\-related business logic\. This logic can be located either inside your source data warehouses or in external scripts that are run separately\. After migration, the ETL processes run in AWS Glue\. You run the ETL to AWS Glue migration as a separate project from converting your data definition language \(DDL\) statements and data\.

![\[A diagram showing the conversion of databases and ETL.\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/images/glue-conversion.png)

Currently, AWS SCT supports the conversion of the following objects to AWS Glue:
+ Stored database code objects
  + Microsoft SQL Server
  + Oracle
  + Teradata
+ Teradata Basic Teradata Query \(BTEQ\) ETL scripts
+ Microsoft SQL Server Integration Services \(SSIS\) ETL packages

**Topics**
+ [Prerequisites](#CHAP-converting-aws-glue-prerequisites)
+ [Understanding the AWS Glue Data Catalog](#CHAP-converting-aws-glue-data-catalog)
+ [Limitations for converting using AWS SCT with AWS Glue](#CHAP-converting-aws-glue-limitations)
+ [Converting using AWS Glue in the AWS SCT](CHAP-converting-aws-glue-ui-process.md)
+ [Converting using the Python API for AWS Glue](CHAP-converting-aws-glue-api-process.md)
+ [Converting SSIS to AWS Glue using AWS SCT](CHAP-converting-aws-glue-ssis.md)

## Prerequisites<a name="CHAP-converting-aws-glue-prerequisites"></a>

Before you begin, do the following:
+ Migrate any source databases that you intend to migrate to AWS\.
+ Migrate the target data warehouses to AWS\.
+ Collect a list of all the code involved in your ETL process\.
+ Collect a list of all the necessary connection information for each database\.

## Understanding the AWS Glue Data Catalog<a name="CHAP-converting-aws-glue-data-catalog"></a>

As part of the process of conversion, AWS Glue loads information regarding the source and target databases\. It organizes this information into categories, in a structure called a *tree\.* The structure includes the following:
+ **Connections** – Connection parameters 
+ **Crawlers** – A list of crawlers, one crawler for each schema
+ **Databases** – Containers that hold tables
+ **Tables** – Metadata definitions that represent the data in the tables
+ **ETL jobs** – Business logic that performs the ETL work
+ **Triggers** – Logic that controls when an ETL job runs in AWS Glue \(whether on demand, by schedule, or triggered by job events\)

The *AWS Glue Data Catalog* is an index to the location, schema, and runtime metrics of your data\. When you work with AWS Glue and AWS SCT, the AWS Glue Data Catalog contains references to data that is used as sources and targets of your ETL jobs in AWS Glue\. To create your data warehouse, catalog this data\. 

You use the information in the Data Catalog to create and monitor your ETL jobs\. Typically, you run a crawler to take inventory of the data in your data stores, but there are other ways to add metadata tables into your Data Catalog\.

When you define a table in your Data Catalog, you add it to a database\. A database is used to organize tables in AWS Glue\. 

## Limitations for converting using AWS SCT with AWS Glue<a name="CHAP-converting-aws-glue-limitations"></a>

The following limitations apply when converting using AWS SCT with AWS Glue\.


|  |  | 
| --- |--- |
| Resource | Default limit | 
| Number of databases for each account | 10,000 | 
| Number of tables for each database | 100,000 | 
| Number of partitions for each table | 1,000,000 | 
| Number of table versions for each table | 100,000 | 
| Number of tables for each account | 1,000,000 | 
| Number of partitions for each account | 10,000,000 | 
| Number of table versions for each account | 1,000,000 | 
| Number of connections for each account | 1,000 | 
| Number of crawlers for each account | 25 | 
| Number of jobs for each account | 25 | 
| Number of triggers for each account | 25 | 
| Number of concurrent job runs for each account | 30 | 
| Number of concurrent job runs for each job | 3 | 
| Number of jobs for each trigger | 10 | 
| Number of development endpoints for each account | 5 | 
| Maximum data processing units \(DPUs\) used by a development endpoint at one time | 5 | 
| Maximum DPUs used by a role at one time | 100 | 
| Database name length |  Unlimited For compatibility with other metadata stores, such as Apache Hive, the name is changed to use lowercase characters\. If you plan to access the database from Athena, provide a name with only alphanumeric and underscore characters\.  | 
| Connection name length | Unlimited | 
| Crawler name length | Unlimited | 