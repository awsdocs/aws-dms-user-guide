# Creating Tasks for Ongoing Replication Using AWS DMS<a name="CHAP_Task.CDC"></a>

You can create an AWS DMS task that captures ongoing changes to the source data store You can do this capture while you are migrating your data\. You can also create a task that captures ongoing changes after you complete your initial migration to a supported target data store\. This process is called ongoing replication or change data capture \(CDC\)\. AWS DMS uses this process when replicating ongoing changes from a source data store\. This process works by collecting changes to the database logs using the database engine's native API\. 

Each source engine has specific configuration requirements for exposing this change stream to a given user account\. Most engines require some additional configuration to make it possible for the capture process to consume the change data in a meaningful way, without data loss\. For example, Oracle requires the addition of supplemental logging, and MySQL requires row\-level binary logging \(bin logging\)\. 

 To read ongoing changes from the source database, AWS DMS uses engine\-specific API actions to read changes from the source engine’s transaction logs\. Following are some examples of how AWS DMS does that: 
+ For Oracle, AWS DMS uses either the Oracle LogMiner API or binary reader API \(bfile API\) to read ongoing changes\. AWS DMS reads ongoing changes from the online or archive redo logs based on the system change number \(SCN\)\. 
+ For Microsoft SQL Server, AWS DMS uses MS\-Replication or MS\-CDC to write information to the SQL Server transaction log\. It then uses the `fn_dblog()` or` fn_dump_dblog()` function in SQL Server to read the changes in the transaction log based on the log sequence number \(LSN\)\. 
+ For MySQL, AWS DMS reads changes from the row\-based binary logs \(binlogs\) and migrates those changes to the target\.
+ For PostgreSQL, AWS DMS sets up logical replication slots and uses the `test_decoding` plugin to read changes from the source and migrate them to the target\.
+ For Amazon RDS as a source, we recommend ensuring that backups are enabled to setup CDC\. We also recommend ensuring that the source database is configured to retain change logs for a sufficient time—24 hours is usually enough\.

There are two types of ongoing replication tasks:
+ Full load plus CDC \- The task migrates existing data and then updates the target database based on changes to the source database\.
+ CDC only \- The task migrates ongoing changes after you have data on your target database\.

## Performing Replication Starting From a CDC Start Point<a name="CHAP_Task.CDC.StartPoint"></a>

You can start an AWS DMS ongoing replication task \(change data capture only\) from several points\. These include: 
+  *From a custom CDC start time* — You can use the AWS Management Console or AWS CLI to provide AWS DMS with a timestamp where you want the replication to start\. AWS DMS then starts an ongoing replication task from this custom CDC start time\. AWS DMS converts the given timestamp \(in UTC\) to a native start point, such as an LSN for SQL Server or an SCN for Oracle\. AWS DMS uses engine\-specific methods to determine where exactly to start the migration task based on the source engine’s change stream\. 
**Note**  
 PostgreSQL as a source doesn't support a custom CDC start time because the database engine doesn't have a way to map a timestamp to an LSN or SCN and Oracle and SQL Server do\. 
+ *From a CDC native start point* — Instead of providing a timestamp, which can include multiple native points in the transaction log, you can choose to start from a native point in the source engine’s transaction log\. AWS DMS supports this feature for the following source endpoints: 
  + SQL Server
  + Oracle
  + MySQL

### Determining a CDC Native Start Point<a name="CHAP_Task.CDC.StartPoint.Native"></a>

A CDC native start point is a point in the database engine's log that defines a time where you can begin CDC\. If a bulk data dump has been applied to the target from a point in time, you can look up the native start point for the ongoing replication\-only task from a point before the dump was taken\.

Following are examples of how you can find the CDC native start point from a supported source engine:

**SQL Server**  
SQL Server’s log sequence number \(LSN\) has three parts:  
+ Virtual log file \(VLF\) sequence number
+ Starting offset of a log block
+ Slot number
 An example LSN is as follows: 00000014:00000061:0001   
