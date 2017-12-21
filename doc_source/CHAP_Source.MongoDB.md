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

+ When you add a document to an existing collection, the document \(row\) is replicated\. If there are fields that don't exist in the collection, those fields are not replicated\.

+ When you update a document, the updated document is replicated\. If there are fields that don't exist in the collection, those fields are not replicated\.

+ Deleting a document is fully supported\.

+ Adding a new collection doesn't result in a new table on the target when done during a CDC task\.

+ Renaming a collection is not supported\. 

## Prerequisites When Using CDC with MongoDB as a Source for AWS DMS<a name="CHAP_Source.MongoDB.PrerequisitesCDC"></a>

To use change data capture \(CDC\) with a MongoDB source, you will need to do several things\. First, you deploy the replica set to create the operations log\. Next, you create the system user administrator\. Finally, you set the extractDocID parameter to *true* to extract the document ID that is used during CDC\.

The MongoDB operations log \(oplog\) is a special capped collection that keeps a rolling record of all operations that modify the data stored in your databases\. MongoDB applies database operations on the **primary** and then records the operations on the primary's **oplog**\. The secondary members then copy and apply these operations in an asynchronous process\.

You can only use CDC with the primary node of a MongoDB replica set as the source endpoint; secondary nodes cannot access the oplog so CDC is not possible\.

The user account used for the MongoDB endpoint needs to have access to the operations log of the replica set you create\.

### Deploying a CDC Replica Set for MongoDB as a Source for AWS DMS<a name="CHAP_Source.MongoDB.PrerequisitesCDC.ReplicaSet"></a>

To use MongoDB as an AWS DMS source, you need to deploy a replica set\. When you deploy the replica set, you create the operations log that is used for CDC\. For more information, see [ the MongoDB documentation](https://docs.mongodb.com/manual/tutorial/deploy-replica-set/)\.

**To deploy a replica set**

1. Using the command line, connect to mongo\.

   ```
   mongo localhost
   ```

1. Run in mongo shell\.

   ```
   rs.initiate() root
   ```

1. Verify the deployment\.

   ```
   rs.conf()
   ```

1. Set a different database path\. Run with \-\-dbpath the\-path\.

Next, create the system user administrator role\.

**To create the root user**

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

1. Stop mongod\.

1. Restart mongod using the following command:

   ```
   mongod --replSet "rs0" --auth
   ```

1. Test that the operations log is readable using the following commands:

   ```
   mongo localhost/admin -u root -p rootpass 
   mongo  --authenticationDatabase admin -u root -p rootpass
   ```

1. If you are unable to read from operations log, run the following command:

   ```
   rs.initiate();
   ```

The final requirement to use CDC with MongoDB is to set the `extractDocID` parameter\. Set the `extractDocID` parameter to *true* to create a second column named `"_id"` that acts as the primary key\.

## Security Requirements When Using MongoDB as a Source for AWS DMS<a name="CHAP_Source.MongoDB.Security"></a>

AWS DMS supports two authentication methods for MongoDB\. The two authentication methods are used to encrypt the password, so they are only used when the `authType` parameter is set to *password*\.

The MongoDB authentication methods are:

+ **MONOGODB\-CR** — the default when using MongoDB 2\.x authentication\.

+ **SCRAM\-SHA\-1** — the default when using MongoDB version 3\.x authentication\.

If an authentication method is not specified, AWS DMS uses the default method for the version of the MongoDB source\. The two authentication methods are used to encrypt the password, so they are only used when the `authType` parameter is set to *password*\.

## Limitations When Using MongoDB as a Source for AWS DMS<a name="CHAP_Source.MongoDB.Limitations"></a>

The following are limitations when using MongoDB as a source for AWS DMS:

+ When the `extractDocID` parameter is set to *true*, the ID string cannot exceed 200 characters\.

+ ObjectId and array type keys are converted to columns that are prefixed with `oid` and `array` in table mode\.

  Internally these columns are referenced with the prefixed names\. When you use transformation rules, you must specify the prefixed column\. For example, you would specify `${oid__id}` and not `${_id}`, or you would specify `${array__addresses}` and not `${_addresses}`\. 

+  Collection names cannot include the dollar symbol \($\)\. 

+ Secondary nodes cannot be used as source endpoints\.

## Configuration Properties When Using MongoDB as a Source for AWS DMS<a name="CHAP_Source.MongoDB.Configuration"></a>

When you set up your MongoDB source endpoint, you can specify additional configuration settings attributes\. Attributes are specified by key\-value pairs and separated by semicolons\. For example, the following code specifies that user name and password are not used for authentication, and that table mode is used\.

The following table describes the configuration properties available when using MongoDB databases as an AWS DMS source database\.


| Attribute Name | Valid Values | Default Value and Description | 
| --- | --- | --- | 
|   authType   |  NO  PASSWORD   |  PASSWORD – When NO is selected, user name and password parameters are not used and can be empty\.   | 
|   authMechanism   |  DEFAULT MONGODB\_CR SCRAM\_SHA\_1  |  DEFAULT – For MongoDB version 2\.x, use MONGODB\_CR\. For MongoDB version 3\.x, use SCRAM\_SHA\_1\. This attribute is not used when authType=No\.   | 
|   nestingLevel   |  NONE  ONE   |  NONE – Specify NONE to use document mode\. Specify ONE to use table mode\.  | 
|   extractDocID   |  true  false   |  false – Use this attribute when `nestingLevel` is set to NONE\.   | 
|   docsToInvestigate   |  A positive integer greater than 0\.  |  1000 – Use this attribute when `nestingLevel` is set to ONE\.   | 
|   authSource   |  A valid MongoDB database name\.  |  admin – This attribute is not used when authType=No\.  | 