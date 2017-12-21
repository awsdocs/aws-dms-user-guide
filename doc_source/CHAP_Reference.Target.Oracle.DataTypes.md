# Target Data Types for Oracle<a name="CHAP_Reference.Target.Oracle.DataTypes"></a>

A target Oracle database used with AWS DMS supports most Oracle data types\. The following table shows the Oracle target data types that are supported when using AWS DMS and the default mapping from AWS DMS data types\. For more information about how to view the data type that is mapped from the source, see the section for the source you are using\.


|  AWS DMS Data Type  |  Oracle Data Type  | 
| --- | --- | 
|  BOOLEAN  |  NUMBER \(1\)  | 
|  BYTES  |  RAW \(length\)  | 
|  DATE  |  DATETIME  | 
|  TIME  | TIMESTAMP \(0\) | 
|  DATETIME  |  TIMESTAMP \(scale\)  | 
|  INT1  | NUMBER \(3\) | 
|  INT2  |  NUMBER \(5\)  | 
|  INT4  | NUMBER \(10\) | 
|  INT8  |  NUMBER \(19\)  | 
|  NUMERIC  |  NUMBER \(p,s\)  | 
|  REAL4  |  FLOAT  | 
|  REAL8  | FLOAT | 
|  STRING  |  With date indication: DATE  With time indication: TIMESTAMP  With timestamp indication: TIMESTAMP  With timestamp\_with\_timezone indication: TIMESTAMP WITH TIMEZONE  With timestamp\_with\_local\_timezone indication: TIMESTAMP WITH LOCAL TIMEZONE With interval\_year\_to\_month indication: INTERVAL YEAR TO MONTH  With interval\_day\_to\_second indication: INTERVAL DAY TO SECOND  If length > 4000: CLOB In all other cases: VARCHAR2 \(length\)  | 
|  UINT1  |  NUMBER \(3\)  | 
|  UINT2  |  NUMBER \(5\)  | 
|  UINT4  |  NUMBER \(10\)  | 
|  UINT8  |  NUMBER \(19\)  | 
|  WSTRING  |  If length > 2000: NCLOB In all other cases: NVARCHAR2 \(length\)  | 
|  BLOB  |  BLOB To use this data type with AWS DMS, you must enable the use of BLOBs for a specific task\. BLOB data types are supported only in tables that include a primary key  | 
|  CLOB  |  CLOB To use this data type with AWS DMS, you must enable the use of CLOBs for a specific task\. During CDC, CLOB data types are supported only in tables that include a primary key\.  | 
|  NCLOB  |  NCLOB To use this data type with AWS DMS, you must enable the use of NCLOBs for a specific task\. During CDC, NCLOB data types are supported only in tables that include a primary key\.  | 
| XMLTYPE |  The XMLTYPE target data type is only relevant in Oracle\-to\-Oracle replication tasks\. When the source database is Oracle, the source data types are replicated "as is" to the Oracle target\. For example, an XMLTYPE data type on the source is created as an XMLTYPE data type on the target\.  | 