# Using Amazon DocumentDB \(with MongoDB compatibility\) as a source for AWS DMS<a name="CHAP_Source.DocumentDB"></a>

AWS DMS supports Amazon DocumentDB \(with MongoDB compatibility\) versions 3\.6 and 4\.0 as a database source\. Using Amazon DocumentDB as a source, you can migrate data from one Amazon DocumentDB cluster to another Amazon DocumentDB cluster\. You can also migrate data from an Amazon DocumentDB cluster to one of the other target endpoints supported by AWS DMS\.

If you are new to Amazon DocumentDB, be aware of the following important concepts for Amazon DocumentDB databases:
+ A record in Amazon DocumentDB is a *document*, a data structure composed of field and value pairs\. The value of a field can include other documents, arrays, and arrays of documents\. A document is roughly equivalent to a row in a relational database table\.
+ A *collection* in Amazon DocumentDB is a group of documents, and is roughly equivalent to a relational database table\.
+ A *database* in Amazon DocumentDB is a set of collections, and is roughly equivalent to a schema in a relational database\.

AWS DMS supports two migration modes when using Amazon DocumentDB as a source, document mode and table mode\. You specify the migration mode when you create the Amazon DocumentDB source endpoint in the AWS DMS console, using either the **Metadata mode** option or the extra connection attribute `nestingLevel`\. Following, you can find an explanation how the choice of migration mode affects the resulting format of the target data\.

**Document mode**  
In *document mode, *the JSON document is migrated as is\. That means the document data is consolidated into one of two items\. When you use a relational database as a target, the data is a single column named `_doc` in a target table\. When you use a nonrelational database as a target, the data is a single JSON document\. Document mode is the default mode, which we recommend when migrating to an Amazon DocumentDB target\.  
For example, consider the following documents in an Amazon DocumentDB collection called `myCollection`\.  

```
> db.myCollection.find()
{ "_id" : ObjectId("5a94815f40bd44d1b02bdfe0"), "a" : 1, "b" : 2, "c" : 3 }
{ "_id" : ObjectId("5a94815f40bd44d1b02bdfe1"), "a" : 4, "b" : 5, "c" : 6 }
```
After migrating the data to a relational database table using document mode, the data is structured as follows\. The data fields in the document are consolidated into the` _doc` column\.      
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Source.DocumentDB.html)
You can optionally set the extra connection attribute `extractDocID` to `true` to create a second column named `"_id"` that acts as the primary key\. If you are going to use change data capture \(CDC\), set this parameter to `true` except when using Amazon DocumentDB as the target\.  
If you add a new collection to the source database, AWS DMS creates a new target table for the collection and replicates any documents\. 

**Table mode**  
In *table mode, *AWS DMS transforms each top\-level field in an Amazon DocumentDB document into a column in the target table\. If a field is nested, AWS DMS flattens the nested values into a single column\. AWS DMS then adds a key field and data types to the target table's column set\.   
For each Amazon DocumentDB document, AWS DMS adds each key and type to the target table's column set\. For example, using table mode, AWS DMS migrates the previous example into the following table\.      
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Source.DocumentDB.html)
Nested values are flattened into a column containing dot\-separated key names\. The column is named using the concatenation of the flattened field names separated by periods\. For example, AWS DMS migrates a JSON document with a field of nested values such as `{"a" : {"b" : {"c": 1}}}` into a column named `a.b.c.`  
To create the target columns, AWS DMS scans a specified number of Amazon DocumentDB documents and creates a set of all the fields and their types\. AWS DMS then uses this set to create the columns of the target table\. If you create or modify your Amazon DocumentDB source endpoint using the console, you can specify the number of documents to scan\. The default value is 1,000 documents\. If you use the AWS CLI, you can use the extra connection attribute `docsToInvestigate`\.  
In table mode, AWS DMS manages documents and collections like this:  
+ When you add a document to an existing collection, the document is replicated\. If there are fields that don't exist in the target, those fields aren't replicated\.
+ When you update a document, the updated document is replicated\. If there are fields that don't exist in the target, those fields aren't replicated\.
+ Deleting a document is fully supported\.
+ Adding a new collection doesn't result in a new table on the target when done during a CDC task\.
+ Renaming a collection isn't supported\. 

