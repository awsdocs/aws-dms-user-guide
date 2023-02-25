# Cross\-service confused deputy prevention<a name="cross-service-confused-deputy-prevention"></a>

The confused deputy problem is a security issue where an entity that doesn't have permission to perform an action can coerce a more\-privileged entity to perform the action\. In AWS, cross\-service impersonation can result in the confused deputy problem\. Cross\-service impersonation can occur when one service \(the *calling service*\) calls another service \(the *called service*\)\. The calling service can be manipulated to use its permissions to act on another customer's resources in a way it should not otherwise have permission to access\. To prevent this, AWS provides tools that help you protect your data for all services with service principals that have been given access to resources in your account\.

We recommend using the [https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-sourcearn](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-sourcearn) and [https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-sourceaccount](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-sourceaccount) global condition context keys in resource policies to limit the permissions that AWS Database Migration Service gives another service to the resource\. If the `aws:SourceArn` value doesn't contain the account ID, such as an AWS DMS replication instance name \(ARN\), you must use both global condition context keys to limit permissions\. If you use both global condition context keys and the `aws:SourceArn` value contains the account ID, the `aws:SourceAccount` value and the account in the `aws:SourceArn` value must use the same account ID when used in the same policy statement\. Use `aws:SourceArn` if you want only one resource to be associated with the cross\-service access\. Use `aws:SourceAccount` if you want to allow any resource in that account to be associated with the cross\-service use\.

AWS DMS supports confused deputy options starting from the 3\.4\.7 version and higher\. For more information, see [AWS Database Migration Service 3\.4\.7 release notes](CHAP_ReleaseNotes.md#CHAP_ReleaseNotes.DMS347)\. If your replication instance uses AWS DMS version 3\.4\.6 or lower, make sure that you upgrade to latest version before you set the confused deputy options\.

The most effective way to protect against the confused deputy problem is to use the `aws:SourceArn` global condition context key with the full ARN of the resource\. If you don't know the full ARN of the resource or if you are specifying multiple resources, use the `aws:SourceArn` global context condition key with wildcard characters \(`*`\) for the unknown portions of the ARN\. For example, `arn:aws:dms:*:123456789012:rep:*`\. 

**Topics**
+ [IAM roles to use with AWS DMS API for cross\-service confused deputy prevention](#cross-service-confused-deputy-prevention-dms-api)
+ [IAM policy to store preflight assessments in Amazon S3 for cross\-service confused deputy prevention](#cross-service-confused-deputy-prevention-s3)
+ [Using Amazon DynamoDB as a target endpoint with AWS DMS for cross\-service confused deputy prevention](#cross-service-confused-deputy-prevention-dynamodb)

## IAM roles to use with AWS DMS API for cross\-service confused deputy prevention<a name="cross-service-confused-deputy-prevention-dms-api"></a>

To use the AWS CLI or the AWS DMS API for your database migration, you must add the `dms-vpc-role` and `dms-cloudwatch-logs-role` IAM roles to your AWS account before you can use the features of AWS DMS\. For more information, see [Creating the IAM roles to use with the AWS CLI and AWS DMS API](security-iam.md#CHAP_Security.APIRole)\.

The following example shows policies for using the `dms-vpc-role` role with the `my-replication-instance` replication instance\. Use these policies to prevent the confused deputy problem\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "",
            "Effect": "Allow",
            "Principal": {
                "Service": "dms.amazonaws.com"
            },
            "Action": "sts:AssumeRole",
            "Condition": {
                "StringEquals": {
                    "AWS:SourceAccount": "your_account_id"
                },
                "ArnEqual": {
                    "AWS:SourceArn": "arn:aws:dms:your_region:your_account_id:rep:my-replication-instance"
                }
            }
        }
    ]
}
```

## IAM policy to store preflight assessments in Amazon S3 for cross\-service confused deputy prevention<a name="cross-service-confused-deputy-prevention-s3"></a>

To store preassessment results in your S3 bucket, you create an IAM policy that allows AWS DMS to manage objects in Amazon S3\. For more information, see [Storing premigration assessment runs in an S3 Bucket](CHAP_Tasks.AssessmentReport.md#CHAP_Tasks.AssessmentReport.IAM)\.

The following example shows a trust policy with confused deputy conditions that are set on an IAM role that allows AWS DMS to access all tasks and assessment runs under a specified user account\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "",
            "Effect": "Allow",
            "Principal": {
                "Service": "dms.amazonaws.com"
            },
            "Action": "sts:AssumeRole",
            "Condition": {
                "StringEquals": {
                    "AWS:SourceAccount": "your_account_id"
                },
                "ArnLike": {
                    "AWS:SourceArn": [
                        "arn:aws:dms:your_region:your_account_id:assessment-run:*",
                        "arn:aws:dms:region:your_account_id:task:*"
                    ]
                }
            }
        }
    ]
}
```

## Using Amazon DynamoDB as a target endpoint with AWS DMS for cross\-service confused deputy prevention<a name="cross-service-confused-deputy-prevention-dynamodb"></a>

To use Amazon DynamoDB as a target endpoint for your database migration, you must create the IAM role that allows AWS DMS to assume and grant access to the DynamoDB tables\. Then, use this role when you create your target DynamoDB endpoint in AWS DMS\. For more information, see [](CHAP_Target.DynamoDB.md)\.

The following example shows a trust policy with confused deputy conditions that are set on an IAM role that allows all AWS DMS endpoints to access DynamoDB tables\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "",
            "Effect": "Allow",
            "Principal": {
                "Service": "dms.amazonaws.com"
            },
            "Action": "sts:AssumeRole",
            "Condition": {
                "StringEquals": {
                    "AWS:SourceAccount": "your_account_id"
                },
                "ArnLike": {
                    "AWS:SourceArn": "arn:aws:dms:your_region:your_account_id:endpoint:*"
                }
            }
        }
    ]
}
```