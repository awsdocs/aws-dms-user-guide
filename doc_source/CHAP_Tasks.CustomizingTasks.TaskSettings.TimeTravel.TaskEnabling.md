# Turning on the Time Travel logs for a task<a name="CHAP_Tasks.CustomizingTasks.TaskSettings.TimeTravel.TaskEnabling"></a>

You can turn on Time Travel for an AWS DMS task using the task settings described previously\. Make sure that your replication task is stopped before you turn on Time Travel\.

**To turn on Time Travel using the AWS CLI**

1. Create a DMS task configuration JSON file and add a `TTSettings` section such as the following\. For information about how to use a task configuration file to set task settings, see [Task settings example](CHAP_Tasks.CustomizingTasks.TaskSettings.md#CHAP_Tasks.CustomizingTasks.TaskSettings.Example)\.

   ```
    .
    .
    .
       },
   "TTSettings" : {
     "EnableTT" : true,
     "TTS3Settings": {
         "EncryptionMode": "SSE_KMS",
         "ServerSideEncryptionKmsKeyId": "arn:aws:kms:us-west-2:112233445566:key/myKMSKey",
         "ServiceAccessRoleArn": "arn:aws:iam::112233445566:role/dms-tt-s3-access-role",
         "BucketName": "myttbucket",
         "BucketFolder": "myttfolder",
         "EnableDeletingFromS3OnTaskDelete": false
       },
     "TTRecordSettings": {
         "EnableRawData" : true,
         "OperationsToLog": "DELETE,UPDATE",
         "MaxRecordSize": 64
       },
    .
    .
    .
   ```

1. In an appropriate task action, specify this JSON file using the `--replication-task-settings` option\. For example, the CLI code fragment following specifies this Time Travel settings file as part of `create-replication-task`\.

   ```
   aws dms create-replication-task 
   --target-endpoint-arn arn:aws:dms:us-east-1:112233445566:endpoint:ELS5O7YTYV452CAZR2EYBNQGILFHQIFVPWFRQAY \
   --source-endpoint-arn arn:aws:dms:us-east-1:112233445566:endpoint:HNX2BWIIN5ZYFF7F6UFFZVWTDFFSMTNOV2FTXZA \
   --replication-instance-arn arn:aws:dms:us-east-1:112233445566:rep:ERLHG2UA52EEJJKFYNYWRPCG6T7EPUAB5AWBUJQ \
   --migration-type full-load-and-cdc --table-mappings 'file:///FilePath/mappings.json' \
   --replication-task-settings 'file:///FilePath/task-settings-tt-enabled.json' \
   --replication-task-identifier test-task
                               .
                               .
                               .
   ```

   Here, the name of this Time Travel settings file is `task-settings-tt-enabled.json`\.

Similarly, you can specify this file as part of the `modify-replication-task` action\.

Note the special handling of Time Travel logs for the task actions following:
+ `start-replication-task` – When you run a replication task, if an S3 bucket used for Time Travel isn't accessible, the task is marked as `FAILED`\.
+ `stop-replication-task` – When the task stops, AWS DMS immediately pushes all Time Travel logs that are currently available for the replication instance to the S3 bucket used for Time Travel\.

While a replication task runs, you can change the `EncryptionMode` value from `"SSE_KMS"` to `"SSE_S3"` but not the reverse\.

If the size of Time Travel logs for an ongoing task exceeds 1 GB, DMS pushes the logs to S3 within five minutes of reaching that size\. After a task is running, if the S3 bucket or KMS key becomes inaccessible, DMS stops pushing logs to this bucket\. If you find your logs aren't being pushed to your S3 bucket, check your S3 and AWS KMS permissions\. For more details on how often DMS pushes these logs to S3, see [How often AWS DMS uploads Time Travel logs to S3](CHAP_Tasks.CustomizingTasks.TaskSettings.TimeTravel.UploadsToS3.md)\.

To turn on Time Travel for an existing task from the console, use the JSON editor option under **Task Settings** to add a `TTSettings` section\.