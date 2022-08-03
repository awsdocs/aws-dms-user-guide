# Selection rules and actions<a name="CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Selections"></a>

Using table mapping, you can specify what tables, views, and schemas you want to work with by using selection rules and actions\. For table\-mapping rules that use the selection rule type, you can apply the following values\. 

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Selections.html)

**Example Migrate all tables in a schema**  
The following example migrates all tables from a schema named `Test` in your source to your target endpoint\.  

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
        }
    ]
}
```

**Example Migrate some tables in a schema**  
The following example migrates all tables except those starting with `DMS` from a schema named `Test` in your source to your target endpoint\.  

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
            "rule-type": "selection",
            "rule-id": "2",
            "rule-name": "2",
            "object-locator": {
                "schema-name": "Test",
                "table-name": "DMS%"
            },
            "rule-action": "exclude"
        }
    ]
}
```

**Example Migrate a specified single table in single schema**  
The following example migrates the `Customer` table from the `NewCust` schema in your source to your target endpoint\.  

```
{
    "rules": [
        {
            "rule-type": "selection",
            "rule-id": "1",
            "rule-name": "1",
            "object-locator": {
                "schema-name": "NewCust",
                "table-name": "Customer"
            },
            "rule-action": "explicit"
        }
    ]
}
```
You can explicitly select on multiple tables and schemas by specifying multiple selection rules\.

**Example Migrate tables in a set order**  
The following example migrates two tables\. Table `loadfirst` \(with priority 2\) is migrated before table `loadsecond`\.  

```
{
"rules": [
     {
            "rule-type": "selection",
            "rule-id": "1",
            "rule-name": "1",
            "object-locator": {
                "schema-name": "Test",
                "table-name": "loadsecond"
                 },
        "rule-action": "include",
	    "load-order": "1"
        },
	{
	    "rule-type": "selection",
            "rule-id": "2",
            "rule-name": "2",
            "object-locator": {
                "schema-name": "Test",
                "table-name": "loadfirst"
            },
            "rule-action": "include",
	    "load-order": "2"
         }
    ]    
}
```

**Example Migrate some views in a schema**  
The following example migrates some views from a schema named `Test` in your source to equivalent tables in your target\.  

```
{
   "rules": [
        {
           "rule-type": "selection",
           "rule-id": "2",
           "rule-name": "2",
           "object-locator": {
               "schema-name": "Test",
               "table-name": "view_DMS%",
               "table-type": "view"
            },
           "rule-action": "include"
        }
    ]
}
```

**Example Migrate all tables and views in a schema**  
The following example migrates all tables and views from a schema named `report` in your source to equivalent tables in your target\.  

```
{
   "rules": [
        {
           "rule-type": "selection",
           "rule-id": "3",
           "rule-name": "3",
           "object-locator": {
               "schema-name": "report",
               "table-name": "%",
               "table-type": "all"
            },
           "rule-action": "include"
        }
    ]
}
```