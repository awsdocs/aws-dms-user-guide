# Deleting a replication instance<a name="CHAP_ReplicationInstance.Deleting"></a>

You can delete an AWS DMS replication instance when you are finished using it\. If you have migration tasks that use the replication instance, you must stop and delete the tasks before deleting the replication instance\.

If you close your AWS account, all AWS DMS resources and configurations associated with your account are deleted after two days\. These resources include all replication instances, source and target endpoint configuration, replication tasks, and SSL certificates\. If after two days you decide to use AWS DMS again, you recreate the resources you need\.

## Deleting a replication instance using the AWS console<a name="CHAP_ReplicationInstance.Deleting.CON"></a>

To delete a replication instance, use the AWS console\.

**To delete a replication instance using the AWS console**

1. Sign in to the AWS Management Console and open the AWS DMS console at [https://console\.aws\.amazon\.com/dms/v2/](https://console.aws.amazon.com/dms/v2/)\.

1. In the navigation pane, choose **Replication instances**\.

1. Choose the replication instance you want to delete\. 

1. Choose **Delete**\.

1. In the dialog box, choose **Delete**\.

## Deleting a replication instance using the CLI<a name="CHAP_ReplicationInstance.Deleting.CLI"></a>

To delete a replication instance, use the AWS CLI [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) command with the following parameter:
+ `--replication-instance-arn`

**Example delete**  
The following AWS CLI example deletes a replication instance\.  

```
aws dms delete-replication-instance \
--replication-instance-arn arn of my rep instance
```

## Deleting a replication instance using the API<a name="CHAP_ReplicationInstance.Deleting.API"></a>

To delete a replication instance, use the AWS DMS API [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) action with the following parameters:
+ `ReplicationInstanceArn = arn of my rep instance`

**Example delete**  
The following code example deletes a replication instance\.  

```
 1. https://dms.us-west-2.amazonaws.com/
 2. ?Action=DeleteReplicationInstance
 3. &DBInstanceArn=arn of my rep instance
 4. &SignatureMethod=HmacSHA256
 5. &SignatureVersion=4
 6. &Version=2014-09-01
 7. &X-Amz-Algorithm=AWS4-HMAC-SHA256
 8. &X-Amz-Credential=AKIADQKE4SARGYLE/20140425/us-east-1/dms/aws4_request
 9. &X-Amz-Date=20140425T192732Z
10. &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
11. &X-Amz-Signature=1dc9dd716f4855e9bdf188c70f1cf9f6251b070b68b81103b59ec70c3e7854b3
```