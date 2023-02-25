# Creating required AWS resources for AWS DMS Fleet Advisor<a name="CHAP_FleetAdvisor.Prerequisites"></a>

DMS Fleet Advisor needs a set of AWS resources in your account to forward and import inventory information, and to update the status of the DMS data collector\.

Before you collect data and create inventories of databases and schemas for the first time, complete the following prerequisites\.

## Create an Amazon S3 bucket<a name="CHAP_FleetAdvisor_Prerequisites_S3"></a>

Create an Amazon S3 bucket where inventory metadata can be stored\. We recommend that you preconfigure this S3 bucket before using DMS Fleet Advisor\. Make sure that this S3 bucket is in the same AWS Region where you plan to run DMS Fleet Advisor\. AWS DMS stores your DMS Fleet Advisor inventory metadata in this S3 bucket\.

For more information about creating an S3 bucket, see [Create your first S3 bucket](https://docs.aws.amazon.com/AmazonS3/latest/userguide/creating-bucket.html) in the *Amazon S3 User Guide*\. 

**To create an Amazon S3 bucket to store local data environment information**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. Choose **Create bucket**\. 

1. On the **Create bucket** page, give the bucket a globally unique name using your sign\-in name, for example **fa\-bucket\-*yoursignin***\. 

1. Choose the AWS Region where you use the DMS Fleet Advisor\.

1. Keep the remaining settings and choose **Create bucket**\.

## Create IAM Resources<a name="CHAP_FleetAdvisor_Prerequisites_IAM"></a>

In this section, you create IAM resources for accessing Amazon S3 and DMS Fleet Advisor\.

**To create an IAM policy for DMS Fleet Advisor and your data collector to access Amazon S3**

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Policies**\.

1. Choose **Create policy**\.

1. In the **Create policy** page, choose the **JSON** tab\.

1. Paste the following JSON into the editor, replacing the example code\. Replace `<fa_bucket>` with the Amazon S3 bucket you created in the previous section\.

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Effect": "Allow",
               "Action": [
                   "s3:GetObject*",
                   "s3:GetBucket*",
                   "s3:List*",
                   "s3:DeleteObject*",
                   "s3:PutObject*"
               ],
               "Resource": [
                   "arn:aws:s3:::<fa_bucket>",
                   "arn:aws:s3:::<fa_bucket>/*"
               ]
           }
       ]
   }
   ```

1. Choose **Next: Tags** and **Next: Review\.**

1. Enter **FleetAdvisorS3Policy** for **Name\***\. Choose **Create policy**\.

**To create an IAM policy for DMS data collector to access DMS Fleet Advisor**

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Policies**\.

1. Choose **Create policy**\.

1. In the **Create policy** page, choose the **JSON** tab\.

1. Paste the following JSON code into the editor, replacing the example code\. 

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Effect": "Allow",
               "Action": [
                   "dms:DescribeFleetAdvisorCollectors",
                   "dms:ModifyFleetAdvisorCollectorStatuses",
                   "dms:UploadFileMetadataList"
               ],
               "Resource": "*"
           }
       ]
   }
   ```

1. Choose **Next: Tags** and **Next: Review\.**

1. Enter **DMSCollectorPolicy** for **Name\***\. Choose **Create policy**\.

**To create an IAM role for DMS Fleet Advisor and your data collector to access Amazon S3**

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Roles**\.

1. Choose **Create role**\.

1. On the **Select trusted entity** page, for **Trusted entity type**, choose **AWS Service**\. For **Use cases for other AWS services**, choose **DMS**\. Check **DMS**\. Choose **Next**\. 

1. On the **Add permissions** page, choose **FleetAdvisorS3Policy**\. Choose **Next**\.

1. On the **Name, review, and create** page, enter **FleetAdvisorS3Role** for **Role name**\. Choose **Create role**\.

1. On the **Roles** page, enter **FleetAdvisorS3Role** for **Role name**\. Choose **FleetAdvisorS3Role**\.

1. On the **FleetAdvisorS3Role** page, choose the **Trust relationships** tab\. Choose **Edit trust policy**\.

1. On the **Edit trust policy** page, paste the following JSON into the editor, replacing the existing text\.

   ```
   {
    "Version": "2012-10-17",
    "Statement": [
       {
        "Sid": "",
        "Effect": "Allow",
        "Principal": {
          "Service": [
            "dms.amazonaws.com",
            "dms-fleet-advisor.amazonaws.com"
           ]
         },
        "Action": "sts:AssumeRole"
       }
     ]
   }
   ```

   The preceding policy document grants the `sts:AssumeRole` permission to the services that AWS DMS uses to import collected data from the Amazon S3 bucket\.

1. Choose **Update policy**\.

**To create an IAM user with minimum permissions to use DMS Fleet Advisor**

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Users**\.

1. Choose **Add users**\.

1. On the **Add user** page, enter **FleetAdvisorUser** for **User name\***\. Choose **Access key\- Programmatic Access** for **Select AWS Access Type**\. Choose **Next: Permissions**\.

1. In the **Set permissions** section, choose **Attach existing policies directly**\.

1. Use the search control to find and choose the **DMSCollectorPolicy** and **FleetAdvisorS3Policy** policies\. Choose **Next: Tags**\.

1. On the **Tags** page, choose **Next: Review**\.

1. On the **Review** page, choose **Create user**\. On the next page, choose **Download \.csv** to save the new user credentials\. Use these credentials with DMS Fleet Advisor for minimum required access permissions\.