# Saving and applying your converted code in DMS Schema Conversion<a name="schema-conversion-save-apply"></a>

After DMS Schema Conversion converts your source database objects, it doesn't immediately apply the converted code to your target database\. Instead, DMS Schema Conversion stores the converted code in your project until you are ready to apply it to your target database\. 

Before you apply the converted code, you can update your source database code and convert the updated objects again to address the existing action items\. For more information about items that DMS Schema Conversion can't convert automatically, see [Creating database migration assessment reports with DMS Schema Conversion](assessment-reports.md)\. For more information about refreshing your source database objects in migration project for DMS Schema Conversion, see [Refreshing your database schemas](schema-conversion-refresh.md)\.

Instead of applying the converted code directly to your database in DMS Schema Conversion, you can save the code to a file as a SQL script\. You can review these SQL scripts, edit them where necessary, and then manually apply these SQL scripts to your target database\.

## Saving your converted code to a SQL file<a name="schema-conversion-save"></a>

You can save your converted schema as SQL scripts in a text file\. You can modify the converted code to address action items that DMS Schema Conversion can't convert automatically\. You can then run your updated SQL scripts on your target database to apply the converted code to your target database\.

**To save your converted schema as SQL scripts**

1. Sign in to the AWS Management Console and open the AWS DMS console at [https://console\.aws\.amazon\.com/dms/v2/](https://console.aws.amazon.com/dms/v2/)\.

1. Choose **Migration projects**\. The **Migration projects** page opens\.

1. Choose your migration project, and then choose **Schema conversion**\.

1. Choose **Launch schema conversion**\. The **Schema conversion** page opens\.

1. In the right pane, choose the target database schema or select the converted objects that you want to save\. Make sure that DMS Schema Conversion highlights the parent node name in blue and activates the **Actions** menu for the target database\.

1. Choose **Save as SQL** for **Actions**\. The **Save** dialog box appears\. 

1. Choose **Save as SQL** to confirm your choice\. 

   DMS Schema Conversion creates an archive with SQL files and stores this archive in your Amazon S3 bucket\. 

1. \(Optional\) Change the S3 bucket for the archive by editing the schema conversion settings in your instance profile\.

1. Open the SQL scripts from your S3 bucket\.

## Applying your converted code<a name="schema-conversion-apply"></a>

When you are ready to apply your converted code to your target database, choose the database objects in the right pane of your project\. You can apply changes to an entire database schema or selected database schema objects\.

After you select the database objects, DMS Schema Conversion highlights the name of the selected node or the parent node in blue\. It then activates the **Actions** menu\. Choose **Apply changes** for **Actions**\. In the dialog box that appears, choose **Apply** to confirm your choice and apply the converted code to your target database\.

## Applying the extension pack schema<a name="schema-conversion-save-apply-extension-pack"></a>

When you apply your converted schema to your target database for the first time, DMS Schema Conversion might also apply the extension pack schema\. The extension pack schema emulates system functions of the source database that are required to run your converted code for your target database\. If your converted code uses the functions of the extension pack, make sure that you apply the extension pack schema\. 

To apply the extension pack to your target database manually, choose **Apply changes** for **Actions**\. In the dialog box that appears, choose **confirm** to apply the extension pack to your target database\.

We recommend that you don't modify the extension pack schema to avoid unexpected results in the converted code\.

For more information, see [Using extension packs in DMS Schema Conversion](extension-pack.md)\.