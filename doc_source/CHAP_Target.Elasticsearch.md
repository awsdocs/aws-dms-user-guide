# Using an Amazon OpenSearch Service cluster as a target for AWS Database Migration Service<a name="CHAP_Target.Elasticsearch"></a>

You can use AWS DMS to migrate data to Amazon OpenSearch Service \(OpenSearch Service\)\. OpenSearch Service is a managed service that makes it easy to deploy, operate, and scale an OpenSearch Service cluster\. 

In OpenSearch Service, you work with indexes and documents\. An *index* is a collection of documents, and a *document* is a JSON object containing scalar values, arrays, and other objects\. OpenSearch provides a JSON\-based query language, so that you can query data in an index and retrieve the corresponding documents\.

When AWS DMS creates indexes for a target endpoint for OpenSearch Service, it creates one index for each table from the source endpoint\. The cost for creating an OpenSearch Service index depends on several factors\. These are the number of indexes created, the total amount of data in these indexes, and the small amount of metadata that OpenSearch stores for each document\.

Configure your OpenSearch Service cluster with compute and storage resources that are appropriate for the scope of your migration\. We recommend that you consider the following factors, depending on the replication task you want to use:
+ For a full data load, consider the total amount of data that you want to migrate, and also the speed of the transfer\.
+ For replicating ongoing changes, consider the frequency of updates, and your end\-to\-end latency requirements\.

Also, configure the index settings on your OpenSearch cluster, paying close attention to the document count\.

**Multithreaded full load task settings**

To help increase the speed of the transfer, AWS DMS supports a multithreaded full load to an OpenSearch Service target cluster\. AWS DMS supports this multithreading with task settings that include the following:
+ `MaxFullLoadSubTasks` – Use this option to indicate the maximum number of source tables to load in parallel\. DMS loads each table into its corresponding OpenSearch Service target index using a dedicated subtask\. The default is 8; the maximum value is 49\.
+ `ParallelLoadThreads` – Use this option to specify the number of threads that AWS DMS uses to load each table into its OpenSearch Service target index\. The maximum value for an OpenSearch Service target is 32\. You can ask to have this maximum limit increased\.
**Note**  
If you don't change `ParallelLoadThreads` from its default \(0\), AWS DMS transfers a single record at a time\. This approach puts undue load on your OpenSearch Service cluster\. Make sure that you set this option to 1 or more\.
+ `ParallelLoadBufferSize` – Use this option to specify the maximum number of records to store in the buffer that the parallel load threads use to load data to the OpenSearch Service target\. The default value is 50\. The maximum value is 1,000\. Use this setting with `ParallelLoadThreads`\. `ParallelLoadBufferSize` is valid only when there is more than one thread\.

For more information on how DMS loads an OpenSearch Service cluster using multithreading, see the AWS blog post [Scale Amazon OpenSearch Service for AWS Database Migration Service migrations](http://aws.amazon.com/blogs/database/scale-amazon-elasticsearch-service-for-aws-database-migration-service-migrations/)\. 

**Multithreaded CDC load task settings**

You can improve the performance of change data capture \(CDC\) for an OpenSearch Service target cluster using task settings to modify the behavior of the `PutRecords` API call\. To do this, you can specify the number of concurrent threads, queues per thread, and the number of records to store in a buffer using `ParallelApply*` task settings\. For example, suppose you want to perform a CDC load and apply 32 threads in parallel\. You also want to access 64 queues per thread, with 50 records stored per buffer\. 
**Note**  
Support for the use of `ParallelApply*` task settings during CDC to Amazon OpenSearch Service target endpoints is available in AWS DMS versions 3\.4\.0 and higher\.

To promote CDC performance, AWS DMS supports these task settings:
+ `ParallelApplyThreads` – Specifies the number of concurrent threads that AWS DMS uses during a CDC load to push data records to a OpenSearch Service target endpoint\. The default value is zero \(0\) and the maximum value is 32\.
+ `ParallelApplyBufferSize` – Specifies the maximum number of records to store in each buffer queue for concurrent threads to push to a OpenSearch Service target endpoint during a CDC load\. The default value is 100 and the maximum value is 1,000\. Use this option when `ParallelApplyThreads` specifies more than one thread\. 
+ `ParallelApplyQueuesPerThread` – Specifies the number of queues that each thread accesses to take data records out of queues and generate a batch load for a OpenSearch Service endpoint during CDC\.

When using `ParallelApply*` task settings, the `partition-key-type` default is the `primary-key` of the table, not `schema-name.table-name`\.

## Migrating from a relational database table to an OpenSearch Service index<a name="CHAP_Target.Elasticsearch.RDBMS2Elasticsearch"></a>

AWS DMS supports migrating data to OpenSearch Service's scalar data types\. When migrating from a relational database like Oracle or MySQL to OpenSearch Service, you might want to restructure how you store this data\.

AWS DMS supports the following OpenSearch Service scalar data types: 
+ Boolean 
+ Date
+ Float
+ Int
+ String

AWS DMS converts data of type Date into type String\. You can specify custom mapping to interpret these dates\.

AWS DMS doesn't support migration of LOB data types\.

## Prerequisites for using Amazon OpenSearch Service as a target for AWS Database Migration Service<a name="CHAP_Target.Elasticsearch.Prerequisites"></a>

Before you begin work with an OpenSearch Service database as a target for AWS DMS, make sure that you create an AWS Identity and Access Management \(IAM\) role\. This role should let AWS DMS access the OpenSearch Service indexes at the target endpoint\. The minimum set of access permissions is shown in the following IAM policy\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "1",
            "Effect": "Allow",
            "Principal": {
                "Service": "dms.amazonaws.com"
        },
        "Action": "sts:AssumeRole"
        }
    ]
}
```

The role that you use for the migration to OpenSearch Service must have the following permissions\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                       "es:ESHttpDelete",
                       "es:ESHttpGet",  
                       "es:ESHttpHead",
                       "es:ESHttpPost",
                       "es:ESHttpPut"
                     ],
            "Resource": "arn:aws:es:region:account-id:domain/domain-name/*"
        }
    ]
}
```

