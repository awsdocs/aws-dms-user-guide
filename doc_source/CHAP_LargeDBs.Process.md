# Overview of migrating large data stores using AWS DMS and AWS Snowball Edge<a name="CHAP_LargeDBs.Process"></a>

The process of using AWS DMS and AWS Snowball Edge incorporates both on\-premises applications and AWS\-managed services\. We use the terms *local* and *remote* to distinguish these components\.

Local components include the following:
+ AWS SCT
+ AWS DMS Agent \(a local version of AWS DMS that works on\-premises\)
+ AWS Snowball Edge devices

Remote components include the following:
+ Amazon S3
+ AWS DMS

In the following sections, you can find a step\-by\-step guide to configuring, installing, and managing an AWS DMS migration using an Edge device or devices\. 

The following diagram shows an overview of the migration process\.

![\[AWS Database Migration Service and Edge device process\]](http://docs.aws.amazon.com/dms/latest/userguide/images/Snowball-flow.png)

The migration involves a local task, where you move data to an Edge device using the DMS Agent\. After an Edge device is loaded, you return it to AWS\. If you have multiple Edge devices, you can return them at the same time, or in sequence\. When AWS receives an Edge device, a remote task using AWS DMS loads the data to the target data store on AWS\.

To migrate from a local data store to an AWS data store, you follow these steps: 

1. Use the [AWS Snow Family Management Console](https://console.aws.amazon.com/snowfamily/home) to create a new job for importing data to S3 with a **Snowball Edge Storage Optimized** device\. The job involves requesting the device be sent to your address\.

1. Set up AWS SCT on a local machine that can access AWS\. Install the Snowball Edge client tool on a different local machine\.

   For more information about AWS SCT, see [Installing AWS SCT](https://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_Installing.html) in the *AWS Schema Conversion Tool User Guide*\. For more information about the Snowball Edge client tool, see [Downloading and Installing the Snowball Edge client](https://docs.aws.amazon.com/snowball/latest/developer-guide/download-the-client.html) in the *AWS Snowball Edge Developer Guide*\.

1. When the Edge device arrives, power it on, connect to it, then unlock it with the client tool\. For step\-by\-step information, see [Getting started with AWS Snowball edge](https://docs.aws.amazon.com/snowball/latest/developer-guide/common-get-start.html) in the *AWS Snowball Edge Developer Guide\.*

1. Install the Open Database Connectivity \(ODBC\) drivers for your data sources\. Put these on the machine with the Edge client tool\.

1. Install and configure the AWS DMS Agent host on the machine with the Edge client tool\. For more information, see [Step 5: Install the AWS DMS Agent](CHAP_LargeDBs.SBS.install-dms-agent.md)\.

   The AWS DMS Agent must have connectivity to the source database, AWS SCT, AWS, and AWS Snowball Edge\. The AWS DMS Agent is supported on the following Linux platforms only:
   + Red Hat Enterprise Linux versions 6\.2 through 6\.8, 7\.0, and 7\.1 \(64\-bit\)
   + SUSE Linux version 12 \(64\-bit\)

   Although the AWS DMS Agent comes in the AWS SCT installation package, it's better that they aren't located in the same place\. We recommend that you install the DMS Agent on a different machine—not the machine you installed AWS SCT on\.

1. Create a new project in AWS SCT as described in [Step\-by\-step procedures for migrating data using AWS DMS with AWS Snowball Edge](CHAP_LargeDBs.SBS.md)\.

1. Configure AWS SCT to use the AWS Snowball Edge device as described in [Step\-by\-step procedures for migrating data using AWS DMS with AWS Snowball Edge](CHAP_LargeDBs.SBS.md)\. 

1. Register the AWS DMS Agent with AWS SCT as described in [Step\-by\-step procedures for migrating data using AWS DMS with AWS Snowball Edge](CHAP_LargeDBs.SBS.md)\.

1. Create a local and AWS DMS task in SCT as described in [Step\-by\-step procedures for migrating data using AWS DMS with AWS Snowball Edge](CHAP_LargeDBs.SBS.md)\.

1. Run and monitor the task in SCT as described in [Step\-by\-step procedures for migrating data using AWS DMS with AWS Snowball Edge](CHAP_LargeDBs.SBS.md)\.