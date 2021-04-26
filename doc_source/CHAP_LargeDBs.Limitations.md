# Limitations when working with AWS Snowball Edge and AWS DMS<a name="CHAP_LargeDBs.Limitations"></a>

There are some limitations you should be aware of when working with AWS Snowball Edge:
+ Every AWS SCT task creates two endpoint connections on AWS DMS\. If you create multiple tasks, you can reach a resource limit for the number of endpoints that you can create\.
+ A schema is the minimum task scope when using Snowball Edge\. You can't migrate individual tables or subsets of tables using Snowball Edge\.
+ The AWS DMS Agent doesn't support HTTP/HTTPS or SOCKS proxy configurations\. Connections to the source and target can fail if the AWS DMS agent host uses proxies\.
+ The LOB mode limits LOB file size to 32 K\. LOBs larger than 32 K aren't migrated\.
+ In some cases, an error can occur when loading from the local database to the Edge device or when loading data from Amazon S3 to the target database\. In some of these cases, the error is recoverable and the task can restart\. If AWS DMS can't recover from the error, the migration stops\. If this happens, contact AWS Support\.