In the preceding example, replace `region` with the AWS Region identifier, *`account-id`* with your AWS account ID, and `domain-name` with the name of your Amazon OpenSearch Service domain\. An example is `arn:aws:es:us-west-2:123456789012:domain/my-es-domain`

## Endpoint settings when using OpenSearch Service as a target for AWS DMS<a name="CHAP_Target.Elasticsearch.Configuration"></a>

You can use endpoint settings to configure your OpenSearch Service target database similar to using extra connection attributes\. You specify the settings when you create the target endpoint using the AWS DMS console, or by using the `create-endpoint` command in the [AWS CLI](https://docs.aws.amazon.com/cli/latest/reference/dms/index.html), with the `--elasticsearch-settings '{"EndpointSetting": "value", ...}'` JSON syntax\.

The following table shows the endpoint settings that you can use with OpenSearch Service as a target\.


| Attribute name | Valid values | Default value and description | 
| --- | --- | --- | 
|  `FullLoadErrorPercentage`   |  A positive integer greater than 0 but no larger than 100\.  |  10 – For a full load task, this attribute determines the threshold of errors allowed before the task fails\. For example, suppose that there are 1,500 rows at the source endpoint and this parameter is set to 10\. Then the task fails if AWS DMS encounters more than 150 errors \(10 percent of the row count\) when writing to the target endpoint\.  | 
|   `ErrorRetryDuration`   |  A positive integer greater than 0\.  |  300 – If an error occurs at the target endpoint, AWS DMS retries for this many seconds\. Otherwise, the task fails\.  | 

## Limitations when using Amazon OpenSearch Service as a target for AWS Database Migration Service<a name="CHAP_Target.Elasticsearch.Limitations"></a>

The following limitations apply when using Amazon OpenSearch Service as a target:
+ OpenSearch Service uses dynamic mapping \(auto guess\) to determine the data types to use for migrated data\.
+ OpenSearch Service stores each document with a unique ID\. The following is an example ID\. 

  ```
  "_id": "D359F8B537F1888BC71FE20B3D79EAE6674BE7ACA9B645B0279C7015F6FF19FD"
  ```

  Each document ID is 64 bytes long, so anticipate this as a storage requirement\. For example, if you migrate 100,000 rows from an AWS DMS source, the resulting OpenSearch Service index requires storage for an additional 6,400,000 bytes\.
+ With OpenSearch Service, you can't make updates to the primary key attributes\. This restriction is important when using ongoing replication with change data capture \(CDC\) because it can result in unwanted data in the target\. In CDC mode, primary keys are mapped to SHA256 values, which are 32 bytes long\. These are converted to human\-readable 64\-byte strings, and are used as OpenSearch Service document IDs\.
+ If AWS DMS encounters any items that can't be migrated, it writes error messages to Amazon CloudWatch Logs\. This behavior differs from that of other AWS DMS target endpoints, which write errors to an exceptions table\.
+ AWS DMS doesn't support connection to an Amazon ES cluster that has Fine\-grained Access Control enabled with master user and password\.
+ AWS DMS doesn't support OpenSearch Service serverless\.

## Target data types for Amazon OpenSearch Service<a name="CHAP_Target.Elasticsearch.DataTypes"></a>

When AWS DMS migrates data from heterogeneous databases, the service maps data types from the source database to intermediate data types called AWS DMS data types\. The service then maps the intermediate data types to the target data types\. The following table shows each AWS DMS data type and the data type it maps to in OpenSearch Service\.


| AWS DMS data type | OpenSearch Service data type | 
| --- | --- | 
|  Boolean  |  boolean  | 
|  Date  |  string  | 
|  Time  |  date  | 
|  Timestamp  |  date  | 
|  INT4  |  integer  | 
|  Real4  |  float  | 
|  UINT4  |  integer  | 

For additional information about AWS DMS data types, see [Data types for AWS Database Migration Service](CHAP_Reference.DataTypes.md)\.