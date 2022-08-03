# Using AWS DMS Fleet Advisor to evaluate databases for migration<a name="CHAP_DMSStudio.FleetAdvisor"></a>

AWS DMS Fleet Advisor collects data from multiple database environments to provide insight into your data infrastructure\. Fleet Advisor collects data from your on\-premises database and analytic servers from one or more central locations without the need to install agents on every computer\. Currently, Fleet Advisor supports Microsoft SQL Server, MySQL, Oracle, and PostgreSQL database servers\.

Based on data discovered from your network, AWS DMS Fleet Advisor builds an inventory that you can review to determine which database servers and objects to monitor\. As details about these servers, databases, and schemas are collected, you can analyze the feasibility of your intended database migrations\.

Before you collect data and create inventories of databases and schemas for the first time, complete the following prerequisites:
+ Create an Amazon S3 bucket where inventory metadata can be stored\. We recommend that you preconfigure this S3 bucket before using AWS DMS Fleet Advisor\. Make sure that this S3 bucket is in the same AWS Region where you plan to run DMS Fleet Advisor\. DMS stores your Fleet Advisor inventory metadata in this S3 bucket\. 

  For more information about creating an S3 bucket, see [Create your first S3 bucket](https://docs.aws.amazon.com/AmazonS3/latest/userguide/creating-bucket.html) in the *Amazon S3 User Guide*\. 
+ Create an IAM role that grants permissions to access the specified Amazon S3 bucket\. We recommend that you preconfigure this IAM role before using AWS DMS Fleet Advisor\. For more information about creating an IAM role, see [Create an IAM role](CHAP_DMSStudio_GettingStarted_Prerequisites.md#CHAP_DMSStudio_GettingStarted_Prerequisites_IAM)\. 

After you perform these tasks, take the following steps, described in detail later in this topic:
+ Create a data collector and download the DMS Collector\.
+ Install the DMS Collector on a local client\.
+ Specify data forwarding credentials and add a Lightweight Directory Access Protocol \(LDAP\) server\.

**To create a data collector and download the DMS Collector**

1. From the DMS Studio console, choose **Data collectors**\. The **Data collectors** page opens\.

1. Choose **Create data collector**\. The **Create data collector** page opens\.  
![\[Create data collector page\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-create-collectors.png)

1. In the **General configuration** section, enter a value for your DMS Collector for **Name** and for **Description**\.

1. In the **Connectivity** section, choose **Browse S3**\. Choose the Amazon S3 bucket that you preconfigured from the list that appears\.

   DMS stores your Fleet Advisor inventory metadata in this S3 bucket\. Ensure that this S3 bucket is in the same AWS Region where your AWS DMS Fleet Advisor is currently running\. 

1. From the list of IAM roles, choose the IAM role that you preconfigured from the list that appears\. This role grants AWS DMS permissions to access the specified Amazon S3 bucket\. 

1. Choose **Create data collector**\. The **Data collectors** page opens\. A message appears at the top of the page telling you that the DMS Collector is created and waiting to be downloaded\.

   When creating your first DMS Collector, AWS DMS configures an environment in your Amazon S3 bucket that formats data and stores attributes for use with Fleet Advisor\.

1. Choose **Download local collector** to download your newly created DMS Collector\. A message informs you that the download is in progress\. After the download has finished, you can access the `Installer.zip` file from your download page\.

You can now install the DMS Collector on your client\. The following table describes hardware and software requirements for installing DMS Collector\. 


| Minimum | Recommended | 
| --- | --- | 
|  **Processor**: 2 cores with CPU benchmark score greater than 8,000  |  **Processor**: 4 cores with CPU benchmark score greater than 11,000  | 
|  **RAM**: 8 GB  |  **RAM**: 16 GB  | 
|  **Hard drive size**: 256 GB  |  **Hard drive size**: 512 GB  | 
|  **Operating system**: Microsoft Windows Server 2012 or higher  |  **Operating system**: Windows Server 2016 or higher  | 

**To install a DMS Collector on a client on your network**

1. From the `Installer.zip` file that you downloaded when creating a DMS Collector, unpack the archive \(extract contents\) into a local folder and run the **\.MSI** installer\. The **AWS DMS Fleet Advisor Collector Setup Wizard** page appears\.

1. Choose **Next**\. The **End\-user license agreement** appears\. 

1. Read and accept the **End\-user license agreement**\.

1. Choose **Next**\. The **Destination folder** page appears\. 

1. Choose **Next** to install the DMS Collector in the default directory\.

   Or choose **Change** to enter another install directory\. Then choose **Next**\.

1. On the **Desktop shortcut** page, select the box to install an icon on your desktop\.

1. Choose **Install**\. The DMS Collector is installed in the directory that you chose\.

1. On the **Completed DMS Collector Setup Wizard** page, choose **Launch AWS DMS Fleet Advisor**, and then choose **Finish**\.

After you install the DMS Collector, you can run it from a browser by entering **http://localhost:11000/** as the address\. Or from the Microsoft Windows **Start** menu, choose **AWS DMS Collector** from the list of programs\.

The AWS DMS Collector home page provides information to you when preparing and running data collection, including the following status conditions:
+ Status and health of your data collection\.
+ Accessibility to your Amazon S3 bucket and to AWS DMS so the DMS Collector can forward data to DMS\.
+ Connectivity to your installed database drivers\.
+ Credentials of an LDAP server to perform initial discovery\.

![\[DMS Collector Home page\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-dms-collector-home.png)

The DMS Collector uses an LDAP directory to obtain information about the machines and database servers in your network\. *Lightweight Directory Access Protocol \(LDAP\)* is an open, industry\-standard application protocol\. It's used for accessing and maintaining distributed directory information services over an Internet Protocol network\. You can add an existing LDAP server to your project for DMS Collector to use to discover information about your systems infrastructure\.

**To specify data forwarding credentials and add an LDAP server to your project**

1. On the DMS Collector home page, in the **Data forwarding** section, choose **Configure credentials**\. The **Credentials for data forwarding** dialog box opens\.

1. Enter your **AWS Access Key ID**, and your **AWS Secret Access Key**\.

1. Choose **Save credentials**, then choose **Yes** to verify that there is access to your Amazon S3 bucket and DMS\.

1. On the DMS Collector home page, choose **\+Server** from the **LDAP servers configurations** section\.

1. Enter a value for **Host name** for the LDAP server\.

1. Enter values for **Username** and **Password**\.

1. Choose **Add server** and verify that your connection status is **Passed**\.

After you have a configured the LDAP server, you can perform an initial discovery of operating system \(OS\) and database servers\.