# Using Amazon S3 as a Source for AWS DMS<a name="CHAP_Source.S3"></a>

You can migrate data from an Amazon S3 bucket using AWS DMS\. To do this, provide access to an S3 bucket containing one or more data files\. In that S3 bucket, include a JSON file that describes the mapping between the data and the database tables of the data in those files\.

The source data files must be present in the S3 bucket before the full load starts\. You specify the bucket name using the `bucketName` parameter\. 

The source data files must be in comma separated value \(CSV\) format\. Name them using the naming convention shown following\. In this convention, *`schemaName`* is the source schema and *`tableName`* is the name of a table within that schema\.

```
/schemaName/tableName/LOAD001.csv
/schemaName/tableName/LOAD002.csv
/schemaName/tableName/LOAD003.csv
...
```

 For example, suppose that your data files are in `mybucket`, at the following S3 path\.

```
s3://mybucket/hr/employee
```

At load time, AWS DMS assumes that the source schema name is `hr`, and that the source table name is `employee`\.

In addition to `bucketName` \(which is required\), you can optionally provide a `bucketFolder` parameter to specify where AWS DMS should look for data files in the S3 bucket\. Continuing the previous example, if you set `bucketFolder` to *sourcedata*, then AWS DMS reads the data files at the following path\.

```
s3://mybucket/sourcedata/hr/employee
```

