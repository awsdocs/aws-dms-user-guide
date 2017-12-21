# Using a SAP ASE Database as a Source for AWS DMS<a name="CHAP_Source.SAP"></a>

You can migrate data from a SAP Adaptive Server Enterprise \(ASE\) database–formerly known as Sybase–using AWS DMS\. With a SAP ASE database as a source, you can migrate data to any of the other supported AWS DMS target databases\.

For additional details on working with SAP ASE databases and AWS DMS, see the following sections\.


+ [Prerequisites for Using a SAP ASE Database as a Source for AWS DMS](#CHAP_Source.SAP.Prerequisites)
+ [Limitations on Using SAP ASE as a Source for AWS DMS](#CHAP_Source.SAP.Limitations)
+ [User Account Permissions Required for Using SAP ASE as a Source for AWS DMS](#CHAP_Source.SAP.Security)
+ [Removing the Truncation Point](#CHAP_Source.SAP.Truncation)

## Prerequisites for Using a SAP ASE Database as a Source for AWS DMS<a name="CHAP_Source.SAP.Prerequisites"></a>

For a SAP ASE database to be a source for AWS DMS, you should do the following:

+ SAP ASE replication must be enabled for tables by using the `sp_setreptable` command\.

+ `RepAgent` must be disabled on the SAP ASE database\.

+ When replicating to SAP ASE version 15\.7 installed on a Windows EC2 instance configured with a non\-Latin language \(for example, Chinese\), AWS DMS requires SAP ASE 15\.7 SP121 to be installed on the target SAP ASE machine\.

## Limitations on Using SAP ASE as a Source for AWS DMS<a name="CHAP_Source.SAP.Limitations"></a>

The following limitations apply when using an SAP ASE database as a source for AWS DMS \(AWS DMS\):

+ Only one AWS DMS task can be run per SAP ASE database\.

+ Renaming a table is not supported, for example: `sp_rename 'Sales.SalesRegion', 'SalesReg;`

+ Renaming a column is not supported, for example: `sp_rename 'Sales.Sales.Region', 'RegID', 'COLUMN';`

+ Zero values located at the end of binary data type strings are truncated when replicated to the target database\. For example, `0x0000000000000000000000000100000100000000` in the source table becomes `0x00000000000000000000000001000001` in the target table\.

+ If the database default is not to allow NULL values, AWS DMS creates the target table with columns that don't allow NULL values\. Consequently, if a full load or CDC replication task contains empty values, errors occur\. You can prevent these errors by allowing NULL values in the source database by using the following commands\.

  ```
  sp_dboption <database name>, 'allow nulls by default', 'true'
  go
  use <database name>
  CHECKPOINT
  go
  ```

+ The `reorg rebuild` index command isn't supported\.

## User Account Permissions Required for Using SAP ASE as a Source for AWS DMS<a name="CHAP_Source.SAP.Security"></a>

To use an SAP ASE database as a source in an AWS DMS task, the user specified in the AWS DMS SAP ASE database definitions must be granted the following permissions in the SAP ASE database\. 

+ sa\_role

+ replication\_role

+ sybase\_ts\_role

+ If you have set the `enableReplication` connection property to `Y`, then your must also be granted the `sp_setreptable` permission\. For more information on connection properties see [Using Extra Connection Attributes with AWS Database Migration Service](CHAP_Introduction.ConnectionAttributes.md)\.

## Removing the Truncation Point<a name="CHAP_Source.SAP.Truncation"></a>

When a task starts, AWS DMS establishes a `$replication_truncation_point` entry in the `syslogshold` system view, indicating that a replication process is in progress\. While AWS DMS is working, it advances the replication truncation point at regular intervals, according to the amount of data that has already been copied to the target\.

Once the `$replication_truncation_point` entry has been established, the AWS DMS task must be kept running at all times to prevent the database log from becoming excessively large\. If you want to stop the AWS DMS task permanently, the replication truncation point must be removed by issuing the following command:

```
dbcc settrunc('ltm','ignore')
```

After the truncation point has been removed, the AWS DMS task cannot be resumed\. The log continues to be truncated automatically at the checkpoints \(if automatic truncation is set\)\.