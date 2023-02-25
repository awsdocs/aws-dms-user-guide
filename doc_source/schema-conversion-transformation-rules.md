# Setting up transformation rules in DMS Schema Conversion<a name="schema-conversion-transformation-rules"></a>

Before you convert your database schema with DMS Schema Conversion, you can set up transformation rules\. *Transformation rules* can do such things as change an object name to lowercase or uppercase, add or remove a prefix or suffix, and rename objects\. For example, suppose that you have a set of tables in your source schema named `test_TABLE_NAME`\. You can set up a rule that changes the prefix `test_` to the prefix `demo_` in the target schema\.

You can create transformation rules that perform the following tasks:
+ Add, remove, or replace a prefix
+ Add, remove, or replace a suffix
+ Change the data type of a column
+ Change the object name to lowercase or uppercase
+ Rename objects

You can create transformation rules for the following objects:
+ Schema 
+ Table 
+ Column 

## Creating transformation rules<a name="schema-conversion-transformation-rules-create"></a>

DMS Schema Conversion stores transformation rules as part of your migration project\. You can set up transformation rules when you create your migration project, or edit them later\. 

You can add multiple transformation rules in your project\. DMS Schema Conversion applies transformation rules during conversion in the same order as you added them\.

**To create transformation rules**

1. On the **Create migration project** page, choose **Add transformation rule**\. For more information, see [ Creating migration projects](migration-projects-create.md)\.

1. For **Rule target**, choose the type of database objects to which this rule applies\. 

1. For **Source schema**, choose **Enter a schema**\. Then, enter the names of your source schemas, tables, and columns to which this rule applies\. You can enter an exact name to select one object, or you can enter a pattern to select multiple objects\. Use the percent \(%\) as a wildcard to replace any number of any symbols in the database object name\. 

1. For **Action**, choose the task to perform\. 

1. Depending on the rule type, enter one or two additional values\. For example, to rename an object, enter the new name of the object\. To replace a prefix, enter the old prefix and the new prefix\.

1. Choose **Add transformation rule** to add another transformation rule\.

   After you are done adding rules, choose **Create migration project**\.

To duplicate an existing transformation rule, choose **Duplicate**\. To edit an existing transformation rule, choose the rule from the list\. To delete an existing transformation rule, choose **Remove**\. 

## Editing transformation rules<a name="schema-conversion-transformation-rules-edit"></a>

You can add new, remove, or edit existing transformation rules in your migration project\. Because DMS Schema Conversion applies the transformation rules during the launch of schema conversion, make sure that you close schema conversion and launch it again after you edit your rules\.

**To edit transformation rules**

1. Sign in to the AWS Management Console, and open the AWS DMS console at [https://console\.aws\.amazon\.com/dms/v2/](https://console.aws.amazon.com/dms/v2/)\.

1. Choose **Migration projects**, and then choose your migration project\.

1. Choose **Schema conversion**, and then choose **Close schema conversion**\.

1. After AWS DMS closes schema conversion, choose **Modify** to edit your migration project settings\.

1. For **Transformation rules**, choose one of the following actions:
   + Choose **Duplicate** to duplicate an existing transformation rule and add it in the end of the list\.
   + Choose **Remove** to remove an existing transformation rule\.
   + Choose the existing transformation rule to edit it\.

1. After you are done editing rules, choose **Save changes**\.

1. On the **Migration projects** page, choose your project from the list\. Choose **Schema conversion**, then choose **Launch schema conversion**\.