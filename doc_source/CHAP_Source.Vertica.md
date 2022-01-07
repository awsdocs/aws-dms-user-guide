# Using Vertica as a source for AWS SCT<a name="CHAP_Source.Vertica"></a>

You can use AWS SCT to convert schemas, code objects, and application code from Vertica to Amazon Redshift\.

## Privileges for Vertica as a source<a name="CHAP_Source.Vertica.Permissions"></a>

The privileges required for Vertica as a source are listed following:
+ USAGE ON SCHEMA *<schema\_name>* 
+ USAGE ON SCHEMA PUBLIC 
+ SELECT ON ALL TABLES IN SCHEMA *<schema\_name>* 
+ SELECT ON ALL SEQUENCES IN SCHEMA *<schema\_name>* 
+ EXECUTE ON ALL FUNCTIONS IN SCHEMA *<schema\_name>* 
+ EXECUTE ON PROCEDURE *<schema\_name\.procedure\_name\(procedure\_signature\)>* 

In the example preceding, replace placeholders as following:
+ Replace `schema_name` with the name of the source schema\.
+ Replace `procedure_name` with the name of a source procedure\. Repeat the grant for each procedure that you are converting\. 
+ Replace `procedure_signature` with the comma\-delimited list of procedure argument types\.

## Connecting to Vertica as a source<a name="CHAP_Source.Vertica.Connecting"></a>

Use the following procedure to connect to your Vertica source database with the AWS Schema Conversion Tool\. 

**To connect to a Vertica source database**

1. In the AWS Schema Conversion Tool, choose **Add source**\. 

1. Choose **Vertica**, then choose **Next**\.

   The **Add source** dialog box appears\.  
![\[Vertica connection information\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/images/source-vertica.png)

1. Provide the Vertica source database connection information\. Use the instructions in the following table\.     
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_Source.Vertica.html)

1. Choose **Test Connection** to verify that AWS SCT can connect to your source database\. 

1. Choose **Connect** to connect to your source database\.