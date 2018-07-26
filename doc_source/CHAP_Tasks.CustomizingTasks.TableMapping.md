# Using Table Mapping to Specify Task Settings<a name="CHAP_Tasks.CustomizingTasks.TableMapping"></a>

Table mapping uses several types of rules to specify the data source, source schema, data, and any transformations that should occur during the task\. You can use table mapping to specify individual tables in a database to migrate and the schema to use for the migration\. In addition, you can use filters to specify what data from a given table column you want replicated and you can use transformations to modify the data written to the target database\. 

## Selection and Transformation Table Mapping Using the AWS Management Console<a name="CHAP_Tasks.CustomizingTasks.TableMapping.Console"></a>

You can use the AWS Management Console to perform table mapping, including specifying table selection and transformations\. In the AWS console user interface, you use the **Where** section to specify the schema, table, and action \(include or exclude\)\. You use the **Filter** section to specify the column name in a table and the conditions you want to apply to the replication task\. Together these two actions create a selection rule\.

Transformations can be included in a table mapping after you have specified at least one selection rule\. Transformations can be used to rename a schema or table, add a prefix or suffix to a schema or table, or remove a table column\.

The following example shows how to set up selection rules for a table called Customers in a schema called EntertainmentAgencySample\. Note that the **Guided** tab, where you create selection rules and transformations, only appears when you have a source endpoint that has schema and table information\.

**To specify a table selection, filter criteria, and transformations using the AWS console**

1. Sign in to the AWS Management Console and choose AWS DMS\. Note that if you are signed in as an AWS Identity and Access Management \(IAM\) user, you must have the appropriate permissions to access AWS DMS\. For more information on the permissions required, see [IAM Permissions Needed to Use AWS DMS](CHAP_Security.IAMPermissions.md)\.

1. On the **Dashboard** page, choose **Tasks**\.

1. Select **Create Task**\.

