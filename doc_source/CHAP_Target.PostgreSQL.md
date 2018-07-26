# Using a PostgreSQL Database as a Target for AWS Database Migration Service<a name="CHAP_Target.PostgreSQL"></a>

You can migrate data to PostgreSQL databases using AWS DMS, either from another PostgreSQL database or from one of the other supported databases\. PostgreSQL versions 9\.4 and later are supported for on\-premises, Amazon RDS, Amazon Aurora with PostgreSQL compatibility, and EC2 instance databases\.

AWS DMS takes a table\-by\-table approach when migrating data from source to target in the Full Load phase\. Table order during the full load phase cannot be guaranteed\. Tables are out of sync during the full load phase and while cached transactions for individual tables are being applied\. As a result, active referential integrity constraints can result in task failure during the full load phase\.

In PostgreSQL, foreign keys \(referential integrity constraints\) are implemented using triggers\. During the full load phase, AWS DMS loads each table one at a time\. We strongly recommend that you disable foreign key constraints during a full load, using one of the following methods:
+ Temporarily disable all triggers from the instance, and finish the full load\.
+ Use the `session_replication_role` parameter in PostgreSQL\.

At any given time, a trigger can be in one of the following states: `origin`, `replica`, `always`, or `disabled`\. When the `session_replication_role` parameter is set to `replica`, only triggers in the `replica` state are active, and they are fired when they are called\. Otherwise, the triggers remain inactive\. 

PostgreSQL has a failsafe mechanism to prevent a table from being truncated, even when `session_replication_role` is set\. You can use this as an alternative to disabling triggers, to help the full load run to completion\. To do this, set the target table preparation mode to `DO_NOTHING`\. Otherwise, DROP and TRUNCATE operations fail when there are foreign key constraints\.

In Amazon RDS, you can control set this parameter using a parameter group\. For a PostgreSQL instance running on Amazon EC2, you can set the parameter directly\.

For additional details on working with a PostgreSQL database as a target for AWS DMS, see the following sections: 

**Topics**
+ [Limitations on Using PostgreSQL as a Target for AWS Database Migration Service](#CHAP_Target.PostgreSQL.Limitations)
+ [Security Requirements When Using a PostgreSQL Database as a Target for AWS Database Migration Service](#CHAP_Target.PostgreSQL.Security)
+ [Extra Connection Attributes When Using PostgreSQL as a Target for AWS DMS](#CHAP_Target.PostgreSQL.ConnectionAttrib)
+ [Target Data Types for PostgreSQL](#CHAP_Target.PostgreSQL.DataTypes)

## Limitations on Using PostgreSQL as a Target for AWS Database Migration Service<a name="CHAP_Target.PostgreSQL.Limitations"></a>

The following limitations apply when using a PostgreSQL database as a target for AWS DMS:
+ The JSON data type is converted to the Native CLOB data type\.
+ In an Oracle to PostgreSQL migration, if a column in Oracle contains a NULL character \(Hex value U\+0000\), AWS DMS converts the NULL character to a space \(Hex value U\+0020\)\. This is due to a PostgreSQL limitation\.

## Security Requirements When Using a PostgreSQL Database as a Target for AWS Database Migration Service<a name="CHAP_Target.PostgreSQL.Security"></a>

For security purposes, the user account used for the data migration must be a registered user in any PostgreSQL database that you use as a target\.

## Extra Connection Attributes When Using PostgreSQL as a Target for AWS DMS<a name="CHAP_Target.PostgreSQL.ConnectionAttrib"></a>

You can use extra connection attributes to configure your PostgreSQL target\. You specify these settings when you create the target endpoint\. Multiple extra connection attribute settings should be separated by a semicolon\.

The following table shows the extra connection attributes you can use to configure PostgreSQL as a target for AWS DMS\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Target.PostgreSQL.html)

## Target Data Types for PostgreSQL<a name="CHAP_Target.PostgreSQL.DataTypes"></a>

The PostgreSQL database endpoint for AWS DMS supports most PostgreSQL database data types\. The following table shows the PostgreSQL database target data types that are supported when using AWS DMS and the default mapping from AWS DMS data types\. Unsupported data types are listed following the table\.

For additional information about AWS DMS data types, see [Data Types for AWS Database Migration Service](CHAP_Reference.DataTypes.md)\.


|  AWS DMS Data Type  |  PostgreSQL Data Type  | 
| --- | --- | 
|  BOOL  |  BOOL  | 
|  BYTES  |  BYTEA  | 
|  DATE  |  DATE  | 
|  TIME  |  TIME  | 
|  TIMESTAMP  |  If the scale is from 0 through 6, then use TIMESTAMP\. If the scale is from 7 through 9, then use VARCHAR \(37\)\.  | 
|  INT1  |  SMALLINT  | 
|  INT2  |  SMALLINT  | 
|  INT4  |  INTEGER  | 
|  INT8  |  BIGINT  | 
|  NUMERIC   |  DECIMAL \(P,S\)  | 
|  REAL4  |  FLOAT4  | 
|  REAL8  |  FLOAT8  | 
|  STRING  |  If the length is from 1 through 21,845, then use VARCHAR \(length in bytes\)\.  If the length is 21,846 through 2,147,483,647, then use VARCHAR \(65535\)\.  | 
|  UINT1  |  SMALLINT  | 
|  UINT2  |  INTEGER  | 
|  UINT4  |  BIGINT  | 
|  UINT8  |  BIGINT  | 
|  WSTRING  |  If the length is from 1 through 21,845, then use VARCHAR \(length in bytes\)\.  If the length is 21,846 through 2,147,483,647, then use VARCHAR \(65535\)\.  | 
|  BCLOB  |  BYTEA  | 
|  NCLOB  |  TEXT  | 
|  CLOB  |  TEXT  | 

**Note**  
When replicating from a PostgreSQL source, AWS DMS creates the target table with the same data types for all columns, apart from columns with user\-defined data types\. In such cases, the data type is created as "character varying" in the target\.