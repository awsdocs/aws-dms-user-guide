# Prerequisites for working with DMS Schema Conversion<a name="set-up"></a>

To set up DMS Schema Conversion, complete the following tasks\. Then you can set up an instance profile, add data providers, and create a migration project\.

**Topics**
+ [Create a VPC based on Amazon VPC](#set-up-vpc)
+ [Create an Amazon S3 bucket](#set-up-s3-bucket)
+ [Store database credentials in AWS Secrets Manager](#set-up-secrets)
+ [Create IAM roles](#set-up-iam-roles)

## Create a VPC based on Amazon VPC<a name="set-up-vpc"></a>

In this step, you create a virtual private cloud \(VPC\) in your AWS account\. This VPC is based on the Amazon Virtual Private Cloud \(Amazon VPC\) service and contains your AWS resources\.

**To create a VPC for DMS Schema Conversion**

1. Sign in to the AWS Management Console and open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. Choose **Create VPC**\.

1. On the **Create VPC** page, enter the following settings:
   + **Resources to create** – **VPC and more**
   + **Name tag auto\-generation** – Choose **Auto\-generate** and enter a globally unique name\. For example, enter **sc\-vpc**\.
   + **IPv4 CIDR block** – **10\.0\.1\.0/24**
   + **NAT gateways** – **In 1 AZ**
   + **VPC endpoints** – **None**

1. Keep the rest of the settings as they are, and then choose **Create VPC**\.

1. Choose **Subnets**, and take a note of your public and private subnet IDs\.

   To connect to your Amazon RDS databases, use public subnet IDs\.

   To connect to your on\-premises databases, use private subnet IDs\.

1. Choose **NAT gateways**\. Choose your **NAT gateway** and take a note of your **Elastic IP address**\.

   Configure your network to make sure that AWS DMS can access your source on\-premises database from this NAT gateway's public IP address\. For more information, see [Using an internet connection to a VPC](instance-profiles-network.md#instance-profiles-network-internet)\.

Use this VPC when you create your instance profile and target databases on Amazon RDS\.

## Create an Amazon S3 bucket<a name="set-up-s3-bucket"></a>

To store information from your migration project, create an Amazon S3 bucket\. DMS Schema Conversion uses this Amazon S3 bucket to save items such as assessment reports, converted SQL code, information about database schema objects, and so on\.

**To create an Amazon S3 bucket for DMS Schema Conversion**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. Choose **Create bucket**\.

1. On the **Create bucket** page, select a globally unique name for your S3 bucket\. For example, enter **sc\-s3\-bucket**\.

1. For **AWS Region**, choose your Region\.

1. For **Bucket Versioning**, choose **Enable**\.

1. Keep the rest of the settings as they are, and then choose **Create bucket**\.

## Store database credentials in AWS Secrets Manager<a name="set-up-secrets"></a>

Store your source and target database credentials in AWS Secrets Manager\. Make sure that you replicate these secrets to your AWS Region\. DMS Schema Conversion uses these secrets to connect to your databases in the migration project\.

**To store your database credentials in AWS Secrets Manager**

1. Sign in to the AWS Management Console and open the AWS Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. Choose **Store a new secret**\.

1. The **Choose secret type** page opens\. For **Secret type**, choose the type of database credentials to store:
   + **Credentials for Amazon RDS database** – Choose this option to store credentials for your Amazon RDS database\. For **Credentials**, enter the credentials for your database\. For **Database**, choose your database\.
   + **Credentials for other database** – Choose this option to store credentials for your source Oracle or SQL Server databases\. For **Credentials**, enter the credentials for your database\.
   + **Other type of secret** – Choose this option to store only the user name and password to connect to your database\. Choose **Add row** to add two key\-value pairs\. Make sure that you use **username** and **password** for key names\. For values related to these keys, enter the credentials for your database\.

1. For **Encryption key**, choose the AWS KMS key that Secrets Manager uses to encrypt the secret value\. Choose **Next**\.

1. On the **Configure secret** page, enter a descriptive **Secret name**\. For example, enter **sc\-source\-secret** or **sc\-target\-secret**\. 

1. Choose **Replicate secret** and then for **AWS Region** choose your Region\. Choose **Next**\.

1. On the **Configure rotation** page, choose **Next**\.

1. On the **Review** page, review your secret details, and then choose **Store**\.

To store credentials for your source and target databases, repeat these steps\.

## Create IAM roles<a name="set-up-iam-roles"></a>

Create AWS Identity and Access Management \(IAM\) roles to use in your migration project\. DMS Schema Conversion uses these IAM roles to access your Amazon S3 bucket and database credentials stored in AWS Secrets Manager\.

**To create an IAM role that provides access to your Amazon S3 bucket**

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Roles**\.

1. Choose **Create role**\.

1. On the **Select trusted entity** page, choose **AWS service**\. Choose **DMS**\.

1. Choose **Next**\. The **Add permissions** page opens\.

1. For **Filter policies**, enter **S3**\. Choose **AmazonS3FullAccess**\.

1. Choose **Next**\. The **Name, review, and create** page opens\.

1. For **Role name**, enter a descriptive name\. For example, enter **sc\-s3\-role**\. Choose **Create role**\.

**To create an IAM role that provides access to AWS Secrets Manager**

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Roles**\.

1. Choose **Create role**\.

1. On the **Select trusted entity** page, choose **AWS service**\. Choose **DMS**\.

1. Choose **Next**\. The **Add permissions** page opens\.

1. For **Filter policies**, enter **Secret**\. Choose **SecretsManagerReadWrite**\.

1. Choose **Next**\. The **Name, review, and create** page opens\.

1. For **Role name**, enter a descriptive name\. For example, enter **sc\-secrets\-manager\-role**\. Choose **Create role**\.

1. On the **Roles** page, enter **sc\-secrets\-manager\-role** for **Role name**\. Choose **sc\-secrets\-manager\-role**\.

1. On the **sc\-secrets\-manager\-role** page, choose the **Trust relationships tab**\. Choose **Edit trust policy**\.

1. On the **Edit trust policy** page, edit the trust relationships for the role to use your AWS DMS regional service principal as the trusted entity\. This principal has the following format\.

   ```
   dms.region-name.amazonaws.com
   ```

   Replace *region\-name* the name of your Region, such as `us-east-1`\.

   The following code example shows the principal for the `us-east-1` Region\.

   ```
   dms.us-east-1.amazonaws.com
   ```

1. Choose **Update trust policy**\.