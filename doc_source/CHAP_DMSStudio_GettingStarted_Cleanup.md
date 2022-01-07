# Step 5: Clean up<a name="CHAP_DMSStudio_GettingStarted_Cleanup"></a>


|  | 
| --- |
| AWS DMS Studio is in preview release for AWS Database Migration Service and is subject to change\. | 

After you complete the getting started tutorial, clean up your resources unless you need them for future projects\.

**Topics**
+ [Uninstall DMS Collector](#CHAP_DMSStudio_GettingStarted_Cleanup_Agent)
+ [Uninstall AWS resources](#CHAP_DMSStudio_GettingStarted_Cleanup_AZ_Resources)
+ [Uninstall MySQL](#CHAP_DMSStudio_GettingStarted_Cleanup_MySQL)

## Uninstall DMS Collector<a name="CHAP_DMSStudio_GettingStarted_Cleanup_Agent"></a>

To start cleanup, uninstall the DMS Collector\.

**To uninstall the DMS Collector**

1. On your PC, open **Settings**, **Apps & Features**\.

1.  Choose AWS **DMS Collector**, and choose **Uninstall**\. Verify your choice\. Microsoft Windows uninstalls the DMS Collector\.

1. Delete the `%PROGRAMDATA%\Amazon\AWS DMS Fleet Advisor` folder\.

## Uninstall AWS resources<a name="CHAP_DMSStudio_GettingStarted_Cleanup_AZ_Resources"></a>

 After you uninstall the collector, you can remove the Amazon S3 bucket and IAM role that you created for [Prerequisites](CHAP_DMSStudio_GettingStarted_Prerequisites.md)\.

**To remove the S3 bucket and IAM role that you created**

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. Choose the **test\-dms\-discovery\-*yoursignin*** bucket, and choose **Empty**\. Verify the operation by entering **permanently delete** in the input field, and choose **Empty**\.

1. Choose the **test\-dms\-discovery\-*yoursignin*** bucket again\. Choose **Delete**, and confirm the operation\.

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Roles**\.

1. Enter **DMSDiscoveryS3FullAccess** in the search bar\. Choose the **DMSDiscoveryS3FullAccess** role\.

1. On the **Summary** page, choose **Delete role**\. Verify the operation\.

## Uninstall MySQL<a name="CHAP_DMSStudio_GettingStarted_Cleanup_MySQL"></a>

After you remove the Amazon S3 bucket and IAM role, you can uninstall MySQL\.

**To uninstall MySQL**

1. On your PC, open **Settings**, **Apps & Features**\.

1. Choose **MySQL Server 8\.0**\. Choose **Uninstall**\. Verify your choice\.

Microsoft Windows uninstalls MySQL Server\. 