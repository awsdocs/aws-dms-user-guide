# Prerequisites<a name="CHAP_GettingStarted.Prerequisites"></a>

In this section, you set up your source and target databases\. You also set up an Amazon VPC to contain your resources, and an Amazon EC2 instance you use to populate the source database and verify replication on the target database\.

**Note**  
Populating the source database takes up to 45 minutes\.

For this tutorial, you create a MySQL database as your source, and a PostgreSQL database as your target\. This scenario uses commonly\-used, low\-cost database engines to demonstrate replication\. Using different database engines demonstrates AWS DMS's features for migrating data between hetergeneous platforms\.

The resources in this tutorial use the `US West (Oregon)` region\. If you want to use a different region, specify your chosen region instead wherever `US West (Oregon)` appears\.

**Note**  
For the sake of simplicity, the databases you create for this tutorial do not use encryption or other advanced security features\. You must use security features to keep your production databases secure\. For more information, see [Security in Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/UsingWithRDS.html)\.

**Topics**
+ [Create an Amazon VPC](#CHAP_GettingStarted.Prerequisites.VPC)
+ [Create Amazon RDS Parameter Groups](#CHAP_GettingStarted.Prerequisites.params)
+ [Create the source Amazon RDS database](#CHAP_GettingStarted.Prerequisites.sdatabase)
+ [Create the target Amazon RDS database](#CHAP_GettingStarted.Prerequisites.tdatabase)
+ [Create an Amazon EC2 client](#CHAP_GettingStarted.Prerequisites.client)
+ [Populate the Source Database](#CHAP_GettingStarted.Prerequisites.Populate)

## Create an Amazon VPC<a name="CHAP_GettingStarted.Prerequisites.VPC"></a>

In this section, you create an Amazon VPC to contain your AWS resources\. Using a VPC is a best practice when using AWS resources, so that your databases, Amazon EC2 instances, security groups, etc\. are logically organized and secure\. Using a VPC for your tutorial resources also ensures that you delete all of the resources you use when you are done with the tutorial\. This is because you must delete all of the resources a VPC contains before you can delete the VPC\.

1. Sign in to the AWS Management Console and open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. From the navigation bar, choose **VPC Dashboard**\.

1. Choose **Launch VPC Wizard**\.

1. Leave **VPC with a Single Public Subnet** selected, and choose **Select**\.

1. In the **Step 2: VPC with a Single Public Subnet** page, enter the following options:
   + **VPC Name**: **DMSVPC**
   + **Availability Zone**: **us\-west\-2a**
   + **Subnet Name**: **DMSSubnet1**

   Choose **Create VPC**\.

1. From the left navigation bar, choose **Your VPCs**\. Record the VPC ID for **DMSVPC**\.

1. From the left navigation bar, choose **Subnets**\. 

1. Choose **DMSSubnet1**\. Make a note of the **Route table** ID in the **Details** section\.

1. Choose **Create Subnet**\.

1. In the **Create subnet** page, choose the following settings:
   + **VPC ID**: Choose the VPC ID for **DMSVPC**\.
   + **Subnet name**: **DMSSubnet2**
   + **Availability Zone**: **US West \(Oregon\) / us\-west\-2b**
   + **IPv4 CIDR block**: **10\.0\.1\.0/24**

   Choose **Create subnet**\.

1. On the **Subnets** page, choose **DMSSubnet2**\. Choose the **Route table** tab\.

1. Choose **Edit route table association**\.

1. On the **Edit route table association** page, choose the **Route table ID** that you recorded previously\. Choose **Save**\.

1. From the navigation bar, choose **Security Groups**\. 

1. Choose the group named **default** that has a **VPC ID** that matches the ID you recorded for **DMSVPC**\.

1. Choose the **Inbound rules** tab\. Choose **Edit inbound rules**\.

1. Choose **Add rule**\. Add a rule of type **MySQL/Aurora** with a **Source** of **0\.0\.0\.0/0**\.

1. Choose **Add rule** again\. Add a rule of type **PostgreSQL** with a **Source** of **0\.0\.0\.0/0**\.

1. Choose **Save rules**\.

## Create Amazon RDS Parameter Groups<a name="CHAP_GettingStarted.Prerequisites.params"></a>

You need parameter groups to specify settings on your source and target databases\. To allow initial and ongoing replication between your databases, you need the following settings:
+ You must configure your source database's binary log, so that AWS DMS can determine what incremental updates it needs to replicate\.
+ You must configure your target database's replication role, so that it will ignore foreign key constraints during the initial data transfer\. This is so that AWS DMS can migrate data out of order\.

To create parameter groups, do the following:

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. From the navigation bar, choose **Parameter groups**\.

1. On the **Parameter groups** page, choose **Create parameter group**\.

1. On the **Create parameter group** page, enter the following settings:
   + **Parameter group family**: **mysql8\.0**
   + **Group name**: **dms\-mysql\-parameters**
   + **Description**: **Group for specifying binary log settings for replication**

   Choose **Create**\.

1. On the **Parameter groups** page, choose **dms\-mysql\-parameters**\.

1. On the **dms\-parameter\-group** page, choose **Edit parameters**\.

1. Set the following parameters to the following values:
   + **binlog\_checksum**: **NONE**
   + **binlog\_format**: **ROW**

   Choose **Save changes**\.

1. On the **Parameter groups** page, choose **Create parameter group** again\.

1. On the **Create parameter group** page, enter the following settings:
   + **Parameter group family**: **postgres12**
   + **Group name**: **dms\-postgresql\-parameters**
   + **Description**: **Group for specifying role setting for replication**

   Choose **Create**\.

1. On the **Parameter groups** page, choose **dms\-postgresql\-parameters**\.

1. On the **dms\-postgresql\-parameters** page, choose **Edit parameters**\.

1. Set the following parameter to the following value:
   + **session\_replication\_role**: **replica**

   Choose **Save changes**\.

## Create the source Amazon RDS database<a name="CHAP_GettingStarted.Prerequisites.sdatabase"></a>

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. On the **Dashboard** page, choose **Create Database** in the **Database** section\. Do not choose **Create Database** in the **Amazon Aurora** section at the top of the page\.

1. On the **Create database** page, choose the following options:
   + **Engine options**: for **Engine type**, choose **MySQL**\.
   + **Templates**: choose **Dev/Test**\.
   + **Settings**: 
     + **DB instance identifier**: **dms\-mysql**
     + **Master username**: leave as **admin**
     + **Auto generate a password**: leave unchecked
     + **Master password**: **changeit**
     + **Confirm passowrd**: enter **changeit** again\.
   + **DB instance class**: 
     + **DB instance class**: Leave **Standard classes** chosen\.
     + In the dropdown under **DB instance class**, choose **db\.m5\.large**\.
   + **Storage**: 
     + Uncheck **Enable storage autoscaling**\.
     + Leave the rest of the settings as they are\.
   + **Availability & Durability**: Leave setting as it is
   + **Connectivity**:
     + **Virtual private cloud**: **DMSVPC**
     + **Public access**: **Yes**\. You must enable public access to use the AWS Schema Conversion Tool\.
     + **Availability zone**: **us\-west\-2a**
     + Leave the rest of the settings as they are\.
   + **Database authentication**: Leave **Password authentication** chosen\.
   + Expand **Additional configuration**\.
     + Under **Database options**, enter **dms\_sample** for **Initial database name**\. 
     + Under **DB parameter group**, choose **dms\-mysql\-parameters**\.
     + Under **Backup**, leave **Enable automatic backups** checked\. Your source database must have automatic backups enabled to support ongoing replication\.
     + Under **Backup retention period**, choose **1 day**\.
     + Under **Encryption**, uncheck **Enable encryption**\.
     + Under **Performance Insights**, uncheck **Enable Performance Insights**\.
     + Under **Monitoring**, uncheck **Enable Enhanced monitoring**\.
     + Under **Maintenance**, uncheck **Enable auto minor version upgrade**\.

1. Choose **Create database**\.

## Create the target Amazon RDS database<a name="CHAP_GettingStarted.Prerequisites.tdatabase"></a>

Repeat the previous procedure, with the following changes:

1. For **Engine options**, choose **PostgreSQL**\.

1. For **DB instance identifier**, enter **dms\-postgresql**\.

1. For **DB parameter group**, choose **dms\-postgresql\-parameters**\.

1. Uncheck **Enable automatic backups**\.

## Create an Amazon EC2 client<a name="CHAP_GettingStarted.Prerequisites.client"></a>

In this section, you create an Amazon EC2 client\. You use this client to populate the source database with data to replicate\. You also use this client to verify replication by running queries on the target database\.

Using an Amazon EC2 client to access your databases provides the following advantages over accessing your databases over the Internet:
+ You can restrict access to your databases to clients that are in the same VPC\.
+ We have confirmed that the tools you use in this tutorial work, and are easy to install, on the operating system this tutorial recommends \(Amazon Linux 2\)\.
+ Data operations between components in a VPC are more performant than those over the Internet\.

To create and configure an Amazon EC2 client, do the following:

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. On the **Dashboard**, choose **Launch instance**\.

1. In the **Amazon Linux 2 AMI \(HVM\), SSD Volume Type** section, choose **Select**\.

1. On the **Step 2: Choose an Instance Type** page, choose **t2\.xlarge**\. Choose **Next: Configure instance details**\.

1. On the **Step 3: Configure Instance Details** page, choose the following settings:
   + **Network**: **DMSVPC**
   + **Subnet**: **DMSSubnet1**
   + **Auto\-assign Public IP**: choose **Enable**\.

   Leave the rest of the settings as they are, and choose **5\. Add Tags** in the header\.

1. In the **Step 5: Add Tags** page, choose **Add Tag**\. Add a tag with a **Key** of **Name** and a **Value** of **DMSClient**\.

1. Leave the rest of the settings as they are, and choose **7\. Review** in the header\.

1. Choose **Launch**\.

1. In the **Select an existing key pair or create a new key pair** page, choose **Create a new key pair** Name the key pair **DMSKeyPair**\. Choose **Download Key Pair** and save the key for the new key pair\.
**Note**  
You can also use an existing Amazon EC2 key pair rather than creating a new one\.

1. Acknowledge that you have the key pair file and choose **Launch instances**\.

1. In the navigation pane, choose Instances, and then choose **DMSClient** by selecting the check box next to it\. 

1. Record the **Public IPv4 DNS** for later use\.

1. In the navigation bar, choose **Security groups**\.

1. Choose the group named **default** that has a **VPC ID** that matches the ID you recorded for **DMSVPC**\.

1. Choose the **Inbound rules** tab\. Choose **Edit inbound rules**\.

1. Choose **Add rule**\. Set **Type** to **All traffic**, and **Source** to **Custom**, **launch\-wizard\-1** \(or whichever security group you created for your Amazon EC2 instance\.\) Choose **Save rules**\.

## Populate the Source Database<a name="CHAP_GettingStarted.Prerequisites.Populate"></a>

In this section, you use the following tools to populate the source database:
+ **Git**: You use this tool to download the database creation script\.
+ **MySQL Client**: You use this tool to run the database creation script\.

### Get Endpoints<a name="CHAP_GettingStarted.Prerequisites.Populate.Hosts"></a>

In this section, you record the endpoints of the MySQL and PostgreSQL Amazon RDS instances\.

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the left navigation bar, choose **Databases**\.

1. Choose the **dms\-mysql** database\.

1. Record the **Endpoint** value for the database\.

1. Repeat the previous steps for the **dms\-postgresql** database\.

### Populate the Source Database<a name="CHAP_GettingStarted.Prerequisites.Populate.Git"></a>

In this section, you connect to the client instance, install software, download the database scripts from Git, and run them to populate the source database\.

1. Connect to the client instance using the host name and public key you saved in previous steps\. For more information on connecting to an Amazon EC2 instance, see [Accessing Instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstances.html) in the Amazon EC2 User Guide for Linux Instances\.
**Note**  
If you are using PuTTY, enable TCP keepalives in the **Connection** settings page so that your connection doesn't time out from inactivity\.

1. Install Git and MySQL\. Confirm installation as needed\.

   ```
   $ sudo yum install git
   $ sudo yum install mysql
   ```

1. Install PSQL\. You use the PSQL client later to verify replication\.
   + Create the Yum Package Repository Configuration file \(`pgdg.repo`\)\. 

     ```
     $ sudo su
     # cd /etc/yum.repos.d
     # nano pgdg.repo
     ```
   + Add the following contents to the `pgdg.repo` file:

     ```
     [pgdg11]
     name=PostgreSQL 11 $releasever - $basearch
     baseurl=https://download.postgresql.org/pub/repos/yum/11/redhat/rhel-7.5-x86_64
     enabled=1
     gpgcheck=0
     gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-PGDG-11
     ```
   + Install\. Confirm installation as needed\.

     ```
     sed -i "s/rhel-\$releasever-\$basearch/rhel-7.5-x86_64/g" "/etc/yum.repos.d/pgdg.repo"
     yum groupinstall "PostgreSQL Database Server 11 PGDG"
     ```
   + Exit `sudo`:

     ```
     $ exit
     ```

1. Run the following command to download the database creation scripts from GitHub:

   ```
   git clone https://github.com/aws-samples/aws-database-migration-samples.git
   ```

1. Change to the `aws-database-migration-samples/mysql/sampledb/v1/` directory\.

1. Run the following command\. Provide the endpoint for your source RDS instance that you recorded previously, e\.g\. `dms-mysql.cdv5fbeyiy4e.us-west-2.rds.amazonaws.com`\.

   ```
   mysql -h dms-mysql.abcdefghij01.us-west-2.rds.amazonaws.com -P 3306 -u admin -pchangeit dms_sample < ~/aws-database-migration-samples/mysql/sampledb/v1/install-rds.sql
   ```

1. Let the database creation script run\. The script takes up to 45 minutes to create the schema and populate the data\.