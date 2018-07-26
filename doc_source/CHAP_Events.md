# Working with Events and Notifications in AWS Database Migration Service<a name="CHAP_Events"></a>

 AWS Database Migration Service \(AWS DMS\) uses Amazon Simple Notification Service \(Amazon SNS\) to provide notifications when an AWS DMS event occurs, for example the creation or deletion of a replication instance\. You can work with these notifications in any form supported by Amazon SNS for an AWS Region, such as an email message, a text message, or a call to an HTTP endpoint\. 

AWS DMS groups events into categories that you can subscribe to, so you can be notified when an event in that category occurs\. For example, if you subscribe to the Creation category for a given replication instance, you are notified whenever a creation\-related event occurs that affects your replication instance\. If you subscribe to a Configuration Change category for a replication instance, you are notified when the replication instance's configuration is changed\. You also receive notification when an event notification subscription changes\. For a list of the event categories provided by AWS DMS, see [AWS DMS Event Categories and Event Messages](#USER_Events.Messages), following\.

AWS DMS sends event notifications to the addresses you provide when you create an event subscription\. You might want to create several different subscriptions, such as one subscription receiving all event notifications and another subscription that includes only critical events for your production DMS resources\. You can easily turn off notification without deleting a subscription by setting the **Enabled** option to **No** in the AWS DMS console or by setting the `Enabled` parameter to *false* using the AWS DMS API\. 

