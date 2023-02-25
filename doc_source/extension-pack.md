# Using extension packs in DMS Schema Conversion<a name="extension-pack"></a>

An *extension pack* in DMS Schema Conversion is an add\-on module that emulates source database functions that aren't supported in the target database\. Use an extension pack to make sure that the converted code produces the same results as the source code\. Before you can install an extension pack, convert your database schemas\.

Each extension pack includes a database schema\. This schema includes SQL functions, procedures, tables, and views for emulating specific online transaction processing \(OLTP\) objects or unsupported built\-in functions from the source database\.

When you convert your source database, DMS Schema Conversion adds an additional schema to your target database\. This schema implements SQL system functions of the source database that are required to run your converted code on your target database\. This additional schema is called the extension pack schema\.

The extension pack schema is named according to your source database as follows:
+ Microsoft SQL Server – `aws_sqlserver_ext`
+ Oracle – `aws_oracle_ext`

You can apply extension packs in two ways:
+ DMS Schema Conversion can automatically apply an extension pack when you apply your converted code\. DMS Schema Conversion applies the extension pack before it applies all other schema objects\.
+ You can apply an extension pack manually\. To do so, choose the extension pack schema in your target database tree, and then choose **Apply**, then **Apply extension pack**\.