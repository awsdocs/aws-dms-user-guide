# Managing migration projects in DMS Schema Conversion<a name="migration-projects-manage"></a>

After you create your migration project, you can modify or delete it\. For example, to change the source or target data provider, modify your migration project\.

You can modify or delete your migration project only after you close the schema conversion operation\. To do so, choose your migration project from the list, and choose **Schema conversion**\. Next, choose **Close schema conversion** and confirm your choice\. After you edit your migration project, choose **Launch schema conversion**\. AWS DMS starts a schema conversion instance again\. This operation takes 10\-15 minutes\.

**To modify a migration project**

1. Sign in to the AWS Management Console and open the AWS DMS console at [https://console\.aws\.amazon\.com/dms/v2/](https://console.aws.amazon.com/dms/v2/)\.

1. Choose **Migration projects**\. The **Migration projects** page opens\.

1. Choose your migration project, and then choose **Modify**\.

1. Update the name of your project, edit the instance profile, or change source and target data providers\. Optionally, add or edit migration rules that change the object names during conversion\.

1. Choose **Save changes**\.

**To delete a migration project**

1. Sign in to the AWS Management Console and open the AWS DMS console at [https://console\.aws\.amazon\.com/dms/v2/](https://console.aws.amazon.com/dms/v2/)\.

1. Choose **Migration projects**\. The **Migration projects** page opens\.

1. Choose your migration project, and then choose **Delete**\.

1. Choose **Delete** to confirm your choice\.