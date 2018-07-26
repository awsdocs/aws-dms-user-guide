# Working with an AWS DMS Replication Instance<a name="CHAP_ReplicationInstance"></a>

When you create an AWS DMS replication instance, AWS DMS creates the replication instance on an Amazon Elastic Compute Cloud \(Amazon EC2\) instance in a VPC based on the Amazon Virtual Private Cloud \(Amazon VPC\) service\. You use this replication instance to perform your database migration\. The replication instance provides high availability and failover support using a Multi\-AZ deployment when you select the **Multi\-AZ** option\. 

In a Multi\-AZ deployment, AWS DMS automatically provisions and maintains a synchronous standby replica of the replication instance in a different Availability Zone\. The primary replication instance is synchronously replicated across Availability Zones to a standby replica\. This approach provides data redundancy, eliminates I/O freezes, and minimizes latency spikes\.

![\[AWS Database Migration Service replication instance\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-conceptual2.png)

AWS DMS uses a replication instance to connect to your source data store, read the source data, and format the data for consumption by the target data store\. A replication instance also loads the data into the target data store\. Most of this processing happens in memory\. However, large transactions might require some buffering on disk\. Cached transactions and log files are also written to disk\.

You can create an AWS DMS replication instance in the following AWS Regions\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_ReplicationInstance.html)

AWS DMS supports a special AWS Region called AWS GovCloud \(US\) that is designed to allow US government agencies and customers to move more sensitive workloads into the cloud\. AWS GovCloud \(US\) addresses the US government's specific regulatory and compliance requirements\. For more information about AWS GovCloud \(US\), see [What Is AWS GovCloud \(US\)?](http://docs.aws.amazon.com/govcloud-us/latest/UserGuide/whatis.html)

Following, you can find out more details about replication instances\.

