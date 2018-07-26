# Using a Microsoft SQL Server Database as a Target for AWS Database Migration Service<a name="CHAP_Target.SQLServer"></a>

You can migrate data to Microsoft SQL Server databases using AWS DMS\. With an SQL Server database as a target, you can migrate data from either another SQL Server database or one of the other supported databases\.

For on\-premises and Amazon EC2 instance databases, AWS DMS supports as a target SQL Server versions 2005, 2008, 2008R2, 2012, 2014, and 2016, for the Enterprise, Standard, Workgroup, and Developer editions\. The Web and Express editions are not supported\.

For Amazon RDS instance databases, AWS DMS supports as a target SQL Server versions 2008R2, 2012, 2014, and 2016, for the Enterprise, Standard, Workgroup, and Developer editions are supported\. The Web and Express editions are not supported\.

For additional details on working with AWS DMS and SQL Server target databases, see the following\.

**Topics**
+ [Limitations on Using SQL Server as a Target for AWS Database Migration Service](#CHAP_Target.SQLServer.Limitations)
+ [Security Requirements When Using SQL Server as a Target for AWS Database Migration Service](#CHAP_Target.SQLServer.Security)
+ [Extra Connection Attributes When Using SQLServer as a Target for AWS DMS](#CHAP_Target.SQLServer.ConnectionAttrib)
+ [Target Data Types for Microsoft SQL Server](#CHAP_Target.SQLServer.DataTypes)

## Limitations on Using SQL Server as a Target for AWS Database Migration Service<a name="CHAP_Target.SQLServer.Limitations"></a>

The following limitations apply when using a SQL Server database as a target for AWS DMS:
+ When you manually create a SQL Server target table with a computed column, full load replication is not supported when using the BCP bulk\-copy utility\. To use full load replication, disable the **Use BCP for loading tables** option in the console's **Advanced** tab\. For more information on working with BCP, see the [Microsoft SQL Server documentation](https://docs.microsoft.com/en-us/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)\.
+ When replicating tables with SQL Server spatial data types \(GEOMETRY and GEOGRAPHY\), AWS DMS replaces any spatial reference identifier \(SRID\) that you might have inserted with the default SRID\. The default SRID is 0 for GEOMETRY and 4326 for GEOGRAPHY\.
+ Temporal tables are not supported\. Migrating temporal tables may work with a replication\-only task in transnational apply mode if those tables are manually created on the target\.

## Security Requirements When Using SQL Server as a Target for AWS Database Migration Service<a name="CHAP_Target.SQLServer.Security"></a>

The following describes the security requirements for using AWS DMS with a Microsoft SQL Server target\.
+ AWS DMS user account must have at least the `db_owner` user role on the Microsoft SQL Server database you are connecting to\.
+ A Microsoft SQL Server system administrator must provide this permission to all AWS DMS user accounts\.

## Extra Connection Attributes When Using SQLServer as a Target for AWS DMS<a name="CHAP_Target.SQLServer.ConnectionAttrib"></a>

You can use extra connection attributes to configure your SQL Server target\. You specify these settings when you create the target endpoint\. Multiple extra connection attribute settings should be separated by a semicolon\.

The following table shows the extra connection attributes that you can use when SQL Server is the target\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Target.SQLServer.html)

## Target Data Types for Microsoft SQL Server<a name="CHAP_Target.SQLServer.DataTypes"></a>

The following table shows the Microsoft SQL Server target data types that are supported when using AWS DMS and the default mapping from AWS DMS data types\. For additional information about AWS DMS data types, see [Data Types for AWS Database Migration Service](CHAP_Reference.DataTypes.md)\.


|  AWS DMS Data Type  |  SQL Server Data Type  | 
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
|  NUMERIC  |  NUMBER \(p,s\)  | 
|  REAL4  |  REAL  | 
|  REAL8  | FLOAT | 
|  STRING  |  If the column is a date or time column, then do the following:  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Target.SQLServer.html) If the column is not a date or time column, use VARCHAR \(length\)\.  | 
|  UINT1  |  TINYINT  | 
|  UINT2  |  SMALLINT  | 
|  UINT4  |  INT  | 
|  UINT8  |  BIGINT  | 
|  WSTRING  |  NVARCHAR \(length\)  | 
|  BLOB  |  VARBINARY\(max\) IMAGE To use this data type with AWS DMS, you must enable the use of BLOBs for a specific task\. AWS DMS supports BLOB data types only in tables that include a primary key\.  | 
|  CLOB  |  VARCHAR\(max\) To use this data type with AWS DMS, you must enable the use of CLOBs for a specific task\. During CDC, AWS DMS supports CLOB data types only in tables that include a primary key\.  | 
|  NCLOB  |  NVARCHAR\(max\) To use this data type with AWS DMS, you must enable the use of NCLOBs for a specific task\. During CDC, AWS DMS supports NCLOB data types only in tables that include a primary key\.  | 