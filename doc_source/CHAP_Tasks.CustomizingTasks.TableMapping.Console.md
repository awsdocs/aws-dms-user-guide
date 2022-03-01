# Specifying table selection and transformations rules from the console<a name="CHAP_Tasks.CustomizingTasks.TableMapping.Console"></a>

You can use the AWS Management Console to perform table mapping, including specifying table selection and transformations\. On the console, use the **Where** section to specify the schema, table, and action \(include or exclude\)\. Use the **Filter** section to specify the column name in a table and the conditions that you want to apply to a replication task\. Together, these two actions create a selection rule\.

You can include transformations in a table mapping after you have specified at least one selection rule\. You can use transformations to rename a schema or table, add a prefix or suffix to a schema or table, or remove a table column\.

**Note**  
AWS DMS doesn't support more than one transformation rule per schema level or per table level\. However, AWS DMS does support more than one transformation rule per column level\.

The following procedure shows how to set up selection rules, based on a table called **Customers** in a schema called **EntertainmentAgencySample**\. 

**To specify a table selection, filter criteria, and transformations using the console**

1. Sign in to the AWS Management Console and open the AWS DMS console at [https://console\.aws\.amazon\.com/dms/v2/](https://console.aws.amazon.com/dms/v2/)\. 

   If you are signed in as an IAM user, make sure that you have the appropriate permissions to access AWS DMS\. For more information about the permissions required, see [IAM permissions needed to use AWS DMS](CHAP_Security.md#CHAP_Security.IAMPermissions)\.

1. On the **Dashboard** page, choose **Database migration tasks**\.

1. Choose **Create Task**\.

1. In the **Task configuration** section, enter the task information, including **Task identifier**, **Replication instance**, **Source database endpoint**, **Target database endpoint**, and **Migration type**\.   
![\[Schema and table selection\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-create-task-20.png)

1. In the **Table mapping** section, enter the schema name and table name\. You can use "%" as a wildcard value when specifying the schema name or the table name\. For information about other wildcards you can use, see [Wildcards](CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Wildcards.md)\. Specify the action to be taken, to include or exclude data defined by the filter\.   
![\[Schema and table selection\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-Tasks-selecttransfrm.png)

1. Specify filter information using the **Add column filter** and the **Add condition **links\.

   1. Choose **Add column filter** to specify a column and conditions\.

   1. Choose **Add condition ** to add additional conditions\.

    The following example shows a filter for the **Customers** table that includes **AgencyIDs** between **01** and **85**\.  
![\[Schema and table selection\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-Tasks-filter.png)

1. When you have created the selections you want, choose **Add new selection rule**\.

1. After you have created at least one selection rule, you can add a transformation to the task\. Choose **add transformation rule**\.  
![\[transformation rule\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-Tasks-transform1.png)

1. Choose the target that you want to transform, and enter the additional information requested\. The following example shows a transformation that deletes the **AgencyStatus** column from the **Customer** table\.  
![\[transformation rule\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-Tasks-transform2.png)

1. Choose **Add transformation rule**\.

1. Choose **Create task**\.

**Note**  
AWS DMS doesn't support more than one transformation rule per schema level or per table level\. However, AWS DMS does support more than one transformation rule per column level\.