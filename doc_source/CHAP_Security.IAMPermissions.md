# IAM Permissions Needed to Use AWS DMS<a name="CHAP_Security.IAMPermissions"></a>

You need to use certain IAM permissions and IAM roles to use AWS DMS\. If you are signed in as an IAM user and want to use AWS DMS, your account administrator must attach the policy discussed in this section to the IAM user, group, or role that you use to run AWS DMS\. For more information about IAM permissions, see the [IAM User Guide](http://docs.aws.amazon.com/IAM/latest/UserGuide/introduction_access-management.html)\. 

The following set of permissions gives you access to AWS DMS, and also permissions for certain actions needed from other Amazon services such as AWS KMS, IAM, Amazon Elastic Compute Cloud \(Amazon EC2\), and Amazon CloudWatch\. CloudWatch monitors your AWS DMS migration in real time and collects and tracks metrics that indicate the progress of your migration\. You can use CloudWatch Logs to debug problems with a task\. 

**Note**  
You can further restrict access to AWS DMS resources using tagging\. For more information about restricting access to AWS DMS resources using tagging, see [Fine\-Grained Access Control Using Resource Names and Tags](CHAP_Security.FineGrainedAccess.md)

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

A breakdown of these permissions might help you better understand why each one is necessary\.

This section is required to allow the user to call AWS DMS API operations\.

```
{
            "Effect": "Allow",
            "Action": "dms:*",
            "Resource": "*"
}
```

This section is required to allow the user to list their available KMS Keys and alias for display in the console\. This entry is not required if the KMSkey ARN is known and when using only the CLI\.

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

This section is required for certain endpoint types that require a Role ARN to be passed in with the endpoint\. In addition, if the required AWS DMS roles are not created ahead of time, the AWS DMS console has the ability to create the role\. If all roles are configured ahead of time, all that is required in iam:GetRole and iam:PassRole\. For more information about roles, see [Creating the IAM Roles to Use With the AWS CLI and AWS DMS API](CHAP_Security.APIRole.md)\.

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

This section is required since AWS DMS needs to create the EC2 instance and configure the network for the replication instance that is created\. These resources exist in the customer's account, so the ability to perform these actions on behalf of the customer is required\.

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

This section is required to allow the user to be able to view replication instance metrics\.

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

This section is required when using Redshift as a target\. It allows AWS DMS to validate that the Redshift cluster is set up properly for AWS DMS\.

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

The AWS DMS console creates several roles that are automatically attached to your AWS account when you use the AWS DMS console\. If you use the AWS Command Line Interface \(AWS CLI\) or the AWS DMS API for your migration, you need to add these roles to your account\. For more information on adding these roles, see [Creating the IAM Roles to Use With the AWS CLI and AWS DMS API](CHAP_Security.APIRole.md)\.