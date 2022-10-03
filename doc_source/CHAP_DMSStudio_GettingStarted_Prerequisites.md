# Prerequisites for working with DMS Studio<a name="CHAP_DMSStudio_GettingStarted_Prerequisites"></a>

DMS Studio Fleet Advisor needs a set of AWS resources in your account to deliver the data collector build, forward and import inventory information, and update the status of the DMS Collector\. This section describes how to create the resources that DMS Studio Fleet Advisor needs\.

In this section, you do the following prerequisite tasks to run this tutorial: 
+ [MySQL database](#CHAP_DMSStudio_GettingStarted_Prerequisites_Database): Create a database for the DMS Collector to discover\.
+ [Amazon S3 bucket](#CHAP_DMSStudio_GettingStarted_Prerequisites_S3) Create an Amazon S3 bucket to deliver the local DMS Collector build, and to forward and import your collected inventory information\.
+ [IAM Resources](#CHAP_DMSStudio_GettingStarted_Prerequisites_IAM) : Create IAM resources that the DMS Collector and DMS Studio Fleet Advisor use to access your Amazon S3 bucket, and that DMS Collector uses to access DMS Studio Fleet Advisor\.

## Create a local MySQL database<a name="CHAP_DMSStudio_GettingStarted_Prerequisites_Database"></a>

Before you get started with DMS Studio, create a local MySQL database and populate it with data\. You can also run the DMS Collector on an existing database in your local network, rather than creating a MySQL database\.

**To create a local MySQL database and populate it with data**

1. Download and install the [MySQL Community Server](https://dev.mysql.com/downloads/windows/installer/8.0.html)\. Make sure to register for an Oracle account if you don't already have one\. You do this because the Oracle Corporation owns MySQL, and MySQL downloads are hosted on Oracle's website\. 

   On the **Choose a setup type** screen, choose **Developer Default**\. Provide a root password when prompted, and leave all other settings as they are\.

1. Add the following location to your path\.

   ```
   C:\Program Files\MySQL\MySQL Server 8.0\bin\
   ```

1. Install Git if you haven't already done so\. To do this, see [Git](https://git-scm.com/downloads) on the Git website\. On the **Adjusting your PATH environment** page, verify that **Git from the command line and also from 3rd party software** is selected\.

1. Run the following command to download a script to create and populate a database on your server\.

   ```
   git clone https://github.com/datacharmer/test_db.git
   ```

1. Run the following command from the `test_db` directory to run the script\. Provide your root password that you created when you installed MySQL\. In the following command, there's no space between `-p` and your password\.

   ```
   mysql -uroot -p<root password> < employees.sql
   ```

## Create an Amazon S3 bucket<a name="CHAP_DMSStudio_GettingStarted_Prerequisites_S3"></a>

Next, create an Amazon S3 bucket to store information about your local data environment\.

**To create an Amazon S3 bucket to store local data environment information**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. Choose **Create bucket**\. 

1. On the **Create bucket** page, give the bucket a globally unique name using your sign\-in name, for example **fa\-bucket\-*yoursignin***\. 

1. Choose the AWS Region where you use the DMS Studio Fleet Advisor\.

1. Leave the rest of the settings as they are, and choose **Create bucket**\.

## Create IAM Resources<a name="CHAP_DMSStudio_GettingStarted_Prerequisites_IAM"></a>

In this section, you create IAM resources for accessing Amazon S3 and DMS Studio Fleet Advisor\.

**To create an IAM policy for DMS Studio Fleet Advisor and DMS Collector to access Amazon S3**

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

**To create an IAM policy for DMS Collector to access DMS Studio Fleet Advisor**

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Policies**\.

1. Choose **Create policy**\.

1. In the **Create policy** page, choose the **JSON** tab\.

1. Paste the following JSON into the editor, replacing the example code\. 

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

**To create an IAM role for DMS Studio Fleet Advisor and DMS Collector to access Amazon S3**

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

**To create an IAM user with minimum permissions to use DMS Studio Fleet Advisor**

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Users**\.

1. Choose **Add users**\.

1. On the **Add user** page, enter **FleetAdvisorUser** for **User name\***\. Choose **Access key\- Programmatic Access** for **Select AWS Access Type**\. Choose **Next: Permissions**\.

1. In the **Set permissions** section, choose **Attach existing policies directly**\.

1. Use the search control to find and choose the **DMSCollectorPolicy** and **FleetAdvisorS3Policy** policies\. Choose **Next: Tags**\.

1. On the **Tags** page, choose **Next: Review**\.

1. On the **Review** page, choose **Create user**\. On the next page, choose **Download \.csv** to save the new user credentials\. Use these credentials with DMS Studio Fleet Advisor for minimum required access permissions\.