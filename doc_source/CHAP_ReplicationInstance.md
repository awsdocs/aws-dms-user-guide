# Working with a Replication Instance in AWS Database Migration Service<a name="CHAP_ReplicationInstance"></a>

The first step in migrating data using AWS Database Migration Service is to create a replication instance\. An *AWS DMS replication instance* runs on an Amazon Elastic Compute Cloud \(Amazon EC2\) instance\. A replication instance provides high availability and failover support using a Multi\-AZ deployment\. 

In a Multi\-AZ deployment, AWS DMS automatically provisions and maintains a synchronous standby replica of the replication instance in a different Availability Zone\. The primary replication instance is synchronously replicated across Availability Zones to a standby replica\. This approach provides data redundancy, eliminates I/O freezes, and minimizes latency spikes during system backups\.

AWS DMS uses a replication instance that connects to the source data store, reads the source data, and formats the data for consumption by the target data store\. A replication instance also loads the data into the target data store\. Most of this processing happens in memory\. However, large transactions might require some buffering on disk\. Cached transactions and log files are also written to disk\.

Following, you can find out more details about replication instances\.


+ [AWS DMS Replication Instances in Depth](#CHAP_ReplicationInstance.InDepth)
+ [Public and Private Replication Instances](#CHAP_ReplicationInstance.PublicPrivate)
+ [AWS DMS Maintenance](#CHAP_ReplicationInstance.Maintenance)
+ [Working with Replication Engine Versions](#CHAP_ReplicationInstance.EngineVersions)
+ [Setting Up a Network for a Replication Instance](#CHAP_ReplicationInstance.VPC)
+ [Setting an Encryption Key for a Replication Instance](#CHAP_ReplicationInstance.EncryptionKey)
+ [Creating a Replication Instance](#CHAP_ReplicationInstance.Creating)
+ [Modifying a Replication Instance](#CHAP_ReplicationInstance.Modifying)
+ [Rebooting a Replication Instance](#CHAP_ReplicationInstance.Rebooting)
+ [DDL Statements Supported by AWS DMS](#CHAP_ReplicationInstance.SupportedDDL)

## AWS DMS Replication Instances in Depth<a name="CHAP_ReplicationInstance.InDepth"></a>

AWS DMS creates a replication instance that runs on an Amazon Elastic Compute Cloud \(Amazon EC2\) instance in a VPC based on the Amazon Virtual Private Cloud \(Amazon VPC\) service\. You use this replication instance to perform your database migration\. The replication instance provides high availability and failover support using a Multi\-AZ deployment when you select the **Multi\-AZ** option\. 

In a Multi\-AZ deployment, AWS DMS automatically provisions and maintains a synchronous standby replica of the replication instance in a different Availability Zone\. The primary replication instance is synchronously replicated across Availability Zones to a standby replica to provide data redundancy, eliminate I/O freezes, and minimize latency spikes\.

![\[ AWS Database Migration Service replication instance\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-conceptual2.png)

AWS DMS currently supports the T2 and C4 instance classes for replication instances\. The T2 instance classes are low\-cost standard instances designed to provide a baseline level of CPU performance with the ability to burst above the baseline\. They are suitable for developing, configuring, and testing your database migration process, and for periodic data migration tasks that can benefit from the CPU burst capability\. The C4 instance classes are designed to deliver the highest level of processor performance\. They achieve significantly higher packet per second \(PPS\) performance, lower network jitter, and lower network latency\. You should use C4 instance classes if you are migrating large databases and want to minimize the migration time\.

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

#### Changing the Maintenance Window Using the Console<a name="CHAP_ReplicationInstance.MaintenanceWindow.Changing"></a>

You can change the maintenance window time frame using the AWS Management Console, the AWS CLI, or the AWS DMS API\.

**To change the preferred maintenance window using the console**

1.  Sign in to the AWS Management Console and choose AWS DMS\. 

1. In the navigation pane, choose **Replication instances**\.

1. Choose the replication instance you want to modify and choose **Modify**\.

1. Expand the **Maintenance** section and choose a date and time for your maintenance window\.  
![\[modify a replication instance\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-modifymaintenance.png)

1. Choose **Apply changes immediately**\. 

1.  Choose **Modify**\.

#### Changing the Maintenance Window Setting Using the CLI<a name="AdjustingTheMaintenanceWindow.CLI"></a>

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

#### Changing the Maintenance Window Setting Using the API<a name="AdjustingTheMaintenanceWindow.API"></a>

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

#### Effect of Maintenance on Existing Migration Tasks<a name="CHAP_ReplicationInstance.MaintenanceWindow.Effect"></a>

When an AWS DMS migration task is running on an instance, the following events occur when a patch is applied:

+ If the tables in the migration task are in the replicating ongoing changes phase \(CDC\), AWS DMS pauses the task for a moment while the patch is applied\. The migration then continues from where it was interrupted when the patch was applied\.

+ If AWS DMS is migrating a table when the patch is applied, AWS DMS restarts the migration for the table\. 

## Working with Replication Engine Versions<a name="CHAP_ReplicationInstance.EngineVersions"></a>

The *replication engine* is the core AWS DMS software that runs on your replication instance and performs the migration tasks you specify\. AWS periodically releases new versions of the AWS DMS replication engine software, with new features and performance improvements\. Each version of the replication engine software has its own version number, to distinguish it from other versions\.

When you launch a new replication instance, it runs the latest AWS DMS engine version unless you specify otherwise\. For more information, see [Working with a Replication Instance in AWS Database Migration Service](#CHAP_ReplicationInstance)\.

If you have a replication instance that is currently running, you can upgrade it to a more recent engine version\. \(AWS DMS doesn't support engine version downgrades\.\) For more information, including a list of replication engine versions, see the following section\.

### Deprecating a Replication Instance Version<a name="CHAP_ReplicationInstance.EngineVersions.Deprecating"></a>

Occasionally AWS DMS deprecates older versions of the replication instance\. Beginning April 2, 2018, AWS DMS will disable creation of any new replication instance version 1\.9\.0\. This version was initially supported in AWS DMS on March 15, 2016, and has since been replaced by subsequent versions containing improvements to functionality, security, and reliability\. 

Beginning on May 7, 2018, at 0:00 UTC, all DMS replication instances running version 1\.9\.0 will be scheduled for automatic upgrade to the latest available version during the maintenance window specified for each instance\. We recommend that you upgrade your instances before that at your convenience\. 

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

## Setting Up a Network for a Replication Instance<a name="CHAP_ReplicationInstance.VPC"></a>

AWS DMS always creates the replication instance in a VPC based on Amazon Virtual Private Cloud \(Amazon VPC\)\. You specify the VPC where your replication instance is located\. You can use your default VPC for your account and AWS Region, or you can create a new VPC\. The VPC must have two subnets in at least one Availability Zone\.

The Elastic Network Interface \(ENI\) allocated for the replication instance in your VPC must be associated with a security group that has rules that allow all traffic on all ports to leave \(egress\) the VPC\. This approach allows communication from the replication instance to your source and target database endpoints, as long as correct egress rules are enabled on the endpoints\. We recommend that you use the default settings for the endpoints, which allows egress on all ports to all addresses\.

The source and target endpoints access the replication instance that is inside the VPC either by connecting to the VPC or by being inside the VPC\. The database endpoints must include network access control lists \(ACLs\) and security group rules \(if applicable\) that allow incoming access from the replication instance\. Depending on the network configuration you are using, you can use the replication instance VPC security group, the replication instance's private or public IP address, or the NAT gateway's public IP address\. These connections form a network that you use for data migration\.

### Network Configurations for Database Migration<a name="CHAP_ReplicationInstance.VPC.Configurations"></a>

You can use several different network configurations with AWS Database Migration Service\. The following are common configurations for a network used for database migration\.


+ [Configuration with All Database Migration Components in One VPC](#CHAP_ReplicationInstance.VPC.Configurations.ScenarioAllVPC)
+ [Configuration with Two VPCs](#CHAP_ReplicationInstance.VPC.Configurations.ScenarioVPCPeer)
+ [Configuration for a Network to a VPC Using AWS Direct Connect or a VPN](#CHAP_ReplicationInstance.VPC.Configurations.ScenarioDirect)
+ [Configuration for a Network to a VPC Using the Internet](#CHAP_ReplicationInstance.VPC.Configurations.ScenarioInternet)
+ [Configuration with an Amazon RDS DB instance not in a VPC to a DB instance in a VPC Using ClassicLink](#CHAP_ReplicationInstance.VPC.Configurations.ClassicLink)

#### Configuration with All Database Migration Components in One VPC<a name="CHAP_ReplicationInstance.VPC.Configurations.ScenarioAllVPC"></a>

The simplest network for database migration is for the source endpoint, the replication instance, and the target endpoint to all be in the same VPC\. This configuration is a good one if your source and target endpoints are on an Amazon RDS DB instance or an Amazon EC2 instance\.

The following illustration shows a configuration where a database on an Amazon EC2 instance connects to the replication instance and data is migrated to an Amazon RDS DB instance\.

![\[ AWS Database Migration Service All in one VPC example\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-scenarioAllVPC.png)

The VPC security group used in this configuration must allow ingress on the database port from the replication instance\. You can do this by either ensuring that the security group used by the replication instance has ingress to the endpoints, or by explicitly allowing the private IP address of the replication instance\. 

#### Configuration with Two VPCs<a name="CHAP_ReplicationInstance.VPC.Configurations.ScenarioVPCPeer"></a>

If your source endpoint and target endpoints are in different VPCs, you can create your replication instance in one of the VPCs and then link the two VPCs by using VPC peering\.

A VPC peering connection is a networking connection between two VPCs that enables routing using each VPC’s private IP addresses as if they were in the same network\. We recommend this method for connecting VPCs within an AWS Region\. You can create VPC peering connections between your own VPCs or with a VPC in another AWS account within the same AWS Region\. For more information about VPC peering, see [VPC Peering](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/vpc-peering.html) in the *Amazon VPC User Guide*\.

The following illustration shows an example configuration using VPC peering\. Here, the source database on an Amazon EC2 instance in a VPC connects by VPC peering to a VPC\. This VPC contains the replication instance and the target database on an Amazon RDS DB instance\.

![\[ AWS Database Migration Service replication instance\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-scenarioVPCPeer.png)

The VPC security groups used in this configuration must allow ingress on the database port from the replication instance\.

#### Configuration for a Network to a VPC Using AWS Direct Connect or a VPN<a name="CHAP_ReplicationInstance.VPC.Configurations.ScenarioDirect"></a>

Remote networks can connect to a VPC using several options such as AWS Direct Connect or a software or hardware VPN connection\. These options are often used to integrate existing on\-site services, such as monitoring, authentication, security, data, or other systems, by extending an internal network into the AWS cloud\. By using this type of network extension, you can seamlessly connect to AWS\-hosted resources such as a VPC\.

The following illustration shows a configuration where the source endpoint is an on\-premises database in a corporate data center\. It is connected by using AWS Direct Connect or a VPN to a VPC that contains the replication instance and a target database on an Amazon RDS DB instance\.

![\[ AWS Database Migration Service replication instance\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-scenarioDirect.png)

In this configuration, the VPC security group must include a routing rule that sends traffic destined for a specific IP address or range to a host\. This host must be able to bridge traffic from the VPC into the on\-premises VPN\. In this case, the NAT host includes its own security group settings that must allow traffic from the replication instance’s private IP address or security group into the NAT instance\. 

#### Configuration for a Network to a VPC Using the Internet<a name="CHAP_ReplicationInstance.VPC.Configurations.ScenarioInternet"></a>

If you don't use a VPN or AWS Direct Connect to connect to AWS resources, you can use the Internet to migrate a database to an Amazon EC2 instance or Amazon RDS DB instance\. This configuration involves a public replication instance in a VPC with an internet gateway that contains the target endpoint and the replication instance\.

![\[ AWS Database Migration Service replication instance\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-scenarioInternet.png)

To add an Internet gateway to your VPC, see [Attaching an Internet Gateway](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_Internet_Gateway.html#Add_IGW_Attach_Gateway) in the *Amazon VPC User Guide*\.

The VPC security group must include routing rules that send traffic not destined for the VPC by default to the Internet gateway\. In this configuration, the connection to the endpoint appears to come from the public IP address of the replication instance, not the private IP address\. 

#### Configuration with an Amazon RDS DB instance not in a VPC to a DB instance in a VPC Using ClassicLink<a name="CHAP_ReplicationInstance.VPC.Configurations.ClassicLink"></a>

You can use ClassicLink with a proxy server to connect an Amazon RDS DB instance that is not in a VPC to an AWS DMS replication server and DB instance that reside in a VPC\. 

ClassicLink allows you to link an EC2\-Classic DB instance to a VPC in your account, within the same AWS Region\. After you've created the link, the source DB instance can communicate with the replication instance inside the VPC using their private IP addresses\. 

Because the replication instance in the VPC cannot directly access the source DB instance on the EC2\-Classic platform using ClassicLink, you must use a proxy server\. The proxy server connects the source DB instance to the VPC containing the replication instance and target DB instance\. The proxy server uses ClassicLink to connect to the VPC\. Port forwarding on the proxy server allows communication between the source DB instance and the target DB instance in the VPC\. 

![\[AWS Database Migration Service using ClassicLink\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-scenarioClassicLink.png)

For step\-by\-step instructions on creating a ClassicLink for use with AWS DMS, see [Using ClassicLink with AWS Database Migration Service](CHAP_Reference.ClassicLink.md)\. 

### Creating a Replication Subnet Group<a name="CHAP_ReplicationInstance.VPC.Subnets"></a>

As part of the network to use for database migration, you need to specify what subnets in your Amazon Virtual Private Cloud \(Amazon VPC\) you plan to use\. A *subnet* is a range of IP addresses in your VPC in a given Availability Zone\. These subnets can be distributed among the Availability Zones for the AWS Region where your VPC is located\.

You create a replication instance in a subnet that you select, and you can manage what subnet a source or target endpoint uses by using the AWS DMS console\.

You create a replication subnet group to define which subnets to use\. You must specify at least one subnet in two different Availability Zones\.

**To create a replication subnet group**

1. Sign in to the AWS Management Console and choose AWS Database Migration Service\. If you are signed in as an AWS Identity and Access Management \(IAM\) user, you must have the appropriate permissions to access AWS DMS\. For more information on the permissions required for database migration, see [IAM Permissions Needed to Use AWS DMS](CHAP_Security.IAMPermissions.md)\.

1. In the navigation pane, choose **Subnet Groups**\.

1. Choose **Create Subnet Group**\. 

1. On the **Edit Replication Subnet Group** page, shown following, specify your replication subnet group information\. The following table describes the settings\.  
![\[ AWS Database Migration Service replication instance\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-repsubnetgroup.png)    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_ReplicationInstance.html)

1. Choose **Add** to add the subnets to the replication subnet group\.

1. Choose **Create**\.

## Setting an Encryption Key for a Replication Instance<a name="CHAP_ReplicationInstance.EncryptionKey"></a>

AWS DMS encrypts the storage used by a replication instance and the endpoint connection information\. To encrypt the storage used by a replication instance, AWS DMS uses a master key that is unique to your AWS account\. You can view and manage this master key with AWS Key Management Service \(AWS KMS\)\. You can use the default master key in your account \(`aws/dms`\) or a custom master key that you create\. If you have an existing AWS KMS encryption key, you can also use that key for encryption\. 

You can specify your own encryption key by supplying a KMS key identifier to encrypt your AWS DMS resources\. When you specify your own encryption key, the user account used to perform the database migration must have access to that key\. For more information on creating your own encryption keys and giving users access to an encryption key, see the *[AWS KMS Developer Guide](http://docs.aws.amazon.com/kms/latest/developerguide/create-keys.html)*\. 

If you don't specify a KMS key identifier, then AWS DMS uses your default encryption key\. KMS creates the default encryption key for AWS DMS for your AWS account\. Your AWS account has a different default encryption key for each AWS Region\. 

To manage the keys used for encrypting your AWS DMS resources, you use KMS\. You can find KMS in the AWS Management Console by choosing **Identity & Access Management** on the console home page and then choosing **Encryption Keys** on the navigation pane\. 

KMS combines secure, highly available hardware and software to provide a key management system scaled for the cloud\. Using KMS, you can create encryption keys and define the policies that control how these keys can be used\. KMS supports AWS CloudTrail, so you can audit key usage to verify that keys are being used appropriately\. Your KMS keys can be used in combination with AWS DMS and supported AWS services such as Amazon RDS, S3, Amazon Elastic Block Store \(Amazon EBS\), and Amazon Redshift\. 

When you have created your AWS DMS resources with a specific encryption key, you can't change the encryption key for those resources\. Make sure to determine your encryption key requirements before you create your AWS DMS resources\. 

## Creating a Replication Instance<a name="CHAP_ReplicationInstance.Creating"></a>

Your first task in migrating a database is to create a replication instance that has sufficient storage and processing power to perform the tasks you assign and migrate data from your source database to the target database\. The required size of this instance varies depending on the amount of data you need to migrate and the tasks that you need the instance to perform\. For more information about replication instances, see [Working with a Replication Instance in AWS Database Migration Service](#CHAP_ReplicationInstance)\. 

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

**To reboot a replication instance using the AWS console**

1.  Sign in to the AWS Management Console and select AWS DMS\. 

1. In the navigation pane, choose **Replication instances**\.

1. Choose the replication instance you want to reboot\. 

1. Choose **Reboot**\.

1. In the **Reboot replication instance** dialog box, choose **Reboot With Failover?** if you have configured your replication instance for Multi\-AZ deployment and you want to fail over to another AWS Availability Zone\.

1. Choose **Reboot**\.

### Rebooting a Replication Instance Using the CLI<a name="AdjustingTheMaintenanceWindow.CLI"></a>

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

### Rebooting a Replication Instance Using the API<a name="AdjustingTheMaintenanceWindow.API"></a>

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