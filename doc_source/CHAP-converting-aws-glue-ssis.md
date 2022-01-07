# Converting SSIS to AWS Glue using AWS SCT<a name="CHAP-converting-aws-glue-ssis"></a>

Following, you can find how to convert Microsoft SQL Server Integration Services \(SSIS\) packages to AWS Glue using AWS SCT\. 

To convert Microsoft SSIS packages to AWS Glue, make sure that you use AWS SCT version 1\.0\.642 or later\. You also need to have an SSIS project with ETL packages – `.dtsx`, `.conmgr`, and `.params` files in the local folder\.

You don't need an installed SSIS server\. The conversion process goes through the local SSIS files\.

**To convert an SSIS package to AWS Glue using AWS SCT**

1. Create a new project in AWS SCT or open an existing project\. For more information, see [Creating an AWS SCT project](CHAP_UserInterface.md#CHAP_UserInterface.Project)\. 

1. Choose **Add source** from the menu to add a new source SSIS package to your project\. 

1.  Choose **SQL Server Integration Services** and complete the following: 
   + **Connection name** – Enter the name for your connection\. AWS SCT displays this name in the metadata tree\.
   + **SSIS packages folder** – Choose the path to your SSIS project folder with packages\.

   AWS SCT reads the project files \(files with the extensions `.dtsx`, `.conmgr` or `.params`\) from the local folder, parses them, and organizes them into an AWS SCT tree of categories\.

1. Choose **Add target** from the menu to add a new target platform to convert your source SSIS packages\. 

1.  Choose **AWS Glue** and complete the following: 
   + **Connection name** – Enter the name for your connection\. AWS SCT displays this name in the metadata tree\.
   + **Copy from AWS profile** – Choose the profile to use\.
   + **AWS access key** – Enter your AWS access key\.
   + **AWS secret key** – Enter your AWS secret key\.
   + **Region** – Choose the AWS Region that you want to use from the list\.
   + **Amazon S3 bucket folder** – Enter the folder path for the Amazon S3 bucket that you plan to use\.

    You can use a virtual AWS Glue target\. In this case, you don't need to specify the connection credentials\. For more information, see [Using virtual targets](CHAP_Mapping.VirtualTargets.md) 

1. Create a new mapping rule that includes your source SSIS package and your AWS Glue target\. For more information, see [Adding a new mapping rule](CHAP_Mapping.New.md) 

1. In the SSIS tree view, open the context \(right\-click\) menu for **Connection managers**, and then choose **Configure connections**\.

   To configure a connection mapping for SSIS connection managers, specify the AWS Glue connection for the corresponding SSIS connection manager\. Make sure that your AWS Glue connections are already created\. 

   1. Configure the project connection manager:

      1. Under **Connections**, choose **Project connections**\.

      1. For **Glue catalog connection**, choose the appropriate AWS Glue connection\.

   1. Configure the package connection manager:

      1. Under **Connections**, choose your package\.

      1. For **Glue catalog connection**, choose the appropriate AWS Glue connection\.

      1. Repeat these actions for all connections available for your package\.

   1. Choose **Apply**\.

1. Convert your package\. In the source tree view, find **Packages**\. Open the context \(right\-click\) menu for your package, then choose **Convert package**\.

1. Save the converted script to Amazon S3\. In the target tree view, find **Package scripts**\. Open the context \(right\-click\) menu for your converted script, then choose **Save to S3**\.

1. Configure your AWS Glue job\. In the target tree view, find **Package scripts**\. Open the context \(right\-click\) menu for your converted script, then choose **Configure AWS Glue job**\.

1. Complete the three configuration sections as shown following:

   1. Design data flow
      + **Execution strategy** – Choose how your job will run ETL scripts\. Choose **SEQUENTIAL** to run the scripts in the order that is specified in the wizard\. Choose **PARALLEL** to run the scripts in parallel, disregarding the order that is specified in the wizard\.
      + **Scripts** – Choose the name of your converted script\.
      + Choose **Next**\.

   1. Job properties
      + **Name** – Enter the name of your AWS Glue job\.
      + **IAM Role** – Choose the IAM role that is used for authorization to resources used to run the job and access data stores\.
      + **Script file name** – Enter the name of your converted script\.
      + **Script file S3 path** – Enter the Amazon S3 path to your converted script\.
      + **Encrypt script using SSE\-S3** – Choose this option to protect data using server\-side encryption with Amazon S3\-managed encryption keys \(SSE\-S3\)\.
      + **Temporary directory** – Enter the Amazon S3 path to a temporary directory for intermediate results\. AWS Glue and AWS Glue built\-in transforms use this directory to read or write to Amazon Redshift\.
      + AWS SCT automatically generates the path for Python libraries\. You can review this path in **Generated python library path**\. You can't edit this automatically generated path\. To use additional Python libraries, enter the path in **User python library path**\. 
      + **User python library path** – Enter the paths for additional user python libraries\. Separate Amazon S3 paths with commas\.
      + **Dependent jars path** – Enter the paths for dependent jar files\. Separate Amazon S3 paths with commas\.
      + **Referenced files path** – Enter the paths for additional files, such as configuration files, that are required by your script\. Separate Amazon S3 paths with commas\.
      + **Maximum capacity** – Enter the maximum number of AWS Glue data processing units \(DPUs\) that can be allocated when this job runs\. You can enter an integer from 2 to 100\. The default is 2\.
      + **Max concurrency** – Enter the maximum number of concurrent runs that are allowed for this job\. The default is 1\. AWS Glue returns an error when this threshold is reached\.
      + **Job timeout \(minutes\)** – Enter the timeout value on your ETL job as a safeguard against runaway jobs\. The default is 2880 minutes \(48 hours\) for batch jobs\. If the job exceeds this limit, the job run state changes to `TIMEOUT`\.
      + **Delay notification threshold \(minutes\)** – Enter the threshold in minutes before AWS SCT sends a delay notification\.
      + **Number of retries** – Enter the number of times \(0–10\) that AWS Glue should automatically restart the job if it fails\. Jobs that reach the timeout limit aren't restarted\. The default is 0\.
      + Choose **Next**\.

   1. Configure the required connections: 

      1. From **All connections**, choose the required AWS Glue connections and add them to the list of **Selected connections**\.

      1. Choose **Finish**\.

1. Create a configured AWS Glue job\. In the target tree view, find and expand **ETL Jobs**\. Open the context \(right\-click\) menu for ETL job that you configured and then choose **Create AWS Glue Job**\.

1. Run the AWS Glue job:

   1. Open the AWS Glue console at [https://console\.aws\.amazon\.com/glue/](https://console.aws.amazon.com/glue/)\.

   1. In the navigation pane, choose **Jobs**\.

   1. Choose **Add job**, then choose the job that you want to run\.

   1. On the **Actions** tab, choose **Run job**\.