**Topics**
+ [Setting permissions to use Amazon DocumentDB as a source](#CHAP_Source.DocumentDB.Permissions)
+ [Configuring CDC for an Amazon DocumentDB cluster](#CHAP_Source.DocumentDB.ConfigureCDC)
+ [Connecting to Amazon DocumentDB using TLS](#CHAP_Source.DocumentDB.TLS)
+ [Creating an Amazon DocumentDB source endpoint](#CHAP_Source.DocumentDB.ConfigureEndpoint)
+ [Segmenting Amazon DocumentDB collections and migrating in parallel](#CHAP_Source.DocumentDB.ParallelLoad)
+ [Migrating multiple databases when using Amazon DocumentDB as a source for AWS DMS](#CHAP_Source.DocumentDB.Multidatabase)
+ [Limitations when using Amazon DocumentDB as a source for AWS DMS](#CHAP_Source.DocumentDB.Limitations)
+ [Using extra connections attributes with Amazon DocumentDB as a source](#CHAP_Source.DocumentDB.ECAs)
+ [Source data types for Amazon DocumentDB](#CHAP_Source.DocumentDB.DataTypes)

## Setting permissions to use Amazon DocumentDB as a source<a name="CHAP_Source.DocumentDB.Permissions"></a>

When using Amazon DocumentDB source for an AWS DMS migration, you can create a user account with root privileges\. Or you can create a user with permissions only for the database to be migrated\. 

The following code creates a user as the root account\.

```
use admin
db.createUser(
  {
    user: "root",
    pwd: "password",
    roles: [ { role: "root", db: "admin" } ]
  })
```

The following code creates a user with minimal privileges on the database to be migrated\.

```
use database_to_migrate
db.createUser( 
{ 
    user: "dms-user",
    pwd: "password",
    roles: [ { role: "read", db: "db_name" }, "read"] 
})
```

## Configuring CDC for an Amazon DocumentDB cluster<a name="CHAP_Source.DocumentDB.ConfigureCDC"></a>

To use ongoing replication or CDC with Amazon DocumentDB, AWS DMS requires access to the Amazon DocumentDB cluster's change streams\. For a description of the time\-ordered sequence of update events in your cluster's collections and databases, see [Using change streams](https://docs.aws.amazon.com/documentdb/latest/developerguide/change_streams.html) in the *Amazon DocumentDB Developer Guide*\. 

Authenticate to your Amazon DocumentDB cluster using the MongoDB shell\. Then run the following command to enable change streams\.

```
db.adminCommand({modifyChangeStreams: 1,
    database: "DB_NAME",
    collection: "", 
    enable: true});
```

This approach enables the change stream for all collections in your database\. After change streams are enabled, you can create a migration task that migrates existing data and at the same time replicates ongoing changes\. AWS DMS continues to capture and apply changes even after the bulk data is loaded\. Eventually, the source and target databases synchronize, minimizing downtime for a migration\.

## Connecting to Amazon DocumentDB using TLS<a name="CHAP_Source.DocumentDB.TLS"></a>

By default, a newly created Amazon DocumentDB cluster accepts secure connections only using Transport Layer Security \(TLS\)\. When TLS is enabled, every connection to Amazon DocumentDB requires a public key\.

You can retrieve the public key for Amazon DocumentDB by downloading the file `rds-combined-ca-bundle.pem` from an AWS\-hosted Amazon S3 bucket\. For more information on downloading this file, see [Encrypting connections using TLS](https://docs.aws.amazon.com/documentdb/latest/developerguide/security.encryption.ssl.html) in the *Amazon DocumentDB Developer Guide\.* 

After you download the `rds-combined-ca-bundle.pem` file, you can import the public key that it contains into AWS DMS\. The following steps describe how to do so\.

**To import your public key using the AWS DMS console**

1. Sign in to the AWS Management Console and choose AWS DMS\.

1. In the navigation pane, choose **Certificates**\.

1. Choose **Import certificate**\. The **Import new CA certificate** page appears\.

1. In the **Certificate configuration** section, do one of the following:
   + For **Certificate identifier**, enter a unique name for the certificate, such as `docdb-cert`\.
   + Choose **Choose file**, navigate to the location where you saved the `rds-combined-ca-bundle.pem` file, and select it\.

1. Choose **Add new CA certificate**\.

The AWS CLI following example uses the AWS DMS `import-certificate` command to import the public key `rds-combined-ca-bundle.pem` file\.

```
aws dms import-certificate \
    --certificate-identifier docdb-cert \
    --certificate-pem file://./rds-combined-ca-bundle.pem
```

## Creating an Amazon DocumentDB source endpoint<a name="CHAP_Source.DocumentDB.ConfigureEndpoint"></a>

You can create an Amazon DocumentDB source endpoint using either the console or AWS CLI\. Use the procedure following with the console\.

**To configure an Amazon DocumentDB source endpoint using the AWS DMS console**

1. Sign in to the AWS Management Console and choose AWS DMS\.

1. Choose **Endpoints** from the navigation pane, then choose **Create Endpoint**\.

1. For **Endpoint identifier**, provide a name that helps you easily identify it, such as `docdb-source`\.

1. For **Source engine**, choose **Amazon DocumentDB \(with MongoDB compatibility\)**\.

1. For **Server name**, enter the name of the server where your Amazon DocumentDB database endpoint resides\. For example, you might enter the public DNS name of your Amazon EC2 instance, such as `democluster.cluster-cjf6q8nxfefi.us-east-2.docdb.amazonaws.com`\.

1. For **Port**, enter 27017\.

1. For **SSL mode**, choose **verify\-full**\. If you have disabled SSL on your Amazon DocumentDB cluster, you can skip this step\.

1. For **CA certificate**, choose the Amazon DocumentDB certificate, `rds-combined-ca-bundle.pem`\. For instructions on adding this certificate, see [Connecting to Amazon DocumentDB using TLS](#CHAP_Source.DocumentDB.TLS)\.

1. For **Database name**, enter the name of the database to be migrated\.

Use the following procedure with the CLI\.

**To configure an Amazon DocumentDB source endpoint using the AWS CLI**
+ Run the following AWS DMS `create-endpoint` command to configure an Amazon DocumentDB source endpoint, replacing placeholders with your own values\.

  ```
  aws dms create-endpoint \
             --endpoint-identifier a_memorable_name \
             --endpoint-type source \
             --engine-name docdb \
             --username value \
             --password value \
             --server-name servername_where_database_endpoint_resides \
             --port 27017 \
             --database-name name_of_endpoint_database
  ```

## Segmenting Amazon DocumentDB collections and migrating in parallel<a name="CHAP_Source.DocumentDB.ParallelLoad"></a>

To improve performance of a migration task, Amazon DocumentDB source endpoints support two options of the parallel full load feature in table mapping\. In other words, you can migrate a collection in parallel by using either the autosegmentation or the range segmentation options of table mapping for a parallel full load in JSON settings\. The auto\-segmenting options allow you to specify the criteria for AWS DMS to automatically segment your source for migration in each thread\. The range segmentation options allow you to tell AWS DMS the specific range of each segment for DMS to migrate in each thread\. For more information on these settings, see [Table and collection settings rules and operations](CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Tablesettings.md)\.

### Migrating an Amazon DocumentDB database in parallel using autosegmentation ranges<a name="CHAP_Source.DocumentDB.ParallelLoad.AutoPartitioned"></a>

You can migrate your documents in parallel by specifying the criteria for AWS DMS to automatically parition \(segment\) your data for each thread, especially the number of documents to migrate per thread\. Using this approach, AWS DMS attempts to optimize segment boundaries for maximum performance per thread\.

You can specify the segmentation criteria using the table\-settings options following in table\-mapping:


|  Table\-settings option  |  Description  | 
| --- | --- | 
|  `"type"`  |  \(Required\) Set to `"partitions-auto"` for Amazon DocumentDB as a source\.  | 
|  `"number-of-partitions"`  |  \(Optional\) Total number of partitions \(segments\) used for migration\. The default is 16\.  | 
|  `"collection-count-from-metadata"`  |  \(Optional\) If set to `true`, AWS DMS uses an estimated collection count for determining the number of partitions\. If set to `false`, AWS DMS uses the actual collection count\. The default is `true`\.  | 
|  `"max-records-skip-per-page"`  |  \(Optional\) The number of records to skip at once when determining the boundaries for each partition\. AWS DMS uses a paginated skip approach to determine the minimum boundary for a partition\. The default is 10000\. Setting a relatively large value might result in curser timeouts and task failures\. Setting a relatively low value results in more operations per page and a slower full load\.   | 
|  `"batch-size"`  |  \(Optional\) Limits the number of documents returned in one batch\. Each batch requires a round trip to the server\. If the batch size is zero \(0\), the cursor uses the server\-defined maximum batch size\. The default is 0\.  | 

The example following shows a table mapping for autosegmentation\.

```
{
    "rules": [
        {
            "rule-type": "selection",
            "rule-id": "1",
            "rule-name": "1",
            "object-locator": {
                "schema-name": "admin",
                "table-name": "departments"
            },
            "rule-action": "include",
            "filters": []
        },
        {
            "rule-type": "table-settings",
            "rule-id": "2",
            "rule-name": "2",
            "object-locator": {
                "schema-name": "admin",
                "table-name": "departments"
            },
            "parallel-load": {
                "type": "partitions-auto",
                "number-of-partitions": 5,
                "collection-count-from-metadata": "true",
                "max-records-skip-per-page": 1000000,
                "batch-size": 50000
            }
        }
    ]
}
```

Auto\-segmentation has the limitation following\. The migration for each segment fetches the collection count and the minimum `_id` for the collection separately\. It then uses a paginated skip to calculate the minimum boundary for that segment\. Therefore, ensure that the minimum `_id` value for each collection remains constant until all the segment boundaries in the collection are calculated\. If you change the minimum `_id` value for a collection during its segment boundary calculation, this might cause data loss or duplicate row errors\.

### Migrating an Amazon DocumentDB database in parallel using specific segment ranges<a name="CHAP_Source.DocumentDB.ParallelLoad.Ranges"></a>

The following example shows an Amazon DocumentDB collection that has seven items, and `_id` as the primary key\.

![\[Amazon DocumentDB collection with seven items.\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-docdb-collection.png)

To split the collection into three segments and migrate in parallel, you can add table mapping rules to your migration task as shown in the following JSON example\.

```
{ // Task table mappings:
  "rules": [
    {
      "rule-type": "selection",
      "rule-id": "1",
      "rule-name": "1",
      "object-locator": {
        "schema-name": "testdatabase",
        "table-name": "testtable"
      },
      "rule-action": "include"
    }, // "selection" :"rule-type"
    {
      "rule-type": "table-settings",
      "rule-id": "2",
      "rule-name": "2",
      "object-locator": {
        "schema-name": "testdatabase",
        "table-name": "testtable"
      },
      "parallel-load": {
        "type": "ranges",
        "columns": [
           "_id",
           "num"
        ],
        "boundaries": [
          // First segment selects documents with _id less-than-or-equal-to 5f805c97873173399a278d79
          // and num less-than-or-equal-to 2.
          [
             "5f805c97873173399a278d79",
             "2"
          ],
          // Second segment selects documents with _id > 5f805c97873173399a278d79 and
          // _id less-than-or-equal-to 5f805cc5873173399a278d7c and
          // num > 2 and num less-than-or-equal-to 5.
          [
             "5f805cc5873173399a278d7c",
             "5"
          ]                                   
          // Third segment is implied and selects documents with _id > 5f805cc5873173399a278d7c.
        ] // :"boundaries"
      } // :"parallel-load"
    } // "table-settings" :"rule-type"
  ] // :"rules"
} // :Task table mappings
```

That table mapping definition splits the source collection into three segments and migrates in parallel\. The following are the segmentation boundaries\.

```
Data with _id less-than-or-equal-to "5f805c97873173399a278d79" and num less-than-or-equal-to 2 (2 records)
Data with _id less-than-or-equal-to "5f805cc5873173399a278d7c" and num less-than-or-equal-to 5 and not in (_id less-than-or-equal-to  "5f805c97873173399a278d79" and num less-than-or-equal-to 2) (3 records)
Data not in (_id less-than-or-equal-to "5f805cc5873173399a278d7c" and num less-than-or-equal-to 5) (2 records)
```

After the migration task is complete, you can verify from the task logs that the tables loaded in parallel, as shown in the following example\. You can also verify the Amazon DocumentDB `find` clause used to unload each segment from the source table\.

```
[TASK_MANAGER    ] I:  Start loading segment #1 of 3 of table 'testdatabase'.'testtable' (Id = 1) by subtask 1. Start load timestamp 0005B191D638FE86  (replicationtask_util.c:752)

[SOURCE_UNLOAD   ] I:  Range Segmentation filter for Segment #0 is initialized.   (mongodb_unload.c:157)

[SOURCE_UNLOAD   ] I:  Range Segmentation filter for Segment #0 is: { "_id" : { "$lte" : { "$oid" : "5f805c97873173399a278d79" } }, "num" : { "$lte" : { "$numberInt" : "2" } } }  (mongodb_unload.c:328)

[SOURCE_UNLOAD   ] I: Unload finished for segment #1 of segmented table 'testdatabase'.'testtable' (Id = 1). 2 rows sent.

[TASK_MANAGER    ] I: Start loading segment #1 of 3 of table 'testdatabase'.'testtable' (Id = 1) by subtask 1. Start load timestamp 0005B191D638FE86 (replicationtask_util.c:752)
 
[SOURCE_UNLOAD   ] I: Range Segmentation filter for Segment #0 is initialized. (mongodb_unload.c:157) 

[SOURCE_UNLOAD   ] I: Range Segmentation filter for Segment #0 is: { "_id" : { "$lte" : { "$oid" : "5f805c97873173399a278d79" } }, "num" : { "$lte" : { "$numberInt" : "2" } } } (mongodb_unload.c:328)
 
[SOURCE_UNLOAD   ] I: Unload finished for segment #1 of segmented table 'testdatabase'.'testtable' (Id = 1). 2 rows sent.

[TARGET_LOAD     ] I: Load finished for segment #1 of segmented table 'testdatabase'.'testtable' (Id = 1). 1 rows received. 0 rows skipped. Volume transfered 480.

[TASK_MANAGER    ] I: Load finished for segment #1 of table 'testdatabase'.'testtable' (Id = 1) by subtask 1. 2 records transferred.
```

Currently, AWS DMS supports the following Amazon DocumentDB data types as a segment key column:
+ Double
+ String
+ ObjectId
+ 32 bit integer
+ 64 bit integer

## Migrating multiple databases when using Amazon DocumentDB as a source for AWS DMS<a name="CHAP_Source.DocumentDB.Multidatabase"></a>

AWS DMS versions 3\.4\.5 and later support migrating multiple databases in a single task only for Amazon DocumentDB versions 4\.0 and above\. If you want to migrate multiple databases, do the following:

1. When you create the Amazon DocumentDB source endpoint:
   + In the AWS Management Console for AWS DMS, leave **Database name** empty under **Endpoint configuration** on the **Create endpoint** page\.
   + In the AWS Command Line Interface \(AWS CLI\), assign an empty string value to the **DatabaseName** parameter in **DocumentDBSettings** that you specify for the **CreateEndpoint** action\.

1. For each database that you want to migrate from this Amazon DocumentDB source endpoint, specify the name of each database as the name of a schema in the table\-mapping for the task using either the guided input in the console or directly in JSON\. For more information on the guided input, see the description of the [Specifying table selection and transformations rules from the console](CHAP_Tasks.CustomizingTasks.TableMapping.Console.md)\. For more information on the JSON, see [Selection rules and actions](CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Selections.md)\.

For example, you might specify the JSON following to migrate three Amazon DocumentDB databases\.

**Example Migrate all tables in a schema**  
The JSON following migrates all tables from the `Customers`, `Orders`, and `Suppliers` databases in your source enpoint to your target endpoint\.  

```
{
    "rules": [
        {
            "rule-type": "selection",
            "rule-id": "1",
            "rule-name": "1",
            "object-locator": {
                "schema-name": "Customers",
                "table-name": "%"
            },
            "object-locator": {
                "schema-name": "Orders",
                "table-name": "%"
            },
            "object-locator": {
                "schema-name": "Inventory",
                "table-name": "%"
            },
            "rule-action": "include"
        }
    ]
}
```

## Limitations when using Amazon DocumentDB as a source for AWS DMS<a name="CHAP_Source.DocumentDB.Limitations"></a>

The following are limitations when using Amazon DocumentDB as a source for AWS DMS:
+ When the `_id` option is set as a separate column, the ID string can't exceed 200 characters\.
+ Object ID and array type keys are converted to columns that are prefixed with `oid` and `array` in table mode\.

  Internally, these columns are referenced with the prefixed names\. If you use transformation rules in AWS DMS that reference these columns, make sure to specify the prefixed column\. For example, specify `${oid__id}` and not `${_id}`, or `${array__addresses}` and not `${_addresses}`\. 
+  Collection names and key names can't include the dollar symbol \($\)\. 
+ Table mode and document mode have the limitations discussed preceding\.
+ Migrating in parallel using autosegmentation has the limitations described preceding\.
+ An Amazon DocumentDB \(MongoDB compatible\) source doesn’t support using a specific timestamp as a start position for change data capture \(CDC\)\. An ongoing replication task starts capturing changes regardless of the timestamp\.

## Using extra connections attributes with Amazon DocumentDB as a source<a name="CHAP_Source.DocumentDB.ECAs"></a>

When you set up your Amazon DocumentDB source endpoint, you can specify extra connection attributes\. Extra connection attributes are specified by key\-value pairs\. If you have multiple connection attribute settings, separate them from each other by semicolons with no additional white space \(for example, `oneSetting;thenAnother`\)\.

The following table describes the extra connection attributes available when using Amazon DocumentDB databases as an AWS DMS source\.


| Attribute name | Valid values | Default value and description | 
| --- | --- | --- | 
|   `nestingLevel`   |  `"none"` `"one"`  |  `"none"` – Specify `"none"` to use document mode\. Specify `"one"` to use table mode\.  | 
|   `extractDocID`   |  `"true"` `"false"`  |  `"false"` – Use this attribute when `nestingLevel` is set to `"none"`\.  If your target database is Amazon DocumentDB, set `extractDocID=true`\.   | 
|   `docsToInvestigate`   |  A positive integer greater than `0`\.  |  `1000` – Use this attribute when `nestingLevel` is set to `"one"`\.   | 

**Note**  
If the target endpoint is Amazon DocumentDB, run the migration in** Document mode**, and set the extra connection attribute `extractDocID=true`\. To set the extra connection attribute `extractDocID=true`, modify your source endpoint and check the box **\_id as separate column**\.

## Source data types for Amazon DocumentDB<a name="CHAP_Source.DocumentDB.DataTypes"></a>

In the following table, you can find the Amazon DocumentDB source data types that are supported when using AWS DMS\. You can also find the default mapping from AWS DMS data types in this table\. For more information about data types, see [BSON types](https://docs.mongodb.com/manual/reference/bson-types) in the MongoDB documentation\.

For information on how to view the data type that is mapped in the target, see the section for the target endpoint that you are using\.

For additional information about AWS DMS data types, see [Data types for AWS Database Migration Service](CHAP_Reference.DataTypes.md)\.


|  Amazon DocumentDB data types  |  AWS DMS data types  | 
| --- | --- | 
| Boolean | Bool | 
| Binary | BLOB | 
| Date | Date | 
| Timestamp | Date | 
| Int | INT4 | 
| Long | INT8 | 
| Double | REAL8 | 
| String \(UTF\-8\) | CLOB | 
| Array | CLOB | 
| OID | String | 