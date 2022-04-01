# Using Amazon S3 as a source for AWS DMS<a name="CHAP_Source.S3"></a>

You can migrate data from an Amazon S3 bucket using AWS DMS\. To do this, provide access to an Amazon S3 bucket containing one or more data files\. In that S3 bucket, include a JSON file that describes the mapping between the data and the database tables of the data in those files\.

The source data files must be present in the Amazon S3 bucket before the full load starts\. You specify the bucket name using the `bucketName` parameter\. 

The source data files must be in comma\-separated value \(\.csv\) format\. Name them using the following naming convention\. In this convention, *`schemaName`* is the source schema and *`tableName`* is the name of a table within that schema\.

```
/schemaName/tableName/LOAD001.csv
/schemaName/tableName/LOAD002.csv
/schemaName/tableName/LOAD003.csv
...
```

 For example, suppose that your data files are in `mybucket`, at the following Amazon S3 path\.

```
s3://mybucket/hr/employee
```

At load time, AWS DMS assumes that the source schema name is `hr`, and that the source table name is `employee`\.

In addition to `bucketName` \(which is required\), you can optionally provide a `bucketFolder` parameter to specify where AWS DMS should look for data files in the Amazon S3 bucket\. Continuing the previous example, if you set `bucketFolder` to `sourcedata`, then AWS DMS reads the data files at the following path\.

```
s3://mybucket/sourcedata/hr/employee
```

