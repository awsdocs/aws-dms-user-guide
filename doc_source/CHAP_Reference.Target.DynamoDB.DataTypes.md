# Target Data Types for Amazon DynamoDB<a name="CHAP_Reference.Target.DynamoDB.DataTypes"></a>

The Amazon DynamoDB endpoint for Amazon AWS DMS supports most Amazon DynamoDB data types\. The following table shows the Amazon AWS DMS target data types that are supported when using AWS DMS and the default mapping from AWS DMS data types\.

For additional information about AWS DMS data types, see [Data Types for AWS Database Migration Service](CHAP_Reference.DataTypes.md)\.

When AWS DMS migrates data from heterogeneous databases, we map data types from the source database to intermediate data types called AWS DMS data types\. We then map the intermediate data types to the target data types\. The following table shows each AWS DMS data type and the data type it maps to in DynamoDB:


| AWS DMS Data Type | DynamoDB Data Type | 
| --- | --- | 
|  String  |  String  | 
|  WString  |  String  | 
|  Boolean  |  Boolean  | 
|  Date  |  String  | 
|  DateTime  |  String  | 
|  INT1  |  Number  | 
|  INT2  |  Number  | 
|  INT4  |  Number  | 
|  INT8  |  Number  | 
|  Numeric  |  Number  | 
|  Real4  |  Number  | 
|  Real8  |  Number  | 
|  UINT1  |  Number  | 
|  UINT2  |  Number  | 
|  UINT4  |  Number  | 
| UINT8 | Number | 
| CLOB | String | 