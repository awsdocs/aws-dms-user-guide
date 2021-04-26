# Using table mapping to specify task settings<a name="CHAP_Tasks.CustomizingTasks.TableMapping"></a>

Table mapping uses several types of rules to specify the data source, source schema, data, and any transformations that should occur during the task\. You can use table mapping to specify individual tables in a database to migrate and the schema to use for the migration\. 

When working with table mapping, you can use filters to specify data that you want replicated from table columns\. In addition, you can use transformations to modify selected schemas, tables, or views before they are written to the target database\.

**Topics**
+ [Specifying table selection and transformations rules from the console](CHAP_Tasks.CustomizingTasks.TableMapping.Console.md)
+ [Specifying table selection and transformations rules using JSON](CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.md)
+ [Selection rules and actions](CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Selections.md)
+ [Transformation rules and actions](CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Transformations.md)
+ [Using transformation rule expressions to define column content](CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Expressions.md)
+ [Table and collection settings rules and operations](CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Tablesettings.md)
+ [Using source filters](CHAP_Tasks.CustomizingTasks.Filters.md)

**Note**  
When working with table mapping for a MongoDB source endpoint, you can use filters to specify data that you want replicated, and specify a database name in place of the `schema_name`\. Or, you can use the default `"%"`\.