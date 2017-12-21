# Target Data Types for MySQL<a name="CHAP_Reference.Target.MySQL.DataTypes"></a>

The following table shows the MySQL database target data types that are supported when using AWS DMS and the default mapping from AWS DMS data types\.

For additional information about AWS DMS data types, see [Data Types for AWS Database Migration Service](CHAP_Reference.DataTypes.md)\.


|  AWS DMS Data Types  |  MySQL Data Types  | 
| --- | --- | 
|  BOOLEAN  |  BOOLEAN  | 
|  BYTES  |  If the length is from 1 through 65,535, then use VARBINARY \(length\)\.  If the length is from 65,536 through 2,147,483,647, then use LONGLOB\.  | 
|  DATE  |  DATE  | 
|  TIME  |  TIME  | 
|  TIMESTAMP  |  "If scale is => 0 and =< 6, then: DATETIME \(Scale\) If scale is => 7 and =< 9, then: VARCHAR \(37\)"  | 
|  INT1  |  TINYINT  | 
|  INT2  |  SMALLINT  | 
|  INT4  |  INTEGER  | 
|  INT8  |  BIGINT  | 
|  NUMERIC  |  DECIMAL \(p,s\)  | 
|  REAL4  |  FLOAT  | 
|  REAL8  |  DOUBLE PRECISION  | 
|  STRING  |  If the length is from 1 through 21,845, then use VARCHAR \(length\)\. If the length is from 21,846 through 2,147,483,647, then use LONGTEXT\.  | 
|  UINT1  |  UNSIGNED TINYINT  | 
|  UINT2  |  UNSIGNED SMALLINT  | 
|  UINT4  |  UNSIGNED INTEGER  | 
|  UINT8  |  UNSIGNED BIGINT  | 
|  WSTRING  |  If the length is from 1 through 32,767, then use VARCHAR \(length\)\.  If the length is from 32,768 through 2,147,483,647, then use LONGTEXT\.  | 
|  BLOB  |  If the length is from 1 through 65,535, then use BLOB\. If the length is from 65,536 through 2,147,483,647, then use LONGBLOB\. If the length is 0, then use LONGBLOB \(full LOB support\)\.  | 
|  NCLOB  |  If the length is from 1 through 65,535, then use TEXT\. If the length is from 65,536 through 2,147,483,647, then use LONGTEXT with ucs2 for CHARACTER SET\. If the length is 0, then use LONGTEXT \(full LOB support\) with ucs2 for CHARACTER SET\.  | 
|  CLOB  |  If the length is from 1 through 65535, then use TEXT\. If the length is from 65536 through 2147483647, then use LONGTEXT\. If the length is 0, then use LONGTEXT \(full LOB support\)\.  | 