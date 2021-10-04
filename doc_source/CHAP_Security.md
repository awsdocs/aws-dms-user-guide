# Security in AWS Database Migration Service<a name="CHAP_Security"></a>

Cloud security at AWS is the highest priority\. As an AWS customer, you benefit from a data center and network architecture that are built to meet the requirements of the most security\-sensitive organizations\.

Security is a shared responsibility between AWS and you\. The [shared responsibility model](https://aws.amazon.com/compliance/shared-responsibility-model/) describes this as security *of* the cloud and security *in* the cloud:
+ **Security of the cloud** – AWS is responsible for protecting the infrastructure that runs AWS services in the AWS Cloud\. AWS also provides you with services that you can use securely\. Third\-party auditors regularly test and verify the effectiveness of our security as part of the [AWS compliance programs](https://aws.amazon.com/compliance/programs/)\. To learn about the compliance programs that apply to AWS DMS, see [AWS services in scope by compliance program](https://aws.amazon.com/compliance/services-in-scope/)\.
+ **Security in the cloud** – Your responsibility is determined by the AWS service that you use\. You are also responsible for other factors including the sensitivity of your data, your organization's requirements, and applicable laws and regulations\. 

This documentation helps you understand how to apply the shared responsibility model when using AWS DMS\. The following topics show you how to configure AWS DMS to meet your security and compliance objectives\. You also learn how to use other AWS services that help you monitor and secure your AWS DMS resources\. 

You can manage access to your AWS DMS resources and your databases \(DBs\)\. The method you use to manage access depends on the replication task you need to perform with AWS DMS: 
+ Use AWS Identity and Access Management \(IAM\) policies to assign permissions that determine who is allowed to manage AWS DMS resources\. AWS DMS requires that you have the appropriate permissions if you sign in as an IAM user\. For example, you can use IAM to determine who is allowed to create, describe, modify, and delete DB instances and clusters, tag resources, or modify security groups\. For more information about IAM and using it with AWS DMS, see [Identity and access management for AWS Database Migration Service](#security-iam)\.
+ AWS DMS uses Secure Sockets Layer \(SSL\) for your endpoint connections with Transport Layer Security \(TLS\)\. For more information about using SSL/TLS with AWS DMS, see [Using SSL with AWS Database Migration Service](#CHAP_Security.SSL)\.
+ AWS DMS uses AWS Key Management Service \(AWS KMS\) encryption keys to encrypt the storage used by your replication instance and its endpoint connection information\. AWS DMS also uses AWS KMS encryption keys to secure your target data at rest for Amazon S3 and Amazon Redshift target endpoints\. For more information, see [Setting an encryption key and specifying AWS KMS permissions](#CHAP_Security.EncryptionKey)\.
+ AWS DMS always creates your replication instance in a virtual private cloud \(VPC\) based on the Amazon VPC service for the greatest possible network access control\. For your DB instances and instance clusters, use the same VPC as your replication instance, or additional VPCs to match this level of access control\. Each Amazon VPC that you use must be associated with a security group that has rules that allow all traffic on all ports to leave \(egress\) the VPC\. This approach allows communication from the replication instance to your source and target database endpoints, as long as correct ingress is enabled on those endpoints\. 

  For more information about available network configurations for AWS DMS, see [Setting up a network for a replication instance](CHAP_ReplicationInstance.VPC.md)\. For more information about creating a DB instance or instance cluster in a VPC, see the security and cluster management documentation for your Amazon databases at [AWS documentation](https://docs.aws.amazon.com/index.html?nc2=h_ql_doc_do_v)\. For more information about network configurations that AWS DMS supports, see [Setting up a network for a replication instance](CHAP_ReplicationInstance.VPC.md)\.
+ To view database migration logs, you need the appropriate Amazon CloudWatch Logs permissions for the IAM role you are using\. For more information about logging for AWS DMS, see [Monitoring replication tasks using Amazon CloudWatch](CHAP_Monitoring.md#CHAP_Monitoring.CloudWatch)\.

**Topics**
+ [Data protection in AWS Database Migration Service](#CHAP_Security.DataProtection)
+ [Identity and access management for AWS Database Migration Service](#security-iam)
+ [Compliance validation for AWS Database Migration Service](#dms-compliance)
+ [Resilience in AWS Database Migration Service](#disaster-recovery-resiliency)
+ [Infrastructure security in AWS Database Migration Service](#infrastructure-security)
+ [IAM permissions needed to use AWS DMS](#CHAP_Security.IAMPermissions)
+ [Creating the IAM roles to use with the AWS CLI and AWS DMS API](#CHAP_Security.APIRole)
+ [Fine\-grained access control using resource names and tags](#CHAP_Security.FineGrainedAccess)
+ [Setting an encryption key and specifying AWS KMS permissions](#CHAP_Security.EncryptionKey)
+ [Network security for AWS Database Migration Service](#CHAP_Security.Network)
+ [Using SSL with AWS Database Migration Service](#CHAP_Security.SSL)
+ [Changing the database password](#CHAP_Security.ChangingDBPassword)

## Data protection in AWS Database Migration Service<a name="CHAP_Security.DataProtection"></a>

### Data encryption<a name="CHAP_Security.DataProtection.DataEncryption"></a>

You can enable encryption for data resources of supported AWS DMS target endpoints\. AWS DMS also encrypts connections to AWS DMS and between AWS DMS and all its source and target endpoints\. In addition, you can manage the keys that AWS DMS and its supported target endpoints use to enable this encryption\.

**Topics**
+ [Encryption at rest](#CHAP_Security.DataProtection.DataEncryption.EncryptionAtRest)
+ [Encryption in transit](#CHAP_Security.DataProtection.DataEncryption.EncryptionInTransit)
+ [Key management](#CHAP_Security.DataProtection.DataEncryption.KeyManagement)

#### Encryption at rest<a name="CHAP_Security.DataProtection.DataEncryption.EncryptionAtRest"></a>

AWS DMS supports encryption at rest by allowing you to specify the server\-side encryption mode that you want used to push your replicated data to Amazon S3 before it is copied to supported AWS DMS target endpoints\. You can specify this encryption mode by setting the `encryptionMode` extra connection attribute for the endpoint\. If this `encryptionMode` setting specifies KMS key encryption mode, you can also create custom AWS KMS keys specifically to encrypt the target data for the following AWS DMS target endpoints:
+ Amazon Redshift – For more information about setting `encryptionMode`, see [Extra connection attributes when using Amazon Redshift as a target for AWS DMS](CHAP_Target.Redshift.md#CHAP_Target.Redshift.ConnectionAttrib)\. For more information about creating a custom AWS KMS encryption key, see [Creating and using AWS KMS keys to encrypt Amazon Redshift target data](CHAP_Target.Redshift.md#CHAP_Target.Redshift.KMSKeys)\.
+ Amazon S3 – For more information about setting `encryptionMode`, see [Extra connection attributes when using Amazon S3 as a target for AWS DMS](CHAP_Target.S3.md#CHAP_Target.S3.Configuring)\. For more information about creating a custom AWS KMS encryption key, see [Creating AWS KMS keys to encrypt Amazon S3 target objects](CHAP_Target.S3.md#CHAP_Target.S3.KMSKeys)\.

#### Encryption in transit<a name="CHAP_Security.DataProtection.DataEncryption.EncryptionInTransit"></a>

AWS DMS supports encryption in transit by ensuring that the data it replicates moves securely from the source endpoint to the target endpoint\. This includes encrypting an S3 bucket on the replication instance that your replication task uses for intermediate storage as the data moves through the replication pipeline\. To encrypt task connections to source and target endpoints AWS DMS uses Secure Socket Layer \(SSL\) with Transport Layer Security \(TLS\)\. By encrypting connections to both endpoints, AWS DMS ensures that your data is secure as it moves both from the source endpoint to your replication task and from your task to the target endpoint\. For more information about using SSL/TLS with AWS DMS, see [Using SSL with AWS Database Migration Service](#CHAP_Security.SSL)

AWS DMS supports both default and custom keys to encrypt both intermediate replication storage and connection information\. You manage these keys by using AWS KMS\. For more information, see [Setting an encryption key and specifying AWS KMS permissions](#CHAP_Security.EncryptionKey)\.

#### Key management<a name="CHAP_Security.DataProtection.DataEncryption.KeyManagement"></a>

AWS DMS supports default or custom keys to encrypt replication storage, connection information, and the target data storage for certain target endpoints\. You manage these keys by using AWS KMS\. For more information, see [Setting an encryption key and specifying AWS KMS permissions](#CHAP_Security.EncryptionKey)\.

### Internetwork traffic privacy<a name="CHAP_Security.DataProtection.InternetworkTraffic"></a>

Connections are provided with protection between AWS DMS and source and target endpoints in the same AWS Region, whether running on premises or as part of an AWS service in the cloud\. \(At least one endpoint, source or target, must run as part of an AWS service in the cloud\.\) This protection applies whether these components share the same virtual private cloud \(VPC\) or exist in separate VPCs, if the VPCs are all in the same AWS Region\. For more information about the supported network configurations for AWS DMS, see [Setting up a network for a replication instance](CHAP_ReplicationInstance.VPC.md)\. For more information about the security considerations when using these network configurations, see [Network security for AWS Database Migration Service](#CHAP_Security.Network)\.

## Identity and access management for AWS Database Migration Service<a name="security-iam"></a>

AWS Identity and Access Management \(IAM\) is an AWS service that helps an administrator securely control access to AWS resources\. IAM administrators control who can be *authenticated* \(signed in\) and *authorized* \(have permissions\) to use AWS DMS resources\. IAM is an AWS service that you can use with no additional charge\.

**Topics**
+ [Audience](#security_iam_audience)
+ [Authenticating with identities](#security_iam_authentication)
+ [Managing access using policies](#security_iam_access-manage)
+ [How AWS Database Migration Service works with IAM](#security_iam_service-with-iam)
+ [AWS Database Migration Service identity\-based policy examples](#security_iam_id-based-policy-examples)
+ [Resource\-based policy examples for AWS KMS](#security_iam_resource-based-policy-examples)
+ [Using secrets to access AWS Database Migration Service endpoints](#security_iam_secretsmanager)
+ [Troubleshooting AWS Database Migration Service identity and access](#security_iam_troubleshoot)

### Audience<a name="security_iam_audience"></a>

How you use AWS Identity and Access Management \(IAM\) differs, depending on the work that you do in AWS DMS\.

**Service user** – If you use the AWS DMS service to do your job, then your administrator provides you with the credentials and permissions that you need\. As you use more AWS DMS features to do your work, you might need additional permissions\. Understanding how access is managed can help you request the right permissions from your administrator\. If you cannot access a feature in AWS DMS, see [Troubleshooting AWS Database Migration Service identity and access](#security_iam_troubleshoot)\.

**Service administrator** – If you're in charge of AWS DMS resources at your company, you probably have full access to AWS DMS\. It's your job to determine which AWS DMS features and resources your employees should access\. You must then submit requests to your IAM administrator to change the permissions of your service users\. Review the information on this page to understand the basic concepts of IAM\. To learn more about how your company can use IAM with AWS DMS, see [How AWS Database Migration Service works with IAM](#security_iam_service-with-iam)\.

**IAM administrator** – If you're an IAM administrator, you might want to learn details about how you can write policies to manage access to AWS DMS\. To view example AWS DMS identity\-based policies that you can use in IAM, see [AWS Database Migration Service identity\-based policy examples](#security_iam_id-based-policy-examples)\.

### Authenticating with identities<a name="security_iam_authentication"></a>

Authentication is how you sign in to AWS using your identity credentials\. For more information about signing in using the AWS Management Console, see [Signing in to the AWS Management Console as an IAM user or root user](https://docs.aws.amazon.com/IAM/latest/UserGuide/console.html) in the *IAM User Guide*\.

You must be *authenticated* \(signed in to AWS\) as the AWS account root user, an IAM user, or by assuming an IAM role\. You can also use your company's single sign\-on authentication or even sign in using Google or Facebook\. In these cases, your administrator previously set up identity federation using IAM roles\. When you access AWS using credentials from another company, you are assuming a role indirectly\. 

To sign in directly to the [AWS Management Console](https://console.aws.amazon.com/), use your password with your root user email address or your IAM user name\. You can access AWS programmatically using your root user or IAM users access keys\. AWS provides SDK and command line tools to cryptographically sign your request using your credentials\. If you don't use AWS tools, you must sign the request yourself\. Do this using *Signature Version 4*, a protocol for authenticating inbound API requests\. For more information about authenticating requests, see [Signature Version 4 signing process](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html) in the *AWS General Reference*\.

Regardless of the authentication method that you use, you might also be required to provide additional security information\. For example, AWS recommends that you use multi\-factor authentication \(MFA\) to increase the security of your account\. To learn more, see [Using multi\-factor authentication \(MFA\) in AWS](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_mfa.html) in the *IAM User Guide*\.

#### AWS account root user<a name="security_iam_authentication-rootuser"></a>

  When you first create an AWS account, you begin with a single sign\-in identity that has complete access to all AWS services and resources in the account\. This identity is called the AWS account *root user* and is accessed by signing in with the email address and password that you used to create the account\. We strongly recommend that you do not use the root user for your everyday tasks, even the administrative ones\. Instead, adhere to the [best practice of using the root user only to create your first IAM user](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html#create-iam-users)\. Then securely lock away the root user credentials and use them to perform only a few account and service management tasks\. 

#### IAM users and groups<a name="security_iam_authentication-iamuser"></a>

An *[IAM user](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users.html)* is an identity within your AWS account that has specific permissions for a single person or application\. An IAM user can have long\-term credentials such as a user name and password or a set of access keys\. To learn how to generate access keys, see [Managing access keys for IAM users](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html) in the *IAM User Guide*\. When you generate access keys for an IAM user, make sure you view and securely save the key pair\. You cannot recover the secret access key in the future\. Instead, you must generate a new access key pair\.

An [https://docs.aws.amazon.com/IAM/latest/UserGuide/id_groups.html](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_groups.html) is an identity that specifies a collection of IAM users\. You can't sign in as a group\. You can use groups to specify permissions for multiple users at a time\. Groups make permissions easier to manage for large sets of users\. For example, you could have a group named *IAMAdmins* and give that group permissions to administer IAM resources\.

Users are different from roles\. A user is uniquely associated with one person or application, but a role is intended to be assumable by anyone who needs it\. Users have permanent long\-term credentials, but roles provide temporary credentials\. To learn more, see [When to create an IAM user \(instead of a role\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html#id_which-to-choose) in the *IAM User Guide*\.

#### IAM roles<a name="security_iam_authentication-iamrole"></a>

An *[IAM role](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html)* is an identity within your AWS account that has specific permissions\. It is similar to an IAM user, but is not associated with a specific person\. You can temporarily assume an IAM role in the AWS Management Console by [switching roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_switch-role-console.html)\. You can assume a role by calling an AWS CLI or AWS API operation or by using a custom URL\. For more information about methods for using roles, see [Using IAM roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use.html) in the *IAM User Guide*\.

IAM roles with temporary credentials are useful in the following situations:
+ **Temporary IAM user permissions** – An IAM user can assume an IAM role to temporarily take on different permissions for a specific task\. 
+ **Federated user access** –  Instead of creating an IAM user, you can use existing identities from AWS Directory Service, your enterprise user directory, or a web identity provider\. These are known as *federated users*\. AWS assigns a role to a federated user when access is requested through an [identity provider](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers.html)\. For more information about federated users, see [Federated users and roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction_access-management.html#intro-access-roles) in the *IAM User Guide*\. 
+ **Cross\-account access** – You can use an IAM role to allow someone \(a trusted principal\) in a different account to access resources in your account\. Roles are the primary way to grant cross\-account access\. However, with some AWS services, you can attach a policy directly to a resource \(instead of using a role as a proxy\)\. To learn the difference between roles and resource\-based policies for cross\-account access, see [How IAM roles differ from resource\-based policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_compare-resource-policies.html) in the *IAM User Guide*\.
+ **Cross\-service access** –  Some AWS services use features in other AWS services\. For example, when you make a call in a service, it's common for that service to run applications in Amazon EC2 or store objects in Amazon S3\. A service might do this using the calling principal's permissions, using a service role, or using a service\-linked role\. 
  + **Principal permissions** –  When you use an IAM user or role to perform actions in AWS, you are considered a principal\. Policies grant permissions to a principal\. When you use some services, you might perform an action that then triggers another action in a different service\. In this case, you must have permissions to perform both actions\. To see whether an action requires additional dependent actions in a policy, see [Actions, Resources, and Condition Keys for AWS Database Migration Service](https://docs.aws.amazon.com/IAM/latest/UserGuide/list_awsdatabasemigrationservice.html) in the *Service Authorization Reference*\. 
  + **Service role** –  A service role is an [IAM role](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html) that a service assumes to perform actions on your behalf\. Service roles provide access only within your account and cannot be used to grant access to services in other accounts\. An IAM administrator can create, modify, and delete a service role from within IAM\. For more information, see [Creating a role to delegate permissions to an AWS service](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-service.html) in the *IAM User Guide*\. 
  + **Service\-linked role** –  A service\-linked role is a type of service role that is linked to an AWS service\. The service can assume the role to perform an action on your behalf\. Service\-linked roles appear in your IAM account and are owned by the service\. An IAM administrator can view, but not edit the permissions for service\-linked roles\. 
+ **Applications running on Amazon EC2** –  You can use an IAM role to manage temporary credentials for applications that are running on an EC2 instance and making AWS CLI or AWS API requests\. This is preferable to storing access keys within the EC2 instance\. To assign an AWS role to an EC2 instance and make it available to all of its applications, you create an instance profile that is attached to the instance\. An instance profile contains the role and enables programs that are running on the EC2 instance to get temporary credentials\. For more information, see [Using an IAM role to grant permissions to applications running on Amazon EC2 instances](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_switch-role-ec2.html) in the *IAM User Guide*\. 

To learn whether to use IAM roles or IAM users, see [When to create an IAM role \(instead of a user\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html#id_which-to-choose_role) in the *IAM User Guide*\.

### Managing access using policies<a name="security_iam_access-manage"></a>

You control access in AWS by creating policies and attaching them to IAM identities or AWS resources\. A policy is an object in AWS that, when associated with an identity or resource, defines their permissions\. You can sign in as the root user or an IAM user, or you can assume an IAM role\. When you then make a request, AWS evaluates the related identity\-based or resource\-based policies\. Permissions in the policies determine whether the request is allowed or denied\. Most policies are stored in AWS as JSON documents\. For more information about the structure and contents of JSON policy documents, see [Overview of JSON policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html#access_policies-json) in the *IAM User Guide*\.

Administrators can use AWS JSON policies to specify who has access to what\. That is, which **principal** can perform **actions** on what **resources**, and under what **conditions**\.

Every IAM entity \(user or role\) starts with no permissions\. In other words, by default, users can do nothing, not even change their own password\. To give a user permission to do something, an administrator must attach a permissions policy to a user\. Or the administrator can add the user to a group that has the intended permissions\. When an administrator gives permissions to a group, all users in that group are granted those permissions\.

IAM policies define permissions for an action regardless of the method that you use to perform the operation\. For example, suppose that you have a policy that allows the `iam:GetRole` action\. A user with that policy can get role information from the AWS Management Console, the AWS CLI, or the AWS API\.

#### Identity\-based policies<a name="security_iam_access-manage-id-based-policies"></a>

Identity\-based policies are JSON permissions policy documents that you can attach to an identity, such as an IAM user, group of users, or role\. These policies control what actions users and roles can perform, on which resources, and under what conditions\. To learn how to create an identity\-based policy, see [Creating IAM policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create.html) in the *IAM User Guide*\.

Identity\-based policies can be further categorized as *inline policies* or *managed policies*\. Inline policies are embedded directly into a single user, group, or role\. Managed policies are standalone policies that you can attach to multiple users, groups, and roles in your AWS account\. Managed policies include AWS managed policies and customer managed policies\. To learn how to choose between a managed policy or an inline policy, see [Choosing between managed policies and inline policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html#choosing-managed-or-inline) in the *IAM User Guide*\.

#### Resource\-based policies<a name="security_iam_access-manage-resource-based-policies"></a>

Resource\-based policies are JSON policy documents that you attach to a resource\. Examples of resource\-based policies are IAM *role trust policies* and Amazon S3 *bucket policies*\. In services that support resource\-based policies, service administrators can use them to control access to a specific resource\. For the resource where the policy is attached, the policy defines what actions a specified principal can perform on that resource and under what conditions\. You must [specify a principal](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_principal.html) in a resource\-based policy\. Principals can include accounts, users, roles, federated users, or AWS services\.

Resource\-based policies are inline policies that are located in that service\. You can't use AWS managed policies from IAM in a resource\-based policy\.

#### Access control lists \(ACLs\)<a name="security_iam_access-manage-acl"></a>

Access control lists \(ACLs\) control which principals \(account members, users, or roles\) have permissions to access a resource\. ACLs are similar to resource\-based policies, although they do not use the JSON policy document format\.

Amazon S3, AWS WAF, and Amazon VPC are examples of services that support ACLs\. To learn more about ACLs, see [Access control list \(ACL\) overview](https://docs.aws.amazon.com/AmazonS3/latest/dev/acl-overview.html) in the *Amazon Simple Storage Service Developer Guide*\.

#### Other policy types<a name="security_iam_access-manage-other-policies"></a>

AWS supports additional, less\-common policy types\. These policy types can set the maximum permissions granted to you by the more common policy types\. 
+ **Permissions boundaries** – A permissions boundary is an advanced feature in which you set the maximum permissions that an identity\-based policy can grant to an IAM entity \(IAM user or role\)\. You can set a permissions boundary for an entity\. The resulting permissions are the intersection of entity's identity\-based policies and its permissions boundaries\. Resource\-based policies that specify the user or role in the `Principal` field are not limited by the permissions boundary\. An explicit deny in any of these policies overrides the allow\. For more information about permissions boundaries, see [Permissions boundaries for IAM entities](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_boundaries.html) in the *IAM User Guide*\.
+ **Service control policies \(SCPs\)** – SCPs are JSON policies that specify the maximum permissions for an organization or organizational unit \(OU\) in AWS Organizations\. AWS Organizations is a service for grouping and centrally managing multiple AWS accounts that your business owns\. If you enable all features in an organization, then you can apply service control policies \(SCPs\) to any or all of your accounts\. The SCP limits permissions for entities in member accounts, including each AWS account root user\. For more information about Organizations and SCPs, see [How SCPs work](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_about-scps.html) in the *AWS Organizations User Guide*\.
+ **Session policies** – Session policies are advanced policies that you pass as a parameter when you programmatically create a temporary session for a role or federated user\. The resulting session's permissions are the intersection of the user or role's identity\-based policies and the session policies\. Permissions can also come from a resource\-based policy\. An explicit deny in any of these policies overrides the allow\. For more information, see [Session policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html#policies_session) in the *IAM User Guide*\. 

#### Multiple policy types<a name="security_iam_access-manage-multiple-policies"></a>

When multiple types of policies apply to a request, the resulting permissions are more complicated to understand\. To learn how AWS determines whether to allow a request when multiple policy types are involved, see [Policy evaluation logic](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_evaluation-logic.html) in the *IAM User Guide*\.

### How AWS Database Migration Service works with IAM<a name="security_iam_service-with-iam"></a>

Before you use IAM to manage access to AWS DMS, you should understand what IAM features are available to use with AWS DMS\. To get a high\-level view of how AWS DMS and other AWS services work with IAM, see [AWS services that work with IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_aws-services-that-work-with-iam.html) in the *IAM User Guide*\.

**Topics**
+ [AWS DMS identity\-based policies](#security_iam_service-with-iam-id-based-policies)
+ [AWS DMS resource\-based policies](#security_iam_service-with-iam-resource-based-policies)
+ [Authorization based on AWS DMS tags](#security_iam_service-with-iam-tags)
+ [IAM roles for AWS DMS](#security_iam_service-with-iam-roles)

#### AWS DMS identity\-based policies<a name="security_iam_service-with-iam-id-based-policies"></a>

With IAM identity\-based policies, you can specify allowed or denied actions and resources, and also the conditions under which actions are allowed or denied\. AWS DMS supports specific actions, resources, and condition keys\. To learn about all of the elements that you use in a JSON policy, see [IAM JSON policy elements reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html) in the *IAM User Guide*\.

##### Actions<a name="security_iam_service-with-iam-id-based-policies-actions"></a>

Administrators can use AWS JSON policies to specify who has access to what\. That is, which **principal** can perform **actions** on what **resources**, and under what **conditions**\.

The `Action` element of a JSON policy describes the actions that you can use to allow or deny access in a policy\. Policy actions usually have the same name as the associated AWS API operation\. There are some exceptions, such as *permission\-only actions* that don't have a matching API operation\. There are also some operations that require multiple actions in a policy\. These additional actions are called *dependent actions*\.

Include actions in a policy to grant permissions to perform the associated operation\.

Policy actions in AWS DMS use the following prefix before the action: `dms:`\. For example, to grant someone permission to create a replication task with the AWS DMS `CreateReplicationTask` API operation, you include the `dms:CreateReplicationTask` action in their policy\. Policy statements must include either an `Action` or `NotAction` element\. AWS DMS defines its own set of actions that describe tasks that you can perform with this service\.

To specify multiple actions in a single statement, separate them with commas as follows\.

```
"Action": [
      "dms:action1",
      "dms:action2"
```

You can specify multiple actions using wildcards \(\*\)\. For example, to specify all actions that begin with the word `Describe`, include the following action\.

```
"Action": "dms:Describe*"
```



To see a list of AWS DMS actions, see [Actions Defined by AWS Database Migration Service](https://docs.aws.amazon.com/IAM/latest/UserGuide/list_awsdatabasemigrationservice.html#awsdatabasemigrationservice-actions-as-permissions) in the *IAM User Guide*\.

##### Resources<a name="security_iam_service-with-iam-id-based-policies-resources"></a>

Administrators can use AWS JSON policies to specify who has access to what\. That is, which **principal** can perform **actions** on what **resources**, and under what **conditions**\.

The `Resource` JSON policy element specifies the object or objects to which the action applies\. Statements must include either a `Resource` or a `NotResource` element\. As a best practice, specify a resource using its [Amazon Resource Name \(ARN\)](https://docs.aws.amazon.com/general/latest/gr/aws-arns-and-namespaces.html)\. You can do this for actions that support a specific resource type, known as *resource\-level permissions*\.

For actions that don't support resource\-level permissions, such as listing operations, use a wildcard \(\*\) to indicate that the statement applies to all resources\.

```
"Resource": "*"
```



AWS DMS works with the following resources:
+ Certificates
+ Endpoints
+ Event subscriptions
+ Replication instances
+ Replication subnet \(security\) groups
+ Replication tasks

The resource or resources that AWS DMS requires depends on the action or actions that you invoke\. You need a policy that permits these actions on the associated resource or resources specified by the resource ARNs\.

For example, an AWS DMS endpoint resource has the following ARN:

```
arn:${Partition}:dms:${Region}:${Account}:endpoint/${InstanceId}
```

For more information about the format of ARNs, see [Amazon Resource Names \(ARNs\) and AWS service namespaces](https://docs.aws.amazon.com/general/latest/gr/aws-arns-and-namespaces.html)\.

For example, to specify the `1A2B3C4D5E6F7G8H9I0J1K2L3M` endpoint instance for the `use-east-2` region in your statement, use the following ARN\.

```
"Resource": "arn:aws:dms:us-east-2:987654321098:endpoint/1A2B3C4D5E6F7G8H9I0J1K2L3M"
```

To specify all endpoints that belong to a specific account, use the wildcard \(\*\)\.

```
"Resource": "arn:aws:dms:us-east-2:987654321098:endpoint/*"
```

Some AWS DMS actions, such as those for creating resources, cannot be performed on a specific resource\. In those cases, you must use the wildcard \(\*\)\.

```
"Resource": "*"
```

Some AWS DMS API actions involve multiple resources\. For example, `StartReplicationTask` starts and connects a replication task to two database endpoint resources, a source and a target, so an IAM user must have permissions to read the source endpoint and to write to the target endpoint\. To specify multiple resources in a single statement, separate the ARNs with commas\. 

```
"Resource": [
      "resource1",
      "resource2" ]
```

For more information on controlling access to AWS DMS resources using policies, see [](#CHAP_Security.FineGrainedAccess.ResourceName)\. To see a list of AWS DMS resource types and their ARNs, see [Resources Defined by AWS Database Migration Service](https://docs.aws.amazon.com/IAM/latest/UserGuide/list_awsdatabasemigrationservice.html#awsdatabasemigrationservice-resources-for-iam-policies) in the *IAM User Guide*\. To learn with which actions you can specify the ARN of each resource, see [Actions Defined by AWS Database Migration Service](https://docs.aws.amazon.com/IAM/latest/UserGuide/list_awsdatabasemigrationservice.html#awsdatabasemigrationservice-actions-as-permissions)\.

##### Condition keys<a name="security_iam_service-with-iam-id-based-policies-conditionkeys"></a>

Administrators can use AWS JSON policies to specify who has access to what\. That is, which **principal** can perform **actions** on what **resources**, and under what **conditions**\.

The `Condition` element \(or `Condition` *block*\) lets you specify conditions in which a statement is in effect\. The `Condition` element is optional\. You can create conditional expressions that use [condition operators](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_condition_operators.html), such as equals or less than, to match the condition in the policy with values in the request\. 

If you specify multiple `Condition` elements in a statement, or multiple keys in a single `Condition` element, AWS evaluates them using a logical `AND` operation\. If you specify multiple values for a single condition key, AWS evaluates the condition using a logical `OR` operation\. All of the conditions must be met before the statement's permissions are granted\.

 You can also use placeholder variables when you specify conditions\. For example, you can grant an IAM user permission to access a resource only if it is tagged with their IAM user name\. For more information, see [IAM policy elements: variables and tags](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_variables.html) in the *IAM User Guide*\. 

AWS supports global condition keys and service\-specific condition keys\. To see all AWS global condition keys, see [AWS global condition context keys](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html) in the *IAM User Guide*\.

AWS DMS defines its own set of condition keys and also supports using some global condition keys\. To see all AWS global condition keys, see [AWS global condition context keys](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html) in the *IAM User Guide*\.



AWS DMS defines a set of standard tags that you can use in its condition keys and also allows you defined your own custom tags\. For more information, see [Using tags to control access](#CHAP_Security.FineGrainedAccess.Tags)\.

To see a list of AWS DMS condition keys, see [Condition Keys for AWS Database Migration Service](https://docs.aws.amazon.com/IAM/latest/UserGuide/list_awsdatabasemigrationservice.html#awsdatabasemigrationservice-policy-keys) in the *IAM User Guide*\. To learn with which actions and resources you can use a condition key, see [Actions Defined by AWS Database Migration Service](https://docs.aws.amazon.com/IAM/latest/UserGuide/list_awsdatabasemigrationservice.html#awsdatabasemigrationservice-actions-as-permissions) and [Resources Defined by AWS Database Migration Service](https://docs.aws.amazon.com/IAM/latest/UserGuide/list_awsdatabasemigrationservice.html#awsdatabasemigrationservice-resources-for-iam-policies)\.

##### Examples<a name="security_iam_service-with-iam-id-based-policies-examples"></a>



To view examples of AWS DMS identity\-based policies, see [AWS Database Migration Service identity\-based policy examples](#security_iam_id-based-policy-examples)\.

#### AWS DMS resource\-based policies<a name="security_iam_service-with-iam-resource-based-policies"></a>

Resource\-based policies are JSON policy documents that specify what actions a specified principal can perform on a given AWS DMS resource and under what conditions\. AWS DMS supports resource\-based permissions policies for AWS KMS encryption keys that you create to encrypt data migrated to supported target endpoints\. The supported target endpoints include Amazon Redshift and Amazon S3\. By using resource\-based policies, you can grant the permission for using these encryption keys to other accounts for each target endpoint\.

To enable cross\-account access, you can specify an entire account or IAM entities in another account as the [principal in a resource\-based policy](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_principal.html)\. Adding a cross\-account principal to a resource\-based policy is only half of establishing the trust relationship\. When the principal and the resource are in different AWS accounts, you must also grant the principal entity permission to access the resource\. Grant permission by attaching an identity\-based policy to the entity\. However, if a resource\-based policy grants access to a principal in the same account, no additional identity\-based policy is required\. For more information, see [How IAM roles differ from resource\-based policies ](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_compare-resource-policies.html)in the *IAM User Guide*\.

The AWS DMS service supports only one type of resource\-based policy called a *key policy*, which is attached to an AWS KMS encryption key\. This policy defines which principal entities \(accounts, users, roles, and federated users\) can encrypt migrated data on the supported target endpoint\.

To learn how to attach a resource\-based policy to an encryption key that you create for the supported target endpoints, see [Creating and using AWS KMS keys to encrypt Amazon Redshift target data](CHAP_Target.Redshift.md#CHAP_Target.Redshift.KMSKeys) and [Creating AWS KMS keys to encrypt Amazon S3 target objects](CHAP_Target.S3.md#CHAP_Target.S3.KMSKeys)\.

##### Examples<a name="security_iam_service-with-iam-resource-based-policies-examples"></a>



For examples of AWS DMS resource\-based policies, see [Resource\-based policy examples for AWS KMS](#security_iam_resource-based-policy-examples)\.

#### Authorization based on AWS DMS tags<a name="security_iam_service-with-iam-tags"></a>

You can attach tags to AWS DMS resources or pass tags in a request to AWS DMS\. To control access based on tags, you provide tag information in the [condition element](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_condition.html) of a policy using the `dms:ResourceTag/key-name`, `aws:RequestTag/key-name`, or `aws:TagKeys` condition key\. AWS DMS defines a set of standard tags that you can use in its condition keys and also enables you to define your own custom tags\. For more information, see [Using tags to control access](#CHAP_Security.FineGrainedAccess.Tags)\.

For an example identity\-based policy that limits access to a resource based on tags, see [Accessing AWS DMS resources based on tags](#security_iam_id-based-policy-examples-access-resources-tags)\.

#### IAM roles for AWS DMS<a name="security_iam_service-with-iam-roles"></a>

An [IAM role](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html) is an entity within your AWS account that has specific permissions\.

##### Using temporary credentials with AWS DMS<a name="security_iam_service-with-iam-roles-tempcreds"></a>

You can use temporary credentials to sign in with federation, assume an IAM role, or assume a cross\-account role\. You get temporary security credentials by calling AWS STS API operations such as [AssumeRole](https://docs.aws.amazon.com/STS/latest/APIReference/API_AssumeRole.html) or [GetFederationToken](https://docs.aws.amazon.com/STS/latest/APIReference/API_GetFederationToken.html)\. 

AWS DMS supports using temporary credentials\. 

##### Service\-linked roles<a name="security_iam_service-with-iam-roles-service-linked"></a>

[Service\-linked roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_terms-and-concepts.html#iam-term-service-linked-role) allow AWS services to access resources in other services to complete an action on your behalf\. Service\-linked roles appear in your IAM account and are owned by the service\. An IAM administrator can view but not edit the permissions for service\-linked roles\.

AWS DMS does not support service\-linked roles\. 

##### Service roles<a name="security_iam_service-with-iam-roles-service"></a>

This feature allows a service to assume a [service role](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_terms-and-concepts.html#iam-term-service-role) on your behalf\. This role allows the service to access resources in other services to complete an action on your behalf\. Service roles appear in your IAM account and are owned by the account\. This means that an IAM administrator can change the permissions for this role\. However, doing so might break the functionality of the service\.

AWS DMS supports two types of service roles that you must create to use certain source or target endpoints:
+ Roles with permissions to allow AWS DMS access to the following source and target endpoints \(or their resources\):
  + Amazon DynamoDB as a target – For more information see [Prerequisites for using DynamoDB as a target for AWS Database Migration Service](CHAP_Target.DynamoDB.md#CHAP_Target.DynamoDB.Prerequisites)\.
  + Elasticsearch as a target – For more information see [Prerequisites for using Amazon Elasticsearch Service as a target for AWS Database Migration Service](CHAP_Target.Elasticsearch.md#CHAP_Target.Elasticsearch.Prerequisites)\.
  + Amazon Kinesis as a target – For more information see [Prerequisites for using a Kinesis data stream as a target for AWS Database Migration Service](CHAP_Target.Kinesis.md#CHAP_Target.Kinesis.Prerequisites)\.
  + Amazon Redshift as a target – You need to create the specified role only for creating a custom KMS encryption key to encrypt the target data or for specifying a custom S3 bucket to hold intermediate task storage\. For more information, see [Creating and using AWS KMS keys to encrypt Amazon Redshift target data](CHAP_Target.Redshift.md#CHAP_Target.Redshift.KMSKeys) or [Amazon S3 bucket settings](CHAP_Target.Redshift.md#CHAP_Target.Redshift.EndpointSettings.S3Buckets)\.
  + Amazon S3 as a source or as a target – For more information, see [Prerequisites when using Amazon S3 as a source for AWS DMS](CHAP_Source.S3.md#CHAP_Source.S3.Prerequisites) or [Prerequisites for using Amazon S3 as a target](CHAP_Target.S3.md#CHAP_Target.S3.Prerequisites)\.

  For example, to read data from an S3 source endpoint or to push data to an S3 target endpoint, you must create a service role as a prerequisite to accessing S3 for each of these endpoint operations\. 
+ Roles with permissions required to use the AWS CLI and AWS DMS API – Two IAM roles that you need to create are `dms-vpc-role` and `dms-cloudwatch-logs-role`\. If you use Amazon Redshift as a target database, you must also create and add the IAM role `dms-access-for-endpoint` to your AWS account\. For more information, see [Creating the IAM roles to use with the AWS CLI and AWS DMS API](#CHAP_Security.APIRole)\.

##### Choosing an IAM role in AWS DMS<a name="security_iam_service-with-iam-roles-choose"></a>

If you use the AWS CLI or the AWS DMS API for your database migration, you must add certain IAM roles to your AWS account before you can use the features of AWS DMS\. Two of these are `dms-vpc-role` and `dms-cloudwatch-logs-role`\. If you use Amazon Redshift as a target database, you must also add the IAM role `dms-access-for-endpoint` to your AWS account\. For more information, see [Creating the IAM roles to use with the AWS CLI and AWS DMS API](#CHAP_Security.APIRole)\.

### AWS Database Migration Service identity\-based policy examples<a name="security_iam_id-based-policy-examples"></a>

By default, IAM users and roles don't have permission to create or modify AWS DMS resources\. They also can't perform tasks using the AWS Management Console, AWS CLI, or AWS API\. An IAM administrator must create IAM policies that grant users and roles permission to perform specific API operations on the specified resources they need\. The administrator must then attach those policies to the IAM users or groups that require those permissions\.

To learn how to create an IAM identity\-based policy using these example JSON policy documents, see [Creating policies on the JSON tab](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create.html#access_policies_create-json-editor) in the *IAM User Guide*\.

**Topics**
+ [Policy best practices](#security_iam_service-with-iam-policy-best-practices)
+ [Using the AWS DMS console](#security_iam_id-based-policy-examples-console)
+ [Allow users to view their own permissions](#security_iam_id-based-policy-examples-view-own-permissions)
+ [Accessing one Amazon S3 bucket](#security_iam_id-based-policy-examples-access-one-bucket)
+ [Accessing AWS DMS resources based on tags](#security_iam_id-based-policy-examples-access-resources-tags)

#### Policy best practices<a name="security_iam_service-with-iam-policy-best-practices"></a>

Identity\-based policies are very powerful\. They determine whether someone can create, access, or delete AWS DMS resources in your account\. These actions can incur costs for your AWS account\. When you create or edit identity\-based policies, follow these guidelines and recommendations:
+ **Get started using AWS managed policies** – To start using AWS DMS quickly, use AWS managed policies to give your employees the permissions they need\. These policies are already available in your account and are maintained and updated by AWS\. For more information, see [Get started using permissions with AWS managed policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html#bp-use-aws-defined-policies) in the *IAM User Guide*\.
+ **Grant least privilege** – When you create custom policies, grant only the permissions required to perform a task\. Start with a minimum set of permissions and grant additional permissions as necessary\. Doing so is more secure than starting with permissions that are too lenient and then trying to tighten them later\. For more information, see [Grant least privilege](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html#grant-least-privilege) in the *IAM User Guide*\.
+ **Enable MFA for sensitive operations** – For extra security, require IAM users to use multi\-factor authentication \(MFA\) to access sensitive resources or API operations\. For more information, see [Using multi\-factor authentication \(MFA\) in AWS](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_mfa.html) in the *IAM User Guide*\.
+ **Use policy conditions for extra security** – To the extent that it's practical, define the conditions under which your identity\-based policies allow access to a resource\. For example, you can write conditions to specify a range of allowable IP addresses that a request must come from\. You can also write conditions to allow requests only within a specified date or time range, or to require the use of SSL or MFA\. For more information, see [IAM JSON policy elements: Condition](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_condition.html) in the *IAM User Guide*\.

#### Using the AWS DMS console<a name="security_iam_id-based-policy-examples-console"></a>

The following policy gives you access to AWS DMS, including the AWS DMS console, and also specifies permissions for certain actions needed from other Amazon services such as Amazon EC2\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "dms:*",
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "kms:ListAliases", 
                "kms:DescribeKey"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "iam:GetRole",
                "iam:PassRole",
                "iam:CreateRole",
                "iam:AttachRolePolicy"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "ec2:DescribeVpcs",
                "ec2:DescribeInternetGateways",
                "ec2:DescribeAvailabilityZones",
                "ec2:DescribeSubnets",
                "ec2:DescribeSecurityGroups",
                "ec2:ModifyNetworkInterfaceAttribute",
                "ec2:CreateNetworkInterface",
                "ec2:DeleteNetworkInterface"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "cloudwatch:Get*",
                "cloudwatch:List*"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "logs:DescribeLogGroups",
                "logs:DescribeLogStreams",
                "logs:FilterLogEvents",
                "logs:GetLogEvents"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "redshift:Describe*",
                "redshift:ModifyClusterIamRoles"
            ],
            "Resource": "*"
        }
    ]
}
```

A breakdown of these permissions might help you better understand why each one required for using the console is necessary\.

The following section is required to allow the user to list their available AWS KMS keys and alias for display in the console\. This entry is not required if you know the Amazon Resource Name \(ARN\) for the KMS key and you are using only the AWS Command Line Interface \(AWS CLI\)\.

```
{
            "Effect": "Allow",
            "Action": [
                "kms:ListAliases", 
                "kms:DescribeKey"
            ],
            "Resource": "*"
        }
```

The following section is required for certain endpoint types that require a role ARN to be passed in with the endpoint\. In addition, if the required AWS DMS roles aren't created ahead of time, the AWS DMS console has the ability to create the role\. If all roles are configured ahead of time, all that is required in `iam:GetRole` and `iam:PassRole`\. For more information about roles, see [Creating the IAM roles to use with the AWS CLI and AWS DMS API](#CHAP_Security.APIRole)\.

```
{
            "Effect": "Allow",
            "Action": [
                "iam:GetRole",
                "iam:PassRole",
                "iam:CreateRole",
                "iam:AttachRolePolicy"
            ],
            "Resource": "*"
        }
```

The following section is required because AWS DMS needs to create the Amazon EC2 instance and configure the network for the replication instance that is created\. These resources exist in the customer's account, so the ability to perform these actions on behalf of the customer is required\.

```
{
            "Effect": "Allow",
            "Action": [
                "ec2:DescribeVpcs",
                "ec2:DescribeInternetGateways",
                "ec2:DescribeAvailabilityZones",
                "ec2:DescribeSubnets",
                "ec2:DescribeSecurityGroups",
                "ec2:ModifyNetworkInterfaceAttribute",
                "ec2:CreateNetworkInterface",
                "ec2:DeleteNetworkInterface"
            ],
            "Resource": "*"
        }
```

The following section is required to allow the user to be able to view replication instance metrics\.

```
{
            "Effect": "Allow",
            "Action": [
                "cloudwatch:Get*",
                "cloudwatch:List*"
            ],
            "Resource": "*"
        }
```

This section is required to allow the user to view replication logs\.

```
{
            "Effect": "Allow",
            "Action": [
                "logs:DescribeLogGroups",
                "logs:DescribeLogStreams",
                "logs:FilterLogEvents",
                "logs:GetLogEvents"
            ],
            "Resource": "*"
        }
```

The following section is required when using Amazon Redshift as a target\. It allows AWS DMS to validate that the Amazon Redshift cluster is set up properly for AWS DMS\.

```
{
            "Effect": "Allow",
            "Action": [
                "redshift:Describe*",
                "redshift:ModifyClusterIamRoles"
            ],
            "Resource": "*"
        }
```

The AWS DMS console creates several roles that are automatically attached to your AWS account when you use the AWS DMS console\. If you use the AWS Command Line Interface \(AWS CLI\) or the AWS DMS API for your migration, you need to add these roles to your account\. For more information about adding these roles, see [Creating the IAM roles to use with the AWS CLI and AWS DMS API](#CHAP_Security.APIRole)\.

For more information on the requirements for using this policy to access AWS DMS, see [IAM permissions needed to use AWS DMS](#CHAP_Security.IAMPermissions)\.

#### Allow users to view their own permissions<a name="security_iam_id-based-policy-examples-view-own-permissions"></a>

This example shows how you might create a policy that allows IAM users to view the inline and managed policies that are attached to their user identity\. This policy includes permissions to complete this action on the console or programmatically using the AWS CLI or AWS API\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "ViewOwnUserInfo",
            "Effect": "Allow",
            "Action": [
                "iam:GetUserPolicy",
                "iam:ListGroupsForUser",
                "iam:ListAttachedUserPolicies",
                "iam:ListUserPolicies",
                "iam:GetUser"
            ],
            "Resource": ["arn:aws:iam::*:user/${aws:username}"]
        },
        {
            "Sid": "NavigateInConsole",
            "Effect": "Allow",
            "Action": [
                "iam:GetGroupPolicy",
                "iam:GetPolicyVersion",
                "iam:GetPolicy",
                "iam:ListAttachedGroupPolicies",
                "iam:ListGroupPolicies",
                "iam:ListPolicyVersions",
                "iam:ListPolicies",
                "iam:ListUsers"
            ],
            "Resource": "*"
        }
    ]
}
```

#### Accessing one Amazon S3 bucket<a name="security_iam_id-based-policy-examples-access-one-bucket"></a>

AWS DMS uses Amazon S3 buckets as intermediate storage for database migration\. Typically, AWS DMS manages default S3 buckets for this purpose\. However, in certain cases, especially when you use the AWS CLI or the AWS DMS API, AWS DMS enables you to specify your own S3 bucket instead\. For example, you can specify your own S3 bucket for migrating data to an Amazon Redshift target endpoint\. In this case, you need to create a role with permissions based on the AWS\-managed `AmazonDMSRedshiftS3Role` policy\.

The following example shows a version of the `AmazonDMSRedshiftS3Role` policy\. It allows AWS DMS to grant an IAM user in your AWS account access to one of your Amazon S3 buckets\. It also allows the user to add, update, and delete objects\.

In addition to granting the `s3:PutObject`, `s3:GetObject`, and `s3:DeleteObject` permissions to the user, the policy also grants the `s3:ListAllMyBuckets`, `s3:GetBucketLocation`, and `s3:ListBucket` permissions\. These are the additional permissions required by the console\. Other permissions allow AWS DMS to manage the bucket life cycle\. Also, the `s3:GetObjectAcl` action is required to be able to copy objects\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:CreateBucket",
                "s3:ListBucket",
                "s3:DeleteBucket",
                "s3:GetBucketLocation",
                "s3:GetObject",
                "s3:PutObject",
                "s3:DeleteObject",
                "s3:GetObjectVersion",
                "s3:GetBucketPolicy",
                "s3:PutBucketPolicy",
                "s3:GetBucketAcl",
                "s3:PutBucketVersioning",
                "s3:GetBucketVersioning",
                "s3:PutLifecycleConfiguration",
                "s3:GetLifecycleConfiguration",
                "s3:DeleteBucketPolicy"
            ],
            "Resource": "arn:aws:s3:::dms-*"
        }
    ]
}
```

For more information on creating a role based on this policy, see [Amazon S3 bucket settings](CHAP_Target.Redshift.md#CHAP_Target.Redshift.EndpointSettings.S3Buckets)\.

#### Accessing AWS DMS resources based on tags<a name="security_iam_id-based-policy-examples-access-resources-tags"></a>

You can use conditions in your identity\-based policy to control access to AWS DMS resources based on tags\. This example shows how you might create a policy that allows access to all AWS DMS endpoints\. However, permission is granted only if the endpoint database tag `Owner` has the value of that user's user name\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "dms:*",
            "Resource": "arn:aws:dms:*:*:endpoint/*",
            "Condition": {
                "StringEquals": {"dms:endpoint-tag/Owner": "${aws:username}"}
            }
        }
    ]
}
```

You can attach this policy to the IAM users in your account\. If a user named `richard-roe` attempts to access an AWS DMS endpoint, the endpoint database must be tagged `Owner=richard-roe` or `owner=richard-roe`\. Otherwise, this user is denied access\. The condition tag key `Owner` matches both `Owner` and `owner` because condition key names are not case\-sensitive\. For more information, see [IAM JSON policy elements: Condition](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_condition.html) in the *IAM User Guide*\.

### Resource\-based policy examples for AWS KMS<a name="security_iam_resource-based-policy-examples"></a>

AWS DMS allows you to create custom AWS KMS encryption keys to encrypt supported target endpoint data\. To learn how to create and attach a key policy to the encryption key you create for supported target data encryption, see [Creating and using AWS KMS keys to encrypt Amazon Redshift target data](CHAP_Target.Redshift.md#CHAP_Target.Redshift.KMSKeys) and [Creating AWS KMS keys to encrypt Amazon S3 target objects](CHAP_Target.S3.md#CHAP_Target.S3.KMSKeys)\.

**Topics**
+ [A policy for a custom AWS KMS encryption key to encrypt Amazon Redshift target data](#security_iam_resource-based-policy-examples-custom-rs-key-policy)
+ [A policy for a custom AWS KMS encryption key to encrypt Amazon S3 target data](#security_iam_resource-based-policy-examples-custom-s3-key-policy)

#### A policy for a custom AWS KMS encryption key to encrypt Amazon Redshift target data<a name="security_iam_resource-based-policy-examples-custom-rs-key-policy"></a>

The following example shows the JSON for the key policy created for an AWS KMS encryption key that you create to encrypt Amazon Redshift target data\.

```
{
  "Id": "key-consolepolicy-3",
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "Enable IAM User Permissions",
      "Effect": "Allow",
      "Principal": {
        "AWS": [
          "arn:aws:iam::987654321098:root"
        ]
      },
      "Action": "kms:*",
      "Resource": "*"
    },
    {
      "Sid": "Allow access for Key Administrators",
      "Effect": "Allow",
      "Principal": {
        "AWS": [
          "arn:aws:iam::987654321098:role/Admin"
        ]
      },
      "Action": [
        "kms:Create*",
        "kms:Describe*",
        "kms:Enable*",
        "kms:List*",
        "kms:Put*",
        "kms:Update*",
        "kms:Revoke*",
        "kms:Disable*",
        "kms:Get*",
        "kms:Delete*",
        "kms:TagResource",
        "kms:UntagResource",
        "kms:ScheduleKeyDeletion",
        "kms:CancelKeyDeletion"
      ],
      "Resource": "*"
    },
    {
      "Sid": "Allow use of the key",
      "Effect": "Allow",
      "Principal": {
        "AWS": [
          "arn:aws:iam::987654321098:role/DMS-Redshift-endpoint-access-role"
        ]
      },
      "Action": [
        "kms:Encrypt",
        "kms:Decrypt",
        "kms:ReEncrypt*",
        "kms:GenerateDataKey*",
        "kms:DescribeKey"
      ],
      "Resource": "*"
    },
    {
      "Sid": "Allow attachment of persistent resources",
      "Effect": "Allow",
      "Principal": {
        "AWS": [
          "arn:aws:iam::987654321098:role/DMS-Redshift-endpoint-access-role"
        ]
      },
      "Action": [
        "kms:CreateGrant",
        "kms:ListGrants",
        "kms:RevokeGrant"
      ],
      "Resource": "*",
      "Condition": {
        "Bool": {
          "kms:GrantIsForAWSResource": true
        }
      }
    }
  ]
}
```

Here, you can see where the key policy references the role for accessing Amazon Redshift target endpoint data that you created before creating the key\. In the example, that is `DMS-Redshift-endpoint-access-role`\. You can also see the different key actions permitted for the different principals \(users and roles\)\. For example, any user with `DMS-Redshift-endpoint-access-role` can encrypt, decrypt, and re\-encrypt the target data\. Such a user can also generate data keys for export to encrypt the data outside of AWS KMS\. They can also return detailed information about a AWS KMS key, such as the key that you just created\. In addition, such a user can manage attachments to AWS resources, such as the target endpoint\.

#### A policy for a custom AWS KMS encryption key to encrypt Amazon S3 target data<a name="security_iam_resource-based-policy-examples-custom-s3-key-policy"></a>

The following example shows the JSON for the key policy created for an AWS KMS encryption key that you create to encrypt Amazon S3 target data\.

```
{
  "Id": "key-consolepolicy-3",
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "Enable IAM User Permissions",
      "Effect": "Allow",
      "Principal": {
        "AWS": [
          "arn:aws:iam::987654321098:root"
        ]
      },
      "Action": "kms:*",
      "Resource": "*"
    },
    {
      "Sid": "Allow access for Key Administrators",
      "Effect": "Allow",
      "Principal": {
        "AWS": [
          "arn:aws:iam::987654321098:role/Admin"
        ]
      },
      "Action": [
        "kms:Create*",
        "kms:Describe*",
        "kms:Enable*",
        "kms:List*",
        "kms:Put*",
        "kms:Update*",
        "kms:Revoke*",
        "kms:Disable*",
        "kms:Get*",
        "kms:Delete*",
        "kms:TagResource",
        "kms:UntagResource",
        "kms:ScheduleKeyDeletion",
        "kms:CancelKeyDeletion"
      ],
      "Resource": "*"
    },
    {
      "Sid": "Allow use of the key",
      "Effect": "Allow",
      "Principal": {
        "AWS": [
          "arn:aws:iam::987654321098:role/DMS-S3-endpoint-access-role"
        ]
      },
      "Action": [
        "kms:Encrypt",
        "kms:Decrypt",
        "kms:ReEncrypt*",
        "kms:GenerateDataKey*",
        "kms:DescribeKey"
      ],
      "Resource": "*"
    },
    {
      "Sid": "Allow attachment of persistent resources",
      "Effect": "Allow",
      "Principal": {
        "AWS": [
          "arn:aws:iam::987654321098:role/DMS-S3-endpoint-access-role"
        ]
      },
      "Action": [
        "kms:CreateGrant",
        "kms:ListGrants",
        "kms:RevokeGrant"
      ],
      "Resource": "*",
      "Condition": {
        "Bool": {
          "kms:GrantIsForAWSResource": true
        }
      }
    }
  ]
```

Here, you can see where the key policy references the role for accessing Amazon S3 target endpoint data that you created prior to creating the key\. In the example, that is `DMS-S3-endpoint-access-role`\. You can also see the different key actions permitted for the different principals \(users and roles\)\. For example, any user with `DMS-S3-endpoint-access-role` can encrypt, decrypt, and re\-encrypt the target data\. Such a user can also generate data keys for export to encrypt the data outside of AWS KMS\. They can also return detailed information about a AWS KMS key, such as the key that you just created\. In addition, such a user can manage attachment to AWS resources, such as the target endpoint\.

### Using secrets to access AWS Database Migration Service endpoints<a name="security_iam_secretsmanager"></a>

For AWS DMS, a *secret* is an encrypted key that you can use to represent a set of user credentials to authenticate, through *secret authentication*, the database connection for a supported AWS DMS source or target endpoint\. For an Oracle endpoint that also uses Oracle Advanced Storage Management \(ASM\), AWS DMS requires an additional secret that represents the user credentials to access Oracle ASM\.

You can create the secret or secrets that AWS DMS requires for secret authentication using AWS Secrets Manager, a service for securely creating, storing, and retrieving credentials to access applications, services, and IT resources in the cloud and on premise\. This includes support for automatic periodic rotation of the encrypted secret value without your intervention, providing an extra level of security for your credentials\. Enabling secret value rotation in AWS Secrets Manager also ensures that this secret value rotation happens without any effect on any database migration that relies on the secret\. For secretly authenticating an endpoint database connection, create a secret whose identity or ARN you assign to `SecretsManagerSecretId`, which you include in your endpoint settings\. For secretly authenticating Oracle ASM as part of an Oracle endpoint, create a secret whose identity or ARN you assign to `SecretsManagerOracleAsmSecretId`, which you also include in your endpoint settings\.

For more information on AWS Secrets Manager, see [What Is AWS Secrets Manager?](https://docs.aws.amazon.com/secretsmanager/latest/userguide/intro.html) in the *AWS Secrets Manager User Guide*\.

AWS DMS supports secret authentication for the following on\-premise or AWS\-managed databases on supported source and target endpoints:
+ Amazon DocumentDB
+ IBM Db2 LUW
+ Microsoft SQL Server
+ MongoDB
+ MySQL
+ Oracle
+ PostgreSQL
+ Amazon Redshift
+ SAP ASE

For connection to any of these databases, you have the choice of entering one of the following sets of values, but not both, as part of your endpoint settings:
+ Clear\-text values to authenticate the database connection using the `UserName`, `Password`, `ServerName`, and `Port` settings\. For an Oracle endpoint that also uses Oracle ASM, include additional clear\-text values to authenticate ASM using the `AsmUserName`, `AsmPassword`, and `AsmServerName` settings\.
+ Secret authentication using values for the `SecretsManagerSecretId` and `SecretsManagerAccessRoleArn` settings\. For an Oracle endpoint using Oracle ASM, include additional values for the `SecretsManagerOracleAsmSecretId` and `SecretsManagerOracleAsmAccessRoleArn` settings\. The secret values for these settings can include the following for: 
  + `SecretsManagerSecretId` – The full Amazon Resource Name \(ARN\), partial ARN, or friendly name of a secret that you have created for endpoint database access in the AWS Secrets Manager\.
  + `SecretsManagerAccessRoleArn` – The ARN of a secret access role that you have created in IAM to provide AWS DMS access to this `SecretsManagerSecretId` secret on your behalf\.
  + `SecretsManagerOracleAsmSecretId` – The full Amazon Resource Name \(ARN\), partial ARN, or friendly name of a secret that you have created for Oracle ASM access in the AWS Secrets Manager\.
  + `SecretsManagerOracleAsmAccessRoleArn` – The ARN of a secret access role that you have created in IAM to provide AWS DMS access to this `SecretsManagerOracleAsmSecretId` secret on your behalf\.
**Note**  
You can also use a single secret access role to provide AWS DMS access to both the `SecretsManagerSecretId` secret and the `SecretsManagerOracleAsmSecretId` secret\. If you create this single secret access role for both secrets, ensure that you assign the same ARN for this access role to both `SecretsManagerAccessRoleArn` and `SecretsManagerOracleAsmAccessRoleArn`\. For example, if your secret access role for both secrets has its ARN assigned to the variable, `ARN2xsecrets`, you can set these ARN settings as follows:  

  ```
  SecretsManagerAccessRoleArn = ARN2xsecrets;
  SecretsManagerOracleAsmAccessRoleArn = ARN2xsecrets;
  ```

  For more information on creating these values, see [Using the AWS Management Console to create a secret and secret access role](#security_iam_secretsmanager.console)\.

After you have created and specified the required secret and secret access\-role endpoint settings for your endpoints, update the permissions on the user accounts that will run the `CreateEndpoint` or `ModifyEndpoint` API request with these secret details\. Ensure that these account permissions include the `IAM:GetRole` permission on the secret access role and the `SecretsManager:DescribeSecret` permission on the secret\. AWS DMS requires these permissions to validate both the access role and its secret\.

**To provide and verify required user permissions**

1. Sign in to the AWS Management Console and open the AWS Identity and Access Management console at [https://console.aws.amazon.com/iam/](https://console.aws.amazon.com/iam/)\.

1. Choose **Users**, then select the **User ID** used for making `CreateEndpoint` and `ModifyEndpoint` API calls\.

1. From the **Permissions **tab, choose **\{\} JSON**\.

1. Make sure the user has the permissions shown following\.

   ```
   {
   	"Statement": [{
   			"Effect": "Allow",
   			"Action": [
   				"iam:GetRole",
   				"iam:PassRole"
   			],
   			"Resource": "SECRET_ACCESS_ROLE_ARN"
   		},
   		{
   			"Effect": "Allow",
   			"Action": "secretsmanager:DescribeSecret",
   			"Resource": "SECRET_ARN"
   		}
   	]
   }
   ```

1. If the user doesn't have those permission, add the permissions\.

1. If you're using an IAM Role for making DMS API calls, repeat the steps above for the respective role\.

1. Open a terminal and use the AWS CLI to validate that permissions are given correctly by assuming the Role or User used above\.

   1. Validate user’s permission on the SecretAccessRole using the IAM `get-role` command\.

      ```
      aws iam get-role --role-name ROLE_NAME
      ```

      Replace *ROLE\_NAME* with the name of `SecretsManagerAccessRole`\.

      If the command returns an error message, make sure the permissions were given correctly\.

   1. Validate user’s permission on the secret using the Secrets Manager `describe-secret` command\.

      ```
      aws secretsmanager describe-secret --secret-id SECRET_NAME OR SECRET_ARN --region=REGION_NAME
      ```

      User can be the friendly name, partial ARN or the full ARN\. For more information, see [describe\-secret](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/describe-secret.html)\.

      If the command returns an error message, make sure the permissions were given correctly\.

#### Using the AWS Management Console to create a secret and secret access role<a name="security_iam_secretsmanager.console"></a>

You can use the AWS Management Console to create a secret for endpoint authentication and to create the policy and role to allow AWS DMS to access the secret on your behalf\.

**To create a secret using the AWS Management Console that AWS DMS can use to authenticate a database for source and target endpoint connections**

1. Sign in to the AWS Management Console and open the AWS Secrets Manager console at [https://console.aws.amazon.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. Choose **Store a new secret**\.

1. Under **Select secret type** on the **Store a new secret** page, choose **Other type of secrets**, then choose **Plaintext**\.
**Note**  
This is the only place that you need to enter clear text credentials to connect to your endpoint database from this point forward\.

1. In the **Plaintext** field: 
   + For a secret whose identity you assign to `SecretsManagerSecretId`, enter the following JSON structure\.

     ```
     {
       "username": db_username,
       "password": db_user_password,
       "port": db_port_number,
       "host": db_server_name
     }
     ```
**Note**  
This is the minimum list of JSON members required to authenticate the endpoint database\. You can add any additional JSON endpoint settings as JSON members in all lower case that you want\. However, AWS DMS ignores any additional JSON members for endpoint authentication\.

     Here, `db_username` is the name of the user accessing the database, `db_user_password` is the password of the database user, `db_port_number` is the port number to access the database, and `db_server_name` is the database server name \(address\) on the web, as in the following example\.

     ```
     {
       "username": "admin",
       "password": "some_password",
       "port": "8190",
       "host": "oracle101.abcdefghij.us-east-1.rds.amazonaws.com"
     }
     ```
   + For a secret whose identity you assign to `SecretsManagerOracleAsmSecretId`, enter the following JSON structure\.

     ```
     {
       "asm_user": asm_username,
       "asm_password": asm_user_password,
       "asm_server": asm_server_name
     }
     ```
**Note**  
This is the minimum list of JSON members required to authenticate Oracle ASM for an Oracle endpoint\. It is also the complete list that you can specify based on the available Oracle ASM endpoint settings\.

     Here, `asm_username` is the name of the user accessing Oracle ASM, `asm_user_password` is the password of the Oracle ASM user, and `asm_server_name` is the Oracle ASM server name \(address\) on the web, including the port, as in the following example\.

     ```
     { 
       "asm_user": "oracle_asm_user", 
       "asm_password": "oracle_asm_password",
       "asm_server": "oracle101.abcdefghij.us-east-1.rds.amazonaws.com:8190/+ASM" 
     }
     ```

1. Select an AWS KMS encryption key to encrypt the secret\. You can accept the default encryption key created for your service by AWS Secrets Manager or select a AWS KMS key that you create\.

1. Specify a name to reference this secret and an optional description\. This is the friendly name that you use as the value for `SecretsManagerSecretId` or `SecretsManagerOracleAsmSecretId`\.

1. If you want to enable automatic rotation in the secret, you need to select or create an AWS Lambda function with permission to rotate the credentials for the secret as described\. However, before setting automatic rotation to use your Lambda function, ensure that the configuration settings for the function add the following four characters to the value of the `EXCLUDE_CHARACTERS` environment variable\.

   ```
   ;.:+
   ```

   AWS DMS doesn't allow these characters in passwords used for endpoint credentials\. Configuring your Lambda function to exclude them prevents AWS Secrets Manager from generating these characters as part of its rotated password values\. After you set automatic rotation to use your Lambda function, AWS Secrets Manager immediately rotates the secret to validate your secret configuration\.
**Note**  
Depending on your database engine configuration, your database might not fetch the rotated credentials\. In this case, you need to manually restart the task to refresh the credentials\.

1. Review and store your secret in AWS Secrets Manager\. You can then look up each secret by its friendly name in AWS Secrets Manager, then retrieve the secret ARN as the value for `SecretsManagerSecretId` or `SecretsManagerOracleAsmSecretId` as appropriate to authenticate access to your endpoint database connection and Oracle ASM \(if used\)\.

**To create the secret access policy and role to set your `SecretsManagerAccessRoleArn` or `SecretsManagerOracleAsmAccessRoleArn`, which allows AWS DMS to access AWS Secrets Manager to access your appropriate secret**

1. Sign in to the AWS Management Console and open the AWS Identity and Access Management \(IAM\) console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. Choose **Policies**, then choose **Create policy**\.

1. Choose **JSON** and enter the following policy to enable access to and decryption of your secret\.

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Effect": "Allow",
               "Action": "secretsmanager:GetSecretValue",
               "Resource": secret_arn,
           },
           {
                "Effect": "Allow",
                "Action": [
                           "kms:Decrypt",
                           "kms:DescribeKey"
                         ],
                "Resource": kms_key_arn,
           }
        ]
   }
   ```

   Here, `secret_arn` is the ARN of your secret, which you can get from either `SecretsManagerSecretId` or `SecretsManagerOracleAsmSecretId` as appropriate, and `kms_key_arn` is the ARN of the AWS KMS key that you are using to encrypt your secret, as in the following example\.

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Effect": "Allow",
               "Action": "secretsmanager:GetSecretValue",
               "Resource": "arn:aws:secretsmanager:us-east-2:123456789012:secret:MySQLTestSecret-qeHamH"
           },
           {
                "Effect": "Allow",
                "Action": [
                           "kms:Decrypt",
                           "kms:DescribeKey"
                         ],
                "Resource": "arn:aws:kms:us-east-2:123456789012:key/761138dc-0542-4e58-947f-4a3a8458d0fd"
           }
        ]
   }
   ```
**Note**  
If you use the default encryption key created by AWS Secrets Manager, you do not have to specify the AWS KMS permissions for `kms_key_arn`\.  
If you want your policy to provide access to both secrets, simply specify an additional JSON resource object for the other *secret\_arn*\.

1. Review and create the policy with a friendly name and optional description\.

1. Choose **Roles**, then choose **Create role**\.

1. Choose **AWS service** as the type of trusted entity\.

1. Choose **DMS** from the list of services as the trusted service, then choose **Next: Permissions**\.

1. Look up and attach the policy you created in step 4, then proceed through adding any tags and review your role\. At this point, edit the trust relationships for the role to use your AWS DMS regional service principal as the trusted entity\. This principal has the following format\.

   ```
   dms.region-name.amazonaws.com
   ```

   Here, *`region-name`* is the name of your region, such as `us-east-1`\. Thus, an AWS DMS regional service principal for this region follows\.

   ```
   dms.us-east-1.amazonaws.com
   ```

1. After editing the trusted entity for the role, create the role with a friendly name and optional description\. You can now look up your new role by its friendly name in IAM, then retrieve the role ARN as the `SecretsManagerAccessRoleArn` or `SecretsManagerOracleAsmAccessRoleArn` value to authenticate your endpoint database connection\.

**To use secrets manager with a replication instance in a private subnet**

1. Create a secret manager VPC endpoint and note the DNS for the endpoint\. For more information about creating a secrets manager VPC endpoint, see [Connecting to Secrets Manager through a VPC endpoint](https://docs.aws.amazon.com/secretsmanager/latest/userguide/vpc-endpoint-overview.html#vpc-endpoint) []()in the *AWS Secrets Manager User Guide\.*

1. Attach the replication instance security group to the secret manager VPC endpoint\.

1. For the replication instance security group egress rules, allow all traffic for destination `0.0.0.0/0`\.

1. Set the endpoint extra connection attribute, `secretsManagerEndpointOverride=secretsManager endpoint DNS` to provide the secret manager VPC endpoint DNS, as shown in the following example\.

   ```
   secretsManagerEndpointOverride=vpce-1234a5678b9012c-12345678.secretsmanager.eu-west-1.vpce.amazonaws.com
   ```

### Troubleshooting AWS Database Migration Service identity and access<a name="security_iam_troubleshoot"></a>

Use the following information to help you diagnose and fix common issues that you might encounter when working with AWS DMS and IAM\.

**Topics**
+ [I am not authorized to perform an action in AWS DMS](#security_iam_troubleshoot-no-permissions)
+ [I am not authorized to perform iam:PassRole](#security_iam_troubleshoot-passrole)
+ [I want to view my access keys](#security_iam_troubleshoot-access-keys)
+ [I'm an administrator and want to allow others to access AWS DMS](#security_iam_troubleshoot-admin-delegate)
+ [I want to allow people outside of my AWS account to access my AWS DMS resources](#security_iam_troubleshoot-cross-account-access)

#### I am not authorized to perform an action in AWS DMS<a name="security_iam_troubleshoot-no-permissions"></a>

If the AWS Management Console tells you that you're not authorized to perform an action, then you must contact your administrator for assistance\. Your administrator is the person that provided you with your user name and password\.

The following example error occurs when the `mateojackson` IAM user tries to use the console to view details about an AWS DMS endpoint but does not have `dms: DescribeEndpoint` permissions\.

```
User: arn:aws:iam::123456789012:user/mateojackson is not authorized to perform: dms:DescribeEndpoint on resource: my-postgresql-target
```

In this case, Mateo asks his administrator to update his policies to allow him to access the `my-postgresql-target` endpoint resource using the `dms:DescribeEndpoint` action\.

#### I am not authorized to perform iam:PassRole<a name="security_iam_troubleshoot-passrole"></a>

If you receive an error that you're not authorized to perform the `iam:PassRole` action, then you must contact your administrator for assistance\. Your administrator is the person that provided you with your user name and password\. Ask that person to update your policies to allow you to pass a role to AWS DMS\.

Some AWS services allow you to pass an existing role to that service, instead of creating a new service role or service\-linked role\. To do this, you must have permissions to pass the role to the service\.

The following example error occurs when an IAM user named `marymajor` tries to use the console to perform an action in AWS DMS\. However, the action requires the service to have permissions granted by a service role\. Mary does not have permissions to pass the role to the service\.

```
User: arn:aws:iam::123456789012:user/marymajor is not authorized to perform: iam:PassRole
```

In this case, Mary asks her administrator to update her policies to allow her to perform the `iam:PassRole` action\.

#### I want to view my access keys<a name="security_iam_troubleshoot-access-keys"></a>

After you create your IAM user access keys, you can view your access key ID at any time\. However, you can't view your secret access key again\. If you lose your secret key, you must create a new access key pair\. 

Access keys consist of two parts: an access key ID \(for example, `AKIAIOSFODNN7EXAMPLE`\) and a secret access key \(for example, `wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY`\)\. Like a user name and password, you must use both the access key ID and secret access key together to authenticate your requests\. Manage your access keys as securely as you do your user name and password\.

**Important**  
 Do not provide your access keys to a third party, even to help [find your canonical user ID](https://docs.aws.amazon.com/general/latest/gr/acct-identifiers.html#FindingCanonicalId)\. By doing this, you might give someone permanent access to your account\. 

When you create an access key pair, you are prompted to save the access key ID and secret access key in a secure location\. The secret access key is available only at the time you create it\. If you lose your secret access key, you must add new access keys to your IAM user\. You can have a maximum of two access keys\. If you already have two, you must delete one key pair before creating a new one\. To view instructions, see [Managing access keys](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html#Using_CreateAccessKey) in the *IAM User Guide*\.

#### I'm an administrator and want to allow others to access AWS DMS<a name="security_iam_troubleshoot-admin-delegate"></a>

To allow others to access AWS DMS, you must create an IAM entity \(user or role\) for the person or application that needs access\. They will use the credentials for that entity to access AWS\. You must then attach a policy to the entity that grants them the correct permissions in AWS DMS\.

To get started right away, see [Creating your first IAM delegated user and group](https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started_create-delegated-user.html) in the *IAM User Guide*\.

#### I want to allow people outside of my AWS account to access my AWS DMS resources<a name="security_iam_troubleshoot-cross-account-access"></a>

You can create a role that users in other accounts or people outside of your organization can use to access your resources\. You can specify who is trusted to assume the role\. For services that support resource\-based policies or access control lists \(ACLs\), you can use those policies to grant people access to your resources\.

To learn more, consult the following:
+ To learn whether AWS DMS supports these features, see [How AWS Database Migration Service works with IAM](#security_iam_service-with-iam)\.
+ To learn how to provide access to your resources across AWS accounts that you own, see [Providing access to an IAM user in another AWS account that you own](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_common-scenarios_aws-accounts.html) in the *IAM User Guide*\.
+ To learn how to provide access to your resources to third\-party AWS accounts, see [Providing access to AWS accounts owned by third parties](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_common-scenarios_third-party.html) in the *IAM User Guide*\.
+ To learn how to provide access through identity federation, see [Providing access to externally authenticated users \(identity federation\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_common-scenarios_federated-users.html) in the *IAM User Guide*\.
+ To learn the difference between using roles and resource\-based policies for cross\-account access, see [How IAM roles differ from resource\-based policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_compare-resource-policies.html) in the *IAM User Guide*\.

## Compliance validation for AWS Database Migration Service<a name="dms-compliance"></a>

Third\-party auditors assess the security and compliance of AWS Database Migration Service as part of multiple AWS compliance programs\. These include the following programs:
+  SOC
+ PCI
+ ISO
+ FedRAMP
+ DoD CC SRG
+ HIPAA BAA
+ MTCS
+ CS
+ K\-ISMS
+ ENS High
+ OSPAR
+ HITRUST CSF

For a list of AWS services in scope of specific compliance programs, see [AWS services in scope by compliance program](http://aws.amazon.com/compliance/services-in-scope/)\. For general information, see [AWS compliance programs](http://aws.amazon.com/compliance/programs/)\.

You can download third\-party audit reports using AWS Artifact\. For more information, see [Downloading reports in AWS artifact](https://docs.aws.amazon.com/artifact/latest/ug/downloading-documents.html)\.

Your compliance responsibility when using AWS DMS is determined by the sensitivity of your data, your company's compliance objectives, and applicable laws and regulations\. AWS provides the following resources to help with compliance:
+ [Security and compliance quick start guides](http://aws.amazon.com/quickstart/?awsf.quickstart-homepage-filter=categories%23security-identity-compliance) – These deployment guides discuss architectural considerations and provide steps for deploying security\- and compliance\-focused baseline environments on AWS\.
+ [Architecting for HIPAA security and compliance whitepaper ](https://d0.awsstatic.com/whitepapers/compliance/AWS_HIPAA_Compliance_Whitepaper.pdf) – This whitepaper describes how companies can use AWS to create HIPAA\-compliant applications\.
+ [AWS compliance resources](http://aws.amazon.com/compliance/resources/) – This collection of workbooks and guides might apply to your industry and location\.
+ [AWS Config](https://docs.aws.amazon.com/config/latest/developerguide/evaluate-config.html) – This AWS service assesses how well your resource configurations comply with internal practices, industry guidelines, and regulations\.
+ [AWS Security Hub](https://docs.aws.amazon.com/securityhub/latest/userguide/what-is-securityhub.html) – This AWS service provides a comprehensive view of your security state within AWS that helps you check your compliance with security industry standards and best practices\.

## Resilience in AWS Database Migration Service<a name="disaster-recovery-resiliency"></a>

The AWS global infrastructure is built around AWS Regions and Availability Zones\. AWS Regions provide multiple physically separated and isolated Availability Zones, which are connected with low\-latency, high\-throughput, and highly redundant networking\. With Availability Zones, you can design and operate applications and databases that automatically fail over between Availability Zones without interruption\. Availability Zones are more highly available, fault tolerant, and scalable than traditional single or multiple data center infrastructures\. 

For more information about AWS Regions and Availability Zones, see [AWS global infrastructure](http://aws.amazon.com/about-aws/global-infrastructure/)\.

In addition to the AWS global infrastructure, AWS DMS provides high availability and failover support for a replication instance using a Multi\-AZ deployment when you choose the **Multi\-AZ** option\.

In a Multi\-AZ deployment, AWS DMS automatically provisions and maintains a standby replica of the replication instance in a different Availability Zone\. The primary replication instance is synchronously replicated to the standby replica\. If the primary replication instance fails or becomes unresponsive, the standby resumes any running tasks with minimal interruption\. Because the primary is constantly replicating its state to the standby, Multi\-AZ deployment does incur some performance overhead\.

For more information on working with Multi\-AZ deployments, see [Working with an AWS DMS replication instance](CHAP_ReplicationInstance.md)\.

## Infrastructure security in AWS Database Migration Service<a name="infrastructure-security"></a>

As a managed service, AWS Database Migration Service is protected by the AWS global network security procedures that are described in the [Amazon Web Services: Overview of security processes](https://d0.awsstatic.com/whitepapers/Security/AWS_Security_Whitepaper.pdf) whitepaper\.

You use AWS published API calls to access AWS DMS through the network\. Clients must support Transport Layer Security \(TLS\) 1\.0 or later\. Clients must also support cipher suites with perfect forward secrecy \(PFS\) such as Ephemeral Diffie\-Hellman \(DHE\) or Elliptic Curve Ephemeral Diffie\-Hellman \(ECDHE\)\. Most modern systems such as Java 7 and later support these modes\.

Additionally, requests must be signed by using an access key ID and a secret access key that is associated with an AWS Identity and Access Management \(IAM\) principal\. Or you can use the [AWS Security Token Service](https://docs.aws.amazon.com/STS/latest/APIReference/Welcome.html) \(AWS STS\) to generate temporary security credentials to sign requests\.

You can call these API operations from any network location\. AWS DMS also supports resource\-based access policies, which can specify restrictions on actions and resources, for example, based on the source IP address\. In addition, you can use AWS DMS policies to control access from specific Amazon VPC endpoints or specific virtual private clouds \(VPCs\)\. Effectively, this isolates network access to a given AWS DMS resource from only the specific VPC within the AWS network\. For more information about using resource\-based access policies with AWS DMS, including examples, see [Fine\-grained access control using resource names and tags](#CHAP_Security.FineGrainedAccess)\.

To confine your communications with AWS DMS within a single VPC, you can create a VPC interface endpoint that enables you to connect to AWS DMS through AWS PrivateLink\. AWS PrivateLink helps ensure that any call to AWS DMS and its associated results remain confined to the specific VPC for which your interface endpoint is created\. You can then specify the URL for this interface endpoint as an option with every AWS DMS command that you run using the AWS CLI or an SDK\. Doing this helps ensure that your entire communications with AWS DMS remain confined to the VPC and are otherwise invisible to the public internet\.

**To create an interface endpoint to access DMS in a single VPC**

1. Sign in to the AWS Management Console and open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. From the navigation pane, choose **Endpoints**\. This opens the **Create endpoints** page, where you can create the interface endpoint from a VPC to AWS DMS\.

1. Choose **AWS services**, then search for and choose a value for **Service Name**, in this case AWS DMS in the following form\.

   ```
   com.amazonaws.region.dms
   ```

   Here, *`region`* specifies the AWS Region where AWS DMS runs, for example `com.amazonaws.us-west-2.dms`\.

1. For **VPC**, choose the VPC to create the interface endpoint from, for example `vpc-12abcd34`\.

1. Choose a value for **Availability Zone** and for **Subnet ID**\. These values should indicate a location where your chosen AWS DMS endpoint can run, for example `us-west-2a (usw2-az1)` and `subnet-ab123cd4`\.

1. Choose **Enable DNS name** to create the endpoint with a DNS name\. This DNS name consists of the endpoint ID \(`vpce-12abcd34efg567hij`\) hyphenated with a random string \(`ab12dc34`\)\. These are separated from the service name by a dot in reverse dot\-separated order, with `vpce` added \(`dms.us-west-2.vpce.amazonaws.com`\)\. 

   An example is `vpce-12abcd34efg567hij-ab12dc34.dms.us-west-2.vpce.amazonaws.com`\.

1. For **Security group**, choose a group to use for the endpoint\.

   When you set up your security group, make sure to allow outbound HTTPS calls from within it\. For more information, see [Creating security groups](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html#CreatingSecurityGroups) in the *Amazon VPC User Guide*\. 

1. Choose either **Full Access** or a custom value for **Policy**\. For example, you might choose a custom policy similar to the following that restricts your endpoint's access to certain actions and resources\.

   ```
   {
     "Statement": [
       {
         "Action": "dms:*",
         "Effect": "Allow",
         "Resource": "*",
         "Principal": "*"
       },
       {
         "Action": [
           "dms:ModifyReplicationInstance",
           "dms:DeleteReplicationInstance"
         ],
         "Effect": "Deny",
         "Resource": "arn:aws:dms:us-west-2:<account-id>:rep:<replication-instance-id>",
         "Principal": "*"
       }
     ]
   }
   ```

   Here, the sample policy allows any AWS DMS API call, except for deleting or modifying a specific replication instance\.

You can now specify a URL formed using the DNS name created in step 6 as an option\. You specify this for every AWS DMS CLI command or API operation to access the service instance using the created interface endpoint\. For example, you might run the DMS CLI command `DescribeEndpoints` in this VPC as shown following\.

```
$ aws dms describe-endpoints --endpoint-url https://vpce-12abcd34efg567hij-ab12dc34.dms.us-west-2.vpce.amazonaws.com
```

If you enable the private DNS option, you don't have to specify the endpoint URL in the request\.

For more information on creating and using VPC interface endpoints \(including enabling the private DNS option\), see [Interface VPC endpoints \(AWS PrivateLink\)](https://docs.aws.amazon.com/vpc/latest/userguide/vpce-interface.html) in the *Amazon VPC User Guide*\.

## IAM permissions needed to use AWS DMS<a name="CHAP_Security.IAMPermissions"></a>

You use certain IAM permissions and IAM roles to use AWS DMS\. If you are signed in as an IAM user and want to use AWS DMS, your account administrator must attach the policy discussed in this section to the IAM user, group, or role that you use to run AWS DMS\. For more information about IAM permissions, see the [IAM User Guide](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction_access-management.html)\. 

The following policy gives you access to AWS DMS, and also permissions for certain actions needed from other Amazon services such as AWS KMS, IAM, Amazon EC2, and Amazon CloudWatch\. CloudWatch monitors your AWS DMS migration in real time and collects and tracks metrics that indicate the progress of your migration\. You can use CloudWatch Logs to debug problems with a task\. 

**Note**  
You can further restrict access to AWS DMS resources using tagging\. For more information about restricting access to AWS DMS resources using tagging, see [Fine\-grained access control using resource names and tags](#CHAP_Security.FineGrainedAccess)\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "dms:*",
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "kms:ListAliases", 
                "kms:DescribeKey"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "iam:GetRole",
                "iam:PassRole",
                "iam:CreateRole",
                "iam:AttachRolePolicy"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "ec2:DescribeVpcs",
                "ec2:DescribeInternetGateways",
                "ec2:DescribeAvailabilityZones",
                "ec2:DescribeSubnets",
                "ec2:DescribeSecurityGroups",
                "ec2:ModifyNetworkInterfaceAttribute",
                "ec2:CreateNetworkInterface",
                "ec2:DeleteNetworkInterface"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "cloudwatch:Get*",
                "cloudwatch:List*"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "logs:DescribeLogGroups",
                "logs:DescribeLogStreams",
                "logs:FilterLogEvents",
                "logs:GetLogEvents"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "redshift:Describe*",
                "redshift:ModifyClusterIamRoles"
            ],
            "Resource": "*"
        }
    ]
}
```

The breakdown of these following permissions might help you better understand why each one is necessary\.

The following section is required to allow the user to call AWS DMS API operations\.

```
{
            "Effect": "Allow",
            "Action": "dms:*",
            "Resource": "*"
}
```

The following section is required to allow the user to list their available AWS KMS keys and alias for display in the console\. This entry is not required if you know the Amazon Resource Name \(ARN\) for the KMS key and you are using only the AWS Command Line Interface \(AWS CLI\)\.

```
{
            "Effect": "Allow",
            "Action": [
                "kms:ListAliases", 
                "kms:DescribeKey"
            ],
            "Resource": "*"
        }
```

The following section is required for certain endpoint types that require an IAM role ARN to be passed in with the endpoint\. In addition, if the required AWS DMS roles aren't created ahead of time, the AWS DMS console can create the role\. If all roles are configured ahead of time, all that is required is `iam:GetRole` and `iam:PassRole`\. For more information about roles, see [Creating the IAM roles to use with the AWS CLI and AWS DMS API](#CHAP_Security.APIRole)\.

```
{
            "Effect": "Allow",
            "Action": [
                "iam:GetRole",
                "iam:PassRole",
                "iam:CreateRole",
                "iam:AttachRolePolicy"
            ],
            "Resource": "*"
        }
```

The following section is required because AWS DMS needs to create the Amazon EC2 instance and configure the network for the replication instance that is created\. These resources exist in the customer's account, so the ability to perform these actions on behalf of the customer is required\.

```
{
            "Effect": "Allow",
            "Action": [
                "ec2:DescribeVpcs",
                "ec2:DescribeInternetGateways",
                "ec2:DescribeAvailabilityZones",
                "ec2:DescribeSubnets",
                "ec2:DescribeSecurityGroups",
                "ec2:ModifyNetworkInterfaceAttribute",
                "ec2:CreateNetworkInterface",
                "ec2:DeleteNetworkInterface"
            ],
            "Resource": "*"
        }
```

The following section is required to allow the user to be able to view replication instance metrics\.

```
{
            "Effect": "Allow",
            "Action": [
                "cloudwatch:Get*",
                "cloudwatch:List*"
            ],
            "Resource": "*"
        }
```

This section is required to allow the user to view replication logs\.

```
{
            "Effect": "Allow",
            "Action": [
                "logs:DescribeLogGroups",
                "logs:DescribeLogStreams",
                "logs:FilterLogEvents",
                "logs:GetLogEvents"
            ],
            "Resource": "*"
        }
```

The following section is required when using Amazon Redshift as a target\. It allows AWS DMS to validate that the Amazon Redshift cluster is set up properly for AWS DMS\.

```
{
            "Effect": "Allow",
            "Action": [
                "redshift:Describe*",
                "redshift:ModifyClusterIamRoles"
            ],
            "Resource": "*"
        }
```

The AWS DMS console creates several roles that are automatically attached to your AWS account when you use the AWS DMS console\. If you use the AWS Command Line Interface \(AWS CLI\) or the AWS DMS API for your migration, you need to add these roles to your account\. For more information about adding these roles, see [Creating the IAM roles to use with the AWS CLI and AWS DMS API](#CHAP_Security.APIRole)\.

## Creating the IAM roles to use with the AWS CLI and AWS DMS API<a name="CHAP_Security.APIRole"></a>

If you use the AWS CLI or the AWS DMS API for your database migration, you must add three IAM roles to your AWS account before you can use the features of AWS DMS\. Two of these are `dms-vpc-role` and `dms-cloudwatch-logs-role`\. If you use Amazon Redshift as a target database, you must also add the IAM role `dms-access-for-endpoint` to your AWS account\.

Updates to managed policies are automatic\. If you are using a custom policy with the IAM roles, be sure to periodically check for updates to the managed policy in this documentation\. You can view the details of the managed policy by using a combination of the `get-policy` and `get-policy-version` commands\.

For example, the following `get-policy` command retrieves information about the specified IAM role\.

```
aws iam get-policy --policy-arn arn:aws:iam::aws:policy/service-role/AmazonDMSVPCManagementRole
```

The information returned from the command is as follows\.

```
{
    "Policy": {
        "PolicyName": "AmazonDMSVPCManagementRole", 
        "Description": "Provides access to manage VPC settings for AWS managed customer configurations", 
        "CreateDate": "2015-11-18T16:33:19Z", 
        "AttachmentCount": 1, 
        "IsAttachable": true, 
        "PolicyId": "ANPAJHKIGMBQI4AEFFSYO", 
        "DefaultVersionId": "v3", 
        "Path": "/service-role/", 
        "Arn": "arn:aws:iam::aws:policy/service-role/AmazonDMSVPCManagementRole", 
        "UpdateDate": "2016-05-23T16:29:57Z"
    }
}
```

The following `get-policy-version` command retrieves IAM policy information\.

```
aws iam get-policy-version --policy-arn arn:aws:iam::aws:policy/service-role/AmazonDMSVPCManagementRole --version-id v3
```

The information returned from the command is as follows\.

```
{
    "PolicyVersion": {
        "CreateDate": "2016-05-23T16:29:57Z", 
        "VersionId": "v3", 
        "Document": {
        "Version": "2012-10-17", 
        "Statement": [
            {
                "Action": [
                    "ec2:CreateNetworkInterface", 
                    "ec2:DescribeAvailabilityZones", 
                    "ec2:DescribeInternetGateways", 
                    "ec2:DescribeSecurityGroups", 
                    "ec2:DescribeSubnets", 
                    "ec2:DescribeVpcs", 
                    "ec2:DeleteNetworkInterface", 
                    "ec2:ModifyNetworkInterfaceAttribute"
                ], 
                "Resource": "*", 
                "Effect": "Allow"
            }
        ]
    }, 
    "IsDefaultVersion": true
    }
}
```

You can use the same commands to get information about `AmazonDMSCloudWatchLogsRole` and the `AmazonDMSRedshiftS3Role` managed policy\.

**Note**  
If you use the AWS DMS console for your database migration, these roles are added to your AWS account automatically\.

The following procedures create the `dms-vpc-role`, `dms-cloudwatch-logs-role`, and `dms-access-for-endpoint` IAM roles\.

**To create the dms\-vpc\-role IAM role for use with the AWS CLI or AWS DMS API**

1.  Create a JSON file with the following IAM policy\. Name the JSON file `dmsAssumeRolePolicyDocument.json`\. 

   ```
   {
      "Version": "2012-10-17",
      "Statement": [
      {
        "Effect": "Allow",
        "Principal": {
           "Service": "dms.amazonaws.com"
        },
      "Action": "sts:AssumeRole"
      }
    ]
   }
   ```

    Create the role using the AWS CLI using the following command\.

   ```
   aws iam create-role --role-name dms-vpc-role --assume-role-policy-document file://dmsAssumeRolePolicyDocument.json                    
   ```

1.  Attach the `AmazonDMSVPCManagementRole` policy to `dms-vpc-role` using the following command\.

   ```
   aws iam attach-role-policy --role-name dms-vpc-role --policy-arn arn:aws:iam::aws:policy/service-role/AmazonDMSVPCManagementRole                    
   ```

**To create the dms\-cloudwatch\-logs\-role IAM role for use with the AWS CLI or AWS DMS API**

1.  Create a JSON file with the following IAM policy\. Name the JSON file `dmsAssumeRolePolicyDocument2.json`\. 

   ```
   {
      "Version": "2012-10-17",
      "Statement": [
      {
        "Effect": "Allow",
        "Principal": {
           "Service": "dms.amazonaws.com"
        },
      "Action": "sts:AssumeRole"
      }
    ]
   }
   ```

    Create the role using the AWS CLI using the following command\.

   ```
   aws iam create-role --role-name dms-cloudwatch-logs-role --assume-role-policy-document file://dmsAssumeRolePolicyDocument2.json                    
   ```

1.  Attach the `AmazonDMSCloudWatchLogsRole` policy to `dms-cloudwatch-logs-role` using the following command\.

   ```
   aws iam attach-role-policy --role-name dms-cloudwatch-logs-role --policy-arn arn:aws:iam::aws:policy/service-role/AmazonDMSCloudWatchLogsRole                    
   ```

If you use Amazon Redshift as your target database, you must create the IAM role `dms-access-for-endpoint` to provide access to Amazon S3\.

**To create the dms\-access\-for\-endpoint IAM role for use with Amazon Redshift as a target database**

1. Create a JSON file with the following IAM policy\. Name the JSON file `dmsAssumeRolePolicyDocument3.json`\. 

   ```
    {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Sid": "1",
         "Effect": "Allow",
         "Principal": {
           "Service": "dms.amazonaws.com"
         },
         "Action": "sts:AssumeRole"
       },
       {
         "Sid": "2",
         "Effect": "Allow",
         "Principal": {
           "Service": "redshift.amazonaws.com"
         },
         "Action": "sts:AssumeRole"
       }
     ]
   }
   ```

1.  Create the role using the AWS CLI using the following command\.

   ```
     aws iam create-role --role-name dms-access-for-endpoint --assume-role-policy-document file://dmsAssumeRolePolicyDocument3.json                   
   ```

1.  Attach the `AmazonDMSRedshiftS3Role` policy to `dms-access-for-endpoint` role using the following command\.

   ```
   aws iam attach-role-policy --role-name dms-access-for-endpoint \
       --policy-arn arn:aws:iam::aws:policy/service-role/AmazonDMSRedshiftS3Role
   ```

You should now have the IAM policies in place to use the AWS CLI or AWS DMS API\.

## Fine\-grained access control using resource names and tags<a name="CHAP_Security.FineGrainedAccess"></a>

You can use resource names and resource tags based on Amazon Resource Names \(ARNs\) to manage access to AWS DMS resources\. You do this by defining permitted action or including conditional statements in IAM policies\. 

### Using resource names to control access<a name="CHAP_Security.FineGrainedAccess.ResourceName"></a>

You can create an IAM user account and assign a policy based on the AWS DMS resource's ARN\.

The following policy denies access to the AWS DMS replication instance with the ARN *arn:aws:dms:us\-east\-1:152683116:rep:DOH67ZTOXGLIXMIHKITV*:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "dms:*"
            ],
            "Effect": "Deny",
            "Resource": "arn:aws:dms:us-east-1:152683116:rep:DOH67ZTOXGLIXMIHKITV"
        }
    ]
}
```

For example, the following commands fail when the policy is in effect\.

```
$ aws dms delete-replication-instance 
   --replication-instance-arn "arn:aws:dms:us-east-1:152683116:rep:DOH67ZTOXGLIXMIHKITV"

A client error (AccessDeniedException) occurred when calling the DeleteReplicationInstance 
operation: User: arn:aws:iam::152683116:user/dmstestusr is not authorized to perform: 
dms:DeleteReplicationInstance on resource: arn:aws:dms:us-east-1:152683116:rep:DOH67ZTOXGLIXMIHKITV

$ aws dms modify-replication-instance 
   --replication-instance-arn "arn:aws:dms:us-east-1:152683116:rep:DOH67ZTOXGLIXMIHKITV"

A client error (AccessDeniedException) occurred when calling the ModifyReplicationInstance 
operation: User: arn:aws:iam::152683116:user/dmstestusr is not authorized to perform: 
dms:ModifyReplicationInstance on resource: arn:aws:dms:us-east-1:152683116:rep:DOH67ZTOXGLIXMIHKITV
```

You can also specify IAM policies that limit access to AWS DMS endpoints and replication tasks\.

The following policy limits access to an AWS DMS endpoint using the endpoint's ARN\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "dms:*"
            ],
            "Effect": "Deny",
            "Resource": "arn:aws:dms:us-east-1:152683116:endpoint:D6E37YBXTNHOA6XRQSZCUGX"
        }
    ]
}
```

For example, the following commands fail when the policy using the endpoint's ARN is in effect\.

```
$ aws dms delete-endpoint 
   --endpoint-arn "arn:aws:dms:us-east-1:152683116:endpoint:D6E37YBXTNHOA6XRQSZCUGX"

A client error (AccessDeniedException) occurred when calling the DeleteEndpoint operation: 
User: arn:aws:iam::152683116:user/dmstestusr is not authorized to perform: dms:DeleteEndpoint 
on resource: arn:aws:dms:us-east-1:152683116:endpoint:D6E37YBXTNHOA6XRQSZCUGX

$ aws dms modify-endpoint 
   --endpoint-arn "arn:aws:dms:us-east-1:152683116:endpoint:D6E37YBXTNHOA6XRQSZCUGX"     

A client error (AccessDeniedException) occurred when calling the ModifyEndpoint operation: 
User: arn:aws:iam::152683116:user/dmstestusr is not authorized to perform: dms:ModifyEndpoint 
on resource: arn:aws:dms:us-east-1:152683116:endpoint:D6E37YBXTNHOA6XRQSZCUGX
```

The following policy limits access to an AWS DMS task using the task's ARN\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "dms:*"
            ],
            "Effect": "Deny",
            "Resource": "arn:aws:dms:us-east-1:152683116:task:UO3YR4N47DXH3ATT4YMWOIT"
        }
    ]
}
```

For example, the following commands fail when the policy using the task's ARN is in effect\.

```
$ aws dms delete-replication-task 
   --replication-task-arn "arn:aws:dms:us-east-1:152683116:task:UO3YR4N47DXH3ATT4YMWOIT"

A client error (AccessDeniedException) occurred when calling the DeleteReplicationTask operation: 
User: arn:aws:iam::152683116:user/dmstestusr is not authorized to perform: dms:DeleteReplicationTask 
on resource: arn:aws:dms:us-east-1:152683116:task:UO3YR4N47DXH3ATT4YMWOIT
```

### Using tags to control access<a name="CHAP_Security.FineGrainedAccess.Tags"></a>

AWS DMS defines a set of common key\-value pairs that are available for use in customer defined policies without any additional tagging requirements\. For more information about tagging AWS DMS resources, see [Tagging resources in AWS Database Migration Service](CHAP_Tagging.md)\. 

The following lists the standard tags available for use with AWS DMS: 
+  aws:CurrentTime – Represents the request date and time, allowing the restriction of access based on temporal criteria\. 
+  aws:EpochTime – This tag is similar to the aws:CurrentTime tag preceding, except that the current time is represented as the number of seconds elapsed since the Unix epoch\. 
+  aws:MultiFactorAuthPresent – This is a Boolean tag that indicates whether or not the request was signed via multi\-factor authentication\. 
+  aws:MultiFactorAuthAge – Provides access to the age of the multi\-factor authentication token \(in seconds\)\. 
+  aws:principaltype – Provides access to the type of principal \(user, account, federated user, etc\.\) for the current request\. 
+  aws:SourceIp – Represents the source ip address for the user issuing the request\. 
+  aws:UserAgent – Provides information about the client application requesting a resource\. 
+  aws:userid – Provides access to the ID of the user issuing the request\. 
+  aws:username – Provides access to the name of the user issuing the request\. 
+  dms:InstanceClass – Provides access to the compute size of the replication instance host\(s\)\. 
+  dms:StorageSize – Provides access to the storage volume size \(in GB\)\. 

You can also define your own tags\. Customer\-defined tags are simple key\-value pairs that are persisted in the AWS tagging service\. You can add these to AWS DMS resources, including replication instances, endpoints, and tasks\. These tags are matched by using IAM "Conditional" statements in policies, and are referenced using a specific conditional tag\. The tag keys are prefixed with "dms", the resource type, and the "tag" prefix\. The following shows the tag format\.

```
dms:{resource type}-tag/{tag key}={tag value}
```

For example, suppose that you want to define a policy that only allows an API call to succeed for a replication instance that contains the tag "stage=production"\. The following conditional statement matches a resource with the given tag\.

```
"Condition":
{
    "streq":
        {
            "dms:rep-tag/stage":"production"
        }
}
```

You add the following tag to a replication instance that matches this policy condition\. 

```
stage production
```

In addition to tags already assigned to AWS DMS resources, policies can also be written to limit the tag keys and values that can be applied to a given resource\. In this case, the tag prefix is "req"\. 

For example, the following policy statement limits the tags that a user can assign to a given resource to a specific list of allowed values\.

```
 "Condition":
{
    "streq":
        {
            "dms:rep-tag/stage": [ "production", "development", "testing" ]
        }
}
```

The following policy examples limit access to an AWS DMS resource based on resource tags\.

The following policy limits access to a replication instance where the tag value is "Desktop" and the tag key is "Env":

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "dms:*"
            ],
            "Effect": "Deny",
            "Resource": "*",
            "Condition": {
                "StringEquals": {
                    "dms:rep-tag/Env": [
                        "Desktop"
                    ]
                }
            }
        }
    ]
}
```

The following commands succeed or fail based on the IAM policy that restricts access when the tag value is "Desktop" and the tag key is "Env"\.

```
$ aws dms list-tags-for-resource 
   --resource-name arn:aws:dms:us-east-1:152683116:rep:46DHOU7JOJYOJXWDOZNFEN 
   --endpoint-url http://localhost:8000                                   
{
    "TagList": [
        {
            "Value": "Desktop", 
            "Key": "Env"
        }
    ]
}

$ aws dms delete-replication-instance 
   --replication-instance-arn "arn:aws:dms:us-east-1:152683116:rep:46DHOU7JOJYOJXWDOZNFEN"
A client error (AccessDeniedException) occurred when calling the DeleteReplicationInstance 
operation: User: arn:aws:iam::152683116:user/dmstestusr is not authorized to perform: 
dms:DeleteReplicationInstance on resource: arn:aws:dms:us-east-1:152683116:rep:46DHOU7JOJYOJXWDOZNFEN

$ aws dms modify-replication-instance 
   --replication-instance-arn "arn:aws:dms:us-east-1:152683116:rep:46DHOU7JOJYOJXWDOZNFEN" 

A client error (AccessDeniedException) occurred when calling the ModifyReplicationInstance 
operation: User: arn:aws:iam::152683116:user/dmstestusr is not authorized to perform: 
dms:ModifyReplicationInstance on resource: arn:aws:dms:us-east-1:152683116:rep:46DHOU7JOJYOJXWDOZNFEN

$ aws dms add-tags-to-resource 
   --resource-name arn:aws:dms:us-east-1:152683116:rep:46DHOU7JOJYOJXWDOZNFEN 
   --tags Key=CostCenter,Value=1234 

A client error (AccessDeniedException) occurred when calling the AddTagsToResource 
operation: User: arn:aws:iam::152683116:user/dmstestusr is not authorized to perform: 
dms:AddTagsToResource on resource: arn:aws:dms:us-east-1:152683116:rep:46DHOU7JOJYOJXWDOZNFEN

$ aws dms remove-tags-from-resource 
   --resource-name arn:aws:dms:us-east-1:152683116:rep:46DHOU7JOJYOJXWDOZNFEN 
   --tag-keys Env             

A client error (AccessDeniedException) occurred when calling the RemoveTagsFromResource 
operation: User: arn:aws:iam::152683116:user/dmstestusr is not authorized to perform: 
dms:RemoveTagsFromResource on resource: arn:aws:dms:us-east-1:152683116:rep:46DHOU7JOJYOJXWDOZNFEN
```

The following policy limits access to an AWS DMS endpoint where the tag value is "Desktop" and the tag key is "Env"\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "dms:*"
            ],
            "Effect": "Deny",
            "Resource": "*",
            "Condition": {
                "StringEquals": {
                    "dms:endpoint-tag/Env": [
                        "Desktop"
                    ]
                }
            }
        }
    ]
}
```

The following commands succeed or fail based on the IAM policy that restricts access when the tag value is "Desktop" and the tag key is "Env"\.

```
$ aws dms list-tags-for-resource 
   --resource-name arn:aws:dms:us-east-1:152683116:endpoint:J2YCZPNGOLFY52344IZWA6I
{
    "TagList": [
        {
            "Value": "Desktop", 
            "Key": "Env"
        }
    ]
}

$ aws dms delete-endpoint 
   --endpoint-arn "arn:aws:dms:us-east-1:152683116:endpoint:J2YCZPNGOLFY52344IZWA6I"

A client error (AccessDeniedException) occurred when calling the DeleteEndpoint 
operation: User: arn:aws:iam::152683116:user/dmstestusr is not authorized to perform: 
dms:DeleteEndpoint on resource: arn:aws:dms:us-east-1:152683116:endpoint:J2YCZPNGOLFY52344IZWA6I

$ aws dms modify-endpoint 
   --endpoint-arn "arn:aws:dms:us-east-1:152683116:endpoint:J2YCZPNGOLFY52344IZWA6I"    

A client error (AccessDeniedException) occurred when calling the ModifyEndpoint 
operation: User: arn:aws:iam::152683116:user/dmstestusr is not authorized to perform: 
dms:ModifyEndpoint on resource: arn:aws:dms:us-east-1:152683116:endpoint:J2YCZPNGOLFY52344IZWA6I

$ aws dms add-tags-to-resource 
   --resource-name arn:aws:dms:us-east-1:152683116:endpoint:J2YCZPNGOLFY52344IZWA6I 
   --tags Key=CostCenter,Value=1234

A client error (AccessDeniedException) occurred when calling the AddTagsToResource 
operation: User: arn:aws:iam::152683116:user/dmstestusr is not authorized to perform: 
dms:AddTagsToResource on resource: arn:aws:dms:us-east-1:152683116:endpoint:J2YCZPNGOLFY52344IZWA6I

$ aws dms remove-tags-from-resource 
   --resource-name arn:aws:dms:us-east-1:152683116:endpoint:J2YCZPNGOLFY52344IZWA6I 
   --tag-keys Env

A client error (AccessDeniedException) occurred when calling the RemoveTagsFromResource 
operation: User: arn:aws:iam::152683116:user/dmstestusr is not authorized to perform: 
dms:RemoveTagsFromResource on resource: arn:aws:dms:us-east-1:152683116:endpoint:J2YCZPNGOLFY52344IZWA6I
```

The following policy limits access to a replication task where the tag value is "Desktop" and the tag key is "Env"\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "dms:*"
            ],
            "Effect": "Deny",
            "Resource": "*",
            "Condition": {
                "StringEquals": {
                    "dms:task-tag/Env": [
                        "Desktop"
                    ]
                }
            }
        }
    ]
}
```

The following commands succeed or fail based on the IAM policy that restricts access when the tag value is "Desktop" and the tag key is "Env"\.

```
$ aws dms list-tags-for-resource 
   --resource-name arn:aws:dms:us-east-1:152683116:task:RB7N24J2XBUPS3RFABZTG3
{
    "TagList": [
        {
            "Value": "Desktop", 
            "Key": "Env"
        }
    ]
}

$ aws dms delete-replication-task 
   --replication-task-arn "arn:aws:dms:us-east-1:152683116:task:RB7N24J2XBUPS3RFABZTG3"

A client error (AccessDeniedException) occurred when calling the DeleteReplicationTask 
operation: User: arn:aws:iam::152683116:user/dmstestusr is not authorized to perform: 
dms:DeleteReplicationTask on resource: arn:aws:dms:us-east-1:152683116:task:RB7N24J2XBUPS3RFABZTG3

$ aws dms add-tags-to-resource 
   --resource-name arn:aws:dms:us-east-1:152683116:task:RB7N24J2XBUPS3RFABZTG3 
   --tags Key=CostCenter,Value=1234

A client error (AccessDeniedException) occurred when calling the AddTagsToResource 
operation: User: arn:aws:iam::152683116:user/dmstestusr is not authorized to perform: 
dms:AddTagsToResource on resource: arn:aws:dms:us-east-1:152683116:task:RB7N24J2XBUPS3RFABZTG3

$ aws dms remove-tags-from-resource 
   --resource-name arn:aws:dms:us-east-1:152683116:task:RB7N24J2XBUPS3RFABZTG3 
   --tag-keys Env

A client error (AccessDeniedException) occurred when calling the RemoveTagsFromResource 
operation: User: arn:aws:iam::152683116:user/dmstestusr is not authorized to perform: 
dms:RemoveTagsFromResource on resource: arn:aws:dms:us-east-1:152683116:task:RB7N24J2XBUPS3RFABZTG3
```

## Setting an encryption key and specifying AWS KMS permissions<a name="CHAP_Security.EncryptionKey"></a>

AWS DMS encrypts the storage used by a replication instance and the endpoint connection information\. To encrypt the storage used by a replication instance, AWS DMS uses an AWS Key Management Service \(AWS KMS\) key that is unique to your AWS account\. You can view and manage this key with AWS KMS\. You can use the default KMS key in your account \(`aws/dms`\) or you can create a custom KMS key\. If you have an existing KMS key, you can also use that key for encryption\.

**Note**  
Any custom or existing AWS KMS key that you use as an encryption key must be a symmetric key\. AWS DMS does not support the use of asymmetric encryption keys\. For more information on symmetric and asymmetric encryption keys, see [https://docs.aws.amazon.com/kms/latest/developerguide/symmetric-asymmetric.html](https://docs.aws.amazon.com/kms/latest/developerguide/symmetric-asymmetric.html) in the *AWS Key Management Service Developer Guide*\.

The default KMS key \(`aws/dms`\) is created when you first launch a replication instance, if you haven't selected a custom KMS key from the **Advanced** section of the **Create Replication Instance** page\. If you use the default KMS key, the only permissions you need to grant to the IAM user account you are using for migration are `kms:ListAliases` and `kms:DescribeKey`\. For more information about using the default KMS key, see [IAM permissions needed to use AWS DMS](#CHAP_Security.IAMPermissions)\. 

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
+  [ Configuration with two VPCs](CHAP_ReplicationInstance.VPC.md#CHAP_ReplicationInstance.VPC.Configurations.ScenarioVPCPeer) – The security group used by the replication instance must have a rule for the VPC range and the DB port on the database\. 
+  [ Configuration for a network to a VPC using AWS Direct Connect or a VPN](CHAP_ReplicationInstance.VPC.md#CHAP_ReplicationInstance.VPC.Configurations.ScenarioDirect) – a VPN tunnel allowing traffic to tunnel from the VPC into an on\- premises VPN\. In this configuration, the VPC includes a routing rule that sends traffic destined for a specific IP address or range to a host that can bridge traffic from the VPC into the on\-premises VPN\. If this case, the NAT host includes its own Security Group settings that must allow traffic from the Replication Instance's private IP address or security group into the NAT instance\. 
+  [ Configuration for a network to a VPC using the internet](CHAP_ReplicationInstance.VPC.md#CHAP_ReplicationInstance.VPC.Configurations.ScenarioInternet) – The VPC security group must include routing rules that send traffic not destined for the VPC to the Internet gateway\. In this configuration, the connection to the endpoint appears to come from the public IP address on the replication instance\. 
+  [Configuration with an RDS DB instance not in a VPC to a DB instance in a VPC using ClassicLink](CHAP_ReplicationInstance.VPC.md#CHAP_ReplicationInstance.VPC.Configurations.ClassicLink) – When the source or target Amazon RDS DB instance is not in a VPC and does not share a security group with the VPC where the replication instance is located, you can setup a proxy server and use ClassicLink to connect the source and target databases\. 
+  **Source endpoint is outside the VPC used by the replication instance and uses a NAT gateway** – You can configure a network address translation \(NAT\) gateway using a single Elastic IP address bound to a single Elastic network interface\. This Elastic network interface then receives a NAT identifier \(nat\-\#\#\#\#\#\)\. If the VPC includes a default route to that NAT gateway instead of the internet gateway, the replication instance instead appears to contact the database endpoint using the public IP address of the internet gateway\. In this case, the ingress to the database endpoint outside the VPC needs to allow ingress from the NAT address instead of the replication instance's public IP address\. 
+ **VPC endpoints for non\-RDBMS engines** – AWS DMS doesn’t support VPC endpoints for non\-RDBMS engines\.

## Using SSL with AWS Database Migration Service<a name="CHAP_Security.SSL"></a>

You can encrypt connections for source and target endpoints by using Secure Sockets Layer \(SSL\)\. To do so, you can use the AWS DMS Management Console or AWS DMS API to assign a certificate to an endpoint\. You can also use the AWS DMS console to manage your certificates\. 

Not all databases use SSL in the same way\. Amazon Aurora MySQL\-Compatible Edition uses the server name, the endpoint of the primary instance in the cluster, as the endpoint for SSL\. An Amazon Redshift endpoint already uses an SSL connection and does not require an SSL connection set up by AWS DMS\. An Oracle endpoint requires additional steps; for more information, see [SSL support for an Oracle endpoint](CHAP_Source.Oracle.md#CHAP_Security.SSL.Oracle)\.

**Topics**
+ [Limitations on using SSL with AWS DMS](#CHAP_Security.SSL.Limitations)
+ [Managing certificates](#CHAP_Security.SSL.ManagingCerts)
+ [Enabling SSL for a MySQL\-compatible, PostgreSQL, or SQL Server endpoint](#CHAP_Security.SSL.Procedure)

To assign a certificate to an endpoint, you provide the root certificate or the chain of intermediate CA certificates leading up to the root \(as a certificate bundle\), that was used to sign the server SSL certificate that is deployed on your endpoint\. Certificates are accepted as PEM formatted X509 files, only\. When you import a certificate, you receive an Amazon Resource Name \(ARN\) that you can use to specify that certificate for an endpoint\. If you use Amazon RDS, you can download the root CA and certificate bundle provided in the `rds-combined-ca-bundle.pem` file hosted by Amazon RDS\. For more information about downloading this file, see [Using SSL/TLS to encrypt a connection to a DB instance](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/UsingWithRDS.SSL.html) in the *Amazon RDS User Guide*\.

You can choose from several SSL modes to use for your SSL certificate verification\. 
+ **none** – The connection is not encrypted\. This option is not secure, but requires less overhead\.
+ **require** – The connection is encrypted using SSL \(TLS\) but no CA verification is made\. This option is more secure, and requires more overhead\. 
+ **verify\-ca** – The connection is encrypted\. This option is more secure, and requires more overhead\. This option verifies the server certificate\. 
+ **verify\-full** – The connection is encrypted\. This option is more secure, and requires more overhead\. This option verifies the server certificate and verifies that the server hostname matches the hostname attribute for the certificate\. 

Not all SSL modes work with all database endpoints\. The following table shows which SSL modes are supported for each database engine\.


|  DB engine  |  **none**  |  **require**  |  **verify\-ca**  |  **verify\-full**  | 
| --- | --- | --- | --- | --- | 
|  MySQL/MariaDB/Amazon Aurora MySQL  | Default | Not supported | Supported | Supported | 
|  Microsoft SQL Server  | Default | Supported | Not Supported | Supported | 
|  PostgreSQL  | Default | Supported | Supported | Supported | 
|  Amazon Redshift  | Default | SSL not enabled | SSL not enabled | SSL not enabled | 
|  Oracle  | Default | Not supported | Supported | Not Supported | 
|  SAP ASE  | Default | SSL not enabled | SSL not enabled | Supported | 
|  MongoDB  | Default | Supported | Not Supported | Supported | 
|  Db2 LUW  | Default | Not Supported | Supported | Not Supported | 

**Note**  
The SSL Mode option on the DMS console or API doesn’t apply to some data streaming and NoSQL services like Kinesis, and DynamoDB\. They are secure by default, so DMS shows the SSL mode setting is equal to none \(**SSL Mode=None**\)\. You don’t need to provide any additional configuration for your endpoint to make use of SSL\. For example, when using Kinesis as a target endpoint, it is secure by default\. All API calls to Kinesis use SSL, so there is no need for an additional SSL option in the DMS endpoint\. You can securely put data and retrieve data through SSL endpoints using the HTTPS protocol, which DMS uses by default when connecting to a Kinesis Data Stream\.

### Limitations on using SSL with AWS DMS<a name="CHAP_Security.SSL.Limitations"></a>

Following are limitations on using SSL with AWS DMS:
+ SSL connections to Amazon Redshift target endpoints aren't supported\. AWS DMS uses an Amazon S3 bucket to transfer data to the Amazon Redshift database\. This transmission is encrypted by Amazon Redshift by default\. 
+ SQL timeouts can occur when performing change data capture \(CDC\) tasks with SSL\-enabled Oracle endpoints\. If you have an issue where CDC counters don't reflect the expected numbers, set the `MinimumTransactionSize` parameter from the `ChangeProcessingTuning` section of the task settings to a lower value\. You can start with a value as low as 100\. For more information about the `MinimumTransactionSize` parameter, see [Change processing tuning settings](CHAP_Tasks.CustomizingTasks.TaskSettings.ChangeProcessingTuning.md)\.
+ You can import certificates only in the \.pem and \.sso \(Oracle wallet\) formats\.
+ In some cases, your server SSL certificate might be signed by an intermediate certificate authority \(CA\)\. If so, make sure that the entire certificate chain leading from the intermediate CA up to the root CA is imported as a single \.pem file\.
+ If you are using self\-signed certificates on your server, choose **require** as your SSL mode\. The **require** SSL mode implicitly trusts the server's SSL certificate and doesn't try to validate that the certificate was signed by a CA\. 

### Managing certificates<a name="CHAP_Security.SSL.ManagingCerts"></a>

You can use the DMS console to view and manage your SSL certificates\. You can also import your certificates using the DMS console\.

![\[AWS Database Migration Service SSL certificate management\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-certificatemgr.png)

### Enabling SSL for a MySQL\-compatible, PostgreSQL, or SQL Server endpoint<a name="CHAP_Security.SSL.Procedure"></a>

You can add an SSL connection to a newly created endpoint or to an existing endpoint\.

**To create an AWS DMS endpoint with SSL**

1. Sign in to the AWS Management Console and open the AWS DMS console at [https://console\.aws\.amazon\.com/dms/v2/](https://console.aws.amazon.com/dms/v2/)\. 

   If you're signed in as an AWS Identity and Access Management \(IAM\) user, make sure that you have the appropriate permissions to access AWS DMS\. For more information about the permissions required for database migration, see [IAM permissions needed to use AWS DMS](#CHAP_Security.IAMPermissions)\.

1. In the navigation pane, choose **Certificates**\.

1. Choose **Import Certificate**\.

1. Upload the certificate you want to use for encrypting the connection to an endpoint\.
**Note**  
You can also upload a certificate using the AWS DMS console when you create or modify an endpoint by selecting **Add new CA certificate** on the **Create database endpoint** page\.  
For Aurora Serverless as target, get the certificate mentioned in [Using TLS/SSL with Aurora Serverless](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/aurora-serverless.html#aurora-serverless.tls)\.

1. Create an endpoint as described in [Step 2: Specify source and target endpoints](CHAP_GettingStarted.Replication.md#CHAP_GettingStarted.Replication.Endpoints)

**To modify an existing AWS DMS endpoint to use SSL**

1. Sign in to the AWS Management Console and open the AWS DMS console at [https://console\.aws\.amazon\.com/dms/v2/](https://console.aws.amazon.com/dms/v2/)\. 

   If you're signed in as an IAM user, make sure that you have the appropriate permissions to access AWS DMS\. For more information about the permissions required for database migration, see [IAM permissions needed to use AWS DMS](#CHAP_Security.IAMPermissions)\.

1. In the navigation pane, choose **Certificates**\.

1. Choose **Import Certificate**\.

1. Upload the certificate you want to use for encrypting the connection to an endpoint\.
**Note**  
You can also upload a certificate using the AWS DMS console when you create or modify an endpoint by selecting **Add new CA certificate** on the **Create database endpoint** page\.

1. In the navigation pane, choose **Endpoints**, select the endpoint you want to modify, and choose **Modify**\.

1. Choose a value for **SSL mode**\.

   If you choose **verify\-ca** or **verify\-full** mode, specify the certificate that you want to use for **CA certificate**, as shown following\.   
![\[AWS Database Migration Service SSL certificate management\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-certificate2.png)

   

1. Choose **Modify**\.

1. When the endpoint has been modified, choose the endpoint and choose **Test connection** to determine if the SSL connection is working\.

After you create your source and target endpoints, create a task that uses these endpoints\. For more information about creating a task, see [Step 3: Create a task and migrate data](CHAP_GettingStarted.Replication.md#CHAP_GettingStarted.Replication.Tasks)\. 

## Changing the database password<a name="CHAP_Security.ChangingDBPassword"></a>

In most situations, changing the database password for your source or target endpoint is straightforward\. If you need to change the database password for an endpoint that you are currently using in a migration or replication task, the process is slightly more complex\. The procedure following shows how to do this\.

**To change the database password for an endpoint in a migration or replication task**

1. Sign in to the AWS Management Console and open the AWS DMS console at [https://console\.aws\.amazon\.com/dms/v2/](https://console.aws.amazon.com/dms/v2/)\. 

   If you're signed in as an IAM user, make sure that you have the appropriate permissions to access AWS DMS\. For more information about the permissions required, see [IAM permissions needed to use AWS DMS](#CHAP_Security.IAMPermissions)\.

1. In the navigation pane, choose **Tasks**\.

1. Choose the task that uses the endpoint you want to change the database password for, and then choose **Stop**\.

1. While the task is stopped, you can change the password of the database for the endpoint using the native tools you use to work with the database\.

1. Return to the DMS Management Console and choose **Endpoints** from the navigation pane\.

1. Choose the endpoint for the database you changed the password for, and then choose **Modify**\.

1. Type the new password in the **Password** box, and then choose **Modify**\.

1. Choose **Tasks** from the navigation pane\.

1. Choose the task that you stopped previously, and choose **Start/Resume**\.

1. Choose either **Start** or **Resume**, depending on how you want to continue the task, and then choose **Start task**\.