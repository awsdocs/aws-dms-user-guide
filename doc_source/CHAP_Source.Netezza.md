# Using Netezza as a source for AWS SCT<a name="CHAP_Source.Netezza"></a>

You can use AWS SCT to convert schemas, code objects, and application code from Netezza to Amazon Redshift\. 

## Privileges for Netezza as a source<a name="CHAP_Source.Netezza.Permissions"></a>

The privileges required for Netezza as a source are listed following: 
+ SELECT ON SYSTEM\.DEFINITION\_SCHEMA\.SYSTEM VIEW 
+ SELECT ON SYSTEM\.DEFINITION\_SCHEMA\.SYSTEM TABLE 
+ SELECT ON SYSTEM\.DEFINITION\_SCHEMA\.MANAGEMENT TABLE 
+ LIST ON *<database\_name>* 
+ LIST ON *<schema\_name>* 
+ LIST ON *<database\_name>*\.ALL\.TABLE 
+ LIST ON *<database\_name>*\.ALL\.EXTERNAL TABLE 
+ LIST ON *<database\_name>*\.ALL\.VIEW 
+ LIST ON *<database\_name>*\.ALL\.MATERIALIZED VIEW 
+ LIST ON *<database\_name>*\.ALL\.PROCEDURE 
+ LIST ON *<database\_name>*\.ALL\.SEQUENCE 
+ LIST ON *<database\_name>*\.ALL\.FUNCTION 
+ LIST ON *<database\_name>*\.ALL\.AGGREGATE 

In the example preceding, replace placeholders as following:
+ Replace `database_name` with the name of the source database\.
+ Replace `schema_name` with the name of the source schema\.

## Connecting to Netezza as a source<a name="CHAP_Source.Netezza.Connecting"></a>

Use the following procedure to connect to your Netezza source database with the AWS Schema Conversion Tool\. 

**To connect to a Netezza source database**

1. In the AWS Schema Conversion Tool, choose **Add source**\. 

1. Choose **Netezza**, then choose **Next**\. 

   The **Add source** dialog box appears\.  
![\[Netezza connection information\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/images/source-netezza.png)

1. Provide the Netezza source database connection information\. Use the instructions in the following table\.   
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_Source.Netezza.html)

1. Choose **Test Connection** to verify that AWS SCT can connect to your source database\. 

1. Choose **Connect** to connect to your source database\.