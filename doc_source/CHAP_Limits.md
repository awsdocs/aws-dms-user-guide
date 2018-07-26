# Limits for AWS Database Migration Service<a name="CHAP_Limits"></a>

Following, you can find the resource limits and naming constraints for AWS Database Migration Service \(AWS DMS\)\.

The maximum size of a database that AWS DMS can migrate depends on your source environment, the distribution of data in your source database, and how busy your source system is\. The best way to determine whether your particular system is a candidate for AWS DMS is to test it out\. Start slowly so you can get the configuration worked out, then add some complex objects, and finally, attempt a full load as a test\. 

## Limits for AWS Database Migration Service<a name="CHAP_Limits.Limits"></a>

Each AWS account has limits, per region, on the number of AWS DMS resources that can be created\. Once a limit for a resource has been reached, additional calls to create that resource will fail with an exception\.

The 6 TB limit for storage applies to the DMS replication instance\. This storage is used to cache changes if the target cannot keep up with the source and for storing log information\. This limit does not apply to the target size; target endpoints can be larger than 6 TB\.

The following table lists the AWS DMS resources and their limits per region\.


| Resource | Default Limit | 
| --- | --- | 
|  Replication instances  | 20 | 
|  Total amount of storage  | 6 TB | 
|  Event subscriptions  | 20 | 
|  Replication subnet groups  | 20 | 
|  Subnets per replication subnet group  | 20 | 
|  Endpoints  | 100 | 
|  Tasks  | 200 | 
|  Endpoints per instance  | 20 | 