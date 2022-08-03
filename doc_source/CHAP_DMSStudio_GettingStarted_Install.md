# Step 1: Create a data collector and install the DMS Collector<a name="CHAP_DMSStudio_GettingStarted_Install"></a>

After your prerequisites are in place as described in [Prerequisites](CHAP_DMSStudio_GettingStarted_Prerequisites.md), you create a data collector\. You then download, install, and configure the DMS Collector\. A *data collector *is an AWS resource that you use to manage incoming information from the DMS Collector\. The DMS Collector is an executable file that you run locally to collect information about your data environment\.

**To create a data collector**

1. Sign in to the AWS Management Console and open the AWS DMS console at [https://console\.aws\.amazon\.com/dms/v2/](https://console.aws.amazon.com/dms/v2/)\.

1. Choose **DMS Studio Experience** to turn on DMS Studio functionality in the AWS DMS console\.

1. Choose **Data collectors** in the navigation pane\.

1. Choose **Create data collector**\.

1. In the **Create data collector** page, provide the following values:
   + **Name** – **Test Collector**
   + **Description** – **My test collector**
   + **Amazon S3 bucket** – Choose the Amazon S3 bucket that you created in [Amazon S3 bucket](CHAP_DMSStudio_GettingStarted_Prerequisites.md#CHAP_DMSStudio_GettingStarted_Prerequisites_S3)\.
   + **IAM role** – Choose **DMSDiscoveryS3FullAccess**\.

1. Choose **Create data collector**\. 

The new data collector appears on the **Data collectors** page\.

![\[Screenshot showing created data collector\]](http://docs.aws.amazon.com/dms/latest/userguide/images/dmsstudio_01.png)

**To download, install, and configure the DMS Collector**

1. Open the AWS DMS console at [https://console\.aws\.amazon\.com/dms/v2/](https://console.aws.amazon.com/dms/v2/)\.

1. In the navigation pane, choose **Data collectors**\. 

1. Choose **Test Collector**\.

1. For **Actions**, choose **Download local collector**\.

1. When the download completes, run the `AWS_DMS_Collector_Installer.msi` file\. Leave all of the settings as they are, and choose **Finish**\.

1. After the DMS Collector installation is complete, open the following location in a browser if it doesn't open automatically: `http://localhost:11000`\. 

   The DMS Collector **Configure Credentials** page appears\. Provide a login name and password, confirm the password, and choose **Configure credentials**\. Using a login name and password keeps your DMS Collector secure\.

   The DMS Collector page appears\.  
![\[Screenshot showing successful software check\]](http://docs.aws.amazon.com/dms/latest/userguide/images/dmsstudio_02.png)

1. On the **DMS Collector** page, verify that **MySQL connector for \.NET** in the **Software check** section is **Passed**\.

1. In the **Data forwarding** section, choose **Configure credentials**\.

1. In the **Configure credentials for data forwarding** dialog box, enter your AWS account credentials, and choose **Save credentials**\.  
![\[Screenshot showing configured credentials dialog box\]](http://docs.aws.amazon.com/dms/latest/userguide/images/dmsstudio_03.png)

   For more information about account credentials, see [ Programmatic Access](https://docs.aws.amazon.com/general/latest/gr/aws-sec-cred-types.html#access-keys-and-secret-access-keys) in the *AWS General Reference*\. 

1. On the **DMS Collector** page, verify that the **Data forwarding** section has **Yes** for **Access to Amazon S3** and **Access to AWS DMS**\.  
![\[Screenshot showing successful connection\]](http://docs.aws.amazon.com/dms/latest/userguide/images/dmsstudio_04.png)

1. If your computer is on an Active Directory domain, you can specify a domain controller that DMS Collector uses to discover database servers\.

   If your computer is not on a domain, or you want to skip server discovery and specify your local database manually, skip to step 13\.

   In the **LDAP servers configuration** section, choose **\+ Server**\.

1. In the **Add LDAP Server** dialog box, enter the fully qualified domain name \(FQDN\) and credentials for your domain controller\. To find your domain controller's FQDN, do the following:

   1. Open a command prompt window, and enter the following command to find the domain controller's hostname\.

      ```
      > echo %logonserver%
      ```

   1. Enter the following command to find your DNS suffix\.

      ```
      nslookup
      ```

      Your domain suffix is listed as **Connection\-specific DNS suffix**\.

   1. Your domain controller's FQDN is its hostname followed by its DNS suffix, as in the following example\.

      ```
      my_dc.corp.example.com
      ```

1. \(Optional\) If you want to add your local database manually rather than running server discovery, do the following: 

   1. On the **DMS Collector** home page, choose the **Monitored objects** icon from the navigation pane\.

   1. Choose the **Database servers** tab\.

   1. Choose **\+ Server**\. In the **Add monitored object** dialog box, provide the following information:
      + **Engine** – **MySQL Server**
      + **Host name/ IP** – **localhost**
      + **Port** – **3306**
      + **Authentication type** – **Login/ Password authentication**
      + **Allow public key retrieval** – Select this check box
      + **User name** – **root**
      + **Password** – Enter the password that you created in [Create a local MySQL database](CHAP_DMSStudio_GettingStarted_Prerequisites.md#CHAP_DMSStudio_GettingStarted_Prerequisites_Database)

   1. Choose **Verify connection**\. If the credentials are correct and the connection is successful, you see **Connection verified**\.  
![\[Screenshot showing the successful connection\]](http://docs.aws.amazon.com/dms/latest/userguide/images/dmsstudio_05.png)

   1. Choose **Save**\. The local server appears in the list of monitored objects\.  
![\[Screenshot showing the local server\]](http://docs.aws.amazon.com/dms/latest/userguide/images/dmsstudio_06.png)