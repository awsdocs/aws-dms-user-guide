# Step 1: Create an instance profile<a name="getting-started-instance"></a>

Before you create an instance profile, configure a subnet group for your instance profile\. A *subnet* is a range of IP addresses in your VPC\. A *subnet group* includes subnets from different Availability Zones which your instance profile can use\.

**To create a subnet group**

1. Sign in to the AWS Management Console and open the AWS DMS console at [https://console\.aws\.amazon\.com/dms/v2/](https://console.aws.amazon.com/dms/v2/)\.

1. In the navigation pane, choose **Subnet groups**, and then choose **Create subnet group**\.

1. For **Name**, enter a unique name of your subnet group\.

1. For **Description**, enter a brief description of your subnet group\.

1. For **VPC**, choose a VPC that has at least one subnet in at least two Availability Zones\.

1. For **Add subnets**, choose subnets to include in the subnet group\. You must choose subnets in at least two Availability Zones\.

   To connect to Amazon RDS databases, add public subnets into your subnet group\. To connect to on\-premises databases, use private subnets into your subnet group\.

1. Choose **Create subnet group**\.

You can create an instance profile as described in the following procedure\. In this instance profile, you specify network and security settings for your DMS Schema Conversion project\.

**To create an instance profile**

1. Sign in to the AWS Management Console and open the AWS DMS console at [https://console\.aws\.amazon\.com/dms/v2/](https://console.aws.amazon.com/dms/v2/)\.

1. In the navigation pane, choose **Instance profiles**, and then choose **Create instance profile**\.

1. For **Name**, enter a unique name for your instance profile\. For example, enter **sc\-instance**\.

1. For **Network type**, choose **IPv4** to create an instance profile that supports only IPv4 addressing\. To create an instance profile that supports IPv4 and IPv6 addressing, choose **Dual\-stack mode**\.

1. For **Virtual private cloud \(VPC\)**, choose the VPC that you created in the prerequisites step\.

1. For **Subnet group**, choose the subnet group for your instance profile\. To connect to Amazon RDS databases, use a subnet group that includes public subnets\. To connect to on\-premises databases, use a subnet group that includes private subnets\.

1. For **S3 bucket** under **Schema conversion settings \- optional**, choose an Amazon S3 bucket that you created in the prerequisites step\.

1. For **IAM role**, choose the AWS Identity and Access Management \(IAM\) role that grants access to Amazon S3\. You created this role in the prerequisites step\.

1. Choose **Create instance profile**\.

To create a migration project, use this instance profile\.