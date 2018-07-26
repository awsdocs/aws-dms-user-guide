# Change Processing DDL Handling Policy Task Settings<a name="CHAP_Tasks.CustomizingTasks.TaskSettings.DDLHandling"></a>

The following settings determine how AWS DMS handles DDL changes for target tables during change data capture \(CDC\)\. Change processing DDL handling policy settings include the following:
+ `HandleSourceTableDropped ` – Set this option to `true` to drop the target table when the source table is dropped
+ `HandleSourceTableTruncated` – Set this option to `true` to truncate the target table when the source table is truncated
+ `HandleSourceTableAltered` – Set this option to `true` to alter the target table when the source table is altered\.