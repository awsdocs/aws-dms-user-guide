# Before image task settings<a name="CHAP_Tasks.CustomizingTasks.TaskSettings.BeforeImage"></a>

When writing CDC updates to a data\-streaming target like Kinesis or Apache Kafka, you can view a source database row's original values before change by an update\. To make this possible, AWS DMS populates a *before image* of update events based on data supplied by the source database engine\. For information about how to use a task configuration file to set task settings, see [Task settings example](CHAP_Tasks.CustomizingTasks.TaskSettings.md#CHAP_Tasks.CustomizingTasks.TaskSettings.Example)\.

To do so, you use the `BeforeImageSettings` parameter, which adds a new JSON attribute to every update operation with values collected from the source database system\. 

Make sure to apply `BeforeImageSettings` only to full load plus CDC tasks or CDC only tasks\. Full load plus CDC tasks migrate existing data and replicate ongoing changes\. CDC only tasks replicate data changes only\. 

Don't apply `BeforeImageSettings` to tasks that are full load only\.

Possible options for `BeforeImageSettings` are the following:
+ `EnableBeforeImage` – Turns on before imaging when set to `true`\. The default is `false`\. 
+ `FieldName` – Assigns a name to the new JSON attribute\. When `EnableBeforeImage` is `true`, `FieldName` is required and can't be empty\.
+ `ColumnFilter` – Specifies a column to add by using before imaging\. To add only columns that are part of the table's primary keys, use the default value, `pk-only`\. To add only columns that are not of LOB type, use `non-lob`\. To add any column that has a before image value, use `all`\. 

The following shows an example of the use of `BeforeImageSettings`\. 

```
"BeforeImageSettings": {
    "EnableBeforeImage": true,
    "FieldName": "before-image",
    "ColumnFilter": "pk-only"
  }
```

For information on before image settings for Kinesis, including additional table mapping settings, see [Using a before image to view original values of CDC rows for a Kinesis data stream as a target](CHAP_Target.Kinesis.md#CHAP_Target.Kinesis.BeforeImage)\.

For information on before image settings for Kafka, including additional table mapping settings, see [Using a before image to view original values of CDC rows for Apache Kafka as a target](CHAP_Target.Kafka.md#CHAP_Target.Kafka.BeforeImage)\.