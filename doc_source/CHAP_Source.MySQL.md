# Using MySQL as a source for AWS SCT<a name="CHAP_Source.MySQL"></a>

You can use AWS SCT to convert schemas, database code objects, and application code from MySQL to the following targets: 
+ Amazon RDS for PostgreSQL
+ Amazon Aurora PostgreSQL\-Compatible Edition
+ Amazon RDS for MySQL

For more information, see the following sections:

**Topics**
+ [Privileges for MySQL as a source database](#CHAP_Source.MySQL.Permissions)
+ [Connecting to MySQL as a source database](#CHAP_Source.MySQL.Connecting)

## Privileges for MySQL as a source database<a name="CHAP_Source.MySQL.Permissions"></a>

The privileges required for MySQL as a source are listed following: 
+ SELECT ON \*\.\* 
+ SHOW VIEW ON \*\.\* 

## Connecting to MySQL as a source database<a name="CHAP_Source.MySQL.Connecting"></a>

Use the following procedure to connect to your MySQL source database with the AWS Schema Conversion Tool\. 

**To connect to a MySQL source database**

1. In the AWS Schema Conversion Tool, choose **Add source**\. 

1. Choose **MySQL**, then choose **Next**\.

   The **Add source** dialog box appears\.  
![\[MySQL connection information\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/images/source-mysql.png)

1. Provide the MySQL source database connection information\. Use the instructions in the following table\.   
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_Source.MySQL.html)

1. Choose **Test Connection** to verify that AWS SCT can connect to your source database\. 

1. Choose **Connect** to connect to your source database\.