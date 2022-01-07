# Using AWS SCT with AWS DMS<a name="CHAP_DMSIntegration"></a>

## Using an AWS SCT replication agent with AWS DMS<a name="dms-integration.replication-agent"></a>

For very large database migrations, you can use an AWS SCT replication agent \(aws\-schema\-conversion\-tool\-dms\-agent\) to copy data from your on\-premises database to Amazon S3 or an Amazon Snowball Edge device\. The replication agent works in conjunction with AWS DMS and can work in the background while AWS SCT is closed\.

When working with Amazon Snowball Edge, the AWS SCT agent replicates data to the Amazon Snowball device\. The device is then sent to AWS and the data is loaded to an Amazon S3 bucket\. During this time, the AWS SCT agent continues to run\. The agent then takes the data on Amazon S3 and copies the data to the target endpoint\.

For more information, see [Using data extraction agents](agents.md)\.

## Using an AWS SCT data extraction agent with AWS DMS<a name="dms-integration.data-extraction-agent"></a>

In AWS SCT, you can find a data extraction agent \(`aws-schema-conversion-tool-extractor`\) that helps make migrations from Apache Cassandra to Amazon DynamoDB easier\. Cassandra and DynamoDB are NoSQL databases, but they differ in system architecture and data representation\. You can use wizard\-based workflows in AWS SCT to automate the Cassandra\-to\-DynamoDB migration process\. AWS SCT integrates with AWS Database Migration Service \(AWS DMS\) to perform the actual migration\.

For more information, see [Using data extraction agents](agents.md)\.

## Increasing logging levels when using AWS SCT with AWS DMS<a name="dms-integration.logging-levels"></a>

You can increase logging levels when using AWS SCT with AWS DMS, for example if you need to work with AWS Support\.  

After installing AWS SCT and the required drivers, open the application by choosing the AWS SCT icon\. If you see an update notification, you can choose to update before or after your project is complete\. If an auto\-project window opens, close the window and manually create a project\. 

**To increase logging levels when using AWS SCT with AWS DMS**

1. On the **Settings** menu, choose **Global settings**\.

1. In the **Global settings** window, choose **Logging**\.

1. For **Debug mode**, choose **True**\.

1.   From the **Message level** section, you can modify the following types of logs:
   + General
   + Loader
   + Parser
   + Printer
   + Resolver
   + Telemetry
   + Converter

   By default, all message levels are set to **Info**\.

1. Choose a level of logging for any message level types that you want to change:
   + Trace \(most detailed logging\)
   + Debug
   + Info
   + Warning
   + Error \(least detailed logging\)
   + Critical
   + Mandatory

1. Choose **Apply** to modify settings for your project\. 

1. Choose **OK** to close the **Global settings** window\.