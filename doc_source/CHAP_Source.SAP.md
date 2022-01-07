# Using SAP ASE \(Sybase ASE\) as a source for AWS SCT<a name="CHAP_Source.SAP"></a>

You can use AWS SCT to convert schemas, database code objects, and application code from SAP \(Sybase\) Adaptive Server Enterprise \(ASE\) to the following targets: 
+ Amazon RDS for MySQL
+ Amazon Aurora MySQL\-Compatible Edition
+ Amazon RDS for MariaDB
+ Amazon RDS for PostgreSQL
+ Amazon Aurora PostgreSQL\-Compatible Edition

For more information, see the following sections:

**Topics**
+ [Database User and Privileges for SAP ASE \(Sybase ASE\) as a source database](#CHAP_Source.SAP.Permissions)
+ [Connecting to SAP ASE \(Sybase\) as a source](#CHAP_Source.SAP.Connecting)

## Database User and Privileges for SAP ASE \(Sybase ASE\) as a source database<a name="CHAP_Source.SAP.Permissions"></a>

To use an SAP ASE database as a source, you create a database user and grant permissions\. To do this, take the following steps\.

**Create and configure a database user**

1. Connect to the source database\.

1. Create a database user with the following commands\. Provide a password for the new user\.

   ```
   USE master
   CREATE LOGIN min_privs WITH PASSWORD <password>
   sp_adduser min_privs
   grant select on dbo.spt_values to min_privs
   grant select on asehostname to min_privs
   ```

1. For every database you are going to migrate, grant the following privileges\.

   ```
   USE <database_name>
   sp_adduser min_privs
   grant select on dbo.sysusers to min_privs
   grant select on dbo.sysobjects to min_privs
   grant select on dbo.sysindexes to min_privs
   grant select on dbo.syscolumns to min_privs
   grant select on dbo.sysreferences to min_privs
   grant select on dbo.syscomments to min_privs
   grant select on dbo.syspartitions to min_privs
   grant select on dbo.syspartitionkeys to min_privs
   grant select on dbo.sysconstraints to min_privs
   grant select on dbo.systypes to min_privs
   grant select on dbo.sysqueryplans to min_privs
   ```

## Connecting to SAP ASE \(Sybase\) as a source<a name="CHAP_Source.SAP.Connecting"></a>

Use the following procedure to connect to your SAP ASE source database with the AWS Schema Conversion Tool\. 

**To connect to a SAP ASE source database**

1. In the AWS Schema Conversion Tool, choose **Add source**\. 

1. Choose **SAP ASE**, then choose **Next**\.

   The **Add source** dialog box appears\.  
![\[SAP ASE connection information\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/images/SCT-SAP-connect.png)

1. Provide the SAP ASE source database connection information\. Use the instructions in the following table\.   
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_Source.SAP.html)

1. Choose **Test Connection** to verify that AWS SCT can connect to your source database\. 

1. Choose **Connect** to connect to your source database\.