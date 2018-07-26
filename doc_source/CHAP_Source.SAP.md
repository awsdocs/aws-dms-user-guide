# Using an SAP ASE Database as a Source for AWS DMS<a name="CHAP_Source.SAP"></a>

You can migrate data from an SAP Adaptive Server Enterprise \(ASE\) database—formerly known as Sybase—using AWS DMS\. With an SAP ASE database as a source, you can migrate data to any of the other supported AWS DMS target databases\. AWS DMS\. supports SAP ASE versions 12\.5\.3 or higher, 15, 15\.5, 15\.7, 16 and later as sources\.

For additional details on working with SAP ASE databases and AWS DMS, see the following sections\.

**Topics**
+ [Prerequisites for Using an SAP ASE Database as a Source for AWS DMS](#CHAP_Source.SAP.Prerequisites)
+ [Limitations on Using SAP ASE as a Source for AWS DMS](#CHAP_Source.SAP.Limitations)
+ [Permissions Required for Using SAP ASE as a Source for AWS DMS](#CHAP_Source.SAP.Security)
+ [Removing the Truncation Point](#CHAP_Source.SAP.Truncation)
+ [Source Data Types for SAP ASE](#CHAP_Source.SAP.DataTypes)

## Prerequisites for Using an SAP ASE Database as a Source for AWS DMS<a name="CHAP_Source.SAP.Prerequisites"></a>

For an SAP ASE database to be a source for AWS DMS, do the following:
+ Enable SAP ASE replication for tables by using the `sp_setreptable` command\.
+ Disable `RepAgent` on the SAP ASE database\.
+ To replicate to SAP ASE version 15\.7 on an Amazon EC2 instance on Microsoft Windows configured for non\-Latin characters \(for example, Chinese\), install SAP ASE 15\.7 SP121 on the target computer\.

## Limitations on Using SAP ASE as a Source for AWS DMS<a name="CHAP_Source.SAP.Limitations"></a>

The following limitations apply when using an SAP ASE database as a source for AWS DMS:
+ Only one AWS DMS task can be run for each SAP ASE database\.
+ You can't rename a table\. For example, the following command fails:

  ```
  sp_rename 'Sales.SalesRegion', 'SalesReg;
  ```
+ You can't rename a column\. For example, the following command fails: 

  ```
  sp_rename 'Sales.Sales.Region', 'RegID', 'COLUMN';
  ```
+ Zero values located at the end of binary data type strings are truncated when replicated to the target database\. For example, `0x0000000000000000000000000100000100000000` in the source table becomes `0x00000000000000000000000001000001` in the target table\.
+ If the database default is set not to allow NULL values, AWS DMS creates the target table with columns that don't allow NULL values\. Consequently, if a full load or change data capture \(CDC\) replication task contains empty values, AWS DMS throws an error\. You can prevent these errors by allowing NULL values in the source database by using the following commands\.

  ```
  sp_dboption <database name>, 'allow nulls by default', 'true'
  go
  use <database name>
  CHECKPOINT
  go
  ```
+ The `reorg rebuild` index command isn't supported\.
+ Clusters aren't supported\.

## Permissions Required for Using SAP ASE as a Source for AWS DMS<a name="CHAP_Source.SAP.Security"></a>

To use an SAP ASE database as a source in an AWS DMS task, grant the user account specified in the AWS DMS database definitions the following permissions in the SAP ASE database\. 
+ sa\_role
+ replication\_role
+ sybase\_ts\_role
+ If you enable the **Automatically enable Sybase replication** option \(in the **Advanced** tab\) when you create the SAP ASE source endpoint, also give permission to AWS DMS to run the stored procedure `sp_setreptable`\.

## Removing the Truncation Point<a name="CHAP_Source.SAP.Truncation"></a>

When a task starts, AWS DMS establishes a `$replication_truncation_point` entry in the `syslogshold` system view, indicating that a replication process is in progress\. While AWS DMS is working, it advances the replication truncation point at regular intervals, according to the amount of data that has already been copied to the target\.

After the `$replication_truncation_point` entry is established, keep the AWS DMS task running to prevent the database log from becoming excessively large\. If you want to stop the AWS DMS task permanently, remove the replication truncation point by issuing the following command:

```
dbcc settrunc('ltm','ignore')
```

After the truncation point is removed, you can't resume the AWS DMS task\. The log continues to be truncated automatically at the checkpoints \(if automatic truncation is set\)\.

## Source Data Types for SAP ASE<a name="CHAP_Source.SAP.DataTypes"></a>

For a list of the SAP ASE source data types that are supported when using AWS DMS and the default mapping from AWS DMS data types, see the following table\. AWS DMS doesn't support SAP ASE source tables with columns of the user\-defined type \(UDT\) data type\. Replicated columns with this data type are created as NULL\. 

For information on how to view the data type that is mapped in the target, see the [Targets for Data Migration](CHAP_Target.md) section for your target endpoint\.

For additional information about AWS DMS data types, see [Data Types for AWS Database Migration Service](CHAP_Reference.DataTypes.md)\.


|  SAP ASE Data Types  |  AWS DMS Data Types  | 
| --- | --- | 
| BIGINT | INT8 | 
| BINARY | BYTES | 
| BIT | BOOLEAN | 
| CHAR | STRING | 
| DATE | DATE | 
| DATETIME | DATETIME | 
| DECIMAL | NUMERIC | 
| DOUBLE | REAL8 | 
| FLOAT | REAL8 | 
| IMAGE | BLOB | 
| INT | INT4 | 
| MONEY | NUMERIC | 
| NCHAR | WSTRING | 
| NUMERIC | NUMERIC | 
| NVARCHAR | WSTRING | 
| REAL | REAL4 | 
| SMALLDATETIME | DATETIME | 
| SMALLINT | INT2 | 
| SMALLMONEY | NUMERIC | 
| TEXT | CLOB | 
| TIME | TIME | 
| TINYINT | UINT1 | 
| UNICHAR | UNICODE CHARACTER | 
| UNITEXT | NCLOB | 
| UNIVARCHAR | UNICODE | 
| VARBINARY | BYTES | 
| VARCHAR | STRING | 