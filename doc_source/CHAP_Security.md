# Security in AWS Database Migration Service<a name="CHAP_Security"></a>

Cloud security at AWS is the highest priority\. As an AWS customer, you benefit from a data center and network architecture that are built to meet the requirements of the most security\-sensitive organizations\.

Security is a shared responsibility between AWS and you\. The [shared responsibility model](https://aws.amazon.com/compliance/shared-responsibility-model/) describes this as security *of* the cloud and security *in* the cloud:
+ **Security of the cloud** – AWS is responsible for protecting the infrastructure that runs AWS services in the AWS Cloud\. AWS also provides you with services that you can use securely\. Third\-party auditors regularly test and verify the effectiveness of our security as part of the [AWS compliance programs](https://aws.amazon.com/compliance/programs/)\. To learn about the compliance programs that apply to AWS DMS, see [AWS services in scope by compliance program](https://aws.amazon.com/compliance/services-in-scope/)\.
+ **Security in the cloud** – Your responsibility is determined by the AWS service that you use\. You are also responsible for other factors including the sensitivity of your data, your organization's requirements, and applicable laws and regulations\. 

This documentation helps you understand how to apply the shared responsibility model when using AWS DMS\. The following topics show you how to configure AWS DMS to meet your security and compliance objectives\. You also learn how to use other AWS services that help you monitor and secure your AWS DMS resources\. 

You can manage access to your AWS DMS resources and your databases \(DBs\)\. The method you use to manage access depends on the replication task you need to perform with AWS DMS: 
+ Use AWS Identity and Access Management \(IAM\) policies to assign permissions that determine who is allowed to manage AWS DMS resources\. AWS DMS requires that you have the appropriate permissions if you sign in as an IAM user\. For example, you can use IAM to determine who is allowed to create, describe, modify, and delete DB instances and clusters, tag resources, or modify security groups\. For more information about IAM and using it with AWS DMS, see [Identity and access management for AWS Database Migration Service](security-iam.md)\.
+ AWS DMS uses Secure Sockets Layer \(SSL\) for your endpoint connections with Transport Layer Security \(TLS\)\. For more information about using SSL/TLS with AWS DMS, see [Using SSL with AWS Database Migration Service](CHAP_Security.SSL.md)\.
+ AWS DMS uses AWS Key Management Service \(AWS KMS\) encryption keys to encrypt the storage used by your replication instance and its endpoint connection information\. AWS DMS also uses AWS KMS encryption keys to secure your target data at rest for Amazon S3 and Amazon Redshift target endpoints\. For more information, see [Setting an encryption key and specifying AWS KMS permissions](#CHAP_Security.EncryptionKey)\.
+ AWS DMS always creates your replication instance in a virtual private cloud \(VPC\) based on the Amazon VPC service for the greatest possible network access control\. For your DB instances and instance clusters, use the same VPC as your replication instance, or additional VPCs to match this level of access control\. Each Amazon VPC that you use must be associated with a security group that has rules that allow all traffic on all ports to leave \(egress\) the VPC\. This approach allows communication from the replication instance to your source and target database endpoints, as long as correct ingress is enabled on those endpoints\. 

  For more information about available network configurations for AWS DMS, see [Setting up a network for a replication instance](CHAP_ReplicationInstance.VPC.md)\. For more information about creating a DB instance or instance cluster in a VPC, see the security and cluster management documentation for your Amazon databases at [AWS documentation](https://docs.aws.amazon.com/index.html?nc2=h_ql_doc_do_v)\. For more information about network configurations that AWS DMS supports, see [Setting up a network for a replication instance](CHAP_ReplicationInstance.VPC.md)\.
+ To view database migration logs, you need the appropriate Amazon CloudWatch Logs permissions for the IAM role you are using\. For more information about logging for AWS DMS, see [Monitoring replication tasks using Amazon CloudWatch](CHAP_Monitoring.md#CHAP_Monitoring.CloudWatch)\.

**Topics**
+ [Data protection in AWS Database Migration Service](CHAP_Security.DataProtection.md)
+ [Identity and access management for AWS Database Migration Service](security-iam.md)
+ [Compliance validation for AWS Database Migration Service](dms-compliance.md)
+ [Resilience in AWS Database Migration Service](disaster-recovery-resiliency.md)
+ [Infrastructure security in AWS Database Migration Service](infrastructure-security.md)
+ [Fine\-grained access control using resource names and tags](CHAP_Security.FineGrainedAccess.md)
+ [Setting an encryption key and specifying AWS KMS permissions](#CHAP_Security.EncryptionKey)
+ [Network security for AWS Database Migration Service](#CHAP_Security.Network)
+ [Using SSL with AWS Database Migration Service](CHAP_Security.SSL.md)
+ [Changing the database password](#CHAP_Security.ChangingDBPassword)

## Setting an encryption key and specifying AWS KMS permissions<a name="CHAP_Security.EncryptionKey"></a>

AWS DMS encrypts the storage used by a replication instance and the endpoint connection information\. To encrypt the storage used by a replication instance, AWS DMS uses an AWS Key Management Service \(AWS KMS\) key that is unique to your AWS account\. You can view and manage this key with AWS KMS\. You can use the default KMS key in your account \(`aws/dms`\) or you can create a custom KMS key\. If you have an existing KMS key, you can also use that key for encryption\.

**Note**  
Any custom or existing AWS KMS key that you use as an encryption key must be a symmetric key\. AWS DMS does not support the use of asymmetric encryption keys\. For more information on symmetric and asymmetric encryption keys, see [https://docs.aws.amazon.com/kms/latest/developerguide/symmetric-asymmetric.html](https://docs.aws.amazon.com/kms/latest/developerguide/symmetric-asymmetric.html) in the *AWS Key Management Service Developer Guide*\.

The default KMS key \(`aws/dms`\) is created when you first launch a replication instance, if you haven't selected a custom KMS key from the **Advanced** section of the **Create Replication Instance** page\. If you use the default KMS key, the only permissions you need to grant to the IAM user account you are using for migration are `kms:ListAliases` and `kms:DescribeKey`\. For more information about using the default KMS key, see [IAM permissions needed to use AWS DMS](security-iam.md#CHAP_Security.IAMPermissions)\. 

To use a custom KMS key, assign permissions for the custom KMS key using one of the following options:
+ Add the IAM user account used for the migration as a key administrator or key user for the AWS KMS custom key\. Doing this ensures that necessary AWS KMS permissions are granted to the IAM user account\. This action is in addition to the IAM permissions that you grant to the IAM user account to use AWS DMS\. For more information about granting permissions to a key user, see [ Allows key users to use the KMS key](https://docs.aws.amazon.com/kms/latest/developerguide/key-policies.html#key-policy-default-allow-users) in the *AWS Key Management Service Developer Guide\.*
+ If you don't want to add the IAM user account as a key administrator or key user for your custom KMS key, then add the following additional permissions to the IAM permissions that you must grant to the IAM user account to use AWS DMS\. 

  ```
  {
              "Effect": "Allow",
              "Action": [
                  "kms:ListAliases",
                  "kms:DescribeKey",
                  "kms:CreateGrant",
                  "kms:Encrypt",
                  "kms:ReEncrypt*"
              ],
              "Resource": "*"
          },
  ```

AWS DMS also works with KMS key aliases\. For more information about creating your own AWS KMS keys and giving users access to a KMS key, see the *[AWS KMS Developer Guide](https://docs.aws.amazon.com/kms/latest/developerguide/create-keys.html)*\. 

If you don't specify a KMS key identifier, then AWS DMS uses your default encryption key\. AWS KMS creates the default encryption key for AWS DMS for your AWS account\. Your AWS account has a different default encryption key for each AWS Region\. 

To manage the AWS KMS keys used for encrypting your AWS DMS resources, use the AWS Key Management Service\. AWS KMS combines secure, highly available hardware and software to provide a key management system scaled for the cloud\. Using AWS KMS, you can create encryption keys and define the policies that control how these keys can be used\.

**You can find AWS KMS in the AWS Management Console**

1. Sign in to the AWS Management Console and open the AWS Key Management Service \(AWS KMS\) console at [https://console\.aws\.amazon\.com/kms](https://console.aws.amazon.com/kms)\.

1. To change the AWS Region, use the Region selector in the upper\-right corner of the page\.

1. Choose one of the following options to work with AWS KMS keys:
   + To view the keys in your account that AWS creates and manages for you, in the navigation pane, choose **AWS managed keys**\.
   + To view the keys in your account that you create and manage, in the navigation pane choose **Customer managed keys**\.

AWS KMS supports AWS CloudTrail, so you can audit key usage to verify that keys are being used appropriately\. Your AWS KMS keys can be used in combination with AWS DMS and supported AWS services such as Amazon RDS, Amazon S3, Amazon Redshift, and Amazon EBS\. 

You can also create custom AWS KMS keys specifically to encrypt target data for the following AWS DMS endpoints:
+ Amazon Redshift – For more information, see [Creating and using AWS KMS keys to encrypt Amazon Redshift target data](CHAP_Target.Redshift.md#CHAP_Target.Redshift.KMSKeys)\.
+ Amazon S3 – For more information, see [Creating AWS KMS keys to encrypt Amazon S3 target objects](CHAP_Target.S3.md#CHAP_Target.S3.KMSKeys)\.

After you have created your AWS DMS resources with a KMS key, you can't change the encryption key for those resources\. Make sure to determine your encryption key requirements before you create your AWS DMS resources\. 

## Network security for AWS Database Migration Service<a name="CHAP_Security.Network"></a>

The security requirements for the network you create when using AWS Database Migration Service depend on how you configure the network\. The general rules for network security for AWS DMS are as follows: 
+ The replication instance must have access to the source and target endpoints\. The security group for the replication instance must have network ACLs or rules that allow egress from the instance out on the database port to the database endpoints\.
+ Database endpoints must include network ACLs and security group rules that allow incoming access from the replication instance\. You can achieve this using the replication instance's security group, the private IP address, the public IP address, or the NAT gateway's public address, depending on your configuration\. 
+ If your network uses a VPN tunnel, the Amazon EC2 instance acting as the NAT gateway must use a security group that has rules that allow the replication instance to send traffic through it\.

By default, the VPC security group used by the AWS DMS replication instance has rules that allow egress to 0\.0\.0\.0/0 on all ports\. If you modify this security group or use your own security group, egress must, at a minimum, be permitted to the source and target endpoints on the respective database ports\.

The network configurations that you can use for database migration each require specific security considerations:
+  [ Configuration with all database migration components in one VPC](CHAP_ReplicationInstance.VPC.md#CHAP_ReplicationInstance.VPC.Configurations.ScenarioAllVPC) – The security group used by the endpoints must allow ingress on the database port from the replication instance\. Ensure that the security group used by the replication instance has ingress to the endpoints, or you can create a rule in the security group used by the endpoints that allows the private IP address of the replication instance access\. 
+  [ Configuration with multiple VPCs](CHAP_ReplicationInstance.VPC.md#CHAP_ReplicationInstance.VPC.Configurations.ScenarioVPCPeer) – The security group used by the replication instance must have a rule for the VPC range and the DB port on the database\. 
+  [ Configuration for a network to a VPC using AWS Direct Connect or a VPN](CHAP_ReplicationInstance.VPC.md#CHAP_ReplicationInstance.VPC.Configurations.ScenarioDirect) – a VPN tunnel allowing traffic to tunnel from the VPC into an on\- premises VPN\. In this configuration, the VPC includes a routing rule that sends traffic destined for a specific IP address or range to a host that can bridge traffic from the VPC into the on\-premises VPN\. If this case, the NAT host includes its own Security Group settings that must allow traffic from the Replication Instance's private IP address or security group into the NAT instance\. 
+  [ Configuration for a network to a VPC using the internet](CHAP_ReplicationInstance.VPC.md#CHAP_ReplicationInstance.VPC.Configurations.ScenarioInternet) – The VPC security group must include routing rules that send traffic not destined for the VPC to the Internet gateway\. In this configuration, the connection to the endpoint appears to come from the public IP address on the replication instance\. 
+  [Configuration with an RDS DB instance not in a VPC to a DB instance in a VPC using ClassicLink](CHAP_ReplicationInstance.VPC.md#CHAP_ReplicationInstance.VPC.Configurations.ClassicLink) – When the source or target Amazon RDS DB instance is not in a VPC and does not share a security group with the VPC where the replication instance is located, you can setup a proxy server and use ClassicLink to connect the source and target databases\. 
+  **Source endpoint is outside the VPC used by the replication instance and uses a NAT gateway** – You can configure a network address translation \(NAT\) gateway using a single Elastic IP address bound to a single Elastic network interface\. This Elastic network interface then receives a NAT identifier \(nat\-\#\#\#\#\#\)\. If the VPC includes a default route to that NAT gateway instead of the internet gateway, the replication instance instead appears to contact the database endpoint using the public IP address of the internet gateway\. In this case, the ingress to the database endpoint outside the VPC needs to allow ingress from the NAT address instead of the replication instance's public IP address\. 
+ **VPC endpoints for non\-RDBMS engines** – AWS DMS doesn’t support VPC endpoints for non\-RDBMS engines\.

## Changing the database password<a name="CHAP_Security.ChangingDBPassword"></a>

In most situations, changing the database password for your source or target endpoint is straightforward\. If you need to change the database password for an endpoint that you are currently using in a migration or replication task, the process is slightly more complex\. The procedure following shows how to do this\.

**To change the database password for an endpoint in a migration or replication task**

1. Sign in to the AWS Management Console and open the AWS DMS console at [https://console\.aws\.amazon\.com/dms/v2/](https://console.aws.amazon.com/dms/v2/)\. 

   If you're signed in as an IAM user, make sure that you have the appropriate permissions to access AWS DMS\. For more information about the permissions required, see [IAM permissions needed to use AWS DMS](security-iam.md#CHAP_Security.IAMPermissions)\.

1. In the navigation pane, choose **Tasks**\.

1. Choose the task that uses the endpoint you want to change the database password for, and then choose **Stop**\.

1. While the task is stopped, you can change the password of the database for the endpoint using the native tools you use to work with the database\.

1. Return to the DMS Management Console and choose **Endpoints** from the navigation pane\.

1. Choose the endpoint for the database you changed the password for, and then choose **Modify**\.

1. Type the new password in the **Password** box, and then choose **Modify**\.

1. Choose **Tasks** from the navigation pane\.

1. Choose the task that you stopped previously, and choose **Start/Resume**\.

1. Choose either **Start** or **Resume**, depending on how you want to continue the task, and then choose **Start task**\.