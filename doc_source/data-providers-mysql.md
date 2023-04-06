# Using a MySQL database as a target in DMS Schema Conversion<a name="data-providers-mysql"></a>

You can use MySQL databases version 8\.x as a target database in DMS Schema Conversion\.

## Privileges for MySQL as a target<a name="data-providers-mysql-permissions"></a>

The following privileges are required for MySQL as a target:
+ `CREATE ON *.*`
+ `ALTER ON *.*`
+ `DROP ON *.*`
+ `INDEX ON *.*`
+ `REFERENCES ON *.*`
+ `SELECT ON *.*`
+ `CREATE VIEW ON *.*`
+ `SHOW VIEW ON *.*`
+ `TRIGGER ON *.*`
+ `CREATE ROUTINE ON *.*`
+ `ALTER ROUTINE ON *.*`
+ `EXECUTE ON *.*`
+ `CREATE TEMPORARY TABLES ON *.*`
+ `AWS_LAMBDA_ACCESS`
+ `INSERT, UPDATE ON AWS_ORACLE_EXT.*`
+ `INSERT, UPDATE, DELETE ON AWS_ORACLE_EXT_DATA.*`
+ `INSERT, UPDATE ON AWS_SQLSERVER_EXT.*`
+ `INSERT, UPDATE, DELETE ON AWS_SQLSERVER_EXT_DATA.*`
+ `CREATE TEMPORARY TABLES ON AWS_SQLSERVER_EXT_DATA.*`

You can use the following code example to create a database user and grant the privileges\.

```
CREATE USER 'user_name' IDENTIFIED BY 'your_password';
GRANT CREATE ON *.* TO 'user_name';
GRANT ALTER ON *.* TO 'user_name';
GRANT DROP ON *.* TO 'user_name';
GRANT INDEX ON *.* TO 'user_name';
GRANT REFERENCES ON *.* TO 'user_name';
GRANT SELECT ON *.* TO 'user_name';
GRANT CREATE VIEW ON *.* TO 'user_name';
GRANT SHOW VIEW ON *.* TO 'user_name';
GRANT TRIGGER ON *.* TO 'user_name';
GRANT CREATE ROUTINE ON *.* TO 'user_name';
GRANT ALTER ROUTINE ON *.* TO 'user_name';
GRANT EXECUTE ON *.* TO 'user_name';
GRANT CREATE TEMPORARY TABLES ON *.* TO 'user_name';
GRANT AWS_LAMBDA_ACCESS TO 'user_name';
GRANT INSERT, UPDATE ON AWS_ORACLE_EXT.* TO 'user_name';
GRANT INSERT, UPDATE, DELETE ON AWS_ORACLE_EXT_DATA.* TO 'user_name';
GRANT INSERT, UPDATE ON AWS_SQLSERVER_EXT.* TO 'user_name';
GRANT INSERT, UPDATE, DELETE ON AWS_SQLSERVER_EXT_DATA.* TO 'user_name';
GRANT CREATE TEMPORARY TABLES ON AWS_SQLSERVER_EXT_DATA.* TO 'user_name';
```

In the preceding example, replace *user\_name* with the name of your user\. Then, replace *your\_password* with a secure password\.

To use Amazon RDS for MySQL or Aurora MySQL as a target, set the `lower_case_table_names` parameter to `1`\. This value means that the MySQL server handles identifiers of such object names as tables, indexes, triggers, and databases as case insensitive\. If you have turned on binary logging in your target instance, then set the `log_bin_trust_function_creators` parameter to `1`\. In this case, you don't need to use the `DETERMINISTIC`, `READS SQL DATA` or `NO SQL` characteristics to create stored functions\. To configure these parameters, create a new DB parameter group or modify an existing DB parameter group\.