# Target Data Types for Amazon Redshift<a name="CHAP_Reference.Target.Redshift.DataTypes"></a>

The Amazon Redshift endpoint for AWS DMS supports most Amazon Redshift data types\. The following table shows the Amazon Redshift target data types that are supported when using AWS DMS and the default mapping from AWS DMS data types\.

For additional information about AWS DMS data types, see [Data Types for AWS Database Migration Service](CHAP_Reference.DataTypes.md)\.


| AWS DMS Data Types | Amazon Redshift Data Types | 
| --- | --- | 
| BOOLEAN | BOOL | 
| BYTES | VARCHAR \(Length\) | 
| DATE | DATE | 
| TIME | VARCHAR\(20\) | 
| DATETIME | If the scale is => 0 and =< 6, then:  TIMESTAMP \(s\)  If the scale is => 7 and =< 9, then:  VARCHAR \(37\) | 
| INT1 | INT2 | 
| INT2 | INT2 | 
| INT4 | INT4 | 
| INT8 | INT8 | 
| NUMERIC | If the scale is => 0 and =< 37, then:  NUMERIC \(p,s\)  If the scale is => 38 and =< 127, then:  VARCHAR \(Length\) | 
| REAL4 | FLOAT4 | 
| REAL8 | FLOAT8 | 
| STRING | If the length is => 1 and =< 65535, then:  VARCHAR \(Length in Bytes\)  If the length is => 65536 and =< 2147483647, then:  VARCHAR \(65535\) | 
| UINT1 | INT2 | 
| UINT2 | INT2 | 
| UINT4 | INT4 | 
| UINT8 | NUMERIC \(20,0\) | 
| WSTRING | If the length is => 1 and =< 65535, then:  NVARCHAR \(Length in Bytes\)  If the length is => 65536 and =< 2147483647, then:  NVARCHAR \(65535\) | 
| BLOB | VARCHAR \(Max LOB Size \*2\)  Note: The maximum LOB size cannot exceed 31 KB\. | 
| NCLOB | NVARCHAR \(Max LOB Size\)  Note: The maximum LOB size cannot exceed 63 KB\. | 
| CLOB | VARCHAR \(Max LOB Size\)  Note: The maximum LOB size cannot exceed 63 KB\. | 