# Specifying table selection and transformations rules using JSON<a name="CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation"></a>

To specify the table mappings that you want to apply during migration, you can create a JSON file\. If you create a migration task using the console, you can browse for this JSON file or enter the JSON directly into the table mapping box\. If you use the CLI or API to perform migrations, you can specify this file using the `TableMappings` parameter of the `CreateReplicationTask` or `ModifyReplicationTask` API operation\. 

You can specify what tables, views, and schemas you want to work with\. You can also perform table, view, and schema transformations and specify settings for how AWS DMS loads individual tables and views\. You create table\-mapping rules for these options using the following rule types:
+ `selection` rules – Identify the types and names of source tables, views, and schemas to load\. For more information, see [Selection rules and actions](CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Selections.md)\.
+ `transformation` rules – Specify certain changes or additions to particular source tables and schemas on the source before they are loaded on the target\. For more information, see [ Transformation rules and actions](CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Transformations.md)\.

  Also, to define content of new and existing columns, you can use an expression within a transformation rule\. For more information, see [Using transformation rule expressions to define column content](CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Expressions.md)\.
+ `table-settings` rules – Specify how DMS tasks load the data for individual tables\. For more information, see [Table and collection settings rules and operations](CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Tablesettings.md)\.

**Note**  
For Amazon S3 targets, you can also tag S3 objects mapped to selected tables and schemas using the `post-processing` rule type and the `add-tag` rule action\. For more information, see [Amazon S3 object tagging](CHAP_Target.S3.md#CHAP_Target.S3.Tagging)\.  
For the targets following, you can specify how and where selected schemas and tables are migrated to the target using the `object-mapping` rule type:  
Amazon DynamoDB – For more information, see [Using object mapping to migrate data to DynamoDB](CHAP_Target.DynamoDB.md#CHAP_Target.DynamoDB.ObjectMapping)\.
Amazon Kinesis – For more information, see [Using object mapping to migrate data to a Kinesis data stream](CHAP_Target.Kinesis.md#CHAP_Target.Kinesis.ObjectMapping)\.
Apache Kafka – For more information, see [Using object mapping to migrate data to a Kafka topic](CHAP_Target.Kafka.md#CHAP_Target.Kafka.ObjectMapping)\.