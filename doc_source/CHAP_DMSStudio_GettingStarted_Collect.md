# Step 3: Collect and verify data<a name="CHAP_DMSStudio_GettingStarted_Collect"></a>


|  | 
| --- |
| AWS DMS Studio is in preview release for AWS Database Migration Service and is subject to change\. | 

Next, you collect information about your local database environment\. DMS Collector automatically uploads your database information to AWS after collection is complete\.

**To collect information about your local database environment**

1. In the navigation pane, choose **Data collection**\. 

1. On the **Data collection** page, choose **Start collection**\.

   When data collection completes, DMS Collector displays the data objects\. DMS Collector automatically uploads your data to AWS\.  
![\[Screenshot showing the local server\]](http://docs.aws.amazon.com/dms/latest/userguide/images/dmsstudio_07.png)

1. Sign in to the AWS Management Console and open the AWS DMS console at [https://console\.aws\.amazon\.com/dms/v2/](https://console.aws.amazon.com/dms/v2/) to verify that your data is successfully uploaded\.

1. In the navigation pane, choose **Inventory**\.

1. On the **Inventory** page, verify that your database information appears\.  
![\[Screenshot showing the database information\]](http://docs.aws.amazon.com/dms/latest/userguide/images/dmsstudio_08.png)

1. Choose the **localhost:3306** database\. On the **Database overview** page, verify that the database and schema information appears\.  
![\[Screenshot showing the database and schema information\]](http://docs.aws.amazon.com/dms/latest/userguide/images/dmsstudio_09.png)

Your information about your local database environment is now stored in AWS DMS\. In the next step, you run analysis on your database information and get recommendations about how best to migrate your databases to AWS\.