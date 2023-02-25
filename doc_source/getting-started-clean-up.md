# Step 7: Clean up and troubleshoot<a name="getting-started-clean-up"></a>

You can use Amazon CloudWatch to review or share your DMS Schema Conversion logs\.

**To review DMS Schema Conversion logs**

1. Sign in to the AWS Management Console and open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. Choose **Logs**, **Log groups**\. 

   The name of your DMS Schema Conversion log group starts with `dms-tasks-sct`\. You can sort the log groups by **Creation time** to find DMS Schema Conversion log group\.

   Also, the name of your log group includes the Amazon Resource Name \(ARN\) of your migration project\. You can see the ARN of your project on the **Migration projects** page in DMS Schema Conversion\. Make sure that you choose **ARN** in **Preferences**\.

1. Choose the name of your log group, and then choose the name of your log stream\.

1. For **Actions**, choose **Export results** to save your DMS Schema Conversion log\.

After you've finished your schema conversion in DMS Schema Conversion, clean up your resources\.

**To clean up your DMS Schema Conversion resources**

1. Sign in to the AWS Management Console and open the AWS DMS console\.

1. In the navigation pane, choose **Migration projects**\.

   1. Choose **sc\-project**\.

   1. Choose **Schema conversion**, and then choose **Close schema conversion**\.

   1. Choose **Delete** and confirm your choice\.

1. In the navigation pane, choose **Instance profiles**\.

   1. Choose **sc\-instance**\.

   1. Choose **Delete** and confirm your choice\.

1. In the navigation pane, choose **Data providers**\.

   1. Select **sc\-source** and **sc\-target**\.

   1. Choose **Delete** and confirm your choice\.

Also, make sure that you clean up other AWS resources that you created, such as your Amazon S3 bucket, database secrets in AWS Secrets Manager, IAM roles, and virtual private cloud \(VPC\)\.