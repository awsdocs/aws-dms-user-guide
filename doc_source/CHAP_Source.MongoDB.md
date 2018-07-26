# Using MongoDB as a Source for AWS DMS<a name="CHAP_Source.MongoDB"></a>

AWS DMS supports MongoDB versions 2\.6\.x and 3\.x as a database source\.

If you are new to MongoDB, be aware of the following important MongoDB database concepts: 
+ A record in MongoDB is a *document*, which is a data structure composed of field and value pairs\. The value of a field can include other documents, arrays, and arrays of documents\.A document is roughly equivalent to a row in a relational database table\.
+ A *collection* in MongoDB is a group of documents, and is roughly equivalent to a relational database table\.
+ Internally, a MongoDB document is stored as a binary JSON \(BSON\) file in a compressed format that includes a type for each field in the document\. Each document has a unique ID\.

AWS DMS supports two migration modes when using MongoDB as a source\. You specify the migration mode using the **Metadata mode** parameter using the AWS Management Console or the extra connection attribute `nestingLevel` when you create the MongoDB endpoint\. The choice of migration mode affects the resulting format of the target data as explained following\. 

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
You can optionally set the extra connection attribute `extractDocID` to *true* to create a second column named `"_id"` that acts as the primary key\. If you are going to use change data capture \(CDC\), set this parameter to *true*\.  
In document mode, AWS DMS manages the creation and renaming of collections like this:  
+ If you add a new collection to the source database, AWS DMS creates a new target table for the collection and replicates any documents\. 
+ If you rename an existing collection on the source database, AWS DMS doesn't rename the target table\. 

**Table mode**  
In table mode, AWS DMS transforms each top\-level field in a MongoDB document into a column in the target table\. If a field is nested, AWS DMS flattens the nested values into a single column\. AWS DMS then adds a key field and data types to the target table's column set\.   
For each MongoDB document, AWS DMS adds each key and type to the target table’s column set\. For example, using table mode, AWS DMS migrates the previous example into the following table\.      
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Source.MongoDB.html)
Nested values are flattened into a column containing dot\-separated key names\. The column is named the concatenation of the flattened field names separated by periods\. For example, AWS DMS migrates a JSON document with a field of nested values such as `{"a" : {"b" : {"c": 1}}}` into a column named `a.b.c.`  
To create the target columns, AWS DMS scans a specified number of MongoDB documents and creates a set of all the fields and their types\. AWS DMS then uses this set to create the columns of the target table\. If you create or modify your MongoDB source endpoint using the console, you can specify the number of documents to scan\. The default value is 1000 documents\. If you use the AWS CLI, you can use the extra connection attribute `docsToInvestigate`\.  
In table mode, AWS DMS manages documents and collections like this:  
+ When you add a document to an existing collection, the document is replicated\. If there are fields that don't exist in the target, those fields aren't replicated\.
+ When you update a document, the updated document is replicated\. If there are fields that don't exist in the target, those fields aren't replicated\.
+ Deleting a document is fully supported\.
+ Adding a new collection doesn't result in a new table on the target when done during a CDC task\.
+ Renaming a collection is not supported\. 

## Permissions Needed When Using MongoDB as a Source for AWS DMS<a name="CHAP_Source.MongoDB.PrerequisitesCDC"></a>

For an AWS DMS migration with a MongoDB source, you can create either a user account with root privileges, or a user with permissions only on the database to migrate\. 

The following code creates a user to be the root account\.

```
use admin
db.createUser(
  {
    user: "root",
    pwd: "<password>",
    roles: [ { role: "root", db: "admin" } ]
  }
)
```

The following code creates a user with minimal privileges on the database to be migrated\.

```
use <database_to_migrate>
db.createUser( 
{ 
    user: "<dms-user>",
    pwd: "<password>",
    roles: [ { role: "read", db: "local" }, "read"] 
})
```

### Configuring a MongoDB Replica Set for Change Data Capture \(CDC\)<a name="CHAP_Source.MongoDB.PrerequisitesCDC.ReplicaSet"></a>

