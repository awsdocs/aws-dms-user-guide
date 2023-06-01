# AWS DMS Serverless components<a name="CHAP_Serverless.Components"></a>

To manage the resources needed to perform a replication, AWS DMS Serverless has granular states that reveal different internal actions taken by the service\. When you start the replication, AWS DMS Serverless calculates the capacity load, provisions the calculated capacity, and starts the data replication according to the following replication states\.

The following diagram shows the state transitions for an AWS DMS Serverless replication\.

![\[AWS DMS Serverless replication states\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-replicationstate.png)
+ The first state after you start the replication is **Initializing**\. In this state, all the required parameters are initialized\.
+ The states immediately following include ** Preparing Metadata Resources**, **Testing Connection**, and **Fetching Metadata**\. In these states, AWS DMS Serverless connects to your source database to obtain the information needed to predict the capacity needed\. 
  + When the replication state is **Testing Connection**, AWS DMS Serverless verifies that the connection to your source and target databases are set up successfully\. 
  + The replication state following **Testing Connection** is **Fetching Metadata**\. Here, AWS DMS retrieves the information needed to calculate capacity\. 
  + Once AWS DMS retrieves the necessary information, the next state is **Calculating Capacity**\. Here, the system calculates the size of underlying resources required to perform the replication\. 
+ The state transition following **Calculating Capacity** is **Provisioning Capacity**\. While the replication is in this state, AWS DMS Serverless initializes the underlying compute resources\. 
+ The replication state after all resources are successfully provisioned is **Replication Starting**\. In this state, AWS DMS Serverless begins the replication of data\.
+ The final state is **Running**\. In the **Running** state, the replication of data is ongoing\.

**Topics**
+ [Supported Engine Versions](#CHAP_Serverless.SupportedVersions)
+ [Creating a serverless replication](#CHAP_Serverless.create)
+ [Modifying AWS DMS serverless replications](#CHAP_Serverless.modify)
+ [Compute Config](#CHAP_Serverless.computeconfig)
+ [Understanding autoscaling in AWS DMS serverless](#CHAP_Serverless.autoscaling)
+ [Monitoring AWS DMS serverless replications](#CHAP_Serverless.monitoring)

For AWS DMS Serverless, the left\-hand navigation panel of the AWS DMS console has a new option, **Serverless replications**\. For **Serverless Replications**, you specify *Replications* instead of replication instance types or tasks to define a replication\. In addition, you specify the maximum and minimum DMS capacity units \(DCUs\) that you want DMS to provision for the replication\. A DCU is 2GB of RAM\. AWS DMS bills your account for each DCU that your replication is currently using\. For information about AWS DMS pricing, see [AWS Database Migration Service pricing](https://aws.amazon.com/dms/pricing/)\.

AWS DMS then automatically provisions replication resources based on your table mappings and the predicted size of your workload\. This capacity unit is a value in the range of the minimum and maximum capacity unit values that you specify\.

## Supported Engine Versions<a name="CHAP_Serverless.SupportedVersions"></a>

With AWS DMS Serverless, you do not need to choose and manage engine versions, as the service handles that setting\. AWS DMS Serverless supports the following sources:
+ Microsoft SQL Server
+ PostgreSQL\-compatible databases
+ MySQL\-compatible databases
+ Oracle

AWS DMS Serverless supports the following targets:
+ Microsoft SQL Server
+ PostgreSQL
+ MySQL\-compatible databases
+ Oracle
+ Amazon S3
+ Amazon Redshift
+ Amazon DynamoDB
+ Amazon Kinesis Data Streams
+ Amazon Managed Streaming for Apache Kafka
+ Amazon OpenSearch Service
+ Amazon DocumentDB \(with MongoDB compatibility\)
+ Amazon Neptune



As part of AWS DMS Serverless, you have access to console commands that allow you to create, configure, start, and manage AWS DMS serverless replications\. To run these  commands using the **Serverless replications** section of the console, you need to do one of the following:
+ Set up a new AWS Identity and Access Management \(IAM\) policy and IAM role to attach that policy to\.
+ Use an AWS CloudFormation template to provide the access that you need\.

AWS DMS Serverless requires a service linked role \(SLR\) to exist in your account\. AWS DMS manages the creation and usage of this role\. For more information about making sure that you have the necessary SLR, see [Service\-linked role for AWS DMS Serverless](slr-services-sl.md)\.

## Creating a serverless replication<a name="CHAP_Serverless.create"></a>

To create a serverless replication between two existing AWS DMS endpoints, do the following\. For information about creating AWS DMS endpoints, see [Creating source and target endpoints](CHAP_Endpoints.Creating.md)\.

**Creating a serverless replication**

1. Sign in to the AWS Management Console and open the AWS DMS console at [https://console\.aws\.amazon\.com/dms/v2/](https://console.aws.amazon.com/dms/v2/)\.

1. On the navigation pane, choose **Serverless replications**, and then choose **Create replication**\.

1. On the **Create replication** page, specify your serverless replication configuration:    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Serverless.Components.html)

   In the **Settings** section, set the settings that your replication requires\.

   In the **Table mappings** section, set up table mapping to define rules to select and filter data that you are replicating\. Before you specify your mapping, make sure that you review the documentation section on data type mapping for your source and your target database\.

   In the **Compute settings** section, set the following settings\. For information about Compute Config settings, see [Compute Config](#CHAP_Serverless.computeconfig)\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Serverless.Components.html)

   Leave the **Maintenance** settings as they are\.

1. Choose **Create replication**\.

AWS DMS creates a serverless replication to perform your migration\.

## Modifying AWS DMS serverless replications<a name="CHAP_Serverless.modify"></a>

To modify your replication configuration, use the `modify-replication-config` action\. You can only modify an AWS DMS replication configuration that is in the `CREATED`, `STOPPED`, or `FAILED` states\. 

**To modify a serverless replication configuration by using the AWS Management Console**

1. Sign in to the AWS Management Console and open the AWS DMS console at [https://console\.aws\.amazon\.com/dms/v2/](https://console.aws.amazon.com/dms/v2/)\.

1. In the navigation pane, choose **Serverless replications**\.

1. Choose the replication you want to modify\. The following table describes the modifications you can make based on the current state of the replication\.     
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Serverless.Components.html)

## Compute Config<a name="CHAP_Serverless.computeconfig"></a>

You configure your replication provisioning using the Compute Config parameter or console section\. The fields in the Compute Config object include the following:


| Option | Description | 
| --- | --- | 
|   **MinCapacityUnits**   | This is the minimum number of DMS Capacity Units \(DCU\) that AWS DMS will provision\. This is also the minimum DCU that autoscaling could scale down to\.  | 
|   **MaxCapacityUnits**   | This is the the maximum DMS Capacity Units \(DCU\) that AWS DMS can provision, depending on your replication's capacity prediction\. This is also the maximum DCU that autoscaling could scale up to\.  | 
|   **KmsKeyId**   | The encryption key to use to encrypt replication storage and connection information\. If you choose \(Default\) aws/dms, AWS DMS uses the default KMS key associated with your account and AWS Region\. A description and your account number are shown, along with the key's ARN\. For more information about using the encryption key, see [Setting an encryption key and specifying AWS KMS permissions](CHAP_Security.md#CHAP_Security.EncryptionKey)\. For this tutorial, leave \(Default\) aws/dms chosen\.  | 
|   **ReplicationSubnetGroupId**   | The replication subnet group in your selected VPC where you want the replication to be created\. If your source database is in a VPC, choose the subnet group that contains the source database as the location for your replication\. For more information about replication subnet groups, see [ Creating a replication subnet group](CHAP_ReplicationInstance.VPC.md#CHAP_ReplicationInstance.VPC.Subnets)\.  | 
|   **VpcSecurityGroupIds**   | The replication instance is created in a VPC\. If your source database is in a VPC, choose the VPC security group that provides access to the DB instance where the database is located\.  | 
|   **PreferredMaintenanceWindow**   | This parameter defines a weekly time range during which system maintenance can occur, in Universal Coordinated Time \(UTC\)\. The default is a 30\-minute window selected at random from an 8\-hour block of time per AWS Region, occurring on a random day of the week\.  | 
|   **MultiA**   | Setting this optional parameter creates a standby replica of your replication in another Availability Zone for failover support\. If you intend to use change data capture \(CDC\) or ongoing replication, we recommend that you turn on this option\.  | 

## Understanding autoscaling in AWS DMS serverless<a name="CHAP_Serverless.autoscaling"></a>

After you provision a replication and it is in the `RUNNING` state, the AWS DMS service manages the capacity of the underlying resources to adapt to changing workloads\. This management scales replication resources based on the following replication settings:
+ `MinCapacityUnits`
+ `MaxCapacityUnits`

Replications scale up after a period of exceeding an upper utilization threshold, and down when capacity utilization is below a minimum capacity utilization threshold for a longer period\.

### Tuning autoscaling in AWS DMS serverless<a name="CHAP_Serverless.autoscaling.tuning"></a>

To tune your replication autoscaling parameters, we recommend that you set `MaxCapacityUnits` to the maximum value, and let AWS DMS manage provisioning of resources\. It is recommended that you choose the largest DCU maximum capacity setting to allow the greatest benefit from auto\-scaling, to accommodate spikes in transaction volume\. The pricing calculator shows the maximum monthly cost if your replication continuously uses the maximum DCU\. The maximum DCU does not represent the actual cost, as you only pay for the capacity used\.z 

If your replication is not using its resources at full capacity, AWS DMS will gradually deprovision resources to save you costs\. However, since provisioning and deprovisioning resources takes time, we recommend that you set your `MinCapacityUnits` setting to a value that can handle any sudden spikes you expect in your replication workload\. This will keep your replication from being under\-provisioned while AWS DMS provisions resources for the higher workload level\. 

If you under\-provision your replication, you might see your `CapacityUtilization` and `CPUUtilization` metrics consistently at their maximum values\. This can cause your replication to fail\. If your replication fails due to under\-provisioned resources, AWS DMS creates an out\-of\-memory event in your replication logs\. 

## Monitoring AWS DMS serverless replications<a name="CHAP_Serverless.monitoring"></a>

AWS provides several tools for monitoring your AWS DMS serverless replications, and responding to potential incidents:
+ [AWS DMS serverless replication metrics](#CHAP_Serverless.monitoring.metrics)
+ [AWS DMS serverless replication logs](#CHAP_Serverless.monitoring.logs)

### AWS DMS serverless replication metrics<a name="CHAP_Serverless.monitoring.metrics"></a>

Serverless replication monitoring includes Amazon CloudWatch metrics for the following statistics\. These statistics are grouped by each serverless replication\.


|  Metric  |  Units  |  Description  | 
| --- | --- | --- | 
| CapacityUtilization | Percent |  The percentage of memory used by the serverless replication  | 
| CDCIncomingChanges | Percent |  The total number of change events at a point\-in\-time that are waiting to be applied to the target\. Note that this is not the same as a measure of the transaction change rate of the source endpoint\. A large number for this metric usually indicates AWS DMS is unable to apply captured changes in a timely manner, thus causing high target latency\.  | 
| CDCLatencySource | Seconds |  The gap, in seconds, between the last event captured from the source endpoint and current system time stamp of the AWS DMS instance\. CDCLatencySource represents the latency between source and replication instance\. High CDCLatencySource means the process of capturing changes from source is delayed\. To identify latency in an ongoing replication, you can view this metric together with CDCLatencyTarget\. If both CDCLatencySource and CDCLatencyTarget are high, investigate CDCLatencySource first\. CDCLatencySource can be 0 when there is no replication lag between the source and the replication\. CDCLatencySource can also become zero when the replication attempts to read the next event in the source's transaction log and there are no new events compared to the last time it read from the source\. When this happens, the replication resets the CDCLatencySource to 0\.  | 
| CDCLatencyTarget | Seconds |  The gap, in seconds, between the first event timestamp waiting to commit on the target and the current timestamp of the AWS DMS instance\. Target latency is the difference between the replication instance server time and the oldest unconfirmed event id forwarded to a target component\. In other words, target latency is the timestamp difference between the replication instance and the oldest event applied but unconfirmed by TRG endpoint \(99%\)\. When CDCLatencyTarget is high, it indicates the process of applying change events to the target is delayed\. To identify latency in an ongoing replication, you can view this metric together with CDCLatencySource\. If CDCLatencyTarget is high but CDCLatencySource isn’t high, investigate if: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Serverless.Components.html)  | 
| CDCThroughputBandwidthTarget | KB/ second |  Outgoing data transmitted for the target in KB per second\. CDCThroughputBandwidth records outgoing data transmitted on sampling points\. If no network traffic is found, the value is zero\. Because CDC does not issue long\-running transactions, network traffic may not be recorded\.  | 
| CDCThroughputRowsSource | Rows/ second |  Incoming changes from the source in rows per second\.  | 
| CDCThroughputRowsTarget | Rows/ second |  Outgoing changes for the target in rows per second\.  | 
| CPUUtilization | Percent |  The percentage of allocated vCPU \(virtual CPU\) currently in use by the replication\.  | 
| FullLoadThroughputBandwidthTarget | KB/ second |  Outgoing data transmitted from a full load for the target in KB per second\.  | 
| FullLoadThroughputRowsTarget | Rows/ second |  Outgoing changes from a full load for the target in rows per second\.  | 

### AWS DMS serverless replication logs<a name="CHAP_Serverless.monitoring.logs"></a>

You can use Amazon CloudWatch to log replication information during an AWS DMS migration process\. You enable logging when you select replication settings\.

Serverless replications upload status logs to your CloudWatch account to provide increased visibility into the progress of the replication, and to assist with troubleshooting\.

AWS DMS uploads serverless\-linked logs to a dedicated log group with the prefix `dms-serverless-replication-<your replication config resource ID>`\. Within this log group, there is a log stream called `dms-serverless-replication-orchestrator-<your replication config resource ID>`\. This log stream reports the replication state of your replication, and an associated message providing further details on the work it is doing in this stage\. For examples of log entries, see [Serverless replication log examples](#CHAP_Serverless.monitoring.logs.examples) following\.

**Note**  
AWS DMS doesn't create either the log group or stream until you run the replication\. AWS DMS doesn't create the log group or stream if you only create the replication\.

To view logs of a replication that ran, follow these steps:

1. Open the AWS DMS console, and choose **Database migration tasks** from the navigation pane\. The Database migration tasks dialog appears\. 

1. Select the name of your replication\. The Overview details dialog appears\.

1. Locate the **Migration task logs** section and choose **View CloudWatch Logs**\.

If your replication fails, AWS DMS will create a log entry with a replication state of `failed`, and a message describing the reason for the failure\. You should check your CloudWatch logs as the first step in troubleshooting a failed replication\. 

#### Serverless replication log examples<a name="CHAP_Serverless.monitoring.logs.examples"></a>

This section includes examples of log entries for serverless replications\.

##### Example: Replication start<a name="CHAP_Serverless.monitoring.logs.examples.start"></a>

When you run a serverless replication, AWS DMS creates a log entry similar to the following:

```
{'replication_state':'initializing', 'message': 'Initializing the replication workflow.'}
```

##### Example: Replication failure<a name="CHAP_Serverless.monitoring.logs.examples.fail"></a>

If one of the endpoints of the replication is not configured correctly, AWS DMS creates a log entry similar to the following:

```
{'replication_state':'failed', 'message': 'Test connection failed for endpoint X.', 'failure_message': 'X'}
```

If you see this message in your log after a failure, make sure that the specified endpoint is healthy and configured correctly\.