You can specify the column delimiter, row delimiter, null value indicator, and other parameters using extra connection attributes\. For more information, see [Extra Connection Attributes for S3 as a Source for AWS DMS](#CHAP_Source.S3.Configuring)\.

## Defining External Tables for S3 as a Source for AWS DMS<a name="CHAP_Source.S3.ExternalTableDef"></a>

In addition to the data files, you must also provide an external table definition\. An *external table definition* is a JSON document that describes how AWS DMS should interpret the data from S3\. The maximum size of this document is 2 MB\. If you create a source endpoint using the AWS DMS Management Console, you can enter the JSON directly into the table mapping box\. If you use the AWS Command Line Interface \(AWS CLI\) or AWS DMS API to perform migrations, you can create a JSON file to specify the external table definition\.

Suppose that you have a data file that includes the following\.

```
101,Smith,Bob,4-Jun-14,New York
102,Smith,Bob,8-Oct-15,Los Angeles
103,Smith,Bob,13-Mar-17,Dallas
104,Smith,Bob,13-Mar-17,Dallas
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

`TableCount`—the number of source tables\. In this example, there is only one table\.

`Tables`—an array consisting of one JSON map per source table\. In this example, there is only one map\. Each map consists of the following elements:
+ `TableName`—the name of the source table\.
+ `TablePath`—the path in your S3 bucket where AWS DMS can find the full data load file\. If a `bucketFolder` value is specified, this value is prepended to the path\.
+ `TableOwner`—the schema name for this table\.
+ `TableColumns`—an array of one or more maps, each of which describes a column in the source table:
  + `ColumnName`—the name of a column in the source table\.
  + `ColumnType`—the data type for the column\. For valid data types, see [Source Data Types for Amazon S3](#CHAP_Source.S3.DataTypes)\.
  + `ColumnLength`—the number of bytes in this column\.
  + `ColumnNullable`—\(optional\) a Boolean value that is `true` if this column can contain NULL values\. 
  + `ColumnIsPk`—\(optional\) a Boolean value that is `true` if this column is part of the primary key\. 
+ `TableColumnsTotal`—the total number of columns\. This number must match the number of elements in the `TableColumns` array\.

In the example preceding, some of the columns are of type STRING\. In this case, use the `ColumnLength` element to specify the maximum number of characters\.

`ColumnLength` applies for the following data types:
+ BYTE
+ STRING

If you don't specify otherwise, AWS DMS assumes that `ColumnLength` is zero\.

For a column of the NUMERIC type, you need to specify the precision and scale\. *Precision* is the total number of digits in a number, and *scale* is the number of digits to the right of the decimal point\. You use the `ColumnPrecision` and `ColumnScale` elements for this, as shown following\.

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

## Using CDC with S3 as a Source for AWS DMS<a name="CHAP_Source.S3.CDC"></a>

After AWS DMS performs a full data load, it can optionally replicate data changes to the target endpoint\. To do this, you upload change data capture files \(CDC files\) to your S3 bucket\. AWS DMS reads these CDC files when you upload them, and then applies the changes at the target endpoint\. 

The CDC files are named as follows:

```
CDC00001.csv
CDC00002.csv
CDC00003.csv
...
```

To indicate where AWS DMS can find the files, you must specify the `cdcPath` parameter\. Continuing the previous example, if you set `cdcPath` to *changedata*, then AWS DMS reads the CDC files at the following path\.

```
s3://mybucket/changedata
```

The records in a CDC file are formatted as follows:
+ Operation—the change operation to be performed: `INSERT`, `UPDATE`, or `DELETE`\. These keywords are case\-insensitive\.
+ Table name—the name of the source table\.
+ Schema name—the name of the source schema\.
+ Data—one or more columns that represent the data to be changed\.

Following is an example CDC file for a table named `employee`\.

```
INSERT,employee,hr,101,Smith,Bob,4-Jun-14,New York
UPDATE,employee,hr,101,Smith,Bob,8-Oct-15,Los Angeles
UPDATE,employee,hr,101,Smith,Bob,13-Mar-17,Dallas
DELETE,employee,hr,101,Smith,Bob,13-Mar-17,Dallas
```

## Prerequisites When Using S3 as a Source for AWS DMS<a name="CHAP_Source.S3.Prerequisites"></a>

When you use S3 as a source for AWS DMS, the source S3 bucket that you use must be in the same AWS Region as the AWS DMS replication instance that you use to migrate your data\. In addition, the AWS account you use for the migration must have read access to the source bucket\. 

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

## Extra Connection Attributes for S3 as a Source for AWS DMS<a name="CHAP_Source.S3.Configuring"></a>

You can specify the following options as extra connection attributes\.


| **Option** | **Description** | 
| --- | --- | 
| bucketFolder |  \(Optional\) A folder name in the S3 bucket\. If this attribute is provided, source data files and CDC files are read from the path `bucketFolder/schemaName/tableName/`\. If this attribute is not specified, then the path used is `schemaName/tableName/`\. An example follows\. `bucketFolder=testFolder;`  | 
| bucketName |  The name of the S3 bucket\. An example follows\. `bucketName=buckettest;`  | 
| cdcPath | The location of change data capture \(CDC\) files\. This attribute is required if a task captures change data; otherwise, it's optional\. If cdcPath is present, then AWS DMS reads CDC files from this path and replicate the data changes to the target endpoint\. For more information, see [Using CDC with S3 as a Source for AWS DMS](#CHAP_Source.S3.CDC)\. An example follows\.`cdcPath=dataChanges;` | 
| csvRowDelimiter |  The delimiter used to separate rows in the source files\. The default is a carriage return \(`\n`\)\. An example follows\. `csvRowDelimiter=\n;`  | 
| csvDelimiter |  The delimiter used to separate columns in the source files\. The default is a comma\. An example follows\. `csvDelimiter=,;`  | 
| externalTableDefinition |  A JSON object that describes how AWS DMS should interpret the data in the S3 bucket during the migration\. For more information, see [Defining External Tables for S3 as a Source for AWS DMS](#CHAP_Source.S3.ExternalTableDef)\. An example follows\. `externalTableDefinition=`*<json\_object>*  | 
| ignoreHeaderRows |  When set to 1, AWS DMS ignores the first row header in a CSV file\. A value of 1 enables the feature, a value of 0 disables the feature\. The default is 0\. `ignoreHeaderRows=1`  | 

## Source Data Types for Amazon S3<a name="CHAP_Source.S3.DataTypes"></a>

Data migration that uses Amazon S3 as a source for AWS DMS needs to map data from S3 to AWS DMS data types\. For more information, see [Defining External Tables for S3 as a Source for AWS DMS](#CHAP_Source.S3.ExternalTableDef)\.

For information on how to view the data type that is mapped in the target, see the section for the target endpoint you are using\.

For additional information about AWS DMS data types, see [Data Types for AWS Database Migration Service](CHAP_Reference.DataTypes.md)\.


|  AWS DMS Data Types—Amazon S3 as Source  | 
| --- | 
| BYTERequires `ColumnLength`\. For more information, see [Defining External Tables for S3 as a Source for AWS DMS](#CHAP_Source.S3.ExternalTableDef)\. | 
| DATE | 
| TIME | 
| DATETIME | 
| TIMESTAMP | 
| INT1 | 
| INT2 | 
| INT4 | 
| INT8 | 
| NUMERIC Requires `ColumnPrecision` and `ColumnScale`\. For more information, see [Defining External Tables for S3 as a Source for AWS DMS](#CHAP_Source.S3.ExternalTableDef)\. | 
| REAL4 | 
| REAL8 | 
| STRINGRequires `ColumnLength`\. For more information, see [Defining External Tables for S3 as a Source for AWS DMS](#CHAP_Source.S3.ExternalTableDef)\. | 
| UINT1 | 
| UINT2 | 
| UINT4 | 
| UINT8 | 
| BLOB | 
| CLOB | 
| BOOLEAN | 