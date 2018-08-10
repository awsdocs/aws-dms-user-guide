# Monitoring AWS DMS Tasks<a name="CHAP_Monitoring"></a>

You can monitor the progress of your task by checking the task status and by monitoring the task's control table\. For more information about control tables, see [Control Table Task Settings](CHAP_Tasks.CustomizingTasks.TaskSettings.ControlTable.md)\.

You can also monitor the progress of your tasks using Amazon CloudWatch\. By using the AWS Management Console, the AWS Command Line Interface \(CLI\), or AWS DMS API, you can monitor the progress of your task and also the resources and network connectivity used\.

Finally, you can monitor the status of your source tables in a task by viewing the table state\.

Note that the "last updated" column the DMS console only indicates the time that AWS DMS last updated the table statistics record for a table\. It does not indicate the time of the last update to the table\.

For more information, see the following topics\.

**Topics**
+ [Task Status](#CHAP_Tasks.Status)
+ [Table State During Tasks](#CHAP_Tasks.CustomizingTasks.TableState)
+ [Monitoring Replication Tasks Using Amazon CloudWatch](#CHAP_Monitoring.CloudWatch)
+ [Data Migration Service Metrics](#CHAP_Monitoring.Metrics)
+ [Managing AWS DMS Task Logs](#CHAP_Monitoring.ManagingLogs)
+ [Logging AWS DMS API Calls with AWS CloudTrail](#logging-using-cloudtrail)

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

![\[AWS Database Migration Service replication instance\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-TableState.png)


| State | Description | 
| --- | --- | 
|   **Table does not exist**   |  AWS DMS cannot find the table on the source endpoint\.   | 
|   **Before load**   |  The full load process has been enabled, but it hasn't started yet\.   | 
|   **Full load**   |  The full load process is in progress\.   | 
|   **Table completed**   |  Full load has completed\.  | 
|   **Table cancelled**   |  Loading of the table has been cancelled\.   | 
|   **Table error**   |  An error occurred when loading the table\.  | 

## Monitoring Replication Tasks Using Amazon CloudWatch<a name="CHAP_Monitoring.CloudWatch"></a>

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

**CDCThroughputBandwidthSource**  
Network bandwidth for the target in KB per second\. CDCThroughputBandwidth records bandwidth on sampling points\. If no network traffic is found, the value is zero\. Because CDC does not issue long\-running transactions, network traffic may not be recorded\.

**CDCThroughputBandwidthTarget**  
Network bandwidth for the target in KB per second\. CDCThroughputBandwidth records bandwidth on sampling points\. If no network traffic is found, the value is zero\. Because CDC does not issue long\-running transactions, network traffic may not be recorded\.

**CDCThroughputRowsSource**  
Incoming task changes from the source in rows per second\.

**CDCThroughputRowsTarget**  
Outgoing task changes for the target in rows per second\.

**CDCLatencySource**  
The gap, in seconds, between the last event captured from the source endpoint and current system time stamp of the AWS DMS instance\. If no changes have been captured from the source due to task scoping, AWS DMS sets this value to zero\.

**CDCLatencyTarget**  
The gap, in seconds, between the first event timestamp waiting to commit on the target and the current timestamp of the AWS DMS instance\. This value occurs if there are transactions that are not handled by target\. Otherwise, target latency is the same as source latency if all transactions are applied\. Target latency should never be smaller than the source latency\.

## Managing AWS DMS Task Logs<a name="CHAP_Monitoring.ManagingLogs"></a>

AWS DMS uses Amazon CloudWatch to log task information during the migration process\. You can use the AWS CLI or the AWS DMS API to view information about the task logs\. To do this, use the `describe-replication-instance-task-logs` AWS CLI command or the AWS DMS API action `DescribeReplicationInstanceTaskLogs`\. 

For example, the following AWS CLI command shows the task log metadata in JSON format\.

```
$ aws dms describe-replication-instance-task-logs \
   --replication-instance-arn arn:aws:dms:us-east-1:237565436:rep:CDSFSFSFFFSSUFCAY
```

A sample response from the command is as follows\.

```
{
    "ReplicationInstanceTaskLogs": [
        {
            "ReplicationTaskArn": "arn:aws:dms:us-east-1:237565436:task:MY34U6Z4MSY52GRTIX3O4AY",
            "ReplicationTaskName": "mysql-to-ddb",
            "ReplicationInstanceTaskLogSize": 3726134
        }
    ],
    "ReplicationInstanceArn": "arn:aws:dms:us-east-1:237565436:rep:CDSFSFSFFFSSUFCAY"
}
```

In this response, there is a single task log \(`mysql-to-ddb`\) associated with the replication instance\. The size of this log is 3,726,124 bytes\.

You can use the information returned by `describe-replication-instance-task-logs` to diagnose and troubleshoot problems with task logs\. For example, if you enable detailed debug logging for a task, the task log will grow quickly—potentially consuming all of the available storage on the replication instance, and causing the instance status to change to `storage-full`\. By describing the task logs, you can determine which ones you no longer need; then you can delete them, freeing up storage space\.

To delete the task logs for a task, set the task setting `DeleteTaskLogs` to true\. For example, the following JSON deletes the task logs when modifying a task using the AWS CLI `modify-replication-task` command or the AWS DMS API `ModifyReplicationTask` action\.

```
{
   "Logging": {
       "DeleteTaskLogs":true
   }
}
```

## Logging AWS DMS API Calls with AWS CloudTrail<a name="logging-using-cloudtrail"></a>

AWS DMS is integrated with AWS CloudTrail, a service that provides a record of actions taken by a user, role, or an AWS service in AWS DMS\. CloudTrail captures all API calls for AWS DMS as events, including calls from the AWS DMS console and from code calls to the AWS DMS APIs\. If you create a trail, you can enable continuous delivery of CloudTrail events to an Amazon S3 bucket, including events for AWS DMS\. If you don't configure a trail, you can still view the most recent events in the CloudTrail console in **Event history**\. Using the information collected by CloudTrail, you can determine the request that was made to AWS DMS, the IP address from which the request was made, who made the request, when it was made, and additional details\. 

To learn more about CloudTrail, see the [AWS CloudTrail User Guide](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/)\.

### AWS DMS Information in CloudTrail<a name="service-name-info-in-cloudtrail"></a>

CloudTrail is enabled on your AWS account when you create the account\. When activity occurs in AWS DMS, that activity is recorded in a CloudTrail event along with other AWS service events in **Event history**\. You can view, search, and download recent events in your AWS account\. For more information, see [Viewing Events with CloudTrail Event History](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/view-cloudtrail-events.html)\. 

For an ongoing record of events in your AWS account, including events for AWS DMS, create a trail\. A trail enables CloudTrail to deliver log files to an Amazon S3 bucket\. By default, when you create a trail in the console, the trail applies to all regions\. The trail logs events from all regions in the AWS partition and delivers the log files to the Amazon S3 bucket that you specify\. Additionally, you can configure other AWS services to further analyze and act upon the event data collected in CloudTrail logs\. For more information, see: 
+ [Overview for Creating a Trail](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-create-and-update-a-trail.html)
+ [CloudTrail Supported Services and Integrations](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-aws-service-specific-topics.html#cloudtrail-aws-service-specific-topics-integrations)
+ [Configuring Amazon SNS Notifications for CloudTrail](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/getting_notifications_top_level.html)
+ [Receiving CloudTrail Log Files from Multiple Regions](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/receive-cloudtrail-log-files-from-multiple-regions.html) and [Receiving CloudTrail Log Files from Multiple Accounts](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-receive-logs-from-multiple-accounts.html)

All AWS DMS actions are logged by CloudTrail and are documented in the [AWS Database Migration Service API Reference](http://docs.aws.amazon.com/dms/latest/APIReference/)\. For example, calls to the `CreateReplicationInstance`, `TestConnection` and `StartReplicationTask` actions generate entries in the CloudTrail log files\. 

Every event or log entry contains information about who generated the request\. The identity information helps you determine the following: 
+ Whether the request was made with root or IAM user credentials\.
+ Whether the request was made with temporary security credentials for a role or federated user\.
+ Whether the request was made by another AWS service\.

For more information, see the [CloudTrail userIdentity Element](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-event-reference-user-identity.html)\.

### Understanding AWS DMS Log File Entries<a name="understanding-service-name-entries"></a>

A trail is a configuration that enables delivery of events as log files to an Amazon S3 bucket that you specify\. CloudTrail log files contain one or more log entries\. An event represents a single request from any source and includes information about the requested action, the date and time of the action, request parameters, and so on\. CloudTrail log files are not an ordered stack trace of the public API calls, so they do not appear in any specific order\. 

The following example shows a CloudTrail log entry that demonstrates the `RebootReplicationInstance` action\.

```
    {
    "eventVersion": "1.05",
    "userIdentity": {
        "type": "AssumedRole",
        "principalId": "AKIAIOSFODNN7EXAMPLE:johndoe",
        "arn": "arn:aws:sts::123456789012:assumed-role/admin/johndoe",
        "accountId": "123456789012",
        "accessKeyId": "ASIAYFI33SINADOJJEZW",
        "sessionContext": {
            "attributes": {
                "mfaAuthenticated": "false",
                "creationDate": "2018-08-01T16:42:09Z"
            },
            "sessionIssuer": {
                "type": "Role",
                "principalId": "AKIAIOSFODNN7EXAMPLE",
                "arn": "arn:aws:iam::123456789012:role/admin",
                "accountId": "123456789012",
                "userName": "admin"
            }
        }
    },
    "eventTime": "2018-08-02T00:11:44Z",
    "eventSource": "dms.amazonaws.com",
    "eventName": "RebootReplicationInstance",
    "awsRegion": "us-east-1",
    "sourceIPAddress": "72.21.198.64",
    "userAgent": "console.amazonaws.com",
    "requestParameters": {
        "forceFailover": false,
        "replicationInstanceArn": "arn:aws:dms:us-east-1:123456789012:rep:EX4MBJ2NMRDL3BMAYJOXUGYPUE"
    },
    "responseElements": {
        "replicationInstance": {
            "replicationInstanceIdentifier": "replication-instance-1",
            "replicationInstanceStatus": "rebooting",
            "allocatedStorage": 50,
            "replicationInstancePrivateIpAddresses": [
                "172.31.20.204"
            ],
            "instanceCreateTime": "Aug 1, 2018 11:56:21 PM",
            "autoMinorVersionUpgrade": true,
            "engineVersion": "2.4.3",
            "publiclyAccessible": true,
            "replicationInstanceClass": "dms.t2.medium",
            "availabilityZone": "us-east-1b",
            "kmsKeyId": "arn:aws:kms:us-east-1:123456789012:key/f7bc0f8e-1a3a-4ace-9faa-e8494fa3921a",
            "replicationSubnetGroup": {
                "vpcId": "vpc-1f6a9c6a",
                "subnetGroupStatus": "Complete",
                "replicationSubnetGroupArn": "arn:aws:dms:us-east-1:123456789012:subgrp:EDHRVRBAAAPONQAIYWP4NUW22M",
                "subnets": [
                    {
                        "subnetIdentifier": "subnet-cbfff283",
                        "subnetAvailabilityZone": {
                            "name": "us-east-1b"
                        },
                        "subnetStatus": "Active"
                    },
                    {
                        "subnetIdentifier": "subnet-d7c825e8",
                        "subnetAvailabilityZone": {
                            "name": "us-east-1e"
                        },
                        "subnetStatus": "Active"
                    },
                    {
                        "subnetIdentifier": "subnet-6746046b",
                        "subnetAvailabilityZone": {
                            "name": "us-east-1f"
                        },
                        "subnetStatus": "Active"
                    },
                    {
                        "subnetIdentifier": "subnet-bac383e0",
                        "subnetAvailabilityZone": {
                            "name": "us-east-1c"
                        },
                        "subnetStatus": "Active"
                    },
                    {
                        "subnetIdentifier": "subnet-42599426",
                        "subnetAvailabilityZone": {
                            "name": "us-east-1d"
                        },
                        "subnetStatus": "Active"
                    },
                    {
                        "subnetIdentifier": "subnet-da327bf6",
                        "subnetAvailabilityZone": {
                            "name": "us-east-1a"
                        },
                        "subnetStatus": "Active"
                    }
                ],
                "replicationSubnetGroupIdentifier": "default-vpc-1f6a9c6a",
                "replicationSubnetGroupDescription": "default group created by console for vpc id vpc-1f6a9c6a"
            },
            "replicationInstanceEniId": "eni-0d6db8c7137cb9844",
            "vpcSecurityGroups": [
                {
                    "vpcSecurityGroupId": "sg-f839b688",
                    "status": "active"
                }
            ],
            "pendingModifiedValues": {},
            "replicationInstancePublicIpAddresses": [
                "18.211.48.119"
            ],
            "replicationInstancePublicIpAddress": "18.211.48.119",
            "preferredMaintenanceWindow": "fri:22:44-fri:23:14",
            "replicationInstanceArn": "arn:aws:dms:us-east-1:123456789012:rep:EX4MBJ2NMRDL3BMAYJOXUGYPUE",
            "replicationInstanceEniIds": [
                "eni-0d6db8c7137cb9844"
            ],
            "multiAZ": false,
            "replicationInstancePrivateIpAddress": "172.31.20.204",
            "patchingPrecedence": 0
        }
    },
    "requestID": "a3c83c11-95e8-11e8-9d08-4b8f2b45bfd5",
    "eventID": "b3c4adb1-e34b-4744-bdeb-35528062a541",
    "eventType": "AwsApiCall",
    "recipientAccountId": "123456789012"
}
```