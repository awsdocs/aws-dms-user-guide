# Using PostgreSQL as a source for AWS SCT<a name="CHAP_Source.PostgreSQL"></a>

You can use AWS SCT to convert schemas, database code objects, and application code from PostgreSQL to the following targets: 
+ Amazon RDS for MySQL
+ Amazon Aurora MySQL\-Compatible Edition
+ Amazon RDS for PostgreSQL
+ Amazon Aurora PostgreSQL\-Compatible Edition

For more information, see the following sections:

**Topics**
+ [Privileges for PostgreSQL as a source database](#CHAP_Source.PostgreSQL.Permissions)
+ [Connecting to PostgreSQL as a source](#CHAP_Source.PostgreSQL.Connecting)

## Privileges for PostgreSQL as a source database<a name="CHAP_Source.PostgreSQL.Permissions"></a>

The privileges required for PostgreSQL as a source are listed following: 
+ CONNECT ON DATABASE *<database\_name>* 
+ USAGE ON SCHEMA *<database\_name>* 
+ SELECT ON ALL TABLES IN SCHEMA *<database\_name>* 
+ SELECT ON ALL SEQUENCES IN SCHEMA *<database\_name>* 

## Connecting to PostgreSQL as a source<a name="CHAP_Source.PostgreSQL.Connecting"></a>

Use the following procedure to connect to your PostgreSQL source database with the AWS Schema Conversion Tool\. 

**To connect to a PostgreSQL source database**

1. In the AWS Schema Conversion Tool, choose **Add source**\. 

1. Choose **PostgreSQL**, then choose **Next**\. 

1. The **Add source** dialog box appears\.  
![\[PostgreSQL connection information\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/images/source-postgres.png)

1. Provide the PostgreSQL source database connection information\. Use the instructions in the following table\.   
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_Source.PostgreSQL.html)

1. Choose **Test Connection** to verify that AWS SCT can connect to your source database\. 

1. Choose **Connect** to connect to your source database\.