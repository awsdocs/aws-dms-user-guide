# Monitoring AWS Database Migration Service Tasks<a name="CHAP_Monitoring"></a>

You can monitor the progress of your task by checking the task status and by monitoring the task's control table\. For more information about control tables, see [Control Table Task Settings](CHAP_Tasks.CustomizingTasks.TaskSettings.ControlTable.md)\.

You can also monitor the progress of your tasks using Amazon CloudWatch\. By using the AWS Management Console, the AWS Command Line Interface \(CLI\), or AWS DMS API, you can monitor the progress of your task and also the resources and network connectivity used\.

Finally, you can monitor the status of your source tables in a task by viewing the table state\.

Note that the "last updated" column the DMS console only indicates the time that AWS DMS last updated the table statistics record for a table\. It does not indicate the time of the last update to the table\.

For more information, see the following topics\.


+ [Task Status](#CHAP_Tasks.Status)
+ [Table State During Tasks](#CHAP_Tasks.CustomizingTasks.TableState)
+ [Monitoring Tasks Using Amazon CloudWatch](#CHAP_Monitoring.CloudWatch)
+ [Data Migration Service Metrics](#CHAP_Monitoring.Metrics)
+ [Logging AWS DMS API Calls Using AWS CloudTrail](#CHAP_Monitoring.CloudTrail)

## Task Status<a name="CHAP_Tasks.Status"></a>

The task status indicated the condition of the task\. The following table shows the possible statuses a task can have:


| Task Status | Description | 
| --- | --- | 
|   **Creating**   |  AWS DMS is creating the task\.  | 
|   **Running**   |  The task is performing the migration duties specified\.   | 
|   **Stopped**   |  The task is stopped\.   | 
|   **Stopping**   |  The task is being stopped\. This is usually an indication of user intervention in the task\.  | 
|   **Deleting**   |  The task is being deleted, usually from a request for user intervention\.   | 
|   **Failed**   |  The task has failed\. See the task log files for more information\.  | 
|   **Starting**   |  The task is connecting to the replication instance and to the source and target endpoints\. Any filters and transformations are being applied\.  | 
|   **Ready**   |  The task is ready to run\. This status usually follows the "creating" status\.  | 
|   **Modifying**   |  The task is being modified, usually due to a user action that modified the task settings\.  | 

The task status bar gives an estimation of the task's progress\. The quality of this estimate depends on the quality of the source database’s table statistics; the better the table statistics, the more accurate the estimation\. For tasks with only one table that has no estimated rows statistic, we are unable to provide any kind of percentage complete estimate\. In this case, the task state and the indication of rows loaded can be used to confirm that the task is indeed running and making progress\.

## Table State During Tasks<a name="CHAP_Tasks.CustomizingTasks.TableState"></a>

The AWS DMS console updates information regarding the state of your tables during migration\. The following table shows the possible state values:

![\[ AWS Database Migration Service replication instance\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-TableState.png)


| State | Description | 
| --- | --- | 
|   **Table does not exist**   |  AWS DMS cannot find the table on the source endpoint\.   | 
|   **Before load**   |  The full load process has been enabled, but it hasn't started yet\.   | 
|   **Full load**   |  The full load process is in progress\.   | 
|   **Table completed**   |  Full load has completed\.  | 
|   **Table cancelled**   |  Loading of the table has been cancelled\.   | 
|   **Table error**   |  An error occurred when loading the table\.  | 

## Monitoring Tasks Using Amazon CloudWatch<a name="CHAP_Monitoring.CloudWatch"></a>

You can use Amazon CloudWatch alarms or events to more closely track your migration\. For more information about Amazon CloudWatch, see [What Are Amazon CloudWatch, Amazon CloudWatch Events, and Amazon CloudWatch Logs?](http://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/WhatIsCloudWatch.html) in the Amazon CloudWatch User Guide\. Note that there is a charge for using Amazon CloudWatch\.

The AWS DMS console shows basic CloudWatch statistics for each task, including the task status, percent complete, elapsed time, and table statistics, as shown following\. Select the replication task and then select the **Task monitoring** tab\.

![\[AWS DMS monitoring\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-monitoring1.png)

The AWS DMS console shows performance statistics for each table, including the number of inserts, deletions, and updates, when you select the **Table statistics** tab\.

![\[AWS DMS monitoring\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-monitoring3.png)

In addition, if you select a replication instance from the **Replication Instance** page, you can view performance metrics for the instance by selecting the **Monitoring** tab\.

![\[AWS DMS monitoring\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-monitoring4.png)

## Data Migration Service Metrics<a name="CHAP_Monitoring.Metrics"></a>

AWS DMS provides statistics for the following: 

+ **Host Metrics** – Performance and utilization statistics for the replication host, provided by Amazon CloudWatch\. For a complete list of the available metrics, see [Replication Instance Metrics](#CHAP_Monitoring.Metrics.CloudWatch)\.

+ **Replication Task Metrics** – Statistics for replication tasks including incoming and committed changes, and latency between the replication host and both the source and target databases\. For a complete list of the available metrics, see [Replication Task Metrics](#CHAP_Monitoring.Metrics.Task)\.

+ **Table Metrics** – Statistics for tables that are in the process of being migrated, including the number of insert, update, delete, and DDL statements completed\.

Task metrics are divided into statistics between the replication host and the source endpoint, and statistics between the replication host and the target endpoint\. You can determine the total statistic for a task by adding two related statistics together\. For example, you can determine the total latency, or replica lag, for a task by combining the **CDCLatencySource** and **CDCLatencyTarget** values\.

Task metric values can be influenced by current activity on your source database\. For example, if a transaction has begun, but has not been committed, then the **CDCLatencySource** metric continues to grow until that transaction has been committed\.

For the replication instance, the **FreeableMemory** metric requires clarification\. Freeable memory is not a indication of the actual free memory available\. It is the memory that is currently in use that can be freed and used for other uses; it's is a combination of buffers and cache in use on the replication instance\. 

While the **FreeableMemory** metric does not reflect actual free memory available, the combination of the **FreeableMemory** and **SwapUsage** metrics can indicate if the replication instance is overloaded\.

Monitor these two metrics for the following conditions\.

 • The **FreeableMemory** metric approaching zero\.

 • The **SwapUsage** metric increases or fluctuates\.

If you see either of these two conditions, they indicate that you should consider moving to a larger replication instance\. You should also consider reducing the number and type of tasks running on the replication instance\. Full Load tasks require more memory than tasks that just replicate changes\.

### Replication Instance Metrics<a name="CHAP_Monitoring.Metrics.CloudWatch"></a>

Replication instance monitoring include Amazon CloudWatch metrics for the following statistics:

**CPUUtilization**  
 The amount of CPU used\.  
 Units: Bytes 

**FreeStorageSpace**  
 The amount of available storage space\.  
Units: Bytes

**FreeableMemory**  
 The amount of available random access memory\.  
Units: Bytes

**WriteIOPS**  
 The average number of disk I/O operations per second\.  
Units: Count/Second

**ReadIOPS**  
 The average number of disk I/O operations per second\.  
Units: Count/Second

**WriteThroughput**  
 The average number of bytes written to disk per second\.  
Units: Bytes/Second

**ReadThroughput**  
 The average number of bytes read from disk per second\.  
Units: Bytes/Second

**WriteLatency**  
 The average amount of time taken per disk I/O operation\.  
Units: Seconds

**ReadLatency**  
 The average amount of time taken per disk I/O operation\.  
Units: Seconds

**SwapUsage**  
 The amount of swap space used on the replication instance\.  
Units: Bytes

**NetworkTransmitThroughput**  
 The outgoing \(Transmit\) network traffic on the replication instance, including both customer database traffic and AWS DMS traffic used for monitoring and replication\.  
Units: Bytes/second

**NetworkReceiveThroughput**  
 The incoming \(Receive\) network traffic on the replication instance, including both customer database traffic and AWS DMS traffic used for monitoring and replication\.  
Units: Bytes/second

### Replication Task Metrics<a name="CHAP_Monitoring.Metrics.Task"></a>

Replication task monitoring includes metrics for the following statistics:

**FullLoadThroughputBandwidthSource**  
Incoming network bandwidth from a full load from the source in kilobytes \(KB\) per second\.

**FullLoadThroughputBandwidthTarget**  
Outgoing network bandwidth from a full load for the target in KB per second\.

**FullLoadThroughputRowsSource**  
Incoming changes from a full load from the source in rows per second\.

**FullLoadThroughputRowsTarget**  
Outgoing changes from a full load for the target in rows per second\.

**CDCIncomingChanges**  
The total number of change events at a point\-in\-time that are waiting to be applied to the target\. Note that this is not the same as a measure of the transaction change rate of the source endpoint\. A large number for this metric usually indicates AWS DMS is unable to apply captured changes in a timely manner, thus causing high target latency\.

**CDCChangesMemorySource**  
Amount of rows accumulating in a memory and waiting to be committed from the source\.

**CDCChangesMemoryTarget**  
Amount of rows accumulating in a memory and waiting to be committed to the target\.

**CDCChangesDiskSource**  
Amount of rows accumulating on disk and waiting to be committed from the source\.

**CDCChangesDiskTarget**  
Amount of rows accumulating on disk and waiting to be committed to the target\. 

**CDCThroughputBandwidthTarget**  
Outgoing task network bandwidth for the target in KB per second\.

**CDCThroughputRowsSource**  
Incoming task changes from the source in rows per second\.

**CDCThroughputRowsTarget**  
Outgoing task changes for the target in rows per second\.

**CDCLatencySource**  
The gap, in seconds, between the last event captured from the source endpoint and current system time stamp of the AWS DMS instance\. If no changes have been captured from the source due to task scoping, AWS DMS sets this value to zero\.

**CDCLatencyTarget**  
The gap, in seconds, between the last event applied on the target and the current system timestamp of the AWS DMS instance\. Target latency should never be smaller than the source latency\.

## Logging AWS DMS API Calls Using AWS CloudTrail<a name="CHAP_Monitoring.CloudTrail"></a>

The AWS CloudTrail service logs all AWS Database Migration Service \(AWS DMS\) API calls made by or on behalf of your AWS account\. AWS CloudTrail stores this logging information in an S3 bucket\. You can use the information collected by CloudTrail to monitor AWS DMS activity, such as creating or deleting a replication instance or an endpoint\. For example, you can determine whether a request completed successfully and which user made the request\. To learn more about CloudTrail, see the [AWS CloudTrail User Guide](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/)\.

If an action is taken on behalf of your AWS account using the AWS DMS console or the AWS DMS command line interface, then AWS CloudTrail logs the action as calls made to the AWS DMS API\. For example, if you use the AWS DMS console to describe connections, or call the AWS CLI [describe\-connections](http://docs.aws.amazon.com//cli/latest/reference/dms/describe-connections.html) command, then the AWS CloudTrail log shows a call to the AWS DMS API [DescribeConnections](http://docs.aws.amazon.com/dms/latest/APIReference//API_DescribeConnections.html) action\. For a list of the AWS DMS API actions that are logged by AWS CloudTrail, see the [AWS DMS API Reference](http://docs.aws.amazon.com/dms/latest/APIReference//Welcome.html)\. 

### Configuring CloudTrail Event Logging<a name="USER_Auditing.CloudTrail"></a>

CloudTrail creates audit trails in each region separately and stores them in an S3 bucket\. You can configure CloudTrail to use Amazon Simple Notification Service \(Amazon SNS\) to notify you when a log file is created, but that is optional\. CloudTrail will notify you frequently, so we recommend that you use Amazon SNS with an Amazon Simple Queue Service \(Amazon SQS\) queue and handle notifications programmatically\.

You can enable CloudTrail using the AWS Management Console, CLI, or API\. When you enable CloudTrail logging, you can have the CloudTrail service create an S3 bucket for you to store your log files\. For details, see [Creating and Updating Your Trail](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/setupyourtrail.html) in the *AWS CloudTrail User Guide*\. The *AWS CloudTrail User Guide* also contains information on how to [aggregate CloudTrail logs from multiple regions into a single S3 bucket](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/aggregatinglogs.html)\.

There is no cost to use the CloudTrail service\. However, standard rates for S3 usage apply, and also rates for Amazon SNS usage should you include that option\. For pricing details, see the [S3](http://aws.amazon.com/s3/pricing/) and [Amazon SNS](http://aws.amazon.com/sns/pricing/) pricing pages\.

### AWS Database Migration Service Event Entries in CloudTrail Log Files<a name="USER_Auditing.CloudTrail_Events"></a>

CloudTrail log files contain event information formatted using JSON\. An event record represents a single AWS API call and includes information about the requested action, the user that requested the action, the date and time of the request, and so on\. 

CloudTrail log files include events for all AWS API calls for your AWS account, not just calls to the AWS DMS API\. However, you can read the log files and scan for calls to the AWS DMS API using the `eventName` element\.

For more information about the different elements and values in CloudTrail log files, see [CloudTrail Event Reference](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/eventreference.html) in the *AWS CloudTrail User Guide*\.

You might also want to make use of one of the Amazon partner solutions that integrate with CloudTrail to read and analyze your CloudTrail log files\. For options, see the [AWS partners](http://aws.amazon.com/cloudtrail/partners/) page\.