# Using Snowflake as a source for AWS SCT<a name="CHAP_Source.Snowflake"></a>

You can use AWS SCT to convert schemas, code objects, and application code from Snowflake to Amazon Redshift\.

## Privileges for using Snowflake as a source database<a name="CHAP_Source.Snowflake.Permissions"></a>

You can create a role with privileges and grant this role the name of a user by using the `SECURITYADMIN` role and the `SECURITYADMIN` session context\.

The example following creates minimal privileges and grants them to the `min_privs` user\. 

```
create role role_name;  

grant role role_name to role sysadmin;

grant usage on database db_name to role role_name;  

grant usage on schema db_name.schema_name to role role_name;             

grant usage on warehouse datawarehouse_name to role role_name;

grant monitor on database db_name to role role_name;

grant monitor on warehouse datawarehouse_name to role role_name;

grant select on all tables in schema db_name.schema_name to role role_name;

grant select on future tables in schema db_name.schema_name to role role_name;

grant select on all views in schema db_name.schema_name to role role_name;

grant select on future views in schema db_name.schema_name to role role_name;

grant select on all external tables in schema db_name.schema_name to role role_name;

grant select on future external tables in schema db_name.schema_name to role role_name;

grant usage on all sequences in schema db_name.schema_name to role role_name;

grant usage on future sequences in schema db_name.schema_name to role role_name;

grant usage on all functions in schema db_name.schema_name to role role_name;

grant usage on future functions in schema db_name.schema_name to role role_name;

grant usage on all procedures in schema db_name.schema_name to role role_name;

grant usage on future procedures in schema db_name.schema_name to role role_name;

create user min_privs password='real_user_password'  
DEFAULT_ROLE = role_name DEFAULT_WAREHOUSE = 'datawarehouse_name';

grant role role_name to user min_privs;
```

In the example preceding, replace placeholders as following:
+ Replace *`role_name`* with the name of a role with read\-only privileges\.
+ Replace `db_name` with the name of the source database\.
+ Replace `schema_name` with the name of the source schema\.
+ Replace *`datawarehousename`* with the name of a required data warehouse\.
+ Replace `min_privs` with the name of a user that has minimal privileges\.

The `DEFAULT_ROLE` and `DEFAULT_WAREHOUSE` parameters are key\-sensitive\.

## Configuring secure access to Amazon S3<a name="CHAP_Source.Snowflake.IAM"></a>

Security and access management policies for an Amazon S3 bucket allow Snowflake to access, read data from, and write data to the S3 bucket\. You can configure secure access to a private Amazon S3 bucket using the Snowflake `STORAGE INTEGRATION` object type\. A Snowflake storage integration object delegates authentication responsibility to a Snowflake identity and access management entity\.

For more information, see [Configuring a Snowflake Storage Integration to Access Amazon S3](https://docs.snowflake.com/en/user-guide/data-load-s3-config-storage-integration.html) in the Snowflake documentation\. 

## Connecting to Snowflake as a source<a name="CHAP_Source.Snowflake.Connecting"></a>

Use the following procedure to connect to your source database with the AWS Schema Conversion Tool\. 

**To connect to an Snowflake source database**

1. In the AWS Schema Conversion Tool, choose **Add source**\. 

1. Choose **Snowflake**, then choose **Next**\. 

   The **Add source** dialog box appears\.

1. Provide the Snowflake source database connection information\. Use the instructions in the following table\.   
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_Source.Snowflake.html)

1. Choose **Test Connection** to verify that AWS SCT can connect to your source database\. 

1. Choose **Connect** to connect to your source database\.

## Limitations for Snowflake as a source<a name="CHAP_Source.Snowflake.Limitations"></a>

The following are limitations when using Snowflake as a source for AWS SCT:
+ Object identifiers must be unique within the context of the object type and the parent object:  
**Database**  
Schema identifiers must be unique within a database\.  
**Schemas**  
Objects identifiers such as for tables and views must be unique within a schema\.  
**Tables/Views**  
Column identifiers must be unique within a table\.
+ The maximum number of tables for large and xlarge cluster node types is 9,900\. For 8xlarge cluster node types, the maximum number of tables is 100,000\. The limit includes temporary tables, both user\-defined and created by Amazon Redshift during query processing or system maintenance\. For more information, see [Amazon Redshift quotas](https://docs.aws.amazon.com/redshift/latest/mgmt/amazon-redshift-limits.html) in the *Amazon Redshift Cluster Management Guide*\.
+ For stored procedures, the maximum number of input and output arguments is 32\.

## Source data types for Snowflake<a name="CHAP_Source.Snowflake.DataTypes"></a>

Following, you can find the Snowflake source data types that are supported when using AWS SCT and the default mapping to an Amazon Redshift target\.


| Snowflake data types | Amazon Redshift data types | 
| --- | --- | 
|  NUMBER  |  NUMERIC\(38\)  | 
|  NUMBER\(p\)  |  If p is =< 4, then SMALLINT If p is => 5 and =< 9, then INTEGER If p is => 10 and =< 18, then BIGINT If p is => 19 then NUMERIC\(p\)   | 
|  NUMBER\(p, 0\)  |  If p is =< 4, then SMALLINT If p is => 5 and =< 9, then INTEGER If p is => 10 and =< 18, then BIGINT If p is => 19 then: NUMERIC\(p,0\)  | 
|  NUMBER\(p, s\)  |  If p is => 1 and =< 38, and if s is => 1 and =< 37, then NUMERIC\(p,s\)   | 
|  FLOAT  | FLOAT | 
|  TEXT Unicode characters up to 16,777,216 bytes; up to 4 bytes per character\.  |  VARCHAR\(MAX\)  | 
|  TEXT\(p\) Unicode characters up to 65,535 bytes; up to 4 bytes per character\.  |  If p is =< 65,535 then, VARCHAR\(p\)  | 
|  TEXT\(p\) Unicode characters up to 16,777,216 bytes; up to 4 bytes per character\.  |  If p is => 65,535 and =< 16,777,216 then, VARCHAR\(MAX\)  | 
|  BINARY Single\-byte characters up to 8,388,608 bytes; 1 byte per character\.  | VARCHAR\(MAX\) | 
|  BINARY\(p\) Single\-byte characters up to 65,535 bytes; 1 byte per character\.  | VARCHAR\(p\) | 
|  BINARY\(p\) Single\-byte characters up to 8,388,608 bytes; 1 byte per character\.  | VARCHAR\(MAX\) | 
|  BOOLEAN  | BOOLEAN | 
|  DATE  | DATE | 
|  TIME Time values between 00:00:00 and 23:59:59\.999999999\.  | VARCHAR\(18\) | 
|  TIME\(f\) Time values between 00:00:00 and 23:59:59\.9\(f\)\.   | VARCHAR\(n\) – 9 \+ dt\-attr\-1 | 
|  TIMESTAMP\_NTZ  | TIMESTAMP | 
|  TIMESTAMP\_TZ  | TIMESTAMPTZ | 