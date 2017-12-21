# Target Data Types for SAP ASE<a name="CHAP_Reference.Target.SAP.DataTypes"></a>

The following table shows the SAP ASE database target data types that are supported when using AWS DMS and the default mapping from AWS DMS data types\.

For additional information about AWS DMS data types, see [Data Types for AWS Database Migration Service](CHAP_Reference.DataTypes.md)\.


|  AWS DMS Data Types  |  SAP ASE Data Types  | 
| --- | --- | 
| BOOLEAN | BIT | 
| BYTES | VARBINARY \(Length\) | 
| DATE | DATE | 
| TIME | TIME | 
| TIMESTAMP |  If scale is => 0 and =< 6, then: BIGDATETIME  If scale is => 7 and =< 9, then: VARCHAR \(37\)  | 
| INT1 | TINYINT | 
| INT2 | SMALLINT | 
| INT4 | INTEGER | 
| INT8 | BIGINT | 
| NUMERIC | NUMERIC \(p,s\) | 
| REAL4 | REAL | 
| REAL8 | DOUBLE PRECISION | 
| STRING | VARCHAR \(Length\) | 
| UINT1 | TINYINT | 
| UINT2 | UNSIGNED SMALLINT | 
| UINT4 | UNSIGNED INTEGER | 
| UINT8 | UNSIGNED BIGINT | 
| WSTRING | VARCHAR \(Length\) | 
| BLOB | IMAGE | 
| CLOB | UNITEXT | 
| NCLOB | TEXT | 

AWS DMS does not support tables that include fields with the following data types\. Replicated columns with these data types will show as null\. 

+ User\-defined type \(UDT\)