To use ongoing replication or change data capture \(CDC\) with MongoDB, AWS DMS requires access to the MongoDB operations log \(oplog\)\. To create the oplog, you need to deploy a replica set if one doesn't exist\. For more information, see [ the MongoDB documentation](https://docs.mongodb.com/manual/tutorial/deploy-replica-set/)\.

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
   mongod --replSet "rs0" --auth -port <port_number>
   ```

1. Test the connection to the replica set using the following commands:

   ```
   mongo -u root -p <password> --host rs0/localhost:<port_number> 
      --authenticationDatabase "admin"
   ```

If you plan to perform a document mode migration, select option `_id as a separate column` when you create the MongoDB endpoint\. Selecting this option creates a second column named `_id` that acts as the primary key\. This second column is required by AWS DMS to support data manipulation language \(DML\) operations\.

### Security Requirements When Using MongoDB as a Source for AWS DMS<a name="CHAP_Source.MongoDB.Security"></a>

AWS DMS supports two authentication methods for MongoDB\. The two authentication methods are used to encrypt the password, so they are only used when the `authType` parameter is set to *PASSWORD*\.

The MongoDB authentication methods are as follows:
+ **MONOGODB\-CR** – the default when using MongoDB 2\.x authentication\.
+ **SCRAM\-SHA\-1** – the default when using MongoDB version 3\.x authentication\.

If an authentication method is not specified, AWS DMS uses the default method for the version of the MongoDB source\.

### Limitations When Using MongoDB as a Source for AWS DMS<a name="CHAP_Source.MongoDB.Limitations"></a>

The following are limitations when using MongoDB as a source for AWS DMS:
+ When the `_id` option is set as a separate column, the ID string can't exceed 200 characters\.
+ Object ID and array type keys are converted to columns that are prefixed with `oid` and `array` in table mode\.

  Internally, these columns are referenced with the prefixed names\. If you use transformation rules in AWS DMS that reference these columns, you must specify the prefixed column\. For example, you specify `${oid__id}` and not `${_id}`, or `${array__addresses}` and not `${_addresses}`\. 
+  Collection names can't include the dollar symbol \($\)\. 
+ Table mode and document mode have the limitations discussed preceding\.

### Extra Connection Attributes When Using MongoDB as a Source for AWS DMS<a name="CHAP_Source.MongoDB.Configuration"></a>

When you set up your MongoDB source endpoint, you can specify extra connection attributes\. Extra connection attributes are specified by key\-value pairs and separated by semicolons\.

The following table describes the extra connection attributes available when using MongoDB databases as an AWS DMS source\.


| Attribute Name | Valid Values | Default Value and Description | 
| --- | --- | --- | 
|   `authType`   |  NO  PASSWORD   |  PASSWORD – When NO is selected, user name and password parameters aren't used and can be empty\.   | 
|   `authMechanism`   |  DEFAULT MONGODB\_CR SCRAM\_SHA\_1  |  DEFAULT – For MongoDB version 2\.x, use MONGODB\_CR\. For MongoDB version 3\.x, use SCRAM\_SHA\_1\. This attribute isn't used when `authType=NO`\.   | 
|   `nestingLevel`   |  NONE  ONE   |  NONE – Specify NONE to use document mode\. Specify ONE to use table mode\.  | 
|   `extractDocID`   |  true  false   |  false – Use this attribute when `nestingLevel` is set to NONE\.   | 
|   `docsToInvestigate`   |  A positive integer greater than 0\.  |  1000 – Use this attribute when `nestingLevel` is set to ONE\.   | 
|   `authSource`   |  A valid MongoDB database name\.  |  admin – This attribute isn't used when `authType=NO`\.  | 

### Source Data Types for MongoDB<a name="CHAP_Source.MongoDB.DataTypes"></a>

Data migration that uses MongoDB as a source for AWS DMS supports most MongoDB data types\. In the following table, you can find the MongoDB source data types that are supported when using AWS DMS and the default mapping from AWS DMS data types\. For more information about MongoDB data types, see [ BSON Types](https://docs.mongodb.com/manual/reference/bson-types) in the MongoDB documentation\.

For information on how to view the data type that is mapped in the target, see the section for the target endpoint that you are using\.

For additional information about AWS DMS data types, see [Data Types for AWS Database Migration Service](CHAP_Reference.DataTypes.md)\.


|  MongoDB Data Types  |  AWS DMS Data Types  | 
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