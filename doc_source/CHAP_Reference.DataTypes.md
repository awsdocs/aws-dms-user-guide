# Data types for AWS Database Migration Service<a name="CHAP_Reference.DataTypes"></a>

AWS Database Migration Service uses built\-in data types to migrate data from a source database engine type to a target database engine type\. The following table shows the built\-in data types and their descriptions\.


|  AWS DMS data types  |  Description  | 
| --- | --- | 
|  STRING  |  A character string\.  | 
|  WSTRING  |  A double\-byte character string\.  | 
|  BOOLEAN  |  A Boolean value\.  | 
|  BYTE  |  A binary data value\.  | 
|  DATE  |  A date value: year, month, day\.  | 
|  TIME  |  A time value: hour, minutes, seconds\.  | 
|  DATETIME  |  A timestamp value: year, month, day, hour, minute, second, fractional seconds\. The fractional seconds have a maximum scale of 9 digits\. The following format is supported: YYYY:MM:DD HH:MM:SS\.F\(9\)\.  | 
|  INT1  |  A one\-byte, signed integer\.  | 
|  INT2  |  A two\-byte, signed integer\.  | 
|  INT4  |  A four\-byte, signed integer\.  | 
|  INT8  |  An eight\-byte, signed integer\.  | 
|  NUMERIC   |  An exact numeric value with a fixed precision and scale\.  | 
|  REAL4  |  A single\-precision floating\-point value\.  | 
|  REAL8  |  A double\-precision floating\-point value\.  | 
|  UINT1  |  A one\-byte, unsigned integer\.  | 
|  UINT2  |  A two\-byte, unsigned integer\.  | 
|  UINT4  |  A four\-byte, unsigned integer\.  | 
|  UINT8  |  An eight\-byte, unsigned integer\.  | 
|  BLOB  |   Binary large object\. This data type can be used only with Oracle endpoints\.  | 
|  CLOB  |  Character large object\.    | 
|  NCLOB  |  Native character large object\.    | 

**Note**  
AWS DMS can't migrate any LOB data type to an Apache Kafka endpoint\.