# Getting started with AWS SCT<a name="CHAP_GettingStarted"></a>

You can use the AWS Schema Conversion Tool \(AWS SCT\) to convert the schema for a source database located either on\-premises or hosted by Amazon Web Services \(AWS\)\. You can convert your source schema to a schema for any supported database that is hosted by AWS\. The AWS SCT application provides a project\-based user interface\. 

Almost all work you do with AWS SCT starts with the following steps:

1. Install AWS SCT\. For more information, see [Installing, verifying, and updating AWS SCT](CHAP_Installing.md)\.

1. Install an AWS SCT agent, if needed\. AWS SCT agents are only required for certain migration scenarios, such as between heterogeneous sources and targets\. For more information, see [Using data extraction agents](agents.md)\. 

1. Familiarize yourself with the user interface of AWS SCT\. For more information, see [Using the AWS SCT user interface](CHAP_UserInterface.md)\.

1. Create an AWS SCT project\. Connect to your source and target databases\. For more information about connecting to your source database, see [Sources for AWS SCT](CHAP_Source.md)\.

1. Create mapping rules\. For more information about mapping rules, see [Creating mapping rules in AWS SCT](CHAP_Source.md)\.

1. Run and then review the Database Migration Assessment Report\. For more information about the assessment report, see [Creating and reviewing the database migration assessment report](CHAP_UserInterface.md#CHAP_UserInterface.AssessmentReport)\.

1. Convert the source database schemas\. There are several aspects of the conversion you need to keep in mind, such as what to do with items that don't convert, and how to map items that should be converted a particular way\. For more information about converting a source schema, see [Converting database schemas using the AWS SCT](CHAP_Converting.md)\.

   If you are converting a data warehouse schema, there are also aspects you need to consider before doing the conversion\. For more information, see [Converting data warehouse schemas to Amazon Redshift using the AWS SCT](CHAP_Converting.DW.md)\.

1. Applying the schema conversion to your target\. For more information about applying a source schema conversion, see [Using the AWS SCT user interface](CHAP_UserInterface.md)\.

1. You can also use the AWS SCT to convert SQL stored procedures and other application code\. For more information, see [Converting application SQL using the AWS SCT](CHAP_Converting.App.md)

You can also use AWS SCT to migrate your data from a source database to an Amazon\-managed database\. For examples, see [Using data extraction agents](agents.md)\.