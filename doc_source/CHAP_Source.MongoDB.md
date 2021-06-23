# Using MongoDB as a source for AWS DMS<a name="CHAP_Source.MongoDB"></a>

AWS DMS supports MongoDB versions 3\.x and 4\.0 as a database source\.

If you are new to MongoDB, be aware of the following important MongoDB database concepts: 
+ A record in MongoDB is a *document*, which is a data structure composed of field and value pairs\. The value of a field can include other documents, arrays, and arrays of documents\. A document is roughly equivalent to a row in a relational database table\.
+ A *collection* in MongoDB is a group of documents, and is roughly equivalent to a relational database table\.
+ Internally, a MongoDB document is stored as a binary JSON \(BSON\) file in a compressed format that includes a type for each field in the document\. Each document has a unique ID\.

AWS DMS supports two migration modes when using MongoDB as a source, *Document mode* or *Table mode*\. You specify which migration mode to use when you create the MongoDB endpoint or by setting the **Metadata mode** parameter from the AWS DMS console\. Optionally, you can create a second column named `_id` that acts as the primary key by selecting the check mark button for **\_id as a separate column** in the endpoint configuration panel\. 

Your choice of migration mode affects the resulting format of the target data, as explained following\. 

**Document mode**  
In document mode, the MongoDB document is migrated as is, meaning that the document data is consolidated into a single column named `_doc` in a target table\. Document mode is the default setting when you use MongoDB as a source endpoint\.  
For example, consider the following documents in a MongoDB collection called myCollection\.  

```
> db.myCollection.find()
{ "_id" : ObjectId("5a94815f40bd44d1b02bdfe0"), "a" : 1, "b" : 2, "c" : 3 }
{ "_id" : ObjectId("5a94815f40bd44d1b02bdfe1"), "a" : 4, "b" : 5, "c" : 6 }
```
After migrating the data to a relational database table using document mode, the data is structured as follows\. The data fields in the MongoDB document are consolidated into the` _doc` column\.      
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Source.MongoDB.html)
You can optionally set the extra connection attribute `extractDocID` to *true* to create a second column named `"_id"` that acts as the primary key\. If you are going to use CDC, set this parameter to *true* except when using Amazon DocumentDB as target\.  
In document mode, AWS DMS manages the creation and renaming of collections like this:  
+ If you add a new collection to the source database, AWS DMS creates a new target table for the collection and replicates any documents\. 
+ If you rename an existing collection on the source database, AWS DMS doesn't rename the target table\. 
If the target endpoint is Amazon DocumentDB, run the migration in **Document mode**\.

**Table mode**  
In table mode, AWS DMS transforms each top\-level field in a MongoDB document into a column in the target table\. If a field is nested, AWS DMS flattens the nested values into a single column\. AWS DMS then adds a key field and data types to the target table's column set\.   
For each MongoDB document, AWS DMS adds each key and type to the target table's column set\. For example, using table mode, AWS DMS migrates the previous example into the following table\.      
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Source.MongoDB.html)
Nested values are flattened into a column containing dot\-separated key names\. The column is named the concatenation of the flattened field names separated by periods\. For example, AWS DMS migrates a JSON document with a field of nested values such as `{"a" : {"b" : {"c": 1}}}` into a column named `a.b.c.`  
To create the target columns, AWS DMS scans a specified number of MongoDB documents and creates a set of all the fields and their types\. AWS DMS then uses this set to create the columns of the target table\. If you create or modify your MongoDB source endpoint using the console, you can specify the number of documents to scan\. The default value is 1000 documents\. If you use the AWS CLI, you can use the extra connection attribute `docsToInvestigate`\.  
In table mode, AWS DMS manages documents and collections like this:  
+ When you add a document to an existing collection, the document is replicated\. If there are fields that don't exist in the target, those fields aren't replicated\.
+ When you update a document, the updated document is replicated\. If there are fields that don't exist in the target, those fields aren't replicated\.
+ Deleting a document is fully supported\.
+ Adding a new collection doesn't result in a new table on the target when done during a CDC task\.
+ Renaming a collection isn't supported\. 

