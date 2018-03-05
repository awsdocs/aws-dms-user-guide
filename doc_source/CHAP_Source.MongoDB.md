# Using MongoDB as a Source for AWS DMS<a name="CHAP_Source.MongoDB"></a>

AWS DMS supports MongoDB versions 2\.6\.x and 3\.x as a database source\. A MongoDB database is a JSON document database where there are multiple MongoDB collections made up of JSON documents\. In MongoDB, a collection is somewhat equivalent to a relational database table and a JSON document is somewhat equivalent to a row in that relational database table\. Internally, a JSON document is stored as a binary JSON \(BSON\) file in a compressed format that includes a type for each field in the document\. Each document has a unique ID\.

AWS DMS supports two migration modes when using MongoDB as a source:

**Document mode**  
In document mode, the MongoDB document is migrated "as is," meaning that its JSON data becomes a single column in a target table named "\_doc"\.  
You can optionally set the `extractDocID` parameter to *true* to create a second column named `"_id"` that acts as the primary key\. You must set this parameter to *true* if you are going to use change data capture \(CDC\)\.  
Document mode is the default setting when you use MongoDB as a source\. To explicitly specify document mode, add `nestingLevel=NONE` to the extra connection attribute on the MongoDB source endpoint\.  
Following is how AWS DMS manages documents and collections in document mode:  

+ When adding a new collection, the collection is replication as a CREATE TABLE\. 

+ Renaming a collection is not supported\. 

**Table mode**  
In table mode, AWS DMS scans a specified number of MongoDB documents and creates a set of all the keys and their types\. This set is then used to create the columns of the target table\. In this mode, a MongoDB document is transformed into a table data row\. Each top\-level field is transformed into a column\. For each MongoDB document, AWS DMS adds each key and type to the target table’s column set\. Nested values are flattened into a column containing dot\-separated key names\. For example, a JSON document consisting of `{"a" : {"b" : {"c": 1}}}` is migrated into a column named `a.b.c.`  
You can specify how many documents are scanned by setting the `docsToInvestigate` parameter\. The default value is 1000\. You can enable table mode by adding `nestingLevel=ONE` to the extra connection attributes of the MongoDB source endpoint\.  
Following is how AWS DMS manages documents and collections in table mode:  

+ When you add a document \(row\) to an existing collection, the document is replicated\. If there are fields that don't exist in the collection, those fields are not replicated\.

+ When you update a document, the updated document is replicated\. If there are fields that don't exist in the collection, those fields are not replicated\.

+ Deleting a document is fully supported\.

+ Adding a new collection doesn't result in a new table on the target when done during a CDC task\.

+ Renaming a collection is not supported\. 

## Prerequisites When Using CDC with MongoDB as a Source for AWS DMS<a name="CHAP_Source.MongoDB.PrerequisitesCDC"></a>

To use change data capture \(CDC\) with a MongoDB source, you need to do several things\. First, you deploy the replica set to create the operations log\. Next, you create a user that should have at least READ privileges on the database to be migrated to and those same privileges to the LOCAL database\. 

You can use CDC with the Primary or Secondary node of a MongoDB replica set as the source endpoint\.

### Deploying a CDC Replica Set for MongoDB as a Source for AWS DMS<a name="CHAP_Source.MongoDB.PrerequisitesCDC.ReplicaSet"></a>

