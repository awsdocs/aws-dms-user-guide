# Table and collection settings rules and operations<a name="CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Tablesettings"></a>

Use table settings to specify any settings that you want to apply to a selected table or view for a specified operation\. Table\-settings rules are optional, depending on your endpoint and migration requirements\. 

Instead of using tables and views, MongoDB and Amazon DocumentDB databases store data records as documents that are gathered together in *collections*\. A single database for any MongoDB or Amazon DocumentDB endpoint is a specific set of collections identified by the database name\. 

When migrating from a MongoDB or Amazon DocumentDB source, you work with parallel load settings slightly differently\. In this case, consider the autosegmentation or range segmentation type of parallel load settings for selected collections rather than tables and views\.

**Topics**
+ [Wildcards in table\-settings are restricted](#CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Tablesettings.Wildcards)
+ [Using parallel load for selected tables, views, and collections](#CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Tablesettings.ParallelLoad)
+ [Specifying LOB settings for a selected table or view](#CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Tablesettings.LOB)
+ [Table\-settings examples](#CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Tablesettings.Examples)

For table\-mapping rules that use the table\-settings rule type, you can apply the following parameters\. 

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Tablesettings.html)

## Wildcards in table\-settings are restricted<a name="CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Tablesettings.Wildcards"></a>

Using the percent wildcard \(`"%"`\) in `"table-settings"` rules is not supported for source databases as shown following\.

```
{
    "rule-type": "table-settings",
    "rule-id": "8",
    "rule-name": "8",
    "object-locator": {
        "schema-name": "ipipeline-prod",            
        "table-name": "%"
    },
    "parallel-load": {
        "type": "partitions-auto",
        "number-of-partitions": 16,
        "collection-count-from-metadata": "true",
        "max-records-skip-per-page": 1000000,
        "batch-size": 50000
    }
  }
```

If you use `"%"` in the `"table-settings"` rules as shown, AWS DMS returns the exception following\.

```
Error in mapping rules. Rule with ruleId = x failed validation. Exact 
schema and table name required when using table settings rule.
```

In addition, AWS recommends that you don't load a great number of large collections using a single task with `parallel-load`\. Note that AWS DMS limits resource contention as well as the number of segments loaded in parallel by the value of the `MaxFullLoadSubTasks` task settings parameter, with a maximum value of 49\. 

Instead, specify all collections for your source database for the largest collections by specifying each `"schema-name"` and `"table-name"` individually\. Also, scale up your migration properly\. For example, run multiple tasks across a sufficient number of replication instances to handle a great number of large collections in your database\.

## Using parallel load for selected tables, views, and collections<a name="CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Tablesettings.ParallelLoad"></a>

To speed up migration and make it more efficient, you can use parallel load for selected relational tables, views, and collections\. In other words, you can migrate a single segmented table, view, or collection using several threads in parallel\. To do this, AWS DMS splits a full\-load task into threads, with each table segment allocated to its own thread\. 

Using this parallel\-load process, you can first have multiple threads unload multiple tables, views, and collections in parallel from the source endpoint\. You can then have multiple threads migrate and load the same tables, views, and collections in parallel to the target endpoint\. For some database engines, you can segment the tables and views by existing partitions or subpartitions\. For other database engines, you can have AWS DMS automatically segment collections according to specific parameters \(autosegmentation\)\. Otherwise, you can segment any table, view, or collection by ranges of column values that you specify\.

Parallel load is supported for the following source endpoints:
+ Oracle
+ Microsoft SQL Server
+ MySQL
+ PostgreSQL
+ IBM Db2
+ SAP Adaptive Server Enterprise \(ASE\)
+ MongoDB \(only supports the autosegmentation and range segmentation options of a parallel full load\)
+ Amazon DocumentDB \(only supports the autosegmentation and range segmentation options of a parallel full load\)

For MongoDB and Amazon DocumentDB endpoints, AWS DMS supports the following data types for columns that are partition keys for the range segmentation option of a parallel full load\.
+ Double
+ String
+ ObjectId
+ 32 bit integer
+ 64 bit integer

Parallel load for use with table\-setting rules are supported for the following target endpoints:
+ Oracle
+ Microsoft SQL Server
+ MySQL
+ PostgreSQL
+ Amazon S3
+ SAP Adaptive Server Enterprise \(ASE\)
+ Amazon Redshift
+ MongoDB \(only supports the autosegmentation and range segmentation options of a parallel full load\)
+ Amazon DocumentDB \(only supports the autosegmentation and range segmentation options of a parallel full load\)

To specify the maximum number of tables, views, and collections to load in parallel, use the `MaxFullLoadSubTasks` task setting\. To specify the maximum number of threads per table, view, or collection for a parallel\-load task, use the `ParallelLoadThreads` task setting\. To specify the buffer size for a parallel load task, use the `ParallelLoadBufferSize` task setting\. The availability and settings of `ParallelLoadThreads`and `ParallelLoadBufferSize` depend on the target endpoint\.

For more information about the `ParallelLoadThreads` and `ParallelLoadBufferSize` settings, see [Target metadata task settings](CHAP_Tasks.CustomizingTasks.TaskSettings.TargetMetadata.md)\. For more information about the `MaxFullLoadSubTasks` setting, see [Full\-load task settings](CHAP_Tasks.CustomizingTasks.TaskSettings.FullLoad.md)\. For information specific to target endpoints, see the related topics\.

To use parallel load, create a table\-mapping rule of type `table-settings` with the `parallel-load` option\. Within the `table-settings` rule, you can specify the segmentation criteria for a single table, view, or collection that you want to load in parallel\. To do so, set the `type` parameter of the `parallel-load` option to one of several options\. 

How to do this depends on how you want to segment the table, view, or collection for parallel load:
+ By partitions \(or segments\) – Load all existing table or view partitions \(or segments\) using the `partitions-auto` type\. Or load only selected partitions using the `partitions-list` type with a specified partitions array\.

  For MongoDB and Amazon DocumentDB endpoints only, load all or specified collections by segments that AWS DMS automatically calculates also using the `partitions-auto` type and additional optional `table-settings` parameters\.
+ \(Oracle endpoints only\) By subpartitions – Load all existing table or view subpartitions using the `subpartitions-auto` type\. Or load only selected subpartitions using the `partitions-list` type with a specified `subpartitions` array\.
+ By segments that you define – Load table, view, or collection segments that you define by using column\-value boundaries\. To do so, use the `ranges` type with specified `columns` and `boundaries` arrays\.
**Note**  
PostgreSQL endpoints support only this type of a parallel load\. MongoDB and Amazon DocumentDB as a source endpoints support both this range segmentation type and the autosegmentation type of a parallel full load \(`partitions-auto`\)\.

To identify additional tables, views, or collections to load in parallel, specify additional `table-settings` objects with `parallel-load` options\. 

In the following procedures, you can find out how to code JSON for each parallel\-load type, from the simplest to the most complex\.

**To specify all table, view, or collection partitions, or all table or view subpartitions**
+ Specify `parallel-load` with either the `partitions-auto` type or the `subpartitions-auto` type \(but not both\)\. 

  Every table, view, or collection partition \(or segment\) or subpartition is then automatically allocated to its own thread\.

  For some endpoints, parallel load includes partitions or subpartitions only if they are already defined for the table or view\. For MongoDB and Amazon DocumentDB source endpoints, you can have AWS DMS automatically calculate the partitions \(or segments\) based on optional additional parameters\. These include `number-of-partitions`, `collection-count-from-metadata`, `max-records-skip-per-page`, and `batch-size`\.

**To specify selected table or view partitions, subpartitions, or both**

1. Specify `parallel-load` with the `partitions-list` type\.

1. \(Optional\) Include partitions by specifying an array of partition names as the value of `partitions`\.

   Each specified partition is then allocated to its own thread\.
**Important**  
For Oracle endpoints, make sure partitions and subpartitions aren't overlapping when choosing them for parallel load\. If you use overlapping partitions and subpartitions to load data in parallel, it duplicates entries, or it fails due to a primary key duplicate violation\. 

1. \(Optional\) , For Oracle endpoints only, include subpartitions by specifying an array of subpartition names as the value of `subpartitions`\.

   Each specified subpartition is then allocated to its own thread\.
**Note**  
Parallel load includes partitions or subpartitions only if they are already defined for the table or view\.

You can specify table or view segments as ranges of column values\. When you do so, be aware of these column characteristics:
+ Specifying indexed columns significantly improves performance\.
+ You can specify up to 10 columns\.
+ You can't use columns to define segment boundaries with the following AWS DMS data types: DOUBLE, FLOAT, BLOB, CLOB, and NCLOB
+ Records with null values aren't replicated\.

**To specify table, view, or collection segments as ranges of column values**

1. Specify `parallel-load` with the `ranges` type\.

1. Define a boundary between table or view segments by specifying an array of column names as the value of `columns`\. Do this for every column for which you want to define a boundary between table or view segments\. 

   The order of columns is significant\. The first column is the most significant and the last column is least significant in defining each boundary, as described following\.

1. Define the data ranges for all the table or view segments by specifying a boundary array as the value of `boundaries`\. A *boundary array* is an array of column\-value arrays\. To do so, take the following steps:

   1. Specify each element of a column\-value array as a value that corresponds to each column\. A *column\-value array* represents the upper boundary of each table or view segment that you want to define\. Specify each column in the same order that you specified that column in the `columns` array\.

      Enter values for DATE columns in the format supported by the source\.

   1. Specify each column\-value array as the upper boundary, in order, of each segment from the bottom to the next\-to\-top segment of the table or view\. If any rows exist above the top boundary that you specify, these rows complete the top segment of the table or view\. Thus, the number of range\-based segments is potentially one more than the number of segment boundaries in the boundary array\. Each such range\-based segment is allocated to its own thread\.

      All of the non\-null data is replicated, even if you don't define data ranges for all of the columns in the table or view\.

   For example, suppose that you define three column\-value arrays for columns COL1, COL2, and COL3 as follows\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Tablesettings.html)

   You have defined three segment boundaries for a possible total of four segments\.

   To identify the ranges of rows to replicate for each segment, the replication instance applies a search to these three columns for each of the four segments\. The search is like the following:  
**Segment 1**  
Replicate all rows where the following is true: The first two\-column values are less than or equal to their corresponding **Segment 1** upper boundary values\. Also, the values of the third column are less than its **Segment 1** upper boundary value\.  
**Segment 2**  
Replicate all rows \(except **Segment 1** rows\) where the following is true: The first two\-column values are less than or equal to their corresponding **Segment 2** upper boundary values\. Also, the values of the third column are less than its **Segment 2** upper boundary value\.  
**Segment 3**  
Replicate all rows \(except **Segment 2** rows\) where the following is true: The first two\-column values are less than or equal to their corresponding **Segment 3** upper boundary values\. Also, the values of the third column are less than its **Segment 3** upper boundary value\.  
**Segment 4**  
Replicate all remaining rows \(except the **Segment 1, 2, and 3** rows\)\.

   In this case, the replication instance creates a `WHERE` clause to load each segment as follows:  
**Segment 1**  
`((COL1 < 10) OR ((COL1 = 10) AND (COL2 < 30)) OR ((COL1 = 10) AND (COL2 = 30) AND (COL3 < 105)))`  
**Segment 2**  
`NOT ((COL1 < 10) OR ((COL1 = 10) AND (COL2 < 30)) OR ((COL1 = 10) AND (COL2 = 30) AND (COL3 < 105))) AND ((COL1 < 20) OR ((COL1 = 20) AND (COL2 < 20)) OR ((COL1 = 20) AND (COL2 = 20) AND (COL3 < 120)))`  
**Segment 3**  
`NOT ((COL1 < 20) OR ((COL1 = 20) AND (COL2 < 20)) OR ((COL1 = 20) AND (COL2 = 20) AND (COL3 < 120))) AND ((COL1 < 100) OR ((COL1 = 100) AND (COL2 < 12)) OR ((COL1 = 100) AND (COL2 = 12) AND (COL3 < 99)))`  
**Segment 4**  
`NOT ((COL1 < 100) OR ((COL1 = 100) AND (COL2 < 12)) OR ((COL1 = 100) AND (COL2 = 12) AND (COL3 < 99)))`

## Specifying LOB settings for a selected table or view<a name="CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Tablesettings.LOB"></a>

You can set task LOB settings for one or more tables by creating a table\-mapping rule of type `table-settings` with the `lob-settings` option for one or more `table-settings` objects\. 

Specifying LOB settings for selected tables or views is supported for the following source endpoints:
+ Oracle
+ Microsoft SQL Server
+ MySQL
+ PostgreSQL
+ IBM Db2, depending on the `mode` and `bulk-max-size` settings, described following
+ SAP Adaptive Server Enterprise \(ASE\), depending on the `mode` and `bulk-max-size` settings, as described following

Specifying LOB settings for selected tables or views is supported for the following target endpoints:
+ Oracle
+ Microsoft SQL Server
+ MySQL
+ PostgreSQL
+ SAP ASE, depending on the `mode` and `bulk-max-size` settings, as described following

**Note**  
You can use LOB data types only with tables and views that include a primary key\.

To use LOB settings for a selected table or view, you create a table\-mapping rule of type `table-settings` with the `lob-settings` option\. Doing this specifies LOB handling for the table or view identified by the `object-locator` option\. Within the `table-settings` rule, you can specify a `lob-settings` object with the following parameters:
+ `mode` – Specifies the mechanism for handling LOB migration for the selected table or view as follows: 
  + `limited` – The default limited LOB mode is the fastest and most efficient mode\. Use this mode only if all of your LOBs are small \(within 100 MB in size\) or the target endpoint doesn't support an unlimited LOB size\. Also if you use `limited`, all LOBs need to be within the size that you set for `bulk-max-size`\. 

    In this mode for a full load task, the replication instance migrates all LOBs inline together with other column data types as part of main table or view storage\. However, the instance truncates any migrated LOB larger than your `bulk-max-size` value to the specified size\. For a change data capture \(CDC\) load task, the instance migrates all LOBs using a source table lookup, as in standard full LOB mode \(see the following\)\. It does so regardless of LOB size\.
**Note**  
You can migrate views for full\-load tasks only\.
  + `unlimited` – The migration mechanism for full LOB mode depends on the value you set for `bulk-max-size` as follows:
    + **Standard full LOB mode** – When you set `bulk-max-size` to zero, the replication instance migrates all LOBs using standard full LOB mode\. This mode requires a lookup in the source table or view to migrate every LOB, regardless of size\. This approach typically results in a much slower migration than for limited LOB mode\. Use this mode only if all or most of your LOBs are large \(1 GB or larger\)\.
    + **Combination full LOB mode** – When you set `bulk-max-size` to a nonzero value, this full LOB mode uses a combination of limited LOB mode and standard full LOB mode\. That is for a full load task, if a LOB size is within your `bulk-max-size` value, the instance migrates the LOB inline as in limited LOB mode\. If the LOB size is greater than this value, the instance migrates the LOB using a source table or view lookup as in standard full LOB mode\. For a change data capture \(CDC\) load task, the instance migrates all LOBs using a source table lookup, as in standard full LOB mode \(see the following\)\. It does so regardless of LOB size\.
**Note**  
You can migrate views for full\-load tasks only\.

      This mode results in a migration speed that is a compromise between the faster, limited LOB mode and the slower, standard full LOB mode\. Use this mode only when you have a mix of small and large LOBs, and most of the LOBs are small\.

      This combination full LOB mode is available only for the following endpoints:
      + IBM Db2 as source 
      + SAP ASE as source or target

    Regardless of the mechanism you specify for `unlimited` mode, the instance migrates all LOBs fully, without truncation\.
  + `none` – The replication instance migrates LOBs in the selected table or view using your task LOB settings\. Use this option to help compare migration results with and without LOB settings for the selected table or view\.

  If the specified table or view has LOBs included in the replication, you can set the `BatchApplyEnabled` task setting to `true` only when using `limited` LOB mode\. 

  In some cases, you might set `BatchApplyEnabled` to `true` and `BatchApplyPreserveTransaction` to `false`\. In these cases, the instance sets `BatchApplyPreserveTransaction` to `true` if the table or view has LOBs and the source and target endpoints are Oracle\.
+ `bulk-max-size` – Set this value to a zero or non\-zero value in kilobytes, depending on the `mode` as described for the previous items\. In `limited` mode, you must set a nonzero value for this parameter\.

  The instance converts LOBs to binary format\. Therefore, to specify the largest LOB you need to replicate, multiply its size by three\. For example, if your largest LOB is 2 MB, set `bulk-max-size` to 6,000 \(6 MB\)\.

## Table\-settings examples<a name="CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Tablesettings.Examples"></a>

Following, you can find some examples that demonstrate the use of table settings\.

**Example Load a table segmented by partitions**  
The following example loads a `SALES` table in your source more efficiently by loading it in parallel based on all its partitions\.  

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
            "rule-type": "table-settings",
            "rule-id": "2",
            "rule-name": "2",
            "object-locator": {
                "schema-name": "HR",
                "table-name": "SALES"
            },
            "parallel-load": {
                "type": "partitions-auto"
            }
        }
     ]
}
```

**Example Load a table segmented by subpartitions**  
The following example loads a `SALES` table in your Oracle source more efficiently by loading it in parallel based on all its subpartitions\.  

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
            "rule-type": "table-settings",
            "rule-id": "2",
            "rule-name": "2",
            "object-locator": {
                "schema-name": "HR",
                "table-name": "SALES"
            },
            "parallel-load": {
                "type": "subpartitions-auto"
            }
        }
     ]
}
```

