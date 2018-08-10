# Migrating Large Data Stores Using AWS Database Migration Service and AWS Snowball<a name="CHAP_LargeDBs"></a>

Large\-scale data migrations can include many terabytes of information, and can be slowed by network performance and by the sheer amount of data that has to be moved\. AWS DMS can load data onto an AWS Snowball device, transfer that data to AWS, and then load the data to the target AWS data store\. 

Using AWS DMS and the AWS Schema Conversion Tool \(AWS SCT\), you migrate your data in two stages\. First, you use the AWS SCT to process the data locally and then move that data to the AWS Snowball Edge appliance\. AWS Snowball then automatically loads the data into an Amazon S3 bucket\. Next, when the data is available on Amazon S3, AWS DMS takes the files and migrates the data to the target data store\. If you are using change data capture \(CDC\), those updates are written to the Amazon S3 bucket and the target data store is constantly updated\.

AWS Snowball is an AWS service you can use to transfer data to the cloud at faster\-than\-network speeds using an AWS\-owned appliance\. An AWS Snowball Edge device can hold up to 100 TB of data\. It uses 256\-bit encryption and an industry\-standard Trusted Platform Module \(TPM\) to ensure both security and full chain\-of\-custody for your data\.

Amazon S3 is a storage and retrieval service\. To store an object in Amazon S3, you upload the file you want to store to a bucket\. When you upload a file, you can set permissions on the object and also on any metadata\.

AWS DMS supports the following scenarios:
+ Migration from an on\-premises data warehouse to Amazon Redshift\. This approach involves a client\-side software installation of the AWS Schema Conversion Tool\. The tool reads information from the warehouse \(the extractor\), and then moves data to S3 or Snowball\. Then in the AWS Cloud, information is either read from S3 or Snowball and injected into Amazon Redshift\.
+ Migration from an on\-premises relational database to an Amazon RDS database\. This approach again involves a client\-side software installation of the AWS Schema Conversion Tool\. The tool reads information from a local database that AWS supports\. The tool then moves data to S3 or Snowball\. When the data is in the AWS Cloud, AWS DMS writes it to a supported database in either Amazon EC2 or Amazon RDS\.

## Process Overview<a name="CHAP_LargeDBs.Process"></a>

The process of using AWS DMS and AWS Snowball involves several steps, and it uses not only AWS DMS and AWS Snowball but also the AWS Schema Conversion Tool \(AWS SCT\)\. The sections following this overview provide a step\-by\-step guide to each of these tasks\.

**Note**  
We recommend that you test your migration before you use the AWS Snowball device\. To do so, you can set up a task to send data, such as a single table, to an Amazon S3 bucket instead of the AWS Snowball device\.

