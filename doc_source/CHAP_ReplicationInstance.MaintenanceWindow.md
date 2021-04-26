# Working with the AWS DMS maintenance window<a name="CHAP_ReplicationInstance.MaintenanceWindow"></a>

Every AWS DMS replication instance has a weekly maintenance window during which any available system changes are applied\. You can think of the maintenance window as an opportunity to control when modifications and software patching occurs\. 

If AWS DMS determines that maintenance is required during a given week, the maintenance occurs during the 30\-minute maintenance window you chose when you created the replication instance\. AWS DMS completes most maintenance during the 30\-minute maintenance window\. However, a longer time might be required for larger changes\.

The 30\-minute maintenance window that you selected when you created the replication instance is from an 8\-hour block of time allocated for each AWS Region\. If you don't specify a preferred maintenance window when you create your replication instance, AWS DMS assigns one on a randomly selected day of the week\. For a replication instance that uses a Multi\-AZ deployment, a failover might be required for maintenance to be completed\.

The following table lists the maintenance window for each AWS Region that supports AWS DMS\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_ReplicationInstance.MaintenanceWindow.html)

## Effect of maintenance on existing migration tasks<a name="CHAP_ReplicationInstance.MaintenanceWindow.Effect"></a>

When an AWS DMS migration task is running on an instance, the following events occur when a patch is applied:
+ If the tables in the migration task are in the replicating ongoing changes phase \(CDC\), AWS DMS pauses the task for a moment while the patch is applied\. The migration then continues from where it was interrupted when the patch was applied\.
+ If AWS DMS is migrating a table when the patch is applied, AWS DMS restarts the migration for the table\. 

## Changing the maintenance window setting<a name="CHAP_ReplicationInstance.MaintenanceWindow.Changing"></a>

You can change the maintenance window time frame using the AWS Management Console, the AWS CLI, or the AWS DMS API\.

### Changing the maintenance window setting using the console<a name="CHAP_ReplicationInstance.AdjustingTheMaintenanceWindow.CON"></a>

You can change the maintenance window time frame using the AWS Management Console\.

**To change the preferred maintenance window using the console**

1.  Sign in to the AWS Management Console and open the AWS DMS console at [https://console\.aws\.amazon\.com/dms/v2/](https://console.aws.amazon.com/dms/v2/)\. 

1. In the navigation pane, choose **Replication instances**\.

1. Choose the replication instance you want to modify and choose **Modify**\.

1. Expand the **Maintenance** tab and choose a date and time for your maintenance window\.

1. Choose **Apply changes immediately**\. 

1.  Choose **Modify**\.

### Changing the maintenance window setting using the CLI<a name="CHAP_ReplicationInstanceAdjustingTheMaintenanceWindow.CLI"></a>

To adjust the preferred maintenance window, use the AWS CLI [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) command with the following parameters\.
+ `--replication-instance-identifier`
+ `--preferred-maintenance-window`

**Example**  
The following AWS CLI example sets the maintenance window to Tuesdays from 4:00–4:30 a\.m\. UTC\.  

```
aws dms modify-replication-instance \
--replication-instance-identifier myrepinstance \
--preferred-maintenance-window Tue:04:00-Tue:04:30
```

### Changing the maintenance window setting using the API<a name="CHAP_ReplicationInstanceAdjustingTheMaintenanceWindow.API"></a>

To adjust the preferred maintenance window, use the AWS DMS API [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) action with the following parameters\.
+ `ReplicationInstanceIdentifier = myrepinstance`
+ `PreferredMaintenanceWindow = Tue:04:00-Tue:04:30`

**Example**  
The following code example sets the maintenance window to Tuesdays from 4:00–4:30 a\.m\. UTC\.  

```
 1. https://dms.us-west-2.amazonaws.com/
 2. ?Action=ModifyReplicationInstance
 3. &DBInstanceIdentifier=myrepinstance
 4. &PreferredMaintenanceWindow=Tue:04:00-Tue:04:30
 5. &SignatureMethod=HmacSHA256
 6. &SignatureVersion=4
 7. &Version=2014-09-01
 8. &X-Amz-Algorithm=AWS4-HMAC-SHA256
 9. &X-Amz-Credential=AKIADQKE4SARGYLE/20140425/us-east-1/dms/aws4_request
10. &X-Amz-Date=20140425T192732Z
11. &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
12. &X-Amz-Signature=1dc9dd716f4855e9bdf188c70f1cf9f6251b070b68b81103b59ec70c3e7854b3
```