**Topics**
+ [Selecting the Right AWS DMS Replication Instance for Your Migration](#CHAP_ReplicationInstance.InDepth)
+ [Public and Private Replication Instances](#CHAP_ReplicationInstance.PublicPrivate)
+ [AWS DMS Maintenance](#CHAP_ReplicationInstance.Maintenance)
+ [Working with Replication Engine Versions](#CHAP_ReplicationInstance.EngineVersions)
+ [Setting Up a Network for a Replication Instance](CHAP_ReplicationInstance.VPC.md)
+ [Setting an Encryption Key for a Replication Instance](#CHAP_ReplicationInstance.EncryptionKey)
+ [Creating a Replication Instance](#CHAP_ReplicationInstance.Creating)
+ [Modifying a Replication Instance](#CHAP_ReplicationInstance.Modifying)
+ [Rebooting a Replication Instance](#CHAP_ReplicationInstance.Rebooting)
+ [Deleting a Replication Instance](#CHAP_ReplicationInstance.Deleting)
+ [DDL Statements Supported by AWS DMS](#CHAP_ReplicationInstance.SupportedDDL)

## Selecting the Right AWS DMS Replication Instance for Your Migration<a name="CHAP_ReplicationInstance.InDepth"></a>

AWS DMS creates the replication instance on an Amazon Elastic Compute Cloud \(Amazon EC2\) instance\. AWS DMS currently supports the T2, C4, and R4 Amazon EC2 instance classes for replication instances: 
+ The T2 instance classes are low\-cost standard instances designed to provide a baseline level of CPU performance with the ability to burst above the baseline\. They are suitable for developing, configuring, and testing your database migration process\. They also work well for periodic data migration tasks that can benefit from the CPU burst capability\. 
+ The C4 instance classes are designed to deliver the highest level of processor performance for computer\-intensive workloads\. They achieve significantly higher packet per second \(PPS\) performance, lower network jitter, and lower network latency\. AWS DMS can be CPU\-intensive, especially when performing heterogeneous migrations and replications such as migrating from Oracle to PostgreSQL\. C4 instances can be a good choice for these situations\.
+ The R4 instance classes are memory optimized for memory\-intensive workloads\. Ongoing migrations or replications of high\-throughput transaction systems using DMS can, at times, consume large amounts of CPU and memory\. R4 instances include more memory per vCPU\.

Each replication instance has a specific configuration of memory and vCPU\. The following table shows the configuration for each replication instance type\. For pricing information, see the [AWS Database Migration Service pricing page](https://aws.amazon.com/dms/pricing/)\.


| Replication Instance Type | vCPU | Memory \(GB\) | 
| --- | --- | --- | 
| General Purpose | 
| dms\.t2\.micro | 1 | 1 | 
| dms\.t2\.small | 1 | 2 | 
| dms\.t2\.medium | 2 | 4 | 
| dms\.t2\.large | 2 | 8 | 
| Compute Optimized | 
| dms\.c4\.large | 2 | 3\.75 | 
| dms\.c4\.xlarge | 4 | 7\.5 | 
| dms\.c4\.2xlarge | 8 | 15 | 
| dms\.c4\.4xlarge | 16 | 30 | 
| Memory Optimized | 
| dms\.r4\.large | 2 | 15\.25 | 
| dms\.r4\.xlarge | 4 | 30\.5 | 
| dms\.r4\.2xlarge | 8 | 61 | 
| dms\.r4\.4xlarge | 16 | 122 | 
| dms\.r4\.8xlarge | 32 | 244 | 

To help you determine which replication instance class would work best for your migration, let’s look at the change data capture \(CDC\) process that the AWS DMS replication instance uses\.

Let’s assume that you’re running a full load plus CDC task \(bulk load plus ongoing replication\)\. In this case, the task has its own SQLite repository to store metadata and other information\. Before AWS DMS starts a full load, these steps occur: 
+ AWS DMS starts capturing changes for the tables it's migrating from the source engine’s transaction log \(we call these *cached changes*\)\. After full load is done, these cached changes are collected and applied on the target\. Depending on the volume of cached changes, these changes can directly be applied from memory, where they are collected first, up to a set threshold\. Alternatively, they can be applied from disk, where changes are written when they can't be held in memory\. 
+ After cached changes are applied, by default AWS DMS starts a transactional apply on the target instance\. 

During the applied cached changes phase and ongoing replications phase, AWS DMS uses two stream buffers, one each for incoming and outgoing data\. AWS DMS also uses an important component called a sorter, which is another memory buffer\. Following are two important uses of the sorter component \(which has others\): 
+ It tracks all transactions and makes sure that it forwards only relevant transactions to the outgoing buffer\. 
+ It makes sure that transactions are forwarded in the same commit order as on the source\. 

As you can see, we have three important memory buffers in this architecture for CDC in AWS DMS\. If any of these buffers experience memory pressure, the migration can have performance issues that can potentially cause failures\.

When you plug heavy workloads with a high number of transactions per second \(TPS\) into this architecture, you can find the extra memory provided by R4 instances useful\. You can use R4 instances to hold a large number of transactions in memory and prevent memory\-pressure issues during ongoing replications\.

## Public and Private Replication Instances<a name="CHAP_ReplicationInstance.PublicPrivate"></a>

You can specify whether a replication instance has a public or private IP address that the instance uses to connect to the source and target databases\. 

A private replication instance has a private IP address that you can't access outside the replication network\. A replication instance should have a private IP address when both source and target databases are in the same network that is connected to the replication instance's VPC by using a VPN, AWS Direct Connect, or VPC peering\.

A *VPC peering* connection is a networking connection between two VPCs that enables routing using each VPC’s private IP addresses as if they were in the same network\. For more information about VPC peering, see [VPC Peering](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/vpc-peering.html) in the *Amazon VPC User Guide*\.

## AWS DMS Maintenance<a name="CHAP_ReplicationInstance.Maintenance"></a>

Periodically, AWS DMS performs maintenance on AWS DMS resources\. Maintenance most often involves updates to the replication instance or the replication instance's operating system \(OS\)\. You can manage the time period for your maintenance window and see maintenance updates using the AWS CLI or AWS DMS API\. The AWS DMS console is not currently supported for this work\. 

Maintenance items require that AWS DMS take your replication instance offline for a short time\. Maintenance that requires a resource to be offline includes required operating system or instance patching\. Required patching is automatically scheduled only for patches that are related to security and instance reliability\. Such patching occurs infrequently \(typically once or twice a year\) and seldom requires more than a fraction of your maintenance window\. You can have minor version updates applied automatically by choosing the **Auto minor version upgrade** console option\.

### AWS DMS Maintenance Window<a name="CHAP_ReplicationInstance.MaintenanceWindow"></a>

Every AWS DMS replication instance has a weekly maintenance window during which any available system changes are applied\. You can think of the maintenance window as an opportunity to control when modifications and software patching occurs\. 

If AWS DMS determines that maintenance is required during a given week, the maintenance occurs during the 30\-minute maintenance window you chose when you created the replication instance\. AWS DMS completes most maintenance during the 30\-minute maintenance window\. However, a longer time might be required for larger changes\.

The 30\-minute maintenance window that you selected when you created the replication instance is from an 8\-hour block of time allocated for each AWS Region\. If you don't specify a preferred maintenance window when you create your replication instance, AWS DMS assigns one on a randomly selected day of the week\. For a replication instance that uses a Multi\-AZ deployment, a failover might be required for maintenance to be completed\.

The following table lists the maintenance window for each AWS Region that supports AWS DMS\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_ReplicationInstance.html)

#### Effect of Maintenance on Existing Migration Tasks<a name="CHAP_ReplicationInstance.MaintenanceWindow.Effect"></a>

When an AWS DMS migration task is running on an instance, the following events occur when a patch is applied:
+ If the tables in the migration task are in the replicating ongoing changes phase \(CDC\), AWS DMS pauses the task for a moment while the patch is applied\. The migration then continues from where it was interrupted when the patch was applied\.
+ If AWS DMS is migrating a table when the patch is applied, AWS DMS restarts the migration for the table\. 

#### Changing the Maintenance Window Setting<a name="CHAP_ReplicationInstance.MaintenanceWindow.Changing"></a>

You can change the maintenance window time frame using the AWS Management Console, the AWS CLI, or the AWS DMS API\.

##### Changing the Maintenance Window Setting Using the AWS Console<a name="CHAP_ReplicationInstance.AdjustingTheMaintenanceWindow.CON"></a>

You can change the maintenance window time frame using the AWS Management Console\.

**To change the preferred maintenance window using the AWS console**

1.  Sign in to the AWS Management Console and choose AWS DMS\. 

1. In the navigation pane, choose **Replication instances**\.

1. Choose the replication instance you want to modify and choose **Modify**\.

1. Expand the **Maintenance** section and choose a date and time for your maintenance window\.  
![\[modify a replication instance\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-modifymaintenance.png)

1. Choose **Apply changes immediately**\. 

1.  Choose **Modify**\.

##### Changing the Maintenance Window Setting Using the CLI<a name="CHAP_ReplicationInstanceAdjustingTheMaintenanceWindow.CLI"></a>

To adjust the preferred maintenance window, use the AWS CLI [http://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html](http://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) command with the following parameters\.
+ `--replication-instance-identifier`
+ `--preferred-maintenance-window`

**Example**  
The following AWS CLI example sets the maintenance window to Tuesdays from 4:00–4:30 a\.m\. UTC\.  

```
aws dms modify-replication-instance \
--replication-instance-identifier myrepinstance \
--preferred-maintenance-window Tue:04:00-Tue:04:30
```

##### Changing the Maintenance Window Setting Using the API<a name="CHAP_ReplicationInstanceAdjustingTheMaintenanceWindow.API"></a>

To adjust the preferred maintenance window, use the AWS DMS API [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) action with the following parameters\.
+ `ReplicationInstanceIdentifier = myrepinstance`
+ `PreferredMaintenanceWindow = Tue:04:00-Tue:04:30`

**Example**  
The following code example sets the maintenance window to Tuesdays from 4:00–4:30 a\.m\. UTC\.  

```
 1. https://dms.us-west-2.amazonaws.com/
 2. ?Action=ModifyReplicationInstance
 3. &DBInstanceIdentifier=myrepinstance
 4. &PreferredMaintenanceWindow=Tue:04:00-Tue:04:30
 5. &SignatureMethod=HmacSHA256
 6. &SignatureVersion=4
 7. &Version=2014-09-01
 8. &X-Amz-Algorithm=AWS4-HMAC-SHA256
 9. &X-Amz-Credential=AKIADQKE4SARGYLE/20140425/us-east-1/dms/aws4_request
10. &X-Amz-Date=20140425T192732Z
11. &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
12. &X-Amz-Signature=1dc9dd716f4855e9bdf188c70f1cf9f6251b070b68b81103b59ec70c3e7854b3
```

## Working with Replication Engine Versions<a name="CHAP_ReplicationInstance.EngineVersions"></a>

The *replication engine* is the core AWS DMS software that runs on your replication instance and performs the migration tasks you specify\. AWS periodically releases new versions of the AWS DMS replication engine software, with new features and performance improvements\. Each version of the replication engine software has its own version number, to distinguish it from other versions\.

When you launch a new replication instance, it runs the latest AWS DMS engine version unless you specify otherwise\. For more information, see [Working with an AWS DMS Replication Instance](#CHAP_ReplicationInstance)\.

If you have a replication instance that is currently running, you can upgrade it to a more recent engine version\. \(AWS DMS doesn't support engine version downgrades\.\) For more information, including a list of replication engine versions, see the following section\.

### Deprecating a Replication Instance Version<a name="CHAP_ReplicationInstance.EngineVersions.Deprecating"></a>

Occasionally AWS DMS deprecates older versions of the replication instance\. Beginning April 2, 2018, AWS DMS will disable creation of any new replication instance version 1\.9\.0\. This version was initially supported in AWS DMS on March 15, 2016, and has since been replaced by subsequent versions containing improvements to functionality, security, and reliability\. 

Beginning on August 5, 2018, at 0:00 UTC, all DMS replication instances running version 1\.9\.0 will be scheduled for automatic upgrade to the latest available version during the maintenance window specified for each instance\. We recommend that you upgrade your instances before that time, at a time that is convenient for you\. 

You can initiate an upgrade of your replication instance by using the instructions in the following section, [Upgrading the Engine Version of a Replication Instance ](#CHAP_ReplicationInstance.EngineVersions.Upgrading)\. 

For migration tasks that are running when you choose to upgrade the replication instance, tables in the full load phase at the time of the upgrade are reloaded from the start once the upgrade is complete\. Replication for all other tables should resume without interruption once the upgrade is complete\. We recommend testing all current migration tasks on the latest available version of AWS DMS replication instance before upgrading the instances from version 1\.9\.0\. 

### Upgrading the Engine Version of a Replication Instance<a name="CHAP_ReplicationInstance.EngineVersions.Upgrading"></a>

AWS periodically releases new versions of the AWS DMS replication engine software, with new features and performance improvements\. The following is a summary of available AWS DMS engine versions\.


****  

| Version | Summary  | 
| --- | --- | 
| 2\.4\.x |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_ReplicationInstance.html) | 
| 2\.3\.x |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_ReplicationInstance.html) | 
| 2\.2\.x |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_ReplicationInstance.html) | 
| 1\.9\.x | Cumulative release of AWS DMS replication engine software\. | 

#### Upgrading the Engine Version Using the Console<a name="Upgrading.Console"></a>

You can upgrade an AWS DMS replication instance using the AWS Management Console\.

**To upgrade a replication instance using the console**

1. Open the AWS DMS console at [https://console\.aws\.amazon\.com/dms/](https://console.aws.amazon.com/dms/)\.

1. In the navigation pane, choose **Replication instances**\. 

1. Choose your replication engine, and then choose **Modify**\.

1. For **Replication engine version**, choose the version number you want, and then choose **Modify**\.

**Note**  
Upgrading the replication instance takes several minutes\. When the instance is ready, its status changes to **available**\.

#### Upgrading the Engine Version Using the CLI<a name="Upgrading.CLI"></a>

You can upgrade an AWS DMS replication instance using the AWS CLI, as follows\.

**To upgrade a replication instance using the AWS CLI**

1. Determine the Amazon Resource Name \(ARN\) of your replication instance by using the following command\.

   ```
   aws dms describe-replication-instances \
   --query "ReplicationInstances[*].[ReplicationInstanceIdentifier,ReplicationInstanceArn,ReplicationInstanceClass]"
   ```

   In the output, take note of the ARN for the replication instance you want to upgrade, for example: `arn:aws:dms:us-east-1:123456789012:rep:6EFQQO6U6EDPRCPKLNPL2SCEEY` 

1. Determine which replication instance versions are available by using the following command\.

   ```
   aws dms describe-orderable-replication-instances \
   --query "OrderableReplicationInstances[*].[ReplicationInstanceClass,EngineVersion]"
   ```

   In the output, take note of the engine version number or numbers that are available for your replication instance class\. You should see this information in the output from step 1\.

1. Upgrade the replication instance by using the following command\.

   ```
   aws dms modify-replication-instance \
   --replication-instance-arn arn \
   --engine-version n.n.n
   ```

   Replace *arn* in the preceding with the actual replication instance ARN from the previous step\. 

   Replace *n\.n\.n *with the engine version number that you want, for example: `2.2.1`

**Note**  
Upgrading the replication instance takes several minutes\. You can view the replication instance status using the following command\.  

```
aws dms describe-replication-instances \
--query "ReplicationInstances[*].[ReplicationInstanceIdentifier,ReplicationInstanceStatus]"
```
When the replication instance is ready, its status changes to **available**\.

## Setting an Encryption Key for a Replication Instance<a name="CHAP_ReplicationInstance.EncryptionKey"></a>

AWS DMS encrypts the storage used by a replication instance and the endpoint connection information\. To encrypt the storage used by a replication instance, AWS DMS uses a master key that is unique to your AWS account\. You can view and manage this master key with AWS Key Management Service \(AWS KMS\)\. You can use the default master key in your account \(`aws/dms`\) or a custom master key that you create\. If you have an existing AWS KMS encryption key, you can also use that key for encryption\. 

You can specify your own encryption key by supplying a KMS key identifier to encrypt your AWS DMS resources\. When you specify your own encryption key, the user account used to perform the database migration must have access to that key\. For more information on creating your own encryption keys and giving users access to an encryption key, see the *[AWS KMS Developer Guide](http://docs.aws.amazon.com/kms/latest/developerguide/create-keys.html)*\. 

If you don't specify a KMS key identifier, then AWS DMS uses your default encryption key\. KMS creates the default encryption key for AWS DMS for your AWS account\. Your AWS account has a different default encryption key for each AWS Region\. 

To manage the keys used for encrypting your AWS DMS resources, you use KMS\. You can find KMS in the AWS Management Console by choosing **Identity & Access Management** on the console home page and then choosing **Encryption Keys** on the navigation pane\. 

KMS combines secure, highly available hardware and software to provide a key management system scaled for the cloud\. Using KMS, you can create encryption keys and define the policies that control how these keys can be used\. KMS supports AWS CloudTrail, so you can audit key usage to verify that keys are being used appropriately\. Your KMS keys can be used in combination with AWS DMS and supported AWS services such as Amazon RDS, S3, Amazon Elastic Block Store \(Amazon EBS\), and Amazon Redshift\. 

When you have created your AWS DMS resources with a specific encryption key, you can't change the encryption key for those resources\. Make sure to determine your encryption key requirements before you create your AWS DMS resources\. 

## Creating a Replication Instance<a name="CHAP_ReplicationInstance.Creating"></a>

Your first task in migrating a database is to create a replication instance that has sufficient storage and processing power to perform the tasks you assign and migrate data from your source database to the target database\. The required size of this instance varies depending on the amount of data you need to migrate and the tasks that you need the instance to perform\. For more information about replication instances, see [Working with an AWS DMS Replication Instance](#CHAP_ReplicationInstance)\. 

The procedure following assumes that you have chosen the AWS DMS console wizard\. You can also do this step by selecting **Replication instances** from the AWS DMS console's navigation pane and then selecting **Create replication instance**\.

**To create a replication instance by using the AWS console**

1. On the **Create replication instance** page, specify your replication instance information\. The following table describes the settings\.   
![\[Create a replication instance\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-gs-wizard2.png)    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_ReplicationInstance.html)

1. Choose the **Advanced** tab, shown following, to set values for network and encryption settings if you need them\. The following table describes the settings\.  
![\[Advanced Tab\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-gs-wizard2A.png)    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_ReplicationInstance.html)

1. Specify the **Maintenance** settings\. The following table describes the settings\. For more information about maintenance settings, see [AWS DMS Maintenance Window](#CHAP_ReplicationInstance.MaintenanceWindow)\.  
![\[Maintenance Tab\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-gs-wizard2B.png)    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_ReplicationInstance.html)

1. Choose **Create replication instance**\.

## Modifying a Replication Instance<a name="CHAP_ReplicationInstance.Modifying"></a>

You can modify the settings for a replication instance to, for example, change the instance class or to increase storage\. 

When you modify a replication instance, you can apply the changes immediately\. To apply changes immediately, you select the **Apply changes immediately** option in the AWS Management Console, you use the `--apply-immediately` parameter when calling the AWS CLI, or you set the `ApplyImmediately` parameter to `true` when using the AWS DMS API\. 

If you don't choose to apply changes immediately, the changes are put into the pending modifications queue\. During the next maintenance window, any pending changes in the queue are applied\. 

**Note**  
If you choose to apply changes immediately, any changes in the pending modifications queue are also applied\. If any of the pending modifications require downtime, choosing **Apply changes immediately** can cause unexpected downtime\. 

**To modify a replication instance by using the AWS console**

1.  Sign in to the AWS Management Console and select AWS DMS\. 

1. In the navigation pane, choose **Replication instances**\.

1. Choose the replication instance you want to modify\. The following table describes the modifications you can make\.     
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_ReplicationInstance.html)

## Rebooting a Replication Instance<a name="CHAP_ReplicationInstance.Rebooting"></a>

You can reboot an AWS DMS replication instance to restart the replication engine\. A reboot results in a momentary outage for the replication instance, during which the instance status is set to **Rebooting**\. If the AWS DMS instance is configured for Multi\-AZ, the reboot can be conducted with a failover\. An AWS DMS event is created when the reboot is completed\.

If your AWS DMS instance is a Multi\-AZ deployment, you can force a failover from one AWS Availability Zone to another when you reboot\. When you force a failover of your AWS DMS instance, AWS DMS automatically switches to a standby instance in another Availability Zone\. Rebooting with failover is beneficial when you want to simulate a failure of an AWS DMS instance for testing purposes\. 

If there are migration tasks running on the replication instance when a reboot occurs, no data loss occurs and the task resumes once the reboot is completed\. If the tables in the migration task are in the middle of a bulk load \(full load phase\), DMS restarts the migration for those tables from the beginning\. If tables in the migration task are in the ongoing replication phase, the task resumes once the reboot is completed\.

You can't reboot your AWS DMS replication instance if its status is not in the **Available** state\. Your AWS DMS instance can be unavailable for several reasons, such as a previously requested modification or a maintenance\-window action\. The time required to reboot an AWS DMS replication instance is typically small \(under 5 minutes\)\. 

### Rebooting a Replication Instance Using the AWS Console<a name="CHAP_ReplicationInstance.Rebooting.CON"></a>

To reboot a replication instance, use the AWS console\.

**To reboot a replication instance using the AWS console**

1.  Sign in to the AWS Management Console and select AWS DMS\. 

1. In the navigation pane, choose **Replication instances**\.

1. Choose the replication instance you want to reboot\. 

1. Choose **Reboot**\.

1. In the **Reboot replication instance** dialog box, choose **Reboot With Failover?** if you have configured your replication instance for Multi\-AZ deployment and you want to fail over to another AWS Availability Zone\.

1. Choose **Reboot**\.

### Rebooting a Replication Instance Using the CLI<a name="CHAP_ReplicationInstance.Rebooting.CLI"></a>

To reboot a replication instance, use the AWS CLI [http://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html](http://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) command with the following parameter:
+ `--replication-instance-arn`

**Example Example Simple Reboot**  
The following AWS CLI example reboots a replication instance\.  

```
aws dms reboot-replication-instance \
--replication-instance-arn arnofmyrepinstance
```

**Example Example Simple Reboot with Failover**  
The following AWS CLI example reboots a replication instance with failover\.  

```
aws dms reboot-replication-instance \
--replication-instance-arn arnofmyrepinstance \
--force-failover
```

### Rebooting a Replication Instance Using the API<a name="CHAP_ReplicationInstance.Rebooting.API"></a>

To reboot a replication instance, use the AWS DMS API [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) action with the following parameters:
+ `ReplicationInstanceArn = arnofmyrepinstance`

**Example Example Simple Reboot**  
The following code example reboots a replication instance\.  

```
 1. https://dms.us-west-2.amazonaws.com/
 2. ?Action=RebootReplicationInstance
 3. &DBInstanceArn=arnofmyrepinstance
 4. &SignatureMethod=HmacSHA256
 5. &SignatureVersion=4
 6. &Version=2014-09-01
 7. &X-Amz-Algorithm=AWS4-HMAC-SHA256
 8. &X-Amz-Credential=AKIADQKE4SARGYLE/20140425/us-east-1/dms/aws4_request
 9. &X-Amz-Date=20140425T192732Z
10. &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
11. &X-Amz-Signature=1dc9dd716f4855e9bdf188c70f1cf9f6251b070b68b81103b59ec70c3e7854b3
```

**Example Example Simple Reboot with Failover**  
The following code example reboots a replication instance and fails over to another AWS Availability Zone\.  

```
 1. https://dms.us-west-2.amazonaws.com/
 2. ?Action=RebootReplicationInstance
 3. &DBInstanceArn=arnofmyrepinstance
 4. &ForceFailover=true
 5. &SignatureMethod=HmacSHA256
 6. &SignatureVersion=4
 7. &Version=2014-09-01
 8. &X-Amz-Algorithm=AWS4-HMAC-SHA256
 9. &X-Amz-Credential=AKIADQKE4SARGYLE/20140425/us-east-1/dms/aws4_request
10. &X-Amz-Date=20140425T192732Z
11. &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
12. &X-Amz-Signature=1dc9dd716f4855e9bdf188c70f1cf9f6251b070b68b81103b59ec70c3e7854b3
```

## Deleting a Replication Instance<a name="CHAP_ReplicationInstance.Deleting"></a>

You can delete an AWS DMS replication instance when you are finished using it\. If you have migration tasks that use the replication instance, you must stop and delete the tasks before deleting the replication instance\.

If you close your AWS account, all AWS DMS resources and configurations associated with your account are deleted after two days\. These resources include all replication instances, source and target endpoint configuration, replication tasks, and SSL certificates\. If after two days you decide to use AWS DMS again, you recreate the resources you need\.

### Deleting a Replication Instance Using the AWS Console<a name="CHAP_ReplicationInstance.Deleting.CON"></a>

To delete a replication instance, use the AWS console\.

**To delete a replication instance using the AWS console**

1.  Sign in to the AWS Management Console and select AWS DMS\. 

1. In the navigation pane, choose **Replication instances**\.

1. Choose the replication instance you want to delete\. 

1. Choose **Delete**\.

1. In the dialog box, choose **Delete**\.

### Deleting a Replication Instance Using the CLI<a name="CHAP_ReplicationInstance.Deleting.CLI"></a>

To delete a replication instance, use the AWS CLI [http://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html](http://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) command with the following parameter:
+ `--replication-instance-arn`

**Example Example Delete**  
The following AWS CLI example deletes a replication instance\.  

```
aws dms delete-replication-instance \
--replication-instance-arn <arnofmyrepinstance>
```

### Deleting a Replication Instance Using the API<a name="CHAP_ReplicationInstance.Deleting.API"></a>

To delete a replication instance, use the AWS DMS API [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) action with the following parameters:
+ `ReplicationInstanceArn = <arnofmyrepinstance>`

**Example Example Delete**  
The following code example deletes a replication instance\.  

```
 1. https://dms.us-west-2.amazonaws.com/
 2. ?Action=DeleteReplicationInstance
 3. &DBInstanceArn=arnofmyrepinstance
 4. &SignatureMethod=HmacSHA256
 5. &SignatureVersion=4
 6. &Version=2014-09-01
 7. &X-Amz-Algorithm=AWS4-HMAC-SHA256
 8. &X-Amz-Credential=AKIADQKE4SARGYLE/20140425/us-east-1/dms/aws4_request
 9. &X-Amz-Date=20140425T192732Z
10. &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
11. &X-Amz-Signature=1dc9dd716f4855e9bdf188c70f1cf9f6251b070b68b81103b59ec70c3e7854b3
```

## DDL Statements Supported by AWS DMS<a name="CHAP_ReplicationInstance.SupportedDDL"></a>

You can execute data definition language \(DDL\) statements on the source database during the data migration process\. These statements are replicated to the target database by the replication server\. 

Supported DDL statements include the following:
+ Create table
+ Drop table
+ Rename table
+ Add column
+ Drop column
+ Rename column
+ Change column data type

For information about which DDL statements are supported for a specific source, see the topic describing that source\.