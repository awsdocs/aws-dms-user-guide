# Source Data Types for Microsoft SQL Server<a name="CHAP_Reference.Source.SQLServer.DataTypes"></a>

Data migration that uses Microsoft SQL Server as a source for AWS DMS supports most SQL Server data types\. The following table shows the SQL Server source data types that are supported when using AWS DMS and the default mapping from AWS DMS data types\.

For information on how to view the data type that is mapped in the target, see the section for the target endpoint you are using\.

For additional information about AWS DMS data types, see [Data Types for AWS Database Migration Service](CHAP_Reference.DataTypes.md)\.


|  SQL Server Data Types  |  AWS DMS Data Types  | 
| --- | --- | 
|  BIGINT  |  INT8  | 
|  BIT  |  BOOLEAN  | 
|  DECIMAL  |  NUMERIC  | 
|  INT  |  INT4  | 
|  MONEY  |  NUMERIC  | 
|  NUMERIC \(p,s\)  |  NUMERIC   | 
|  SMALLINT  |  INT2  | 
|  SMALLMONEY  |  NUMERIC  | 
|  TINYINT  |  UINT1  | 
|  REAL  |  REAL4  | 
|  FLOAT  |  REAL8  | 
|  DATETIME  |  DATETIME  | 
|  DATETIME2 \(SQL Server 2008 and later\)  |  DATETIME  | 
|  SMALLDATETIME  |  DATETIME  | 
|  DATE  |  DATE  | 
|  TIME  |  TIME  | 
|  DATETIMEOFFSET  |  WSTRING  | 
|  CHAR  |  STRING  | 
|  VARCHAR  |  STRING  | 
|  VARCHAR \(max\)  |  CLOB TEXT To use this data type with AWS DMS, you must enable the use of CLOB data types for a specific task\. For SQL Server tables, AWS DMS updates LOB columns in the target even for UPDATE statements that don't change the value of the LOB column in SQL Server\. During CDC, AWS DMS supports CLOB data types only in tables that include a primary key\.  | 
|  NCHAR  |  WSTRING  | 
|  NVARCHAR \(length\)  |  WSTRING  | 
|  NVARCHAR \(max\)  |  NCLOB NTEXT To use this data type with AWS DMS, you must enable the use of NCLOB data types for a specific task\. For SQL Server tables, AWS DMS updates LOB columns in the target even for UPDATE statements that don't change the value of the LOB column in SQL Server\. During CDC, AWS DMS supports CLOB data types only in tables that include a primary key\.  | 
|  BINARY  |  BYTES  | 
|  VARBINARY  |  BYTES  | 
|  VARBINARY \(max\)  |  BLOB IMAGE For SQL Server tables, AWS DMS updates LOB columns in the target even for UPDATE statements that don't change the value of the LOB column in SQL Server\. To use this data type with AWS DMS, you must enable the use of BLOB data types for a specific task\. AWS DMS supports BLOB data types only in tables that include a primary key\.  | 
|  TIMESTAMP  |  BYTES  | 
|  UNIQUEIDENTIFIER  |  STRING  | 
|  HIERARCHYID   |  Use HIERARCHYID when replicating to a SQL Server target endpoint\. Use WSTRING \(250\) when replicating to all other target endpoints\.  | 
|  XML  |  NCLOB For SQL Server tables, AWS DMS updates LOB columns in the target even for UPDATE statements that don't change the value of the LOB column in SQL Server\. To use this data type with AWS DMS, you must enable the use of NCLOB data types for a specific task\. During CDC, AWS DMS supports NCLOB data types only in tables that include a primary key\.  | 
|  GEOMETRY  |  Use GEOMETRY when replicating to target endpoints that support this data type\. Use CLOB when replicating to target endpoints that don't support this data type\.  | 
|  GEOGRAPHY  |  Use GEOGRAPHY when replicating to target endpoints that support this data type\. Use CLOB when replicating to target endpoints that do not support this data type\.  | 

AWS DMS does not support tables that include fields with the following data types: 

+ CURSOR

+ SQL\_VARIANT

+ TABLE

**Note**  
User\-defined data types are supported according to their base type\. For example, a user\-defined data type based on DATETIME is handled as a DATETIME data type\.