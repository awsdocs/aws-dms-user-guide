# Using Amazon DocumentDB \(with MongoDB compatibility\) as a source for AWS DMS<a name="CHAP_Source.DocumentDB"></a>

AWS DMS supports Amazon DocumentDB \(with MongoDB compatibility\) versions 3\.6 and 4\.0 as a database source\. Using Amazon DocumentDB as a source, you can migrate data from one Amazon DocumentDB cluster to another Amazon DocumentDB cluster\. You can also migrate data from an Amazon DocumentDB cluster to one of the other target endpoints supported by AWS DMS\.

If you are new to DocumentDB, be aware of the following important concepts for DocumentDB databases:
+ A record in DocumentDB is a *document*, a data structure composed of field and value pairs\. The value of a field can include other documents, arrays, and arrays of documents\. A document is roughly equivalent to a row in a relational database table\.
+ A *collection* in DocumentDB is a group of documents, and is roughly equivalent to a relational database table\.

AWS DMS supports two migration modes when using DocumentDB as a source, document mode and table mode\. You specify the migration mode when you create the DocumentDB source endpoint in the AWS DMS console, using either the **Metadata mode** option or the extra connection attribute `nestingLevel`\. Following, you can find an explanation how the choice of migration mode affects the resulting format of the target data\.

