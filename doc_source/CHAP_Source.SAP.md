# Using an SAP ASE database as a source for AWS DMS<a name="CHAP_Source.SAP"></a>

You can migrate data from an SAP Adaptive Server Enterprise \(ASE\) database—formerly known as Sybase—using AWS DMS\. With an SAP ASE database as a source, you can migrate data to any of the other supported AWS DMS target databases\. AWS DMS supports SAP ASE versions 12\.5\.3 or higher, 15, 15\.5, 15\.7, 16 and later as sources\.

For additional details on working with SAP ASE databases and AWS DMS, see the following sections\.

**Topics**
+ [Prerequisites for using an SAP ASE database as a source for AWS DMS](#CHAP_Source.SAP.Prerequisites)
+ [Limitations on using SAP ASE as a source for AWS DMS](#CHAP_Source.SAP.Limitations)
+ [Permissions required for using SAP ASE as a source for AWS DMS](#CHAP_Source.SAP.Security)
+ [Removing the truncation point](#CHAP_Source.SAP.Truncation)
+ [Extra connection attributes when using SAP ASE as a source for AWS DMS](#CHAP_Source.SAP.ConnectionAttrib)
+ [Source data types for SAP ASE](#CHAP_Source.SAP.DataTypes)

## Prerequisites for using an SAP ASE database as a source for AWS DMS<a name="CHAP_Source.SAP.Prerequisites"></a>

For an SAP ASE database to be a source for AWS DMS, do the following:
+ Enable SAP ASE replication for tables by using the `sp_setreptable` command\. For more information, see [Sybase Infocenter Archive]( http://infocenter.sybase.com/help/index.jsp?topic=/com.sybase.dc32410_1501/html/refman/X37830.htm)\. 
+ Disable `RepAgent` on the SAP ASE database\. For more information, see [Stop and disable the RepAgent thread in the primary database](http://infocenter-archive.sybase.com/help/index.jsp?topic=/com.sybase.dc20096_1260/html/mra126ag/mra126ag65.htm)\. 
+ To replicate to SAP ASE version 15\.7 on an Windows EC2 instance configured for non\-Latin characters \(for example, Chinese\), install SAP ASE 15\.7 SP121 on the target computer\.

**Note**  
For ongoing change data capture \(CDC\) replication, DMS runs `dbcc logtransfer` and `dbcc log` to read data from the transaction log\.

## Limitations on using SAP ASE as a source for AWS DMS<a name="CHAP_Source.SAP.Limitations"></a>

The following limitations apply when using an SAP ASE database as a source for AWS DMS:
+ You can run only one AWS DMS task with ongoing replication or CDC for each SAP ASE database\. You can run multiple full\-load\-only tasks in parallel\.
+ You can't rename a table\. For example, the following command fails\.

  ```
  sp_rename 'Sales.SalesRegion', 'SalesReg;
  ```
+ You can't rename a column\. For example, the following command fails\.

  ```
  sp_rename 'Sales.Sales.Region', 'RegID', 'COLUMN';
  ```
+ Zero values located at the end of binary data type strings are truncated when replicated to the target database\. For example, `0x0000000000000000000000000100000100000000` in the source table becomes `0x00000000000000000000000001000001` in the target table\.
+ If the database default is set not to allow NULL values, AWS DMS creates the target table with columns that don't allow NULL values\. Consequently, if a full load or CDC replication task contains empty values, AWS DMS throws an error\. You can prevent these errors by allowing NULL values in the source database by using the following commands\.

  ```
  sp_dboption database_name, 'allow nulls by default', 'true'
  go
  use database_name
  CHECKPOINT
  go
  ```
+ The `reorg rebuild` index command isn't supported\.
+ AWS DMS does not support clusters or using MSA \(Multi\-Site Availability\)/Warm Standby as a source\.
+ When `AR_H_TIMESTAMP` transformation header expression is used in mapping rules, the milliseconds won't be captured for an added column\.
+ Running Merge operations during CDC will result in a non\-recoverable error\. To bring the target back in sync, run a full load\.
+ Rollback trigger events are not supported for tables that use a data row locking scheme\.

## Permissions required for using SAP ASE as a source for AWS DMS<a name="CHAP_Source.SAP.Security"></a>

To use an SAP ASE database as a source in an AWS DMS task, you need to grant permissions\. Grant the user account specified in the AWS DMS database definitions the following permissions in the SAP ASE database: 
+ sa\_role
+ replication\_role
+ sybase\_ts\_role
+ By default, where you need to have permission to run the `sp_setreptable` stored procedure, AWS DMS enables the SAP ASE replication option\. If you want to run `sp_setreptable` on a table directly from the database endpoint and not through AWS DMS itself, you can use the `enableReplication` extra connection attribute\. For more information, see [Extra connection attributes when using SAP ASE as a source for AWS DMS](#CHAP_Source.SAP.ConnectionAttrib)\.

## Removing the truncation point<a name="CHAP_Source.SAP.Truncation"></a>

When a task starts, AWS DMS establishes a `$replication_truncation_point` entry in the `syslogshold` system view, indicating that a replication process is in progress\. While AWS DMS is working, it advances the replication truncation point at regular intervals, according to the amount of data that has already been copied to the target\.

After the `$replication_truncation_point` entry is established, keep the AWS DMS task running to prevent the database log from becoming excessively large\. If you want to stop the AWS DMS task permanently, remove the replication truncation point by issuing the following command:

```
dbcc settrunc('ltm','ignore')
```

After the truncation point is removed, you can't resume the AWS DMS task\. The log continues to be truncated automatically at the checkpoints \(if automatic truncation is set\)\.

## Extra connection attributes when using SAP ASE as a source for AWS DMS<a name="CHAP_Source.SAP.ConnectionAttrib"></a>

You can use extra connection attributes to configure an SAP ASE source\. You specify these settings when you create the source endpoint\. You must separate multiple extra connection attribute settings from each other by semicolons and no additional white space\.

The following table shows the extra connection attributes available when using SAP ASE as a source for AWS DMS\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Source.SAP.html)

## Source data types for SAP ASE<a name="CHAP_Source.SAP.DataTypes"></a>

For a list of the SAP ASE source data types that are supported when using AWS DMS and the default mapping from AWS DMS data types, see the following table\. AWS DMS doesn't support SAP ASE source tables with columns of the user\-defined type \(UDT\) data type\. Replicated columns with this data type are created as NULL\. 

For information on how to view the data type that is mapped in the target, see the [Targets for data migration](CHAP_Target.md) section for your target endpoint\.

For additional information about AWS DMS data types, see [Data types for AWS Database Migration Service](CHAP_Reference.DataTypes.md)\.


|  SAP ASE data types  |  AWS DMS data types  | 
| --- | --- | 
| BIGINT | INT8 | 
| UNSIGNED BIGINT | UINT8 | 
| INT | INT4 | 
| UNSIGNED INT | UINT4 | 
| SMALLINT | INT2 | 
| UNSIGNED SMALLINT | UINT2 | 
| TINYINT | UINT1 | 
| DECIMAL | NUMERIC | 
| NUMERIC | NUMERIC | 
| FLOAT | REAL8 | 
| DOUBLE | REAL8 | 
| REAL | REAL4 | 
| MONEY | NUMERIC | 
| SMALLMONEY | NUMERIC | 
| DATETIME | DATETIME | 
| BIGDATETIME | DATETIME\(6\) | 
| SMALLDATETIME | DATETIME | 
| DATE | DATE | 
| TIME | TIME | 
| BIGTIME | TIME | 
| CHAR | STRING | 
| UNICHAR | WSTRING | 
| NCHAR | WSTRING | 
| VARCHAR | STRING | 
| UNIVARCHAR | WSTRING | 
| NVARCHAR | WSTRING | 
| BINARY | BYTES | 
| VARBINARY | BYTES | 
| BIT | BOOLEAN | 
| TEXT | CLOB | 
| UNITEXT | NCLOB | 
| IMAGE | BLOB | 