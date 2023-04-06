# Creating data providers in DMS Schema Conversion<a name="data-providers-create"></a>

You can create data providers and use them in migration projects for DMS Schema Conversion\. Your data provider can be a self\-managed engine running on\-premises or on an Amazon EC2 instance\. Also, your data provider can be a fully managed engine, such as Amazon Relational Database Service \(Amazon RDS\) or Amazon Aurora\.

For each database, you can create a single data provider\. You can use a single data provider in multiple migration projects\.

Before creating a migration project, make sure that you have created at least two data providers\. One of your data providers must be on an AWS service\. You can't use DMS Schema Conversion to migrate your database to an on\-premises database\.

The following procedure shows you how to create data providers in the AWS DMS console wizard\.

**To create a data provider**

1. Sign in to the AWS Management Console, then open the AWS DMS console at [https://console\.aws\.amazon\.com/dms/v2/](https://console.aws.amazon.com/dms/v2/)\.

1. Choose **Data providers**\. The **Data providers** page opens\.

1. Choose **Create data provider**\. The following table describes the settings\.  
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/data-providers-create.html)

1. Choose **Create data provider**\.

After you create a data provider, make sure that you add database connection credentials in AWS Secrets Manager\.