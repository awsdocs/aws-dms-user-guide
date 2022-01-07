# Installing, verifying, and updating AWS SCT<a name="CHAP_Installing"></a>

The AWS Schema Conversion Tool \(AWS SCT\) is a standalone application that provides a project\-based user interface\. AWS SCT is available for Fedora Linux, Microsoft Windows, and Ubuntu Linux version 15\.04\. AWS SCT is supported only on 64\-bit operating systems\. AWS SCT also installs Amazon Corretto JDK 11\. 

To ensure that you get the correct version of the AWS SCT distribution file we provide verification steps after you download the compressed file\. You can verify the file using the steps provided\.

**Topics**
+ [Installing AWS SCT](#CHAP_Installing.Procedure)
+ [Verifying the AWS SCT file download](#CHAP_Installing.InstallValidation)
+ [Installing the required database drivers](#CHAP_Installing.JDBCDrivers)
+ [Updating the AWS SCT](#CHAP_Installing.Updating)

## Installing AWS SCT<a name="CHAP_Installing.Procedure"></a>

**To install the AWS SCT**

1. Download the compressed file that contains the AWS SCT installer, using the link for your operating system\. All compressed files have a \.zip extension\. When you extract the AWS SCT installer file, it will be in the appropriate format for your operating system\. 
   + [Microsoft Windows](https://s3.amazonaws.com/publicsctdownload/Windows/aws-schema-conversion-tool-1.0.latest.zip) 
   + [Ubuntu Linux \(\.deb\)](https://s3.amazonaws.com/publicsctdownload/Ubuntu/aws-schema-conversion-tool-1.0.latest.zip) 
   + [Fedora Linux \(\.rpm\)](https://s3.amazonaws.com/publicsctdownload/Fedora/aws-schema-conversion-tool-1.0.latest.zip) 

1. Extract the AWS SCT installer file for your operating system, shown following\.   
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_Installing.html)

1. Run the AWS SCT installer file extracted in the previous step\. Use the instructions for your operating system, shown following\.   
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_Installing.html)

1. Install the Java Database Connectivity \(JDBC\) drivers for your source and target database engines\. For instructions and download links, see [Installing the required database drivers](#CHAP_Installing.JDBCDrivers)\. 

 Now, you have completed the setup of the AWS SCT application\. Double\-click the application icon to run AWS SCT\. 

### Installing previous versions of AWS SCT<a name="CHAP_Installing.PreviousVersions"></a>

You can download and install previous versions of the AWS SCT starting from 1\.0\.625\. Use the following format to download a previous version\. You must provide the version and OS information using this format\. 

```
https://d211wdu1froga6.cloudfront.net/builds/1.0/<version>/<OS>/aws-schema-conversion-tool-1.0.zip
```

For example, to download AWS SCT version 632, do the following:
+ Windows \- [ https://d211wdu1froga6\.cloudfront\.net/builds/1\.0/632/Windows/aws\-schema\-conversion\-tool\-1\.0\.zip ](https://d211wdu1froga6.cloudfront.net/builds/1.0/632/Windows/aws-schema-conversion-tool-1.0.zip)
+ Ubuntu \- [ https://d211wdu1froga6\.cloudfront\.net/builds/1\.0/632/Ubuntu/aws\-schema\-conversion\-tool\-1\.0\.zip ](https://d211wdu1froga6.cloudfront.net/builds/1.0/632/Ubuntu/aws-schema-conversion-tool-1.0.zip)
+ Fedora \- [ https://d211wdu1froga6\.cloudfront\.net/builds/1\.0/632/Fedora/aws\-schema\-conversion\-tool\-1\.0\.zip ](https://d211wdu1froga6.cloudfront.net/builds/1.0/632/Fedora/aws-schema-conversion-tool-1.0.zip)

 

## Verifying the AWS SCT file download<a name="CHAP_Installing.InstallValidation"></a>

There are several ways you can verify the distribution file of the AWS SCT\. The simplest is to compare the checksum of the file with the published checksum from AWS\. As an additional level of security, you can use the procedures following to verify the distribution file, based on the operating system where you installed the file\. 

This section includes the following topics\.

**Topics**
+ [Verifying the checksum of the AWS SCT file](#CHAP_Installing.InstallValidation.Checksum)
+ [Verifying the AWS SCT RPM files on Fedora](#CHAP_Installing.InstallValidation.RPM)
+ [Verifying the AWS SCT DEB files on Ubuntu](#CHAP_Installing.InstallValidation.DEB)
+ [Verifying the AWS SCT MSI file on Microsoft Windows](#CHAP_Installing.InstallValidation.MSI)

### Verifying the checksum of the AWS SCT file<a name="CHAP_Installing.InstallValidation.Checksum"></a>

In order to detect any errors that could have been introduced when downloading or storing the AWS SCT compressed file, you can compare the file checksum with a value provided by AWS\. AWS uses the SHA256 algorithm for the checksum\.

**To verify the AWS SCT distribution file using a checksum**

1. Download the AWS SCT distribution file using the links in the Installing section\.

1. Download the latest checksum file, called [sha256Check\.txt](https://d2fk11eyrwr7ob.cloudfront.net/sha256Check.txt)\. For example, the file can appear like the following:

   ```
   Fedora   b4f5f66f91bfcc1b312e2827e960691c269a9002cd1371cf1841593f88cbb5e6
   Ubuntu   4315eb666449d4fcd95932351f00399adb6c6cf64b9f30adda2eec903c54eca4
   Windows  6e29679a3c53c5396a06d8d50f308981e4ec34bd0acd608874470700a0ae9a23
   ```

1. Run the SHA256 validation command for your operating system in the directory that contains the distribution file\. For example, the command to run on the Linux operating system is the following:

   ```
   shasum -a 256 aws-schema-conversion-tool-1.0.latest.zip
   ```

1. Compare the results of the command with the value shown in the sha256Check\.txt file\. The two values should match\.

### Verifying the AWS SCT RPM files on Fedora<a name="CHAP_Installing.InstallValidation.RPM"></a>

AWS provides another level of validation in addition to the distribution file checksum\. All RPM files in the distribution file are signed by an AWS private key\. The public GPG key can be viewed at [amazon\.com\.public\.gpg\-key](https://d2fk11eyrwr7ob.cloudfront.net/aws-dms-team@amazon.com.public.gpg-key)\.

**To verify the AWS SCT RPM files on Fedora**

1. Download the AWS SCT distribution file using the links in the Installing section\.

1. Verify the checksum of the AWS SCT distribution file\.

1. Extract the contents of the distribution file\. Locate the RPM file you want to verify\.

1. Download GPG public key from [ amazon\.com\.public\.gpg\-key ](https://d2fk11eyrwr7ob.cloudfront.net/aws-dms-team@amazon.com.public.gpg-key)

1. Import the public key to your RPM DB \(make sure you have the appropriate permissions\) by using the following command:

   ```
   sudo rpm --import aws-dms-team@amazon.com.public.gpg-key
   ```

1. Check that the import was successful by running the following command:

   ```
   rpm -q --qf "%{NAME}-%{VERSION}-%{RELEASE} \n %{SUMMARY} \n" gpg-pubkey-ea22abf4-5a21d30c
   ```

1. Check the RPM signature by running the following command:

   ```
   rpm --checksig -v aws-schema-conversion-tool-1.0.build number-1.x86_64.rpm
   ```

### Verifying the AWS SCT DEB files on Ubuntu<a name="CHAP_Installing.InstallValidation.DEB"></a>

AWS provides another level of validation in addition to the distribution file checksum\. All DEB files in the distribution file are signed by a GPG detached signature\.

**To verify the AWS SCT DEB files on Ubuntu**

1. Download the AWS SCT distribution file using the links in the Installing section\.

1. Verifying the checksum of the AWS SCT distribution file\.

1. Extract the contents of the distribution file\. Locate the DEB file you want to verify\.

1. Download the detached signature from [aws\-schema\-conversion\-tool\-1\.0\.latest\.deb\.asc](https://d2fk11eyrwr7ob.cloudfront.net/Ubuntu/signatures/aws-schema-conversion-tool-1.0.latest.deb.asc)\.

1. Download the GPG public key from [amazon\.com\.public\.gpg\-key](https://d2fk11eyrwr7ob.cloudfront.net/aws-dms-team@amazon.com.public.gpg-key)\.

1. Import the GPG public key by running the following command:

   ```
   gpg --import aws-dms-team@amazon.com.public.gpg-key
   ```

1. Verify the signature by running the following command:

   ```
   gpg --verify aws-schema-conversion-tool-1.0.latest.deb.asc aws-schema-conversion-tool-1.0.build number.deb
   ```

### Verifying the AWS SCT MSI file on Microsoft Windows<a name="CHAP_Installing.InstallValidation.MSI"></a>

AWS provides another level of validation in addition to the distribution file checksum\. The MSI file has a digital signature you can check to ensure it was signed by AWS\.

**To verify the AWS SCT MSI file on Windows**

1. Download the AWS SCT distribution file using the links in the Installing section\.

1. Verifying the checksum of the AWS SCT distribution file\.

1. Extract the contents of the distribution file\. Locate the MSI file you want to verify\.

1. In Windows Explorer, right\-click the MSI file and select **Properties**\.

1. Choose the **Digital Signatures** tab\.

1. Verify that the digital signature is from Amazon Services LLC\.

## Installing the required database drivers<a name="CHAP_Installing.JDBCDrivers"></a>

For the AWS SCT to work correctly, you must install the JDBC drivers for your source and target database engines\. 

After you download the drivers, you give the location of the driver files\. For more information, see [Storing driver paths in the global settings](#CHAP_Installing.JDBCDrivers.Settings)\. 

You can download the database drivers from the following locations\. 

**Important**  
Install the latest version of the driver available\. The following table includes the lowest version of database driver supported by AWS SCT\. 


****  

| Database engine | Drivers | Download location | 
| --- | --- | --- | 
| Amazon Aurora \(MySQL compatible\) | mysql\-connector\-java\-5\.1\.6\.jar |   [https://www\.mysql\.com/products/connector/](https://www.mysql.com/products/connector/)   | 
| Amazon Aurora \(PostgreSQL compatible\) | postgresql\-42\.2\.19\.jar |   [https://jdbc\.postgresql\.org/download/postgresql\-42\.2\.19\.jar](https://jdbc.postgresql.org/download/postgresql-42.2.19.jar)   | 
| Amazon Redshift | redshift\-jdbc42\-2\.0\.0\.1\.jar |   [https://s3\.amazonaws\.com/redshift\-downloads/drivers/jdbc/2\.0\.0\.1/redshift\-jdbc42\-2\.0\.0\.1\.zip](https://s3.amazonaws.com/redshift-downloads/drivers/jdbc/2.0.0.1/redshift-jdbc42-2.0.0.1.zip)   | 
| Azure SQL | mssql\-jdbc\-7\.2\.2\.jre11\.jar |   [https://docs\.microsoft\.com/en\-us/sql/connect/jdbc/release\-notes\-for\-the\-jdbc\-driver?view=sql\-server\-ver15\#72](https://docs.microsoft.com/en-us/sql/connect/jdbc/release-notes-for-the-jdbc-driver?view=sql-server-ver15#72)   | 
| Azure Synapse | mssql\-jdbc\-7\.2\.2\.jre11\.jar |   [https://docs\.microsoft\.com/en\-us/sql/connect/jdbc/release\-notes\-for\-the\-jdbc\-driver?view=sql\-server\-ver15\#72](https://docs.microsoft.com/en-us/sql/connect/jdbc/release-notes-for-the-jdbc-driver?view=sql-server-ver15#72)   | 
| Greenplum Database | postgresql\-42\.2\.19\.jar |  [https://jdbc\.postgresql\.org/download/postgresql\-42\.2\.19\.jar](https://jdbc.postgresql.org/download/postgresql-42.2.19.jar)   | 
| Maria DB |  mariadb\-java\-client\-2\.4\.1\.jar  |  [https://downloads\.mariadb\.com/Connectors/java/connector\-java\-2\.4\.1/mariadb\-java\-client\-2\.4\.1\.jar](https://downloads.mariadb.com/Connectors/java/connector-java-2.4.1/mariadb-java-client-2.4.1.jar)   | 
| Microsoft SQL Server | mssql\-jdbc\-7\.2\.2\.jre11\.jar |   [https://docs\.microsoft\.com/en\-us/sql/connect/jdbc/release\-notes\-for\-the\-jdbc\-driver?view=sql\-server\-ver15\#72](https://docs.microsoft.com/en-us/sql/connect/jdbc/release-notes-for-the-jdbc-driver?view=sql-server-ver15#72)   | 
| MySQL | mysql\-connector\-java\-8\.0\.15\.jar |   [https://dev\.mysql\.com/downloads/connector/j/](https://dev.mysql.com/downloads/connector/j/)   | 
| Netezza |  nzjdbc\.jar Use the client tools software\. Install driver version 7\.2\.1, which is backwards compatible with data warehouse version 7\.2\.0\.   |   [http://www\.ibm\.com/support/knowledgecenter/SSULQD\_7\.2\.1/com\.ibm\.nz\.datacon\.doc/c\_datacon\_plg\_overview\.html](http://www.ibm.com/support/knowledgecenter/SSULQD_7.2.1/com.ibm.nz.datacon.doc/c_datacon_plg_overview.html)   | 
| Oracle |  ojdbc8\.jar Driver versions 8 and later are supported\.   |   [https://www\.oracle\.com/database/technologies/jdbc\-ucp\-122\-downloads\.html](https://www.oracle.com/database/technologies/jdbc-ucp-122-downloads.html)   | 
| PostgreSQL | postgresql\-42\.2\.19\.jar |   [https://jdbc\.postgresql\.org/download/postgresql\-42\.2\.19\.jar](https://jdbc.postgresql.org/download/postgresql-42.2.19.jar)   | 
| SAP ASE \(Sybase ASE\) | jconn4\.jar | Available as part of the SDK for SAP Adaptive Server Enterprise 16 provided with SAP ASE product\. You can download the trial version of the SDK at [https://www\.sap\.com/developer/trials\-downloads/additional\-downloads/sdk\-for\-sap\-adaptive\-server\-enterprise\-16\-13351\.html](https://www.sap.com/developer/trials-downloads/additional-downloads/sdk-for-sap-adaptive-server-enterprise-16-13351.html) | 
| Teradata |  terajdbc4\.jar tdgssconfig\.jar  |   [https://downloads\.teradata\.com/download/connectivity/jdbc\-driver ](https://downloads.teradata.com/download/connectivity/jdbc-driver)   | 
| Vertica |  vertica\-jdbc\-9\.1\.1\-0\.jar Driver versions 7\.2\.0 and later are supported\.  |   [https://www\.vertica\.com/client\_drivers/9\.1\.x/9\.1\.1\-0/vertica\-jdbc\-9\.1\.1\-0\.jar](https://www.vertica.com/client_drivers/9.1.x/9.1.1-0/vertica-jdbc-9.1.1-0.jar)   | 
| IBM DB2 LUW |  db2jcc\-db2jcc4\.jar  |   [https://www\.ibm\.com/support/pages/node/382667](https://www.ibm.com/support/pages/node/382667)   | 
| Snowflake |  snowflake\-jdbc\-3\.9\.2\.jar For more information, see [https://docs.snowflake.com/en/user-guide/jdbc-download.html](https://docs.snowflake.com/en/user-guide/jdbc-download.html)   |   [ https://repo1\.maven\.org/maven2/net/snowflake/snowflake\-jdbc/3\.9\.2/snowflake\-jdbc\-3\.9\.2\.jar]( https://repo1.maven.org/maven2/net/snowflake/snowflake-jdbc/3.9.2/snowflake-jdbc-3.9.2.jar)   | 

### Installing JDBC drivers on Linux<a name="CHAP_Installing.JDBCDrivers.Linux"></a>

You can use the following steps to install the JDBC drivers on your Linux system for use with AWS SCT\. 

**To install JDBC drivers on your Linux system**

1. Create a directory to store the JDBC drivers in\. 

   ```
   PROMPT>sudo mkdir –p /usr/local/jdbc-drivers
   ```

1. Install the JDBC driver for your database engine using the commands shown following\.   
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_Installing.html)

### Storing driver paths in the global settings<a name="CHAP_Installing.JDBCDrivers.Settings"></a>

After you have downloaded and installed the required JDBC drivers, you can set the location of the drivers globally in the AWS SCT settings\. If you don't set the location of the drivers globally, the application asks you for the location of the drivers when you connect to a database\. 

**To update the driver file locations**

1. In the AWS SCT, choose **Settings**, and then choose **Global Settings**\.   
![\[Choose global settings\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/images/select_global_settings.png)

1. For **Global settings**, choose **Drivers**\. Add the file path to the JDBC driver for your source database engine and your target Amazon RDS DB instance database engine\.   
![\[Global settings\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/images/driver-settings.png)

1. When you are finished adding the driver paths, choose **OK**\. 

## Updating the AWS SCT<a name="CHAP_Installing.Updating"></a>

AWS periodically updates the AWS SCT with new features and functionality\. If you are updating from a previous version, create a new AWS SCT project and reconvert any database objects you are using\.

You can check to see if updates exist for the AWS SCT\.

**To check for updates to AWS SCT**

1. When in the AWS SCT, choose **Help** and then choose **Check for Updates**\.

1. In the **Check for Updates dialog box**, choose **What's New**\. If the link does not appear, you have the latest version\.