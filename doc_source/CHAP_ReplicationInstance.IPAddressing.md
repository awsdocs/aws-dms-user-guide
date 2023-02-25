# IP addressing and network types<a name="CHAP_ReplicationInstance.IPAddressing"></a>

AWS DMS always creates your replication instance in an Amazon Virtual Private Cloud \(VPC\)\. When you create your VPC, you can determine the IP addressing to use: either IPv4 or IPv6, or both\. Then, when you create or modify a replication instance, you can specify use of an IPv4 address protocol or an IPv6 address protocol using *dual\-stack mode*\. 

**IPv4 addresses**

When you create a VPC, you can specify a range of IPv4 addresses for the VPC in the form of a Classless Inter\-Domain Routing \(CIDR\) block, such as 10\.0\.0\.0/16\. A subnet group defines the range of IP addresses in this CIDR block\. These IP addresses can be private or public\.

A private IPv4 address is an IP address that's not reachable over the internet\. You can use private IPv4 addresses for communication between your replication instance and other resources, such as Amazon EC2 instances, in the same VPC\. Each replication instance has a private IP address for communication in the VPC\.

A public IP address is an IPv4 address that is reachable from the internet\. You can use public addresses for communication between your replication instance and resources on the internet\. You control whether your replication instance receives a public IP address\.

**Dual\-stack mode and IPv6 addresses**

When you have resources that must communicate with your replication instance over IPv6, use *dual\-stack mode*\. To use dual\-stack mode, make sure that each subnet in the subnet group that you associate with the replication instance has an IPv6 CIDR block associated with it\. You can create a new replication subnet group or modify an existing replication subnet group to meet this requirement\. Each IPv6 address is globally unique\. The IPv6 CIDR block for your VPC is automatically assigned from the Amazon pool of IPv6 addresses\. You can't choose the range yourself\. 

DMS disables Internet Gateway access for IPv6 endpoints of private dual\-stack mode replication instances\. DMS does this to ensure that your IPv6 endpoints are private and can only be accessed from within your VPC\.

You can use the AWS DMS Console to create or modify a replication instance, and specify dual\-stack mode in the **Network type** section\. The following image shows the **Network type** section in the console\.

![\[AWS Database Migration Service Network Type\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-network-type.png)

**References**
+ For mode information about IPv4 and IPv6 addresses, see [IP Addressing](https://docs.aws.amazon.com/vpc/latest/userguide/how-it-works.html#vpc-ip-addressing) in the *Amazon VPC User Guide*\.
+ For more information about creating a replication instance using dual\-stack mode, see [Creating a replication instance](CHAP_ReplicationInstance.Creating.md)\. 
+ For mode information about modifying a replication instance, see [ Modifying a replication instance](CHAP_ReplicationInstance.Modifying.md)\. 