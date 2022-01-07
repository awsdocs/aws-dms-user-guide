# Quotas for AWS Database Migration Service<a name="CHAP_Limits"></a>

Following, you can find the resource quotas and naming constraints for AWS Database Migration Service \(AWS DMS\)\.

The maximum size of a database that AWS DMS can migrate depends on a number of factors\. These include your source environment, the distribution of data in your source database, and how busy your source system is\. 

The best way to determine whether your particular system is a candidate for AWS DMS is to test it\. Start slowly so you can get the configuration worked out, then add some complex objects\. Finally, attempt a full load as a test\. 

## Resource quotas for AWS Database Migration Service<a name="CHAP_Limits.Limits"></a>

Each AWS account has quotas for each AWS Region on the number of AWS DMS resources that can be created\. After a quota for a resource has been reached, additional calls to create that resource fail with an exception\.

The following table lists the AWS DMS resources and their quotas for each AWS Region\.


| Resource | Default quota | 
| --- | --- | 
| API request throttling | 100 request maximum per second | 
| API request refresh rate | 4 requests per second | 
| Replication instances per user | 60 | 
| Total amount of storage for a replication instance | 30,000 GB | 
| Event subscriptions per user | 60 | 
| Replication subnet groups per user | 60 | 
| Subnets per replication subnet group | 60 | 
| Endpoints per user | 1000 | 
| Endpoints per replication instance | 100 | 
| Tasks per user | 600 | 
| Tasks per replication instance | 200 | 
| Certificates per user | 100 | 

For more information on the API request throttling quota and refresh rate, see [Understanding API request throttling](#CHAP_Limits.Throttling)\.

The 30,000\-GB quota for storage applies to all your AWS DMS replication instances in a given AWS Region\. This storage is used to cache changes if a target can't keep up with a source, and for storing log information\.

## Understanding API request throttling<a name="CHAP_Limits.Throttling"></a>

AWS DMS supports a varying, but maximum API request quota of 100 API calls per second\. In other words, your API requests are throttled when they exceed this rate\. Also, you can be limited to fewer API calls per second, depending on how long it takes AWS DMS to refresh your quota before you make another API request\. This quota applies both when you make API calls directly and when they are made on your behalf as part of using the AWS DMS Management Console\.

To understand how API request throttling works, it helps to imagine that AWS DMS maintains a token bucket that tracks your API requests\. In this scenario, each token in the bucket allows you to make a single API call\. You can have no more than 100 tokens in the bucket at any one time\. When you make an API call, AWS DMS removes one token from the bucket\. If you make 100 API calls in under a second, your bucket is empty and any attempt to make another API call fails\. For each second that you don't make an API call, AWS DMS adds 4 tokens to the bucket, up to the 100 token maximum\. This is the AWS DMS API request refresh rate\. At any point after throttling, when you have tokens added to your bucket, you can make as many additional API calls as tokens available until your calls are throttled again\.

If you are using the AWS CLI to run API calls that are throttled, AWS DMS returns an error like the following:

```
An error occurred (ThrottlingException) when calling the AwsDmsApiCall operation (reached max retries: 2): Rate exceeded
```

Here, `AwsDmsApiCall` is the name of the AWS DMS API operation that was throttled, for example, `DescribeTableStatistics`\. You can then retry or make a different call after sufficient delay to avoid throttling\.

**Note**  
Unlike API request throttling managed by some other services, such as Amazon EC2, you can't order an increase in the API request throttling quotas managed by AWS DMS\.