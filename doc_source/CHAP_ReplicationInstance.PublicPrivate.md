# Public and private replication instances<a name="CHAP_ReplicationInstance.PublicPrivate"></a>

You can specify whether a replication instance has a public or private IP address that the instance uses to connect to the source and target databases\. 

A *private replication instance* has a private IP address that you can't access outside the replication network\. You use a private instance when both source and target databases are in the same network that is connected to the replication instance's VPC\. The network can be connected to the VPC by using a VPN, AWS Direct Connect, or VPC peering\.

A *VPC peering* connection is a networking connection between two VPCs that enables routing using each VPC's private IP addresses as if they were in the same network\. For more information about VPC peering, see [VPC peering](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-peering.html) in the *Amazon VPC User Guide*\.

A *public replication instance* can use the replication instance VPC security group, and the replication instance's public IP address, or the NAT gateway's public IP address\. These connections form a network that you use for data migration\.