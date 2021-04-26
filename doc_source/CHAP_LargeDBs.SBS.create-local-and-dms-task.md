# Step 9: Create a local and AWS DMS task<a name="CHAP_LargeDBs.SBS.create-local-and-dms-task"></a>

Next, you create the task that is the end\-to\-end migration task\. This task includes two subtasks: 
+ The local subtask – This task migrates data from the source database to the Snowball Edge appliance\. 
+ The AWS DMS subtask – This task moves the data from the appliance into an Amazon S3 bucket and migrates it to the target database\.

**Note**  
We recommend that you test your migration before you use the Snowball Edge device\. You can do this by setting up a task to send data, such as a single table, to an Amazon S3 bucket instead of to the Snowball Edge device\.

**To create the end\-to\-end migration task**

1. Start AWS SCT, choose **View**, and then choose **Database Migration View \(Local & DMS\)**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/dms/latest/userguide/images/snowball-localanddmstask.png)

1. In the left panel that displays the schema from your source database, choose a schema to migrate\. Open the context \(right\-click\) menu for the schema, and then choose **Create Local & DMS Task**\.

   You can't migrate individual tables using AWS DMS and Snowball Edge\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/dms/latest/userguide/images/snowball-contextmenucreatelocal.png)

   The following screen appears\.   
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/dms/latest/userguide/images/snowball-createlocalanddmstask.png)

1. Add your task information\.     
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_LargeDBs.SBS.create-local-and-dms-task.html)

1. Choose **Create** to create the task\.