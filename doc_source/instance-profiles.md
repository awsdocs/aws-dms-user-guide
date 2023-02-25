# Creating instance profiles for DMS Schema Conversion<a name="instance-profiles"></a>

Before you create your migration project in DMS Schema Conversion, you set up an instance profile\. An *instance profile* specifies network and security settings for your DMS Schema Conversion instances that you use for database schema conversion\.

You can create multiple instance profiles\. Select an instance profile to use for each migration project that you create for DMS Schema Conversion\.

You can create up to 60 instance profiles in DMS Schema Conversion\.

**To create an instance profile**

1. Sign in to the AWS Management Console and open the AWS DMS console at [https://console\.aws\.amazon\.com/dms/v2/](https://console.aws.amazon.com/dms/v2/)\.

1. In the navigation pane, choose **Instance profiles**\.

1. Choose **Create instance profile**\.

1. On the **Create instance profile** page, enter a descriptive value for **Name** for your instance profile\.

1. For **Network type**, choose **Dual\-stack mode** to create an instance profile that supports IPv4 and IPv6 addressing\. Keep the default option to create an instance profile that supports only IPv4 addressing\.

1. Next, choose **Virtual private cloud \(VPC\)** to run your instance of the selected network type\. Then choose values for **Subnet IDs** and **VPC security groups** for your instance profile\.

   To connect to Amazon RDS databases, use public subnet IDs\. To connect to on\-premises databases, use private subnet IDs\. Make sure that you configured your network so that AWS DMS can access your source on\-premises database using the NAT gateway's public IP address\. For more information, see [Create a VPC based on Amazon VPC](set-up.md#set-up-vpc)\.

1. For **Schema conversion settings \- optional**, choose an Amazon S3 bucket to store information from your migration project\. Then choose the AWS Identity and Access Management \(IAM\) role that provides access to this Amazon S3 bucket\. For more information, see [Create an Amazon S3 bucket](set-up.md#set-up-s3-bucket)\.

1. Choose **Create instance profile**\.

After you create your instance profile, you can modify or delete it\.

**To modify an instance profile**

1. Sign in to the AWS Management Console and open the AWS DMS console at [https://console\.aws\.amazon\.com/dms/v2/](https://console.aws.amazon.com/dms/v2/)\.

1. Choose **Instance profiles**\. The **Instance profiles** page opens\.

1. Choose your instance profile, and then choose **Modify**\.

1. Update the name of your instance profile, edit the VPC or Amazon S3 bucket settings\.

1. Choose **Save changes**\.

**To delete an instance profile**

1. Sign in to the AWS Management Console and open the AWS DMS console at [https://console\.aws\.amazon\.com/dms/v2/](https://console.aws.amazon.com/dms/v2/)\.

1. Choose **Instance profiles**\. The **Instance profiles** page opens\.

1. Choose your instance profile, and then choose **Delete**\.

1. Choose **Delete** to confirm your choice\.