To get the start point for a SQL Server migration task based on your transaction log backup settings, use the fn\_dblog\(\) or fn\_dump\_dblog\(\) function in SQL Server\.   
In order to use CDC native start point with SQL Server, you must create a publication on any table participating in ongoing replication\. For more information about creating a publication, see [ Creating a SQL Server Publication for Ongoing Replication](CHAP_Source.SQLServer.md#CHAP_Source.SQLServer.CDC.Publication)\. AWS DMS creates the publication automatically when you use CDC without native start point\.

**Oracle**  
A system change number \(SCN\) is a logical, internal time stamp used by Oracle databases\. SCNs order events that occur within the database, which is necessary to satisfy the ACID properties of a transaction\. Oracle databases use SCNs to mark the location where all changes have been written to disk so that a recovery action doesn't apply already written changes\. Oracle also uses SCNs to mark the point where no redo exists for a set of data so that recovery can stop\. For more information about Oracle SCNs, see the [Oracle documentation](https://docs.oracle.com/database/121/CNCPT/transact.htm#CNCPT016)\.   
 To get the current SCN in an Oracle database, run the following command:   

```
SELECT current_scn FROM V$DATABASE
```

**MySQL**  
Before the release of MySQL version 5\.6\.3, the log sequence number \(LSN\) for MySQL was a 4\-byte unsigned integer\. In MySQL version 5\.6\.3, when the redo log file size limit increased from 4GB to 512GB, the LSN became an 8\-byte unsigned integer\. The increase reflects that additional bytes were required to store extra size information\. Applications built on MySQL 5\.6\.3 or later that use LSN values should use 64\-bit rather than 32\-bit variables to store and compare LSN values\. For more information about MySQL LSNs, see the [MySQL documentation](https://dev.mysql.com/doc/refman/5.7/en/glossary.html#glos_lsn)\.   
 To get the current LSN in a MySQL database, run the following command:  

```
mysql> show master status;
```
 The query returns a binlog file name, the position, and several other values\. The CDC native start point is a combination of the binlogs file name and the position, for example `mysql-bin-changelog.000024:373`\. In this example, `mysql-bin-changelog.000024` is the binlogs file name and `373` is the position where AWS DMS needs to start capturing changes\. 

### Using a Checkpoint as a CDC Start Point<a name="CHAP_Task.CDC.StartPoint.Checkpoint"></a>

Start from a checkpoint:

 An ongoing replication task migrates changes, and AWS DMS caches AWS DMS\-specific checkpoint information from time to time\. The checkpoint that AWS DMS creates contains information so the replication engine knows the recovery point for the change stream\. You can use the checkpoint to go back in the timeline of changes and recover a failed migration task\. You can also use a checkpoint to start another ongoing replication task for another target at a any given point in time\.

You can get the checkpoint information in one of the following two ways: 
+ Run the API command `DescribeReplicationTasks` and view the results\. You can filter the information by task and search for the checkpoint\. You can retrieve the latest checkpoint when the task is in stopped or failed state\. 
+ View the metadata table named `awsdms_txn_state` on the target instance\. You can query the table to get checkpoint information\. To create the metadata table, set the `TaskRecoveryTableEnabled` parameter to `Yes` when you create a task\. This setting causes AWS DMS to continuously write checkpoint information to the target metadata table\. This information is lost if a task is deleted\.

  For example, the following is a sample checkpoint in the metadata table: checkpoint:V1\#34\#00000132/0F000E48\#0\#0\#\*\#0\#121

### Stopping a Task at a Commit or Server Time Point<a name="CHAP_Task.CDC.StopPoint"></a>

 With the introduction of CDC native start points, AWS DMS can also stop a task at the following points: 
+ A commit time on the source
+ A server time on the replication instance

 You can modify a task and set a time in UTC to stop as required\. The task automatically stops based on the commit or server time that you set\. Additionally, if you know an appropriate time to stop the migration task at task creation, you can set a stop time when you create the task\. 