You can specify the column delimiter, row delimiter, null value indicator, and other parameters using extra connection attributes\. For more information, see [Extra connection attributes for Amazon S3 as a source for AWS DMS](#CHAP_Source.S3.Configuring)\.

**Topics**
+ [Defining external tables for Amazon S3 as a source for AWS DMS](#CHAP_Source.S3.ExternalTableDef)
+ [Using CDC with Amazon S3 as a source for AWS DMS](#CHAP_Source.S3.CDC)
+ [Prerequisites when using Amazon S3 as a source for AWS DMS](#CHAP_Source.S3.Prerequisites)
+ [Limitations when using Amazon S3 as a source for AWS DMS](#CHAP_Source.S3.Limitations)
+ [Extra connection attributes for Amazon S3 as a source for AWS DMS](#CHAP_Source.S3.Configuring)
+ [Source data types for Amazon S3](#CHAP_Source.S3.DataTypes)

## Defining external tables for Amazon S3 as a source for AWS DMS<a name="CHAP_Source.S3.ExternalTableDef"></a>

In addition to the data files, you must also provide an external table definition\. An *external table definition* is a JSON document that describes how AWS DMS should interpret the data from Amazon S3\. The maximum size of this document is 2 MB\. If you create a source endpoint using the AWS DMS Management Console, you can enter the JSON directly into the table\-mapping box\. If you use the AWS Command Line Interface \(AWS CLI\) or AWS DMS API to perform migrations, you can create a JSON file to specify the external table definition\.

Suppose that you have a data file that includes the following\.

```
101,Smith,Bob,2014-06-04,New York
102,Smith,Bob,2015-10-08,Los Angeles
103,Smith,Bob,2017-03-13,Dallas
104,Smith,Bob,2017-03-13,Dallas
```

Following is an example external table definition for this data\.

```
{
    "TableCount": "1",
    "Tables": [
        {
            "TableName": "employee",
            "TablePath": "hr/employee/",
            "TableOwner": "hr",
            "TableColumns": [
                {
                    "ColumnName": "Id",
                    "ColumnType": "INT8",
                    "ColumnNullable": "false",
                    "ColumnIsPk": "true"
                },
                {
                    "ColumnName": "LastName",
                    "ColumnType": "STRING",
                    "ColumnLength": "20"
                },
                {
                    "ColumnName": "FirstName",
                    "ColumnType": "STRING",
                    "ColumnLength": "30"
                },
                {
                    "ColumnName": "HireDate",
                    "ColumnType": "DATETIME"
                },
                {
                    "ColumnName": "OfficeLocation",
                    "ColumnType": "STRING",
                    "ColumnLength": "20"
                }
            ],
            "TableColumnsTotal": "5"
        }
    ]
}
```

The elements in this JSON document are as follows:

`TableCount` – the number of source tables\. In this example, there is only one table\.

`Tables` – an array consisting of one JSON map per source table\. In this example, there is only one map\. Each map consists of the following elements:
+ `TableName` – the name of the source table\.
+ `TablePath` – the path in your Amazon S3 bucket where AWS DMS can find the full data load file\. If a `bucketFolder` value is specified, its value is prepended to the path\.
+ `TableOwner` – the schema name for this table\.
+ `TableColumns` – an array of one or more maps, each of which describes a column in the source table:
  + `ColumnName` – the name of a column in the source table\.
  + `ColumnType` – the data type for the column\. For valid data types, see [Source data types for Amazon S3](#CHAP_Source.S3.DataTypes)\.
  + `ColumnLength` – the number of bytes in this column\. Maximum column length is limited to2147483647 Bytes \(2,047 MegaBytes\) since an S3 source doesn't support FULL LOB mode\. `ColumnLength` is valid for the following data types:
    + BYTE
    + STRING
  + `ColumnNullable` – a Boolean value that is `true` if this column can contain NULL values \(default=`false`\)\.
  + `ColumnIsPk` – a Boolean value that is `true` if this column is part of the primary key \(default=`false`\)\.
+ `TableColumnsTotal` – the total number of columns\. This number must match the number of elements in the `TableColumns` array\.

If you don't specify otherwise, AWS DMS assumes that `ColumnLength` is zero\.

**Note**  
In supported versions of AWS DMS, the S3 source data can also contain an optional operation column as the first column before the `TableName` column value\. This operation column identifies the operation \(`INSERT`\) used to migrate the data to an S3 target endpoint during a full load\.   
If present, the value of this column is the initial character of the `INSERT` operation keyword \(`I`\)\. If specified, this column generally indicates that the S3 source was created by DMS as an S3 target during a previous migration\.   
In DMS versions prior to 3\.4\.2, this column wasn't present in S3 source data created from a previous DMS full load\. Adding this column to S3 target data allows the format of all rows written to the S3 target to be consistent whether they are written during a full load or during a CDC load\. For more information on the options for formatting S3 target data, see [Indicating source DB operations in migrated S3 data](CHAP_Target.S3.md#CHAP_Target.S3.Configuring.InsertOps)\.

For a column of the NUMERIC type, specify the precision and scale\. *Precision* is the total number of digits in a number, and *scale* is the number of digits to the right of the decimal point\. You use the `ColumnPrecision` and `ColumnScale` elements for this, as shown following\.

```
...
    {
        "ColumnName": "HourlyRate",
        "ColumnType": "NUMERIC",
        "ColumnPrecision": "5"
        "ColumnScale": "2"
    }
...
```

For a column of the DATETIME type with data that contains fractional seconds, specify the scale\. *Scale* is the number of digits for the fractional seconds, and can range from 0 to 9\. You use the `ColumnScale` element for this, as shown following\.

```
...
{
      "ColumnName": "HireDate",
      "ColumnType": "DATETIME",
      "ColumnScale": "3"
}
...
```

If you don't specify otherwise, AWS DMS assumes `ColumnScale` is zero and truncates the fractional seconds\.

## Using CDC with Amazon S3 as a source for AWS DMS<a name="CHAP_Source.S3.CDC"></a>

After AWS DMS performs a full data load, it can optionally replicate data changes to the target endpoint\. To do this, you upload change data capture files \(CDC files\) to your Amazon S3 bucket\. AWS DMS reads these CDC files when you upload them, and then applies the changes at the target endpoint\. 

The CDC files are named as follows:

```
CDC00001.csv
CDC00002.csv
CDC00003.csv
...
```

**Note**  
To replicate CDC files in the change data folder successfully upload them in a lexical \(sequential\) order\. For example, upload the file CDC00002\.csv before the file CDC00003\.csv\. Otherwise, CDC00002\.csv is skipped and isn't replicated if you load it after CDC00003\.csv\. But the file CDC00004\.csv replicates successfully if loaded after CDC00003\.csv\.

To indicate where AWS DMS can find the files, specify the `cdcPath` parameter\. Continuing the previous example, if you set `cdcPath` to `changedata`, then AWS DMS reads the CDC files at the following path\.

```
s3://mybucket/changedata
```

If you set `cdcPath` to `changedata` and `bucketFolder` to `myFolder`, then AWS DMS reads the CDC files at the following path\.

```
s3://mybucket/myFolder/changedata
```

The records in a CDC file are formatted as follows:
+ Operation – the change operation to be performed: `INSERT` or `I`, `UPDATE` or `U`, or `DELETE` or `D`\. These keyword and character values are case\-insensitive\.
**Note**  
In supported AWS DMS versions, AWS DMS can identify the operation to perform for each load record in two ways\. AWS DMS can do this from the record's keyword value \(for example, `INSERT`\) or from its keyword initial character \(for example, `I`\)\. In prior versions, AWS DMS recognized the load operation only from the full keyword value\.   
In prior versions of AWS DMS, the full keyword value was written to log the CDC data\. Also, prior versions wrote the operation value to any S3 target using only the keyword initial\.   
Recognizing both formats allows AWS DMS to handle the operation regardless of how the operation column is written to create the S3 source data\. This approach supports using S3 target data as the source for a later migration\. With this approach, you don't need to change the format of any keyword initial value that appears in the operation column of the later S3 source\.
+ Table name – the name of the source table\.
+ Schema name – the name of the source schema\.
+ Data – one or more columns that represent the data to be changed\.

Following is an example CDC file for a table named `employee`\.

```
INSERT,employee,hr,101,Smith,Bob,2014-06-04,New York
UPDATE,employee,hr,101,Smith,Bob,2015-10-08,Los Angeles
UPDATE,employee,hr,101,Smith,Bob,2017-03-13,Dallas
DELETE,employee,hr,101,Smith,Bob,2017-03-13,Dallas
```

## Prerequisites when using Amazon S3 as a source for AWS DMS<a name="CHAP_Source.S3.Prerequisites"></a>

To use Amazon S3 as a source for AWS DMS, your source S3 bucket must be in the same AWS Region as the DMS replication instance that migrates your data\. In addition, the AWS account you use for the migration must have read access to the source bucket\. 

The AWS Identity and Access Management \(IAM\) role assigned to the user account used to create the migration task must have the following set of permissions\.

```
{
    "Version": "2012-10-17",
    "Statement": [
       {
            "Effect": "Allow",
            "Action": [
                "s3:GetObject"
            ],
            "Resource": [
                "arn:aws:s3:::mybucket*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws:s3:::mybucket*"
            ]
        }
    ]
}
```

## Limitations when using Amazon S3 as a source for AWS DMS<a name="CHAP_Source.S3.Limitations"></a>

The following limitations apply when using Amazon S3 as a source:
+ Don’t enable versioning for S3\. If you need S3 versioning, use lifecycle policies to actively delete old versions\. Otherwise, you might encounter endpoint test connection failures because of an S3 `list-object` call timeout\. To create a lifecycle policy for an S3 bucket, see [ Managing your storage lifecycle](https://docs.aws.amazon.com/AmazonS3/latest/userguide/object-lifecycle-mgmt.html)\. To delete a version of an S3 object, see [ Deleting object versions from a versioning\-enabled bucket](https://docs.aws.amazon.com/AmazonS3/latest/dev/DeletingObjectVersions.html)\.
+ A VPCE\-enabled \(gateway VPC\) S3 bucket isn't currently supported\.

## Extra connection attributes for Amazon S3 as a source for AWS DMS<a name="CHAP_Source.S3.Configuring"></a>

You can specify the following options as extra connection attributes\.


| **Option** | **Description** | 
| --- | --- | 
| bucketFolder |  \(Optional\) A folder name in the S3 bucket\. If this attribute is provided, source data files and CDC files are read from the path `s3://myBucket/bucketFolder/schemaName/tableName/` and `s3://myBucket/bucketFolder/` respectively\. If this attribute isn't specified, then the path used is `schemaName/tableName/`\. An example follows\. `bucketFolder=sourceData;`  | 
| bucketName |  The name of the S3 bucket\. An example follows\. `bucketName=myBucket;`  | 
| cdcPath | The location of CDC files\. This attribute is required if a task captures change data; otherwise, it's optional\. If cdcPath is present, then AWS DMS reads CDC files from this path and replicates the data changes to the target endpoint\. For more information, see [Using CDC with Amazon S3 as a source for AWS DMS](#CHAP_Source.S3.CDC)\. An example follows\.`cdcPath=changeData;` | 
| csvDelimiter |  The delimiter used to separate columns in the source files\. The default is a comma\. An example follows\. `csvDelimiter=,;`  | 
| csvRowDelimiter |  The delimiter used to separate rows in the source files\. The default is a newline \(`\n`\)\. An example follows\. `csvRowDelimiter=\n;`  | 
| ignoreHeaderRows |  When this value is set to 1, AWS DMS ignores the first row header in a \.csv file\. A value of 1 enables the feature, a value of 0 disables the feature\. The default is 0\. Example: `ignoreHeaderRows=1;`  | 
| rfc4180 |  When this value is set to `true` or `y`, each leading double quotation mark has to be followed by an ending double quotation mark\. This formatting complies with RFC 4180\. When this value is set to `false` or `n`, string literals are copied to the target as is\. In this case, a delimiter \(row or column\) signals the end of the field\. Thus, you can't use a delimiter as part of the string, because it signals the end of the value\. The default is `true`\. Valid values: `true`, `false`, `y`, `n` Example: `rfc4180=false;`  | 

## Source data types for Amazon S3<a name="CHAP_Source.S3.DataTypes"></a>

Data migration that uses Amazon S3 as a source for AWS DMS needs to map data from Amazon S3 to AWS DMS data types\. For more information, see [Defining external tables for Amazon S3 as a source for AWS DMS](#CHAP_Source.S3.ExternalTableDef)\.

For information on how to view the data type that is mapped in the target, see the section for the target endpoint you are using\.

For additional information about AWS DMS data types, see [Data types for AWS Database Migration Service](CHAP_Reference.DataTypes.md)\.

The following AWS DMS data types are used with Amazon S3 as a source:
+ BYTE – Requires `ColumnLength`\. For more information, see [Defining external tables for Amazon S3 as a source for AWS DMS](#CHAP_Source.S3.ExternalTableDef)\.
+ DATE
+ TIME
+ DATETIME – For more information and an example, see the DATETIME type example in [Defining external tables for Amazon S3 as a source for AWS DMS](#CHAP_Source.S3.ExternalTableDef)\.
+ INT1
+ INT2
+ INT4
+ INT8
+ NUMERIC – Requires `ColumnPrecision` and `ColumnScale`\. For more information and an example, see the NUMERIC type example in [Defining external tables for Amazon S3 as a source for AWS DMS](#CHAP_Source.S3.ExternalTableDef)\.
+ REAL4
+ REAL8
+ STRING – Requires `ColumnLength`\. For more information, see [Defining external tables for Amazon S3 as a source for AWS DMS](#CHAP_Source.S3.ExternalTableDef)\.
+ UINT1
+ UINT2
+ UINT4
+ UINT8
+ BLOB
+ CLOB
+ BOOLEAN