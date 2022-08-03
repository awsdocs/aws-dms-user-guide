# Using Amazon Kinesis Data Streams as a target for AWS Database Migration Service<a name="CHAP_Target.Kinesis"></a>

You can use AWS DMS to migrate data to an Amazon Kinesis data stream\. Amazon Kinesis data streams are part of the Amazon Kinesis Data Streams service\. You can use Kinesis data streams to collect and process large streams of data records in real time\.

A Kinesis data stream is made up of shards\. *Shards* are uniquely identified sequences of data records in a stream\. For more information on shards in Amazon Kinesis Data Streams, see [Shard](https://docs.aws.amazon.com/streams/latest/dev/key-concepts.html#shard) in the *Amazon Kinesis Data Streams Developer Guide\.*

AWS Database Migration Service publishes records to a Kinesis data stream using JSON\. During conversion, AWS DMS serializes each record from the source database into an attribute\-value pair in JSON format or a JSON\_UNFORMATTED message format\. A JSON\_UNFORMATTED message format is a single line JSON string with new line delimiter\. It allows Amazon Kinesis Data Firehose to deliver Kinesis data to an Amazon S3 destination, and then query it using various query engines including Amazon Athena\.

You use object mapping to migrate your data from any supported data source to a target stream\. With object mapping, you determine how to structure the data records in the stream\. You also define a partition key for each table, which Kinesis Data Streams uses to group the data into its shards\. 

When AWS DMS creates tables on an Kinesis Data Streams target endpoint, it creates as many tables as in the source database endpoint\. AWS DMS also sets several Kinesis Data Streams parameter values\. The cost for the table creation depends on the amount of data and the number of tables to be migrated\.

**Note**  
The **SSL Mode** option on the AWS DMS console or API doesn’t apply to some data streaming and NoSQL services like Kinesis and DynamoDB\. They are secure by default, so AWS DMS shows the SSL mode setting is equal to none \(**SSL Mode=None**\)\. You don’t need to provide any additional configuration for your endpoint to make use of SSL\. For example, when using Kinesis as a target endpoint, it is secure by default\. All API calls to Kinesis use SSL, so there is no need for an additional SSL option in the AWS DMS endpoint\. You can securely put data and retrieve data through SSL endpoints using the HTTPS protocol, which AWS DMS uses by default when connecting to a Kinesis Data Stream\.

**Kinesis Data Streams endpoint settings**

When you use Kinesis Data Streams target endpoints, you can get transaction and control details using the `KinesisSettings` option in the AWS DMS API\. 

You can set connection settings in the following ways:
+ In the AWS DMS console, using endpoint settings\.
+ In the CLI, using the `kinesis-settings` option of the [CreateEndpoint](https://docs.aws.amazon.com/dms/latest/APIReference/API_CreateEndpoint.html) command\.

In the CLI, use the following request parameters of the `kinesis-settings` option:
**Note**  
Support for the `IncludeNullAndEmpty` endpoint setting is available in AWS DMS version 3\.4\.1 and higher\. But support for the other following endpoint settings for Kinesis Data Streams targets is available in AWS DMS\. 
+ `MessageFormat` – The output format for the records created on the endpoint\. The message format is `JSON` \(default\) or `JSON_UNFORMATTED` \(a single line with no tab\)\.
+ `IncludeControlDetails` – Shows detailed control information for table definition, column definition, and table and column changes in the Kinesis message output\. The default is `false`\.
+ `IncludeNullAndEmpty` – Include NULL and empty columns in the target\. The default is `false`\.
+ `IncludePartitionValue` – Shows the partition value within the Kinesis message output, unless the partition type is `schema-table-type`\. The default is `false`\.
+ `IncludeTableAlterOperations` – Includes any data definition language \(DDL\) operations that change the table in the control data, such as `rename-table`, `drop-table`, `add-column`, `drop-column`, and `rename-column`\. The default is `false`\.
+ `IncludeTransactionDetails` – Provides detailed transaction information from the source database\. This information includes a commit timestamp, a log position, and values for `transaction_id`, `previous_transaction_id`, and `transaction_record_id `\(the record offset within a transaction\)\. The default is `false`\.
+ `PartitionIncludeSchemaTable` – Prefixes schema and table names to partition values, when the partition type is `primary-key-type`\. Doing this increases data distribution among Kinesis shards\. For example, suppose that a `SysBench` schema has thousands of tables and each table has only limited range for a primary key\. In this case, the same primary key is sent from thousands of tables to the same shard, which causes throttling\. The default is `false`\.

The following example shows the `kinesis-settings` option in use with an example `create-endpoint` command issued using the AWS CLI\.

```
aws dms create-endpoint --endpoint-identifier=$target_name --engine-name kinesis --endpoint-type target
--region us-east-1 --kinesis-settings ServiceAccessRoleArn=arn:aws:iam::333333333333:role/dms-kinesis-role,
StreamArn=arn:aws:kinesis:us-east-1:333333333333:stream/dms-kinesis-target-doc,MessageFormat=json-unformatted,
IncludeControlDetails=true,IncludeTransactionDetails=true,IncludePartitionValue=true,PartitionIncludeSchemaTable=true,
IncludeTableAlterOperations=true
```

**Multithreaded full load task settings**

To help increase the speed of the transfer, AWS DMS supports a multithreaded full load to a Kinesis Data Streams target instance\. DMS supports this multithreading with task settings that include the following:
+ `MaxFullLoadSubTasks` – Use this option to indicate the maximum number of source tables to load in parallel\. DMS loads each table into its corresponding Kinesis target table using a dedicated subtask\. The default is 8; the maximum value is 49\.
+ `ParallelLoadThreads` – Use this option to specify the number of threads that AWS DMS uses to load each table into its Kinesis target table\. The maximum value for a Kinesis Data Streams target is 32\. You can ask to have this maximum limit increased\.
+ `ParallelLoadBufferSize` – Use this option to specify the maximum number of records to store in the buffer that the parallel load threads use to load data to the Kinesis target\. The default value is 50\. The maximum value is 1,000\. Use this setting with `ParallelLoadThreads`\. `ParallelLoadBufferSize` is valid only when there is more than one thread\.
+ `ParallelLoadQueuesPerThread` – Use this option to specify the number of queues each concurrent thread accesses to take data records out of queues and generate a batch load for the target\. The default is 1\. However, for Kinesis targets of various payload sizes, the valid range is 5–512 queues per thread\.

**Multithreaded CDC load task settings**

You can improve the performance of change data capture \(CDC\) for real\-time data streaming target endpoints like Kinesis using task settings to modify the behavior of the `PutRecords` API call\. To do this, you can specify the number of concurrent threads, queues per thread, and the number of records to store in a buffer using `ParallelApply*` task settings\. For example, suppose you want to perform a CDC load and apply 128 threads in parallel\. You also want to access 64 queues per thread, with 50 records stored per buffer\. 

To promote CDC performance, AWS DMS supports these task settings:
+ `ParallelApplyThreads` – Specifies the number of concurrent threads that AWS DMS uses during a CDC load to push data records to a Kinesis target endpoint\. The default value is zero \(0\) and the maximum value is 32\.
+ `ParallelApplyBufferSize` – Specifies the maximum number of records to store in each buffer queue for concurrent threads to push to a Kinesis target endpoint during a CDC load\. The default value is 100 and the maximum value is 1,000\. Use this option when `ParallelApplyThreads` specifies more than one thread\. 
+ `ParallelApplyQueuesPerThread` – Specifies the number of queues that each thread accesses to take data records out of queues and generate a batch load for a Kinesis endpoint during CDC\.

When using `ParallelApply*` task settings, the `partition-key-type` default is the `primary-key` of the table, not `schema-name.table-name`\.

## Using a before image to view original values of CDC rows for a Kinesis data stream as a target<a name="CHAP_Target.Kinesis.BeforeImage"></a>

When writing CDC updates to a data\-streaming target like Kinesis, you can view a source database row's original values before change by an update\. To make this possible, AWS DMS populates a *before image* of update events based on data supplied by the source database engine\. 

Different source database engines provide different amounts of information for a before image: 
+ Oracle provides updates to columns only if they change\. 
+ PostgreSQL provides only data for columns that are part of the primary key \(changed or not\)\. 
+ MySQL generally provides data for all columns except for BLOB and CLOB data types \(changed or not\)\.

To enable before imaging to add original values from the source database to the AWS DMS output, use either the `BeforeImageSettings` task setting or the `add-before-image-columns` parameter\. This parameter applies a column transformation rule\. 

`BeforeImageSettings` adds a new JSON attribute to every update operation with values collected from the source database system, as shown following\.

```
"BeforeImageSettings": {
    "EnableBeforeImage": boolean,
    "FieldName": string,  
    "ColumnFilter": pk-only (default) / non-lob / all (but only one)
}
```

**Note**  
Only apply `BeforeImageSettings` to AWS DMS tasks that contain a CDC component, such as full load plus CDC tasks \(which migrate existing data and replicate ongoing changes\), or to CDC only tasks \(which replicate data changes only\)\. Don't apply `BeforeImageSettings` to tasks that are full load only\.

For `BeforeImageSettings` options, the following applies:
+ Set the `EnableBeforeImage` option to `true` to enable before imaging\. The default is `false`\. 
+ Use the `FieldName` option to assign a name to the new JSON attribute\. When `EnableBeforeImage` is `true`, `FieldName` is required and can't be empty\.
+ The `ColumnFilter` option specifies a column to add by using before imaging\. To add only columns that are part of the table's primary keys, use the default value, `pk-only`\. To add any column that has a before image value, use `all`\. Note that the before image does not contain columns with LOB data types, such as CLOB or BLOB\.

  ```
  "BeforeImageSettings": {
      "EnableBeforeImage": true,
      "FieldName": "before-image",
      "ColumnFilter": "pk-only"
    }
  ```

**Note**  
Amazon S3 targets don't support `BeforeImageSettings`\. For S3 targets, use only the `add-before-image-columns` transformation rule to perform before imaging during CDC\.

### Using a before image transformation rule<a name="CHAP_Target.Kinesis.BeforeImage.Transform-Rule"></a>

As as an alternative to task settings, you can use the `add-before-image-columns` parameter, which applies a column transformation rule\. With this parameter, you can enable before imaging during CDC on data streaming targets like Kinesis\. 

By using `add-before-image-columns` in a transformation rule, you can apply more fine\-grained control of the before image results\. Transformation rules enable you to use an object locator that gives you control over tables selected for the rule\. Also, you can chain transformation rules together, which allows different rules to be applied to different tables\. You can then manipulate the columns produced by using other rules\. 

**Note**  
Don't use the `add-before-image-columns` parameter together with the `BeforeImageSettings` task setting within the same task\. Instead, use either the parameter or the setting, but not both, for a single task\.

A `transformation` rule type with the `add-before-image-columns` parameter for a column must provide a `before-image-def` section\. The following shows an example\.

```
    {
      "rule-type": "transformation",
      …
      "rule-target": "column",
      "rule-action": "add-before-image-columns",
      "before-image-def":{
        "column-filter": one-of  (pk-only / non-lob / all),
        "column-prefix": string,
        "column-suffix": string,
      }
    }
```

The value of `column-prefix` is prepended to a column name, and the default value of `column-prefix` is `BI_`\. The value of `column-suffix` is appended to the column name, and the default is empty\. Don't set both `column-prefix` and `column-suffix` to empty strings\.

Choose one value for `column-filter`\. To add only columns that are part of table primary keys, choose `pk-only` \. Choose `non-lob` to only add columns that are not of LOB type\. Or choose `all` to add any column that has a before\-image value\.

### Example for a before image transformation rule<a name="CHAP_Target.Kinesis.BeforeImage.Example"></a>

The transformation rule in the following example adds a new column called `BI_emp_no` in the target\. So a statement like `UPDATE employees SET emp_no = 3 WHERE emp_no = 1;` populates the `BI_emp_no` field with 1\. When you write CDC updates to Amazon S3 targets, the `BI_emp_no` column makes it possible to tell which original row was updated\.

```
{
  "rules": [
    {
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

For information on using the `add-before-image-columns` rule action, see [ Transformation rules and actions](CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Transformations.md)\.

## Prerequisites for using a Kinesis data stream as a target for AWS Database Migration Service<a name="CHAP_Target.Kinesis.Prerequisites"></a>

Before you set up a Kinesis data stream as a target for AWS DMS, make sure that you create an IAM role\. This role must allow AWS DMS to assume and grant access to the Kinesis data streams that are being migrated into\. The minimum set of access permissions is shown in the following IAM policy\.

```
{
   "Version": "2012-10-17",
   "Statement": [
   {
     "Sid": "1",
     "Effect": "Allow",
     "Principal": {
        "Service": "dms.amazonaws.com"
     },
   "Action": "sts:AssumeRole"
   }
]
}
```

The role that you use for the migration to a Kinesis data stream must have the following permissions\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "kinesis:DescribeStream",
                "kinesis:PutRecord",
                "kinesis:PutRecords"
            ],
            "Resource": "arn:aws:kinesis:region:accountID:stream/streamName"
        }
    ]
}
```

## Limitations when using Kinesis Data Streams as a target for AWS Database Migration Service<a name="CHAP_Target.Kinesis.Limitations"></a>

The following limitations apply when using Kinesis Data Streams as a target:
+ AWS DMS publishes each update to a single record in the source database as one data record in a given Kinesis data stream regardless of transactions\. However, you can include transaction details for each data record by using relevant parameters of the `KinesisSettings` API\.
+ Full LOB mode is not supported\.
+ Kinesis Data Streams don't support deduplication\. Applications that consume data from a stream need to handle duplicate records\. For more information, see [Handling duplicate records](https://docs.aws.amazon.com/streams/latest/dev/kinesis-record-processor-duplicates.html) in the *Amazon Kinesis Data Streams Developer Guide\.*
+ AWS DMS supports the following two forms for partition keys:
  + `SchemaName.TableName`: A combination of the schema and table name\.
  + `${AttributeName}`: The value of one of the fields in the JSON, or the primary key of the table in the source database\.
+ For information about encrypting your data at rest within Kinesis Data Streams, see [Data protection in Kinesis Data Streams](https://docs.aws.amazon.com/streams/latest/dev/server-side-encryption.html.html) in the *AWS Key Management Service Developer Guide*\. 
+ `BatchApply` is not supported for a Kinesis endpoint\. Using Batch Apply \(for example, the `BatchApplyEnabled` target metadata task setting\) for a Kinesis target might result in loss of data\.
+ Kinesis targets are only supported for a Kinesis data stream in the same AWS account\.
+ When migrating from a MySQL source, the BeforeImage data doesn't include CLOB and BLOB data types\. For more information, see [Using a before image to view original values of CDC rows for a Kinesis data stream as a target](#CHAP_Target.Kinesis.BeforeImage)\.
+ AWS DMS migrates values of `BigInt` data type with more than 16 digits in the scientific notation form\.

## Using object mapping to migrate data to a Kinesis data stream<a name="CHAP_Target.Kinesis.ObjectMapping"></a>

AWS DMS uses table\-mapping rules to map data from the source to the target Kinesis data stream\. To map data to a target stream, you use a type of table\-mapping rule called object mapping\. You use object mapping to define how data records in the source map to the data records published to the Kinesis data stream\. 

Kinesis data streams don't have a preset structure other than having a partition key\. In an object mapping rule, the possible values of a `partition-key-type` for data records are `schema-table`, `transaction-id`, `primary-key`, `constant`, and `attribute-name`\.

To create an object\-mapping rule, you specify `rule-type` as `object-mapping`\. This rule specifies what type of object mapping you want to use\. 

The structure for the rule is as follows\.

```
{
    "rules": [
        {
            "rule-type": "object-mapping",
            "rule-id": "id",
            "rule-name": "name",
            "rule-action": "valid object-mapping rule action",
            "object-locator": {
                "schema-name": "case-sensitive schema name",
                "table-name": ""
            }
        }
    ]
}
```

AWS DMS currently supports `map-record-to-record` and `map-record-to-document` as the only valid values for the `rule-action` parameter\. The `map-record-to-record` and `map-record-to-document` values specify what AWS DMS does by default to records that aren't excluded as part of the `exclude-columns` attribute list\. These values don't affect the attribute mappings in any way\. 

Use `map-record-to-record` when migrating from a relational database to a Kinesis data stream\. This rule type uses the `taskResourceId.schemaName.tableName` value from the relational database as the partition key in the Kinesis data stream and creates an attribute for each column in the source database\. When using `map-record-to-record`, for any column in the source table not listed in the `exclude-columns` attribute list, AWS DMS creates a corresponding attribute in the target stream\. This corresponding attribute is created regardless of whether that source column is used in an attribute mapping\. 

Use `map-record-to-document` to put source columns into a single, flat document in the appropriate target stream using the attribute name "\_doc"\. AWS DMS places the data into a single, flat map on the source called "`_doc`"\. This placement applies to any column in the source table not listed in the `exclude-columns` attribute list\.

One way to understand `map-record-to-record` is to see it in action\. For this example, assume that you are starting with a relational database table row with the following structure and data\.


| FirstName | LastName | StoreId | HomeAddress | HomePhone | WorkAddress | WorkPhone | DateofBirth | 
| --- | --- | --- | --- | --- | --- | --- | --- | 
| Randy | Marsh | 5 | 221B Baker Street | 1234567890 | 31 Spooner Street, Quahog  | 9876543210 | 02/29/1988 | 

To migrate this information from a schema named `Test` to a Kinesis data stream, you create rules to map the data to the target stream\. The following rule illustrates the mapping\. 

```
{
    "rules": [
        {
            "rule-type": "selection",
            "rule-id": "1",
            "rule-name": "1",
            "rule-action": "include",
            "object-locator": {
                "schema-name": "Test",
                "table-name": "%"
            }
        },
        {
            "rule-type": "object-mapping",
            "rule-id": "2",
            "rule-name": "DefaultMapToKinesis",
            "rule-action": "map-record-to-record",
            "object-locator": {
                "schema-name": "Test",
                "table-name": "Customers"
            }
        }
    ]
}
```

The following illustrates the resulting record format in the Kinesis data stream: 
+ StreamName: XXX
+ PartitionKey: Test\.Customers //schmaName\.tableName
+ Data: //The following JSON message

  ```
    {
       "FirstName": "Randy",
       "LastName": "Marsh",
       "StoreId":  "5",
       "HomeAddress": "221B Baker Street",
       "HomePhone": "1234567890",
       "WorkAddress": "31 Spooner Street, Quahog",
       "WorkPhone": "9876543210",
       "DateOfBirth": "02/29/1988"
    }
  ```

However, suppose that you use the same rules but change the `rule-action` parameter to `map-record-to-document` and exclude certain columns\. The following rule illustrates the mapping\.

```
{
	"rules": [
	   {
			"rule-type": "selection",
			"rule-id": "1",
			"rule-name": "1",
			"rule-action": "include",
			"object-locator": {
				"schema-name": "Test",
				"table-name": "%"
			}
		},
		{
			"rule-type": "object-mapping",
			"rule-id": "2",
			"rule-name": "DefaultMapToKinesis",
			"rule-action": "map-record-to-document",
			"object-locator": {
				"schema-name": "Test",
				"table-name": "Customers"
			},
			"mapping-parameters": {
				"exclude-columns": [
					"homeaddress",
					"homephone",
					"workaddress",
					"workphone"
				]
			}
		}
	]
}
```

In this case, the columns not listed in the `exclude-columns` parameter, `FirstName`, `LastName`, `StoreId` and `DateOfBirth`, are mapped to `_doc`\. The following illustrates the resulting record format\. 

```
       {
            "data":{
                "_doc":{
                    "FirstName": "Randy",
                    "LastName": "Marsh",
                    "StoreId":  "5",
                    "DateOfBirth": "02/29/1988"
                }
            }
        }
```

### Restructuring data with attribute mapping<a name="CHAP_Target.Kinesis.AttributeMapping"></a>

You can restructure the data while you are migrating it to a Kinesis data stream using an attribute map\. For example, you might want to combine several fields in the source into a single field in the target\. The following attribute map illustrates how to restructure the data\.

```
{
    "rules": [
        {
            "rule-type": "selection",
            "rule-id": "1",
            "rule-name": "1",
            "rule-action": "include",
            "object-locator": {
                "schema-name": "Test",
                "table-name": "%"
            }
        },
        {
            "rule-type": "object-mapping",
            "rule-id": "2",
            "rule-name": "TransformToKinesis",
            "rule-action": "map-record-to-record",
            "target-table-name": "CustomerData",
            "object-locator": {
                "schema-name": "Test",
                "table-name": "Customers"
            },
            "mapping-parameters": {
                "partition-key-type": "attribute-name",
                "partition-key-name": "CustomerName",
                "exclude-columns": [
                    "firstname",
                    "lastname",
                    "homeaddress",
                    "homephone",
                    "workaddress",
                    "workphone"
                ],
                "attribute-mappings": [
                    {
                        "target-attribute-name": "CustomerName",
                        "attribute-type": "scalar",
                        "attribute-sub-type": "string",
                        "value": "${lastname}, ${firstname}"
                    },
                    {
                        "target-attribute-name": "ContactDetails",
                        "attribute-type": "document",
                        "attribute-sub-type": "json",
                        "value": {
                            "Home": {
                                "Address": "${homeaddress}",
                                "Phone": "${homephone}"
                            },
                            "Work": {
                                "Address": "${workaddress}",
                                "Phone": "${workphone}"
                            }
                        }
                    }
                ]
            }
        }
    ]
}
```

To set a constant value for `partition-key`, specify a `partition-key` value\. For example, you might do this to force all the data to be stored in a single shard\. The following mapping illustrates this approach\. 

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
            "rule-type": "object-mapping",
            "rule-id": "1",
            "rule-name": "TransformToKinesis",
            "rule-action": "map-record-to-document",
            "object-locator": {
                "schema-name": "Test",
                "table-name": "Customer"
            },
            "mapping-parameters": {
                "partition-key": {
                    "value": "ConstantPartitionKey"
                },
                "exclude-columns": [
                    "FirstName",
                    "LastName",
                    "HomeAddress",
                    "HomePhone",
                    "WorkAddress",
                    "WorkPhone"
                ],
                "attribute-mappings": [
                    {
                        "attribute-name": "CustomerName",
                        "value": "${FirstName},${LastName}"
                    },
                    {
                        "attribute-name": "ContactDetails",
                        "value": {
                            "Home": {
                                "Address": "${HomeAddress}",
                                "Phone": "${HomePhone}"
                            },
                            "Work": {
                                "Address": "${WorkAddress}",
                                "Phone": "${WorkPhone}"
                            }
                        }
                    },
                    {
                        "attribute-name": "DateOfBirth",
                        "value": "${DateOfBirth}"
                    }
                ]
            }
        }
    ]
}
```

**Note**  
The `partition-key` value for a control record that is for a specific table is `TaskId.SchemaName.TableName`\. The `partition-key` value for a control record that is for a specific task is that record's `TaskId`\. Specifying a `partition-key` value in the object mapping has no impact on the `partition-key` for a control record\.

### Message format for Kinesis Data Streams<a name="CHAP_Target.Kinesis.Messageformat"></a>

The JSON output is simply a list of key\-value pairs\. A JSON\_UNFORMATTED message format is a single line JSON string with new line delimiter\.

AWS DMS provides the following reserved fields to make it easier to consume the data from the Kinesis Data Streams: 

**RecordType**  
The record type can be either data or control\. *Data records *represent the actual rows in the source\. *Control records* are for important events in the stream, for example a restart of the task\.

**Operation**  
For data records, the operation can be `load`, `insert`, `update`, or `delete`\.  
For control records, the operation can be `create-table`, `rename-table`, `drop-table`, `change-columns`, `add-column`, `drop-column`, `rename-column`, or `column-type-change`\.

**SchemaName**  
The source schema for the record\. This field can be empty for a control record\.

**TableName**  
The source table for the record\. This field can be empty for a control record\.

**Timestamp**  
The timestamp for when the JSON message was constructed\. The field is formatted with the ISO 8601 format\.