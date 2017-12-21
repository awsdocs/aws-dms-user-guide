# Source Data Types for PostgreSQL<a name="CHAP_Reference.Source.PostgreSQL.DataTypes"></a>

The following table shows the PostgreSQL source data types that are supported when using AWS DMS and the default mapping to AWS DMS data types\.

For additional information about AWS DMS data types, see [Data Types for AWS Database Migration Service](CHAP_Reference.DataTypes.md)\.


|  PostgreSQL Data Types  |  AWS DMS Data Types  | 
| --- | --- | 
|  INTEGER  |  INT4  | 
|  SMALLINT  |  INT2  | 
|  BIGINT  |  INT8  | 
|  NUMERIC \(p,s\)  |  If precision is from 0 through 38, then use NUMERIC\. If precision is 39 or greater, then use STRING\.  | 
|  DECIMAL\(P,S\)  |  If precision is from 0 through 38, then use NUMERIC\. If precision is 39 or greater, then use STRING\.  | 
|  REAL  |  REAL4  | 
|  DOUBLE  |  REAL8  | 
|  SMALLSERIAL  |  INT2  | 
|  SERIAL  |  INT4  | 
|  BIGSERIAL  |  INT8  | 
|  MONEY  |  NUMERIC\(38,4\) Note: The MONEY data type is mapped to FLOAT in SQL Server\.  | 
|  CHAR  |  WSTRING \(1\)  | 
|  CHAR\(N\)  |  WSTRING \(n\)  | 
|  VARCHAR\(N\)  |  WSTRING \(n\)  | 
|  TEXT  |  NCLOB  | 
|  BYTEA  |  BLOB  | 
|  TIMESTAMP  |  TIMESTAMP  | 
|  TIMESTAMP \(z\)  |  TIMESTAMP  | 
|  TIMESTAMP with time zone  |  Not supported  | 
|  DATE  |  DATE  | 
|  TIME  |  TIME  | 
|  TIME \(z\)  |  TIME  | 
|  INTERVAL  |  STRING \(128\)—1 YEAR, 2 MONTHS, 3 DAYS, 4 HOURS, 5 MINUTES, 6 SECONDS  | 
|  BOOLEAN  |  CHAR \(5\) false or true  | 
|  ENUM  |  STRING \(64\)  | 
|  CIDR  |  STRING \(50\)  | 
|  INET  |  STRING \(50\)  | 
|  MACADDR  |  STRING \(18\)  | 
|  BIT \(n\)  |  STRING \(n\)  | 
|  BIT VARYING \(n\)  |  STRING \(n\)  | 
|  UUID  |  STRING  | 
|  TSVECTOR  |  CLOB  | 
|  TSQUERY  |  CLOB  | 
|  XML  |  CLOB  | 
|  POINT  |  STRING \(255\) "\(x,y\)"  | 
|  LINE  |  STRING \(255\) "\(x,y,z\)"  | 
|  LSEG  |  STRING \(255\) "\(\(x1,y1\),\(x2,y2\)\)"  | 
|  BOX  |  STRING \(255\) "\(\(x1,y1\),\(x2,y2\)\)"  | 
|  PATH  |  CLOB "\(\(x1,y1\),\(xn,yn\)\)"  | 
|  POLYGON  |  CLOB "\(\(x1,y1\),\(xn,yn\)\)"  | 
|  CIRCLE  |  STRING \(255\) "\(x,y\),r"  | 
|  JSON  |  NCLOB  | 
|  ARRAY  |  NCLOB  | 
|  COMPOSITE  |  NCLOB  | 
|  INT4RANGE  |  STRING \(255\)  | 
|  INT8RANGE  |  STRING \(255\)  | 
|  NUMRANGE  |  STRING \(255\)  | 
|  STRRANGE  |  STRING \(255\)  | 