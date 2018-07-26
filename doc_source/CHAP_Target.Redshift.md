# Using an Amazon Redshift Database as a Target for AWS Database Migration Service<a name="CHAP_Target.Redshift"></a>

You can migrate data to Amazon Redshift databases using AWS Database Migration Service\. Amazon Redshift is a fully managed, petabyte\-scale data warehouse service in the cloud\. With an Amazon Redshift database as a target, you can migrate data from all of the other supported source databases\.

 The Amazon Redshift cluster must be in the same AWS account and same AWS Region as the replication instance\. 

During a database migration to Amazon Redshift, AWS DMS first moves data to an S3 bucket\. Once the files reside in an S3 bucket, AWS DMS then transfers them to the proper tables in the Amazon Redshift data warehouse\. AWS DMS creates the S3 bucket in the same AWS Region as the Amazon Redshift database\. The AWS DMS replication instance must be located in that same region\. 

If you use the AWS Command Line Interface \(AWS CLI\) or the AWS DMS API to migrate data to Amazon Redshift, you must set up an AWS Identity and Access Management \(IAM\) role to allow S3 access\. For more information about creating this IAM role, see [Creating the IAM Roles to Use With the AWS CLI and AWS DMS API](CHAP_Security.APIRole.md)\.

The Amazon Redshift endpoint provides full automation for the following:
+ Schema generation and data type mapping
+ Full load of source database tables
+ Incremental load of changes made to source tables
+ Application of schema changes in data definition language \(DDL\) made to the source tables
+ Synchronization between full load and change data capture \(CDC\) processes\.

AWS Database Migration Service supports both full load and change processing operations\. AWS DMS reads the data from the source database and creates a series of comma\-separated value \(CSV\) files\. For full\-load operations, AWS DMS creates files for each table\. AWS DMS then copies the table files for each table to a separate folder in Amazon S3\. When the files are uploaded to Amazon S3, AWS DMS sends a copy command and the data in the files are copied into Amazon Redshift\. For change\-processing operations, AWS DMS copies the net changes to the CSV files\. AWS DMS then uploads the net change files to Amazon S3 and copies the data to Amazon Redshift\.

For additional details on working with Amazon Redshift as a target for AWS DMS, see the following sections: 

