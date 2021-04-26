# Using transformation rule expressions to define column content<a name="CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Expressions"></a>

To define content for new and existing columns, you can use an expression within a transformation rule\. For example, using expressions you can add a column or replicate source table headers to a target\. You can also use expressions to flag records on target tables as inserted, updated, or deleted at the source\. 

**Topics**
+ [Adding a column using an expression](#CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Expressions-adding)
+ [Flagging target records using an expression](#CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Expressions-Flagging)
+ [Replicating source table headers using expressions](#CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Expressions-Headers)

## Adding a column using an expression<a name="CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Expressions-adding"></a>

To add columns to tables using an expression in a transformation rule, use an `add-column` rule action and a `column` rule target\.

The following example adds a new column to the `ITEM` table\. It sets the new column name to `FULL_NAME`, with a data type of `string`, 50 characters long\. The expression concatenates the values of two existing columns, `FIRST_NAME` and `LAST_NAME`, to evaluate to `FULL_NAME`\. The `schema-name`, `table-name`, and expression parameters refer to objects in the source database table\. `Value` and the `data-type` block refer to objects in the target database table\.

```
{
    "rules": [
        {
            "rule-type": "selection", 
            "rule-id": "1",
            "rule-name": "1",
            "object-locator": {
                "schema-name": "Test",
                "table-name": "%"
            },
            "rule-action": "include"
        },
        {
            "rule-type": "transformation",
            "rule-id": "2",
            "rule-name": "2",
            "rule-action": "add-column",
            "rule-target": "column",
            "object-locator": {
                "schema-name": "Test",
                "table-name": "ITEM"
            },
            "value": "FULL_NAME",
            "expression": "$FIRST_NAME||'_'||$LAST_NAME",
            "data-type": {
                 "type": "string",
                 "length": 50
            }
        }
    ]
}
```

## Flagging target records using an expression<a name="CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Expressions-Flagging"></a>

To flag records in target tables as inserted, updated, or deleted in the source table, use an expression in a transformation rule\. The expression uses an `operation_indicator` function to flag records\. Records deleted from the source aren't deleted from the target\. Instead, the target record is flagged with a user\-provided value to indicate that it was deleted from the source\.

**Note**  
The `operation_indicator` function works only on tables that have a primary key\.

For example, the following transformation rule first adds a new `Operation` column to a target table\. It then updates the column with the value `D` whenever a record is deleted from a source table\.

```
{
      "rule-type": "transformation",
      "rule-id": "2",
      "rule-name": "2",
      "rule-target": "column",
      "object-locator": {
        "schema-name": "%",
        "table-name": "%"
      },
      "rule-action": "add-column",
      "value": "Operation",
      "expression": "operation_indicator('D', 'U', 'I')",
      "data-type": {
        "type": "string",
        "length": 50
      }
}
```

## Replicating source table headers using expressions<a name="CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Expressions-Headers"></a>

By default, headers for source tables aren't replicated to the target\. To indicate which headers to replicate, use a transformation rule with an expression that includes the table column header\. 

You can use the following column headers in expressions\. 

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Expressions.html)

The following example adds a new column to the target by using the stream position value from the source\. For SQL Server, the stream position value is the LSN for the source endpoint\. For Oracle, the stream position value is the SCN for the source endpoint\.

```
{
      "rule-type": "transformation",
     "rule-id": "2",
      "rule-name": "2",
      "rule-target": "column",
      "object-locator": {
        "schema-name": "%",
        "table-name": "%"
      },
      "rule-action": "add-column",
      "value": "transact_id",
      "expression": "$AR_H_STREAM_POSITION",
      "data-type": {
        "type": "string",
        "length": 50
      }
    }
```

The following example adds a new column to the target that has a unique incrementing number from the source\. This value represents a 35 digit unique number at task level\. The first 16 digits are part of a timestamp, and the last 19 digits are the record\_id number incremented by the DBMS\.

```
{
"rule-type": "transformation",
"rule-id": "2",
"rule-name": "2",
"rule-target": "column",
"object-locator": {
"schema-name": "%",
"table-name": "%"
},
"rule-action": "add-column",
"value": "transact_id",
"expression": "$AR_H_CHANGE_SEQ",
"data-type": {
"type": "string",
"length": 50
}
}
```