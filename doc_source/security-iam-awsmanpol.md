# AWS managed policies for AWS Database Migration Service<a name="security-iam-awsmanpol"></a>

**Topics**
+ [AWS managed policy: AWSDMSServerlessServiceRolePolicy](#security-iam-awsmanpol-AWSDMSServerlessServiceRolePolicy)
+ [AWS managed policy: AmazonDMSCloudWatchLogsRole](#security-iam-awsmanpol-AmazonDMSCloudWatchLogsRole)
+ [AWS managed policy: AWSDMSFleetAdvisorServiceRolePolicy](#security-iam-awsmanpol-AWSDMSFleetAdvisorServiceRolePolicy)
+ [AWS DMS updates to AWS managed policies](#security-iam-awsmanpol-updates)

## AWS managed policy: AWSDMSServerlessServiceRolePolicy<a name="security-iam-awsmanpol-AWSDMSServerlessServiceRolePolicy"></a>

This policy is attached to the `AWSServiceRoleForDMSServerless` role, which allows AWS DMS to perform actions on your behalf\. For more information, see [Service\-linked role for AWS DMS Serverless](slr-services-sl.md)\.

This policy grants contributor permissions that allow AWS DMS to manage replication resources\.

**Permissions details**

This policy includes the following permissions\.
+ `dms` – Allows principals to interact with AWS DMS resources\.

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

## AWS managed policy: AmazonDMSCloudWatchLogsRole<a name="security-iam-awsmanpol-AmazonDMSCloudWatchLogsRole"></a>

This policy is attached to the `dms-cloudwatch-logs-role` role, which allows AWS DMS to perform actions on your behalf\. For more information, see [Using service\-linked roles for AWS DMS](using-service-linked-roles.md)\.

This policy grants contributor permissions that allow AWS DMS to publish replication logs to CloudWatch logs\.

**Permissions details**

This policy includes the following permissions\.




+ `logs` – Allows principals to publish logs to CloudWatch Logs\. This permission is required so that AWS DMS can use CloudWatch to display replication logs\.



```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowDescribeOnAllLogGroups",
            "Effect": "Allow",
            "Action": [
                "logs:DescribeLogGroups"
            ],
            "Resource": [
                "*"
            ]
        },
        {
            "Sid": "AllowDescribeOfAllLogStreamsOnDmsTasksLogGroup",
            "Effect": "Allow",
            "Action": [
                "logs:DescribeLogStreams"
            ],
            "Resource": [
                "arn:aws:logs:*:*:log-group:dms-tasks-*",
                "arn:aws:logs:*:*:log-group:dms-serverless-replication-*"
            ]
        },
        {
            "Sid": "AllowCreationOfDmsLogGroups",
            "Effect": "Allow",
            "Action": [
                "logs:CreateLogGroup"
            ],
            "Resource": [
                "arn:aws:logs:*:*:log-group:dms-tasks-*",
                "arn:aws:logs:*:*:log-group:dms-serverless-replication-*:log-stream:"
            ]
        },
        {
            "Sid": "AllowCreationOfDmsLogStream",
            "Effect": "Allow",
            "Action": [
                "logs:CreateLogStream"
            ],
            "Resource": [
                "arn:aws:logs:*:*:log-group:dms-tasks-*:log-stream:dms-task-*",
                "arn:aws:logs:*:*:log-group:dms-serverless-replication-*:log-stream:dms-serverless-*"
            ]
        },
        {
            "Sid": "AllowUploadOfLogEventsToDmsLogStream",
            "Effect": "Allow",
            "Action": [
                "logs:PutLogEvents"
            ],
            "Resource": [
                "arn:aws:logs:*:*:log-group:dms-tasks-*:log-stream:dms-task-*",
                "arn:aws:logs:*:*:log-group:dms-serverless-replication-*:log-stream:dms-serverless-*"
            ]
        }
    ]
}
```

## AWS managed policy: AWSDMSFleetAdvisorServiceRolePolicy<a name="security-iam-awsmanpol-AWSDMSFleetAdvisorServiceRolePolicy"></a>





You can't attach AWSDMSFleetAdvisorServiceRolePolicy to your IAM entities\. This policy is attached to a service\-linked role that allows AWS DMS Fleet Advisor to perform actions on your behalf\. For more information, see [Using service\-linked roles for AWS DMS](using-service-linked-roles.md)\.



This policy grants contributor permissions that allow AWS DMS Fleet Advisor to publish Amazon CloudWatch metrics\.



**Permissions details**

This policy includes the following permissions\.




+ `cloudwatch` – Allows principals to publish metric data points to Amazon CloudWatch\. This permission is required so that AWS DMS Fleet Advisor can use CloudWatch to display charts with database metrics\.



```
{
    "Version": "2012-10-17",
    "Statement": {
        "Effect": "Allow",
        "Resource": "*",
        "Action": "cloudwatch:PutMetricData",
        "Condition": {
            "StringEquals": {
                "cloudwatch:namespace": "AWS/DMS/FleetAdvisor"
            }
        }
    }
}
```





## AWS DMS updates to AWS managed policies<a name="security-iam-awsmanpol-updates"></a>



View details about updates to AWS managed policies for AWS DMS since this service began tracking these changes\. For automatic alerts about changes to this page, subscribe to the RSS feed on the AWS DMS Document history page\.




| Change | Description | Date | 
| --- | --- | --- | 
|  [AWSDMSServerlessServiceRolePolicy](#security-iam-awsmanpol-AWSDMSServerlessServiceRolePolicy) – New policy  |  AWS DMS added the `AWSDMSServerlessServiceRolePolicy` role to allow AWS DMS to create and manage services on your behalf, such as publishing Amazon CloudWatch metrics\.  | May 22, 2023 | 
|  [AmazonDMSCloudWatchLogsRole](#security-iam-awsmanpol-AmazonDMSCloudWatchLogsRole) – Change  |  AWS DMS added the ARN for serverless resources to each of the permissions granted, to allow uploading AWS DMS replication logs from serverless replication configurations to CloudWatch Logs\.  | May 22, 2023 | 
|  [AWSDMSFleetAdvisorServiceRolePolicy](#security-iam-awsmanpol-AWSDMSFleetAdvisorServiceRolePolicy) – New policy  |  AWS DMS Fleet Advisor added a new policy to allow publishing metric data points to Amazon CloudWatch\.  | March 6, 2023 | 
|  AWS DMS started tracking changes  |  AWS DMS started tracking changes for its AWS managed policies\.  | March 6, 2023 | 