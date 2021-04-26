# Creating tasks for ongoing replication using AWS DMS<a name="CHAP_Task.CDC"></a>

You can create an AWS DMS task that captures ongoing changes to the source data store\. You can do this capture while you are migrating your data\. You can also create a task that captures ongoing changes after you complete your initial \(full\-load\) migration to a supported target data store\. This process is called ongoing replication or change data capture \(CDC\)\. AWS DMS uses this process when replicating ongoing changes from a source data store\. This process works by collecting changes to the database logs using the database engine's native API\. 

**Note**  
You can migrate views using full\-load tasks only\. If your task is either a CDC\-only task or a full\-load task that starts CDC after it completes, the migration includes only tables from the source\. Using a full\-load\-only task, you can migrate views or a combination of tables and views\. For more information, see [ Specifying table selection and transformations rules using JSON](CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.md)\.

Each source engine has specific configuration requirements for exposing this change stream to a given user account\. Most engines require some additional configuration to make it possible for the capture process to consume the change data in a meaningful way, without data loss\. For example, Oracle requires the addition of supplemental logging, and MySQL requires row\-level binary logging \(bin logging\)\. 

 To read ongoing changes from the source database, AWS DMS uses engine\-specific API actions to read changes from the source engine's transaction logs\. Following are some examples of how AWS DMS does that: 
+ For Oracle, AWS DMS uses either the Oracle LogMiner API or binary reader API \(bfile API\) to read ongoing changes\. AWS DMS reads ongoing changes from the online or archive redo logs based on the system change number \(SCN\)\. 
+ For Microsoft SQL Server, AWS DMS uses MS\-Replication or MS\-CDC to write information to the SQL Server transaction log\. It then uses the `fn_dblog()` or ` fn_dump_dblog()` function in SQL Server to read the changes in the transaction log based on the log sequence number \(LSN\)\. 
+ For MySQL, AWS DMS reads changes from the row\-based binary logs \(binlogs\) and migrates those changes to the target\.
+ For PostgreSQL, AWS DMS sets up logical replication slots and uses the test\_decoding plugin to read changes from the source and migrate them to the target\.
+ For Amazon RDS as a source, we recommend ensuring that backups are enabled to set up CDC\. We also recommend ensuring that the source database is configured to retain change logs for a sufficient time—24 hours is usually enough\.

There are two types of ongoing replication tasks:
+ Full load plus CDC – The task migrates existing data and then updates the target database based on changes to the source database\.
+ CDC only – The task migrates ongoing changes after you have data on your target database\.

## Performing replication starting from a CDC start point<a name="CHAP_Task.CDC.StartPoint"></a>

You can start an AWS DMS ongoing replication task \(change data capture only\) from several points\. These include the following: 
+  **From a custom CDC start time** – You can use the AWS Management Console or AWS CLI to provide AWS DMS with a timestamp where you want the replication to start\. AWS DMS then starts an ongoing replication task from this custom CDC start time\. AWS DMS converts the given timestamp \(in UTC\) to a native start point, such as an LSN for SQL Server or an SCN for Oracle\. AWS DMS uses engine\-specific methods to determine where to start the migration task based on the source engine's change stream\. 
**Note**  
PostgreSQL as a source doesn't support a custom CDC start time\. This is because the PostgreSQL database engine doesn't have a way to map a timestamp to an LSN or SCN as Oracle and SQL Server do\. 
+ **From a CDC native start point** – You can also start from a native point in the source engine's transaction log\. In some cases, you might prefer this approach because a timestamp can indicate multiple native points in the transaction log\. AWS DMS supports this feature for the following source endpoints: 
  + SQL Server
  + PostgreSQL
  + Oracle
  + MySQL

### Determining a CDC native start point<a name="CHAP_Task.CDC.StartPoint.Native"></a>

A *CDC native start point* is a point in the database engine's log that defines a time where you can begin CDC\. As an example, suppose that a bulk data dump has been applied to the target starting from a point in time\. In this case, you can look up the native start point for the ongoing replication\-only task from a point before the dump was taken\.

Following are examples of how you can find the CDC native start point from supported source engines:

