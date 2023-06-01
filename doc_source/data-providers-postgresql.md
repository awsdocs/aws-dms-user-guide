# Using a PostgreSQL database as a target in DMS Schema Conversion<a name="data-providers-postgresql"></a>

You can use PostgreSQL databases as a migration target in DMS Schema Conversion\.

For information about supported target databases, see [Target data providers for DMS Schema Conversion](CHAP_Introduction.Targets.md#CHAP_Introduction.Targets.SchemaConversion)\.

## Privileges for PostgreSQL as a target<a name="data-providers-postgresql-permissions"></a>

To use PostgreSQL as a target, DMS Schema Conversion requires the `CREATE ON DATABASE` privilege\. Create a user and grant this user with this privilege for each database that you want to use in migration project for DMS Schema Conversion\. 

To use Amazon RDS for PostgreSQL as a target, DMS Schema Conversion requires the `rds_superuser` role\.

To use the converted public synonyms, change the database default search path using the following command\.

```
ALTER DATABASE <db_name> SET SEARCH_PATH = "$user", public_synonyms, public;
```

In this example, replace the `<db_name>` placeholder with the name of your database\.

In PostgreSQL, only the schema owner or a `superuser` can drop a schema\. The owner can drop a schema and all objects that this schema includes, even if the owner of the schema doesn't own some of its objects\.

When you use different users to convert and apply different schemas to your target database, you may encounter an error message when DMS Schema Conversion can't drop a schema\. To avoid this error message, use the `superuser` role\.