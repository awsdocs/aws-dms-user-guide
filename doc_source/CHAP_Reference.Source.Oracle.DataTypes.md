# Source Data Types for Oracle<a name="CHAP_Reference.Source.Oracle.DataTypes"></a>

The Oracle endpoint for AWS DMS supports most Oracle data types\. The following table shows the Oracle source data types that are supported when using AWS DMS and the default mapping to AWS DMS data types\.

For additional information about AWS DMS data types, see [Data Types for AWS Database Migration Service](CHAP_Reference.DataTypes.md)\.


|  Oracle Data Type  |  AWS DMS Data Type  | 
| --- | --- | 
|  BINARY\_FLOAT  |  REAL4  | 
|  BINARY\_DOUBLE  |  REAL8  | 
|  BINARY  |  BYTES  | 
|  FLOAT \(P\)  |  If precision is less than or equal to 24, use REAL4\. If precision is greater than 24, use REAL8\.  | 
|  NUMBER \(P,S\)  |  When scale is less than 0, use REAL8  | 
|  NUMBER according to the "Expose number as" property in the Oracle source database settings\.  |  When scale is 0: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Reference.Source.Oracle.DataTypes.html) In all other cases, use REAL8\. | 
|  DATE  |  DATETIME  | 
|  INTERVAL\_YEAR TO MONTH  |  STRING \(with interval year\_to\_month indication\)  | 
|  INTERVAL\_DAY TO SECOND  |  STRING \(with interval day\_to\_second indication\)  | 
|  TIME  |  DATETIME  | 
|  TIMESTAMP  |  DATETIME  | 
|  TIMESTAMP WITH TIME ZONE  |  STRING \(with timestamp\_with\_timezone indication\)  | 
|  TIMESTAMP WITH LOCAL TIME ZONE  |  STRING \(with timestamp\_with\_local\_ timezone indication\)  | 
|  CHAR  |  STRING  | 
|  VARCHAR2  |  STRING  | 
|  NCHAR  |  WSTRING  | 
|  NVARCHAR2  |  WSTRING  | 
|  RAW  |  BYTES  | 
|  REAL  |  REAL8  | 
|  BLOB  |  BLOB To use this data type with AWS DMS, you must enable the use of BLOB data types for a specific task\. AWS DMS supports BLOB data types only in tables that include a primary key\.  | 
|  CLOB  |  CLOB To use this data type with AWS DMS, you must enable the use of CLOB data types for a specific task\. During change data capture \(CDC\), AWS DMS supports CLOB data types only in tables that include a primary key\.  | 
|  NCLOB  |  NCLOB To use this data type with AWS DMS, you must enable the use of NCLOB data types for a specific task\. During CDC, AWS DMS supports NCLOB data types only in tables that include a primary key\.  | 
|  LONG  |  CLOB The LONG data type is not supported in batch\-optimized apply mode \(TurboStream CDC mode\)\. To use this data type with AWS DMS, you must enable the use of LOBs for a specific task\. During CDC, AWS DMS supports LOB data types only in tables that have a primary key\.  | 
|  LONG RAW  |  BLOB The LONG RAW data type is not supported in batch\-optimized apply mode \(TurboStream CDC mode\)\. To use this data type with AWS DMS, you must enable the use of LOBs for a specific task\. During CDC, AWS DMS supports LOB data types only in tables that have a primary key\.  | 
|  XMLTYPE  |  CLOB Support for the XMLTYPE data type requires the full Oracle Client \(as opposed to the Oracle Instant Client\)\. When the target column is a CLOB, both full LOB mode and limited LOB mode are supported \(depending on the target\)\.  | 

Oracle tables used as a source with columns of the following data types are not supported and cannot be replicated\. Replicating columns with these data types result in a null column\.

+ BFILE

+ ROWID

+ REF

+ UROWID

+ Nested Table

+ User\-defined data types

+ ANYDATA

**Note**  
Virtual columns are not supported\.