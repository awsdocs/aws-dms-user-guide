# Creating a data collector for AWS DMS Fleet Advisor<a name="CHAP_FleetAdvisor.DataCollector"></a>

This section describes how to create and download DMS data collector\.

**To create and download a data collector**

1. On the console, choose **Data collectors**\. The **Data collectors** page opens\.

1. Choose **Create data collector**\. The **Create data collector** page opens\.  
![\[Create data collector page\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-fa-create-collector-22.png)

1. In the **General configuration** section, enter a value for your data collector for **Name** and for **Description**\.

1. In the **Connectivity** section, choose **Browse S3**\. Choose the Amazon S3 bucket that you preconfigured from the list that appears\.

   AWS DMS stores your DMS Fleet Advisor inventory metadata in this S3 bucket\. Ensure that this S3 bucket is in the same AWS Region where your AWS DMS Fleet Advisor is currently running\.

1. In the list of IAM roles, choose the IAM role that you preconfigured from the list that appears\. This role grants AWS DMS permissions to access the specified Amazon S3 bucket\. 

1. Choose **Create data collector**\. The **Data collectors** page opens and the created data collector appears in the list\.

   When creating your first data collector, AWS DMS configures an environment in your Amazon S3 bucket that formats data and stores attributes for use with DMS Fleet Advisor\.

1. Choose **Download local collector** on the information banner to download your newly created data collector\. A message informs you that the download is in progress\. After the download has finished, you can access the `AWS_DMS_Collector_Installer.msi` file\.

You can now install the DMS data collector on your client\.