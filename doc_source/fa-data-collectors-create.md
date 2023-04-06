# Creating a data collector for AWS DMS Fleet Advisor<a name="fa-data-collectors-create"></a>

Learn how to create and download a DMS data collector\.

Before you create a data collector, use the IAM console to create a service\-linked role for DMS Fleet Advisor\. This role allows principals to publish metric data points to Amazon CloudWatch\. DMS Fleet Advisor uses this role to display charts with database metrics\. For more information, see [Creating a service\-linked role for DMS Fleet Advisor](using-service-linked-roles.md#create-slr)\.

**To create and download a data collector**

1. Sign in to the AWS Management Console and open the AWS DMS console at [https://console\.aws\.amazon\.com/dms/v2/](https://console.aws.amazon.com/https://console.aws.amazon.com/dms/v2/)\.

   Choose the Region where you use the DMS Fleet Advisor\.

1. In the navigation pane, choose **Data collectors** under **Discover**\. The **Data collectors** page opens\.

1. Choose **Create data collector**\. The **Create data collector** page opens\.  
![\[Create data collector page.\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-fa-create-collector-22.png)

1. For **Name** in the **General configuration** section, enter a name of your data collector\.

1. In the **Connectivity** section, choose **Browse S3**\. Choose the Amazon S3 bucket that you preconfigured from the list that appears\.

   AWS DMS stores your DMS Fleet Advisor inventory metadata in this S3 bucket\. Make sure that your Amazon S3 bucket is in the same AWS Region where your AWS DMS Fleet Advisor is currently running\.

1. In the list of IAM roles, choose the IAM role that you preconfigured from the list that appears\. This role grants AWS DMS permissions to access the specified Amazon S3 bucket\.

1. Choose **Create data collector**\. The **Data collectors** page opens and the created data collector appears in the list\.

   When creating your first data collector, AWS DMS configures an environment in your Amazon S3 bucket that formats data and stores attributes for use with DMS Fleet Advisor\.

1. Choose **Download local collector** on the information banner to download your newly created data collector\. A message informs you that the download is in progress\. After the download has finished, you can access the `AWS_DMS_Collector_Installer_version_number.msi` file\.

You can now install the DMS data collector on your client\. For more information, see [Installing and configuring a data collector](fa-data-collectors-install.md)\.