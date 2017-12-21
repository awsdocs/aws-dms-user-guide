# Reloading Tables During a Task<a name="CHAP_Tasks.ReloadTables"></a>

While a task is running, you can reload a target database table using data from the source\. You may want to reload a table if, during the task, an error occurs or data changes due to partition operations \(for example, when using Oracle\)\. You can reload up to 10 tables from a task\.

To reload a table, the following conditions must apply:

+ The task must be running\.

+ The migration method for the task must be either Full Load or Full Load with CDC\.

+ Duplicate tables are not allowed\.

## AWS Management Console<a name="CHAP_Tasks.ReloadTables.CON"></a>

**To reload a table using the AWS DMS console**

1. Sign in to the AWS Management Console and choose AWS DMS\. Note that if you are signed in as an AWS Identity and Access Management \(IAM\) user, you must have the appropriate permissions to access AWS DMS\. For more information on the permissions required, see [IAM Permissions Needed to Use AWS DMS](CHAP_Security.IAMPermissions.md)\.

1. Select **Tasks** from the navigation pane\. 

1. Select the running task that has the table you want to reload\. 

1. Click the **Table Statistics** tab\.  
![\[AWS DMS monitoring\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-reloading1.png)

1. Select the table you want to reload\. Note that if the task is no longer running, you will not be able to reload the table\.

1. Choose **Drop and reload table data**\.

When AWS DMS is preparing to reload a table, the console changes the table status to **The table is being reloaded**\.

![\[AWS DMS monitoring\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-reloading2.png)