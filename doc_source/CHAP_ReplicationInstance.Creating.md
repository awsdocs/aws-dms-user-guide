# Creating a replication instance<a name="CHAP_ReplicationInstance.Creating"></a>

Your first task in migrating a database is to create a replication instance\. This replication instance requires sufficient storage and processing power to perform the tasks that you assign and migrate data from your source database to the target database\. The required size of this instance varies depending on the amount of data you need to migrate and the tasks that you need the instance to perform\. For more information about replication instances, see [Working with an AWS DMS replication instance](CHAP_ReplicationInstance.md)\. 

**To create a replication instance by using the AWS console**

1. Choose **Replication instances** in the navigation pane of the AWS DMS console and then choose **Create replication instance**\.

1. On the **Create replication instance** page, specify your replication instance information\. The following table describes the settings you can make\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_ReplicationInstance.Creating.html)

1. Choose the **Advanced** tab to set values for network and encryption settings if you need them\. The following table describes the settings\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_ReplicationInstance.Creating.html)

1. Specify the **Maintenance** settings\. The following table describes the settings\. For more information about maintenance settings, see [Working with the AWS DMS maintenance window](CHAP_ReplicationInstance.MaintenanceWindow.md)\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_ReplicationInstance.Creating.html)

1. Choose **Create replication instance**\.