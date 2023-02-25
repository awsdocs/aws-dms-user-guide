# Resilience in AWS Database Migration Service<a name="disaster-recovery-resiliency"></a>

The AWS global infrastructure is built around AWS Regions and Availability Zones\. AWS Regions provide multiple physically separated and isolated Availability Zones, which are connected with low\-latency, high\-throughput, and highly redundant networking\. With Availability Zones, you can design and operate applications and databases that automatically fail over between Availability Zones without interruption\. Availability Zones are more highly available, fault tolerant, and scalable than traditional single or multiple data center infrastructures\. 

For more information about AWS Regions and Availability Zones, see [AWS global infrastructure](http://aws.amazon.com/about-aws/global-infrastructure/)\.

In addition to the AWS global infrastructure, AWS DMS provides high availability and failover support for a replication instance using a Multi\-AZ deployment when you choose the **Multi\-AZ** option\.

In a Multi\-AZ deployment, AWS DMS automatically provisions and maintains a standby replica of the replication instance in a different Availability Zone\. The primary replication instance is synchronously replicated to the standby replica\. If the primary replication instance fails or becomes unresponsive, the standby resumes any running tasks with minimal interruption\. Because the primary is constantly replicating its state to the standby, Multi\-AZ deployment does incur some performance overhead\.

For more information on working with Multi\-AZ deployments, see [Working with an AWS DMS replication instance](CHAP_ReplicationInstance.md)\.