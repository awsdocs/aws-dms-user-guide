# Using Amazon DocumentDB as a target for AWS Database Migration Service<a name="CHAP_Target.DocumentDB"></a>

You can use AWS DMS to migrate data to Amazon DocumentDB \(with MongoDB compatibility\) from any of the source data engines that AWS DMS supports\. The source engine can be on an AWS\-managed service such as Amazon RDS, Aurora, or Amazon S3\. Or the engine can be on a self\-managed database, such as MongoDB running on Amazon EC2 or on\-premises\.

You can use AWS DMS to replicate source data to Amazon DocumentDB databases, collections, or documents\. 

**Note**  
If your source endpoint is MongoDB or Amazon DocumentDB, run the migration in **Document mode**\.

MongoDB stores data in a binary JSON format \(BSON\)\. AWS DMS supports all of the BSON data types that are supported by Amazon DocumentDB\. For a list of these data types, see [Supported MongoDB APIs, operations, and data types](https://docs.aws.amazon.com/documentdb/latest/developerguide/mongo-apis.html) in *the Amazon DocumentDB Developer Guide\.*

If the source endpoint is a relational database, AWS DMS maps database objects to Amazon DocumentDB as follows:
+ A relational database, or database schema, maps to an Amazon DocumentDB *database*\. 
+ Tables within a relational database map to *collections* in Amazon DocumentDB\.
+ Records in a relational table map to *documents* in Amazon DocumentDB\. Each document is constructed from data in the source record\.

If the source endpoint is Amazon S3, then the resulting Amazon DocumentDB objects correspond to AWS DMS mapping rules for Amazon S3\. For example, consider the following URI\.

```
s3://mybucket/hr/employee
```

In this case, AWS DMS maps the objects in `mybucket` to Amazon DocumentDB as follows:
+ The top\-level URI part \(`hr`\) maps to an Amazon DocumentDB database\. 
+ The next URI part \(`employee`\) maps to an Amazon DocumentDB collection\.
+ Each object in `employee` maps to a document in Amazon DocumentDB\.

For more information on mapping rules for Amazon S3, see [Using Amazon S3 as a source for AWS DMS](CHAP_Source.S3.md)\.

For additional details on working with Amazon DocumentDB as a target for AWS DMS, see the following sections:

**Topics**
+ [Mapping data from a source to an Amazon DocumentDB target](#CHAP_Target.DocumentDB.data-mapping)
+ [Ongoing replication with Amazon DocumentDB as a target](#CHAP_Target.DocumentDB.data-mapping.ongoing-replication)
+ [Limitations to using Amazon DocumentDB as a target](#CHAP_Target.DocumentDB.limitations)
+ [Target data types for Amazon DocumentDB](#CHAP_Target.DocumentDB.datatypes)

**Note**  
For a step\-by\-step walkthrough of the migration process, see [Migrating from MongoDB to Amazon DocumentDB ](https://docs.aws.amazon.com/dms/latest/sbs/CHAP_MongoDB2DocumentDB.html) in the AWS Database Migration Service Step\-by\-Step Migration Guide\.

## Mapping data from a source to an Amazon DocumentDB target<a name="CHAP_Target.DocumentDB.data-mapping"></a>

AWS DMS reads records from the source endpoint, and constructs JSON documents based on the data it reads\. For each JSON document, AWS DMS must determine an `_id` field to act as a unique identifier\. It then writes the JSON document to an Amazon DocumentDB collection, using the `_id` field as a primary key\.

### Source data that is a single column<a name="CHAP_Target.DocumentDB.data-mapping.single-column"></a>

If the source data consists of a single column, the data must be of a string type\. \(Depending on the source engine, the actual data type might be VARCHAR, NVARCHAR, TEXT, LOB, CLOB, or similar\.\) AWS DMS assumes that the data is a valid JSON document, and replicates the data to Amazon DocumentDB as is\.

If the resulting JSON document contains a field named `_id`, then that field is used as the unique `_id` in Amazon DocumentDB\.

If the JSON doesn't contain an `_id` field, then Amazon DocumentDB generates an `_id` value automatically\.

### Source data that is multiple columns<a name="CHAP_Target.DocumentDB.data-mapping.multiple-columns"></a>

If the source data consists of multiple columns, then AWS DMS constructs a JSON document from all of these columns\. To determine the `_id` field for the document, AWS DMS proceeds as follows:
+ If one of the columns is named `_id`, then the data in that column is used as the target`_id`\.
+ If there is no `_id` column, but the source data has a primary key or a unique index, then AWS DMS uses that key or index value as the `_id` value\. The data from the primary key or unique index also appears as explicit fields in the JSON document\.
+ If there is no `_id` column, and no primary key or a unique index, then Amazon DocumentDB generates an `_id` value automatically\.

### Coercing a data type at the target endpoint<a name="CHAP_Target.DocumentDB.coercing-datatype"></a>

AWS DMS can modify data structures when it writes to an Amazon DocumentDB target endpoint\. You can request these changes by renaming columns and tables at the source endpoint, or by providing transformation rules that are applied when a task is running\.

#### Using a nested JSON document \(json\_ prefix\)<a name="CHAP_Target.DocumentDB.coercing-datatype.json"></a>

To coerce a data type, you can prefix the source column name with `json_` \(that is, `json_columnName`\) either manually or using a transformation\. In this case, the column is created as a nested JSON document within the target document, rather than as a string field\.

For example, suppose that you want to migrate the following document from a MongoDB source endpoint\.

```
{
    "_id": "1", 
    "FirstName": "John", 
    "LastName": "Doe",
    "ContactDetails": "{"Home": {"Address": "Boston","Phone": "1111111"},"Work": { "Address": "Boston", "Phone": "2222222222"}}"
}
```

If you don't coerce any of the source data types, the embedded `ContactDetails` document is migrated as a string\.

```
{
    "_id": "1", 
    "FirstName": "John", 
    "LastName": "Doe",
    "ContactDetails": "{\"Home\": {\"Address\": \"Boston\",\"Phone\": \"1111111\"},\"Work\": { \"Address\": \"Boston\", \"Phone\": \"2222222222\"}}"
}
```

However, you can add a transformation rule to coerce `ContactDetails` to a JSON object\. For example, suppose that the original source column name is `ContactDetails`\. To coerce the data type as Nested JSON, the column at source endpoint needs to be renamed as json\_ContactDetails” either by adding “\*json\_\*“ prefix on the source manually or through transformation rules\. For example, you can use the below transformation rule:

```
{
    "rules": [
    {
    "rule-type": "transformation",
    "rule-id": "1",
    "rule-name": "1",
    "rule-target": "column",
    "object-locator": {
    "schema-name": "%",
    "table-name": "%",
    "column-name": "ContactDetails"
     },
    "rule-action": "rename",
    "value": "json_ContactDetails",
    "old-value": null
    }
    ]
}
```

AWS DMS replicates the ContactDetails field as nested JSON, as follows\. 

```
{
    "_id": "1",
    "FirstName": "John",
    "LastName": "Doe",
    "ContactDetails": {
        "Home": {
            "Address": "Boston",
            "Phone": "1111111111"
        },
        "Work": {
            "Address": "Boston",
            "Phone": "2222222222"
        }
    }
}
```

#### Using a JSON array \(array\_ prefix\)<a name="CHAP_Target.DocumentDB.coercing-datatype.array"></a>

To coerce a data type, you can prefix a column name with `array_` \(that is, `array_columnName`\), either manually or using a transformation\. In this case, AWS DMS considers the column as a JSON array, and creates it as such in the target document\.

Suppose that you want to migrate the following document from a MongoDB source endpoint\.

```
{
    "_id" : "1",
    "FirstName": "John",
    "LastName": "Doe", 
    "ContactAddresses": ["Boston", "New York"],             
    "ContactPhoneNumbers": ["1111111111", "2222222222"]
}
```

If you don't coerce any of the source data types, the embedded `ContactDetails` document is migrated as a string\.

```
{
    "_id": "1",
    "FirstName": "John",
    "LastName": "Doe", 
    "ContactAddresses": "[\"Boston\", \"New York\"]",             
    "ContactPhoneNumbers": "[\"1111111111\", \"2222222222\"]" 
}
```

 However, you can add transformation rules to coerce `ContactAddress` and `ContactPhoneNumbers` to JSON arrays, as shown in the following table\.


****  

| Original source column name | Renamed source column | 
| --- | --- | 
| ContactAddress | array\_ContactAddress | 
| ContactPhoneNumbers | array\_ContactPhoneNumbers | 

AWS DMS replicates `ContactAddress` and `ContactPhoneNumbers` as follows\.

```
{
    "_id": "1",
    "FirstName": "John",
    "LastName": "Doe",
    "ContactAddresses": [
        "Boston",
        "New York"
    ],
    "ContactPhoneNumbers": [
        "1111111111",
        "2222222222"
    ]
}
```

### Connecting to Amazon DocumentDB using TLS<a name="CHAP_Target.DocumentDB.tls"></a>

By default, a newly created Amazon DocumentDB cluster accepts secure connections only using Transport Layer Security \(TLS\)\. When TLS is enabled, every connection to Amazon DocumentDB requires a public key\.

You can retrieve the public key for Amazon DocumentDB by downloading the file, `rds-combined-ca-bundle.pem`, from an AWS\-hosted Amazon S3 bucket\. For more information on downloading this file, see [Encrypting connections using TLS](https://docs.aws.amazon.com/documentdb/latest/developerguide/security.encryption.ssl.html) in the *Amazon DocumentDB Developer Guide*

After you download this \.pem file, you can import the public key that it contains into AWS DMS as described following\.

#### AWS Management Console<a name="CHAP_Target.DocumentDB.tls.con"></a>

**To import the public key \(\.pem\) file**

1. Open the AWS DMS console at [https://console\.aws\.amazon\.com/dms](https://console.aws.amazon.com/dms)\.

1. In the navigation pane, choose **Certificates**\.

1. Choose **Import certificate** and do the following:
   + For **Certificate identifier**, enter a unique name for the certificate, for example `docdb-cert`\.
   + For **Import file**, navigate to the location where you saved the \.pem file\.

   When the settings are as you want them, choose **Add new CA certificate**\.

#### AWS CLI<a name="CHAP_Target.DocumentDB.tls.cli"></a>

Use the `aws dms import-certificate` command, as shown in the following example\.

```
aws dms import-certificate \
    --certificate-identifier docdb-cert \
    --certificate-pem file://./rds-combined-ca-bundle.pem
```

When you create an AWS DMS target endpoint, provide the certificate identifier \(for example, `docdb-cert`\)\. Also, set the SSL mode parameter to `verify-full`\.

## Ongoing replication with Amazon DocumentDB as a target<a name="CHAP_Target.DocumentDB.data-mapping.ongoing-replication"></a>

If ongoing replication is enabled, AWS DMS ensures that documents in Amazon DocumentDB stay in sync with the source\. When a source record is created or updated, AWS DMS must first determine which Amazon DocumentDB record is affected by doing the following:
+ If the source record has a column named `_id`, the value of that column determines the corresponding `_id` in the Amazon DocumentDB collection\.
+ If there is no `_id` column, but the source data has a primary key or unique index, then AWS DMS uses that key or index value as the `_id` for the Amazon DocumentDB collection\.
+ If the source record doesn't have an `_id` column, a primary key, or a unique index, then AWS DMS matches all of the source columns to the corresponding fields in the Amazon DocumentDB collection\.

When a new source record is created, AWS DMS writes a corresponding document to Amazon DocumentDB\. If an existing source record is updated, AWS DMS updates the corresponding fields in the target document in Amazon DocumentDB\. Any fields that exist in the target document but not in the source record remain untouched\.

When a source record is deleted, AWS DMS deletes the corresponding document from Amazon DocumentDB\.

### Structural changes \(DDL\) at the source<a name="CHAP_Target.DocumentDB.data-mapping.ongoing-replication.ddl"></a>

With ongoing replication, any changes to source data structures \(such as tables, columns, and so on\) are propagated to their counterparts in Amazon DocumentDB\. In relational databases, these changes are initiated using data definition language \(DDL\) statements\. You can see how AWS DMS propagates these changes to Amazon DocumentDB in the following table\.


****  

| DDL at source | Effect at Amazon DocumentDB target | 
| --- | --- | 
| CREATE TABLE | Creates an empty collection\. | 
| Statement that renames a table \(RENAME TABLE, ALTER TABLE\.\.\.RENAME, and similar\) | Renames the collection\. | 
| TRUNCATE TABLE | Removes all the documents from the collection, but only if HandleSourceTableTruncated is true\. For more information, see [Task settings for change processing DDL handling](CHAP_Tasks.CustomizingTasks.TaskSettings.DDLHandling.md)\. | 
| DROP TABLE | Deletes the collection, but only if HandleSourceTableDropped is true\. For more information, see [Task settings for change processing DDL handling](CHAP_Tasks.CustomizingTasks.TaskSettings.DDLHandling.md)\. | 
| Statement that adds a column to a table \(ALTER TABLE\.\.\.ADD and similar\) | The DDL statement is ignored, and a warning is issued\. When the first INSERT is performed at the source, the new field is added to the target document\. | 
| ALTER TABLE\.\.\.RENAME COLUMN | The DDL statement is ignored, and a warning is issued\. When the first INSERT is performed at the source, the newly named field is added to the target document\. | 
| ALTER TABLE\.\.\.DROP COLUMN | The DDL statement is ignored, and a warning is issued\. | 
| Statement that changes the column data type \(ALTER COLUMN\.\.\.MODIFY and similar\) | The DDL statement is ignored, and a warning is issued\. When the first INSERT is performed at the source with the new data type, the target document is created with a field of that new data type\. | 

## Limitations to using Amazon DocumentDB as a target<a name="CHAP_Target.DocumentDB.limitations"></a>

The following limitations apply when using Amazon DocumentDB as a target for AWS DMS:
+ In Amazon DocumentDB, collection names can't contain the dollar symbol \($\)\. In addition, database names can't contain any Unicode characters\.
+ AWS DMS doesn't support merging of multiple source tables into a single Amazon DocumentDB collection\.
+ When AWS DMS processes changes from a source table that doesn't have a primary key, any LOB columns in that table are ignored\.
+ If the **Change table** option is enabled and AWS DMS encounters a source column named "*\_id*", then that column appears as "*\_\_id*" \(two underscores\) in the change table\.
+ If you choose Oracle as a source endpoint, then the Oracle source must have full supplemental logging enabled\. Otherwise, if there are columns at the source that weren't changed, then the data is loaded into Amazon DocumentDB as null values\.
+ The replication task setting, `TargetTablePrepMode:TRUNCATE_BEFORE_LOAD` isn't supported for use with a DocumentDB target endpoint\. 

## Target data types for Amazon DocumentDB<a name="CHAP_Target.DocumentDB.datatypes"></a>

In the following table, you can find the Amazon DocumentDB target data types that are supported when using AWS DMS, and the default mapping from AWS DMS data types\. For more information about AWS DMS data types, see [Data types for AWS Database Migration Service](CHAP_Reference.DataTypes.md)\.


|  AWS DMS data type  |  Amazon DocumentDB data type  | 
| --- | --- | 
|  BOOLEAN  |  Boolean  | 
|  BYTES  |  Binary data  | 
|  DATE  | Date | 
|  TIME  | String \(UTF8\) | 
|  DATETIME  | Date | 
|  INT1  | 32\-bit integer | 
|  INT2  |  32\-bit integer  | 
|  INT4  | 32\-bit integer | 
|  INT8  |  64\-bit integer  | 
|  NUMERIC  | String \(UTF8\) | 
|  REAL4  |  Double  | 
|  REAL8  | Double | 
|  STRING  |  If the data is recognized as JSON, then AWS DMS migrates it to Amazon DocumentDB as a document\. Otherwise, the data is mapped to String \(UTF8\)\.  | 
|  UINT1  | 32\-bit integer | 
|  UINT2  | 32\-bit integer | 
|  UINT4  | 64\-bit integer | 
|  UINT8  |  String \(UTF8\)  | 
|  WSTRING  | If the data is recognized as JSON, then AWS DMS migrates it to Amazon DocumentDB as a document\. Otherwise, the data is mapped to String \(UTF8\)\. | 
|  BLOB  | Binary | 
|  CLOB  | If the data is recognized as JSON, then AWS DMS migrates it to Amazon DocumentDB as a document\. Otherwise, the data is mapped to String \(UTF8\)\. | 
|  NCLOB  | If the data is recognized as JSON, then AWS DMS migrates it to Amazon DocumentDB as a document\. Otherwise, the data is mapped to String \(UTF8\)\. | 