**SQL Server**  
In SQL Server, a log sequence number \(LSN\) has three parts:  
+ Virtual log file \(VLF\) sequence number
+ Starting offset of a log block
+ Slot number
 An example LSN is as follows: `00000014:00000061:0001`   
To get the start point for a SQL Server migration task based on your transaction log backup settings, use the `fn_dblog()` or `fn_dump_dblog()` function in SQL Server\.   
To use CDC native start point with SQL Server, create a publication on any table participating in ongoing replication\. For more information about creating a publication, see [Setting up ongoing replication without the sysadmin role on self\-managed SQL Server](CHAP_Source.SQLServer.md#CHAP_Source.SQLServer.CDC.Publication)\. AWS DMS creates the publication automatically when you use CDC without using a CDC native start point\.

**PostgreSQL**  
You can use a CDC recovery checkpoint for your PostgreSQL source database\. This checkpoint value is generated at various points as an ongoing replication task runs for your source database \(the parent task\)\. For more information about checkpoints in general, see [Using a checkpoint as a CDC start point](#CHAP_Task.CDC.StartPoint.Checkpoint)\.   
To identify the checkpoint to use as your native start point, use your database `pg_replication_slots` view or your parent task's overview details from the AWS Management Console  

**To find the overview details for your parent task on the console**

1. Sign in to the AWS Management Console and open the AWS DMS console at [https://console\.aws\.amazon\.com/dms/v2/](https://console.aws.amazon.com/dms/v2/)\. 

   If you are signed in as an IAM user, make sure that you have the appropriate permissions to access AWS DMS\. For more information about the permissions required, see [IAM permissions needed to use AWS DMS](CHAP_Security.md#CHAP_Security.IAMPermissions)\.

1. On the navigation pane, choose **Database migration tasks**\.

1. Choose your parent task from the list on the **Database migration tasks** page\. Doing this opens your parent task page, showing the overview details\.

1. Find the checkpoint value under **Change data capture \(CDC\)**, **Change data capture \(CDC\) start position**, and **Change data capture \(CDC\) recovery checkpoint**\. 

   The value appears similar to the following\.

   ```
   checkpoint:V1#1#000004AF/B00000D0#0#0#*#0#0
   ```

   Here, the `4AF/B00000D0` component is what you need to specify this native CDC start point\. Set the DMS API `CdcStartPosition` parameter to this value when you create the CDC task to begin replication at this start point for your PostgreSQL source\. For information on using the AWS CLI to create this CDC task, see [Enabling CDC with an AWS\-managed PostgreSQL DB instance with AWS DMS](CHAP_Source.PostgreSQL.md#CHAP_Source.PostgreSQL.RDSPostgreSQL.CDC)\.

**Oracle**  
A system change number \(SCN\) is a logical, internal time stamp used by Oracle databases\. SCNs order events that occur within the database, which is necessary to satisfy the ACID properties of a transaction\. Oracle databases use SCNs to mark the location where all changes have been written to disk so that a recovery action doesn't apply already written changes\. Oracle also uses SCNs to mark the point where no redo exists for a set of data so that recovery can stop\.   
To get the current SCN in an Oracle database, run the following command\.  

```
SELECT CURRENT_SCN FROM V$DATABASE
```
If you use the current SCN to start a CDC task, you miss the results of any open transactions and fail to migrate these results\. *Open transactions* are transactions that were started earlier but not committed yet\. You can identify the SCN and timestamp to start a CDC task at a point that includes all open transactions\. For more information, see [Transactions](https://docs.oracle.com/database/121/CNCPT/transact.htm#CNCPT016) in the Oracle online documentation\.

**MySQL**  
Before the release of MySQL version 5\.6\.3, the log sequence number \(LSN\) for MySQL was a 4\-byte unsigned integer\. In MySQL version 5\.6\.3, when the redo log file size limit increased from 4 GB to 512 GB, the LSN became an 8\-byte unsigned integer\. The increase reflects that additional bytes were required to store extra size information\. Applications built on MySQL 5\.6\.3 or later that use LSN values should use 64\-bit rather than 32\-bit variables to store and compare LSN values\. For more information about MySQL LSNs, see the [MySQL documentation](https://dev.mysql.com/doc/refman/5.7/en/glossary.html#glos_lsn)\.   
 To get the current LSN in a MySQL database, run the following command\.  

```
mysql> show master status;
```
 The query returns a binlog file name, the position, and several other values\. The CDC native start point is a combination of the binlogs file name and the position, for example `mysql-bin-changelog.000024:373`\. In this example, `mysql-bin-changelog.000024` is the binlogs file name and `373` is the position where AWS DMS needs to start capturing changes\. 

### Using a checkpoint as a CDC start point<a name="CHAP_Task.CDC.StartPoint.Checkpoint"></a>

 An ongoing replication task migrates changes, and AWS DMS caches checkpoint information specific to AWS DMS from time to time\. The checkpoint that AWS DMS creates contains information so the replication engine knows the recovery point for the change stream\. You can use the checkpoint to go back in the timeline of changes and recover a failed migration task\. You can also use a checkpoint to start another ongoing replication task for another target at any given point in time\.

You can get the checkpoint information in one of the following two ways: 
+ Run the API operation `DescribeReplicationTasks` and view the results\. You can filter the information by task and search for the checkpoint\. You can retrieve the latest checkpoint when the task is in stopped or failed state\. 
+ View the metadata table named `awsdms_txn_state` on the target instance\. You can query the table to get checkpoint information\. To create the metadata table, set the `TaskRecoveryTableEnabled` parameter to `Yes` when you create a task\. This setting causes AWS DMS to continuously write checkpoint information to the target metadata table\. This information is lost if a task is deleted\.

  For example, the following is a sample checkpoint in the metadata table: `checkpoint:V1#34#00000132/0F000E48#0#0#*#0#121`

### Stopping a task at a commit or server time point<a name="CHAP_Task.CDC.StopPoint"></a>

 With the introduction of CDC native start points, AWS DMS can also stop a task at the following points: 
+ A commit time on the source
+ A server time on the replication instance

 You can modify a task and set a time in UTC to stop as needed\. The task automatically stops based on the commit or server time that you set\. Or, if you know an appropriate time to stop the migration task at task creation, you can set a stop time when you create the task\. 

## Performing bidirectional replication<a name="CHAP_Task.CDC.Bidirectional"></a>

You can use AWS DMS tasks to perform bidirectional replication between two systems\. In *bidirectional replication*, you replicate data from the same table \(or set of tables\) between two systems in both directions\. 

For example, you can copy an EMPLOYEE table from database A to database B and replicate changes to the table from database A to database B\. You can also replicate changes to the EMPLOYEE table from database B back to A\. Thus, you're performing bidirectional replication\. 

**Note**  
AWS DMS bidirectional replication isn't intended as a full multi\-master solution including a primary node, conflict resolution, and so on\.

Use bidirectional replication for situations where data on different nodes is operationally segregated\. In other words, suppose that you have a data element changed by an application operating on node A, and that node A performs bidirectional replication with node B\. That data element on node A is never changed by any application operating on node B\.

AWS DMS versions 3\.3\.1 and higher support bidirectional replication on these database engines: 
+ Oracle 
+ SQL Server 
+ MySQL 
+ PostgreSQL 
+ Amazon Aurora MySQL\-Compatible Edition
+ Aurora PostgreSQL\-Compatible Edition

### Creating bidirectional replication tasks<a name="CHAP_Task.CDC.Bidirectional.Tasks"></a>

To enable AWS DMS bidirectional replication, configure source and target endpoints for both databases \(A and B\)\. For example, configure a source endpoint for database A, a source endpoint for database B, a target endpoint for database A, and a target endpoint for database B\. 

Then create two tasks: one task for source A to move data to target B, and another task for source B to move data to target A\. Also, make sure that each task is configured with loopback prevention\. Doing this prevents identical changes from being applied to the targets of both tasks, thus corrupting the data for at least one of them\. For more information, see [Preventing loopback](#CHAP_Task.CDC.Bidirectional.Loopback)\.

For the easiest approach, start with identical datasets on both database A and database B\. Then create two CDC only tasks, one task to replicate data from A to B, and another task to replicate data from B to A\.

To use AWS DMS to instantiate a new dataset \(database\) on node B from node A, do the following:

1. Use a full load and CDC task to move data from database A to B\. Make sure that no applications are modifying data on database B during this time\.

1. When the full load is complete and before applications are allowed to modify data on database B, note the time or CDC start position of database B\. For instructions, see [Performing replication starting from a CDC start point](#CHAP_Task.CDC.StartPoint)\.

1. Create a CDC only task that moves data from database B back to A using this start time or CDC start position\.

**Note**  
Only one task in a bidirectional pair can be full load and CDC\.

### Preventing loopback<a name="CHAP_Task.CDC.Bidirectional.Loopback"></a>

To show preventing loopback, suppose that in a task T1 AWS DMS reads change logs from source database A and applies the changes to target database B\. 

Next, a second task, T2, reads change logs from source database B and applies them back to target database A\. Before T2 does this, DMS must make sure that the same changes made to target database B from source database A aren't made to source database A\. In other words, DMS must make sure that these changes aren't echoed \(looped\) back to target database A\. Otherwise, the data in database A can be corrupted\. 

To prevent loopback of changes, add the following task settings to each bidirectional replication task\. Doing this makes sure that loopback data corruption doesn't occur in either direction\.

```
{
. . .

  "LoopbackPreventionSettings": {
    "EnableLoopbackPrevention": Boolean,
    "SourceSchema": String,
    "TargetSchema": String
  },

. . .
}
```

The `LoopbackPreventionSettings` task settings determine if a transaction is new or an echo from the opposite replication task\. When AWS DMS applies a transaction to a target database, it updates a DMS table \(`awsdms_loopback_prevention`\) with an indication of the change\. Before applying each transaction to a target, DMS ignores any transaction that includes a reference to this `awsdms_loopback_prevention` table\. Therefore, it doesn't apply the change\. 

Include these task settings with each replication task in a bidirectional pair\. These settings enable loopback prevention\. They also specify the schema for each source and target database in the task that includes the `awsdms_loopback_prevention` table for each endpoint\.

To enable each task to identify such an echo and discard it, set `EnableLoopbackPrevention` to `true`\. To specify a schema at the source that includes `awsdms_loopback_prevention`, set `SourceSchema` to the name for that schema in the source database\. To specify a schema at the target that includes the same table, set `TargetSchema` to the name for that schema in the target database\.

In the example following, the `SourceSchema` and `TargetSchema` settings for a replication task T1 and its opposite replication task T2 are specified with opposite settings\.

Settings for task T1 are as follows\.

```
{
. . .

  "LoopbackPreventionSettings": {
    "EnableLoopbackPrevention": true,
    "SourceSchema": "LOOP-DATA",
    "TargetSchema": "loop-data"
  },

. . .
}
```

Settings for opposite task T2 are as follows\.

```
{
. . .

  "LoopbackPreventionSettings": {
    "EnableLoopbackPrevention": true,
    "SourceSchema": "loop-data",
    "TargetSchema": "LOOP-DATA"
  },

. . .
}
```

**Note**  
When using the AWS CLI, use only the `create-replication-task` or `modify-replication-task` commands to configure `LoopbackPreventionSettings` in your bidirectional replications tasks\. 

### Limitations of bidirectional replication<a name="CHAP_Task.CDC.Bidirectional.Limitations"></a>

Bidirectional replication for AWS DMS has the following limitations:
+ Loopback prevention tracks only data manipulation language \(DML\) statements\. AWS DMS doesn't support preventing data definition language \(DDL\) loopback\. To do this, configure one of the tasks in a bidirectional pair to filter out DDL statements\.
+ Tasks that use loopback prevention don't support committing changes in batches\. To configure a task with loopback prevention, make sure to set `BatchApplyEnabled` to `false`\.
+ DMS bidirectional replication doesn't include conflict detection or resolution\. To detect data inconsistencies, use data validation on both tasks\. 