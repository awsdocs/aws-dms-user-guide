# Using a SAP ASE Database as a Target for AWS Database Migration Service<a name="CHAP_Target.SAP"></a>

You can migrate data to SAP Adaptive Server Enterprise \(ASE\)–formerly known as Sybase–databases using AWS DMS, either from any of the supported database sources\.

SAP ASE versions 15, 15\.5, 15\.7, 16 and later are supported\.

## Prerequisites for Using a SAP ASE Database as a Target for AWS Database Migration Service<a name="CHAP_Target.SAP.Prerequisites"></a>

Before you begin to work with a SAP ASE database as a target for AWS DMS, make sure that you have the following prerequisites:
+ You must provide SAP ASE account access to the AWS DMS user\. This user must have read/write privileges in the SAP ASE database\.
+ When replicating to SAP ASE version 15\.7 installed on a Windows EC2 instance configured with a non\-Latin language \(for example, Chinese\), AWS DMS requires SAP ASE 15\.7 SP121 to be installed on the target SAP ASE machine\.

## Extra Connection Attributes When Using SAP ASE as a Target for AWS DMS<a name="CHAP_Target.SAP.ConnectionAttrib"></a>

You can use extra connection attributes to configure your SAP ASE target\. You specify these settings when you create the target endpoint\. Multiple extra connection attribute settings should be separated by a semicolon\. 

The following table shows the extra connection attributes available when using SAP ASE as a target:

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Target.SAP.html)

**Note**  
If the user name or password specified in the connection string contains non\-Latin characters \(for example, Chinese\), the following property is required: `charset=gb18030`

## Target Data Types for SAP ASE<a name="CHAP_Target.SAP.DataTypes"></a>

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

AWS DMS does not support tables that include fields with the following data types\. Replicated columns with these data types show as null\. 
+ User\-defined type \(UDT\)