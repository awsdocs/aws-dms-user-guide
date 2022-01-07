# Converting SQL Server to PostgreSQL<a name="CHAP_Source.SQLServer.ToPostgreSQL"></a>

Some things to consider when migrating a SQL Server schema to PostgreSQL: 
+ In PostgreSQL, all object’s names in a schema must be unique, including indexes\. Index names must be unique in the schema of the base table\. In SQL Server, an index name can be the same for different tables\.

  To ensure the uniqueness of index names, AWS SCT gives you the option to generate unique index names if your index names are not unique\. To do this, choose the option **Generate unique index names** in the project properties\. By default, this option is enabled\. If this option is enabled, unique index names are created using the format IX\_table\_name\_index\_name\. If this option is disabled, index names aren’t changed\.
+ A GOTO statement and a label can be used to change the order that statements are run in\. Any Transact\-SQL statements that follow a GOTO statement are skipped and processing continues at the label\. GOTO statements and labels can be used anywhere within a procedure, batch, or statement block\. GOTO statements can also be nested\.

  PostgreSQL doesn’t use GOTO statements\. When AWS SCT converts code that contains a GOTO statement, it converts the statement to use a BEGIN…END or LOOP…END LOOP statement\. You can find examples of how AWS SCT converts GOTO statements in the table following\.  
**SQL Server GOTO statements and the converted PostgreSQL statements**    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_Source.SQLServer.ToPostgreSQL.html)
+ PostgreSQL doesn't support a MERGE statement\. AWS SCT emulates the behavior of a MERGE statement in the following ways:
  + By INSERT ON CONFLICT construction\.
  + By using the UPDATE FROM DML statement, such as MERGE without a WHEN NOT MATCHED clause\.
  + By using CURSOR, such as with a MERGE with DELETE clause or by using a complex MERGE ON condition statement\.
+ AWS SCT can add database triggers to the object tree when Amazon RDS is the target\.
+ AWS SCT can add server\-level triggers to the object tree when Amazon RDS is the target\.
+ AWS SCT can add linked servers to the object tree when Amazon RDS is the target\.
+ When migrating from Microsoft SQL Server to PostgreSQL, the built\-in SUSER\_SNAME function is converted as follows:
  + SUSER\_SNAME – Returns the login name associated with a security identification number \(SID\)\.
  + SUSER\_SNAME\(<server\_user\_sid>\) – Not supported\.
  + SUSER\_SNAME\(\) CURRENT\_USER – Returns the user name of the current execution context\.
  + SUSER\_SNAME\(NULL\) – Returns NULL\.
+ Converting table\-valued functions is supported\. Table\-valued functions return a table and can take the place of a table in a query\.
+ PATINDEX returns the starting position of the first occurrence of a pattern in a specified expression on all valid text and character data types\. It returns zeros if the pattern is not found\. When converting from SQL Server to Amazon RDS for PostgreSQL, AWS SCT replaces application code that uses PATINDEX with aws\_sqlserver\_ext\.patindex\(<pattern character>, <expression character varying>\) \.
+ In SQL Server, a user\-defined table type is a type that represents the definition of a table structure\. You use a user\-defined table type to declare table\-value parameters for stored procedures or functions\. You can also use a user\-defined table type to declare table variables that you want to use in a batch or in the body of a stored procedure or function\. AWS SCT emulated this type in PostgreSQL by creating a temporary table\.

When converting from SQL Server to PostgreSQL, AWS SCT converts SQL Server system objects into recognizable objects in PostgreSQL\. The following table shows how the system objects are converted\. 

 


