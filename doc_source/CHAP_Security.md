# Security for AWS Database Migration Service<a name="CHAP_Security"></a>

AWS Database Migration Service \(AWS DMS\) uses several processes to secure your data during migration\. The service encrypts the storage used by your replication instance and the endpoint connection information using an AWS Key Management Service \(AWS KMS\) key that is unique to your AWS account\. Secure Sockets Layer \(SSL\) is supported\. AWS Database Migration Service also requires that you have the appropriate permissions if you sign in as an AWS Identity and Access Management \(IAM\) user\. 

The VPC based on the Amazon Virtual Private Cloud \(Amazon VPC\) service that you use with your replication instance must be associated with a security group that has rules that allow all traffic on all ports to leave \(egress\) the VPC\. This approach allows communication from the replication instance to your source and target database endpoints, as long as correct ingress is enabled on those endpoints\.

If you want to view database migration logs, you need the appropriate Amazon CloudWatch Logs permissions for the IAM role you are using\.

**Topics**
+ [IAM Permissions Needed to Use AWS DMS](CHAP_Security.IAMPermissions.md)
+ [Creating the IAM Roles to Use With the AWS CLI and AWS DMS API](CHAP_Security.APIRole.md)
+ [Fine\-Grained Access Control Using Resource Names and Tags](CHAP_Security.FineGrainedAccess.md)
+ [Setting an Encryption Key and Specifying KMS Permissions](CHAP_Security.EncryptionKey.md)
+ [Network Security for AWS Database Migration Service](CHAP_Security.Network.md)
+ [Using SSL With AWS Database Migration Service](CHAP_Security.SSL.md)
+ [Changing the Database Password](CHAP_Security.ChangingDBPassword.md)