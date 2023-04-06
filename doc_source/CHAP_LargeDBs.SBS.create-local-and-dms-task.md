# Step 9: Create a local and AWS DMS task<a name="CHAP_LargeDBs.SBS.create-local-and-dms-task"></a>

Next, you create the task that is the end\-to\-end migration task\. This task includes two subtasks: 
+ The local subtask – This task migrates data from the source database to the AWS Snowball Edge appliance\. 
+ The AWS DMS subtask – This task moves the data from the appliance into an Amazon S3 bucket and migrates it to the target database\.

**Note**  
We recommend that you test your migration before you use the AWS Snowball Edge device\. You can do this by setting up a task to send data, such as a single table, to an Amazon S3 bucket instead of to the AWS Snowball Edge device\.

**To create the end\-to\-end migration task**

1. Start AWS SCT, choose **View**, and then choose **Data migration view \(other\)**\.

1. In the left panel that displays the schema from your source database, choose a schema object to migrate\. Open the context \(right\-click\) menu for the object, and then choose **Create Local & DMS task**\.

1. For **Task name**, enter a descriptive name for your data migration task\.

1. For **Migration mode**, choose **Extract, upload, and copy**\.

1. Choose **Amazon S3 settings**\.

1. Select **Use Snowball**\.

1. Enter folders and subfolders in your Amazon S3 bucket where the data extraction agent can store data\.

1. Choose **Create** to create the task\.