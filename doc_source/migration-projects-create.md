# Creating migration projects in DMS Schema Conversion<a name="migration-projects-create"></a>

Before you create a migration project for DMS Schema Conversion, make sure that you create the following resources:
+ Data providers that describe your source and target databases
+ Secrets with database credentials stored in AWS Secrets Manager
+ The AWS Identity and Access Management \(IAM\) role that provides access to Secrets Manager
+ An instance profile that includes network and security settings

**To create a migration project**

1. Sign in to the AWS Management Console and open the AWS DMS console at [https://console\.aws\.amazon\.com/dms/v2/](https://console.aws.amazon.com/dms/v2/)\.

1. Choose **Migration projects**\. The **Migration projects** page opens\.

1. Choose **Create migration project**\. The following table describes the settings\.  
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/migration-projects-create.html)

1. Choose **Create migration project**\.

After AWS DMS creates your migration project, you can use the project to create assessment reports and convert your database objects\. To start working with your migration project, on the **Migration projects** page, choose your project from the list\. Choose **Schema conversion**, then **Launch schema conversion**\.

The first launch of DMS Schema Conversion requires some setup\. AWS Database Migration Service \(AWS DMS\) starts a schema conversion instance, which can take 10\-15 minutes\. This process also reads the metadata from the source and target databases\. After a successful first launch, you can access DMS Schema Conversion faster\.

The schema conversion instance that your migration project uses may be terminated by Amazon three days after you complete the project\. You can retrieve your converted schema and assessment report from the Amazon S3 bucket that you use for DMS Schema Conversion\.