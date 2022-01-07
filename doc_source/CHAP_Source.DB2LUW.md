# Using IBM Db2 LUW as a source for AWS SCT<a name="CHAP_Source.DB2LUW"></a>

You can use AWS SCT to convert schemas, code objects, and application code from IBM Db2 LUW to the following targets\. AWS SCT supports as a source Db2 LUW versions 9\.1, 9\.5, 9\.7, 10\.1, 10\.5, 11\.1, and 11\.5\.
+ Amazon RDS for MySQL
+ Amazon Aurora MySQL\-Compatible Edition
+ Amazon RDS for PostgreSQL
+ Amazon Aurora PostgreSQL\-Compatible Edition
+ Amazon RDS for MariaDB

## Permissions needed when using Db2 LUW as a source<a name="CHAP_Source.DB2LUW.Permissions"></a>

The privileges needed to connect to a DB2LUW database, to check available privileges and read schema metadata for a source are listed following: 
+ Privilege needed to establish a connection:
  + CONNECT ON DATABASE
+ Privilege needed to run SQL statements:
  + EXECUTE ON PACKAGE NULLID\.SYSSH200
+ Privileges needed to get instance\-level information:
  + EXECUTE ON FUNCTION SYSPROC\.ENV\_GET\_INST\_INFO
  + SELECT ON SYSIBMADM\.ENV\_INST\_INFO
  + SELECT ON SYSIBMADM\.ENV\_SYS\_INFO
+ Privileges needed to check privileges granted through roles, groups, and authorities:
  + EXECUTE ON FUNCTION SYSPROC\.AUTH\_LIST\_AUTHORITIES\_FOR\_AUTHID
  + EXECUTE ON FUNCTION SYSPROC\.AUTH\_LIST\_GROUPS\_FOR\_AUTHID
  + EXECUTE ON FUNCTION SYSPROC\.AUTH\_LIST\_ROLES\_FOR\_AUTHID
  + SELECT ON SYSIBMADM\.PRIVILEGES
+ Privileges needed on system catalogs and tables:
  + SELECT ON SYSCAT\.ATTRIBUTES
  + SELECT ON SYSCAT\.CHECKS
  + SELECT ON SYSCAT\.COLIDENTATTRIBUTES
  + SELECT ON SYSCAT\.COLUMNS
  + SELECT ON SYSCAT\.DATAPARTITIONEXPRESSION
  + SELECT ON SYSCAT\.DATAPARTITIONS
  + SELECT ON SYSCAT\.DATATYPEDEP
  + SELECT ON SYSCAT\.DATATYPES
  + SELECT ON SYSCAT\.HIERARCHIES
  + SELECT ON SYSCAT\.INDEXCOLUSE
  + SELECT ON SYSCAT\.INDEXES
  + SELECT ON SYSCAT\.INDEXPARTITIONS
  + SELECT ON SYSCAT\.KEYCOLUSE
  + SELECT ON SYSCAT\.MODULEOBJECTS
  + SELECT ON SYSCAT\.MODULES
  + SELECT ON SYSCAT\.NICKNAMES
  + SELECT ON SYSCAT\.PERIODS
  + SELECT ON SYSCAT\.REFERENCES
  + SELECT ON SYSCAT\.ROUTINEPARMS
  + SELECT ON SYSCAT\.ROUTINES
  + SELECT ON SYSCAT\.ROWFIELDS
  + SELECT ON SYSCAT\.SCHEMATA
  + SELECT ON SYSCAT\.SEQUENCES
  + SELECT ON SYSCAT\.TABCONST
  + SELECT ON SYSCAT\.TABLES
  + SELECT ON SYSCAT\.TRIGGERS
  + SELECT ON SYSCAT\.VARIABLEDEP
  + SELECT ON SYSCAT\.VARIABLES
  + SELECT ON SYSCAT\.VIEWS
  + SELECT ON SYSIBM\.SYSDUMMY1
+  To run SQL statements, the user account needs a privilege to use at least one of the workloads enabled in the database\. If none of the workloads are assigned to the user, ensure that the default user workload is accessible to the user:
  + USAGE ON WORKLOAD SYSDEFAULTUSERWORKLOAD

To run queries, you need to create system temporary tablespaces with page size 8K, 16K, and 32K, if they don't exist\. To create the temporary tablespaces, run the following scripts\.

```
CREATE BUFFERPOOL BP8K
  IMMEDIATE
  ALL DBPARTITIONNUMS
  SIZE AUTOMATIC
  NUMBLOCKPAGES 0
  PAGESIZE 8K;
  
CREATE SYSTEM TEMPORARY TABLESPACE TS_SYS_TEMP_8K 
  PAGESIZE 8192 
  BUFFERPOOL BP8K;
  
CREATE BUFFERPOOL BP16K
  IMMEDIATE
  ALL DBPARTITIONNUMS
  SIZE AUTOMATIC
  NUMBLOCKPAGES 0
  PAGESIZE 16K;
  
CREATE SYSTEM TEMPORARY TABLESPACE TS_SYS_TEMP_BP16K 
  PAGESIZE 16384 
  BUFFERPOOL BP16K;  
  
CREATE BUFFERPOOL BP32K
  IMMEDIATE
  ALL DBPARTITIONNUMS
  SIZE AUTOMATIC
  NUMBLOCKPAGES 0
  PAGESIZE 32K;
  
CREATE SYSTEM TEMPORARY TABLESPACE TS_SYS_TEMP_BP32K 
  PAGESIZE 32768 
  BUFFERPOOL BP32K;
```

## Connecting to Db2 LUW as a source<a name="CHAP_Source.DB2LUW.Connecting"></a>

Use the following procedure to connect to your Db2 LUW source database with the AWS Schema Conversion Tool\. 

**To connect to a Db2 LUW source database**

1. In the AWS Schema Conversion Tool, choose **Add source**\. 

1. Choose **DB2 LUW**, then choose **Next**\. 

   The **Add source** dialog box appears\.  
![\[Db2 LUW connection information\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/images/source-db2luw.png)

1. Provide the Db2 LUW source database connection information\. Use the instructions in the following table\.   
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_Source.DB2LUW.html)

1. Choose **Test Connection** to verify that AWS SCT can connect to your source database\. 

1. Choose **Connect** to connect to your source database\.