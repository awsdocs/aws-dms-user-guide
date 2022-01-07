# Prerequisites for working with DMS Studio<a name="CHAP_DMSStudio_GettingStarted_Prerequisites"></a>


|  | 
| --- |
| AWS DMS Studio is in preview release for AWS Database Migration Service and is subject to change\. | 

In this section, you do the following prerequisite tasks to run this tutorial: 
+ [MySQL database](#CHAP_DMSStudio_GettingStarted_Prerequisites_Database) for the DMS Collector to discover\.
+ [Amazon S3 bucket](#CHAP_DMSStudio_GettingStarted_Prerequisites_S3) to store your collected information\.
+ [IAM role](#CHAP_DMSStudio_GettingStarted_Prerequisites_IAM) that DMS Studio uses to access your Amazon S3 bucket\.

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

1. On the **Create bucket** page, give the bucket a globally unique name using your sign\-in name, for example **test\-dms\-discovery\-*yoursignin***\. Choose your local AWS Region\.

1. Leave the rest of the settings as they are, and choose **Create bucket**\.

## Create an IAM role<a name="CHAP_DMSStudio_GettingStarted_Prerequisites_IAM"></a>

Next, create an AWS Identity and Access Management \(IAM\) role for DMS Collector and the AWS DMS fleet advisor to use to access the S3 bucket\.

**To create an IAM role for AWS DMS fleet advisor to use**

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Roles**\.

1. Choose **Create role**\.

1. On the **Create role** page, choose **AWS Service**\. Choose **DMS**\. 

1. Choose **Next: Permissions**\.

1. For **Filter policies**, enter **S3**\. Choose **AmazonS3FullAccess**\.

1. Choose **Next: Tags** and **Next: Review**\.

1. For **Role name**, enter **DMSDiscoveryS3FullAccess**\. Choose **Create role**\.

1. On the **Roles** page, choose the **DMSDiscoveryS3FullAccess** role\.

1. On the **DMSDiscoveryS3FullAccess Summary** page, choose the **Trust relationships** tab\. Choose **Edit trust relationship**\.

1. On the **Edit trust relationship** page, paste the following JSON into the **Policy document** box\.

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

   The preceding policy document grants the `sts:AssumeRole` permission to the services that AWS DMS uses for discovery\.

1. Choose **Update trust policy**\.