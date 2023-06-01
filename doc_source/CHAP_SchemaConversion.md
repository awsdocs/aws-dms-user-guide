# Converting database schemas using DMS Schema Conversion<a name="CHAP_SchemaConversion"></a>

DMS Schema Conversion in AWS Database Migration Service \(AWS DMS\) makes database migrations between different types of databases more predictable\. Use DMS Schema Conversion to assess the complexity of your migration for your source data provider, and to convert database schemas and code objects\. You can then apply the converted code to your target database\.

DMS Schema Conversion automatically converts your source database schemas and most of the database code objects to a format compatible with the target database\. This conversion includes tables, views, stored procedures, functions, data types, synonyms, and so on\. Any objects that DMS Schema Conversion can't convert automatically are clearly marked\. To complete the migration, you can convert these objects manually\.

At a high level, [DMS Schema Conversion](http://aws.amazon.com/dms/schema-conversion-tool/) operates with the following three components: instance profiles, data providers, and migration projects\. An *instance profile* specifies network and security settings\. A *data provider* stores database connection credentials\. A *migration project* contains data providers, an instance profile, and migration rules\. AWS DMS uses data providers and an instance profile to design a process that converts database schemas and code objects\.

For the list of supported source databases, see [Sources for DMS Schema Conversion](CHAP_Introduction.Sources.md#CHAP_Introduction.Sources.SchemaConversion)\.

For the list of supported target databases, see [Targets for DMS Schema Conversion](CHAP_Introduction.Targets.md#CHAP_Introduction.Targets.SchemaConversion)\.

The following diagram illustrates the DMS Schema Conversion process\.

![\[An architecture diagram of the DMS Schema Conversion.\]](http://docs.aws.amazon.com/dms/latest/userguide/images/dms-schema-conversion-diagram.png)

## Supported AWS Regions<a name="schema-conversion-supported-regions"></a>

You can create a DMS Schema Conversion migration project in the following AWS Regions\. In other Regions, you can use the AWS Schema Conversion Tool\. For more information about AWS SCT, see the [AWS Schema Conversion Tool User Guide](https://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/)\.


| Region Name | Region | 
| --- | --- | 
| US East \(N\. Virginia\) | us\-east\-1 | 
| US East \(Ohio\) | us\-east\-2 | 
| US West \(Oregon\) | us\-west\-2 | 
| Asia Pacific \(Tokyo\) | ap\-northeast\-1 | 
| Asia Pacific \(Singapore\) | ap\-southeast\-1 | 
| Asia Pacific \(Sydney\) | ap\-southeast\-2 | 
| Europe \(Frankfurt\) | eu\-central\-1 | 
| Europe \(Stockholm\) | eu\-north\-1 | 
| Europe \(Ireland\) | eu\-west\-1 | 

## Schema conversion features<a name="schema-conversion-features"></a>

DMS Schema Conversion provides the following features:
+ DMS Schema Conversion automatically manages the AWS Cloud resources that are required for your database migration project\. These resources include instance profiles, data providers, and AWS Secrets Manager secrets\. They also include AWS Identity and Access Management \(IAM\) roles, Amazon S3 buckets, and migration projects\.
+ You can use DMS Schema Conversion to connect to your source database, read the metadata, and create database migration assessment reports\. You can then save the report to an Amazon S3 bucket\. With these reports, you get a summary of your schema conversion tasks and the details for items that DMS Schema Conversion can't automatically convert to your target database\. Database migration assessment reports help evaluate how much of your migration project DMS Schema Conversion can automate\. Also, these reports help estimate the amount of manual effort that is required to complete the conversion\. For more information, see [Creating database migration assessment reports with DMS Schema Conversion](assessment-reports.md)\. 
+ After you connect to your source and target data providers, DMS Schema Conversion can convert your existing source database schemas to the target database engine\. You can choose any schema item from your source database to convert\. After you convert your database code in DMS Schema Conversion, you can review your source code and the converted code\. Also, you can save the converted SQL code to an Amazon S3 bucket\.
+ Before you convert your source database schemas, you can set up transformation rules\. You can use transformation rules to change the data type of columns, move objects from one schema to another, and change the names of objects\. You can apply transformation rules to databases, schemas, tables, and columns\. For more information, see [ Setting up transformation rules](schema-conversion-transformation-rules.md)\.
+ You can change conversion settings to improve the performance of the converted code\. These settings are specific for each conversion pair and depend on the features of the source database that you use in your code\. For more information, see [Specifying schema conversion settings](schema-conversion-settings.md)\.
+ In some cases, DMS Schema Conversion can't convert source database features to equivalent Amazon RDS features\. For these cases, DMS Schema Conversion creates an extension pack in your target database to emulate the features that weren't converted\. For more information, see [Using extension packs](extension-pack.md)\.
+ You can apply the converted code and the extension pack schema to your target database\. For more information, see [Applying your converted code](schema-conversion-save-apply.md#schema-conversion-apply)\.  

## Schema conversion limitations<a name="schema-conversion-limitations"></a>

DMS Schema Conversion is a web\-version of the AWS Schema Conversion Tool \(AWS SCT\)\. DMS Schema Conversion supports less database platforms and provides more limited functionality compared to the AWS SCT desktop application\. To convert data warehouse schemas, big data frameworks, application SQL code, and ETL processes, use AWS SCT\. For more information about AWS SCT, see the [AWS Schema Conversion Tool User Guide](https://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/)\.

The following limitations apply when you use DMS Schema Conversion for database schema conversion:
+ You can't save a migration project and use it in an offline mode\.
+ You can't edit SQL code for the source and target databases in a migration project for DMS Schema Conversion\. To edit the SQL code of your source database, use your regular SQL editor\. Choose **Refresh from database** to add the updated code in your migration project\. To edit the converted code, save it as a SQL script\. Then edit it in your code editor, and apply the updated code to your database manually\.
+ Migration rules in DMS Schema Conversion don't support changing column collation\. Also, you can't use migration rules to move objects to a new schema\.
+ You can't apply filters to your source and target database trees to display only those database objects that meet the filter clause\.
+ DMS Schema Conversion extension pack doesn't include AWS Lambda functions that emulate email sending, job scheduling, and other features in your converted code\.
+ DMS Schema Conversion doesn't support the command line interface \(CLI\)\.