# Transformation rules and actions<a name="CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Transformations"></a>

You use the transformation actions to specify any transformations you want to apply to the selected schema, table, or view\. Transformation rules are optional\. 

**Note**  
AWS DMS doesn't support more than one transformation rule per schema level or per table level\. However, AWS DMS does support more than one transformation rule per column level\.  
Performing multiple rule actions on columns that include converting to lowercase can prevent some transformations from happening correctly\. For example, using the `convert-lowercase` and `change-data-type` rule actions together might not succeed\. Instead, use the `convert-lowercase` rule action separately\. 

For table\-mapping rules that use the transformation rule type, you can apply the following values\. 

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Transformations.html)

**Example Rename a schema**  
The following example renames a schema from `Test` in your source to `Test1` in your target\.  

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
            "rule-action": "rename",
            "rule-target": "schema",
            "object-locator": {
                "schema-name": "Test"
            },
            "value": "Test1"
        }
    ]
}
```

**Example Rename a table**  
The following example renames a table from `Actor` in your source to `Actor1` in your target\.  

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
            "rule-action": "rename",
            "rule-target": "table",
            "object-locator": {
                "schema-name": "Test",
                "table-name": "Actor"
            },
            "value": "Actor1"
        }
    ]
}
```

**Example Rename a column**  
The following example renames a column in table `Actor` from `first_name` in your source to `fname` in your target\.  

```
{
    "rules": [
        {
            "rule-type": "selection",
            "rule-id": "1",
            "rule-name": "1",
            "object-locator": {
                "schema-name": "test",
                "table-name": "%"
            },
            "rule-action": "include"
        },
         {
            "rule-type": "transformation",
            "rule-id": "4",
            "rule-name": "4",
            "rule-action": "rename",
            "rule-target": "column",
            "object-locator": {
                "schema-name": "test",
                "table-name": "Actor",
                "column-name" : "first_name"
            },
            "value": "fname"
        }
    ]
}
```

**Example Rename an Oracle table tablespace**  
The following example renames the table tablespace named `SetSpace` for a table named `Actor` in your Oracle source to `SceneTblSpace` in your Oracle target endpoint\.  

```
{
    "rules": [
        {
            "rule-type": "selection",
            "rule-id": "1",
            "rule-name": "1",
            "object-locator": {
                "schema-name": "Play",
                "table-name": "%"
            },
            "rule-action": "include"
        },
        {
            "rule-type": "transformation",
            "rule-id": "2",
            "rule-name": "2",
            "rule-action": "rename",
            "rule-target": "table-tablespace",
            "object-locator": {
                "schema-name": "Play",
                "table-name": "Actor",
                "table-tablespace-name: "SetSpace"
            },
            "value": "SceneTblSpace"
        }
    ]
}
```

**Example Rename an Oracle index tablespace**  
The following example renames the index tablespace named `SetISpace` for a table named `Actor` in your Oracle source to `SceneIdxSpace` in your Oracle target endpoint\.  

```
{
    "rules": [
        {
            "rule-type": "selection",
            "rule-id": "1",
            "rule-name": "1",
            "object-locator": {
                "schema-name": "Play",
                "table-name": "%"
            },
            "rule-action": "include"
        },
        {
            "rule-type": "transformation",
            "rule-id": "2",
            "rule-name": "2",
            "rule-action": "rename",
            "rule-target": "table-tablespace",
            "object-locator": {
                "schema-name": "Play",
                "table-name": "Actor",
                "table-tablespace-name: "SetISpace"
            },
            "value": "SceneIdxSpace"
        }
    ]
}
```

**Example Add a column**  
The following example adds a `datetime` column to the table `Actor` in schema `test`\.  

```
{
    "rules": [
        {
            "rule-type": "selection",
            "rule-id": "1",
            "rule-name": "1",
            "object-locator": {
                "schema-name": "test",
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
                "schema-name": "test",
                "table-name": "actor"
            },
            "value": "last_updated",
            "data-type": {
                "type": "datetime",
                "precision": 6
            }
        }
    ]
}
```

**Example Remove a column**  
The following example transforms the table named `Actor` in your source to remove all columns starting with the characters `col` from it in your target\.  

```
{
 	"rules": [{
		"rule-type": "selection",
		"rule-id": "1",
		"rule-name": "1",
		"object-locator": {
			"schema-name": "test",
			"table-name": "%"
		},
		"rule-action": "include"
	}, {
		"rule-type": "transformation",
		"rule-id": "2",
		"rule-name": "2",
		"rule-action": "remove-column",
		"rule-target": "column",
		"object-locator": {
			"schema-name": "test",
			"table-name": "Actor",
			"column-name": "col%"
		}
	}]
 }
```

**Example Convert to lowercase**  
The following example converts a table name from `ACTOR` in your source to `actor` in your target\.  

```
{
	"rules": [{
		"rule-type": "selection",
		"rule-id": "1",
		"rule-name": "1",
		"object-locator": {
			"schema-name": "test",
			"table-name": "%"
		},
		"rule-action": "include"
	}, {
		"rule-type": "transformation",
		"rule-id": "2",
		"rule-name": "2",
		"rule-action": "convert-lowercase",
		"rule-target": "table",
		"object-locator": {
			"schema-name": "test",
			"table-name": "ACTOR"
		}
	}]
}
```

**Example Convert to uppercase**  
The following example converts all columns in all tables and all schemas from lowercase in your source to uppercase in your target\.  

