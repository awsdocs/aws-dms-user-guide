# How often AWS DMS uploads Time Travel logs to S3<a name="CHAP_Tasks.CustomizingTasks.TaskSettings.TimeTravel.UploadsToS3"></a>

To minimize the storage usage of your replication instance, AWS DMS offloads Time Travel logs from it periodically\. 

The Time travel logs get pushed to your Amazon S3 bucket in the cases following:
+ If the current size of logs exceeds 1 GB, AWS DMS uploads the logs to S3 within five minutes\. Thus, AWS DMS can make up to 12 calls an hour to S3 and AWS KMS for each running task\.
+ AWS DMS uploads the logs to S3 every hour, regardless of the size of the logs\.
+ When a task is stopped, AWS DMS immediately uploads the time travel logs to S3\.