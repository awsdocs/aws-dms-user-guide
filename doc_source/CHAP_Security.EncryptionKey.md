# Setting an Encryption Key and Specifying KMS Permissions<a name="CHAP_Security.EncryptionKey"></a>

AWS DMS encrypts the storage used by a replication instance and the endpoint connection information\. To encrypt the storage used by a replication instance, AWS DMS uses an AWS Key Management Service \(KMS\) key that is unique to your AWS account\. You can view and manage this key with KMS\. You can use the default KMS key in your account \(`aws/dms`\) or you can create a custom KMS key\. If you have an existing KMS key, you can also use that key for encryption\. 

The default KMS key \(`aws/dms`\) is created when you first launch a replication instance and you have not selected a custom KMS master key from the **Advanced** section of the **Create Replication Instance** page\. If you use the default KMS key, the only permissions you need to grant to the IAM user account you are using for migration are `kms:ListAliases` and `kms:DescribeKey`\. For more information about using the default KMS key, see [IAM Permissions Needed to Use AWS DMS](CHAP_Security.IAMPermissions.md)\. 

To use a custom KMS key, assign permissions for the custom KMS key using one of the following options\.
+ Add the IAM user account used for the migration as a Key Administrator/Key User for the KMS custom key\. This will ensure that necessary KMS permissions are granted to the IAM user account\. Note that this action is in addition to the IAM permissions that you must grant to the IAM user account to use AWS DMS\. For more information about granting permissions to a key user, see [ Allows Key Users to Use the CMK](http://docs.aws.amazon.com/kms/latest/developerguide/key-policies.html#key-policy-default-allow-users)\.
+ If you do not want to add the IAM user account as a Key Administrator/Key User for your custom KMS key, then add the following additional permissions to the IAM permissions that you must grant to the IAM user account to use AWS DMS\. 

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

AWS DMS does not work with KMS Key Aliases, but you can use the KMS key's Amazon Resource Number \(ARN\) when specifying the KMS key information\. For more information on creating your own KMS keys and giving users access to a KMS key, see the *[KMS Developer Guide](http://docs.aws.amazon.com/kms/latest/developerguide/create-keys.html)*\. 

If you don't specify a KMS key identifier, then AWS DMS uses your default encryption key\. KMS creates the default encryption key for AWS DMS for your AWS account\. Your AWS account has a different default encryption key for each AWS region\. 

To manage the KMS keys used for encrypting your AWS DMS resources, you use KMS\. You can find KMS in the AWS Management Console by choosing **Identity & Access Management** on the console home page and then choosing **Encryption Keys** on the navigation pane\. KMS combines secure, highly available hardware and software to provide a key management system scaled for the cloud\. Using KMS, you can create encryption keys and define the policies that control how these keys can be used\. KMS supports AWS CloudTrail, so you can audit key usage to verify that keys are being used appropriately\. Your KMS keys can be used in combination with AWS DMS and supported AWS services such as Amazon RDS, Amazon Simple Storage Service \(Amazon S3\), Amazon Redshift, and Amazon Elastic Block Store \(Amazon EBS\)\. 

Once you have created your AWS DMS resources with the KMS key, you cannot change the encryption key for those resources\. Make sure to determine your encryption key requirements before you create your AWS DMS resources\. 