```
{
    "rules": [
        {
            "rule-type": "selection",
            "rule-id": "1",
            "rule-name": "1",
            "object-locator": {
                "schema-name": "test",
                "table-name": "%"
            },
            "rule-action": "include"
        },
        {
            "rule-type": "transformation",
            "rule-id": "2",
            "rule-name": "2",
            "rule-action": "convert-uppercase",
            "rule-target": "column",
            "object-locator": {
                "schema-name": "%",
                "table-name": "%",
                "column-name": "%"
            }
        }
    ]
}
```

**Example Add a prefix**  
The following example transforms all tables in your source to add the prefix `DMS_` to them in your target\.  

```
{
 	"rules": [{
		"rule-type": "selection",
		"rule-id": "1",
		"rule-name": "1",
		"object-locator": {
			"schema-name": "test",
			"table-name": "%"
		},
		"rule-action": "include"
	}, {
		"rule-type": "transformation",
		"rule-id": "2",
		"rule-name": "2",
		"rule-action": "add-prefix",
		"rule-target": "table",
		"object-locator": {
			"schema-name": "test",
			"table-name": "%"
		},
		"value": "DMS_"
	}]
 
}
```

**Example Replace a prefix**  
The following example transforms all columns containing the prefix `Pre_` in your source to replace the prefix with `NewPre_` in your target\.  

```
{
    "rules": [
        {
            "rule-type": "selection",
            "rule-id": "1",
            "rule-name": "1",
            "object-locator": {
                "schema-name": "test",
                "table-name": "%"
            },
            "rule-action": "include"
        },
        {
            "rule-type": "transformation",
            "rule-id": "2",
            "rule-name": "2",
            "rule-action": "replace-prefix",
            "rule-target": "column",
            "object-locator": {
                "schema-name": "%",
                "table-name": "%",
                "column-name": "%"
            },
            "value": "NewPre_",
            "old-value": "Pre_"
        }
    ]
}
```

**Example Remove a suffix**  
The following example transforms all tables in your source to remove the suffix `_DMS` from them in your target\.  

```
{
	"rules": [{
		"rule-type": "selection",
		"rule-id": "1",
		"rule-name": "1",
		"object-locator": {
			"schema-name": "test",
			"table-name": "%"
		},
		"rule-action": "include"
	}, {
		"rule-type": "transformation",
		"rule-id": "2",
		"rule-name": "2",
		"rule-action": "remove-suffix",
		"rule-target": "table",
		"object-locator": {
			"schema-name": "test",
			"table-name": "%"
		},
		"value": "_DMS"
	}]
}
```

**Example Define a primary key**  
The following example defines a primary key named `ITEM-primary-key` on three columns of the `ITEM` table migrated to your target endpoint\.  

```
{
	"rules": [{
		"rule-type": "selection",
		"rule-id": "1",
		"rule-name": "1",
		"object-locator": {
			"schema-name": "inventory",
			"table-name": "%"
		},
		"rule-action": "include"
	}, {
		"rule-type": "transformation",
		"rule-id": "2",
		"rule-name": "2",
		"rule-action": "define-primary-key",
		"rule-target": "table",
		"object-locator": {
			"schema-name": "inventory",
			"table-name": "ITEM"
		},
		"primary-key-def": {
			"name": ITEM-primary-key,
			"columns": [
				"ITEM-NAME",
				"BOM-MODEL-NUM",
				"BOM-PART-NUM"
			]
              }
	}]
}
```

**Example Define a unique index**  
The following example defines a unique index named `ITEM-unique-idx` on three columns of the `ITEM` table migrated to your target endpoint\.  

```
{
	"rules": [{
		"rule-type": "selection",
		"rule-id": "1",
		"rule-name": "1",
		"object-locator": {
			"schema-name": "inventory",
			"table-name": "%"
		},
		"rule-action": "include"
	}, {
		"rule-type": "transformation",
		"rule-id": "2",
		"rule-name": "2",
		"rule-action": "define-primary-key",
		"rule-target": "table",
		"object-locator": {
			"schema-name": "inventory",
			"table-name": "ITEM"
		},
		"primary-key-def": {
			"name": ITEM-unique-idx,
			"origin": unique-index,
			"columns": [
				"ITEM-NAME",
				"BOM-MODEL-NUM",
				"BOM-PART-NUM"
			]
              }
	}]
}
```

**Example Change data type of target column**  
The following example changes the data type of a target column named `SALE_AMOUNT` from an existing data type to `int8`\.  

```
{
    "rule-type": "transformation",
    "rule-id": "1",
    "rule-name": "RuleName 1",
    "rule-action": "change-data-type",
    "rule-target": "column",
    "object-locator": {
        "schema-name": "dbo",
        "table-name": "dms",
        "column-name": "SALE_AMOUNT"
    },
    "data-type": {
        "type": "int8"
    }
}
```

**Example Add a before image column**  
For a source column named `emp_no`, the transformation rule in the example following adds a new column named `BI_emp_no` in the target\.  

```
{
	"rules": [{
			"rule-type": "selection",
			"rule-id": "1",
			"rule-name": "1",
			"object-locator": {
				"schema-name": "%",
				"table-name": "%"
			},
			"rule-action": "include"
		},
		{
			"rule-type": "transformation",
			"rule-id": "2",
			"rule-name": "2",
			"rule-target": "column",
			"object-locator": {
				"schema-name": "%",
				"table-name": "employees"
			},
			"rule-action": "add-before-image-columns",
			"before-image-def": {
				"column-prefix": "BI_",
				"column-suffix": "",
				"column-filter": "pk-only"
			}
		}
	]
}
```
Here, the statement following populates a `BI_emp_no` column in the corresponding row with 1\.  

```
UPDATE employees SET emp_no = 3 WHERE emp_no = 1;
```
When writing CDC updates to supported AWS DMS targets, the `BI_emp_no` column makes it possible to tell which rows have updated values in the `emp_no` column\.