**Example Load a table segmented by a list of partitions**  
The following example loads a `SALES` table in your source by loading it in parallel by a particular list of partitions\. Here, the specified partitions are named after values starting with portions of the alphabet, for example `ABCD`, `EFGH`, and so on\.   

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
            "rule-type": "table-settings",
            "rule-id": "2",
            "rule-name": "2",
            "object-locator": {
                "schema-name": "HR",
                "table-name": "SALES"
            },
            "parallel-load": {
                "type": "partitions-list",
                "partitions": [
                    "ABCD",
                    "EFGH",
                    "IJKL",
                    "MNOP",
                    "QRST",
                    "UVWXYZ"
                ]
            }
        }
    ]
}
```

**Example Load an Oracle table segmented by a selected list of partitions and subpartitions**  
The following example loads a `SALES` table in your Oracle source by loading it in parallel by a selected list of partitions and subpartitions\. Here, the specified partitions are named after values starting with portions of the alphabet, for example `ABCD`, `EFGH`, and so on\. The specified subpartitions are named after values starting with numerals, for example `01234` and `56789`\.  

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
            "rule-type": "table-settings",
            "rule-id": "2",
            "rule-name": "2",
            "object-locator": {
                "schema-name": "HR",
                "table-name": "SALES"
            },
            "parallel-load": {
                "type": "partitions-list",
                "partitions": [
                    "ABCD",
                    "EFGH",
                    "IJKL",
                    "MNOP",
                    "QRST",
                    "UVWXYZ"
                ],
                "subpartitions": [
                    "01234",
                    "56789"
                ]
            }
        }
    ]
}
```

