# Working with AWS DMS Serverless<a name="CHAP_Serverless"></a>

AWS DMS Serverless is a feature that provides automatic provisioning, scaling, built\-in high availability, and a pay\-for\-use billing model, to increase operations agility and optimize your costs\. The Serverless feature eliminates replication instance management tasks like capacity estimation, provisioning, cost optimization, and managing replication engine versions and patching\.

With AWS DMS Serverless, similar to the current functionality of AWS DMS \(referred to in this document as AWS DMS Standard\), you create source and target connections using endpoints\. After you create your source and target endpoints, you create a replication configuration, which includes configuration settings for the given replication\. You can manage the replications by starting, stopping, modifying, or deleting them\. Each replication has settings that you can configure according to the requirements of your database migration\. You specify these settings using either a JSON file or the AWS DMS section of the AWS Management Console\. For more information about replication settings, see [ Working with AWS DMS endpoints](https://docs.aws.amazon.com/dms/latest/userguide/CHAP_Endpoints.html)\. After starting the replication, AWS DMS serverless connects to the source database and collects the database metadata to analyze the replication workload\. Using this metadata, AWS DMS computes and provisions the required capacity and starts the data replication\.

The following diagram shows the AWS DMS Serverless replication process\.

![\[AWS DMS Serverless replication states\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-serverless-replication-process.png)



View the following topics to discover more details about AWS DMS Serverless\.

**Topics**
+ [AWS DMS Serverless components](CHAP_Serverless.Components.md)
+ [AWS DMS Serverless limitations](CHAP_Serverless.Limitations.md)