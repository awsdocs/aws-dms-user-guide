# Using an IBM Db2 for Linux, Unix, and Windows Database \(Db2 LUW\) as a Source for AWS DMS<a name="CHAP_Source.DB2"></a>

You can migrate data from an IBM Db2 for Linux, Unix, and Windows \(Db2 LUW\) database to any supported target database using AWS DMS \(AWS DMS\)\. AWS DMS supports as a migration source the following Db2 LUW versions:
+ Version 9\.7, all Fix Packs are supported\.
+ Version 10\.1, all Fix Packs are supported\.
+ Version 10\.5, all Fix Packs except for Fix Pack 5 are supported\.

You can use SSL to encrypt connections between your Db2 LUW endpoint and the replication instance\. You must be using AWS DMS engine version 2\.4\.2 or higher to use SSL\. For more information on using SSL with a Db2 LUW endpoint, see [Using SSL With AWS Database Migration Service](CHAP_Security.SSL.md)\.

## Prerequisites When Using Db2 LUW as a Source for AWS DMS<a name="CHAP_Source.DB2.Prerequisites"></a>

The following prerequisites are required before you can use an Db2 LUW database as a source\.

To enable ongoing replication, also called change data capture \(CDC\), you must do the following
+ The database must be set to be recoverable\. To capture changes, AWS DMS requires that the database is configured to be recoverable\. A database is recoverable if either or both of the database configuration parameters LOGARCHMETH1 and LOGARCHMETH2 are set to ON\.
+ The user account must be granted the following permissions:

  SYSADM or DBADM

  DATAACCESS

## Limitations When Using Db2 LUW as a Source for AWS DMS<a name="CHAP_Source.DB2.Limitations"></a>

Clustered databases are not supported\. Note, however, that you can define a separate Db2 LUW for each of the endpoints of a cluster\. See the IBM Db2 LUW documentation for more information\.

When using ongoing replication \(CDC\), the following limitations apply:
+ When truncating a table with multiple partitions, the number of DDL events shown in the AWS DMS console will be equal to the number of partitions\. This is because Db2 LUW records a separate DDL for each partition\.
+ The following DDL actions are not supported on partitioned tables:
  + ALTER TABLE ADD PARTITION
  + ALTER TABLE DETACH PARTITION
  + ALTER TABLE ATTACH PARTITION
+ The DECFLOAT data type is not supported\. Consequently, changes to DECFLOAT columns are ignored during ongoing replication\.
+ The RENAME COLUMN statement is not supported\.
+ When performing updates to MDC \(Multi\-Dimensional Clustering\) tables, each update is shown in the AWS DMS console as INSERT \+ DELETE\.
+ When the task setting **Include LOB columns in replication** is disabled, any table that has LOB columns is suspended during ongoing replication\.
+ When the Audit table option is enabled, the first timestamp record in the audit table will be NULL\.
+ When the Change table option is enabled, the first timestamp record in the table will be zero \(i\.e\. 1970\-01\-01 00:00:00\.000000\)\.
+ For Db2 LUW versions 10\.5 and higher: Variable\-length string columns with data that is stored out\-of\-row is ignored\. Note that this limitation is only applicable to tables created with extended row size\.

## Extra Connection Attributes When Using Db2 LUW as a Source for AWS DMS<a name="CHAP_Source.DB2.ConnectionAttrib"></a>

You can use extra connection attributes to configure your Db2 LUW source\. You specify these settings when you create the source endpoint\. Multiple extra connection attribute settings should be separated by a semicolon\.

The following table shows the extra connection attributes you can use with Db2 LUW as a source:


| Name | Description | 
| --- | --- | 
|  `MaxKBytesPerRead`  |  Maximum number of bytes per read, as a NUMBER value\. The default is 64 KB\.  | 
|  `SetDataCaptureChanges`  |  Enables ongoing replication \(change data capture\), as a BOOLEAN value\. The default is true\.  | 

## Source Data Types for IBM Db2 LUW<a name="CHAP_Source.DB2.DataTypes"></a>

Data migration that uses Db2 LUW as a source for AWS DMS supports most Db2 LUW data types\. The following table shows the Db2 LUW source data types that are supported when using AWS DMS and the default mapping from AWS DMS data types\. For more information about Db2 LUW data types, see [ the Db2 LUW documentation](https://www.ibm.com/support/knowledgecenter/SSEPGG_10.5.0/com.ibm.db2.luw.sql.ref.doc/doc/r0008483.html)\.

For information on how to view the data type that is mapped in the target, see the section for the target endpoint you are using\.

For additional information about AWS DMS data types, see [Data Types for AWS Database Migration Service](CHAP_Reference.DataTypes.md)\.


|  Db2 LUW Data Types  |  AWS DMS Data Types  | 
| --- | --- | 
|  INTEGER  |  INT4  | 
|  SMALLINT  |  INT2  | 
|  BIGINT  |  INT8  | 
|  DECIMAL \(p,s\)  |  NUMERIC \(p,s\)  | 
|  FLOAT  |  REAL8  | 
|  DOUBLE  |  REAL8  | 
|  REAL  |  REAL4  | 
|  DECFLOAT \(p\)  |  If precision = 16, then: REAL8 If precision is = 34, then: STRING  | 
|  GRAPHIC  |  WSTRING n<=127  | 
|  VARGRAPHIC  |  WSTRING n<=16k double byte chars  | 
|  LONG VARGRAPHIC  |  CLOB  | 
|  CHAR \(n\)  |  STRING n<=255  | 
|  VARCHAR \(n\)  |  STRING n<=32k  | 
|  LONG VARCHAR \(n\)  |  CLOB n<=32k  | 
|  CHAR \(n\) FOR BIT DATA  |  BYTES  | 
|  VARCHAR \(n\) FOR BIT DATA  |  BYTES  | 
|  LONG VARCHAR FOR BIT DATA  |  BYTES  | 
|  DATE  |  DATE  | 
|  TIME  |  TIME  | 
|  TIMESTAMP  |  DATETIME  | 
|  BLOB  |  BLOB  | 
|  CLOB  |  CLOB Maximum size: 2 GB  | 
|  DBCLOB  |  CLOB Maximum size: 1 G double byte chars  | 
|  XML  |  CLOB  | 