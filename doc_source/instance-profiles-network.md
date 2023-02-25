# Setting up a network for DMS Schema Conversion<a name="instance-profiles-network"></a>

DMS Schema Conversion creates a schema conversion instance in a virtual private cloud \(VPC\) based on the Amazon VPC service\. When you create your instance profile, you specify the VPC to use\. You can use your default VPC for your account and AWS Region, or you can create a new VPC\.

You can use different network configurations to set up interaction for your source and target databases with DMS Schema Conversion\. These configurations depend on the location of your source data provider and your network settings\. The following topics provide descriptions of common network configurations\.

## Using a single VPC for source and target data providers<a name="instance-profiles-network-one-vpc"></a>

The simplest network configuration for DMS Schema Conversion is a single VPC configuration\. Here, your source data provider, instance profile, and the target data provider are all located in the same VPC\. You can use this configuration to convert your source database on an Amazon EC2 instance\.

To use this configuration, make sure that the VPC security group used by the instance profile has access to the data providers\. For example, you can allow either a VPC Classless Inter\-Domain Routing \(CIDR\) range or the Elastic IP address for your Network Address Translation \(NAT\) gateway\.

## Using multiple VPCs for source and target data providers<a name="instance-profiles-network-multiple-vpc"></a>

If your source and target data providers are in different VPCs, you can create your instance profile in one of the VPCs\. You can then link these two VPCs by using VPC peering\. You can use this configuration to convert your source database on an Amazon EC2 instance\.

A *VPC peering connection* is a networking connection between two VPCs that activates routing using the private IP address of each VPC\\ as if they were in the same network\. You can create a VPC peering connection between your own VPCs, with a VPC in another AWS account, or with a VPC in a different AWS Region\. For more information about VPC peering, see [VPC peering](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-peering.html) in the *Amazon VPC User Guide*\.

To implement VPC peering, follow the instructions in [Work with VPC peering connections](https://docs.aws.amazon.com/vpc/latest/peering/working-with-vpc-peering.html) in the *Amazon VPC User Guide*\. Make sure that the route table of one VPC contains the CIDR block of the other\. For example, suppose that VPC A is using destination 10\.0\.0\.0/16 and VPC B is using destination 172\.31\.0\.0\. In this case, the route table of VPC A should contain 172\.31\.0\.0, and the route table of VPC B must contain 10\.0\.0\.0/16\. For more detailed information, see [Update your route tables for VPC peering connection](https://docs.aws.amazon.com/vpc/latest/peering/vpc-peering-routing.html) in the *Amazon VPC Peering Guide*\. 

## Using AWS Direct Connect or a VPN to configure a network to a VPC<a name="instance-profiles-network-vpn"></a>

Remote networks can connect to a VPC using several options, such as AWS Direct Connect or a software or hardware VPN connection\. You can use these options to integrate existing on\-site services by extending an internal network into the AWS Cloud\. You might integrate on\-site services such as monitoring, authentication, security, data, or other systems\. By using this type of network extension, you can seamlessly connect on\-site services to resources hosted by AWS, such as a VPC\. You can use this configuration to convert your source on\-premises database\.

In this configuration, the VPC security group must include a routing rule that sends traffic destined for a VPC CIDR range or specific IP address to a host\. This host must be able to bridge traffic from the VPC into the on\-premises VPN\. In this case, the NAT host includes its own security group settings\. These settings must allow traffic from your VPC CIDR range or security group into the NAT instance\. For more information, see [Create a Site\-to\-Site VPN connection](https://docs.aws.amazon.com/vpn/latest/s2svpn/SetUpVPNConnections.html#vpn-create-vpn-connection) in the *AWS Site\-to\-Site VPN User Guide*\.

## Using an internet connection to a VPC<a name="instance-profiles-network-internet"></a>

If you don't use a VPN or AWS Direct Connect to connect to AWS resources, you can use an internet connection\. This configuration involves a private subnet in a VPC with an internet gateway\. The gateway contains the target data provider and the instance profile\. You can use this configuration to convert your source on\-premises database\.

To add an internet gateway to your VPC, see [Attaching an internet gateway](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Internet_Gateway.html#Add_IGW_Attach_Gateway) in the *Amazon VPC User Guide*\.

The VPC route table must include routing rules that send traffic *not* destined for the VPC by default to the internet gateway\. In this configuration, the connection to the data provider appears to come from the public IP address of your NAT gateway\. For more information, see [VPC Route Tables](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Route_Tables.html) in the *Amazon VPC User Guide*\.