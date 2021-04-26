# Using IBM Db2 for Linux, Unix, and Windows database \(Db2 LUW\) as a source for AWS DMS<a name="CHAP_Source.DB2"></a>

You can migrate data from an IBM Db2 for Linux, Unix, and Windows \(Db2 LUW\) database to any supported target database using AWS Database Migration Service \(AWS DMS\)\. AWS DMS supports the following versions of Db2 LUW as migration sources:
+ Version 9\.7, with all Fix Packs supported
+ Version 10\.1, with all Fix Packs supported
+ Version 10\.5, with all Fix Packs supported except for Fix Pack 5
+ Version 11\.1, with all Fix Packs supported
+ Version 11\.5, with Fix Pack Zero supported

You can use Secure Sockets Layer \(SSL\) to encrypt connections between your Db2 LUW endpoint and the replication instance\. For more information on using SSL with a Db2 LUW endpoint, see [Using SSL with AWS Database Migration Service](CHAP_Security.md#CHAP_Security.SSL)\.

## Prerequisites when using Db2 LUW as a source for AWS DMS<a name="CHAP_Source.DB2.Prerequisites"></a>

The following prerequisites are required before you can use an Db2 LUW database as a source\.

To enable ongoing replication, also called change data capture \(CDC\), do the following:
+ Set the database to be recoverable, which AWS DMS requires to capture changes\. A database is recoverable if either or both of the database configuration parameters LOGARCHMETH1 and LOGARCHMETH2 are set to ON\.
+ Grant the user account the following permissions:

  SYSADM or DBADM

  DATAACCESS
+ When using IBM DB2 for LUW version 9\.7 as a source, set the extra connection attribute \(ECA\), `CurrentLSN` as follows:

  `CurrentLSN=LSN` where `LSN` specifies a log sequence number \(LSN\) where you want the replication to start\. Or, `CurrentLSN=scan`\.

## Limitations when using Db2 LUW as a source for AWS DMS<a name="CHAP_Source.DB2.Limitations"></a>

AWS DMS doesn't support clustered databases\. However, you can define a separate Db2 LUW for each of the endpoints of a cluster\. 

When using ongoing replication \(CDC\), the following limitations apply:
+ When a table with multiple partitions is truncated, the number of DDL events shown in the AWS DMS console is equal to the number of partitions\. This is because Db2 LUW records a separate DDL for each partition\.
+ The following DDL actions aren't supported on partitioned tables:
  + ALTER TABLE ADD PARTITION
  + ALTER TABLE DETACH PARTITION
  + ALTER TABLE ATTACH PARTITION
+ AWS DMS doesn't support an ongoing replication migration from a DB2 high availability disaster recovery \(HADR\) standby instance\. The standby is inaccessible\.
+ The DECFLOAT data type isn't supported\. Consequently, changes to DECFLOAT columns are ignored during ongoing replication\.
+ The RENAME COLUMN statement isn't supported\.
+ When performing updates to Multi\-Dimensional Clustering \(MDC\) tables, each update is shown in the AWS DMS console as INSERT \+ DELETE\.
+ When the task setting **Include LOB columns in replication** isn't enabled, any table that has LOB columns is suspended during ongoing replication\.
+ When the audit table option is enabled, the first timestamp record in the audit table is NULL\.
+ When the change table option is enabled, the first timestamp record in the table is zero \(1970\-01\-01 00:00:00\.000000\)\.
+ For Db2 LUW versions 10\.5 and higher, variable\-length string columns with data that is stored out\-of\-row are ignored\. This limitation only applies to tables created with extended row size\.

## Extra connection attributes when using Db2 LUW as a source for AWS DMS<a name="CHAP_Source.DB2.ConnectionAttrib"></a>

You can use extra connection attributes to configure your Db2 LUW source\. You specify these settings when you create the source endpoint\. If you have multiple connection attribute settings, separate them from each other by semicolons with no additional white space \(for example, `oneSetting;thenAnother`\)\.

The following table shows the extra connection attributes that you can use with Db2 LUW as a source\.


| Name | Description | 
| --- | --- | 
|  `CurrentLSN`  |  For ongoing replication \(CDC\), use `CurrentLSN` to specify a log sequence number \(LSN\) where you want the replication to start\.   | 
|  `MaxKBytesPerRead`  |  Maximum number of bytes per read, as a NUMBER value\. The default is 64 KB\.  | 
|  `SetDataCaptureChanges`  |  Enables ongoing replication \(CDC\) as a BOOLEAN value\. The default is true\.  | 

## Source data types for IBM Db2 LUW<a name="CHAP_Source.DB2.DataTypes"></a>

Data migration that uses Db2 LUW as a source for AWS DMS supports most Db2 LUW data types\. The following table shows the Db2 LUW source data types that are supported when using AWS DMS and the default mapping from AWS DMS data types\. For more information about Db2 LUW data types, see the [Db2 LUW documentation](https://www.ibm.com/support/knowledgecenter/SSEPGG_10.5.0/com.ibm.db2.luw.sql.ref.doc/doc/r0008483.html)\.

For information on how to view the data type that is mapped in the target, see the section for the target endpoint that you're using\.

For additional information about AWS DMS data types, see [Data types for AWS Database Migration Service](CHAP_Reference.DataTypes.md)\.


|  Db2 LUW data types  |  AWS DMS data types  | 
| --- | --- | 
|  INTEGER  |  INT4  | 
|  SMALLINT  |  INT2  | 
|  BIGINT  |  INT8  | 
|  DECIMAL \(p,s\)  |  NUMERIC \(p,s\)  | 
|  FLOAT  |  REAL8  | 
|  DOUBLE  |  REAL8  | 
|  REAL  |  REAL4  | 
|  DECFLOAT \(p\)  |  If precision is 16, then REAL8; if precision is 34, then STRING  | 
|  GRAPHIC \(n\)  |  WSTRING, for fixed\-length graphic strings of double byte chars with a length greater than 0 and less than or equal to 127  | 
|  VARGRAPHIC \(n\)  |  WSTRING, for varying\-length graphic strings with a length greater than 0 and less than or equal to16,352 double byte chars  | 
|  LONG VARGRAPHIC \(n\)  |  CLOB, for varying\-length graphic strings with a length greater than 0 and less than or equal to16,352 double byte chars  | 
|  CHARACTER \(n\)  |  STRING, for fixed\-length strings of double byte chars with a length greater than 0 and less than or equal to 255  | 
|  VARCHAR \(n\)  |  STRING, for varying\-length strings of double byte chars with a length greater than 0 and less than or equal to 32,704  | 
|  LONG VARCHAR \(n\)  |  CLOB, for varying\-length strings of double byte chars with a length greater than 0 and less than or equal to 32,704  | 
|  CHAR \(n\) FOR BIT DATA  |  BYTES  | 
|  VARCHAR \(n\) FOR BIT DATA  |  BYTES  | 
|  LONG VARCHAR FOR BIT DATA  |  BYTES  | 
|  DATE  |  DATE  | 
|  TIME  |  TIME  | 
|  TIMESTAMP  |  DATETIME  | 
|  BLOB \(n\)  |  BLOB Maximum length is 2,147,483,647 bytes  | 
|  CLOB \(n\)  |  CLOB Maximum length is 2,147,483,647 bytes  | 
|  DBCLOB \(n\)  |  CLOB Maximum length is 1,073,741,824 double byte chars  | 
|  XML  |  CLOB  | 