1. Enter the task information, including **Task name**, **Replication instance**, **Source endpoint**, **Target endpoint**, and **Migration type**\. Select **Guided** from the **Table mappings** section\.  
![\[Schema and table selection\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-Tasks-selecttransfrm0.png)

1. In the **Table mapping** section, select the schema name and table name\. You can use the "%" as a wildcard value when specifying the table name\. Specify the action to be taken, to include or exclude data defined by the filter\.  
![\[Schema and table selection\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-Tasks-selecttransfrm.png)

1. You specify filter information using the **Add column filter** and the **Add condition **links\. First, select **Add column filter** to specify a column and conditions\. Select **Add condition ** to add additional conditions\. The following example shows a filter for the **Customers** table that includes **AgencyIDs** between **01** and **85**\.  
![\[Schema and table selection\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-Tasks-filter.png)

1. When you have created the selections you want, select **Add selection rule**\.

1. After you have created at least one selection rule, you can add a transformation to the task\. Select **add transformation rule**\.  
![\[transformation rule\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-Tasks-transform1.png)

1. Select the target you want to transform and enter the additional information requested\. The following example shows a transformation that deletes the **AgencyStatus** column from the **Customer** table\.  
![\[transformation rule\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-Tasks-transform2.png)

1. Choose **Add transformation rule**\.

1. You can add additional selection rules or transformations by selecting the **add selection rule** or **add transformation rule**\. When you are finished, choose **Create task**\.  
![\[transformation rule\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-Tasks-transform3.png)

## Selection and Transformation Table Mapping using JSON<a name="CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation"></a>

 Table mappings can be created in the JSON format\. If you create a migration task using the AWS DMS Management Console, you can enter the JSON directly into the table mapping box\. If you use the AWS Command Line Interface \(AWS CLI\) or AWS Database Migration Service API to perform migrations, you can create a JSON file to specify the table mappings that you want to occur during migration\.

You can specify what tables or schemas you want to work with, and you can perform schema and table transformations\. You create table mapping rules using the `selection` and `transformation` rule types\. 

### Selection Rules and Actions<a name="CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Selections"></a>

Using table mapping, you can specify what tables or schemas you want to work with by using selection rules and actions\. For table mapping rules that use the selection rule type, the following values can be applied: 

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Tasks.CustomizingTasks.TableMapping.html)

**Example Migrate All Tables in a Schema**  
The following example migrates all tables from a schema named **Test** in your source to your target endpoint:  

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

**Example Migrate Some Tables in a Schema**  
The following example migrates all tables except those starting with **DMS** from a schema named **Test** in your source to your target endpoint:  

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

### Transformation Rules and Actions<a name="CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Transformations"></a>

You use the transformation actions to specify any transformations you want to apply to the selected schema or table\. Transformation rules are optional\. 

For table mapping rules that use the transformation rule type, the following values can be applied: 

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Tasks.CustomizingTasks.TableMapping.html)

**Example Rename a Schema**  
The following example renames a schema from **Test** in your source to **Test1** in your target endpoint:  

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

**Example Rename a Table**  
The following example renames a table from **Actor** in your source to **Actor1** in your target endpoint:  

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

**Example Rename a Column**  
The following example renames a column in table **Actor** from **first\_name** in your source to **fname** in your target endpoint:  

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

**Example Remove a Column**  
The following example transforms the table named **Actor** in your source to remove all columns starting with the characters **col** from it in your target endpoint:  

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

**Example Convert to Lowercase**  
The following example converts a table name from **ACTOR** in your source to **actor** in your target endpoint:  

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

**Example Convert to Uppercase**  
The following example converts all columns in all tables and all schemas from lowercase in your source to uppercase in your target endpoint:  

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

**Example Add a Prefix**  
The following example transforms all tables in your source to add the prefix **DMS\_** to them in your target endpoint:  

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

**Example Replace a Prefix**  
The following example transforms all columns containing the prefix **Pre\_** in your source to replace the prefix with **NewPre\_** in your target endpoint:  

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

**Example Remove a Suffix**  
The following example transforms all tables in your source to remove the suffix **\_DMS** from them in your target endpoint:  

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

## Using Source Filters<a name="CHAP_Tasks.CustomizingTasks.Filters"></a>

You can use source filters to limit the number and type of records transferred from your source to your target\. For example, you can specify that only employees with a location of headquarters are moved to the target database\. Filters are part of a selection rule\. You apply filters on a column of data\. 

Source filters must follow these constraints:
+ A selection rule can have no filters or one or more filters\.
+ Every filter can have one or more filter conditions\.
+ If more than one filter is used, the list of filters will be combined as if using an AND operator between the filters\.
+ If more than one filter condition is used within a single filter, the list of filter conditions will be combined as if using an OR operator between the filter conditions\.
+ Filters are only applied when `rule-action = 'include'`\.
+ Filters require a column name and a list of filter conditions\. Filter conditions must have a filter operator and a value\.
+ Column names, table names, and schema names are case sensitive\. 

### Filtering by Time and Date<a name="CHAP_Tasks.CustomizingTasks.Filters.Dates"></a>

When selecting data to import, you can specify a date or time as part of your filter criteria\. AWS DMS uses the date format YYYY\-MM\-DD and the time format YYYY\-MM\-DD HH:MM:SS for filtering\. The AWS DMS comparison functions follow the SQLite conventions\. For more information about SQLite data types and date comparisons, see [ Datatypes In SQLite Version 3](https://sqlite.org/datatype3.html)\.

The following example shows how to filter on a date\.

**Example Single Date Filter**  
The following filter replicates all employees where `empstartdate >= January 1, 2002` to the target database\.  

```
 {
	"rules": [{
		"rule-type": "selection",
		"rule-id": "1",
		"rule-name": "1",
		"object-locator": {
			"schema-name": "test",
			"table-name": "employee"
		},
		"rule-action": "include",
		"filters": [{
			"filter-type": "source",
			"column-name": "empstartdate",
			"filter-conditions": [{
				"filter-operator": "gte",
				"value": "2002-01-01"
			}]
		}]
	}]
}
```

### Creating Source Filter Rules in JSON<a name="CHAP_Tasks.CustomizingTasks.Filters.Applying"></a>

You can create source filters by specifying a column name, filter condition, filter operator, and a filter value\. 

The following table shows the parameters used for source filtering\.


| Parameter | Value | 
| --- | --- | 
|  filter\-type  | source | 
| column\-name | The name of the source column you want the filter applied to\. The name is case sensitive\. | 
| **filter\-conditions** |  | 
|  filter\-operator | This parameter can be one of the following: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Tasks.CustomizingTasks.TableMapping.html)  | 
|  value | The value of the filter\-operator parameter\. If the filter\-operator is `between`, provide two values, one for start\-value and one for end\-value\. | 

The following examples show some common ways to use source filters\.

**Example Single Filter**  
The following filter replicates all employees where `empid >= 100` to the target database\.  

```
 {
	"rules": [{
		"rule-type": "selection",
		"rule-id": "1",
		"rule-name": "1",
		"object-locator": {
			"schema-name": "test",
			"table-name": "employee"
		},
		"rule-action": "include",
		"filters": [{
			"filter-type": "source",
			"column-name": "empid",
			"filter-conditions": [{
				"filter-operator": "gte",
				"value": "100"
			}]
		}]
	}]
}
```

**Example Multiple Filter Operators**  
The following filter applies multiple filter operators to a single column of data\. The filter replicates all employees where `(empid <=10)` OR `(empid is between 50 and 75)` OR `(empid >= 100)` to the target database\.   

```
{
 	"rules": [{
 		"rule-type": "selection",
 		"rule-id": "1",
 		"rule-name": "1",
 		"object-locator": {
 			"schema-name": "test",
 			"table-name": "employee"
 		},
 		"rule-action": "include",
 		"filters": [{
 			"filter-type": "source",
 			"column-name": "empid",
 			"filter-conditions": [{
 				"filter-operator": "ste",
 				"value": "10"
 			}, {
 				"filter-operator": "between",
 				"start-value": "50",
 				"end-value": "75"
 			}, {
 				"filter-operator": "gte",
 				"value": "100"
 			}]
 		}]
 	}]
 }
```

**Example Multiple Filters**  
The following filter applies multiple filters to two columns in a table\. The filter replicates all employees where `(empid <= 100)` AND `(dept= tech)` to the target database\.   

```
{
	"rules": [{
		"rule-type": "selection",
		"rule-id": "1",
		"rule-name": "1",
		"object-locator": {
			"schema-name": "test",
			"table-name": "employee"
		},
		"rule-action": "include",
		"filters": [{
			"filter-type": "source",
			"column-name": "empid",
			"filter-conditions": [{
				"filter-operator": "ste",
				"value": "100"
			}]
		}, {
			"filter-type": "source",
			"column-name": "dept",
			"filter-conditions": [{
				"filter-operator": "eq",
				"value": "tech"
			}]
		}]
	}]
}
```