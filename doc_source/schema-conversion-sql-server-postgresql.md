# SQL Server to PostgreSQL conversion settings<a name="schema-conversion-sql-server-postgresql"></a>

SQL Server to PostgreSQL conversion settings in DMS Schema Conversion include the following:
+ In SQL Server, you can use indexes with the same name in different tables\. However, in PostgreSQL, all index names that you use in the schema must be unique\. To make sure that DMS Schema Conversion generates unique names for all your indexes, select **Generate unique names for indexes**\.
+ PostgreSQL versions 10 and earlier don't support procedures\. If you aren't familiar with using procedures in PostgreSQL, AWS DMS can convert SQL Server procedures to PostgreSQL functions\. To do so, select **Convert procedures to functions**\.
+ Your source SQL Server database can store the output of `EXEC` in a table\. DMS Schema Conversion creates temporary tables and an additional procedure to emulate this feature\. To use this emulation, select **Create additional routines to handle open datasets**\.
+ You can define the template to use for the schema names in the converted code\. For **Schema names**, choose one of the following options:
  + **DB** – Uses the SQL Server database name as a schema name in PostgreSQL\.
  + **SCHEMA** – Uses the SQL Server schema name as a schema name in PostgreSQL\.
  + **DB\_SCHEMA** – Uses a combination of the SQL Server database and schema names as a schema name in PostgreSQL\.
+ You can keep the letter case of your source object names\. To avoid conversion of object names to lowercase, select **Keep object names in the same case**\. This option applies only when you turn on the case sensitivity option in your target database\.
+ You can keep the parameter names from your source database\. DMS Schema Conversion can add double quotation marks to the names of parameters in the converted code\. To do so, select **Keep original parameter names**\.