## Permissions needed when using MongoDB as a source for AWS DMS<a name="CHAP_Source.MongoDB.PrerequisitesCDC"></a>

For an AWS DMS migration with a MongoDB source, you can create either a user account with root privileges, or a user with permissions only on the database to migrate\. 

The following code creates a user to be the root account\.

```
use admin
db.createUser(
  {
    user: "root",
    pwd: "password",
    roles: [ { role: "root", db: "admin" } ]
  }
)
```

For a MongoDB 3\.x source, the following code creates a user with minimal privileges on the database to be migrated\.

```
use database_to_migrate
db.createUser( 
{ 
    user: "dms-user",
    pwd: "password",
    roles: [ { role: "read", db: "local" }, "read"] 
})
```

For a MongoDB 4\.x source, the following code creates a user with minimal privileges\.

```
{ resource: { db: "", collection: "" }, actions: [ "find", "changeStream" ] }
```

For example, create the following role in the "admin" database\.

```
use admin
db.createRole(
{
role: "changestreamrole",
privileges: [
{ resource: { db: "", collection: "" }, actions: [ "find","changeStream" ] }
],
roles: []
}
)
```

And once the role is created, create a user in the database to be migrated\.

```
> use test
> db.createUser( 
{ 
user: "dms-user12345",
pwd: "password",
roles: [ { role: "changestreamrole", db: "admin" }, "read"] 
})
```

## Configuring a MongoDB replica set for CDC<a name="CHAP_Source.MongoDB.PrerequisitesCDC.ReplicaSet"></a>

To use ongoing replication or CDC with MongoDB, AWS DMS requires access to the MongoDB operations log \(oplog\)\. To create the oplog, you need to deploy a replica set if one doesn't exist\. For more information, see [ the MongoDB documentation](https://docs.mongodb.com/manual/tutorial/deploy-replica-set/)\.

You can use CDC with either the primary or secondary node of a MongoDB replica set as the source endpoint\.

**To convert a standalone instance to a replica set**

1. Using the command line, connect to `mongo`\.

   ```
   mongo localhost
   ```

1. Stop the `mongod` service\.

   ```
   service mongod stop
   ```

1. Restart `mongod` using the following command:

   ```
   mongod --replSet "rs0" --auth -port port_number
   ```

1. Test the connection to the replica set using the following commands:

   ```
   mongo -u root -p password --host rs0/localhost:port_number 
     --authenticationDatabase "admin"
   ```

If you plan to perform a document mode migration, select option `_id as a separate column` when you create the MongoDB endpoint\. Selecting this option creates a second column named `_id` that acts as the primary key\. This second column is required by AWS DMS to support data manipulation language \(DML\) operations\.

## Security requirements when using MongoDB as a source for AWS DMS<a name="CHAP_Source.MongoDB.Security"></a>

AWS DMS supports two authentication methods for MongoDB\. The two authentication methods are used to encrypt the password, so they are only used when the `authType` parameter is set to *PASSWORD*\.

The MongoDB authentication methods are as follows:
+ **MONGODB\-CR** – for backword compatibility
+ **SCRAM\-SHA\-1** – the default when using MongoDB version 3\.x and 4\.0

If an authentication method isn't specified, AWS DMS uses the default method for the version of the MongoDB source\.

## Segmenting MongoDB collections and migrating in parallel<a name="CHAP_Source.MongoDB.ParallelLoad"></a>

To improve performance of a migration task, MongoDB source endpoints support the range segmentation option of the parallel full load feature\. In other words, using table mapping JSON settings for parallel full load, you can migrate a segmented collection using threads in parallel\. For more information, see [Table and collection settings rules and operations](CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Tablesettings.md)\.

The following example shows a MongoDB collection that has seven items, and `_id` as the primary key\.

![\[MongoDB collection with seven items.\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-docdb-collection.png)

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
// First segment will select documents with _id less-than-or-equal-to 5f805c97873173399a278d79 and num less-than-or-equal-to 2"2"
      ],
      [
          "5f805cc5873173399a278d7c", 
// Second segment will select documents with _id > 5f805c97873173399a278d79 and _id less-than-or-equal-to 5f805cc5873173399a278d7c
          "5" // and num > 2 and num less-than-or-equal-to 5
       ]                                   
