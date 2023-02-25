# How AWS Database Migration Service works with IAM<a name="security_iam_service-with-iam"></a>

Before you use IAM to manage access to AWS DMS, you should understand what IAM features are available to use with AWS DMS\. To get a high\-level view of how AWS DMS and other AWS services work with IAM, see [AWS services that work with IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_aws-services-that-work-with-iam.html) in the *IAM User Guide*\.

**Topics**
+ [AWS DMS identity\-based policies](#security_iam_service-with-iam-id-based-policies)
+ [AWS DMS resource\-based policies](#security_iam_service-with-iam-resource-based-policies)
+ [Authorization based on AWS DMS tags](#security_iam_service-with-iam-tags)
+ [IAM roles for AWS DMS](#security_iam_service-with-iam-roles)

## AWS DMS identity\-based policies<a name="security_iam_service-with-iam-id-based-policies"></a>

With IAM identity\-based policies, you can specify allowed or denied actions and resources, and also the conditions under which actions are allowed or denied\. AWS DMS supports specific actions, resources, and condition keys\. To learn about all of the elements that you use in a JSON policy, see [IAM JSON policy elements reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html) in the *IAM User Guide*\.

### Actions<a name="security_iam_service-with-iam-id-based-policies-actions"></a>

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

### Resources<a name="security_iam_service-with-iam-id-based-policies-resources"></a>

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

For more information on controlling access to AWS DMS resources using policies, see [Using resource names to control access](CHAP_Security.FineGrainedAccess.md#CHAP_Security.FineGrainedAccess.ResourceName)\. To see a list of AWS DMS resource types and their ARNs, see [Resources Defined by AWS Database Migration Service](https://docs.aws.amazon.com/IAM/latest/UserGuide/list_awsdatabasemigrationservice.html#awsdatabasemigrationservice-resources-for-iam-policies) in the *IAM User Guide*\. To learn with which actions you can specify the ARN of each resource, see [Actions Defined by AWS Database Migration Service](https://docs.aws.amazon.com/IAM/latest/UserGuide/list_awsdatabasemigrationservice.html#awsdatabasemigrationservice-actions-as-permissions)\.

### Condition keys<a name="security_iam_service-with-iam-id-based-policies-conditionkeys"></a>

Administrators can use AWS JSON policies to specify who has access to what\. That is, which **principal** can perform **actions** on what **resources**, and under what **conditions**\.

The `Condition` element \(or `Condition` *block*\) lets you specify conditions in which a statement is in effect\. The `Condition` element is optional\. You can create conditional expressions that use [condition operators](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_condition_operators.html), such as equals or less than, to match the condition in the policy with values in the request\. 

If you specify multiple `Condition` elements in a statement, or multiple keys in a single `Condition` element, AWS evaluates them using a logical `AND` operation\. If you specify multiple values for a single condition key, AWS evaluates the condition using a logical `OR` operation\. All of the conditions must be met before the statement's permissions are granted\.

 You can also use placeholder variables when you specify conditions\. For example, you can grant an IAM user permission to access a resource only if it is tagged with their IAM user name\. For more information, see [IAM policy elements: variables and tags](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_variables.html) in the *IAM User Guide*\. 

AWS supports global condition keys and service\-specific condition keys\. To see all AWS global condition keys, see [AWS global condition context keys](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html) in the *IAM User Guide*\.

AWS DMS defines its own set of condition keys and also supports using some global condition keys\. To see all AWS global condition keys, see [AWS global condition context keys](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html) in the *IAM User Guide*\.



AWS DMS defines a set of standard tags that you can use in its condition keys and also allows you defined your own custom tags\. For more information, see [Using tags to control access](CHAP_Security.FineGrainedAccess.md#CHAP_Security.FineGrainedAccess.Tags)\.

To see a list of AWS DMS condition keys, see [Condition Keys for AWS Database Migration Service](https://docs.aws.amazon.com/IAM/latest/UserGuide/list_awsdatabasemigrationservice.html#awsdatabasemigrationservice-policy-keys) in the *IAM User Guide*\. To learn with which actions and resources you can use a condition key, see [Actions Defined by AWS Database Migration Service](https://docs.aws.amazon.com/IAM/latest/UserGuide/list_awsdatabasemigrationservice.html#awsdatabasemigrationservice-actions-as-permissions) and [Resources Defined by AWS Database Migration Service](https://docs.aws.amazon.com/IAM/latest/UserGuide/list_awsdatabasemigrationservice.html#awsdatabasemigrationservice-resources-for-iam-policies)\.

### Examples<a name="security_iam_service-with-iam-id-based-policies-examples"></a>



To view examples of AWS DMS identity\-based policies, see [AWS Database Migration Service identity\-based policy examples](security_iam_id-based-policy-examples.md)\.

## AWS DMS resource\-based policies<a name="security_iam_service-with-iam-resource-based-policies"></a>

Resource\-based policies are JSON policy documents that specify what actions a specified principal can perform on a given AWS DMS resource and under what conditions\. AWS DMS supports resource\-based permissions policies for AWS KMS encryption keys that you create to encrypt data migrated to supported target endpoints\. The supported target endpoints include Amazon Redshift and Amazon S3\. By using resource\-based policies, you can grant the permission for using these encryption keys to other accounts for each target endpoint\.

To enable cross\-account access, you can specify an entire account or IAM entities in another account as the [principal in a resource\-based policy](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_principal.html)\. Adding a cross\-account principal to a resource\-based policy is only half of establishing the trust relationship\. When the principal and the resource are in different AWS accounts, you must also grant the principal entity permission to access the resource\. Grant permission by attaching an identity\-based policy to the entity\. However, if a resource\-based policy grants access to a principal in the same account, no additional identity\-based policy is required\. For more information, see [How IAM roles differ from resource\-based policies ](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_compare-resource-policies.html)in the *IAM User Guide*\.

The AWS DMS service supports only one type of resource\-based policy called a *key policy*, which is attached to an AWS KMS encryption key\. This policy defines which principal entities \(accounts, users, roles, and federated users\) can encrypt migrated data on the supported target endpoint\.

To learn how to attach a resource\-based policy to an encryption key that you create for the supported target endpoints, see [Creating and using AWS KMS keys to encrypt Amazon Redshift target data](CHAP_Target.Redshift.md#CHAP_Target.Redshift.KMSKeys) and [Creating AWS KMS keys to encrypt Amazon S3 target objects](CHAP_Target.S3.md#CHAP_Target.S3.KMSKeys)\.

### Examples<a name="security_iam_service-with-iam-resource-based-policies-examples"></a>



For examples of AWS DMS resource\-based policies, see [Resource\-based policy examples for AWS KMS](security_iam_resource-based-policy-examples.md)\.

## Authorization based on AWS DMS tags<a name="security_iam_service-with-iam-tags"></a>

You can attach tags to AWS DMS resources or pass tags in a request to AWS DMS\. To control access based on tags, you provide tag information in the [condition element](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_condition.html) of a policy using the `dms:ResourceTag/key-name`, `aws:RequestTag/key-name`, or `aws:TagKeys` condition key\. AWS DMS defines a set of standard tags that you can use in its condition keys and also enables you to define your own custom tags\. For more information, see [Using tags to control access](CHAP_Security.FineGrainedAccess.md#CHAP_Security.FineGrainedAccess.Tags)\.

For an example identity\-based policy that limits access to a resource based on tags, see [Accessing AWS DMS resources based on tags](security_iam_id-based-policy-examples.md#security_iam_id-based-policy-examples-access-resources-tags)\.

## IAM roles for AWS DMS<a name="security_iam_service-with-iam-roles"></a>

An [IAM role](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html) is an entity within your AWS account that has specific permissions\.

### Using temporary credentials with AWS DMS<a name="security_iam_service-with-iam-roles-tempcreds"></a>

You can use temporary credentials to sign in with federation, assume an IAM role, or assume a cross\-account role\. You get temporary security credentials by calling AWS STS API operations such as [AssumeRole](https://docs.aws.amazon.com/STS/latest/APIReference/API_AssumeRole.html) or [GetFederationToken](https://docs.aws.amazon.com/STS/latest/APIReference/API_GetFederationToken.html)\. 

AWS DMS supports using temporary credentials\. 

### Service\-linked roles<a name="security_iam_service-with-iam-roles-service-linked"></a>

[Service\-linked roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_terms-and-concepts.html#iam-term-service-linked-role) allow AWS services to access resources in other services to complete an action on your behalf\. Service\-linked roles appear in your IAM account and are owned by the service\. An IAM administrator can view but not edit the permissions for service\-linked roles\.

AWS DMS does not support service\-linked roles\. 

### Service roles<a name="security_iam_service-with-iam-roles-service"></a>

This feature allows a service to assume a [service role](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_terms-and-concepts.html#iam-term-service-role) on your behalf\. This role allows the service to access resources in other services to complete an action on your behalf\. Service roles appear in your IAM account and are owned by the account\. This means that an IAM administrator can change the permissions for this role\. However, doing so might break the functionality of the service\.

AWS DMS supports two types of service roles that you must create to use certain source or target endpoints:
+ Roles with permissions to allow AWS DMS access to the following source and target endpoints \(or their resources\):
  + Amazon DynamoDB as a target – For more information see [Prerequisites for using DynamoDB as a target for AWS Database Migration Service](CHAP_Target.DynamoDB.md#CHAP_Target.DynamoDB.Prerequisites)\.
  + OpenSearch as a target – For more information see [Prerequisites for using Amazon OpenSearch Service as a target for AWS Database Migration Service](CHAP_Target.Elasticsearch.md#CHAP_Target.Elasticsearch.Prerequisites)\.
  + Amazon Kinesis as a target – For more information see [Prerequisites for using a Kinesis data stream as a target for AWS Database Migration Service](CHAP_Target.Kinesis.md#CHAP_Target.Kinesis.Prerequisites)\.
  + Amazon Redshift as a target – You need to create the specified role only for creating a custom KMS encryption key to encrypt the target data or for specifying a custom S3 bucket to hold intermediate task storage\. For more information, see [Creating and using AWS KMS keys to encrypt Amazon Redshift target data](CHAP_Target.Redshift.md#CHAP_Target.Redshift.KMSKeys) or [Amazon S3 bucket settings](CHAP_Target.Redshift.md#CHAP_Target.Redshift.EndpointSettings.S3Buckets)\.
  + Amazon S3 as a source or as a target – For more information, see [Prerequisites when using Amazon S3 as a source for AWS DMS](CHAP_Source.S3.md#CHAP_Source.S3.Prerequisites) or [Prerequisites for using Amazon S3 as a target](CHAP_Target.S3.md#CHAP_Target.S3.Prerequisites)\.

  For example, to read data from an S3 source endpoint or to push data to an S3 target endpoint, you must create a service role as a prerequisite to accessing S3 for each of these endpoint operations\. 
+ Roles with permissions required to use the AWS CLI and AWS DMS API – Two IAM roles that you need to create are `dms-vpc-role` and `dms-cloudwatch-logs-role`\. If you use Amazon Redshift as a target database, you must also create and add the IAM role `dms-access-for-endpoint` to your AWS account\. For more information, see [Creating the IAM roles to use with the AWS CLI and AWS DMS API](security-iam.md#CHAP_Security.APIRole)\.

### Choosing an IAM role in AWS DMS<a name="security_iam_service-with-iam-roles-choose"></a>

If you use the AWS CLI or the AWS DMS API for your database migration, you must add certain IAM roles to your AWS account before you can use the features of AWS DMS\. Two of these are `dms-vpc-role` and `dms-cloudwatch-logs-role`\. If you use Amazon Redshift as a target database, you must also add the IAM role `dms-access-for-endpoint` to your AWS account\. For more information, see [Creating the IAM roles to use with the AWS CLI and AWS DMS API](security-iam.md#CHAP_Security.APIRole)\.