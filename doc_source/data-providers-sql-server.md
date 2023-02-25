# Using a Microsoft SQL Server database as a source in DMS Schema Conversion<a name="data-providers-sql-server"></a>

DMS Schema Conversion supports the following versions of on\-premises SQL Server databases: 2008R2, 2012, 2014, 2016, 2017, and 2019\. Also, you can connect to the following versions of RDS for SQL Server databases: 2012, 2014, 2016, 2017, and 2019\.

You can use DMS Schema Conversion to convert database code objects from SQL Server to the following targets:
+ Aurora MySQL
+ Aurora PostgreSQL
+ RDS for MySQL
+ RDS for PostgreSQL

For more information about using DMS Schema Conversion with a source SQL Server database, see the [SQL Server to MySQL migration step\-by\-step walkthrough](https://docs.aws.amazon.com/dms/latest/sbs/schema-conversion-sql-server-mysql.html)\.

## Privileges for Microsoft SQL Server as a source<a name="data-providers-sql-server-permissions"></a>

View the following list of privileges required for Microsoft SQL Server as a source: 
+ VIEW DEFINITION
+ VIEW DATABASE STATE

The `VIEW DEFINITION` privilege enables users that have public access to see object definitions\. DMS Schema Conversion uses the `VIEW DATABASE STATE` privilege to check the features of the SQL Server Enterprise edition\.

Repeat the grant for each database whose schema you are converting\.

In addition, grant the following privileges on the `master` database:
+ VIEW SERVER STATE
+ VIEW ANY DEFINITION

DMS Schema Conversion uses the `VIEW SERVER STATE` privilege to collect server settings and configuration\. Make sure that you grant the `VIEW ANY DEFINITION` privilege to view data providers\.

To read information about Microsoft Analysis Services, run the following command on the `master` database\.

```
EXEC master..sp_addsrvrolemember @loginame = N'<user_name>', @rolename = N'sysadmin'
```

In the preceding example, replace the `<user_name>` placeholder with the name of the user who you previously granted with the required privileges\.

To read information about SQL Server Agent, add your user to the SQLAgentUser role\. Run the following command on the `msdb` database\.

```
EXEC sp_addrolemember <SQLAgentRole>, <user_name>;
```

In the preceding example, replace the `<SQLAgentRole>` placeholder with the name of the SQL Server Agent role\. Then replace the `<user_name>` placeholder with the name of the user who you previously granted with the required privileges\. For more information, see [Adding a user to the SQLAgentUser role](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Appendix.SQLServer.CommonDBATasks.Agent.html#SQLServerAgent.AddUser) in the *Amazon RDS User Guide*\.

To detect log shipping, grant the `SELECT on dbo.log_shipping_primary_databases` privilege on the `msdb` database\.

To use the notification approach of the data definition language \(DDL\) replication, grant the `RECEIVE ON <schema_name>.<queue_name>` privilege on your source databases\. In this example, replace the `<schema_name>` placeholder with the schema name of your database\. Then, replace the `<queue_name>` placeholder with the name of a queue table\.