# Troubleshooting Migration Tasks in AWS Database Migration Service<a name="CHAP_Troubleshooting"></a>

The following sections provide information on troubleshooting issues with AWS Database Migration Service \(AWS DMS\)\. 

**Topics**
+ [Slow Running Migration Tasks](#CHAP_Troubleshooting.General.SlowTask)
+ [Task Status Bar Not Moving](#CHAP_Troubleshooting.General.StatusBar)
+ [Missing Foreign Keys and Secondary Indexes](#CHAP_Troubleshooting.General.MissingSecondaryObjs)
+ [Amazon RDS Connection Issues](#CHAP_Troubleshooting.General.RDSConnection)
+ [Networking Issues](#CHAP_Troubleshooting.General.Network)
+ [CDC Stuck After Full Load](#CHAP_Troubleshooting.General.CDCStuck)
+ [Primary Key Violation Errors When Restarting a Task](#CHAP_Troubleshooting.General.PKErrors)
+ [Initial Load of Schema Fails](#CHAP_Troubleshooting.General.SchemaLoadFail)
+ [Tasks Failing With Unknown Error](#CHAP_Troubleshooting.General.TasksFail)
+ [Task Restart Loads Tables From the Beginning](#CHAP_Troubleshooting.General.RestartLoad)
+ [Number of Tables Per Task](#CHAP_Troubleshooting.General.TableLimit)
+ [Troubleshooting Oracle Specific Issues](#CHAP_Troubleshooting.Oracle)
+ [Troubleshooting MySQL Specific Issues](#CHAP_Troubleshooting.MySQL)
+ [Troubleshooting PostgreSQL Specific Issues](#CHAP_Troubleshooting.PostgreSQL)
+ [Troubleshooting Microsoft SQL Server Specific Issues](#CHAP_Troubleshooting.SQLServer)
+ [Troubleshooting Amazon Redshift Specific Issues](#CHAP_Troubleshooting.Redshift)
+ [Troubleshooting Amazon Aurora MySQL Specific Issues](#CHAP_Troubleshooting.Aurora)

## Slow Running Migration Tasks<a name="CHAP_Troubleshooting.General.SlowTask"></a>

Several issues can cause a migration task to run slowly, or cause subsequent tasks to run slower than the initial task\. The most common reason for a migration task running slowly is that there are inadequate resources allocated to the AWS DMS replication instance\. Check your replication instance's use of CPU, memory, swap files, and IOPS to ensure that your instance has enough resources for the tasks you are running on it\. For example, multiple tasks with Amazon Redshift as an endpoint are IO intensive\. You can increase IOPS for your replication instance or split your tasks across multiple replication instances for a more efficient migration\.

For more information about determining the size of your replication instance, see [Choosing the Optimum Size for a Replication Instance](CHAP_BestPractices.md#CHAP_BestPractices.SizingReplicationInstance)

You can increase the speed of an initial migration load by doing the following:
+ If your target is an Amazon RDS DB instance, ensure that Multi\-AZ is not enabled for the target DB instance\.
+ Turn off any automatic backups or logging on the target database during the load, and turn back on those features once the migration is complete\.
+ If the feature is available on the target, use Provisioned IOPS\.
+ If your migration data contains LOBs, ensure that the task is optimized for LOB migration\. See [Target Metadata Task Settings](CHAP_Tasks.CustomizingTasks.TaskSettings.TargetMetadata.md) for more information on optimizing for LOBs\.

## Task Status Bar Not Moving<a name="CHAP_Troubleshooting.General.StatusBar"></a>

The task status bar gives an estimation of the task's progress\. The quality of this estimate depends on the quality of the source database’s table statistics; the better the table statistics, the more accurate the estimation\. For a task with only one table that has no estimated rows statistic, we are unable to provide any kind of percentage complete estimate\. In this case, the task state and the indication of rows loaded can be used to confirm that the task is indeed running and making progress\.

## Missing Foreign Keys and Secondary Indexes<a name="CHAP_Troubleshooting.General.MissingSecondaryObjs"></a>

 AWS DMS creates tables, primary keys, and in some cases unique indexes, but it doesn't create any other objects that are not required to efficiently migrate the data from the source\. For example, it doesn't create secondary indexes, non\-primary key constraints, or data defaults\. 

To migrate secondary objects from your database, use the database's native tools if you are migrating to the same database engine as your source database\. Use the Schema Conversion Tool if you are migrating to a different database engine than that used by your source database to migrate secondary objects\.

## Amazon RDS Connection Issues<a name="CHAP_Troubleshooting.General.RDSConnection"></a>

There can be several reasons why you are unable to connect to an Amazon RDS DB instance that you set as an endpoint\. These include:
+ Username and password combination is incorrect\.
+ Check that the endpoint value shown in the Amazon RDS console for the instance is the same as the endpoint identifier you used to create the AWS DMS endpoint\.
+ Check that the port value shown in the Amazon RDS console for the instance is the same as the port assigned to the AWS DMS endpoint\.
+ Check that the security group assigned to the Amazon RDS DB instance allows connections from the AWS DMS replication instance\.
+ If the AWS DMS replication instance and the Amazon RDS DB instance are not in the same VPC, check that the DB instance is publicly accessible\.

### Error Message: Incorrect thread connection string: incorrect thread value 0<a name="CHAP_Troubleshooting.General.RDSConnection.ConnectionString"></a>

This error can often occur when you are testing the connection to an endpoint\. The error indicates that there is an error in the connection string, such as a space after the host IP address or a bad character was copied into the connection string\.

## Networking Issues<a name="CHAP_Troubleshooting.General.Network"></a>

The most common networking issue involves the VPC security group used by the AWS DMS replication instance\. By default, this security group has rules that allow egress to 0\.0\.0\.0/0 on all ports\. If you modify this security group or use your own security group, egress must, at a minimum, be permitted to the source and target endpoints on the respective database ports\.

Other configuration related issues include:
+  **Replication instance and both source and target endpoints in the same VPC** — The security group used by the endpoints must allow ingress on the database port from the replication instance\. Ensure that the security group used by the replication instance has ingress to the endpoints, or you can create a rule in the security group used by the endpoints that allows the private IP address of the replication instance access\. 
+  **Source endpoint is outside the VPC used by the replication instance \(using Internet Gateway\)** — The VPC security group must include routing rules that send traffic not destined for the VPC to the Internet Gateway\. In this configuration, the connection to the endpoint appears to come from the public IP address on the replication instance\. 
+  **Source endpoint is outside the VPC used by the replication instance \(using NAT Gateway\)** — You can configure a network address translation \(NAT\) gateway using a single Elastic IP Address bound to a single Elastic Network Interface which then receives a NAT identifier \(nat\-\#\#\#\#\#\)\. If the VPC includes a default route to that NAT Gateway instead of the Internet Gateway, the replication instance will instead appear to contact the Database Endpoint using the public IP address of the Internet Gateway\. In this case, the ingress to the Database Endpoint outside the VPC needs to allow ingress from the NAT address instead of the Replication Instance’s public IP Address\. 

## CDC Stuck After Full Load<a name="CHAP_Troubleshooting.General.CDCStuck"></a>

Slow or stuck replication changes can occur after a full load migration when several AWS DMS settings conflict with each other\. For example, if the **Target table preparation mode** parameter is set to **Do nothing** or **Truncate**, then you have instructed AWS DMS to do no setup on the target tables, including creating primary and unique indexes\. If you haven't created primary or unique keys on the target tables, then AWS DMS must do a full table scan for each update, which can significantly impact performance\.

## Primary Key Violation Errors When Restarting a Task<a name="CHAP_Troubleshooting.General.PKErrors"></a>

This error can occur when data remains in the target database from a previous migration task\. If the **Target table preparation mode** parameter is set to **Do nothing**, AWS DMS does not do any preparation on the target table, including cleaning up data inserted from a previous task\. In order to restart your task and avoid these errors, you must remove rows inserted into the target tables from the previous running of the task\.

## Initial Load of Schema Fails<a name="CHAP_Troubleshooting.General.SchemaLoadFail"></a>

If your initial load of your schemas fails with an error of `Operation:getSchemaListDetails:errType=, status=0, errMessage=, errDetails=`, then the user account used by AWS DMS to connect to the source endpoint does not have the necessary permissions\. 

## Tasks Failing With Unknown Error<a name="CHAP_Troubleshooting.General.TasksFail"></a>

The cause of these types of error can be varied, but often we find that the issue involves insufficient resources allocated to the AWS DMS replication instance\. Check the replication instance's use of CPU, memory, swap files, and IOPS to ensure your instance has enough resources to perform the migration\. For more information on monitoring, see [Data Migration Service Metrics](CHAP_Monitoring.md#CHAP_Monitoring.Metrics)\.

## Task Restart Loads Tables From the Beginning<a name="CHAP_Troubleshooting.General.RestartLoad"></a>

 AWS DMS restarts table loading from the beginning when it has not finished the initial load of a table\. When a task is restarted, AWS DMS does not reload tables that completed the initial load but will reload tables from the beginning when the initial load did not complete\.

## Number of Tables Per Task<a name="CHAP_Troubleshooting.General.TableLimit"></a>

While there is no set limit on the number of tables per replication task, we have generally found that limiting the number of tables in a task to less than 60,000 is a good rule of thumb\. Resource use can often be a bottleneck when a single task uses more than 60,000 tables\. 

## Troubleshooting Oracle Specific Issues<a name="CHAP_Troubleshooting.Oracle"></a>

The following issues are specific to using AWS DMS with Oracle databases\.

**Topics**
+ [Pulling Data from Views](#CHAP_Troubleshooting.Oracle.Views)
+ [Migrating LOBs from Oracle 12c](#CHAP_Troubleshooting.Oracle.12cLOBs)
+ [Switching Between Oracle LogMiner and Binary Reader](#CHAP_Troubleshooting.Oracle.LogMinerBinaryReader)
+ [Error: Oracle CDC stopped 122301 Oracle CDC maximum retry counter exceeded\.](#CHAP_Troubleshooting.Oracle.CDCStopped)
+ [Automatically Add Supplemental Logging to an Oracle Source Endpoint](#CHAP_Troubleshooting.Oracle.AutoSupplLogging)
+ [LOB Changes not being Captured](#CHAP_Troubleshooting.Oracle.LOBChanges)
+ [Error: ORA\-12899: value too large for column <column\-name>](#CHAP_Troubleshooting.Oracle.ORA12899)
+ [NUMBER data type being misinterpreted](#CHAP_Troubleshooting.Oracle.Numbers)

### Pulling Data from Views<a name="CHAP_Troubleshooting.Oracle.Views"></a>

You can pull data once from a view; you cannot use it for ongoing replication\. To be able to extract data from views, you must add the following code to the **Extra connection attributes** in the **Advanced** section of the Oracle source endpoint\. Note that when you extract data from a view, the view is shown as a table on the target schema\.

```
exposeViews=true
```

### Migrating LOBs from Oracle 12c<a name="CHAP_Troubleshooting.Oracle.12cLOBs"></a>

AWS DMS can use two methods to capture changes to an Oracle database, Binary Reader and Oracle LogMiner\. By default, AWS DMS uses Oracle LogMiner to capture changes\. However, on Oracle 12c, Oracle LogMiner does not support LOB columns\. To capture changes to LOB columns on Oracle 12c, use Binary Reader\.

### Switching Between Oracle LogMiner and Binary Reader<a name="CHAP_Troubleshooting.Oracle.LogMinerBinaryReader"></a>

AWS DMS can use two methods to capture changes to a source Oracle database, Binary Reader and Oracle LogMiner\. Oracle LogMiner is the default\. To switch to using Binary Reader for capturing changes, do the following:

**To use Binary Reader for capturing changes**

1.  Sign in to the AWS Management Console and select DMS\. 

1. Select **Endpoints**\.

1. Select the Oracle source endpoint that you want to use Binary Reader\.

1. Select **Modify**\.

1. Select Advanced, and then add the following code to the Extra connection attributes text box:

   ```
   useLogminerReader=N
   ```

1. Use an Oracle developer tool such as SQL\-Plus to grant the following additional privilege to the AWS DMS user account used to connect to the Oracle endpoint:

   ```
   SELECT ON V_$TRANSPORTABLE_PLATFORM
   ```

### Error: Oracle CDC stopped 122301 Oracle CDC maximum retry counter exceeded\.<a name="CHAP_Troubleshooting.Oracle.CDCStopped"></a>

This error occurs when the needed Oracle archive logs have been removed from your server before AWS DMS was able to use them to capture changes\. Increase your log retention policies on your database server\. For an Amazon RDS database, run the following procedure to increase log retention\. For example, the following code increases log retention on an Amazon RDS DB instance to 24 hours\.

```
Exec rdsadmin.rdsadmin_util.set_configuration(‘archivelog retention hours’,24);
```

### Automatically Add Supplemental Logging to an Oracle Source Endpoint<a name="CHAP_Troubleshooting.Oracle.AutoSupplLogging"></a>

By default, AWS DMS has supplemental logging turned off\. To automatically turn on supplemental logging for a source Oracle endpoint, do the following:

**To add supplemental logging to a source Oracle endpoint**

1.  Sign in to the AWS Management Console and select **DMS**\. 

1. Select **Endpoints**\.

1. Select the Oracle source endpoint that you want to add supplemental logging to\.

1. Select **Modify**\.

1. Select **Advanced**, and then add the following code to the **Extra connection attributes** text box:

   ```
   addSupplementalLogging=Y
   ```

1. Choose **Modify**\.

### LOB Changes not being Captured<a name="CHAP_Troubleshooting.Oracle.LOBChanges"></a>

Currently, a table must have a primary key for AWS DMS to capture LOB changes\. If a table that contains LOBs doesn't have a primary key, there are several actions you can take to capture LOB changes:
+ Add a primary key to the table\. This can be as simple as adding an ID column and populating it with a sequence using a trigger\.
+ Create a materialized view of the table that includes a system generated ID as the primary key and migrate the materialized view rather than the table\.
+ Create a logical standby, add a primary key to the table, and migrate from the logical standby\.

### Error: ORA\-12899: value too large for column <column\-name><a name="CHAP_Troubleshooting.Oracle.ORA12899"></a>

The error "ORA\-12899: value too large for column <column\-name>" is often caused by a mismatch in the character sets used by the source and target databases or when NLS settings differ between the two databases\. A common cause of this error is when the source database NLS\_LENGTH\_SEMANTICS parameter is set to CHAR and the target database NLS\_LENGTH\_SEMANTICS parameter is set to BYTE\.

### NUMBER data type being misinterpreted<a name="CHAP_Troubleshooting.Oracle.Numbers"></a>

The Oracle NUMBER data type is converted into various AWS DMS datatypes, depending on the precision and scale of NUMBER\. These conversions are documented here [Using an Oracle Database as a Source for AWS DMSUsing an IBM Db2 for Linux, Unix, and Windows Database \(Db2 LUW\) as a Source for AWS DMS](CHAP_Source.Oracle.md)\. The way the NUMBER type is converted can also be affected by using extra connection attributes for the source Oracle endpoint\. These extra connection attributes are documented in [Extra Connection Attributes When Using Oracle as a Source for AWS DMS](CHAP_Source.Oracle.md#CHAP_Source.Oracle.ConnectionAttrib)\.

## Troubleshooting MySQL Specific Issues<a name="CHAP_Troubleshooting.MySQL"></a>

The following issues are specific to using AWS DMS with MySQL databases\.

**Topics**
+ [CDC Task Failing for Amazon RDS DB Instance Endpoint Because Binary Logging Disabled](#CHAP_Troubleshooting.MySQL.CDCTaskFail)
+ [Connections to a target MySQL instance are disconnected during a task](#CHAP_Troubleshooting.MySQL.ConnectionDisconnect)
+ [Adding Autocommit to a MySQL\-compatible Endpoint](#CHAP_Troubleshooting.MySQL.Autocommit)
+ [Disable Foreign Keys on a Target MySQL\-compatible Endpoint](#CHAP_Troubleshooting.MySQL.DisableForeignKeys)
+ [Characters Replaced with Question Mark](#CHAP_Troubleshooting.MySQL.CharacterReplacement)
+ ["Bad event" Log Entries](#CHAP_Troubleshooting.MySQL.BadEvent)
+ [Change Data Capture with MySQL 5\.5](#CHAP_Troubleshooting.MySQL.MySQL55CDC)
+ [Increasing Binary Log Retention for Amazon RDS DB Instances](#CHAP_Troubleshooting.MySQL.BinLogRetention)
+ [Log Message: Some changes from the source database had no impact when applied to the target database\.](#CHAP_Troubleshooting.MySQL.NoImpact)
+ [Error: Identifier too long](#CHAP_Troubleshooting.MySQL.IDTooLong)
+ [Error: Unsupported Character Set Causes Field Data Conversion to Fail](#CHAP_Troubleshooting.MySQL.UnsupportedCharacterSet)
+ [Error: Codepage 1252 to UTF8 \[120112\] A field data conversion failed](#CHAP_Troubleshooting.MySQL.DataConversionFailed)

### CDC Task Failing for Amazon RDS DB Instance Endpoint Because Binary Logging Disabled<a name="CHAP_Troubleshooting.MySQL.CDCTaskFail"></a>

This issue occurs with Amazon RDS DB instances because automated backups are disabled\. Enable automatic backups by setting the backup retention period to a non\-zero value\.

### Connections to a target MySQL instance are disconnected during a task<a name="CHAP_Troubleshooting.MySQL.ConnectionDisconnect"></a>

If you have a task with LOBs that is getting disconnected from a MySQL target with the following type of errors in the task log, you might need to adjust some of your task settings\. 

```
[TARGET_LOAD ]E: RetCode: SQL_ERROR SqlState: 08S01 NativeError: 
2013 Message: [MySQL][ODBC 5.3(w) Driver][mysqld-5.7.16-log]Lost connection 
to MySQL server during query [122502] ODBC general error.
```

```
 [TARGET_LOAD ]E: RetCode: SQL_ERROR SqlState: HY000 NativeError: 
2006 Message: [MySQL][ODBC 5.3(w) Driver]MySQL server has gone away 
[122502] ODBC general error.
```

To solve the issue where a task is being disconnected from a MySQL target, do the following:
+ Check that you have your database variable `max_allowed_packet` set large enough to hold your largest LOB\.
+ Check that you have the following variables set to have a large timeout value\. We suggest you use a value of at least 5 minutes for each of these variables\.
  + `net_read_timeout` 
  + `net_write_timeout` 
  + `wait_timeout` 
  + `interactive_timeout` 

### Adding Autocommit to a MySQL\-compatible Endpoint<a name="CHAP_Troubleshooting.MySQL.Autocommit"></a>

**To add autocommit to a target MySQL\-compatible endpoint**

1.  Sign in to the AWS Management Console and select **DMS**\. 

1. Select **Endpoints**\.

1. Select the MySQL\-compatible target endpoint that you want to add autocommit to\.

1. Select **Modify**\.

1. Select **Advanced**, and then add the following code to the **Extra connection attributes** text box:

   ```
   Initstmt= SET AUTOCOMMIT=1
   ```

1. Choose **Modify**\.

### Disable Foreign Keys on a Target MySQL\-compatible Endpoint<a name="CHAP_Troubleshooting.MySQL.DisableForeignKeys"></a>

You can disable foreign key checks on MySQL by adding the following to the **Extra Connection Attributes** in the **Advanced** section of the target MySQL, Amazon Aurora with MySQL compatibility, or MariaDB endpoint\.

**To disable foreign keys on a target MySQL\-compatible endpoint**

1.  Sign in to the AWS Management Console and select **DMS**\. 

1. Select **Endpoints**\.

1. Select the MySQL, Aurora MySQL, or MariaDB target endpoint that you want to disable foreign keys\.

1. Select **Modify**\.

1. Select **Advanced**, and then add the following code to the **Extra connection attributes** text box:

   ```
   Initstmt=SET FOREIGN_KEY_CHECKS=0
   ```

1. Choose **Modify**\.

### Characters Replaced with Question Mark<a name="CHAP_Troubleshooting.MySQL.CharacterReplacement"></a>

The most common situation that causes this issue is when the source endpoint characters have been encoded by a character set that AWS DMS doesn't support\. For example, AWS DMS does not support the UTF8MB4 character set\.

### "Bad event" Log Entries<a name="CHAP_Troubleshooting.MySQL.BadEvent"></a>

"Bad event" entries in the migration logs usually indicate that an unsupported DDL operation was attempted on the source database endpoint\. Unsupported DDL operations cause an event that the replication instance cannot skip so a bad event is logged\. To fix this issue, restart the task from the beginning, which will reload the tables and will start capturing changes at a point after the unsupported DDL operation was issued\.

### Change Data Capture with MySQL 5\.5<a name="CHAP_Troubleshooting.MySQL.MySQL55CDC"></a>

AWS DMS change data capture \(CDC\) for Amazon RDS MySQL\-compatible databases requires full image row\-based binary logging, which is not supported in MySQL version 5\.5 or lower\. To use AWS DMS CDC, you must up upgrade your Amazon RDS DB instance to MySQL version 5\.6\.

### Increasing Binary Log Retention for Amazon RDS DB Instances<a name="CHAP_Troubleshooting.MySQL.BinLogRetention"></a>

AWS DMS requires the retention of binary log files for change data capture\. To increase log retention on an Amazon RDS DB instance, use the following procedure\. The following example increases the binary log retention to 24 hours\. 

```
Call mysql.rds_set_configuration(‘binlog retention hours’, 24);
```

### Log Message: Some changes from the source database had no impact when applied to the target database\.<a name="CHAP_Troubleshooting.MySQL.NoImpact"></a>

When AWS DMS updates a MySQL database column’s value to its existing value, a message of `zero rows affected` is returned from MySQL\. This behavior is unlike other database engines such as Oracle and SQL Server that perform an update of one row, even when the replacing value is the same as the current one\. 

### Error: Identifier too long<a name="CHAP_Troubleshooting.MySQL.IDTooLong"></a>

The following error occurs when an identifier is too long:

```
TARGET_LOAD E: RetCode: SQL_ERROR SqlState: HY000 NativeError: 
1059 Message: MySQLhttp://ODBC 5.3(w) Driverhttp://mysqld-5.6.10Identifier 
name ‘<name>’ is too long 122502 ODBC general error. (ar_odbc_stmt.c:4054)
```

When AWS DMS is set to create the tables and primary keys in the target database, it currently does not use the same names for the Primary Keys that were used in the source database\. Instead, AWS DMS creates the Primary Key name based on the tables name\. When the table name is long, the auto\-generated identifier created can be longer than the allowed limits for MySQL\. The solve this issue, currently, pre\-create the tables and Primary Keys in the target database and use a task with the task setting **Target table preparation mode** set to **Do nothing** or **Truncate** to populate the target tables\.

### Error: Unsupported Character Set Causes Field Data Conversion to Fail<a name="CHAP_Troubleshooting.MySQL.UnsupportedCharacterSet"></a>

The following error occurs when an unsupported character set causes a field data conversion to fail:

```
"[SOURCE_CAPTURE ]E: Column ‘<column name>' uses an unsupported character set [120112] 
A field data conversion failed. (mysql_endpoint_capture.c:2154)
```

This error often occurs because of tables or databases using UTF8MB4 encoding\. AWS DMS does not support the UTF8MB4 character set\. In addition, check your database's parameters related to connections\. The following command can be used to see these parameters:

```
SHOW VARIABLES LIKE '%char%';
```

### Error: Codepage 1252 to UTF8 \[120112\] A field data conversion failed<a name="CHAP_Troubleshooting.MySQL.DataConversionFailed"></a>

 The following error can occur during a migration if you have non codepage\-1252 characters in the source MySQL database\.

```
  
[SOURCE_CAPTURE ]E: Error converting column ‘column_xyz’ in table
'table_xyz with codepage 1252 to UTF8 [120112] A field data conversion failed. 
(mysql_endpoint_capture.c:2248)
```

 As a workaround, you can use the `CharsetMapping` extra connection attribute with your source MySQL endpoint to specify character set mapping\. You might need to restart the AWS DMS migration task from the beginning if you add this extra connection attribute\. 

For example, the following extra connection attribute could be used for a MySQL source endpoint where the source character set is `utf8` or `latin1`\. 65001 is the UTF8 code page identifier\. 

```
   
CharsetMapping=utf8,65001
CharsetMapping=latin1,65001
```

## Troubleshooting PostgreSQL Specific Issues<a name="CHAP_Troubleshooting.PostgreSQL"></a>

The following issues are specific to using AWS DMS with PostgreSQL databases\.

**Topics**
+ [JSON data types being truncated](#CHAP_Troubleshooting.PostgreSQL.JSONTruncation)
+ [Columns of a user defined data type not being migrated correctly](#CHAP_Troubleshooting.PostgreSQL.UserDefinedDataType)
+ [Error: No schema has been selected to create in](#CHAP_Troubleshooting.PostgreSQL.NoSchema)
+ [Deletes and updates to a table are not being replicated using CDC](#CHAP_Troubleshooting.PostgreSQL.DeletesNotReplicated)
+ [Truncate statements are not being propagated](#CHAP_Troubleshooting.PostgreSQL.Truncate)
+ [Preventing PostgreSQL from capturing DDL](#CHAP_Troubleshooting.PostgreSQL.NoCaptureDDL)
+ [Selecting the schema where database objects for capturing DDL are created](#CHAP_Troubleshooting.PostgreSQL.SchemaDDL)
+ [Oracle tables missing after migrating to PostgreSQL](#CHAP_Troubleshooting.PostgreSQL.OracleTablesMissing)
+ [Task Using View as a Source Has No Rows Copied](#CHAP_Troubleshooting.PostgreSQL.ViewTask)

### JSON data types being truncated<a name="CHAP_Troubleshooting.PostgreSQL.JSONTruncation"></a>

 AWS DMS treats the JSON data type in PostgreSQL as an LOB data type column\. This means that the LOB size limitation when you use Limited LOB mode applies to JSON data\. For example, if Limited LOB mode is set to 4096 KB, any JSON data larger than 4096 KB is truncated at the 4096 KB limit and will fail the validation test in PostgreSQL\.

For example, the following log information shows JSON that was truncated due to the Limited LOB mode setting and failed validation\.

```
03:00:49
2017-09-19T03:00:49 [TARGET_APPLY ]E: Failed to execute statement: 
  'UPDATE "public"."delivery_options_quotes" SET "id"=? , "enabled"=? , 
  "new_cart_id"=? , "order_id"=? , "user_id"=? , "zone_id"=? , "quotes"=? , 
  "start_at"=? , "end_at"=? , "last_quoted_at"=? , "created_at"=? , 
  "updated_at"=? WHERE "id"=? ' [1022502] (ar_odbc_stmt
2017-09-19T03:00:49 [TARGET_APPLY ]E: Failed to execute statement: 
  'UPDATE "public"."delivery_options_quotes" SET "id"=? , "enabled"=? , 
  "new_cart_id"=? , "order_id"=? , "user_id"=? , "zone_id"=? , "quotes"=? , 
  "start_at"=? , "end_at"=? , "last_quoted_at"=? , "created_at"=? , 
  "updated_at"=? WHERE "id"=? ' [1022502] (ar_odbc_stmt.c:2415)

03:00:49
2017-09-19T03:00:49 [TARGET_APPLY ]E: RetCode: SQL_ERROR SqlState: 
  22P02 NativeError: 1 Message: ERROR: invalid input syntax for type json;, 
  Error while executing the query [1022502] (ar_odbc_stmt.c:2421)
2017-09-19T03:00:49 [TARGET_APPLY ]E: RetCode: SQL_ERROR SqlState: 
  22P02 NativeError: 1 Message: ERROR: invalid input syntax for type json;, 
  Error while executing the query [1022502] (ar_odbc_stmt.c:2421)
```

### Columns of a user defined data type not being migrated correctly<a name="CHAP_Troubleshooting.PostgreSQL.UserDefinedDataType"></a>

When replicating from a PostgreSQL source, AWS DMS creates the target table with the same data types for all columns, apart from columns with user\-defined data types\. In such cases, the data type is created as "character varying" in the target\. 

### Error: No schema has been selected to create in<a name="CHAP_Troubleshooting.PostgreSQL.NoSchema"></a>

The error "SQL\_ERROR SqlState: 3F000 NativeError: 7 Message: ERROR: no schema has been selected to create in" can occur when your JSON table mapping contains a wild card value for the schema but the source database doesn't support that value\.

### Deletes and updates to a table are not being replicated using CDC<a name="CHAP_Troubleshooting.PostgreSQL.DeletesNotReplicated"></a>

Delete and Update operations during change data capture \(CDC\) are ignored if the source table does not have a primary key\. AWS DMS supports change data capture \(CDC\) for PostgreSQL tables with primary keys; if a table does not have a primary key, the WAL logs do not include a before image of the database row and AWS DMS cannot update the table\. Create a primary key on the source table if you want delete operations to be replicated\.

### Truncate statements are not being propagated<a name="CHAP_Troubleshooting.PostgreSQL.Truncate"></a>

When using change data capture \(CDC\), TRUNCATE operations are not supported by AWS DMS\.

### Preventing PostgreSQL from capturing DDL<a name="CHAP_Troubleshooting.PostgreSQL.NoCaptureDDL"></a>

You can prevent a PostgreSQL target endpoint from capturing DDL statements by adding the following **Extra Connection Attribute** statement\. The **Extra Connection Attribute** parameter is available in the **Advanced** tab of the source endpoint\.

```
captureDDLs=N
```

### Selecting the schema where database objects for capturing DDL are created<a name="CHAP_Troubleshooting.PostgreSQL.SchemaDDL"></a>

You can control what schema the database objects related to capturing DDL are created in\. Add the following **Extra Connection Attribute** statement\. The **Extra Connection Attribute** parameter is available in the **Advanced** tab of the target endpoint\.

```
ddlArtifactsSchema=xyzddlschema                
```

### Oracle tables missing after migrating to PostgreSQL<a name="CHAP_Troubleshooting.PostgreSQL.OracleTablesMissing"></a>

Oracle defaults to uppercase table names while PostgreSQL defaults to lowercase table names\. When performing a migration from Oracle to PostgreSQL you will most likely need to supply transformation rules under the table mapping section of your task to convert the case of your table names\.

Your tables and data are still accessible; if you migrated your tables without using transformation rules to convert the case of your table names, you will need to enclose your table names in quotes when referencing them\.

### Task Using View as a Source Has No Rows Copied<a name="CHAP_Troubleshooting.PostgreSQL.ViewTask"></a>

A View as a PostgreSQL source endpoint is not supported by AWS DMS\.

## Troubleshooting Microsoft SQL Server Specific Issues<a name="CHAP_Troubleshooting.SQLServer"></a>

The following issues are specific to using AWS DMS with Microsoft SQL Server databases\.

**Topics**
+ [Special Permissions for AWS DMS user account to use CDC](#CHAP_Troubleshooting.SQLServer.Permissions)
+ [Errors Capturing Changes for SQL Server Database](#CHAP_Troubleshooting.SQLServer.CDCErrors)
+ [Missing Identity Columns](#CHAP_Troubleshooting.SQLServer.IdentityColumns)
+ [Error: SQL Server Does Not Support Publications](#CHAP_Troubleshooting.SQLServer.Publications)
+ [Changes Not Appearing in Target](#CHAP_Troubleshooting.SQLServer.NoChanges)

### Special Permissions for AWS DMS user account to use CDC<a name="CHAP_Troubleshooting.SQLServer.Permissions"></a>

The user account used with AWS DMS requires the SQL Server SysAdmin role in order to operate correctly when using change data capture \(CDC\)\. CDC for SQL Server is only available for on\-premises databases or databases on an EC2 instance\.

### Errors Capturing Changes for SQL Server Database<a name="CHAP_Troubleshooting.SQLServer.CDCErrors"></a>

Errors during change data capture \(CDC\) can often indicate that one of the pre\-requisites was not met\. For example, the most common overlooked pre\-requisite is a full database backup\. The task log indicates this omission with the following error:

```
SOURCE_CAPTURE E: No FULL database backup found (under the 'FULL' recovery model). 
To enable all changes to be captured, you must perform a full database backup. 
120438 Changes may be missed. (sqlserver_log_queries.c:2623)
```

Review the pre\-requisites listed for using SQL Server as a source in [Using a Microsoft SQL Server Database as a Source for AWS DMS](CHAP_Source.SQLServer.md)\.

### Missing Identity Columns<a name="CHAP_Troubleshooting.SQLServer.IdentityColumns"></a>

AWS DMS does not support identity columns when you create a target schema\. You must add them after the initial load has completed\.

### Error: SQL Server Does Not Support Publications<a name="CHAP_Troubleshooting.SQLServer.Publications"></a>

The following error is generated when you use SQL Server Express as a source endpoint:

```
RetCode: SQL_ERROR SqlState: HY000 NativeError: 21106 
Message: This edition of SQL Server does not support publications.
```

AWS DMS currently does not support SQL Server Express as a source or target\.

### Changes Not Appearing in Target<a name="CHAP_Troubleshooting.SQLServer.NoChanges"></a>

AWS DMS requires that a source SQL Server database be in either ‘FULL’ or ‘BULK LOGGED’ data recovery model in order to consistently capture changes\. The ‘SIMPLE’ model is not supported\. 

The SIMPLE recovery model logs the minimal information needed to allow users to recover their database\. All inactive log entries are automatically truncated when a checkpoint occurs\. All operations are still logged, but as soon as a checkpoint occurs the log is automatically truncated, which means that it becomes available for re\-use and older log entries can be over\-written\. When log entries are overwritten, changes cannot be captured, and that is why AWS DMS doesn't support the SIMPLE data recovery model\. For information on other required pre\-requisites for using SQL Server as a source, see [Using a Microsoft SQL Server Database as a Source for AWS DMS](CHAP_Source.SQLServer.md)\.

## Troubleshooting Amazon Redshift Specific Issues<a name="CHAP_Troubleshooting.Redshift"></a>

The following issues are specific to using AWS DMS with Amazon Redshift databases\.

**Topics**
+ [Loading into a Amazon Redshift Cluster in a Different Region Than the AWS DMS Replication Instance](#CHAP_Troubleshooting.Redshift.Regions)
+ [Error: Relation "awsdms\_apply\_exceptions" already exists](#CHAP_Troubleshooting.Redshift.AlreadyExists)
+ [Errors with Tables Whose Name Begins with "awsdms\_changes"](#CHAP_Troubleshooting.Redshift.Changes)
+ [Seeing Tables in Cluster with Names Like dms\.awsdms\_changes000000000XXXX](#CHAP_Troubleshooting.Redshift.TempTables)
+ [Permissions Required to Work with Amazon Redshift](#CHAP_Troubleshooting.Redshift.Permissions)

### Loading into a Amazon Redshift Cluster in a Different Region Than the AWS DMS Replication Instance<a name="CHAP_Troubleshooting.Redshift.Regions"></a>

This can't be done\. AWS DMS requires that the AWS DMS replication instance and a Redshift cluster be in the same region\.

### Error: Relation "awsdms\_apply\_exceptions" already exists<a name="CHAP_Troubleshooting.Redshift.AlreadyExists"></a>

The error "Relation "awsdms\_apply\_exceptions" already exists" often occurs when a Redshift endpoint is specified as a PostgreSQL endpoint\. To fix this issue, modify the endpoint and change the **Target engine** to "redshift\."

### Errors with Tables Whose Name Begins with "awsdms\_changes"<a name="CHAP_Troubleshooting.Redshift.Changes"></a>

Error messages that relate to tables with names that begin with "awsdms\_changes" often occur when two tasks that are attempting to load data into the same Amazon Redshift cluster are running concurrently\. Due to the way temporary tables are named, concurrent tasks can conflict when updating the same table\.

### Seeing Tables in Cluster with Names Like dms\.awsdms\_changes000000000XXXX<a name="CHAP_Troubleshooting.Redshift.TempTables"></a>

AWS DMS creates temporary tables when data is being loaded from files stored in S3\. The name of these temporary tables have the prefix "dms\.awsdms\_changes\." These tables are required so AWS DMS can store data when it is first loaded and before it is placed in its final target table\.

### Permissions Required to Work with Amazon Redshift<a name="CHAP_Troubleshooting.Redshift.Permissions"></a>

To use AWS DMS with Amazon Redshift, the user account you use to access Amazon Redshift must have the following permissions:
+ CRUD \(Select, Insert, Update, Delete\) 
+ Bulk Load
+ Create, Alter, Drop \(if required by the task's definition\)

To see all the pre\-requisites required for using Amazon Redshift as a target, see [Using an Amazon Redshift Database as a Target for AWS Database Migration Service](CHAP_Target.Redshift.md)\.

## Troubleshooting Amazon Aurora MySQL Specific Issues<a name="CHAP_Troubleshooting.Aurora"></a>

The following issues are specific to using AWS DMS with Amazon Aurora MySQL databases\.

**Topics**
+ [Error: CHARACTER SET UTF8 fields terminated by ',' enclosed by '"' lines terminated by '\\n'](#CHAP_Troubleshooting.Aurora.ANSIQuotes)

### Error: CHARACTER SET UTF8 fields terminated by ',' enclosed by '"' lines terminated by '\\n'<a name="CHAP_Troubleshooting.Aurora.ANSIQuotes"></a>

If you are using Amazon Aurora MySQL as a target and see an error like the following in the logs, this usually indicates that you have ANSI\_QUOTES as part of the SQL\_MODE parameter\. Having ANSI\_QUOTES as part of the SQL\_MODE parameter causes double quotes to be handled like quotes and can create issues when you run a task\. To fix this error, remove ANSI\_QUOTES from the SQL\_MODE parameter\.

```
2016-11-02T14:23:48 [TARGET_LOAD ]E: Load data sql statement. load data local infile 
"/rdsdbdata/data/tasks/7XO4FJHCVON7TYTLQ6RX3CQHDU/data_files/4/LOAD000001DF.csv" into table 
`VOSPUSER`.`SANDBOX_SRC_FILE` CHARACTER SET UTF8 fields terminated by ',' 
enclosed by '"' lines terminated by '\n'( `SANDBOX_SRC_FILE_ID`,`SANDBOX_ID`,
`FILENAME`,`LOCAL_PATH`,`LINES_OF_CODE`,`INSERT_TS`,`MODIFIED_TS`,`MODIFIED_BY`,
`RECORD_VER`,`REF_GUID`,`PLATFORM_GENERATED`,`ANALYSIS_TYPE`,`SANITIZED`,`DYN_TYPE`,
`CRAWL_STATUS`,`ORIG_EXEC_UNIT_VER_ID` ) ; (provider_syntax_manager.c:2561)
```