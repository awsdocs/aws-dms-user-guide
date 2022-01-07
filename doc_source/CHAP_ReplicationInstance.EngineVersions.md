# Working with replication engine versions<a name="CHAP_ReplicationInstance.EngineVersions"></a>

The *replication engine* is the core AWS DMS software that runs on your replication instance and performs the migration tasks you specify\. AWS periodically releases new versions of the AWS DMS replication engine software, with new features and performance improvements\. Each version of the replication engine software has its own version number, to distinguish it from other versions\.

When you launch a new replication instance, it runs the latest AWS DMS engine version unless you specify otherwise\. For more information, see [Working with an AWS DMS replication instance](CHAP_ReplicationInstance.md)\.

If you have a replication instance that is currently running, you can upgrade it to a more recent engine version\. \(AWS DMS doesn't support engine version downgrades\.\) For more information about replication engine versions, see [AWS DMS release notes](CHAP_ReleaseNotes.md)\.

## Upgrading the engine version using the console<a name="Upgrading.Console"></a>

You can upgrade an AWS DMS replication instance using the AWS Management Console\.

**To upgrade a replication instance using the console**

1. Open the AWS DMS console at [https://console\.aws\.amazon\.com/dms/](https://console.aws.amazon.com/dms/)\.

1. In the navigation pane, choose **Replication instances**\. 

1. Choose your replication engine, and then choose **Modify**\.

1. For **Replication engine version**, choose the version number you want, and then choose **Modify**\.

**Note**  
We recommend that you stop all tasks before upgrading the Replication Instance\. Upgrading the replication instance takes several minutes\. When the instance is ready, its status changes to **available**\.

## Upgrading the engine version using the AWS CLI<a name="Upgrading.CLI"></a>

You can upgrade an AWS DMS replication instance using the AWS CLI, as follows\.

**To upgrade a replication instance using the AWS CLI**

1. Determine the Amazon Resource Name \(ARN\) of your replication instance by using the following command\.

   ```
   aws dms describe-replication-instances \
   --query "ReplicationInstances[*].[ReplicationInstanceIdentifier,ReplicationInstanceArn,ReplicationInstanceClass]"
   ```

   In the output, take note of the ARN for the replication instance you want to upgrade, for example: `arn:aws:dms:us-east-1:123456789012:rep:6EFQQO6U6EDPRCPKLNPL2SCEEY` 

1. Determine which replication instance versions are available by using the following command\.

   ```
   aws dms describe-orderable-replication-instances \
   --query "OrderableReplicationInstances[*].[ReplicationInstanceClass,EngineVersion]"
   ```

   In the output, note the engine version number or numbers that are available for your replication instance class\. You should see this information in the output from step 1\.

1. Upgrade the replication instance by using the following command\.

   ```
   aws dms modify-replication-instance \
   --replication-instance-arn arn \
   --engine-version n.n.n
   ```

   Replace *arn* in the preceding with the actual replication instance ARN from the previous step\. 

   Replace *n\.n\.n * with the engine version number that you want, for example: `3.4.5`

**Note**  
Upgrading the replication instance takes several minutes\. You can view the replication instance status using the following command\.  

```
aws dms describe-replication-instances \
--query "ReplicationInstances[*].[ReplicationInstanceIdentifier,ReplicationInstanceStatus]"
```
When the replication instance is ready, its status changes to **available**\.