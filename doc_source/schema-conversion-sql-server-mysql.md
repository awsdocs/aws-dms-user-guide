# SQL Server to MySQL conversion settings<a name="schema-conversion-sql-server-mysql"></a>

SQL Server to MySQL conversion settings in DMS Schema Conversion include the following:
+ Your source SQL Server database can store the output of `EXEC` in a table\. DMS Schema Conversion creates temporary tables and an additional procedure to emulate this feature\. To use this emulation, select **Create additional routines to handle open datasets**\.