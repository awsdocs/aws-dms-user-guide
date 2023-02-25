# Specifying schema conversion settings for migration projects<a name="schema-conversion-settings"></a>

After you create a migration project, you can specify conversion settings in DMS Schema Conversion\. Configuring your schema conversion settings improves the performance of the converted code\.

**To edit conversion settings**

1. Sign in to the AWS Management Console and open the AWS DMS console at [https://console\.aws\.amazon\.com/dms/v2/](https://console.aws.amazon.com/dms/v2/)\.

1. Choose **Migration projects**\. The **Migration projects** page opens\.

1. Choose your migration project\. Choose **Schema conversion**, then **Launch schema conversion**\.

1. Choose **Settings**\. The **Settings** page opens\.

1. In the **Conversion** section, change the settings\.

1. Choose **Apply**, and then choose **Schema conversion**\.

For all conversion pairs, you can limit the number of comments with action items in the converted code\. To limit the number of comments in the converted code, open the conversion settings in your migration project\.

For the **Comments in converted SQL code**, choose the severity level of action items\. DMS Schema Conversion adds comments in the converted code for action items of the selected severity and higher\. For example, to minimize the number of comments in your converted code, choose **Errors only**\.

To include comments for all action items in your converted code, choose **All messages**\.

Other conversion settings are different for each pair of source and target databases\.

**Topics**
+ [Oracle to MySQL conversion settings](schema-conversion-oracle-mysql.md)
+ [Oracle to PostgreSQL conversion settings](schema-conversion-oracle-postgresql.md)
+ [SQL Server to MySQL conversion settings](schema-conversion-sql-server-mysql.md)
+ [SQL Server to PostgreSQL conversion settings](schema-conversion-sql-server-postgresql.md)