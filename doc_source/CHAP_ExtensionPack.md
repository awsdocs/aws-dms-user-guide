# Using the AWS SCT extension pack<a name="CHAP_ExtensionPack"></a>

The AWS SCT Extension Pack is an add\-on module that emulates functions present in the source database that are required when converting objects to the target database\. Before you can install the AWS SCT Extension Pack, you need to convert your database schema\. 

The AWS SCT Extension Pack includes the following components:
+ DB schema – Includes SQL functions, procedures, and tables for emulating some OLTP and OLAP objects \(for example, sequence\) or unsupported built\-in\-functions from the source database\. This schema is named in the format `aws_<database engine name>_ext`\. 
+ Custom Python library \(for select OLAP databases\) – Includes a set of Python functions that emulate unsupported built\-in database functions\. Use this library when you migrate from one of the supported databases to Amazon Redshift\. 

  For more information on this library, see [Using the custom Python library for the AWS SCT extension pack](#CHAP_ExtensionPack.DW)\.
+ AWS Lambda functions \(for select OLTP databases\) – Includes AWS Lambda functions that emulate complex database functionality, such as job scheduling and sending emails\. 

The following sections discuss the AWS SCT Extension Pack\.

**Topics**
+ [Using the extension pack schema](#CHAP_ExtensionPack.Schema)
+ [Using the custom Python library for the AWS SCT extension pack](#CHAP_ExtensionPack.DW)
+ [Using the AWS Lambda functions from the AWS SCT extension pack](#CHAP_ExtensionPack.OLTP)

You can apply the AWS SCT Extension Pack in two ways:
+ AWS SCT automatically applies the extension pack when you apply a target database script by choosing **ApplyToTarget** from the context menu\. AWS SCT applies the extension pack before it applies all other schema objects\.
+ To manually apply the extension pack, choose the target database and then choose **Apply Extension Pack** from the context menu\. For most situations, automatic application is sufficient\. However, you might want to apply the pack if it's accidentally deleted\. 

Each time the AWS SCT Extension Pack is applied to a target data store, the components are overwritten\. Each component has a version number, and AWS SCT warns you if the current component version is older than the one being applied\. You can control these notifications in the **Notification Settings** in the **Global Settings** section of **Settings**\.

## Using the extension pack schema<a name="CHAP_ExtensionPack.Schema"></a>

When you convert your database or data warehouse schema, AWS SCT adds an additional schema to your target database\. This schema implements SQL system functions of the source database that are required when writing your converted schema to your target database\. This additional schema is called the extension pack schema\.

The extension pack schema for OLTP databases is named according to the source database as follows: 
+ Microsoft SQL Server: `AWS_SQLSERVER_EXT`
+ MySQL: `AWS_MYSQL_EXT`
+ Oracle: `AWS_ORACLE_EXT`
+ PostgreSQL: `AWS_POSTGRESQL_EXT`

The extension pack schema for OLAP data warehouse applications is named according to the source data store as follows: 
+ Greenplum: `AWS_GREENPLUM_EXT`
+ Microsoft SQL Server: `AWS_SQLSERVER_EXT`
+ Netezza: `AWS_NETEZZA_EXT`
+ Oracle: `AWS_ORACLE_EXT`
+ Teradata: `AWS_TERADATA_EXT`
+ Vertica: `AWS_VERTICA_EXT`

## Using the custom Python library for the AWS SCT extension pack<a name="CHAP_ExtensionPack.DW"></a>

In some cases, AWS Schema Conversion Tool can't convert source database features to equivalent Amazon Redshift features\. The AWS SCT Extension Pack contains a custom Python library that emulates some source database functionality on Amazon Redshift\. 

If you are converting a transactional database, instead see [Using the AWS Lambda functions from the AWS SCT extension pack](#CHAP_ExtensionPack.OLTP)\. 

In two cases, you might want to install the extension pack manually: 
+ You accidentally delete the extension pack schema from your target database\. 
+ You want to upload custom Python libraries to emulate database functionality\. 

### Using AWS services to upload custom Python libraries<a name="CHAP_ExtensionPack.DW.Services"></a>

The AWS SCT extension pack wizard helps you install the custom Python library\. 

### Applying the extension pack<a name="CHAP_ExtensionPack.DW.Installing"></a>

Use the following procedure to apply the extension pack\. 

**To apply the extension pack**

1. In the AWS Schema Conversion Tool, in the target database tree, open the context \(right\-click\) menu, and choose **Apply Extension Pack**\.   
![\[Apply Extension Pack context menu\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/images/extension-pack-context.png)

   The extension pack wizard appears\. 

1. Read the **Welcome** page, and then choose **Next**\. 

1. On the **AWS Services Settings** page, do the following: 
   + If you are reinstalling the extension pack schema only, choose **Skip this step for now**, and then choose **Next**\. 
   + If you are uploading the Python library, provide the credentials to connect to your AWS account\. You can use your AWS Command Line Interface \(AWS CLI\) credentials if you have the AWS CLI installed\. You can also use credentials that you previously stored in a profile in the global application settings and associated with the project\. If necessary, choose **Navigate to Project Settings** to associate a different profile with the project\. If necessary, choose **Global Settings** to create a new profile\. For more information, see [Storing AWS service profiles in the AWS SCT](CHAP_UserInterface.md#CHAP_UserInterface.Profiles)\. 

1. On the **Python Library Upload** page, do the following: 
   + If you are reinstalling the extension pack schema only, choose **Skip this step for now**, and then choose **Next**\. 
   + If you are uploading the Python library, provide the Amazon S3 path, and then choose **Upload Library to S3**\. 

   When you are done, choose **Next**\. 

1. On the **Functions Emulation** page, choose **Create Extension Pack**\. Messages appear with the status of the extension pack operations\. 

   When you are done, choose **Finish**\. 

## Using the AWS Lambda functions from the AWS SCT extension pack<a name="CHAP_ExtensionPack.OLTP"></a>

The AWS Schema Conversion Tool extension pack contains Lambda functions that provide email, job scheduling, and other features to databases hosted on the Amazon EC2 platform\.

### Using AWS Lambda functions to emulate database functionality<a name="CHAP_ExtensionPack.OLTP.Services"></a>

In some cases, database features can't be converted to equivalent Amazon RDS features\. For example, Oracle sends email calls that use `UTL_SMTP`, and Microsoft SQL Server can use a job scheduler\. If you host and self\-manage a database on Amazon EC2, you can emulate these features by substituting AWS services for them\. 

The AWS SCT extension pack wizard helps you install, create, and configure Lambda functions to emulate email, job scheduling, and other features\. 

### Applying the extension pack<a name="CHAP_ExtensionPack.OLTP.Installing"></a>

Use the following procedure to apply the extension pack\. 

**Important**  
The AWS service emulation features are supported only for databases installed and self\-managed on Amazon EC2\. Don't install the service emulation features if your target database is on an Amazon RDS DB instance\. 

**To apply the extension pack**

1. In the AWS Schema Conversion Tool, in the target database tree, open the context \(right\-click\) menu, and choose **Apply Extension Pack**\.   
![\[Apply Extension Pack context menu\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/images/extension-pack-context.png)

   The extension pack wizard appears\. 

1. Read the **Welcome** page, and then choose **Next**\. 

1. On the **AWS Services Settings** page, do the following: 
   + If you are reinstalling the extension pack schema only, choose **Skip this step for now**, and then choose **Next**\. 
   + If you are installing AWS services, provide the credentials to connect to your AWS account\. You can use your AWS Command Line Interface \(AWS CLI\) credentials if you have the AWS CLI installed\. You can also use credentials that you previously stored in a profile in the global application settings and associated with the project\. If necessary, choose **Navigate to Project Settings** to associate a different profile with the project\. If necessary, choose **Global Settings** to create a new profile\. For more information, see [Storing AWS service profiles in the AWS SCT](CHAP_UserInterface.md#CHAP_UserInterface.Profiles)\. 

1. On the **Email Sending Service** page, do the following: 
   + If you are reinstalling the extension pack schema only, choose **Skip this step for now**, and then choose **Next**\. 
   + If you are installing AWS services and you have an existing Lambda function, you can provide it\. Otherwise, the wizard creates it for you\. When you are done, choose **Next**\. 

1. On the **Job Emulation Service** page, do the following: 
   + If you are reinstalling the extension pack schema only, choose **Skip this step for now**, and then choose **Next**\. 
   + If you are installing AWS services and you have an existing Lambda function, you can provide it\. Otherwise, the wizard creates it for you\. When you are done, choose **Next**\. 

1. On the **Functions Emulation** page, choose **Create Extension Pack**\. Messages appear with the status of the extension pack operations\. 

   When you are done, choose **Finish**\. 

**Note**  
To update an extension pack and overwrite the old extension pack components, be sure to use the latest version of AWS SCT\.