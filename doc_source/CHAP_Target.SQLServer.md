# Using a Microsoft SQL Server database as a target for AWS Database Migration Service<a name="CHAP_Target.SQLServer"></a>

You can migrate data to Microsoft SQL Server databases using AWS DMS\. With an SQL Server database as a target, you can migrate data from either another SQL Server database or one of the other supported databases\.

For on\-premises and Amazon EC2 instance databases, AWS DMS supports as a target SQL Server versions 2005, 2008, 2008R2, 2012, 2014, 2016, 2017, and 2019\. The Enterprise, Standard, Workgroup, Developer, and Web editions are supported by AWS DMS\.

For Amazon RDS instance databases, AWS DMS supports as a target SQL Server versions 2008R2, 2012, 2014, 2016, 2017, and 2019\. The Enterprise, Standard, Workgroup, Developer, and Web editions are supported by AWS DMS\.

For additional details on working with AWS DMS and SQL Server target databases, see the following\.

## Limitations on using SQL Server as a target for AWS Database Migration Service<a name="CHAP_Target.SQLServer.Limitations"></a>

The following limitations apply when using a SQL Server database as a target for AWS DMS:
+ When you manually create a SQL Server target table with a computed column, full load replication is not supported when using the BCP bulk\-copy utility\. To use full load replication, disable BCP loading by setting the extra connection attribute \(ECA\) `'useBCPFullLoad=false'` on the endpoint\. For information about setting ECAs on endpoints, see [Creating source and target endpoints](CHAP_Endpoints.Creating.md)\. For more information on working with BCP, see the [Microsoft SQL Server documentation](https://docs.microsoft.com/en-us/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)\.
+ When replicating tables with SQL Server spatial data types \(GEOMETRY and GEOGRAPHY\), AWS DMS replaces any spatial reference identifier \(SRID\) that you might have inserted with the default SRID\. The default SRID is 0 for GEOMETRY and 4326 for GEOGRAPHY\.
+ Temporal tables are not supported\. Migrating temporal tables may work with a replication\-only task in transactional apply mode if those tables are manually created on the target\.
+ Currently, `boolean` data types in a PostgreSQL source are migrated to a SQLServer target as the `bit` data type with inconsistent values\. As a workaround, precreate the table with a `VARCHAR(1)` data type for the column \(or let AWS DMS create the table\)\. Then have downstream processing treat an "F" as False and a "T" as True\.
+ AWS DMS doesn't support change processing to set column nullability \(using the `ALTER COLUMN [SET|DROP] NOT NULL` clause with `ALTER TABLE` statements\)\.

## Security requirements when using SQL Server as a target for AWS Database Migration Service<a name="CHAP_Target.SQLServer.Security"></a>

The following describes the security requirements for using AWS DMS with a Microsoft SQL Server target:
+ The AWS DMS user account must have at least the `db_owner` user role on the SQL Server database that you are connecting to\.
+ A SQL Server system administrator must provide this permission to all AWS DMS user accounts\.

## Extra connection attributes when using SQL Server as a target for AWS DMS<a name="CHAP_Target.SQLServer.ConnectionAttrib"></a>

You can use extra connection attributes to configure your SQL Server target\. You specify these settings when you create the target endpoint\. If you have multiple connection attribute settings, separate them from each other by semicolons with no additional white space\.

The following table shows the extra connection attributes that you can use when SQL Server is the target\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Target.SQLServer.html)

## Target data types for Microsoft SQL Server<a name="CHAP_Target.SQLServer.DataTypes"></a>

The following table shows the Microsoft SQL Server target data types that are supported when using AWS DMS and the default mapping from AWS DMS data types\. For additional information about AWS DMS data types, see [Data types for AWS Database Migration Service](CHAP_Reference.DataTypes.md)\.


|  AWS DMS data type  |  SQL Server data type  | 
| --- | --- | 
|  BOOLEAN  |  TINYINT  | 
|  BYTES  |  VARBINARY\(length\)  | 
|  DATE  |  For SQL Server 2008 and later, use DATE\. For earlier versions, if the scale is 3 or less use DATETIME\. In all other cases, use VARCHAR \(37\)\.  | 
|  TIME  |  For SQL Server 2008 and later, use DATETIME2 \(%d\)\. For earlier versions, if the scale is 3 or less use DATETIME\. In all other cases, use VARCHAR \(37\)\.  | 
|  DATETIME  |  For SQL Server 2008 and later, use DATETIME2 \(scale\)\.  For earlier versions, if the scale is 3 or less use DATETIME\. In all other cases, use VARCHAR \(37\)\.  | 
|  INT1  | SMALLINT | 
|  INT2  |  SMALLINT  | 
|  INT4  | INT | 
|  INT8  |  BIGINT  | 
|  NUMERIC  |  NUMERIC \(p,s\)  | 
|  REAL4  |  REAL  | 
|  REAL8  | FLOAT | 
|  STRING  |  If the column is a date or time column, then do the following:  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Target.SQLServer.html) If the column is not a date or time column, use VARCHAR \(length\)\.  | 
|  UINT1  |  TINYINT  | 
|  UINT2  |  SMALLINT  | 
|  UINT4  |  INT  | 
|  UINT8  |  BIGINT  | 
|  WSTRING  |  NVARCHAR \(length\)  | 
|  BLOB  |  VARBINARY\(max\) IMAGE To use this data type with AWS DMS, you must enable the use of BLOBs for a specific task\. AWS DMS supports BLOB data types only in tables that include a primary key\.  | 
|  CLOB  |  VARCHAR\(max\) To use this data type with AWS DMS, you must enable the use of CLOBs for a specific task\. During change data capture \(CDC\), AWS DMS supports CLOB data types only in tables that include a primary key\.  | 
|  NCLOB  |  NVARCHAR\(max\) To use this data type with AWS DMS, you must enable the use of NCLOBs for a specific task\. During CDC, AWS DMS supports NCLOB data types only in tables that include a primary key\.  | 