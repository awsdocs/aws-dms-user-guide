# Resource\-based policy examples for AWS KMS<a name="security_iam_resource-based-policy-examples"></a>

AWS DMS allows you to create custom AWS KMS encryption keys to encrypt supported target endpoint data\. To learn how to create and attach a key policy to the encryption key you create for supported target data encryption, see [Creating and using AWS KMS keys to encrypt Amazon Redshift target data](CHAP_Target.Redshift.md#CHAP_Target.Redshift.KMSKeys) and [Creating AWS KMS keys to encrypt Amazon S3 target objects](CHAP_Target.S3.md#CHAP_Target.S3.KMSKeys)\.

**Topics**
+ [A policy for a custom AWS KMS encryption key to encrypt Amazon Redshift target data](#security_iam_resource-based-policy-examples-custom-rs-key-policy)
+ [A policy for a custom AWS KMS encryption key to encrypt Amazon S3 target data](#security_iam_resource-based-policy-examples-custom-s3-key-policy)

## A policy for a custom AWS KMS encryption key to encrypt Amazon Redshift target data<a name="security_iam_resource-based-policy-examples-custom-rs-key-policy"></a>

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

## A policy for a custom AWS KMS encryption key to encrypt Amazon S3 target data<a name="security_iam_resource-based-policy-examples-custom-s3-key-policy"></a>

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