# Step 10: Run and monitor the task in SCT<a name="CHAP_LargeDBs.SBS.run-and-monitor-task-in-sct"></a>

You can start the migration task when connections to all endpoints are successful, including the following:
+ AWS DMS Agent connections to these:
  + The source database
  + The staging Amazon S3 bucket
  + The Edge device
+ AWS DMS Task connections to these:
  + The source database
  + The staging Amazon S3 bucket
  + The target database on AWS

If all connections are functioning properly, your SCT console resembles the following screen shot, and you are ready to begin\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/dms/latest/userguide/images/snowball-sct-connections.png)

Use the following procedure to start the migration\.

**To start the migration task**

1. Choose the migration task, then choose **Start**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/dms/latest/userguide/images/snowball-start-migration.png)

1. To monitor the AWS DMS Agent, choose **Show Log**\. The log details include the agent server \(**Agent Log**\) and local running task \(**Task Log**\) logs\. The endpoint connectivity is done by the server\. Because the local task isn't running, it has no task logs\. Connection issues are listed under the **Agent Log **tab\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/dms/latest/userguide/images/snowball-show-agent-log.png)

1. Verify that the status of the migration task is 50 percent\. You can use the Snowball Edge console or AWS SCT to check the status of the device\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/dms/latest/userguide/images/snowball-check-status.png)

   After the source tables have been loaded onto the Snowball Edge appliance, AWS SCT updates the status of the task to show it is 50 percent complete\. This is because the other half of the task involves AWS DMS taking the data from Amazon S3 to the target data store\.

1. Follow the steps outlined in the Snowball Edge documentation, starting with the section named [Stop the Snowball client, and power off the Snowball Edge](https://docs.aws.amazon.com/snowball/latest/developer-guide/turnitoff.html)\. These steps include the following:
   + Stopping the Snowball Edge client
   + Powering off the Edge device
   + Returning the Edge device to AWS

1. Finishing the migration after the device returns to AWS involves waiting for the remote task to complete\.

   When the Snowball Edge appliance arrives at AWS, the remote \(DMS\) task starts to run\. If the migration type you chose was **Migrate existing data**, the status for the AWS DMS task show 100 percent complete after the data has been transferred from Amazon S3 to the target data store\. 

   If you set the task mode to include ongoing replication, then after the full load is complete the task continues to run while AWS DMS applies ongoing changes\.