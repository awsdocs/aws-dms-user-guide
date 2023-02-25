# Time Travel task settings<a name="CHAP_Tasks.CustomizingTasks.TaskSettings.TimeTravel"></a>

To log and debug replication tasks, you can use AWS DMS Time Travel\. In this approach, you use Amazon S3 to store logs and encrypt them using your encryption keys\. Only with access to your Time Travel S3 bucket, can you retrieve your S3 logs using date\-time filters, then view, download, and obfuscate logs as needed\. By doing this, you can securely "travel back in time" to investigate database activities\. Time Travel works independently from the CloudWatch logging\. For more information on CloudWatch logging, see [Logging task settings](CHAP_Tasks.CustomizingTasks.TaskSettings.Logging.md)\. 

You can use Time Travel in all AWS Regions with DMS\-supported PostgreSQL source endpoints and DMS\-supported PostgreSQL and MySQL target endpoints\. You can turn on Time Travel only for full\-load and CDC tasks and for CDC only tasks\. To turn on Time Travel or to modify any existing Time Travel settings, ensure that your replication task is stopped\.

The Time Travel settings include the `TTSettings` properties following:
+ `EnableTT` – If this option is set to `true`, Time Travel logging is turned on for the task\. The default value is `false`\.

  Type: Boolean

  Required: No
+ `EncryptionMode` – The type of server\-side encryption being used on your S3 bucket to store your data and logs\. You can specify either `"SSE_S3"` \(the default\) or `"SSE_KMS"`\.

  You can change `EncryptionMode` from `"SSE_KMS"` to `"SSE_S3"`, but not the reverse\.

  Type: String

  Required: No
+ `ServerSideEncryptionKmsKeyId` – If you specify `"SSE_KMS"` for `EncryptionMode`, provide the ID for your custom managed AWS KMS key\. Make sure that the key that you use has an attached policy that turns on AWS Identity and Access Management \(IAM\) user permissions and allows use of the key\. 

  Only your own custom\-managed symmetric KMS key is supported with the `"SSE_KMS"` option\.

  Type: String

  Required: Only if you set `EncryptionMode` to `"SSE_KMS"`
+ `ServiceAccessRoleArn` – The Amazon Resource Name \(ARN\) used by the service to access the IAM role\. Set the role name to `dms-tt-s3-access-role`\. This is a required setting that allows AWS DMS to write and read objects from an S3 bucket\.

  Type: String

  Required: If Time Travel is turned on

  Following is an example policy for this role\.

  ```
  {
   "Version": "2012-10-17",
   "Statement": [
          {
              "Sid": "VisualEditor0",
              "Effect": "Allow",
              "Action": [
                  "s3:PutObject",
                  "kms:GenerateDataKey",
                  "kms:Decrypt",
                  "s3:ListBucket",
                  "s3:DeleteObject"
              ],
              "Resource": [
                  "arn:aws:s3:::S3bucketName*",
                  "arn:aws:kms:us-east-1:112233445566:key/1234a1a1-1m2m-1z2z-d1d2-12dmstt1234"
              ]
          }
      ]
  }
  ```

  Following is an example trust policy for this role\.

  ```
  {
   "Version": "2012-10-17",
   "Statement": [
           {
               "Effect": "Allow",
               "Principal": {
                   "Service": [
                       "dms.amazonaws.com"
                   ]
               },
               "Action": "sts:AssumeRole"
          }
      ]
  }
  ```
+ `BucketName` – The name of the S3 bucket to store Time Travel logs\. Make sure to create this S3 bucket before turning on Time Travel logs\.

  Type: String

  Required: If Time Travel is turned on
+ `BucketFolder` – An optional parameter to set a folder name in the S3 bucket\. If you specify this parameter, DMS creates the Time Travel logs in the path `"/BucketName/BucketFolder/taskARN/YYYY/MM/DD/hh"`\. If you don't specify this parameter, AWS DMS creates the default path as `"/BucketName/dms-time-travel-logs/taskARN/YYYY/MM/DD/hh`\.

  Type: String

  Required: No
+ `EnableDeletingFromS3OnTaskDelete` – When this option is set to `true`, AWS DMS deletes the Time Travel logs from S3 if the task is deleted\. The default value is `false`\.

  Type: String

  Required: No
+ `EnableRawData ` – When this option is set to `true`, the data manipulation language \(DML\) raw data for Time Travel logs appears under the `raw_data` column of the Time Travel logs\. For the details, see [Using the Time Travel logs](CHAP_Tasks.CustomizingTasks.TaskSettings.TimeTravel.LogSchema.md)\. The default value is `false`\. When this option is set to `false`, only the type of DML is captured\.

  Type: String

  Required: No
+ `OperationsToLog` – Specifies the type of DML operations to log in Time Travel logs\. You can specify one of the following:
  + `"INSERT"`
  + `"UPDATE"`
  + `"DELETE"`
  + `"COMMIT"`
  + `"ROLLBACK"`
  + `"ALL"`

  The default is `"ALL"`\.

  Type: String

  Required: No
+ `MaxRecordSize` – Specifies the maximum size of Time Travel log records that are logged for each row\. Use this parameter to control the growth of Time Travel logs for especially busy tables\. The default is 64 KB\.

  Type: Integer

  Required: No

For more information on turning on and using Time Travel logs, see the following topics\.

**Topics**
+ [Turning on the Time Travel logs for a task](CHAP_Tasks.CustomizingTasks.TaskSettings.TimeTravel.TaskEnabling.md)
+ [Using the Time Travel logs](CHAP_Tasks.CustomizingTasks.TaskSettings.TimeTravel.LogSchema.md)
+ [How often AWS DMS uploads Time Travel logs to S3](CHAP_Tasks.CustomizingTasks.TaskSettings.TimeTravel.UploadsToS3.md)