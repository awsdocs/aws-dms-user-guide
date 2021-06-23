# Creating source and target endpoints<a name="CHAP_Endpoints.Creating"></a>

You can create source and target endpoints when you create your replication instance or you can create endpoints after your replication instance is created\. The source and target data stores can be on an Amazon Elastic Compute Cloud \(Amazon EC2\) instance, an Amazon Relational Database Service \(Amazon RDS\) DB instance, or an on\-premises database\. \(Note that one of your endpoints must be on an AWS service\. You can't use AWS DMS to migrate from an on\-premises database to another on\-premises database\.\)

The following procedure assumes that you have chosen the AWS DMS console wizard\. Note that you can also do this step by selecting **Endpoints** from the AWS DMS console's navigation pane and then selecting **Create endpoint**\. When using the console wizard, you create both the source and target endpoints on the same page\. When not using the console wizard, you create each endpoint separately\.

**To specify source or target database endpoints using the AWS console**

1. On the **Connect source and target database endpoints** page, specify your connection information for the source or target database\. The following table describes the settings\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Endpoints.Creating.html)

   The following table lists the unsupported characters in endpoint passwords for the listed database engines\. If you want to use commas \(,\) in your endpoint passwords, use the Secrets Manager support provided in AWS DMS to authenticate access to your AWS DMS instances\. For more information, see [Using secrets to access AWS Database Migration Service endpoints](CHAP_Security.md#security_iam_secretsmanager)\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Endpoints.Creating.html)

1. Choose the **Advanced** tab to set values for **Extra connection attributes** and **KMS master key** if you need them\. You can test the endpoint connection by choosing **Run test**\. The following table describes the settings\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Endpoints.Creating.html)