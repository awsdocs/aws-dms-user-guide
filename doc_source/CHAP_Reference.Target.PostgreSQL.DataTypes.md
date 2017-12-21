# Target Data Types for PostgreSQL<a name="CHAP_Reference.Target.PostgreSQL.DataTypes"></a>

The PostgreSQL database endpoint for AWS DMS supports most PostgreSQL database data types\. The following table shows the PostgreSQL database target data types that are supported when using AWS DMS and the default mapping from AWS DMS data types\. Unsupported data types are listed following the table\.

For additional information about AWS DMS data types, see [Data Types for AWS Database Migration Service](CHAP_Reference.DataTypes.md)\.


|  AWS DMS Data Type  |  PostgreSQL Data Type  | 
| --- | --- | 
|  BOOL  |  BOOL  | 
|  BYTES  |  BYTEA  | 
|  DATE  |  DATE  | 
|  TIME  |  TIME  | 
|  TIMESTAMP  |  If the scale is from 0 through 6, then use TIMESTAMP\. If the scale is from 7 through 9, then use VARCHAR \(37\)\.  | 
|  INT1  |  SMALLINT  | 
|  INT2  |  SMALLINT  | 
|  INT4  |  INTEGER  | 
|  INT8  |  BIGINT  | 
|  NUMERIC   |  DECIMAL \(P,S\)  | 
|  REAL4  |  FLOAT4  | 
|  REAL8  |  FLOAT8  | 
|  STRING  |  If the length is from 1 through 21,845, then use VARCHAR \(length in bytes\)\.  If the length is 21,846 through 2,147,483,647, then use VARCHAR \(65535\)\.  | 
|  UINT1  |  SMALLINT  | 
|  UINT2  |  INTEGER  | 
|  UINT4  |  BIGINT  | 
|  UINT8  |  BIGINT  | 
|  WSTRING  |  If the length is from 1 through 21,845, then use VARCHAR \(length in bytes\)\.  If the length is 21,846 through 2,147,483,647, then use VARCHAR \(65535\)\.  | 
|  BCLOB  |  BYTEA  | 
|  NCLOB  |  TEXT  | 
|  CLOB  |  TEXT  | 

**Note**  
When replicating from a PostgreSQL source, AWS DMS creates the target table with the same data types for all columns, apart from columns with user\-defined data types\. In such cases, the data type is created as "character varying" in the target\.