**Document mode**  
In *document mode, *the JSON document is migrated as is\. That means the document data is consolidated into one of two items\. When you use a relational database as a target, the data is a single column named `_doc` in a target table\. When you use a nonrelational database as a target, the data is a single JSON document\. Document mode is the default mode, which we recommend when migrating to an Amazon DocumentDB target\.  
For example, consider the following documents in a DocumentDB collection called `myCollection`\.  

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
In *table mode, *AWS DMS transforms each top\-level field in a DocumentDB document into a column in the target table\. If a field is nested, AWS DMS flattens the nested values into a single column\. AWS DMS then adds a key field and data types to the target table's column set\.   
For each DocumentDB document, AWS DMS adds each key and type to the target table's column set\. For example, using table mode, AWS DMS migrates the previous example into the following table\.      
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Source.DocumentDB.html)
Nested values are flattened into a column containing dot\-separated key names\. The column is named using the concatenation of the flattened field names separated by periods\. For example, AWS DMS migrates a JSON document with a field of nested values such as `{"a" : {"b" : {"c": 1}}}` into a column named `a.b.c.`  
To create the target columns, AWS DMS scans a specified number of DocumentDB documents and creates a set of all the fields and their types\. AWS DMS then uses this set to create the columns of the target table\. If you create or modify your DocumentDB source endpoint using the console, you can specify the number of documents to scan\. The default value is 1,000 documents\. If you use the AWS CLI, you can use the extra connection attribute `docsToInvestigate`\.  
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
+ [Limitations of using Amazon DocumentDB as a source](#CHAP_Source.DocumentDB.Limitations)
+ [Using extra connections attributes with Amazon DocumentDB as a source](#CHAP_Source.DocumentDB.ECAs)
+ [Source data types for DocumentDB](#CHAP_Source.DocumentDB.DataTypes)

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

To use ongoing replication or CDC with Amazon DocumentDB, AWS DMS requires access to the DocumentDB cluster's change streams\. For a description of the time\-ordered sequence of update events in your cluster's collections and databases, see [Using change streams](https://docs.aws.amazon.com/documentdb/latest/developerguide/change_streams.html) in the *Amazon DocumentDB Developer Guide*\. 

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

To improve performance of a migration task, DocumentDB source endpoints support the range segmentation option of the parallel full load feature\. In other words, using table mapping JSON settings for parallel full load, you can migrate a segmented collection using threads in parallel\. For more information, see [Table and collection settings rules and operations](CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Tablesettings.md)\.

The following example shows a DocumentDB collection that has seven items, and `_id` as the primary key\.

![\[Amazon DocumentDB collection with seven items.\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-docdb-collection.png)

To split the collection into three segments and migrate in parallel, you can add table mapping rules to your migration task as shown in the following JSON example\.

```
{
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
 },
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
     ]
"boundaries": [
      [
          "5f805c97873173399a278d79", 
// First segment will select documents with _id less-than-or-equal-to 5eae7331a282eff90bf8b060 and num less-than-or-equal-to 2
          "2"
      ],
      [
          "5f805cc5873173399a278d7c", 
// Second segment will select documents with _id less-than-or-equal-to 5f805cc5873173399a278d7c and num less-than-or-equal-to 5, and exclude any document with _id less-than-or-equal-to 5eae7331a282eff90bf8b060 and num less-than-or-equal-to 2
 
 
       ]                                   
// Third segment is implied and selects documents that exclude any documents with _id less-than-or-equal-to 5f805cc5873173399a278d7c and num less-than-or-equal-to 5
 
     ]
   }
 }
]
}
```

That table mapping definition splits the source collection into three segments and migrates in parallel\. The following are the segmentation boundaries\.

```
Data with _id less-than-or-equal-to "5f805c97873173399a278d79" and num less-than-or-equal-to 2 (2 records)
Data with _id less-than-or-equal-to "5f805cc5873173399a278d7c" and num less-than-or-equal-to 5 and not in (_id less-than-or-equal-to  "5f805c97873173399a278d79" and num less-than-or-equal-to 2) (3 records)
Data not in (_id less-than-or-equal-to "5f805cc5873173399a278d7c" and num less-than-or-equal-to 5) (2 records)
```

After the migration task is complete, you can verify from the task logs that the tables loaded in parallel, as shown in the following example\. You can also verify the MongoDB find\(\) clause used to unload each segment from the source table\.

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

Currently, AWS DMS supports the following Amazon DocumentDB data types as a partition key column:
+ Double
+ String
+ ObjectId
+ 32 bit integer
+ 64 bit integer

## Limitations of using Amazon DocumentDB as a source<a name="CHAP_Source.DocumentDB.Limitations"></a>

The following are limitations when using Amazon DocumentDB as a source for AWS DMS:
+ When the `_id` option is set as a separate column, the ID string can't exceed 200 characters\.
+ Object ID and array type keys are converted to columns that are prefixed with `oid` and `array` in table mode\.

  Internally, these columns are referenced with the prefixed names\. If you use transformation rules in AWS DMS that reference these columns, make sure to specify the prefixed column\. For example, specify `${oid__id}` and not `${_id}`, or `${array__addresses}` and not `${_addresses}`\. 
+  Collection names and key names can't include the dollar symbol \($\)\. 
+ Table mode and document mode have the limitations discussed preceding\.
+ A DocumentDB \(MongoDB compatible\) source doesn’t support using a specific timestamp as a start position for change data capture \(CDC\)\. An ongoing replication task starts capturing changes regardless of the timestamp\.

## Using extra connections attributes with Amazon DocumentDB as a source<a name="CHAP_Source.DocumentDB.ECAs"></a>

When you set up your DocumentDB source endpoint, you can specify extra connection attributes\. Extra connection attributes are specified by key\-value pairs\. If you have multiple connection attribute settings, separate them from each other by semicolons with no additional white space \(for example, `oneSetting;thenAnother`\)\.

The following table describes the extra connection attributes available when using DocumentDB databases as an AWS DMS source\.


| Attribute name | Valid values | Default value and description | 
| --- | --- | --- | 
|   `nestingLevel`   |  `"none"` `"one"`  |  `"none"` – Specify `"none"` to use document mode\. Specify `"one"` to use table mode\.  | 
|   `extractDocID`   |  `"true"` `"false"`  |  `"false"` – Use this attribute when `nestingLevel` is set to `"none"`\.  If your target database is DocumentDB, set `extractDocID=true`\.   | 
|   `docsToInvestigate`   |  A positive integer greater than `0`\.  |  `1000` – Use this attribute when `nestingLevel` is set to `"one"`\.   | 

**Note**  
If the target endpoint is DocumentDB, run the migration in** Document mode**, and set the extra connection attribute `extractDocID=true`\. To set the extra connection attribute `extractDocID=true`, modify your source endpoint and check the box **\_id as separate column**\.

## Source data types for DocumentDB<a name="CHAP_Source.DocumentDB.DataTypes"></a>

In the following table, you can find the DocumentDB source data types that are supported when using AWS DMS\. You can also find the default mapping from AWS DMS data types in this table\. For more information about data types, see [BSON types](https://docs.mongodb.com/manual/reference/bson-types) in the MongoDB documentation\.

For information on how to view the data type that is mapped in the target, see the section for the target endpoint that you are using\.

For additional information about AWS DMS data types, see [Data types for AWS Database Migration Service](CHAP_Reference.DataTypes.md)\.


|  DocumentDB data types  |  AWS DMS data types  | 
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