**Note**  
AWS DMS event notifications using SMS text messages are currently available for AWS DMS resources in all regions where AWS DMS is supported\. For more information on using text messages with SNS, see [Sending and Receiving SMS Notifications Using Amazon SNS](http://docs.aws.amazon.com/sns/latest/dg//SMSMessages.html)\.

AWS DMS uses a subscription identifier to identify each subscription\. You can have multiple AWS DMS event subscriptions published to the same Amazon SNS topic\. When you use event notification, Amazon SNS fees apply; for more information on Amazon SNS billing, see [ Amazon SNS Pricing](http://aws.amazon.com/sns/#pricing)\.

To subscribe to AWS DMS events, you use the following process:

1. Create an Amazon SNS topic\. In the topic, you specify what type of notification you want to receive and to what address or number the notification will go to\.

1. Create an AWS DMS event notification subscription by using the AWS Management Console, AWS CLI, or AWS DMS API\.

1. AWS DMS sends an approval email or SMS message to the addresses you submitted with your subscription\. To confirm your subscription, click the link in the approval email or SMS message\.

1. When you have confirmed the subscription, the status of your subscription is updated in the AWS DMS console's **Event Subscriptions** section\.

1. You then begin to receive event notifications\.

For the list of categories and events that you can be notified of, see the following section\. For more details about subscribing to and working with AWS DMS event subscriptions, see [Subscribing to AWS DMS Event Notification](#CHAP_Events.Subscription)\.

## AWS DMS Event Categories and Event Messages<a name="USER_Events.Messages"></a>

 AWS DMS generates a significant number of events in categories that you can subscribe to using the AWS DMS console or the AWS DMS API\. Each category applies to a source type; currently AWS DMS supports the replication instance and replication task source types\. 

The following table shows the possible categories and events for the replication instance source type\.


|  Category  |  DMS Event ID  |  Description  | 
| --- | --- | --- | 
|  Configuration Change  |  DMS\-EVENT\-0012  |  REP\_INSTANCE\_CLASS\_CHANGING – The replication instance class for this replication instance is being changed\.   | 
|  Configuration Change  |  DMS\-EVENT\-0014  |  REP\_INSTANCE\_CLASS\_CHANGE\_COMPLETE – The replication instance class for this replication instance has changed\.   | 
|  Configuration Change  |  DMS\-EVENT\-0018  |  BEGIN\_SCALE\_STORAGE – The storage for the replication instance is being increased\.   | 
|  Configuration Change  |  DMS\-EVENT\-0017  |  FINISH\_SCALE\_STORAGE – The storage for the replication instance has been increased\.   | 
|  Configuration Change  |  DMS\-EVENT\-0024  |  BEGIN\_CONVERSION\_TO\_HIGH\_AVAILABILITY – The replication instance is transitioning to a Multi\-AZ configuration\.   | 
|  Configuration Change  |  DMS\-EVENT\-0025  |  FINISH\_CONVERSION\_TO\_HIGH\_AVAILABILITY – The replication instance has finished transitioning to a Multi\-AZ configuration\.   | 
|  Configuration Change  |  DMS\-EVENT\-0030  |  BEGIN\_CONVERSION\_TO\_NON\_HIGH\_AVAILABILITY – The replication instance is transitioning to a Single\-AZ configuration\.   | 
|  Configuration Change  |  DMS\-EVENT\-0029  |  FINISH\_CONVERSION\_TO\_NON\_HIGH\_AVAILABILITY – The replication instance has finished transitioning to a Single\-AZ configuration\.   | 
|  Creation  |  DMS\-EVENT\-0067  |  CREATING\_REPLICATION\_INSTANCE – A replication instance is being created\.   | 
|  Creation  |  DMS\-EVENT\-0005  |  CREATED\_REPLICATION\_INSTANCE – A replication instance has been created\.   | 
|  Deletion  |  DMS\-EVENT\-0066  |  DELETING\_REPLICATION\_INSTANCE – The replication instance is being deleted\.   | 
|  Deletion  |  DMS\-EVENT\-0003  |  DELETED\_REPLICATION\_INSTANCE – The replication instance has been deleted\.   | 
|  Maintenance  |  DMS\-EVENT\-0047  |  FINISH\_PATCH\_INSTANCE – Management software on the replication instance has been updated\. | 
|  Maintenance  |  DMS\-EVENT\-0026  |  BEGIN\_PATCH\_OFFLINE – Offline maintenance of the replication instance is taking place\. The replication instance is currently unavailable\.  | 
|  Maintenance  |  DMS\-EVENT\-0027  |  FINISH\_PATCH\_OFFLINE – Offline maintenance of the replication instance is complete\. The replication instance is now available\.  | 
|  LowStorage  |  DMS\-EVENT\-0007  |  LOW\_STORAGE – Free storage for the replication instance is low\.  | 
|  Failover  |  DMS\-EVENT\-0013  |  FAILOVER\_STARTED – Failover started for a Multi\-AZ replication instance\.  | 
|  Failover  |  DMS\-EVENT\-0049  |  FAILOVER\_COMPLETED – Failover has been completed for a Multi\-AZ replication instance\. | 
|  Failover  |  DMS\-EVENT\-0050  |  MAZ\_INSTANCE\_ACTIVATION\_STARTED – Multi\-AZ activation has started\.  | 
|  Failover  |  DMS\-EVENT\-0051  |  MAZ\_INSTANCE\_ACTIVATION\_COMPLETED – Multi\-AZ activation completed\.  | 
|  Failure  |  DMS\-EVENT\-0031  |  REPLICATION\_INSTANCE\_FAILURE – The replication instance has gone into storage failure\. | 
|  Failure  |  DMS\-EVENT\-0036  |  INCOMPATIBLE\_NETWORK – The replication instance has failed due to an incompatible network\. | 

The following table shows the possible categories and events for the replication task source type\.


|  Category  |  DMS Event ID  |  Description  | 
| --- | --- | --- | 
|  StateChange  |  DMS\-EVENT\-0069  |  REPLICATION\_TASK\_STARTED – The replication task has started\.   | 
|  StateChange  |  DMS\-EVENT\-0077  |  REPLICATION\_TASK\_STOPPED – The replication task has stopped\.   | 
|  Failure  |  DMS\-EVENT\-0078  |  REPLICATION\_TASK\_FAILED – A replication task has failed\.   | 
|  Deletion  |  DMS\-EVENT\-0073  |  REPLICATION\_TASK\_DELETED – The replication task has been deleted\.   | 
|  Creation  |  DMS\-EVENT\-0074  |  REPLICATION\_TASK\_CREATED – The replication task has been created\.  | 

## Subscribing to AWS DMS Event Notification<a name="CHAP_Events.Subscription"></a>

You can create an AWS DMS event notification subscription so you can be notified when an AWS DMS event occurs\. The simplest way to create a subscription is with the AWS DMS console\. If you choose to create event notification subscriptions using AWS DMS API, you must create an Amazon SNS topic and subscribe to that topic with the Amazon SNS console or API\. In this case, you also need to note the topic's Amazon Resource Name \(ARN\), because this ARN is used when submitting CLI commands or API actions\. For information on creating an Amazon SNS topic and subscribing to it, see [Getting Started with Amazon SNS](http://docs.aws.amazon.com/sns/latest/dg/GettingStarted.html)\.

In a notification subscription, you can specify the type of source you want to be notified of and the AWS DMS source that triggers the event\. You define the AWS DMS source type by using a **SourceType** value\. You define the source generating the event by using a **SourceIdentifier** value\. If you specify both **SourceType** and **SourceIdentifier**, such as `SourceType = db-instance` and `SourceIdentifier = myDBInstance1`, you receive all the DB\_Instance events for the specified source\. If you specify **SourceType** but not **SourceIdentifier**, you receive notice of the events for that source type for all your AWS DMS sources\. If you don't specify either **SourceType** or **SourceIdentifier**, you are notified of events generated from all AWS DMS sources belonging to your customer account\.

### AWS Management Console<a name="USER_Events.Subscribing.Console"></a>

**To subscribe to AWS DMS event notification by using the console**

1. Sign in to the AWS Management Console and choose AWS DMS\. Note that if you are signed in as an AWS Identity and Access Management \(IAM\) user, you must have the appropriate permissions to access AWS DMS\. 

1.  In the navigation pane, choose **Event Subscriptions**\. 

1.  On the **Event Subscriptions** page, choose **Create Event Subscription**\. 

1.  On the **Create Event Subscription** page, do the following:

   1. For **Name**, type a name for the event notification subscription\.

   1. Either choose an existing Amazon SNS topic for **Send notifications to**, or choose **create topic**\. You must have either an existing Amazon SNS topic to send notices to or you must create the topic\. If you choose **create topic**, you can enter an email address where notifications will be sent\.

   1. For **Source Type**, choose a source type\. The only option is **replication instance**\.

   1. Choose **Yes** to enable the subscription\. If you want to create the subscription but not have notifications sent yet, choose **No**\.

   1. Depending on the source type you selected, choose the event categories and sources you want to receive event notifications for\.  
![\[Console Tags tab\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-events1.png)

   1. Choose **Create**\.

The AWS DMS console indicates that the subscription is being created\.

### AWS DMS API<a name="USER_Events.Subscribing.API"></a>

**To subscribe to AWS DMS event notification by using the AWS DMS API**
+  Call [ `CreateEventSubscription`](http://docs.aws.amazon.com/dms/latest/APIReference//API_CreateEventSubscription.html)\. 