// Third segment is implied and selects documents with _id > 5f805cc5873173399a278d7c
     ]
   }
 }
]
}
```

That table mapping definition splits the source collection into three segments and migrates in parallel\. The following are the segmentation boundaries\.

```
Data with _id less-than-or-equal-to "5f805c97873173399a278d79" and num less-than-or-equal-to 2 (2 records)
Data with _id > "5f805c97873173399a278d79" and num > 2 and _id  less-than-or-equal-to "5f805cc5873173399a278d7c" and num less-than-or-equal-to 5 (3 records)
Data with _id > "5f805cc5873173399a278d7c" and num > 5 (2 records)
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

Currently, AWS DMS supports the following MongoDB data types as a partition key column:
+ Double
+ String
+ ObjectId
+ 32 bit integer
+ 64 bit integer

## Limitations when using MongoDB as a source for AWS DMS<a name="CHAP_Source.MongoDB.Limitations"></a>

The following are limitations when using MongoDB as a source for AWS DMS:
+ When the `_id` option is set as a separate column, the ID string can't exceed 200 characters\.
+ Object ID and array type keys are converted to columns that are prefixed with `oid` and `array` in table mode\.

  Internally, these columns are referenced with the prefixed names\. If you use transformation rules in AWS DMS that reference these columns, you must specify the prefixed column\. For example, you specify `${oid__id}` and not `${_id}`, or `${array__addresses}` and not `${_addresses}`\. 
+  Collection names and key names can't include the dollar symbol \($\)\. 
+ Table mode and document mode have the limitations discussed preceding\.
+ Source filters are not supported for MongoDB\.

## Endpoint configuration settings when using MongoDB as a source for AWS DMS<a name="CHAP_Source.MongoDB.Configuration"></a>

When you set up your MongoDB source endpoint, you can specify multiple endpoint configuration settings using the AWS DMS console\. 

The following table describes the configuration settings available when using MongoDB databases as an AWS DMS source\. 


| Settings \(attribute\) | Valid values | Default value and description | 
| --- | --- | --- | 
|  **Authentication mode**  |  `"none"` `"password"`  |  `"password"` prompts for a user name and password\. When `"none"` is specified, user name and password parameters aren't used\.  | 
|  **Authentication source**  |  A valid MongoDB database name\.  |  Default Value is `"admin"`\. The name of the MongoDB database that you want to use to validate your credentials for authentication\.  | 
|  **Authentication mechanism**  |  `"default"` `"mongodb_cr"` `"scram_sha_1"`  |  `"default"` is `"scram_sha_1"`\. This setting isn't used when `authType` is set to `"no"`\.  | 
|  **Metadata mode**  |  Document and Table  |  Choses **Document mode** or **Table mode**\.   | 
|  **Number of documents to scan**\(`docsToInvestigate`\)  |  A positive integer greater than `0`\.  |  Use this option in table mode only to define the target table defination\.  | 
|  **\_id as a separate column**  |  Check mark box  |  Optional check mark box—creates a second column named `_id` that acts as the primary key  | 

If you choose **Document** as **Metadata mode**, different options are available\.

**Note**  
If the target endpoint is DocumentDB, run the migration in **Document mode**, modify your source endpoint and check the box **\_id as separate column**\.

## Source data types for MongoDB<a name="CHAP_Source.MongoDB.DataTypes"></a>

Data migration that uses MongoDB as a source for AWS DMS supports most MongoDB data types\. In the following table, you can find the MongoDB source data types that are supported when using AWS DMS and the default mapping from AWS DMS data types\. For more information about MongoDB data types, see [ BSON types](https://docs.mongodb.com/manual/reference/bson-types) in the MongoDB documentation\.

For information on how to view the data type that is mapped in the target, see the section for the target endpoint that you are using\.

For additional information about AWS DMS data types, see [Data types for AWS Database Migration Service](CHAP_Reference.DataTypes.md)\.


|  MongoDB data types  |  AWS DMS data types  | 
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
| REGEX | CLOB | 
| CODE | CLOB | 