![\[AWS Database Migration Service and AWS Snowball process\]](http://docs.aws.amazon.com/dms/latest/userguide/images/Snowball-flow.png)

The migration involves a local task, where AWS SCT moves the data to the AWS Snowball Edge device, an intermediate action where the data is copied from the AWS Snowball Edge device to an S3 bucket\. The process then involves a remote task where AWS DMS loads the data from the Amazon S3 bucket to the target data store on AWS\.

The following steps need to occur to migrate data from a local data store to an AWS data store using AWS Snowball\.

1. Create an AWS Snowball job using the AWS Snowball console\. For more information, see [Create an Import Job](https://docs.aws.amazon.com/snowball/latest/ug/create-import-job.html) in the AWS Snowball documentation\.

1. Download and install the AWS SCT application on a local machine\. The machine must have network access and be able to access the AWS account to be used for the migration\. For more information about the operating systems AWS SCT can be installed on, see [ Installing and Updating the AWS Schema Conversion Tool ](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_SchemaConversionTool.Installing.html)\.

1. Install the AWS SCT DMS Agent \(DMS Agent\) on a local, dedicated Linux machine\. We recommend that you do not install the DMS Agent on the same machine that you install the AWS SCT application\. 

1. Unlock the AWS Snowball Edge device using the local, dedicated Linux machine where you installed the DMS Agent\.

1. Create a new project in AWS SCT\.

1. Configure the AWS SCT to use the DMS Agent\.

1. Register the DMS Agent with the AWS SCT\.

1. Install the database driver for your source database on the dedicated machine where you installed the DMS Agent\. 

1. Create and set permissions for the Amazon S3 bucket to use\. 

1. Edit the **AWS Service Profile** in AWS SCT\.

1. Create **Local & DMS Task** in SCT\.

1. Run and monitor the **Local & DMS Task** in SCT\.

1. Run the AWS SCT task and monitor progress in SCT\.

## Step\-by\-Step Procedures for Migrating Data Using AWS DMS and AWS Snowball<a name="CHAP_LargeDBs.SBS"></a>

The following sections provide detailed information on the migration steps\.

### Step 1: Create an AWS Snowball Job<a name="CHAP_LargeDBs.SBS.OrderSnowball"></a>

Create an AWS Snowball job by following the steps outlined in the section [ Getting Started with AWS Snowball Edge: Your First Job](http://docs.aws.amazon.com/snowball/latest/developer-guide/common-get-start.html) in the AWS Snowball documentation\.

### Step 2: Download and Install the AWS Schema Conversion Tool \(AWS SCT\)<a name="CHAP_LargeDBs.SBS.InstallSCT"></a>

Download and install the AWS Schema Conversion Tool using the instructions at [ Installing and Updating the AWS Schema Conversion Tool](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_SchemaConversionTool.Installing.html) in the AWS SCT documentation\. Install the AWS SCT on a local machine that has access to AWS\. This machine should be a different one than that the one where you plan to install the DMS Agent\.

### Step 3: Install and Configure the AWS SCT DMS Agent<a name="CHAP_LargeDBs.SBS.InstallAgent"></a>

In this step, you install the DMS Agent on a dedicated machine that has access to AWS and to the machine where AWS SCT was installed\. 

You can install the DMS Agent on the following Linux platforms:
+ Red Hat Enterprise Linux versions 6\.2 through 6\.8, 7\.0 and 7\.1 \(64\-bit\)
+ SUSE Linux version 11\.1 \(64\-bit\)

**To install the DMS Agent**

1. Copy the RPM file called `aws-schema-conversion-tool-dms-agent-2.4.0-R2.x86_64.rpm` from the AWS SCT installation directory to a dedicated Linux machine\.

1. Run the following command as root to install the DMS Agent\. If you install or upgrade the DMS Agent on SUSE Linux, you must add the `--nodeps` parameter to the command\.

   ```
   sudo rpm -i aws-schema-conversion-tool-dms-agent-2.4.0-R2.x86_64.rpm
   ```

   The default installation location is /opt/amazon/aws\-schema\-conversion\-tool\-dms\-agent\. To install the DMS Agent in a non\-default directory, use `--prefix` `<path to new product dir>`\.

1. To verify that the Amazon RDS Migration Server is running, issue the following command\.

   ```
   ps -ef | grep repctl
   ```

   The output of this command should show two processes running\.

To configure the DMS Agent, you must provide a password and port number\. You use the password in AWS SCT, so keep it handy\. The port is the one that the DMS Agent should listen on for AWS SCT connections\. You might have to configure your firewall to allow connectivity\. 

Run the following script to configure the DMS Agent\.

```
sudo /opt/amazon/aws-schema-conversion-tool-dms-agent/bin/configure.sh 
```

### Step 4: Unlock the AWS Snowball Edge Device<a name="w4ab1c33c17c11"></a>

You should run the commands that unlock and provide credentials to the Snowball Edge device from the machine where you installed the DMS Agent\. This way you can be sure that the DMS Agent call connect to the AWS Snowball Edge device\. For more information about unlocking the AWS Snowball Edge device, see [ Unlock the Snowball Edge ](http://docs.aws.amazon.com/snowball/latest/developer-guide/unlockappliance.html)\.

For example, the following command lists the Amazon S3 bucket used by the device\. 

```
aws s3 ls s3://<bucket-name> --profile <Snowball Edge profile> --endpoint http://<Snowball IP>:8080 --recursive
```

### Step 5: Create a New AWS SCT Project<a name="CHAP_LargeDBs.SBS.NewSCTProject"></a>

Next, you create a new AWS SCT project\.

**To create a new project in AWS SCT**

1. Start AWS SCT, and choose **New Project** for **File**\. The **New Project** dialog box appears\.

1. Add the following project information\.     
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_LargeDBs.html)

1. Choose **OK** to create your AWS SCT project\.

1. \(Optional\) Test your connection\. 

### Step 6: Configure the AWS SCT Profile to Work with the DMS Agent<a name="CHAP_LargeDBs.SBS.SCTProfile"></a>

The AWS SCT Profile must be updated to use the DMS Agent\.

**To update the AWS SCT profile to work with the DMS Agent**

1. Start AWS SCT\.

1. Choose **Settings**, and then choose **Global Settings**\. Choose **AWS Service Profiles**\.

1. Choose **Add New AWS Service Profile**\.  
![\[AWS Database Migration Service and AWS Snowball process\]](http://docs.aws.amazon.com/dms/latest/userguide/images/snowball-AWSserviceprofile.png)

1. Add the following profile information\.     
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_LargeDBs.html)

1. After you have entered the information, choose **Test Connection** to verify that AWS SCT can connect to the Amazon S3 bucket\.

   The **OLTP Local & DMS Data Migration** section in the pop\-up window should show all entries with a status of **Pass**\. If the test fails, the failure is probably because the account you are using does not have the correct privileges to access the Amazon S3 bucket\.

1. If the test passes, choose **OK** and then **OK** again to close the window and dialog box\.

### Step 7: Register the DMS Agent in AWS SCT<a name="CHAP_LargeDBs.SBS.RegisterAgent"></a>

Next, you register the DMS Agent in AWS SCT\. SCT then tries to connect to the agent, showing status\. When the agent is available, the status turns to active\.

**To register the DMS Agent**

1. Start AWS SCT, choose **View**, and then choose **Database Migration View \(DMS\)**\.  
![\[View dropdown menu\]](http://docs.aws.amazon.com/dms/latest/userguide/images/snowball-view-databasemigview.png)

1. Choose the **Agent** tab, and then choose **Register**\. The **New Agent Registration** dialog box appears\.  
![\[New Agent Registration dialog box\]](http://docs.aws.amazon.com/dms/latest/userguide/images/snowball-newagentregistration.png)

1. Type your information in the **New Agent Registration** dialog box\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_LargeDBs.html)

1. Choose **Register** to register the agent with your AWS SCT project\.

### Step 8: Install the Source Database Driver for the DMS Agent on the Linux Computer<a name="CHAP_LargeDBs.SBS.SourceDriver"></a>

For the migration to succeed, the DMS Agent must be able to connect to the source database\. To make this possible, you install the database driver for your source database\. The required driver varies by database\.

To restart the DMS Agent after database driver installation, change the working directory to `<product_dir>/bin` and use the steps listed following for each source database\.

```
cd <product_dir>/bin
./arep.ctl stop
./arep.ctl start
```

**To install on Oracle**  
Install Oracle Instant Client for Linux \(x86\-64\) version 11\.2\.0\.3\.0 or later\.   
In addition, if not already included in your system, you need to create a symbolic link in the $ORACLE\_HOME\\lib directory\. This link should be called libclntsh\.so, and should point to a specific version of this file\. For example, on an Oracle 12c client:  

```
lrwxrwxrwx 1 oracle oracle 63 Oct 2 14:16 libclntsh.so ->
                            /u01/app/oracle/home/lib/libclntsh.so.12.1
```
In addition, the LD\_LIBRARY\_PATH environment variable should be appended with the Oracle lib directory and added to the site\_arep\_login\.sh script under the lib folder of the installation\. Add this script if it doesn't exist\.  

```
vi cat <product dir>/bin/site_arep_login.sh
```

```
export ORACLE_HOME=/usr/lib/oracle/12.2/client64; export
                            LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$ORACLE_HOME/lib
```

**To install on Microsoft SQL Server **  
Install the Microsoft ODBC Driver  
Update the site\_arep\_login\.sh with the following code\.  

```
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/opt/microsoft/msodbcsql/lib64/
```
**Simba ODBC Driver **  
 Install the Microsoft ODBC Driver  
 Edit the simba\.sqlserverodbc\.ini file as follows  

```
DriverManagerEncoding=UTF-16
ODBCInstLib=libodbcinst.so
```

**To install on SAP Sybase**  
The SAP Sybase ASE ODBC 64\-bit client should be installed  
If the installation dir is /opt/sap, update the site\_arep\_login\.sh with  

```
export SYBASE_HOME=/opt/sap
export                          
LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$SYBASE_HOME/
   DataAccess64/ODBC/lib:$SYBASE_HOME/DataAccess/ODBC/
   lib:$SYBASE_HOME/OCS-16_0/lib:$SYBASE_HOME/OCS-16_0/
   lib3p64:$SYBASE_HOME/OCS-16_0/lib3p
```
The /etc/odbcinst\.ini should include these entries  

```
[Sybase]
Driver=/opt/sap/DataAccess64/ODBC/lib/libsybdrvodb.so
Description=Sybase ODBC driver
```

**To install on MySQL**  
Install MySQL Connector/ODBC for Linux, version 5\.2\.6 or later   
 Make sure that the /etc/odbcinst\.ini file contains an entry for MySQL, as in the following example  

```
[MySQL ODBC 5.2.6 Unicode Driver]
Driver = /usr/lib64/libmyodbc5w.so 
UsageCount = 1
```

**To install on PostgreSQL**  
Install postgresql94\-9\.4\.4\-1PGDG\.<OS Version>\.x86\_64\.rpm\. This is the package that contains the psql executable\.  
For example, postgresql94\-9\.4\.4\-1PGDG\.rhel7\.x86\_64\.rpm is the package required for Red Hat 7\.  
Install the ODBC driver postgresql94\-odbc\-09\.03\.0400\-1PGDG\.<OS version>\.x86\_64 or above for Linux, where <OS version> is the OS of the agent machine\.  
For example, postgresql94\-odbc\-09\.03\.0400\-1PGDG\.rhel7\.x86\_64 is the client required for Red Hat 7\.  
Make sure that the /etc/odbcinst\.ini file contains an entry for PostgreSQL, as in the following example  

```
[PostgreSQL]
Description = PostgreSQL ODBC driver
Driver = /usr/pgsql-9.4/lib/psqlodbc.so
Setup = /usr/pgsql-9.4/lib/psqlodbcw.so
Debug = 0
CommLog = 1
UsageCount = 2
```

### Step 9: Configure AWS SCT to Access the Amazon S3 Bucket<a name="CHAP_LargeDBs.SBS.ConfigureS3"></a>

For information on configuring an Amazon S3 bucket, see [ Working with Amazon S3 Buckets](http://docs.aws.amazon.com/AmazonS3/latest/dev/UsingBucket.html) in the Amazon S3 documentation\.

**Note**  
To use the resulting Amazon S3 bucket in migration, you must have an AWS DMS replication instance created in the same AWS Region as the S3 bucket\. If you haven't already created one, do so by using the AWS DMS Management Console, as described in [Step 2: Create a Replication Instance](CHAP_GettingStarted.md#CHAP_GettingStarted.ReplicationInstance)\.

### Step 10: Creating a Local & DMS Task<a name="CHAP_LargeDBs.SBS.CreateLocalRemoteTask"></a>

Next, you create the task that is the end\-to\-end migration task\. The task includes two subtasks\. One subtask migrates data from the source database to the AWS Snowball appliance\. The other subtask takes the data that the appliance loads into an Amazon S3 bucket and migrates it to the target database\.

**To create the end\-to\-end migration task**

1. Start AWS SCT, choose **View**, and then choose **Database Migration View \(Local & DMS\)**\.  
![\[View > Database Migration View (Local & DMS)\]](http://docs.aws.amazon.com/dms/latest/userguide/images/snowball-view-databasemigview.png)

1. In the left panel that displays the schema from your source database, choose a schema object to migrate\. Open the context \(right\-click\) menu for the object, and then choose **Create Local & DMS Task**\.  
![\[AWS Database Migration Service and AWS Snowball process\]](http://docs.aws.amazon.com/dms/latest/userguide/images/snowball-contextmenucreatelocal.png)

1. Add your task information\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_LargeDBs.html)

1. Choose **Create** to create the task\.

### Step 11: Running and Monitoring the Local & DMS Task in SCT<a name="CHAP_LargeDBs.SBS.RunMonitorLocalTask"></a>

You can start the Local & DMS Task when all connections to endpoints are successful\. This means all connections for the Local task, which includes connections from the DMS Agent to the source database, the staging Amazon S3 bucket, and the AWS Snowball device, as well as the connections for the DMS task, which includes connections from the staging Amazon S3 bucket to the target database on AWS\.

You can monitor the DMS Agent logs by choosing **Show Log**\. The log details include agent server \(**Agent Log**\) and local running task \(**Task Log**\) logs\. Because the endpoint connectivity is done by the server \(since the local task is not running and there are no task logs\), connection issues are listed under the **Agent Log** tab\.

![\[Local task completed, waiting for the second task\]](http://docs.aws.amazon.com/dms/latest/userguide/images/snowball-tasklog.png)

### Step 12: Manage the AWS Snowball Appliance<a name="CHAP_LargeDBs.SBS.SnowballtoS3"></a>

Once the Snowball appliance is fully loaded, AWS SCT updates the status of the task to show it is 50% complete\. Remember that the other part of the task involves AWS DMS taking the data from Amazon S3 to the target data store\. 

To do so, disconnect the AWS Snowball appliance and ship back to AWS\. For more information about returning the AWS Snowball appliance to AWS, see the steps outlined in [Getting Started with AWS Snowball Edge: Your First Job](http://docs.aws.amazon.com/snowball/latest/developer-guide/common-get-start.html) in the AWS Snowball documentation\. You can use the AWS Snowball console or AWS SCT \(show details of the DMS task\) to check the status of the appliance and find out when AWS DMS begins to load data to the Amazon S3 bucket\.

![\[Local task completed, waiting for the second task\]](http://docs.aws.amazon.com/dms/latest/userguide/images/snowball-twotasksonedone.png)

After the AWS Snowball appliance arrives at AWS and unloads data to S3 bucket, you can see that the remote \(DMS\) task starts to run\. If the migration type you selected for the task was **Migrate existing data**, the status for the DMS task will show 100% complete when the data has been transferred from Amazon S3 to the target data store\. If you set the a task mode to include ongoing replication, then after full load is complete the task status shows that the task continues to run, while AWS DMS applies ongoing changes\.

### Limitations When Working with AWS Snowball and AWS Database Migration Service \(AWS DMS\)<a name="CHAP_LargeDBs.Limitations"></a>

There are some limitations you should be aware of when working with AWS Snowball\.
+ The LOB mode limits LOB file size to 32K\.
+ If an error occurs during the data migration during the load from the local database to the AWS Snowball Edge device or when AWS DMS loads data from Amazon S3 to the target database, the task will restart if the error is recoverable\. If AWS DMS cannot recover from the error, the migration will stop\.
+ Every AWS SCT task creates two endpoint connections on AWS DMS\. If you create multiple tasks, you could reach a resource limit for the number of endpoints that can be created\.