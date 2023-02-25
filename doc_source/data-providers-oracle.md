# Using an Oracle database as a source in DMS Schema Conversion<a name="data-providers-oracle"></a>

DMS Schema Conversion supports the following versions of on\-premises Oracle databases: 10\.2 and later \(for versions 10\.x\), 11g and up to 12\.2, 18c, and 19c\. Also, you can connect to the following versions of Amazon RDS for Oracle databases: 11g \(versions 11\.2\.0\.4 and later\) and up to 12\.2, 18c, and 19c\.

To connect to your Oracle database, use the Oracle System ID \(SID\)\. To find the Oracle SID, submit the following query to your Oracle database:

```
SELECT sys_context('userenv','instance_name') AS SID FROM dual;
```

You can use DMS Schema Conversion to convert database code objects from Oracle Database to the following targets:
+ Aurora MySQL
+ Aurora PostgreSQL
+ RDS for MySQL
+ RDS for PostgreSQL

For more information about using DMS Schema Conversion with a source Oracle database, see the [Oracle to PostgreSQL migration step\-by\-step walkthrough](https://docs.aws.amazon.com/dms/latest/sbs/schema-conversion-oracle-postgresql.html)\.

## Privileges for Oracle as a source<a name="data-providers-oracle-permissions"></a>

The following privileges are required for Oracle as a source: 
+ CONNECT 
+ SELECT\_CATALOG\_ROLE 
+ SELECT ANY DICTIONARY 
+ SELECT ON SYS\.ARGUMENT$