# Target metadata task settings<a name="CHAP_Tasks.CustomizingTasks.TaskSettings.TargetMetadata"></a>

Target metadata settings include the following\. For information about how to use a task configuration file to set task settings, see [Task settings example](CHAP_Tasks.CustomizingTasks.TaskSettings.md#CHAP_Tasks.CustomizingTasks.TaskSettings.Example)\.
+ `TargetSchema` – The target table schema name\. If this metadata option is empty, the schema from the source table is used\. AWS DMS automatically adds the owner prefix for the target database to all tables if no source schema is defined\. This option should be left empty for MySQL\-type target endpoints\. 
+ LOB settings – Settings that determine how large objects \(LOBs\) are managed\. If you set `SupportLobs=true`, you must set one of the following to `true`: 
  + `FullLobMode` – If you set this option to `true`, then you must enter a value for the `LobChunkSize` option\. Enter the size, in kilobytes, of the LOB chunks to use when replicating the data to the target\. The `FullLobMode` option works best for very large LOB sizes but tends to cause slower loading\. The recommended value for `LobChunkSize` is 64 kilobytes\. Increasing the value for `LobChunkSize` above 64 kilobytes can cause task failures\.
  + `InlineLobMaxSize` – This value determines which LOBs AWS DMS transfers inline during a full load\. Transferring small LOBs is more efficient than looking them up from a source table\. During a full load, AWS DMS checks all LOBs and performs an inline transfer for the LOBs that are smaller than `InlineLobMaxSize`\. AWS DMS transfers all LOBs larger than the `InlineLobMaxSize` in `FullLobMode`\. The default value for `InlineLobMaxSize` is 0 and the range is 1 –102400 kilobytes \(100 MB\)\. Set a value for `InlineLobMaxSize` only if you know that most of the LOBs are smaller than the value specified in `InlineLobMaxSize`\.
  + `LimitedSizeLobMode` – If you set this option to `true`, then you must enter a value for the `LobMaxSize` option\. Enter the maximum size, in kilobytes, for an individual LOB\. The maximum recommended value for `LobMaxSize` is 102400 kilobytes \(100 MB\)\.

  For more information about the criteria for using these task LOB settings, see [Setting LOB support for source databases in an AWS DMS task](CHAP_Tasks.LOBSupport.md)\. You can also control the management of LOBs for individual tables\. For more information, see [Table and collection settings rules and operations](CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Tablesettings.md)\.
+ `LoadMaxFileSize` – An option for CSV\-based target endpoints like MySQL, PostgreSQL, and Amazon Redshift that support use of comma\-separated value \(\.csv\) files for loading data\. `LoadMaxFileSize` defines the maximum size on disk of stored, unloaded data, such as \.csv files\. This option overrides the target endpoint connection attribute, `maxFileSize`\. You can provide values from 0, which indicates that this option doesn't override the connection attribute, to 100,000 KB\.
+ `BatchApplyEnabled` – Determines if each transaction is applied individually or if changes are committed in batches\. The default value is `false`\.

  When `BatchApplyEnabled` is set to `true`, DMS requires a Primary Key \(PK\) or Unique Key \(UK\) on the **source** table\(s\)\. Without a PK or UK on source tables, only batch inserts are applied but not batch updates and deletes\.

  When `BatchApplyEnabled` is set to `true`, AWS DMS generates an error message if a **target** table has a unique constraint and a primary key\. Target tables with both a unique constraint and primary key aren't supported when `BatchApplyEnabled` is set to `true`\.

  When `BatchApplyEnabled` is set to true and AWS DMS encounters a data error from a table with the default error\-handling policy, the AWS DMS task switches from batch mode to one\-by\-one mode for the rest of the tables\. To alter this behavior, you can set the `"SUSPEND_TABLE"` action on the following policies in the `"ErrorBehavior"` group property of the task settings JSON file:
  + `DataErrorPolicy`
  + `ApplyErrorDeletePolicy`
  + `ApplyErrorInsertPolicy`
  + `ApplyErrorUpdatePolicy`

  For more information on this `"ErrorBehavior"` group property, see the example task settings JSON file in [Specifying task settings for AWS Database Migration Service tasks](CHAP_Tasks.CustomizingTasks.TaskSettings.md)\. After setting these policies to `"SUSPEND_TABLE"`, the AWS DMS task then suspends data errors on any tables that raise them and continues in batch mode for all tables\.

  You can use the `BatchApplyEnabled` parameter with the `BatchApplyPreserveTransaction` parameter\. If `BatchApplyEnabled` is set to `true`, then the `BatchApplyPreserveTransaction` parameter determines the transactional integrity\. 

  If `BatchApplyPreserveTransaction` is set to `true`, then transactional integrity is preserved and a batch is guaranteed to contain all the changes within a transaction from the source\.

  If `BatchApplyPreserveTransaction` is set to `false`, then there can be temporary lapses in transactional integrity to improve performance\. 

  The `BatchApplyPreserveTransaction` parameter applies only to Oracle target endpoints, and is only relevant when the `BatchApplyEnabled` parameter is set to `true`\.

  When LOB columns are included in the replication, you can use `BatchApplyEnabled` only in limited LOB mode\.

  For more information about using these settings for a change data capture \(CDC\) load, see [Change processing tuning settings](CHAP_Tasks.CustomizingTasks.TaskSettings.ChangeProcessingTuning.md)\.
+ `ParallelLoadThreads` – Specifies the number of threads that AWS DMS uses to load each table into the target database\. This parameter has maximum values for non\-RDBMS targets\. The maximum value for a DynamoDB target is 200\. The maximum value for an Amazon Kinesis Data Streams, Apache Kafka, or Amazon OpenSearch Service target is 32\. You can ask to have this maximum limit increased\. `ParallelLoadThreads` applies to Full Load tasks\. For information on the settings for parallel load of individual tables, see [Table and collection settings rules and operations](CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Tablesettings.md)\.

  This setting applies to the following endpoint engine types:
  + 
  + DynamoDB
  + Amazon Kinesis Data Streams
  + Amazon MSK
  + Amazon OpenSearch Service
  + Amazon Redshift

  AWS DMS supports `ParallelLoadThreads` for MySQL as an extra connection attribute\. `ParallelLoadThreads` does not apply to MySQL as a task setting\. 
+ `ParallelLoadBufferSize` – Specifies the maximum number of records to store in the buffer that the parallel load threads use to load data to the target\. The default value is 50\. The maximum value is 1,000\. This setting is currently only valid when DynamoDB, Kinesis, Apache Kafka, or OpenSearch is the target\. Use this parameter with `ParallelLoadThreads`\. `ParallelLoadBufferSize` is valid only when there is more than one thread\. For information on the settings for parallel load of individual tables, see [Table and collection settings rules and operations](CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Tablesettings.md)\.
+ `ParallelLoadQueuesPerThread` – Specifies the number of queues that each concurrent thread accesses to take data records out of queues and generate a batch load for the target\. The default is 1\. This setting is currently only valid when Kinesis or Apache Kafka is the target\.
+ `ParallelApplyThreads` – Specifies the number of concurrent threads that AWS DMS uses during a CDC load to push data records to an Amazon DocumentDB, Kinesis, Amazon MSK, OpenSearch, or Amazon Redshift target endpoint\. The default is zero \(0\)\.

  This setting only applies for CDC\-only\. This setting does not apply for Full Load\.

  This setting applies to the following endpoint engine types:
  + Amazon DocumentDB \(with MongoDB compatibility\)
  + Amazon Kinesis Data Streams
  + Amazon Managed Streaming for Apache Kafka
  + Amazon OpenSearch Service
  + Amazon Redshift
+ `ParallelApplyBufferSize` – Specifies the maximum number of records to store in each buffer queue for concurrent threads to push to an Amazon DocumentDB, Kinesis, Amazon MSK, OpenSearch, or Amazon Redshift target endpoint during a CDC load\. The default value is 100\. Use this option when `ParallelApplyThreads` specifies more than one thread\. 
+ `ParallelApplyQueuesPerThread` – Specifies the number of queues that each thread accesses to take data records out of queues and generate a batch load for a an Amazon DocumentDB, Kinesis, Amazon MSK, OpenSearch, or Amazon Redshift endpoint during CDC\. The default value is 1\.