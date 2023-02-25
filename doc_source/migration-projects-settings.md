# Specifying migration project settings in DMS Schema Conversion<a name="migration-projects-settings"></a>

After you create your migration project and launch schema conversion, you can specify migration project settings\. You can change conversion settings to improve the performance of converted code\. Also, you can customize your schema conversion view\.

Conversion settings depend on your source and target database platforms\. For more information, see [Data providers](data-providers.md)\.

To specify what schemas and databases you want to see in the source and target database panes, use the tree view settings\. You can hide empty schemas, empty databases, system databases, and user\-defined databases or schemas\.

**To hide databases and schemas in tree view**

1. Sign in to the AWS Management Console and open the AWS DMS console at [https://console\.aws\.amazon\.com/dms/v2/](https://console.aws.amazon.com/dms/v2/)\.

1. Choose **Migration projects**\. The **Migration projects** page opens\.

1. Choose your migration project, choose **Schema conversion**, then **Launch schema conversion**\.

1. Choose **Settings**\. The **Settings** page opens\.

1. In the **Tree view** section, do the following:
   + Choose **Hide empty schemas** to hide empty schemas\.
   + Choose **Hide empty databases** to hide empty databases\.
   + For **System databases or schemas**, choose system databases and schemas by name to hide them\.
   + For **User\-defined databases or schemas**, enter the names of user\-defined databases and schemas that you want to hide\. Choose **Add**\. The names are case\-insensitive\.

     To add multiple databases or schemas, use a comma to separate their names\. To add multiple objects with a similar name, use the percent \(%\) as a wildcard\. This wildcard replaces any number of any symbols in the database or schema name\.

   Repeat these steps for the **Source** and **Target** sections\.

1. Choose **Apply**, and then choose **Schema conversion**\.