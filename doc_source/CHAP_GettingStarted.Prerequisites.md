# Prerequisites for AWS Database Migration Service<a name="CHAP_GettingStarted.Prerequisites"></a>

In this section, you can learn the prerequisite tasks for AWS DMS, such as setting up your source and target databases\. As part of these tasks, you also set up a virtual private cloud \(VPC\) based on the Amazon VPC service to contain your resources\. In addition, you set up an Amazon EC2 instance that you use to populate your source database and verify replication on your target database\.

**Note**  
Populating the source database takes up to 45 minutes\.

For this tutorial, you create a MySQL database as your source, and a PostgreSQL database as your target\. This scenario uses commonly used, low\-cost database engines to demonstrate replication\. Using different database engines demonstrates AWS DMS features for migrating data between heterogeneous platforms\.

The resources in this tutorial use the US West \(Oregon\) Region\. If you want to use a different AWS Region, specify your chosen Region instead wherever US West \(Oregon\) appears\.

**Note**  
For the sake of simplicity, the databases that you create for this tutorial don't use encryption or other advanced security features\. You must use security features to keep your production databases secure\. For more information, see [Security in Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/UsingWithRDS.html)\.

For prerequisite steps, see the following topics\.

**Topics**
+ [Create a VPC](#CHAP_GettingStarted.Prerequisites.VPC)
+ [Create Amazon RDS parameter groups](#CHAP_GettingStarted.Prerequisites.params)
+ [Create your source Amazon RDS database](#CHAP_GettingStarted.Prerequisites.sdatabase)
+ [Create your target Amazon RDS database](#CHAP_GettingStarted.Prerequisites.tdatabase)
+ [Create an Amazon EC2 client](#CHAP_GettingStarted.Prerequisites.client)
+ [Populate your source database](#CHAP_GettingStarted.Prerequisites.Populate)

## Create a VPC<a name="CHAP_GettingStarted.Prerequisites.VPC"></a>

In this section, you create a VPC to contain your AWS resources\. Using a VPC is a best practice when using AWS resources, so that your databases, Amazon EC2 instances, security groups, and so on, are logically organized and secure\. 

Using a VPC for your tutorial resources also ensures that you delete all of the resources you use when you are done with the tutorial\. You must delete all of the resources that a VPC contains before you can delete the VPC\.

**To create a VPC for use with AWS DMS**

1. Sign in to the AWS Management Console and open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. On the navigation pane, choose **VPC Dashboard**, and then choose **Create VPC**\.

1. On the **Create VPC** page, enter the following options:
   + **Resources to create**: **VPC and more**
   + **Name tag auto generation**: Choose **Auto\-generate**, and enter **DMSVPC**\.
   + **IPv4 block**: **10\.0\.1\.0/24**
   + **IPv6 CIDR block**: **No IPv6 CIDR block**
   + **Tenancy**: **Default**
   + **Number of availability zones**: 2
   + **Number of public subnets**: 2
   + **Number of private subnets**: 2
   + **NAT gateways \($\)**: **None**
   + **VPC endpoints**: **None**

   Choose **Create VPC**\.

1. On the navigation pane, choose **Your VPCs**\. Note the VPC ID for **DMSVPC**\.

1. On the navigation pane, choose **Security Groups**\. 

1. Choose the group named **default** that has a **VPC ID** that matches the ID that you noted for **DMSVPC**\.

1. Choose the **Inbound rules** tab, and choose **Edit inbound rules**\.

1. Choose **Add rule**\. Add a rule of type **MySQL/Aurora** and choose **Anywhere\-IPv4** for **Source**\. 

1. Choose **Add rule** again\. Add a rule of type **PostgreSQL** and choose **Anywhere\-IPv4** for **Source**\.

1. Choose **Save rules**\.

## Create Amazon RDS parameter groups<a name="CHAP_GettingStarted.Prerequisites.params"></a>

To specify settings for your source and target databases for AWS DMS, use Amazon RDS parameter groups\. To allow initial and ongoing replication between your databases, make sure to configure the following:
+ Your source database's binary log, so that AWS DMS can determine what incremental updates it needs to replicate\.
+ Your target database's replication role, so that AWS DMS ignores foreign key constraints during the initial data transfer\. With this setting, AWS DMS can migrate data out of order\.

**To create parameter groups for use with AWS DMS**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. On the navigation pane, choose **Parameter groups**\.

1. On the **Parameter groups** page, choose **Create parameter group**\.

1. On the **Create parameter group** page, enter the following settings:
   + **Parameter group family**: **mysql8\.0**
   + **Group name**: **dms\-mysql\-parameters**
   + **Description**: **Group for specifying binary log settings for replication**

   Choose **Create**\.

1. On the **Parameter groups** page, choose **dms\-mysql\-parameters**, and on the **dms\-parameter\-group** page, choose **Edit parameters**\.

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

1. On the **dms\-postgresql\-parameters** page, choose **Edit parameters**, and set **session\_replication\_role parameter** to **replica**\. Note that the **session\_replication\_role ** parameter is not on the first page of parameters\. Use the pagination controls or the search field to find the parameter\.

1. Choose **Save changes**\.

## Create your source Amazon RDS database<a name="CHAP_GettingStarted.Prerequisites.sdatabase"></a>

Use the following procedure to create your source Amazon RDS database\.

**To create your source RDS for MySQL database**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. On the **Dashboard** page, choose **Create Database** in the **Database** section\. Don't choose **Create Database** in the **Amazon Aurora** section at the top of the page\.

1. On the **Create database** page, set the following options:
   + **Choose a database creation method**: Choose **Standard Create**\.
   + **Engine options**: For **Engine type**, choose **MySQL**\. For **Version**, leave **MySQL 8\.0\.28** selected\.
   + **Templates**: Choose **Dev/Test**\.
   + **Availability and Durability**: Leave **Multi\-AZ DB Instance** selected\.
   + **Settings**: 
     + **DB instance identifier**: Enter **dms\-mysql**\.
     + **Master username**: Leave as **admin**\.
     + **Auto generate a password**: Leave unselected\.
     + **Master password**: Enter **changeit**\.
     + **Confirm password**: Enter **changeit** again\.
   + **DB instance class**: 
     + **DB instance class**: Leave **Standard classes** chosen\.
     + For **DB instance class**, choose **db\.m5\.large**\.
   + **Storage**: 
     + Clear the **Enable storage autoscaling** box\.
     + Leave the rest of the settings as they are\.
   + **Connectivity**:
     + **Virtual private cloud**: **DMSVPC**
     + **Public access**: **Yes**\. You must enable public access to use the AWS Schema Conversion Tool\.
     + **Availability zone**: **us\-west\-2a**
     + Leave the rest of the settings as they are\.
   + **Database authentication**: Leave **Password authentication** selected\.
   + Expand **Additional configuration**:
     + Under **Database options**, enter **dms\_sample** for **Initial database name**\. 
     + Under **DB parameter group**, choose **dms\-mysql\-parameters**\.
     + Under **Backup**, leave **Enable automatic backups** selected\. Your source database must have automatic backups enabled to support ongoing replication\.
     + Under **Backup retention period**, choose **1 day**\.
     + Under **Encryption**, clear the **Enable encryption** box\.
     + Under **Performance Insights**, clear **the Enable Performance Insights** box\.
     + Under **Monitoring**, clear the **Enable Enhanced monitoring** box\.
     + Under **Maintenance**, clear the **Enable auto minor version upgrade** box\.

1. Choose **Create database**\.

## Create your target Amazon RDS database<a name="CHAP_GettingStarted.Prerequisites.tdatabase"></a>

Repeat the previous procedure to create your target Amazon RDS database, with the following changes\.

**To create your target RDS for PostgreSQL database**

1. Repeat steps 1 and 2 from the previous procedure\. 

1. On the **Create database** page, set the same options, except for these:

   1. For **Engine options**, choose **PostgreSQL**\.

   1. For **Version**, choose **PostgreSQL 13\.4\-R1**

   1. For **DB instance identifier**, enter **dms\-postgresql**\.

   1. For **Master username**, leave **postgres** selected\.

   1. For **DB parameter group**, choose **dms\-postgresql\-parameters**\.

   1. Clear **Enable automatic backups**\.

1. Choose **Create database**\.

## Create an Amazon EC2 client<a name="CHAP_GettingStarted.Prerequisites.client"></a>

In this section, you create an Amazon EC2 client\. You use this client to populate your source database with data to replicate\. You also use this client to verify replication by running queries on the target database\.

Using an Amazon EC2 client to access your databases provides the following advantages over accessing your databases over the internet:
+ You can restrict access to your databases to clients that are in the same VPC\.
+ We have confirmed that the tools you use in this tutorial work, and are easy to install, on Amazon Linux 2, which we recommend for this tutorial\.
+ Data operations between components in a VPC generally perform better than those over the internet\.

**To create and configure an Amazon EC2 client to populate your source database**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. On the **Dashboard**, choose **Launch instance**\.

1. On the **Launch an Instance** page, enter the following values:

   1. In the **Name and tags** section, enter **DMSClient** for **Name**\.

   1. In the **Application and OS Images \(Amazon Machine Image\)** section, leave the settings as they are\.

   1. In the **Instance Type** section, choose **t2\.xlarge**\.

   1. In the **Key pair \(login\)** section, choose **Create a new key pair**\. 

   1. On the **Create key pair** page, enter the following:
      + **Key pair name**: **DMSKeyPair**
      + **Key pair type**: Leave as **RSA**\.
      + **Private key file format**: Choose **pem** for OpenSSH on MacOS or Linux, or **ppk** for PuTTY on Windows\.

      Save the key file when prompted\.
**Note**  
You can also use an existing Amazon EC2 key pair rather than creating a new one\.

   1. In the **Network Settings** section, choose **Edit**\. Choose the following settings:
      + **VPC \- *required***: Choose the VPC with the ID that you recorded for the **DMSVPC** VPC\.
      + **Subnet**: Choose the first public subnet\.
      + **Auto\-assign public IP**: Choose **Enable**\.

      Leave the rest of the settings as they are, and choose **Launch instance**\.

## Populate your source database<a name="CHAP_GettingStarted.Prerequisites.Populate"></a>

In this section, you find endpoints for your source and target databases for later use and use the following tools to populate the source database:
+ Git, to download the script that populates your source database\.
+ MySQL client, to run this script\.

### Get endpoints<a name="CHAP_GettingStarted.Prerequisites.Populate.Hosts"></a>

Find and note the endpoints of your RDS for PostgreSQL and RDS for PostgreSQL DB instances for later use\.

**To find your DB instance endpoints**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. On the navigation pane, choose **Databases**\.

1. Choose the **dms\-mysql** database, and note the **Endpoint** value for the database\.

1. Repeat the previous steps for the **dms\-postgresql** database\.

### Populate your source database<a name="CHAP_GettingStarted.Prerequisites.Populate.Git"></a>

Next, connect to your client instance, install the necessary software, download AWS sample database scripts from Git, and run the scripts to populate your source database\.

**To populate your source database**

1. Connect to the client instance using the host name and public key that you saved in previous steps\. 

   For more information on connecting to an Amazon EC2 instance, see [Accessing Instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstances.html) in the *Amazon EC2 User Guide for Linux Instances*\.
**Note**  
If you are using PuTTY, enable TCP keepalives on the **Connection** settings page so that your connection doesn't time out from inactivity\.

1. Install Git and MySQL\. Confirm installation as needed\.

   ```
   $ sudo yum install git
   $ sudo yum install mysql
   ```

1. Install PSQL\. You use the PSQL client later to verify replication\.

   To do this, take the following steps:

   1. Create the Yum Package Repository Configuration file \(`pgdg.repo`\)\. 

      ```
      $ sudo su
      # cd /etc/yum.repos.d
      # nano pgdg.repo
      ```

   1. Add the following contents to the `pgdg.repo` file\.

      ```
      [pgdg11]
      name=PostgreSQL 11 $releasever - $basearch
      baseurl=https://download.postgresql.org/pub/repos/yum/11/redhat/rhel-7.5-x86_64
      enabled=1
      gpgcheck=0
      gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-PGDG-11
      ```

   1. Install, and confirm installation as needed\.

      ```
      sed -i "s/rhel-\$releasever-\$basearch/rhel-7.5-x86_64/g" "/etc/yum.repos.d/pgdg.repo"
      yum groupinstall "PostgreSQL Database Server 11 PGDG"
      ```

   1. Exit `sudo`\.

      ```
      $ exit
      ```

1. Run the following command to download the database creation scripts from GitHub\.

   ```
   git clone https://github.com/aws-samples/aws-database-migration-samples.git
   ```

1. Change to the `aws-database-migration-samples/mysql/sampledb/v1/` directory\.

1. Run the following command\. Provide the endpoint for your source RDS instance that you noted previously, for example `dms-mysql.cdv5fbeyiy4e.us-west-2.rds.amazonaws.com`\.

   ```
   mysql -h dms-mysql.abcdefghij01.us-west-2.rds.amazonaws.com -P 3306 -u admin -p dms_sample < ~/aws-database-migration-samples/mysql/sampledb/v1/install-rds.sql
   ```

1. Let the database creation script run\. The script takes up to 45 minutes to create the schema and populate the data\.