**Example Load a table segmented by ranges of column values**  
The following example loads a `SALES` table in your source by loading it in parallel by segments specified by ranges of the `SALES_NO` and `REGION` column values\.  

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
            "rule-type": "table-settings",
            "rule-id": "2",
            "rule-name": "2",
            "object-locator": {
                "schema-name": "HR",
                "table-name": "SALES"
            },
            "parallel-load": {
                "type": "ranges",
                "columns": [
                    "SALES_NO",
                    "REGION"
                ],
                "boundaries": [
                    [
                        "1000",
                        "NORTH"
                    ],
                    [
                        "3000",
                        "WEST"
                    ]
                ]
            }
        }
    ]
}
```
Here, two columns are specified for the segment ranges with the names, `SALES_NO` and `REGION`\. Two boundaries are specified with two sets of column values \(`["1000","NORTH"]` and `["3000","WEST"]`\)\.  
These two boundaries thus identify the following three table segments to load in parallel:    
Segment 1  
Rows with `SALES_NO` less than or equal to 1,000 and `REGION` less than "NORTH"\. In other words, sales numbers up to 1,000 in the EAST region\.  
Segment 2  
Rows other than **Segment 1** with `SALES_NO` less than or equal to 3,000 and `REGION` less than "WEST"\. In other words, sales numbers over 1,000 up to 3,000 in the NORTH and SOUTH regions\.  
Segment 3  
All remaining rows other than **Segment 1** and **Segment 2**\. In other words, sales numbers over 3,000 in the "WEST" region\.

**Example Load two tables: One segmented by ranges and another segmented by partitions**  
The following example loads a `SALES` table in parallel by segment boundaries that you identify\. It also loads an `ORDERS` table in parallel by all of its partitions, as with previous examples\.  

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
            "rule-type": "table-settings",
            "rule-id": "2",
            "rule-name": "2",
            "object-locator": {
                "schema-name": "HR",
                "table-name": "SALES"
            },
            "parallel-load": {
                "type": "ranges",
                "columns": [
                    "SALES_NO",
                    "REGION"
                ],
                "boundaries": [
                    [
                        "1000",
                        "NORTH"
                    ],
                    [
                        "3000",
                        "WEST"
                    ]
                ]
            }
        },
        {
            "rule-type": "table-settings",
            "rule-id": "3",
            "rule-name": "3",
            "object-locator": {
                "schema-name": "HR",
                "table-name": "ORDERS"
            },
            "parallel-load": {
                "type": "partitions-auto" 
            }
        }
    ]
}
```

