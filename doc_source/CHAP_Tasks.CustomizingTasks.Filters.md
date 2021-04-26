# Using source filters<a name="CHAP_Tasks.CustomizingTasks.Filters"></a>

You can use source filters to limit the number and type of records transferred from your source to your target\. For example, you can specify that only employees with a location of headquarters are moved to the target database\. Filters are part of a selection rule\. You apply filters on a column of data\. 

Source filters must follow these constraints:
+ A selection rule can have no filters or one or more filters\.
+ Every filter can have one or more filter conditions\.
+ If more than one filter is used, the list of filters is combined as if using an AND operator between the filters\.
+ If more than one filter condition is used within a single filter, the list of filter conditions is combined as if using an OR operator between the filter conditions\.
+ Filters are only applied when `rule-action = 'include'`\.
+ Filters require a column name and a list of filter conditions\. Filter conditions must have a filter operator that is associated with either one value, two values, or no value, depending on the operator\.
+ You can specify no more than one filter condition within a single filter if you specify a negative operator like `noteq` \(not equal to\), `notbetween` \(not equal to or between two values\), or `notnull` \(no `NULL` values\)\.
+ Column names, table names, view names, and schema names are case\-sensitive\. 

The following limitations apply to using source filters:
+ Filters don't calculate columns of right\-to\-left languages\.
+ Don't apply filters to LOB columns\.
+ Apply filters only to *immutable* columns, which aren't updated after creation\. If source filters are applied to *mutable* columns, which can be updated after creation, adverse behavior can result\. 

  For example, a filter to exclude or include specific rows in a column always excludes or includes the specified rows even if the rows are later changed\. Suppose that you exclude or include rows 1–10 in column A, and they later change to become rows 11–20\. In this case, they continue to be excluded or included even when the data is no longer the same\.

  Similarly, suppose that a row outside of the filter's scope is later updated \(or updated and deleted\), and should then be excluded or included as defined by the filter\. In this case, it's replicated at the target\.

## Creating source filter rules in JSON<a name="CHAP_Tasks.CustomizingTasks.Filters.Applying"></a>

You can create source filters using the JSON `filters` parameter of a selection rule\. The `filters` parameter specifies an array of one or more JSON objects\. Each object has parameters that specify the source filter type, column name, and filter conditions\. These filter conditions include one or more filter operators and filter values\. 

The following table shows the parameters for specifying source filtering in a `filters` object\.


|  Parameter  |  Value  | 
| --- | --- | 
|   `filter-type`   | source | 
|  `column-name`  |  A parameter with the name of the source column to which you want the filter applied\. The name is case\-sensitive\.  | 
|  `filter-conditions`  | An array of one or more objects containing a filter\-operator parameter and zero or more associated value parameters, depending on the filter\-operator value\. | 
|  `filter-operator`  |  A parameter with one of the following values: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Tasks.CustomizingTasks.Filters.html)  | 
|  `value` or `start-value` and `end-value` or no values  |  Zero or more value parameters associated with `filter-operator`: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Tasks.CustomizingTasks.Filters.html)  | 

The examples following show some common ways to use source filters\.

**Example Single filter**  
The filter following replicates all employees where `empid >= 100` to the target database\.  

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

**Example Multiple filter operators**  
The filter following applies multiple filter operators to a single column of data\. The filter replicates all employees where `(empid <= 10)` OR `(empid is between 50 and 75)` OR `(empid >= 100)` to the target database\.   

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
                "filter-operator": "lte",
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

**Example Multiple filters**  
The filters following apply multiple filters to two columns in a table\. The filter replicates all employees where `(empid <= 100)` AND `(dept = tech)` to the target database\.   

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
                "filter-operator": "lte",
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

**Example Filtering NULL values**  
The filter following shows how to filter on empty values\. It replicates all employees where `dept = NULL` to the target database\.  

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
            "column-name": "dept",
            "filter-conditions": [{
                "filter-operator": "null"
            }]
        }]
    }]
}
```

**Example Filtering using NOT operators**  
Some of the operators can be used in the negative form\. The filter following replicates all employees where `(empid is < 50) OR (empid is > 75)` to the target database\.  

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
                "filter-operator": "notbetween",
                "start-value": "50",
                "end-value": "75"
            }]
        }]
    }]
}
```

**Example Multiple filters using NOT operators**  
You can use only one negative filter condition within a single filter \(OR operation\)\. However, you can use multiple filters \(`AND` operation\) with the a negative filter condition\. The filter following replicates all employees where `(empid != 50) AND (dept is not NULL)` to the target database\.  

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
                "filter-operator": "noteq",
                "value": "50"
            }]
        }, {
            "filter-type": "source",
            "column-name": "dept",
            "filter-conditions": [{
                "filter-operator": "notnull"
            }]
        }]
    }]
}
```

## Filtering by time and date<a name="CHAP_Tasks.CustomizingTasks.Filters.Dates"></a>

When selecting data to import, you can specify a date or time as part of your filter criteria\. AWS DMS uses the date format YYYY\-MM\-DD and the time format YYYY\-MM\-DD HH:MM:SS for filtering\. The AWS DMS comparison functions follow the SQLite conventions\. For more information about SQLite data types and date comparisons, see [Datatypes in SQLite version 3](https://sqlite.org/datatype3.html) in the SQLite documentation\.

The filter following shows how to filter on a date\. It replicates all employees where `empstartdate >= January 1, 2002` to the target database\.

**Example Single date filter**  

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