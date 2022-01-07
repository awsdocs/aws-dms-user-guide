# Migrating large data stores using AWS Database Migration Service and AWS Snowball Edge<a name="CHAP_LargeDBs"></a>

Larger data migrations can include many terabytes of information\. This process can be cumbersome due to network bandwidth limits or just the sheer amount of data\. AWS Database Migration Service \(AWS DMS\) can use [Snowball Edge](https://aws.amazon.com/snowball-edge/) and Amazon S3 to migrate large databases more quickly than by other methods\. 

Snowball Edge is an AWS service that provides an Edge device that you can use to transfer data to the cloud at faster\-than\-network speeds\. An Edge device is an AWS\-owned appliance with large amounts of on\-board storage\. It uses 256\-bit encryption and an industry\-standard Trusted Platform Module \(TPM\) to ensure both security and full chain of custody for your data\. Snowball Edge offers many additional features; for more information, see [What is an Snowball Edge?](https://docs.aws.amazon.com/snowball/latest/developer-guide/whatisedge.html) in the* AWS Snowball Edge Developer Guide\.*

Amazon S3 is an AWS storage and retrieval service\. To store an object in Amazon S3, you upload the file you want to store to a bucket\. When you upload a file, you can set permissions for the object and also for any metadata\. For more information, see the [S3 documentation](https://docs.aws.amazon.com/s3/index.html)\.

When you're using an Edge device, the data migration process has the following stages: 

1. You use the AWS Schema Conversion Tool \(AWS SCT\) to extract the data locally and move it to an Edge device\. 

1. You ship the Edge device or devices back to AWS\.

1. After AWS receives your shipment, the Edge device automatically loads its data into an Amazon S3 bucket\. 

1. AWS DMS takes the files and migrates the data to the target data store\. If you are using change data capture \(CDC\), those updates are written to the Amazon S3 bucket and then applied to the target data store\.

In the following sections, you can learn about using an Edge device to migrate relational databases with AWS SCT and AWS DMS\. You can also use an Edge device and AWS SCT to migrate on\-premises data warehouses to the AWS Cloud\. For more information about data warehouse migrations, see [Migrating data from an on\-premises data warehouse to Amazon Redshift ](https://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/agents.dw.html) in the *AWS Schema Conversion Tool User Guide*\.

**Topics**
+ [Overview of migrating large data stores using AWS DMS and AWS Snowball Edge](CHAP_LargeDBs.Process.md)
+ [Prerequisites for migrating large data stores using AWS DMS and AWS Snowball Edge](CHAP_LargeDBs.Process.prereqs.md)
+ [Migration checklist](CHAP_LargeDBs.Process.checklist.md)
+ [Step\-by\-step procedures for migrating data using AWS DMS with AWS Snowball Edge](CHAP_LargeDBs.SBS.md)
+ [Limitations when working with AWS Snowball Edge and AWS DMS](CHAP_LargeDBs.Limitations.md)

**Note**  
You can't use an AWS Snowcone device to migrate data with AWS DMS\.