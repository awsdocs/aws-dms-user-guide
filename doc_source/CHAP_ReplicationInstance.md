# AWS Database Migration Service Replication Instance<a name="CHAP_ReplicationInstance"></a>

 The first step in migrating data using AWS Database Migration Service is to create a replication instance\. An AWS DMS replication instance runs on an Amazon Elastic Compute Cloud \(Amazon EC2\) instance\. The replication instance provides high\-availability and failover support using a Multi\-AZ deployment\. In a Multi\-AZ deployment, AWS DMS automatically provisions and maintains a synchronous standby replica of the replication instance in a different Availability Zone\. The primary replication instance is synchronously replicated across Availability Zones to a standby replica\. This approach provides data redundancy, eliminate I/O freezes, and minimize latency spikes during system backups\.

AWS DMS uses a replication instance that connects to the source data store, reads the source data, formats the data for consumption by the target data store, and loads the data into the target data store\. Most of this processing happens in memory\. However, large transactions might require some buffering on disk\. Cached transactions and log files are also written to disk\.

The following topics are covered in this section\.


+ [AWS Database Migration Service Replication Instance in Depth](#CHAP_ReplicationInstance.InDepth)
+ [Public and Private Replication Instances](#CHAP_ReplicationInstance.PublicPrivate)
+ [AWS DMS Maintenance Window](#CHAP_ReplicationInstance.MaintenanceWindow)
+ [Replication Engine Versions](#CHAP_ReplicationInstance.EngineVersions)
+ [Setting Up a Network for a Replication Instance](#CHAP_ReplicationInstance.VPC)
+ [Setting an Encryption Key for AWS Database Migration Service Replication Instance](#CHAP_ReplicationInstance.EncryptionKey)
+ [DDL Statements Supported by AWS Database Migration Service](#CHAP_ReplicationInstance.SupportedDDL)
+ [Creating a Replication Instance](#CHAP_ReplicationInstance.Creating)
+ [Modifying a Replication Instance](#CHAP_ReplicationInstance.Modifying)

## AWS Database Migration Service Replication Instance in Depth<a name="CHAP_ReplicationInstance.InDepth"></a>

AWS DMS creates a replication instance that runs on an Amazon Elastic Compute Cloud \(Amazon EC2\) instance in a VPC based on the Amazon Virtual Private Cloud \(Amazon VPC\) service\. You use this replication instance to perform the database migration\. The replication instance provides high\-availability and failover support using a Multi\-AZ deployment when you select the **Multi\-AZ** option\. In a Multi\-AZ deployment, AWS DMS automatically provisions and maintains a synchronous standby replica of the replication instance in a different Availability Zone\. The primary replication instance is synchronously replicated across Availability Zones to a standby replica to provide data redundancy, eliminate I/O freezes, and minimize latency spikes\.

![\[ AWS Database Migration Service replication instance\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-conceptual2.png)

AWS DMS currently supports the T2 and C4 instance classes for replication instances\. The T2 instance classes are low\-cost standard instances designed to provide a baseline level of CPU performance with the ability to burst above the baseline\. They are suitable for developing, configuring, and testing your database migration process, and for periodic data migration tasks that can benefit from the CPU burst capability\. The C4 instance classes are designed to deliver the highest level of processor performance and achieve significantly higher packet per second \(PPS\) performance, lower network jitter, and lower network latency\. You should use C4 instance classes if you are migrating large databases and want to minimize the migration time\.

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

## Public and Private Replication Instances<a name="CHAP_ReplicationInstance.PublicPrivate"></a>

You can specify whether a replication instance has a public or private IP address that the instance uses to connect to the source and target databases\. A replication instance should have a public IP address if the source or target database is located in a network that is not connected to the replication instance's VPC by using a virtual private network \(VPN\), AWS Direct Connect, or VPC peering\. 

A private replication instance has a private IP address that cannot be accessed outside the replication network\. A replication instance should have a private IP address when both the source and target databases are located in the same network that is connected to the replication instance's VPC by using a VPN, AWS Direct Connect, or VPC peering\.

A *VPC peering* connection is a networking connection between two VPCs that enables routing using each VPC’s private IP addresses as if they were in the same network\. For more information about VPC peering, see [VPC Peering](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/vpc-peering.html) in the *Amazon VPC User Guide*\.

## AWS DMS Maintenance Window<a name="CHAP_ReplicationInstance.MaintenanceWindow"></a>

Periodically, AWS DMS performs maintenance on AWS DMS resources\. Maintenance most often involves updates to the replication instance or the replication instance's operating system \(OS\)\. You can manage the time period for your maintenance window and see maintenance updates using the AWS CLI or AWS DMS API\. The AWS DMS console is not currently supported for this work\. 

You can manually apply maintenance items at your convenience, or wait for the automatic maintenance process initiated by AWS DMS during your weekly maintenance window\. You can find out whether a maintenance update is available for your replication instance by using the AWS CLI or AWS DMS API\. 

Maintenance items require that AWS DMS take your replication instance offline for a short time\. Maintenance that requires a resource to be offline includes required operating system or instance patching\. Required patching is automatically scheduled only for patches that are related to security and instance reliability\. Such patching occurs infrequently \(typically once or twice a year\) and seldom requires more than a fraction of your maintenance window\. You can have minor version updates applied automatically by selecting the **Auto minor version upgrade** option\.

Use the [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeReplicationInstances.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeReplicationInstances.html) API action to determine if there are pending modifications to your replication instance and to see the current time period for your maintenance window\. The [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ReplicationInstance.html#DMS-Type-ReplicationInstance-PendingModifiedValues](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ReplicationInstance.html#DMS-Type-ReplicationInstance-PendingModifiedValues) parameter indicates what parameter changes are pending\. The [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ReplicationInstance.html#DMS-Type-ReplicationInstance-PreferredMaintenanceWindow](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ReplicationInstance.html#DMS-Type-ReplicationInstance-PreferredMaintenanceWindow) parameter shows the current time period for your maintenance window\. 

To change the time of the maintenance window, use the AWS DMS console or the [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyReplicationInstance.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyReplicationInstance.html) API action and set the [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ReplicationInstance.html#DMS-Type-ReplicationInstance-PreferredMaintenanceWindow](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ReplicationInstance.html#DMS-Type-ReplicationInstance-PreferredMaintenanceWindow) parameter to your preferred time period\.

 If you do not set a default maintenance window time period, a randomly generated time period is set for your replication instance\. 

## Replication Engine Versions<a name="CHAP_ReplicationInstance.EngineVersions"></a>

The *replication engine* is the core AWS DMS software that runs on your replication instance and performs the migration tasks you specify\. AWS periodically releases new versions of the AWS DMS replication engine software, with new features and performance improvements\. Each version of the replication engine software has its own version number, to distinguish it from other versions\.

When you launch a new replication instance, it will run the latest AWS DMS engine version unless you specify otherwise\. For more information, see [AWS Database Migration Service Replication Instance](#CHAP_ReplicationInstance)\.

If you have a replication instance that is currently running, you can upgrade it to a more recent engine version\. \(AWS DMS does not support engine version downgrades\.\) For more information, including a list of replication engine versions, see the following section\.

### Upgrading the Engine Version of a Replication Instance<a name="CHAP_ReplicationInstance.EngineVersions.Upgrading"></a>

AWS periodically releases new versions of the AWS DMS replication engine software, with new features and performance improvements\. The following is a summary of available AWS DMS engine versions:


**AWS DMS Replication Engine Versions**  

| Version | Summary  | 
| --- | --- | 
| 2\.4\.x |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_ReplicationInstance.html) | 
| 2\.3\.x |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_ReplicationInstance.html) | 
| 2\.2\.x |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_ReplicationInstance.html) | 
| 1\.9\.x | Cumulative release of AWS DMS replication engine software\. | 

#### Upgrading the Engine Version Using the AWS Management Console<a name="Upgrading.Console"></a>

To upgrade an AWS DMS replication instance using the AWS Management Console, do the following:

****

1. Open the AWS DMS console at [https://console\.aws\.amazon\.com/dms/](https://console.aws.amazon.com/dms/)\.

1. In the navigation pane, choose **Replication instances**\. 

1. Choose your replication engine, and then choose **Modify**\.

1. In the **Replication engine version** field, choose the version number you want, and then choose **Modify**\.

**Note**  
Upgrading the replication instance will take several minutes\. When the instance is ready, its status changes to **available**\.

#### Upgrading the Engine Version Using the AWS CLI<a name="Upgrading.CLI"></a>

To upgrade an AWS DMS replication instance using the AWS CLI, do the following:

****

1. Determine the ARN \(Amazon Resource Name\) of your replication instance:

   ```
   aws dms describe-replication-instances \
   --query "ReplicationInstances[*].[ReplicationInstanceIdentifier,ReplicationInstanceArn,ReplicationInstanceClass]"
   ```

   In the output, take note of the Amazon Resource Name \(ARN\) for the replication instance you want to upgrade\. For example: `arn:aws:dms:us-east-1:123456789012:rep:6EFQQO6U6EDPRCPKLNPL2SCEEY` 

1. Determine which replication instance versions are available:

   ```
   aws dms describe-orderable-replication-instances \
   --query "OrderableReplicationInstances[*].[ReplicationInstanceClass,EngineVersion]"
   ```

   In the output, take note of the engine version number\(s\) that are available for your replication instance class\. \(You should see this information in the output from Step 1\.\)

1. Upgrade the replication instance:

   ```
   aws dms modify-replication-instance \
   --replication-instance-arn arn \
   --engine-version n.n.n
   ```

   Replace *arn* with the actual replication instance ARN from the previous step\. 

   Replace *n\.n\.n *with the engine version number that you want\. For example: `2.2.1`

**Note**  
Upgrading the replication instance will take several minutes\. You can view the replication instance status using this command:  

```
aws dms describe-replication-instances \
--query "ReplicationInstances[*].[ReplicationInstanceIdentifier,ReplicationInstanceStatus]"
```
When the replication instance is ready, its status changes to **available**\.

## Setting Up a Network for a Replication Instance<a name="CHAP_ReplicationInstance.VPC"></a>

AWS DMS always creates the replication instance in an Amazon Virtual Private Cloud \(Amazon VPC\), and you specify the VPC where your replication instance is located\. You can use your default VPC for your account and region, or you can create a new VPC\. The VPC must have two subnets in at least one Availability Zone\.

The Elastic Network Interface \(ENI\) allocated for the replication instance in your VPC must be associated with a security group that has rules that allow all traffic on all ports to leave \(egress\) the VPC\. This approach allows communication from the replication instance to your source and target database endpoints, as long as correct egress rules are enabled on the endpoints\. We recommend that you use the default settings for the endpoints, which allows egress on all ports to all addresses\.

The source and target endpoints access the replication instance that is inside the VPC either by connecting to the VPC or by being inside the VPC\. The database endpoints must include network ACLs and security group rules \(if applicable\) that allow incoming access from the replication instance\. Depending on the network configuration you are using, you can use the replication instance VPC security group, the replication instance's private or public IP address, or the NAT Gateway's public IP address\. These connections form a network that you use for data migration\.

### Network Configurations for Database Migration<a name="CHAP_ReplicationInstance.VPC.Configurations"></a>

You can use several different network configurations with AWS Database Migration Service\. The following are common configurations for a network used for database migration\.


+ [Configuration with All Database Migration Components in One VPC](#CHAP_ReplicationInstance.VPC.Configurations.ScenarioAllVPC)
+ [Configuration with Two VPCs](#CHAP_ReplicationInstance.VPC.Configurations.ScenarioVPCPeer)
+ [Configuration for a Network to a VPC Using AWS Direct Connect or a VPN](#CHAP_ReplicationInstance.VPC.Configurations.ScenarioDirect)
+ [Configuration for a Network to a VPC Using the Internet](#CHAP_ReplicationInstance.VPC.Configurations.ScenarioInternet)
+ [Configuration with an Amazon RDS DB instance not in a VPC to a DB instance in a VPC Using ClassicLink](#CHAP_ReplicationInstance.VPC.Configurations.ClassicLink)

#### Configuration with All Database Migration Components in One VPC<a name="CHAP_ReplicationInstance.VPC.Configurations.ScenarioAllVPC"></a>

The simplest network for database migration is for the source endpoint, the replication instance, and the target endpoint to all be in the same VPC\. This configuration is a good one if your source and target endpoints are on an Amazon Relational Database Service \(Amazon RDS\) DB instance or an Amazon Elastic Compute Cloud \(Amazon EC2\) instance\.

The following illustration shows a configuration where a database on an Amazon EC2 instance connects to the replication instance and data is migrated to an Amazon RDS DB instance\.

![\[ AWS Database Migration Service All in one VPC example\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-scenarioAllVPC.png)

The VPC security group used in this configuration must allow ingress on the database port from the replication instance\. You can do this by either ensuring that the security group used by the replication instance has ingress to the endpoints, or by explicitly allowing the private IP address of the replication instance\. 

#### Configuration with Two VPCs<a name="CHAP_ReplicationInstance.VPC.Configurations.ScenarioVPCPeer"></a>

If your source endpoint and target endpoints are in different VPCs, you can create your replication instance in one of the VPCs and then link the two VPCs by using VPC peering\.

A VPC peering connection is a networking connection between two VPCs that enables routing using each VPC’s private IP addresses as if they were in the same network\. We recommend this method for connecting VPCs within a region\. You can create VPC peering connections between your own VPCs or with a VPC in another AWS account within the same AWS region\. For more information about VPC peering, see [VPC Peering](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/vpc-peering.html) in the *Amazon VPC User Guide*\.

The following illustration shows a configuration where the source database on an Amazon EC2 instance in a VPC is connected by using VPC peering to a VPC containing the replication instance and the target database on an Amazon RDS DB instance\.

![\[ AWS Database Migration Service replication instance\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-scenarioVPCPeer.png)

The VPC security groups used in this configuration must allow ingress on the database port from the replication instance\.

#### Configuration for a Network to a VPC Using AWS Direct Connect or a VPN<a name="CHAP_ReplicationInstance.VPC.Configurations.ScenarioDirect"></a>

Remote networks can connect to a VPC using several options such as AWS Direct Connect or a software or hardware VPN connection\. These options are often used to integrate existing on\-site services, such as monitoring, authentication, security, data, or other systems, by extending an internal network into the AWS cloud\. By using this type of network extension, you can seamlessly connect to AWS\-hosted resources such as a VPC\.

The following illustration shows a configuration where the source endpoint is an on\-premises database in a corporate data center\. It is connected by using AWS Direct Connect or a VPN to a VPC that contains the replication instance and a target database on an Amazon RDS DB instance\.

![\[ AWS Database Migration Service replication instance\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-scenarioDirect.png)

 In this configuration, the VPC security group must include a routing rule that sends traffic destined for a specific IP address or range to a host that can bridge traffic from the Amazon VPC into the on\-premises VPN\. In this case, the NAT host includes its own security group settings that must allow traffic from the replication instance’s private IP address or security group into the NAT instance\. 

#### Configuration for a Network to a VPC Using the Internet<a name="CHAP_ReplicationInstance.VPC.Configurations.ScenarioInternet"></a>

If you don't use a VPN or AWS Direct Connect to connect to AWS resources, you can use the Internet to migrate a database to an Amazon EC2 instance or Amazon RDS DB instance\. This configuration involves a public replication instance in a VPC with an Internet gateway that contains the target endpoint and the replication instance\.

![\[ AWS Database Migration Service replication instance\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-scenarioInternet.png)

To add an Internet gateway to your VPC, see [Attaching an Internet Gateway](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_Internet_Gateway.html#Add_IGW_Attach_Gateway) in the *Amazon VPC User Guide*\.

The VPC security group must include routing rules that send traffic not destined for the VPC by default to the Internet gateway\. In this configuration, the connection to the endpoint will appear to come from the public IP address of the replication instance, not the private IP address\. 

#### Configuration with an Amazon RDS DB instance not in a VPC to a DB instance in a VPC Using ClassicLink<a name="CHAP_ReplicationInstance.VPC.Configurations.ClassicLink"></a>

 You can use ClassicLink, in conjunction with a proxy server, to connect an Amazon RDS DB instance that is not in a VPC to a AWS DMS replication server and DB instance that reside in a VPC\. ClassicLink allows you to link an EC2\-Classic DB instance to a VPC in your account, within the same region\. After you've created the link, the source DB instance can communicate with the replication instance inside the VPC using their private IP addresses\. Since the replication instance in the VPC cannot directly access the source DB instance on the EC2\-Classic platform using ClassicLink, you must use a proxy server to connect the source DB instance to the VPC containing the replication instance and target DB instance\. The proxy server uses ClassicLink to connect to the VPC, and port forwarding on the proxy server allows communication between the source DB instance and the target DB instance in the VPC\. 

![\[AWS Database Migration Service using ClassicLink\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-scenarioClassicLink.png)

For step\-by\-step instructions on creating a ClassicLink for use with AWS DMS, see [Using ClassicLink with AWS Database Migration Service](CHAP_Reference.ClassicLink.md)\. 

### Creating a Replication Subnet Group<a name="CHAP_ReplicationInstance.VPC.Subnets"></a>

As part of the network to use for database migration, you need to specify what subnets in your Amazon Virtual Private Cloud \(Amazon VPC\) you plan to use\. A *subnet* is a range of IP addresses in your VPC in a given Availability Zone\. These subnets can be distributed among the Availability Zones for the region where your VPC is located\.

You create a replication instance in a subnet that you select, and you can manage what subnet a source or target endpoint uses by using the AWS DMS console\.

You create a replication subnet group to define which subnets will be used\. You must specify at least one subnet in two different Availability Zones\.

**To create a replication subnet group**

1. Sign in to the AWS Management Console and choose AWS Database Migration Service\. Note that if you are signed in as an AWS Identity and Access Management \(IAM\) user, you must have the appropriate permissions to access AWS DMS\. For more information on the permissions required for database migration, see [IAM Permissions Needed to Use AWS DMS](CHAP_Security.IAMPermissions.md)\.

1. In the navigation pane, choose **Subnet Groups**\.

1. Choose **Create Subnet Group**\. 

1. On the **Edit Replication Subnet Group** page, shown following, specify your replication subnet group information\. The following table describes the settings\.  
![\[ AWS Database Migration Service replication instance\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-repsubnetgroup.png)    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_ReplicationInstance.html)

1. Choose **Add** to add the subnets to the replication subnet group\.

1. Choose **Create**\.

## Setting an Encryption Key for AWS Database Migration Service Replication Instance<a name="CHAP_ReplicationInstance.EncryptionKey"></a>

AWS DMS encrypts the storage used by a replication instance and the endpoint connection information\. To encrypt the storage used by a replication instance, AWS DMS uses a master key that is unique to your AWS account\. You can view and manage this master key with AWS Key Management Service \(AWS KMS\)\. You can use the default master key in your account \(`aws/dms`\) or a custom master key that you create\. If you have an existing AWS KMS encryption key, you can also use that key for encryption\. 

You can specify your own encryption key by supplying a KMS key identifier to encrypt your AWS DMS resources\. When you specify your own encryption key, the user account used to perform the database migration must have access to that key\. For more information on creating your own encryption keys and giving users access to an encryption key, see the *[KMS Developer Guide](http://docs.aws.amazon.com/kms/latest/developerguide/create-keys.html)*\. 

If you don't specify a KMS key identifier, then AWS DMS uses your default encryption key\. KMS creates the default encryption key for AWS DMS for your AWS account\. Your AWS account has a different default encryption key for each AWS region\. 

To manage the keys used for encrypting your AWS DMS resources, you use KMS\. You can find KMS in the AWS Management Console by choosing **Identity & Access Management** on the console home page and then choosing **Encryption Keys** on the navigation pane\. KMS combines secure, highly available hardware and software to provide a key management system scaled for the cloud\. Using KMS, you can create encryption keys and define the policies that control how these keys can be used\. KMS supports AWS CloudTrail, so you can audit key usage to verify that keys are being used appropriately\. Your KMS keys can be used in combination with AWS DMS and supported AWS services such as Amazon RDS, Amazon Simple Storage Service \(Amazon S3\), Amazon Elastic Block Store \(Amazon EBS\), and Amazon Redshift\. 

Once you have created your AWS DMS resources with a specific encryption key, you cannot change the encryption key for those resources\. Make sure to determine your encryption key requirements before you create your AWS DMS resources\. 

## DDL Statements Supported by AWS Database Migration Service<a name="CHAP_ReplicationInstance.SupportedDDL"></a>

You can execute data definition language \(DDL\) statements on the source database during the data migration process\. These statements will be replicated to the target database by the replication server\. 

Supported DDL statements include the following:

+ Create table

+ Drop table

+ Rename table

+ Add column

+ Drop column

+ Rename column

+ Change column data type

For information about which DDL statements are supported for a specific source, see the topic describing that source\.

## Creating a Replication Instance<a name="CHAP_ReplicationInstance.Creating"></a>

Your first task in migrating a database is to create a replication instance that has sufficient storage and processing power to perform the tasks you assign and migrate data from your source database to the target database\. The required size of this instance varies depending on the amount of data you need to migrate and the tasks that you need the instance to perform\. For more information about replication instances, see [AWS Database Migration Service Replication Instance](#CHAP_ReplicationInstance)\. 

The procedure following assumes that you have chosen the AWS DMS console wizard\. Note that you can also do this step by selecting **Replication instances** from the AWS DMS console's navigation pane and then selecting **Create replication instance**\.

**To create a replication instance by using the AWS console**

1. On the **Create replication instance** page, specify your replication instance information\. The following table describes the settings\.   
![\[Create a replication instance\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-gs-wizard2.png)    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_ReplicationInstance.html)

1. Choose the **Advanced** tab, shown following, to set values for network and encryption settings if you need them\. The following table describes the settings\.  
![\[Advanced Tab\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-gs-wizard2A.png)    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_ReplicationInstance.html)

1. Specify the **Maintenance** settings\. The following table describes the settings\. For more information about maintenance settings, see [ AWS DMS Maintenance Window](#CHAP_ReplicationInstance.MaintenanceWindow)  
![\[Maintenance Tab\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-gs-wizard2B.png)    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_ReplicationInstance.html)

1. Choose **Create replication instance**\.

## Modifying a Replication Instance<a name="CHAP_ReplicationInstance.Modifying"></a>

Your can modify the settings for a replication instance to, for example, change the instance class or to increase storage\. 

When you modify a replication instance, you can apply the changes immediately\. To apply changes immediately, you select the **Apply changes immediately** option in the AWS Management Console, you use the `--apply-immediately` parameter when calling the AWS CLI, or you set the `ApplyImmediately` parameter to `true` when using the AWS DMS API\. 

If you don't choose to apply changes immediately, the changes are put into the pending modifications queue\. During the next maintenance window, any pending changes in the queue are applied\. 

**Note**  
If you choose to apply changes immediately, any changes in the pending modifications queue are also applied\. If any of the pending modifications require downtime, choosing apply immediately can cause unexpected downtime\. 

**To modify a replication instance by using the AWS console**

1.  Sign in to the AWS Management Console and select AWS DMS\. 

1. In the navigation pane, click **Replication instances**\.

1. Choose the replication instance you want to modify\. The following table describes the modifications you can make\.     
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_ReplicationInstance.html)