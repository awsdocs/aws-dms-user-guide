# Source Data Types for MySQL<a name="CHAP_Reference.Source.MySQL.DataTypes"></a>

The following table shows the MySQL database source data types that are supported when using AWS DMS and the default mapping from AWS DMS data types\.

**Note**  
The UTF\-8 4\-byte character set \(utf8mb4\) is not supported and could cause unexpected behavior in a source database\. Plan to convert any data using the UTF\-8 4\-byte character set before migrating\.

For additional information about AWS DMS data types, see [Data Types for AWS Database Migration Service](CHAP_Reference.DataTypes.md)\.


|  MySQL Data Types  |  AWS DMS Data Types  | 
| --- | --- | 
|  INT  |  INT4  | 
|  MEDIUMINT  |  INT4  | 
|  BIGINT  |  INT8  | 
|  TINYINT  |  INT1  | 
|  DECIMAL\(10\)  |  NUMERIC \(10,0\)  | 
|  BINARY  |  BYTES\(1\)  | 
|  BIT  |  BOOLEAN  | 
|  BIT\(64\)  |  BYTES\(8\)  | 
|  BLOB  |  BYTES\(66535\)  | 
|  LONGBLOB  |  BLOB  | 
|  MEDIUMBLOB  |  BLOB  | 
|  TINYBLOB  |  BYTES\(255\)  | 
|  DATE  |  DATE  | 
|  DATETIME  |  DATETIME  | 
|  TIME  |  STRING  | 
|  TIMESTAMP  |  DATETIME  | 
|  YEAR  |  INT2  | 
|  DOUBLE  |  REAL8  | 
|  FLOAT  |  REAL\(DOUBLE\) The supported FLOAT range is \-1\.79E\+308 to \-2\.23E\-308, 0 and 2\.23E\-308 to 1\.79E\+308 If the FLOAT values are not in the range specified here, map the FLOAT data type to the STRING data type\.  | 
|  VARCHAR \(45\)  |  WSTRING \(45\)  | 
|  VARCHAR \(2000\)  |  WSTRING \(2000\)  | 
|  VARCHAR \(4000\)  |  WSTRING \(4000\)  | 
|  VARBINARY \(4000\)  |  BYTES \(4000\)  | 
|  VARBINARY \(2000\)  |  BYTES \(2000\)  | 
|  CHAR  |  WSTRING  | 
|  TEXT  |  WSTRING \(65535\)  | 
|  LONGTEXT  |  NCLOB  | 
|  MEDIUMTEXT  |  NCLOB  | 
|  TINYTEXT  |  WSTRING \(255\)  | 
|  GEOMETRY  |  BLOB  | 
|  POINT  |  BLOB  | 
|  LINESTRING  |  BLOB  | 
|  POLYGON  |  BLOB  | 
|  MULTIPOINT  |  BLOB  | 
|  MULTILINESTRING  |  BLOB  | 
|  MULTIPOLYGON  |  BLOB  | 
|  GEOMETRYCOLLECTION  |  BLOB  | 

**Note**  
If the DATETIME and TIMESTAMP data types are specified with a "zero" value \(that is, 0000\-00\-00\), you need to make sure that the target database in the replication task supports "zero" values for the DATETIME and TIMESTAMP data types\. Otherwise, they are recorded as null on the target\.

The following MySQL data types are supported in full load only:


|  MySQL Data Types  |  AWS DMS Data Types  | 
| --- | --- | 
|  ENUM  |  STRING  | 
|  SET  |  STRING  | 