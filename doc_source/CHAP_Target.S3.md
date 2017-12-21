# Using Amazon S3 as a Target for AWS Database Migration Service<a name="CHAP_Target.S3"></a>

You can migrate data to Amazon S3 using AWS DMS from any of the supported database sources\. When using S3 as a target in an AWS DMS task, both full load and change data capture \(CDC\) data is written to comma\-separated\-values \(CSV\) format\. AWS DMS names files created during a full load using incremental counters, for example LOAD00001\.csv, LOAD00002, and so on\. AWS DMS names CDC files using timestamps, for example 20141029\-1134010000\.csv\. For each source table, AWS DMS creates a folder under the specified target folder\. AWS DMS writes all full load and CDC files to the specified S3 bucket\.

The parameter `bucketFolder` contains the location where the \.csv files are stored before being uploaded to the S3 bucket\. Table data is stored in the following format in the S3 bucket:

```
<schema_name>/<table_name>/LOAD001.csv
<schema_name>/<table_name>/LOAD002.csv
<schema_name>/<table_name>/<time-stamp>.csv
```

You can specify the column delimiter, row delimiter, null value indicator, and other parameters using the extra connection attributes\. For more information on the extra connection attributes, see [Extra Connection Attributes](#CHAP_Target.S3.Configuring) at the end of this section\.

When you use AWS DMS to replicate data changes, the first column of the CSV output file indicates how the data was changed, as shown following:

```
I,101,Smith,Bob,4-Jun-14,New York
U,101,Smith,Bob,8-Oct-15,Los Angeles
U,101,Smith,Bob,13-Mar-17,Dallas
D,101,Smith,Bob,13-Mar-17,Dallas
```

For this example, suppose that there is an `EMPLOYEE` table in the source database\. AWS DMS writes data to the CSV file, in response to the following events:

+ A new employee \(Bob Smith, employee ID 101\) is hired on 4\-Jun\-14 at the New York office\. In the CSV file, the `I` in the first column indicates that a new row was `INSERT`ed into the EMPLOYEE table at the source database\.

+ On 8\-Oct\-15, Bob transfers to the Los Angeles office\. In the CSV file, the `U` indicates that the corresponding row in the EMPLOYEE table was `UPDATE`d to reflect Bob's new office location\. The rest of the line reflects the row in the EMPLOYEE table as it appears after the `UPDATE`\. 

+ On 13\-Mar,17, Bob transfers again to the Dallas office\. In the CSV file, the `U` indicates that this row was `UPDATE`d again\. The rest of the line reflects the row in the EMPLOYEE table as it appears after the `UPDATE`\.

+ After some time working in Dallas, Bob leaves the company\. In the CSV file, the `D` indicates that the row was `DELETE`d in the source table\. The rest of the line reflects how the row in the EMPLOYEE table appeared before it was deleted\.

## Prerequisites for Using Amazon S3 as a Target<a name="CHAP_Target.S3.Prerequisites"></a>

The Amazon S3 bucket you are using as a target must be in the same region as the DMS replication instance you are using the migrate your data\. 

The AWS account you use for the migration must have write and delete access to the Amazon S3 bucket you are using as a target\. The role assigned to the user account used to create the migration task must have the following set of permissions\.

```
{
    "Version": "2012-10-17",
    "Statement": [
       {
            "Effect": "Allow",
            "Action": [
                "s3:PutObject",
                "s3:DeleteObject"
            ],
            "Resource": [
                "arn:aws:s3:::buckettest2*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws:s3:::buckettest2*"
            ]
        }
    ]
}
```

## Limitations to Using Amazon S3 as a Target<a name="CHAP_Target.S3.Limitations"></a>

The following limitations apply to a file in Amazon S3 that you are using as a target:

+ Only the following data definition language \(DDL\) commands are supported: TRUNCATE TABLE, DROP TABLE, and CREATE TABLE\.

+ Full LOB mode is not supported\.

+ Changes to the source table structure during full load are not supported\. Changes to the data is supported during full load\.

## Security<a name="CHAP_Target.S3.Security"></a>

To use Amazon S3 as a target, the account used for the migration must have write and delete access to the Amazon S3 bucket that is used as the target\. You must specify the Amazon Resource Name \(ARN\) of an IAM role that has the permissions required to access Amazon S3\. 

AWS DMS supports a set of predefined grants for Amazon S3, known as canned ACLs\. Each canned ACL has a set of grantees and permissions you can use to set permissions for the Amazon S3 bucket\. You can specify a canned ACL using the `cannedAclForObjects` on the connection string attribute for your S3 target endpoint\. For more information about using the extra connection attribute `cannedAclForObjects`, see [Extra Connection Attributes](#CHAP_Target.S3.Configuring) for more information\. For more information about Amazon S3 canned ACLs, see [Canned ACL](http://docs.aws.amazon.com/AmazonS3/latest/dev/acl-overview.html#canned-acl)\.

The IAM role that you use for the migration must be able to perform the `s3:PutObjectAcl` API action\.

## Extra Connection Attributes<a name="CHAP_Target.S3.Configuring"></a>

You can specify the following options as extra connection attributes\.


| **Option** | **Description** | 
| --- | --- | 
| bucketFolder |  An optional parameter to set a folder name in the S3 bucket\. If provided, tables are created in the path <bucketFolder>/<schema\_name>/<table\_name>/\. If this parameter is not specified, then the path used is <schema\_name>/<table\_name>/\.  **Example:** `bucketFolder=testFolder;`  | 
| bucketName |  The name of the S3 bucket\. **Example:** `bucketName=buckettest;`  | 
| cannedAclForObjects |  Allows AWS DMS to specify a predefined \(canned\) access control list for objects written to the S3 bucket\. For more information about Amazon S3 canned ACLs, see [Canned ACL](http://docs.aws.amazon.com/AmazonS3/latest/dev/acl-overview.html#canned-acl) in the Amazon S3 Developer Guide\. **Example:** `cannedAclForObjects=PUBLIC_READ;` Valid values for this attribute are: NONE; PRIVATE; PUBLIC\_READ; PUBLIC\_READ\_WRITE; AUTHENTICATED\_READ; AWS\_EXEC\_READ; BUCKET\_OWNER\_READ; BUCKET\_OWNER\_FULL\_CONTROL\. If this attribute isn't specified, it defaults to NONE\.  | 
| cdcInsertsOnly |  An optional parameter to write only `INSERT` operations to the \.CSV output files\. By default, the first field in a \.CSV record contains the letter `I` \(insert\), `U` \(update\) or `D` \(delete\) to indicate whether the row was inserted, updated, or deleted at the source database\. If `cdcInsertsOnly` is set to `true`, then only `INSERT`s are recorded in the CSV file, without any `I` annotation\. **Example:** `cdcInsertsOnly=true;`  | 
| compressionType |  An optional parameter to use GZIP to compress the target files\. Set to NONE \(the default\) or do not use to leave the files uncompressed\. **Example:** `compressionType=GZIP;`  | 
| csvRowDelimiter |  The delimiter used to separate rows in the source files\. The default is a carriage return \(`\n`\)\. **Example:** `csvRowDelimiter=\n;`  | 
| csvDelimiter |  The delimiter used to separate columns in the source files\. The default is a comma\. **Example:** `csvDelimiter=,;`  | 