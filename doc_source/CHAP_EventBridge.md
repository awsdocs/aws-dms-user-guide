# Working with Amazon EventBridge events and notifications in AWS Database Migration Service<a name="CHAP_EventBridge"></a>

You can use Amazon EventBridge to provide notification of when an AWS DMS event occurs, for example the creation or deletion of a replication instance\. EventBridge receives events and routes notification of an event as defined by event rules\. You can work with notifications in any form supported by Amazon EventBridge for an AWS Region\. For more information about using Amazon EventBridge, see [What is Amazon EventBridge?](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-what-is.html) in the *Amazon EventBridge User Guide*\.

**Note**  
Working with Amazon EventBridge events is supported in AWS DMS version 3\.4\.6 and later\.

EventBridge receives an event, an indicator of a change in AWS DMS environment, and applies a rule to route the event to a notification mechanism\. Rules match events to notification mechanisms based on the structure of the event, called an *event pattern*\.

AWS DMS groups events into categories that you can apply an event rule to, so you can be notified when an event in that category occurs\. For example, suppose that you apply an EventBridge event rule to the Creation category for a given replication instance\. You're then notified whenever a creation\-related event occurs that affects your replication instance\. If you apply a rule to a Configuration Change category for a replication instance, you're notified when the replication instance's configuration is changed\. For a list of the event categories provided by AWS DMS, see the AWS DMS event categories and event messages, following\.

**Note**  
To allow publishing from events\.amazonaws\.com, make sure to update your Amazon SNS topics' access policies\. For more information, see [Using resource\-based policies for Amazon EventBridge](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-use-resource-based.html) in the *Amazon EventBridge User Guide*\.  
For more information on using text messages with Amazon SNS, see [Sending and receiving SMS notifications using Amazon SNS](https://docs.aws.amazon.com/sns/latest/dg/SMSMessages.html)\.

## Using Amazon EventBridge event rules for AWS DMS<a name="CHAP_EventBridge.Rule"></a>

Amazon EventBridge sends event notifications to the addresses that you provide when you create an EventBridge event rule\. You might want to create several different rules\. For example, you might create one rule receiving all event notifications and another rule that includes only critical events for your production DMS resources\. You can also turn on or turn off event notifications in EventBridge\.

**To create Amazon EventBridge rules that react to AWS DMS events**
+ Perform the steps described in [Creating Amazon EventBridge rules that react to events](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-create-rule.html) in the *Amazon EventBridge User Guide*, and create a rule for AWS DMS events:

  1. Specify a notification action to take when EventBridge receives an event that matches the event pattern in the rule\. When an event matches, EventBridge sends the event and invokes the action defined in the rule\.

  1. For **Service provider**, choose **AWS**\.

  1. For **Service name**, choose **Database Migration Service \(DMS\)**\.

You can then begin to receive event notifications\.

The following JSON example shows an EventBridge events model for an AWS DMS service\.

```
{
   "version":"0",
   "id":"11a11b11-222b-333a-44d4-01234a5b67890",
   "detail-type":"DMS Replication Task State Change",
   "source":"aws.dms",
   "account":"0123456789012",
   "time":"1970-01-01T00:00:00Z",
   "region":"us-east-1",
   "resources":[
      "arn:aws:dms:us-east-1:012345678901:task:AAAABBBB0CCCCDDDDEEEEE1FFFF2GGG3FFFFFF3"
   ],
   "detail":{
      "type":"ReplicationTask",
      "category":"StateChange",
      "eventType":"REPLICATION_TASK_STARTED",
      "eventName":"DMS-EVENT-0069",
      "resourceLink":"https://console.aws.amazon.com/dms/v2/home?region=us-east-1#taskDetails/taskName",
      "detailMessage":"Replication task started, with flag = fresh start"
   }
}
```

For the list of categories and events that you can be notified of, see the following section\. 

## AWS DMS event categories and event messages<a name="EventBridge.Messages"></a>

 AWS DMS generates a significant number of events in categories that you can identify\. Each category applies to a replication instance or replication task source types\. 

The following table shows the possible categories and events for the replication instance source type\.


|  Category  |  Description  | 
| --- | --- | 
|  Configuration Change  |  The replication instance class for this replication instance is being changed\.   | 
|  Configuration Change  |  The replication instance class for this replication instance has changed\.   | 
|  Configuration Change  |  The storage for the replication instance is being increased\.   | 
|  Configuration Change  |  The storage for the replication instance has been increased\.   | 
|  Configuration Change  |  The replication instance is transitioning to a Multi\-AZ configuration\.   | 
|  Configuration Change  |  The replication instance finished transitioning to a Multi\-AZ configuration\.   | 
|  Configuration Change  |  The replication instance is transitioning to a Single\-AZ configuration\.   | 
|  Configuration Change  |  The replication instance has finished transitioning to a Single\-AZ configuration\.   | 
|  Creation  |  A replication instance is being created\.   | 
|  Creation  |  A replication instance is created\.   | 
|  Deletion  |  The replication instance is being deleted\.   | 
|  Deletion  |  The replication instance is deleted\.   | 
|  Maintenance  | Management software on the replication instance has been updated\. | 
|  Maintenance  | Offline maintenance of the replication instance is taking place\. The replication instance is currently unavailable\.  | 
|  Maintenance  | Offline maintenance of the replication instance is complete\. The replication instance is now available\.  | 
|  Maintenance  | A replication instance is in a state that can't be upgraded\.  | 
|  LowStorage  | Free storage for the replication instance is low\.  | 
|  Failover  | Failover started for a Multi\-AZ replication instance\.  | 
|  Failover  | Failover is complete for a Multi\-AZ replication instance\. | 
|  Failover  | Multi\-AZ failover to standby is complete\. | 
|  Failover  | Multi\-AZ activation has started\.  | 
|  Failover  | Multi\-AZ activation had completed\.  | 
|  Failover  | If you request failover too frequently, this event occurs instead of regular failover events\. | 
|  Failure  | The replication instance has gone into storage failure\. | 
|  Failure  | The replication instance has failed due to an incompatible network\. | 
|  Failure  | The service can't access the AWS KMS key that was used to encrypt the data volume\. | 

The following table shows the possible categories and events for the replication task source type\.


|  Category  |  Description  | 
| --- | --- | 
|  State Change  |  The replication task has started\.   | 
|  State Change  |  A reload of table details has been requested\.   | 
|  State Change  |  The replication task has stopped\.   | 
|  State Change  | Reading was paused because the swap files limit was reached\. | 
|  State Change  | Reading was paused because the swap files limit was reached\. | 
|  State Change  | Reading resumed\. | 
|  Failure  |  The replication task has failed\.   | 
|  Failure  |  A call to delete the task has failed to clean up task data\.   | 
|  Configuration Change  | The replication task is modified\.  | 
|  Deletion  |  The replication task is deleted\.   | 
|  Creation  | The replication task is created\. | 