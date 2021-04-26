# Modifying a replication instance<a name="CHAP_ReplicationInstance.Modifying"></a>

You can modify the settings for a replication instance to, for example, change the instance class or to increase storage\. 

When you modify a replication instance, you can apply the changes immediately\. To apply changes immediately, choose the **Apply changes immediately** option in the AWS Management Console\. Or use the `--apply-immediately` parameter when calling the AWS CLI, or set the `ApplyImmediately` parameter to `true` when using the DMS API\. 

If you don't choose to apply changes immediately, the changes are put into the pending modifications queue\. During the next maintenance window, any pending changes in the queue are applied\. 

**Note**  
If you choose to apply changes immediately, any changes in the pending modifications queue are also applied\. If any of the pending modifications require downtime, choosing **Apply changes immediately** can cause unexpected downtime\. 

**To modify a replication instance by using the AWS console**

1. Sign in to the AWS Management Console and open the AWS DMS console at [https://console\.aws\.amazon\.com/dms/v2/](https://console.aws.amazon.com/dms/v2/)\.

1. In the navigation pane, choose **Replication instances**\.

1. Choose the replication instance you want to modify\. The following table describes the modifications you can make\.     
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_ReplicationInstance.Modifying.html)