# Working with an AWS DMS replication instance<a name="CHAP_ReplicationInstance"></a>

When you create an AWS DMS replication instance, AWS DMS creates it on an Amazon EC2 instance in a virtual private cloud \(VPC\) based on the Amazon VPC service\. You use this replication instance to perform your database migration\. By using a replication instance, you can get high availability and failover support with a Multi\-AZ deployment when you choose the **Multi\-AZ** option\. 

In a Multi\-AZ deployment, AWS DMS automatically provisions and maintains a synchronous standby replica of the replication instance in a different Availability Zone\. The primary replication instance is synchronously replicated across Availability Zones to a standby replica\. This approach provides data redundancy, eliminates I/O freezes, and minimizes latency spikes\.

![\[AWS Database Migration Service replication instance\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-conceptual2.png)

AWS DMS uses a replication instance to connect to your source data store, read the source data, and format the data for consumption by the target data store\. A replication instance also loads the data into the target data store\. Most of this processing happens in memory\. However, large transactions might require some buffering on disk\. Cached transactions and log files are also written to disk\.

You can create an AWS DMS replication instance in the following AWS Regions\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_ReplicationInstance.html)

AWS DMS supports a special AWS Region called AWS GovCloud \(US\) that is designed to allow US government agencies and customers to move sensitive workloads into the cloud\. AWS GovCloud \(US\) addresses the US government's specific regulatory and compliance requirements\. For more information about AWS GovCloud \(US\), see [What is AWS GovCloud \(US\)?](http://docs.aws.amazon.com/govcloud-us/latest/UserGuide/whatis.html)

Following, you can find out more details about replication instances\.

**Topics**
+ [Choosing the right AWS DMS replication instance for your migration](CHAP_ReplicationInstance.Types.md)
+ [Choosing the optimum size for a replication instance](CHAP_BestPractices.SizingReplicationInstance.md)
+ [Public and private replication instances](CHAP_ReplicationInstance.PublicPrivate.md)
+ [Working with replication engine versions](CHAP_ReplicationInstance.EngineVersions.md)
+ [Setting up a network for a replication instance](CHAP_ReplicationInstance.VPC.md)
+ [Setting an encryption key for a replication instance](CHAP_ReplicationInstance.EncryptionKey.md)
+ [Creating a replication instance](CHAP_ReplicationInstance.Creating.md)
+ [Modifying a replication instance](CHAP_ReplicationInstance.Modifying.md)
+ [Rebooting a replication instance](CHAP_ReplicationInstance.Rebooting.md)
+ [Deleting a replication instance](CHAP_ReplicationInstance.Deleting.md)
+ [Working with the AWS DMS maintenance window](CHAP_ReplicationInstance.MaintenanceWindow.md)