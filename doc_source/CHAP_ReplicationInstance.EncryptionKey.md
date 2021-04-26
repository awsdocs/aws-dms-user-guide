# Setting an encryption key for a replication instance<a name="CHAP_ReplicationInstance.EncryptionKey"></a>

AWS DMS encrypts the storage used by a replication instance and the endpoint connection information\. To encrypt the storage used by a replication instance, AWS DMS uses a master key that is unique to your AWS account\. You can view and manage this master key with AWS Key Management Service \(AWS KMS\)\. You can use the default master key in your account \(`aws/dms`\) or a custom master key that you create\. If you have an existing AWS KMS encryption key, you can also use that key for encryption\. 

You can specify your own encryption key by supplying a KMS key identifier to encrypt your AWS DMS resources\. When you specify your own encryption key, the user account used to perform the database migration must have access to that key\. For more information on creating your own encryption keys and giving users access to an encryption key, see the *[AWS KMS Developer Guide](https://docs.aws.amazon.com/kms/latest/developerguide/create-keys.html)*\. 

If you don't specify a KMS key identifier, then AWS DMS uses your default encryption key\. KMS creates the default encryption key for AWS DMS for your AWS account\. Your AWS account has a different default encryption key for each AWS Region\. 

To manage the keys used for encrypting your AWS DMS resources, you use KMS\. You can find KMS in the AWS Management Console by searching for **KMS** on the navigation pane\. 

KMS combines secure, highly available hardware and software to provide a key management system scaled for the cloud\. Using KMS, you can create encryption keys and define the policies that control how these keys can be used\. KMS supports AWS CloudTrail, so you can audit key usage to verify that keys are being used appropriately\. Your KMS keys can be used in combination with AWS DMS and other supported AWS services\. Supported AWS services include Amazon RDS, Amazon S3, Amazon Elastic Block Store \(Amazon EBS\), and Amazon Redshift\. 

When you have created your AWS DMS resources with a specific encryption key, you can't change the encryption key for those resources\. Make sure to determine your encryption key requirements before you create your AWS DMS resources\. 