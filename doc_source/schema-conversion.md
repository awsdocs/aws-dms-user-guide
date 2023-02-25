# Using DMS Schema Conversion<a name="schema-conversion"></a>

DMS Schema Conversion converts your existing database schemas and a majority of the database code objects to a format compatible with the target database\.

DMS Schema Conversion automates much of the process of converting your online transaction processing \(OLTP\) database schemas to Amazon RDS for MySQL or RDS for PostgreSQL\. The source and target database engines contain many different features and capabilities, and DMS Schema Conversion attempts to create an equivalent schema wherever possible\. For database objects where direct conversion isn't possible, DMS Schema Conversion provides a list of actions for you to take\.

To convert your database schema, use the following process:
+ Before you convert your database schemas, set up transformation rules that change the names of your database objects during conversion\.
+ Create a database migration assessment report to estimate the complexity of the migration\. This report provides details about the schema elements that DMS Schema Conversion can't convert automatically\.
+ Convert your source database storage and code objects\. DMS Schema Conversion creates a local version of the converted database objects\. You can access these converted objects in your migration project\. 
+ Save the converted code to SQL files to review, edit, or address conversion action items\. Optionally, apply the converted code directly to your target database\. 

To convert data warehouse schemas, use the desktop AWS Schema Conversion Tool\. For more information, see [Converting data warehouse schemas to Amazon Redshift](https://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_Converting.DW.html) in the *AWS Schema Conversion Tool User Guide*\.

**Topics**
+ [Setting up transformation rules in DMS Schema Conversion](schema-conversion-transformation-rules.md)
+ [Converting database schemas in DMS Schema Conversion](schema-conversion-convert.md)
+ [Specifying schema conversion settings for migration projects](schema-conversion-settings.md)
+ [Refreshing your database schemas in DMS Schema Conversion](schema-conversion-refresh.md)
+ [Saving and applying your converted code in DMS Schema Conversion](schema-conversion-save-apply.md)