**Topics**
+ [Prerequisites for Using an Amazon Redshift Database as a Target for AWS Database Migration Service](#CHAP_Target.Redshift.Prerequisites)
+ [Limitations on Using Amazon Redshift as a Target for AWS Database Migration Service](#CHAP_Target.Redshift.Limitations)
+ [Configuring an Amazon Redshift Database as a Target for AWS Database Migration Service](#CHAP_Target.Redshift.Configuration)
+ [Using Enhanced VPC Routing with an Amazon Redshift as a Target for AWS Database Migration Service](#CHAP_Target.Redshift.EnhancedVPC)
+ [Extra Connection Attributes When Using Amazon Redshift as a Target for AWS DMS](#CHAP_Target.Redshift.ConnectionAttrib)
+ [Target Data Types for Amazon Redshift](#CHAP_Target.Redshift.DataTypes)

## Prerequisites for Using an Amazon Redshift Database as a Target for AWS Database Migration Service<a name="CHAP_Target.Redshift.Prerequisites"></a>

The following list describes the prerequisites necessary for working with Amazon Redshift as a target for data migration:
+ Use the AWS Management Console to launch an Amazon Redshift cluster\. You should note the basic information about your AWS account and your Amazon Redshift cluster, such as your password, user name, and database name\. You need these values when creating the Amazon Redshift target endpoint\. 
+ The Amazon Redshift cluster must be in the same AWS account and the same AWS Region as the replication instance\.
+ The AWS DMS replication instance needs network connectivity to the Amazon Redshift endpoint \(hostname and port\) that your cluster uses\.
+ AWS DMS uses an Amazon S3 bucket to transfer data to the Amazon Redshift database\. For AWS DMS to create the bucket, the DMS console uses an Amazon IAM role, `dms-access-for-endpoint`\. If you use the AWS CLI or DMS API to create a database migration with Amazon Redshift as the target database, you must create this IAM role\. For more information about creating this role, see [Creating the IAM Roles to Use With the AWS CLI and AWS DMS API](CHAP_Security.APIRole.md)\. 
+ AWS DMS converts BLOBs, CLOBs, and NCLOBs to a VARCHAR on the target Amazon Redshift instance\. Amazon Redshift doesn't support VARCHAR data types larger than 64 KB, so you can't store traditional LOBs on Amazon Redshift\. 

## Limitations on Using Amazon Redshift as a Target for AWS Database Migration Service<a name="CHAP_Target.Redshift.Limitations"></a>

When using an Amazon Redshift database as a target, AWS DMS doesn't support the following:
+ The following DDL is not supported:

  ```
  ALTER TABLE <table name> MODIFY COLUMN <column name> <data type>;                   
  ```
+  AWS DMS cannot migrate or replicate changes to a schema with a name that begins with underscore \(\_\)\. If you have schemas that have a name that begins with an underscore, use mapping transformations to rename the schema on the target\. 
+  Amazon Redshift doesn't support VARCHARs larger than 64 KB\. LOBs from traditional databases can't be stored in Amazon Redshift\.

## Configuring an Amazon Redshift Database as a Target for AWS Database Migration Service<a name="CHAP_Target.Redshift.Configuration"></a>

AWS Database Migration Service must be configured to work with the Amazon Redshift instance\. The following table describes the configuration properties available for the Amazon Redshift endpoint\.


| Property | Description | 
| --- | --- | 
| server | The name of the Amazon Redshift cluster you are using\. | 
| port | The port number for Amazon Redshift\. The default value is 5439\. | 
| username | An Amazon Redshift user name for a registered user\. | 
| password | The password for the user named in the username property\. | 
| database | The name of the Amazon Redshift data warehouse \(service\) you are working with\. | 

If you want to add extra connection string attributes to your Amazon Redshift endpoint, you can specify the `maxFileSize` and `fileTransferUploadStreams` attributes\. For more information on these attributes, see [Extra Connection Attributes When Using Amazon Redshift as a Target for AWS DMS](#CHAP_Target.Redshift.ConnectionAttrib)\.

## Using Enhanced VPC Routing with an Amazon Redshift as a Target for AWS Database Migration Service<a name="CHAP_Target.Redshift.EnhancedVPC"></a>

If you're using the *Enhanced VPC Routing* feature with your Amazon Redshift target, the feature forces all COPY traffic between your Amazon Redshift cluster and your data repositories through your Amazon VPC\. Because *Enhanced VPC Routing* affects the way that Amazon Redshift accesses other resources, COPY commands might fail if you haven't configured your VPC correctly\.

AWS DMS can be affected by this behavior because it uses the COPY command to move data in S3 to an Amazon Redshift cluster\.

Following are the steps AWS DMS takes to load data into an Amazon Redshift target:

1. AWS DMS copies data from the source to CSV files on the replication server\.

1. AWS DMS uses the AWS SDK to copy the CSV files into an S3 bucket on your account\.

1. AWS DMS then uses the COPY command in Amazon Redshift to copy data from the CSV files in S3 to an appropriate table in Amazon Redshift\.

If *Enhanced VPC Routing* is not enabled, Amazon Redshift routes traffic through the Internet, including traffic to other services within the AWS network\. If the feature is not enabled, you do not have to configure the network path\. If the feature is enabled, you must specifically create a network path between your cluster's VPC and your data resources\. For more information on the configuration required, see [ Enhanced VPC Routing](http://docs.aws.amazon.com/redshift/latest/mgmt/enhanced-vpc-routing.html) in the Amazon Redshift documentation\. 

## Extra Connection Attributes When Using Amazon Redshift as a Target for AWS DMS<a name="CHAP_Target.Redshift.ConnectionAttrib"></a>

You can use extra connection attributes to configure your Amazon Redshift target\. You specify these settings when you create the source endpoint\. Multiple extra connection attribute settings should be separated by a semicolon\.

The following table shows the extra connection attributes available when Amazon Redshift is the target\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Target.Redshift.html)

## Target Data Types for Amazon Redshift<a name="CHAP_Target.Redshift.DataTypes"></a>

The Amazon Redshift endpoint for AWS DMS supports most Amazon Redshift data types\. The following table shows the Amazon Redshift target data types that are supported when using AWS DMS and the default mapping from AWS DMS data types\.

For additional information about AWS DMS data types, see [Data Types for AWS Database Migration Service](CHAP_Reference.DataTypes.md)\.


| AWS DMS Data Types | Amazon Redshift Data Types | 
| --- | --- | 
| BOOLEAN | BOOL | 
| BYTES | VARCHAR \(Length\) | 
| DATE | DATE | 
| TIME | VARCHAR\(20\) | 
| DATETIME | If the scale is => 0 and =< 6, then:  TIMESTAMP \(s\)  If the scale is => 7 and =< 9, then:  VARCHAR \(37\) | 
| INT1 | INT2 | 
| INT2 | INT2 | 
| INT4 | INT4 | 
| INT8 | INT8 | 
| NUMERIC | If the scale is => 0 and =< 37, then:  NUMERIC \(p,s\)  If the scale is => 38 and =< 127, then:  VARCHAR \(Length\) | 
| REAL4 | FLOAT4 | 
| REAL8 | FLOAT8 | 
| STRING | If the length is 1–65,535, then use VARCHAR \(length in bytes\)  If the length is 65,536–2,147,483,647, then use VARCHAR \(65535\) | 
| UINT1 | INT2 | 
| UINT2 | INT2 | 
| UINT4 | INT4 | 
| UINT8 | NUMERIC \(20,0\) | 
| WSTRING |  If the length is 1–65,535, then use NVARCHAR \(length in bytes\)  If the length is 65,536–2,147,483,647, then use NVARCHAR \(65535\) | 
| BLOB | VARCHAR \(maximum LOB size \*2\)  The maximum LOB size cannot exceed 31 KB\. Amazon Redshift doesn't support VARCHARs larger than 64 KB\. | 
| NCLOB | NVARCHAR \(maximum LOB size\)  The maximum LOB size cannot exceed 63 KB\. Amazon Redshift doesn't support VARCHARs larger than 64 KB\. | 
| CLOB | VARCHAR \(maximum LOB size\)  The maximum LOB size cannot exceed 63 KB\. Amazon Redshift doesn't support VARCHARs larger than 64 KB\. | 