**Example Load a table with LOBs using limited LOB mode**  
The following example loads an `ITEMS` table including LOBs in your source using limited LOB mode \(the default\) with a maximum nontruncated size of 100 MB\. Any LOBs that are larger than this size are truncated to 100 MB\. All LOBs are loaded inline with all other column data types\.  

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
            "rule-type": "table-settings",
            "rule-id": "2",
            "rule-name": "2",
            "object-locator": {
                "schema-name": "INV",
                "table-name": "ITEMS"
            },
            "lob-settings": {
                "bulk-max-size": "100000"
            }
        }
     ]
}
```

**Example Load a table with LOBs using standard full LOB mode**  
The following example loads an `ITEMS` table in your source, including all its LOBs without truncation, using standard full LOB mode\. All LOBs, regardless of size, are loaded separately from other data types using a lookup for each LOB in the source table\.  

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
            "rule-type": "table-settings",
            "rule-id": "2",
            "rule-name": "2",
            "object-locator": {
                "schema-name": "INV",
                "table-name": "ITEMS"
            },
            "lob-settings": {
                "mode": "unlimited",
                "bulk-max-size": "0"
            }
        }
     ]
}
```

**Example Load a table with LOBs using combination full LOB mode**  
The following example loads an `ITEMS` table in your source, including all its LOBs without truncation, using combination full LOB mode\. All LOBs within 100 MB in size are loaded inline along with other data types, as in limited LOB mode\. All LOBs over 100 MB in size are loaded separately from other data types\. This separate load uses a lookup for each such LOB in the source table, as in standard full LOB mode\.  

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
            "rule-type": "table-settings",
            "rule-id": "2",
            "rule-name": "2",
            "object-locator": {
                "schema-name": "INV",
                "table-name": "ITEMS"
            },
            "lob-settings": {
                "mode": "unlimited",
                "bulk-max-size": "100000"
            }
        }
     ]
}
```

**Example Load a table with LOBs using the task LOB settings**  
The following example loads an `ITEMS` table in your source, including all LOBs, using its task LOB settings\. The `bulk-max-size` setting of 100 MB is ignored and left only for a quick reset to `limited` or `unlimited` mode\.  

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
            "rule-type": "table-settings",
            "rule-id": "2",
            "rule-name": "2",
            "object-locator": {
                "schema-name": "INV",
                "table-name": "ITEMS"
            },
            "lob-settings": {
                "mode": "none",
                "bulk-max-size": "100000"
            }
        }
     ]
}
```