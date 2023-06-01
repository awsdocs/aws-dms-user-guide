# AWS DMS Serverless limitations<a name="CHAP_Serverless.Limitations"></a>

AWS DMS Serverless has the following limitations:
+ You can only modify an AWS DMS replication configuration that is in the `CREATED`, `STOPPED`, or `FAILED` states\. For details about which settings you can change under which conditions, see [Modifying AWS DMS serverless replications](CHAP_Serverless.Components.md#CHAP_Serverless.modify)\.
+ You can only delete an AWS DMS replication configuration that is in the `STOPPED`, or `FAILED` states\. 
+ A static 100GB allocated storage is available for a replication\. If your replication uses more memory than this, due to requirements such as long\-running transactions or caching, we recommend that you partition your workload into separate serverless replications\. You can partition your workload by table, or by requirement, such as by putting all replication involving LOBs into a separate serverless replication\.
+ This release of AWS DMS Serverless does not support connecting to source endpoints with SSL\.
+ This release of AWS DMS Serverless does not support AWS Secrets Manager
+ Unlike replication instances, AWS DMS Serverless replications do not have a public IP address for management tasks\. You manage serverless replications using the console\.
+ This release of AWS DMS Serverless does not support all the source and target endpoint types that AWS DMS standard supports\. For a list of supported engine types, see [AWS DMS Serverless components](CHAP_Serverless.Components.md)\.
+ Serverless replications need to access dependencies by using VPC endpoints\. You must use VPC endpoints to access the following endpoint types:
  + Amazon Amazon S3
  + Amazon Kinesis
  + AWS Secrets Manager
  + Amazon DynamoDB
  + Amazon Redshift
  + Amazon OpenSearch Service
  + 

  For information about setting up VPC endpoints, see [Configuring VPC endpoints as AWS DMS source and target endpoints](CHAP_VPC_Endpoints.md)\.