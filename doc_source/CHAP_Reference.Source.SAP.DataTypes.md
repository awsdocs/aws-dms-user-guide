# Source Data Types for SAP ASE<a name="CHAP_Reference.Source.SAP.DataTypes"></a>

Data migration that uses SAP ASE as a source for AWS DMS supports most SAP ASE data types\. The following table shows the SAP ASE source data types that are supported when using AWS DMS and the default mapping from AWS DMS data types\.

For information on how to view the data type that is mapped in the target, see the section for the target endpoint you are using\.

For additional information about AWS DMS data types, see [Data Types for AWS Database Migration Service](CHAP_Reference.DataTypes.md)\.


|  SAP ASE Data Types  |  AWS DMS Data Types  | 
| --- | --- | 
| BIGINT | INT8 | 
| BINARY | BYTES | 
| BIT | BOOLEAN | 
| CHAR | STRING | 
| DATE | DATE | 
| DATETIME | DATETIME | 
| DECIMAL | NUMERIC | 
| DOUBLE | REAL8 | 
| FLOAT | REAL8 | 
| IMAGE | BLOB | 
| INT | INT4 | 
| MONEY | NUMERIC | 
| NCHAR | WSTRING | 
| NUMERIC | NUMERIC | 
| NVARCHAR | WSTRING | 
| REAL | REAL4 | 
| SMALLDATETIME | DATETIME | 
| SMALLINT | INT2 | 
| SMALLMONEY | NUMERIC | 
| TEXT | CLOB | 
| TIME | TIME | 
| TINYINT | UINT1 | 
| UNICHAR | UNICODE CHARACTER | 
| UNITEXT | NCLOB | 
| UNIVARCHAR | UNICODE | 
| VARBINARY | BYTES | 
| VARCHAR | STRING | 

AWS DMS does not support tables that include fields with the following data types: 

+ User\-defined type \(UDT\)