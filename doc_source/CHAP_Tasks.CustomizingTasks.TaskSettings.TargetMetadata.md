# Target Metadata Task Settings<a name="CHAP_Tasks.CustomizingTasks.TaskSettings.TargetMetadata"></a>

Target metadata settings include the following:
+ `TargetSchema` – The target table schema name\. If this metadata option is empty, the schema from the source table is used\. AWS DMS automatically adds the owner prefix for the target database to all tables if no source schema is defined\. This option should be left empty for MySQL\-type target endpoints\. 
+ `LOB settings` – Settings that determine how large objects \(LOBs\) are managed\. If you set `SupportLobs=true`, you must set one of the following to `true`: 
  + `FullLobMode` – If you set this option to `true`, then you must enter a value for the `LobChunkSize` option\. Enter the size, in kilobytes, of the LOB chunks to use when replicating the data to the target\. The `FullLobMode` option works best for very large LOB sizes but tends to cause slower loading\.
  + `LimitedSizeLobMode` – If you set this option to `true`, then you must enter a value for the `LobMaxSize` option\. Enter the maximum size, in kilobytes, for an individual LOB\.
+ `LoadMaxFileSize` – An option for PostgreSQL and MySQL target endpoints that defines the maximum size on disk of stored, unloaded data, such as CSV files\. This option overrides the connection attribute\. You can provide values from 0, which indicates that this option doesn't override the connection attribute, to 100,000 KB\.
+ `BatchApplyEnabled` – Determines if each transaction is applied individually or if changes are committed in batches\. The default value is `false`\.

  The `BatchApplyEnabled` parameter is used in conjunction with the `BatchApplyPreserveTransaction` parameter\. If `BatchApplyEnabled` is set to `true`, then the `BatchApplyPreserveTransaction` parameter determines the transactional integrity\. 

  If `BatchApplyPreserveTransaction` is set to `true`, then transactional integrity is preserved and a batch is guaranteed to contain all the changes within a transaction from the source\.

  If `BatchApplyPreserveTransaction` is set to `false`, then there can be temporary lapses in transactional integrity to improve performance\. 

  The `BatchApplyPreserveTransaction` parameter applies only to Oracle target endpoints, and is only relevant when the `BatchApplyEnabled` parameter is set to `true`\.

  When LOB columns are included in the replication, `BatchApplyEnabled`can only be used in **Limited\-size LOB mode**\.
+ `ParallelLoadThreads` – Specifies the number of threads AWS DMS uses to load each table into the target database\. The maximum value for a MySQL target is 16; the maximum value for a DynamoDB target is 32\. The maximum limit can be increased upon request\.
+ `ParallelLoadBufferSize` – Specifies the maximum number of records to store in the buffer used by the parallel load threads to load data to the target\. The default value is 50\. Maximum value is 1000\. This field is currently only valid when DynamoDB is the target\. This parameter should be used in conjunction with `ParallelLoadThreads` and is valid only when ParallelLoadThreads > 1\.