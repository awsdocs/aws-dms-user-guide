# Source Data Types for Amazon S3<a name="CHAP_Reference.Source.S3.DataTypes"></a>

Data migration that uses Amazon S3 as a source for AWS DMS will need to map data from S3 to AWS DMS data types\. For more information, see [Defining External Tables for S3 as a Source for AWS DMS](CHAP_Source.S3.md#CHAP_Source.S3.ExternalTableDef)\.

For information on how to view the data type that is mapped in the target, see the section for the target endpoint you are using\.

For additional information about AWS DMS data types, see [Data Types for AWS Database Migration Service](CHAP_Reference.DataTypes.md)\.


|  AWS DMS Data Types—Amazon S3 as Source  | 
| --- | 
| BYTERequires `ColumnLength`\. For more information, see [Defining External Tables for S3 as a Source for AWS DMS](CHAP_Source.S3.md#CHAP_Source.S3.ExternalTableDef)\. | 
| DATE | 
| TIME | 
| DATETIME | 
| TIMESTAMP | 
| INT1 | 
| INT2 | 
| INT4 | 
| INT8 | 
| NUMERIC Requires `ColumnPrecision` and `ColumnScale`\. For more information, see [Defining External Tables for S3 as a Source for AWS DMS](CHAP_Source.S3.md#CHAP_Source.S3.ExternalTableDef)\. | 
| REAL4 | 
| REAL8 | 
| STRINGRequires `ColumnLength`\. For more information, see [Defining External Tables for S3 as a Source for AWS DMS](CHAP_Source.S3.md#CHAP_Source.S3.ExternalTableDef)\. | 
| UINT1 | 
| UINT2 | 
| UINT4 | 
| UINT8 | 
| BLOB | 
| CLOB | 
| BOOLEAN | 