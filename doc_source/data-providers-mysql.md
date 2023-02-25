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
+ `INVOKE LAMBDA ON *.*`
+ `INSERT, UPDATE ON AWS_ORACLE_EXT.*`
+ `INSERT, UPDATE, DELETE ON AWS_ORACLE_EXT_DATA.*`
+ `INSERT, UPDATE ON AWS_SQLSERVER_EXT.*`
+ `INSERT, UPDATE, DELETE ON AWS_SQLSERVER_EXT_DATA.*`
+ `CREATE TEMPORARY TABLES ON AWS_SQLSERVER_EXT_DATA.*`

To use RDS for MySQL as a target, set the `log_bin_trust_function_creators` parameter to `1` and the `character_set_server` parameter to `latin1`\.

To use Amazon Aurora MySQL as a target, set the `lower_case_table_names` parameter to `1`\. Also, set the `log_bin_trust_function_creators` parameter to `1`, and the `character_set_server` parameter to `latin1`\.