| >MS SQL Server use cases | PostgreSQL substitution | 
| --- | --- | 
| SYS\.SCHEMAS | AWS\_SQLSERVER\_EXT\.SYS\_SCHEMAS | 
| SYS\.TABLES | AWS\_SQLSERVER\_EXT\.SYS\_TABLES | 
| SYS\.VIEWS | AWS\_SQLSERVER\_EXT\.SYS\_VIEWS | 
| SYS\.ALL\_VIEWS | AWS\_SQLSERVER\_EXT\.SYS\_ALL\_VIEWS | 
| SYS\.TYPES | AWS\_SQLSERVER\_EXT\.SYS\_TYPES | 
| SYS\.COLUMNS | AWS\_SQLSERVER\_EXT\.SYS\_COLUMNS | 
| SYS\.ALL\_COLUMNS | AWS\_SQLSERVER\_EXT\.SYS\_ALL\_COLUMNS | 
| SYS\.FOREIGN\_KEYS | AWS\_SQLSERVER\_EXT\.SYS\_FOREIGN\_KEYS | 
| SYS\.SYSFOREIGNKEYS | AWS\_SQLSERVER\_EXT\.SYS\_SYSFOREIGNKEYS | 
| SYS\.FOREIGN\_KEY\_COLUMNS | AWS\_SQLSERVER\_EXT\.SYS\_FOREIGN\_KEY\_COLUMNS | 
| SYS\.KEY\_CONSTRAINTS | AWS\_SQLSERVER\_EXT\.SYS\_KEY\_CONSTRAINTS | 
| SYS\.IDENTITY\_COLUMNS | AWS\_SQLSERVER\_EXT\.SYS\_IDENTITY\_COLUMNS | 
| SYS\.PROCEDURES | AWS\_SQLSERVER\_EXT\.SYS\_PROCEDURES | 
| SYS\.INDEXES | AWS\_SQLSERVER\_EXT\.SYS\_INDEXES | 
| SYS\.SYSINDEXES | AWS\_SQLSERVER\_EXT\.SYS\_SYSINDEXES | 
| SYS\.OBJECTS | AWS\_SQLSERVER\_EXT\.SYS\_OBJECTS | 
| SYS\.ALL\_OBJECTS | AWS\_SQLSERVER\_EXT\.SYS\_ALL\_OBJECTS | 
| SYS\.SYSOBJECTS | AWS\_SQLSERVER\_EXT\.SYS\_SYSOBJECTS | 
| SYS\.SQL\_MODULES | AWS\_SQLSERVER\_EXT\.SYS\_SQL\_MODULES | 
| SYS\.DATABASES | AWS\_SQLSERVER\_EXT\.SYS\_DATABASES | 
| INFORMATION\_SCHEMA\.SCHEMATA  | AWS\_SQLSERVER\_EXT\.INFORMATION\_SCHEMA\_SCHEMATA | 
| INFORMATION\_SCHEMA\.VIEWS | AWS\_SQLSERVER\_EXT\.INFORMATION\_SCHEMA\_VIEWS | 
| INFORMATION\_SCHEMA\.TABLES | AWS\_SQLSERVER\_EXT\.INFORMATION\_SCHEMA\_TABLES | 
| INFORMATION\_SCHEMA\.COLUMNS | AWS\_SQLSERVER\_EXT\.INFORMATION\_SCHEMA\_COLUMNS | 
| INFORMATION\_SCHEMA\.CHECK\_CONSTRAINTS | AWS\_SQLSERVER\_EXT\.INFORMATION\_SCHEMA\_CHECK\_CONSTRAINTS | 
| INFORMATION\_SCHEMA\.REFERENTIAL\_CONSTRAINTS | AWS\_SQLSERVER\_EXT\.INFORMATION\_SCHEMA\_REFERENTIAL\_CONSTRAINTS | 
| INFORMATION\_SCHEMA\.TABLE\_CONSTRAINTS | AWS\_SQLSERVER\_EXT\.INFORMATION\_SCHEMA\_TABLE\_CONSTRAINTS | 
| INFORMATION\_SCHEMA\.KEY\_COLUMN\_USAGE | AWS\_SQLSERVER\_EXT\.INFORMATION\_SCHEMA\_KEY\_COLUMN\_USAGE | 
| INFORMATION\_SCHEMA\.CONSTRAINT\_TABLE\_USAGE | AWS\_SQLSERVER\_EXT\.INFORMATION\_SCHEMA\_CONSTRAINT\_TABLE\_USAGE  | 
| INFORMATION\_SCHEMA\.CONSTRAINT\_COLUMN\_USAGE | AWS\_SQLSERVER\_EXT\.INFORMATION\_SCHEMA\_CONSTRAINT\_COLUMN\_USAGE  | 
| INFORMATION\_SCHEMA\.ROUTINES | AWS\_SQLSERVER\_EXT\.INFORMATION\_SCHEMA\_ROUTINES | 
| SYS\.SYSPROCESSES | AWS\_SQLSERVER\_EXT\.SYS\_SYSPROCESSES | 
| sys\.system\_objects | AWS\_SQLSERVER\_EXT\.SYS\_SYSTEM\_OBJECTS | 

## Converting SQL Server partitions to PostgreSQL version 10 partitions<a name="CHAP_Source.SQLServer.ToPostgreSQL.PG10Partitions"></a>

In SQL Server, you create partitions with partition functions\. When converting from a SQL Server portioned table to a PostgreSQL version 10 partitioned table, be aware of several potential issues:
+ SQL Server allows you to partition a table using a column without a NOT NULL constraint\. In that case, all NULL values go to the leftmost partition\. PostgreSQL doesn’t support NULL values for RANGE partitioning\.
+ SQL Server allows you to create primary and unique keys for partitioned tables\. For PostgreSQL, you create primary or unique keys for each partition directly\. Thus, PRIMARY or UNIQUE KEY constraint must be removed from their parent table when migrating to PostgreSQL\. The resulting key names take the format 

   `<original_key_name>_<partition_number>`\.
+ SQL Server allows you to create foreign key constraint from and to partitioned tables\. PostgreSQL doesn’t support foreign keys referencing partitioned tables\. Also, PostgreSQL doesn’t support foreign key references from a partitioned table to another table\.
+ SQL Server allows you to create indexes for partitioned tables\. For PostgreSQL, an index should be created for each partition directly\. Thus, indexes must be removed from their parent tables when migrating to PostgreSQL\. The resulting index names take the format `<original_index_name>_<partition_number>`\.
+  PostgreSQL doesn’t support partitioned indexes\.