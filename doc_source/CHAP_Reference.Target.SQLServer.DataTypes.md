# Target Data Types for Microsoft SQL Server<a name="CHAP_Reference.Target.SQLServer.DataTypes"></a>

The following table shows the Microsoft SQL Server target data types that are supported when using AWS DMS and the default mapping from AWS DMS data types\. For additional information about AWS DMS data types, see [Data Types for AWS Database Migration Service](CHAP_Reference.DataTypes.md)\.


|  AWS DMS Data Type  |  SQL Server Data Type  | 
| --- | --- | 
|  BOOLEAN  |  TINYINT  | 
|  BYTES  |  VARBINARY\(length\)  | 
|  DATE  |  For SQL Server 2008 and later, use DATE\. For earlier versions, if the scale is 3 or less use DATETIME\. In all other cases, use VARCHAR \(37\)\.  | 
|  TIME  |  For SQL Server 2008 and later, use DATETIME2 \(%d\)\. For earlier versions, if the scale is 3 or less use DATETIME\. In all other cases, use VARCHAR \(37\)\.  | 
|  DATETIME  |  For SQL Server 2008 and later, use DATETIME2 \(scale\)\.  For earlier versions, if the scale is 3 or less use DATETIME\. In all other cases, use VARCHAR \(37\)\.  | 
|  INT1  | SMALLINT | 
|  INT2  |  SMALLINT  | 
|  INT4  | INT | 
|  INT8  |  BIGINT  | 
|  NUMERIC  |  NUMBER \(p,s\)  | 
|  REAL4  |  REAL  | 
|  REAL8  | FLOAT | 
|  STRING  |  If the column is a date or time column, then do the following:  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Reference.Target.SQLServer.DataTypes.html) If the column is not a date or time column, use VARCHAR \(length\)\.  | 
|  UINT1  |  TINYINT  | 
|  UINT2  |  SMALLINT  | 
|  UINT4  |  INT  | 
|  UINT8  |  BIGINT  | 
|  WSTRING  |  NVARCHAR \(length\)  | 
|  BLOB  |  VARBINARY\(max\) IMAGE To use this data type with AWS DMS, you must enable the use of BLOBs for a specific task\. AWS DMS supports BLOB data types only in tables that include a primary key\.  | 
|  CLOB  |  VARCHAR\(max\) To use this data type with AWS DMS, you must enable the use of CLOBs for a specific task\. During CDC, AWS DMS supports CLOB data types only in tables that include a primary key\.  | 
|  NCLOB  |  NVARCHAR\(max\) To use this data type with AWS DMS, you must enable the use of NCLOBs for a specific task\. During CDC, AWS DMS supports NCLOB data types only in tables that include a primary key\.  | 