# Source Data Types for MongoDB<a name="CHAP_Reference.Source.MongoDB.DataTypes"></a>

Data migration that uses MongoDB as a source for AWS DMS supports most MongoDB data types\. The following table shows the MongoDB source data types that are supported when using AWS DMS and the default mapping from AWS DMS data types\. For more information about MongoDB data types, see [ https://docs\.mongodb\.com/manual/reference/bson\-types](https://docs.mongodb.com/manual/reference/bson-types)\.

For information on how to view the data type that is mapped in the target, see the section for the target endpoint you are using\.

For additional information about AWS DMS data types, see [Data Types for AWS Database Migration Service](CHAP_Reference.DataTypes.md)\.


|  MongoDB Data Types  |  AWS DMS Data Types  | 
| --- | --- | 
| Boolean | Bool | 
| Binary | BLOB | 
| Date | Date | 
| Timestamp | Date | 
| Int | INT4 | 
| Long | INT8 | 
| Double | REAL8 | 
| String \(UTF\-8\) | CLOB | 
| Array | CLOB | 
| OID | String | 
| REGEX | CLOB | 
| CODE | CLOB | 