# Service\-linked role for AWS DMS Serverless<a name="slr-services-sl"></a>

AWS DMS Serverless uses the service\-linked role named **AWSServiceRoleForDMSServerless**\. AWS DMS uses this service\-linked role to create and manage AWS DMS resources on your behalf, such as Amazon CloudWatch metrics\. AWS DMS uses this role so that you only have to be concerned with replications\. This service\-linked role is attached to the following managed policy: `AWSDMSServerlessServiceRolePolicy`\. For updates to this policy, see [AWS managed policies for AWS Database Migration Service](security-iam-awsmanpol.md)\.

The AWSServiceRoleForDMSServerless service\-linked role trusts the following services to assume the role:
+ `dms.amazonaws.com`

The following code example shows the AWSDMSServerlessServiceRolePolicy policy that you use to create the AWSServiceRoleForDMSServerless role\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "id0",
            "Effect": "Allow",
            "Action": [
                "dms:CreateReplicationInstance",
                "dms:CreateReplicationTask"
            ],
            "Resource": "*",
            "Condition": {
                "StringEquals": {
                    "dms:req-tag/ResourceCreatedBy": "DMSServerless"
                }
            }
        },
        {
            "Sid": "id1",
            "Effect": "Allow",
            "Action": [
                "dms:DescribeReplicationInstances",
                "dms:DescribeReplicationTasks"
            ],
            "Resource": "*"
        },
        {
            "Sid": "id2",
            "Effect": "Allow",
            "Action": [
                "dms:StartReplicationTask",
                "dms:StopReplicationTask",
                "dms:DeleteReplicationTask",
                "dms:DeleteReplicationInstance"
            ],
            "Resource": [
                "arn:aws:dms:*:*:rep:*",
                "arn:aws:dms:*:*:task:*"
            ],
            "Condition": {
                "StringEqualsIgnoreCase": {
                    "aws:ResourceTag/ResourceCreatedBy": "DMSServerless"
                }
            }
        },
        {
            "Sid": "id3",
            "Effect": "Allow",
            "Action": [
                "dms:TestConnection",
                "dms:DeleteConnection"
            ],
            "Resource": [
                "arn:aws:dms:*:*:rep:*",
                "arn:aws:dms:*:*:endpoint:*"
            ]
        }
    ]
}
```

You must configure permissions to allow an IAM entity, such as a user, group, or role, to create, edit, or delete a service\-linked role\. For more information, see [Service\-linked role permissions](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html#service-linked-role-permissions) in the *IAM User Guide*\.

## Creating a service\-linked role for AWS DMS Serverless<a name="create-slr-sl"></a>

When you create a replication, AWS DMS serverless programmatically creates a AWS DMS serverless service linked role\. You can view this role in the IAM console\. You can also choose to create this role manually\. To create the role manually, use the IAM console to create a service\-linked role with the **DMS – Serverless** use case\. In the AWS CLI or the AWS API, create a service\-linked role using `dms.amazonaws.com` for the service name\. For more information, see [ Creating a service\-linked role](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html#create-service-linked-role) in the *IAM User Guide*\. If you delete this service\-linked role, you can use this same process to create the role again\.

**Note**  
If you delete a role while you have replications in your account, the replication results in a failure\.

You can use the IAM console to create a service\-linked role with the **DMS – Serverless** use case\. In the AWS CLI or the AWS API, create a service\-linked role with the `dms.amazonaws.com` service name\. For more information, see [Creating a service\-linked role](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html#create-service-linked-role) in the *IAM User Guide*\. If you delete this service\-linked role, you can use this same process to create the role again\.

Make sure that this role exists before you create a serverless replication\. For more information, see [Working with AWS DMS Serverless](CHAP_Serverless.md)\.

## Editing a service\-linked role for AWS DMS Serverless<a name="edit-slr-sl"></a>

AWS DMS doesn't allow you to edit the AWSServiceRoleForDMSServerless service\-linked role\. After you create a service\-linked role, you can't change the name of the role because various entities might reference the role\. However, you can edit the description of the role using IAM\. For more information, see [Editing a service\-linked role](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html#edit-service-linked-role) in the *IAM User Guide*\.

## Deleting a service\-linked role for AWS DMS Serverless<a name="delete-slr-sl"></a>

If you no longer need to use a feature or service that requires a service\-linked role, we recommend that you delete that role\. Thus, you don’t have an unused entity that isn't actively monitored or maintained\. However, you must clean up the resources for your service\-linked role before you can manually delete it\.

**Note**  
If the AWS DMS service is using the role when you try to delete the resources, then the deletion might fail\. If that happens, wait for a few minutes and try the operation again\.

**To delete AWS DMS resources used by the AWSServiceRoleForDMSServerless**

1. Sign in to the AWS Management Console and open the AWS DMS console at [https://console\.aws\.amazon\.com/dms/v2/](https://console.aws.amazon.com/https://console.aws.amazon.com/dms/v2/)\.

1. In the navigation pane, choose **Serverless** under **Discover**\. The **Serverless** page opens\.

1. Choose your serverless replication and choose **Delete**\.

1. To confirm deletion, enter the serverless replication name in the text input field\. Next, choose **Delete**\.

After you delete all serverless replications, you can delete the service\-linked role\.

**To manually delete the service\-linked role using IAM**

Use the IAM console, the AWS CLI, or the AWS API to delete the AWSServiceRoleForDMSServerless service\-linked role\. For more information, see [Deleting a service\-linked role](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html#delete-service-linked-role) in the *IAM User Guide*\.

## Supported regions for AWS DMS Serverless service\-linked roles<a name="slr-regions-sl"></a>

AWS DMS Serverless supports using service\-linked roles in all of the regions where the service is available\. 