# Task settings for change processing DDL handling<a name="CHAP_Tasks.CustomizingTasks.TaskSettings.DDLHandling"></a>



The following settings determine how AWS DMS handles data definition language \(DDL\) changes for target tables during change data capture \(CDC\)\. For information about how to use a task configuration file to set task settings, see [Task settings example](CHAP_Tasks.CustomizingTasks.TaskSettings.md#CHAP_Tasks.CustomizingTasks.TaskSettings.Example)\. 

Task settings to handle change processing DDL include the following:
+ `HandleSourceTableDropped –` Set this option to `true` to drop the target table when the source table is dropped\.
+ `HandleSourceTableTruncated` – Set this option to `true` to truncate the target table when the source table is truncated\.
+ `HandleSourceTableAltered` – Set this option to `true` to alter the target table when the source table is altered\.

Following is an example of how task settings that handle change processing DDL appear in a task setting JSON file:

```
                "ChangeProcessingDdlHandlingPolicy": {
                   "HandleSourceTableDropped": true,
                   "HandleSourceTableTruncated": true,
                   "HandleSourceTableAltered": true
                },
```

**Note**  
For information about which DDL statements are supported for a specific endpoint, see the topic describing that endpoint\.