# Reloading tables during a task<a name="CHAP_Tasks.ReloadTables"></a>

While a task is running, you can reload a target database table using data from the source\. You might want to reload a table if, during the task, an error occurs or data changes due to partition operations \(for example, when using Oracle\)\. You can reload up to 10 tables from a task\.

Reloading tables does not stop the task\.

To reload a table, the following conditions must apply:
+ The task must be running\.
+ The migration method for the task must be either full load or full load with CDC\.
+ Duplicate tables aren't allowed\.
+ AWS DMS retains the previously read table definition and doesn't recreate it during the reload operation\. Any DDL statements such as ALTER TABLE ADD COLUMN or DROP COLUMN that are made to the table before the table is reloaded can cause the reload operation to fail\. 

## AWS Management Console<a name="CHAP_Tasks.ReloadTables.CON"></a>

**To reload a table using the AWS DMS console**

1. Sign in to the AWS Management Console and open the AWS DMS console at [https://console\.aws\.amazon\.com/dms/v2/](https://console.aws.amazon.com/dms/v2/)\. 

   If you are signed in as an IAM user, make sure that you have the appropriate permissions to access AWS DMS\. For more information about the permissions required, see [IAM permissions needed to use AWS DMS](security-iam.md#CHAP_Security.IAMPermissions)\.

1. Choose **Tasks** from the navigation pane\. 

1. Choose the running task that has the table you want to reload\. 

1. Choose the **Table Statistics** tab\.  
![\[AWS DMS monitoring\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-reloading1.png)

1. Choose the table you want to reload\. If the task is no longer running, you can't reload the table\.

1. Choose **Reload table data**\.

When AWS DMS is preparing to reload a table, the console changes the table status to **Table is being reloaded**\.