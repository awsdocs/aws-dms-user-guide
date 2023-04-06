# Data protection in AWS Database Migration Service<a name="CHAP_Security.DataProtection"></a>

## Data encryption<a name="CHAP_Security.DataProtection.DataEncryption"></a>

You can enable encryption for data resources of supported AWS DMS target endpoints\. AWS DMS also encrypts connections to AWS DMS and between AWS DMS and all its source and target endpoints\. In addition, you can manage the keys that AWS DMS and its supported target endpoints use to enable this encryption\.

**Topics**
+ [Encryption at rest](#CHAP_Security.DataProtection.DataEncryption.EncryptionAtRest)
+ [Encryption in transit](#CHAP_Security.DataProtection.DataEncryption.EncryptionInTransit)
+ [Key management](#CHAP_Security.DataProtection.DataEncryption.KeyManagement)

### Encryption at rest<a name="CHAP_Security.DataProtection.DataEncryption.EncryptionAtRest"></a>

AWS DMS supports encryption at rest by allowing you to specify the server\-side encryption mode that you want used to push your replicated data to Amazon S3 before it is copied to supported AWS DMS target endpoints\. You can specify this encryption mode by setting the `encryptionMode` extra connection attribute for the endpoint\. If this `encryptionMode` setting specifies KMS key encryption mode, you can also create custom AWS KMS keys specifically to encrypt the target data for the following AWS DMS target endpoints:
+ Amazon Redshift – For more information about setting `encryptionMode`, see [Endpoint settings when using Amazon Redshift as a target for AWS DMS](CHAP_Target.Redshift.md#CHAP_Target.Redshift.ConnectionAttrib)\. For more information about creating a custom AWS KMS encryption key, see [Creating and using AWS KMS keys to encrypt Amazon Redshift target data](CHAP_Target.Redshift.md#CHAP_Target.Redshift.KMSKeys)\.
+ Amazon S3 – For more information about setting `encryptionMode`, see [Endpoint settings when using Amazon S3 as a target for AWS DMS](CHAP_Target.S3.md#CHAP_Target.S3.Configuring)\. For more information about creating a custom AWS KMS encryption key, see [Creating AWS KMS keys to encrypt Amazon S3 target objects](CHAP_Target.S3.md#CHAP_Target.S3.KMSKeys)\.

### Encryption in transit<a name="CHAP_Security.DataProtection.DataEncryption.EncryptionInTransit"></a>

AWS DMS supports encryption in transit by ensuring that the data it replicates moves securely from the source endpoint to the target endpoint\. This includes encrypting an S3 bucket on the replication instance that your replication task uses for intermediate storage as the data moves through the replication pipeline\. To encrypt task connections to source and target endpoints AWS DMS uses Secure Socket Layer \(SSL\) with Transport Layer Security \(TLS\)\. By encrypting connections to both endpoints, AWS DMS ensures that your data is secure as it moves both from the source endpoint to your replication task and from your task to the target endpoint\. For more information about using SSL/TLS with AWS DMS, see [Using SSL with AWS Database Migration Service](CHAP_Security.SSL.md)

AWS DMS supports both default and custom keys to encrypt both intermediate replication storage and connection information\. You manage these keys by using AWS KMS\. For more information, see [Setting an encryption key and specifying AWS KMS permissions](CHAP_Security.md#CHAP_Security.EncryptionKey)\.

### Key management<a name="CHAP_Security.DataProtection.DataEncryption.KeyManagement"></a>

AWS DMS supports default or custom keys to encrypt replication storage, connection information, and the target data storage for certain target endpoints\. You manage these keys by using AWS KMS\. For more information, see [Setting an encryption key and specifying AWS KMS permissions](CHAP_Security.md#CHAP_Security.EncryptionKey)\.

## Internetwork traffic privacy<a name="CHAP_Security.DataProtection.InternetworkTraffic"></a>

Connections are provided with protection between AWS DMS and source and target endpoints in the same AWS Region, whether running on premises or as part of an AWS service in the cloud\. \(At least one endpoint, source or target, must run as part of an AWS service in the cloud\.\) This protection applies whether these components share the same virtual private cloud \(VPC\) or exist in separate VPCs, if the VPCs are all in the same AWS Region\. For more information about the supported network configurations for AWS DMS, see [Setting up a network for a replication instance](CHAP_ReplicationInstance.VPC.md)\. For more information about the security considerations when using these network configurations, see [Network security for AWS Database Migration Service](CHAP_Security.md#CHAP_Security.Network)\.

## Data protection in DMS Fleet Advisor<a name="fa-security-data-protection"></a>

DMS Fleet Advisor collects and analyzes your database metadata to determine the right size of the migration target\. DMS Fleet Advisor doesn't access data in your tables and doesn't transfer it\. Also, DMS Fleet Advisor doesn't track database feature usage and doesn't access your usage statistics\.

You control access to your databases when you create database users which DMS Fleet Advisor uses to work with your databases\. You grant the required privileges to these users\. To use DMS Fleet Advisor, you grant your database users with read permissions\. DMS Fleet Advisor doesn't modify your databases and doesn't require write permissions\. For more information, see [Creating database users for AWS DMS Fleet Advisor](fa-database-users.md)\.

You can use data encryption in your databases\. AWS DMS also encrypts connections within DMS Fleet Advisor and within its data collectors\.

DMS data collector uses the Data Protection application programming interface \(DPAPI\) to encrypt, protect, and store information about customer's environment and database credentials\. DMS Fleet Advisor stores this encrypted data in a file on the server where your DMS data collector works\. DMS Fleet Advisor doesn't transfer this data from this server\. For more information about DPAPI, see [How to: Use Data Protection](https://learn.microsoft.com/en-us/dotnet/standard/security/how-to-use-data-protection)\.

After you install the DMS data collector, you can view all queries that this application runs to collect metrics\. You can run the DMS data collector in an offline mode and then review the collected data on your server\. Also, you can review this collected data in your Amazon S3 bucket\. For more information, see [How does DMS data collector work?](fa-collecting.md#fa-data-collectors-how-it-works)\.