To use MongoDB as an AWS DMS source, you need to deploy a replica set\. When you deploy the replica set, you create the operations log that AWS DMS uses for CDC processing\. For more information, see [ the MongoDB documentation](https://docs.mongodb.com/manual/tutorial/deploy-replica-set/)\.

**To convert a standalone instance to a replica set**

1. Using the command line, connect to mongo\.

   ```
   mongo localhost
   ```

1. Create a user to be the root account, as shown in the following code\. In this example, we call that user `root`\.

   ```
   use admin
   db.createUser(
     {
       user: "root",
       pwd: "rootpass",
       roles: [ { role: "root", db: "admin" } ]
     }
   )
   ```

   Next, create a user with minimal privileges using the following code\. In this example, we call that user `root1`\.

   ```
   use <<database_to_migrate>>
   db.createUser( 
   { 
       user: "root1",
       pwd: "rootpass",
       roles: [ { role: "read", db: "local" }, "read"] 
   })
   ```

1. Stop the mongod service\.

   ```
   service mongod stop
   ```

1. Restart mongod using the following command:

   ```
   mongod --replSet "rs0" --auth -port <port_number>
   ```

1. Test the connection to the replica set using the following commands:

   ```
   mongo -u root -p rootpass --host rs0/localhost:<port_number> --authenticationDatabase "admin" 
   ```

The final requirement to use CDC with MongoDB is to select the option `_id as a separate column` to create a second column named `"_id"` that acts as the primary key\.

## Security Requirements When Using MongoDB as a Source for AWS DMS<a name="CHAP_Source.MongoDB.Security"></a>

AWS DMS supports two authentication methods for MongoDB\. The two authentication methods are used to encrypt the password, so they are only used when the `Authentication mode` parameter is set to *password*\.

The MongoDB authentication methods are:

+ **MONOGODB\-CR** — the default when using MongoDB 2\.x authentication\.

+ **SCRAM\-SHA\-1** — the default when using MongoDB version 3\.x authentication\.

If an authentication method is not specified, AWS DMS uses the default method for the version of the MongoDB source\. The two authentication methods are used to encrypt the password\.

## Limitations When Using MongoDB as a Source for AWS DMS<a name="CHAP_Source.MongoDB.Limitations"></a>

The following are limitations when using MongoDB as a source for AWS DMS:

+ When the `_id as a separate column` parameter is selected, the ID string cannot exceed 200 characters\.

+ ObjectId and array type keys are converted to columns that are prefixed with `oid` and `array` in table mode\.

  Internally these columns are referenced with the prefixed names\. When you use transformation rules, you must specify the prefixed column\. For example, specify `${oid__id}` and not `${_id}`, or specify `${array__addresses}` and not `${_addresses}`\. 

+  Collection names cannot include the dollar symbol \($\)\. 

## Configuration Properties When Using MongoDB as a Source for AWS DMS<a name="CHAP_Source.MongoDB.Configuration"></a>

When you set up your MongoDB source endpoint, you can specify additional configuration settings attributes\. Attributes are specified by key\-value pairs and separated by semicolons\. 

The following table describes the configuration properties available when using MongoDB databases as an AWS DMS source database\.


| Attribute Name | Valid Values | Default Value and Description | 
| --- | --- | --- | 
|   authType   |  NO  PASSWORD   |  PASSWORD – When NO is selected, user name and password parameters are not used and can be empty\.   | 
|   authMechanism   |  DEFAULT MONGODB\_CR SCRAM\_SHA\_1  |  DEFAULT – For MongoDB version 2\.x, use MONGODB\_CR\. For MongoDB version 3\.x, use SCRAM\_SHA\_1\. This attribute is not used when authType=No\.   | 
|   nestingLevel   |  NONE  ONE   |  NONE – Specify NONE to use document mode\. Specify ONE to use table mode\.  | 
|   extractDocID   |  true  false   |  false – Use this attribute when `nestingLevel` is set to NONE\.   | 
|   docsToInvestigate   |  A positive integer greater than 0\.  |  1000 – Use this attribute when `nestingLevel` is set to ONE\.   | 
|   authSource   |  A valid MongoDB database name\.  |  admin – This attribute is not used when authType=No\.  | 

## Source Data Types for MongoDB<a name="CHAP_Source.MongoDB.DataTypes"></a>

Data migration that uses MongoDB as a source for AWS DMS supports most MongoDB data types\. The following table shows the MongoDB source data types that are supported when using AWS DMS and the default mapping from AWS DMS data types\. For more information about MongoDB data types, see [ https://docs\.mongodb\.com/manual/reference/bson\-types](https://docs.mongodb.com/manual/reference/bson-types)\.

For information on how to view the data type that is mapped in the target, see the section for the target endpoint you are using\.

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