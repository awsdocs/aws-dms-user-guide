# Using Amazon S3 as a target for AWS Database Migration Service<a name="CHAP_Target.S3"></a>

You can migrate data to Amazon S3 using AWS DMS from any of the supported database sources\. When using Amazon S3 as a target in an AWS DMS task, both full load and change data capture \(CDC\) data is written to comma\-separated value \(\.csv\) format by default\. For more compact storage and faster query options, you also have the option to have the data written to Apache Parquet \(\.parquet\) format\. 

AWS DMS names files created during a full load using an incremental hexadecimal counter—for example LOAD00001\.csv, LOAD00002\.\.\., LOAD00009, LOAD0000A, and so on for \.csv files\. AWS DMS names CDC files using timestamps, for example 20141029\-1134010000\.csv\. For each source table that contains records, AWS DMS creates a folder under the specified target folder \(if the source table is not empty\)\. AWS DMS writes all full load and CDC files to the specified Amazon S3 bucket\.

The parameter `bucketFolder` contains the location where the \.csv or \.parquet files are stored before being uploaded to the S3 bucket\. With \.csv files, table data is stored in the following format in the S3 bucket, shown with full\-load files\.

```
database_schema_name/table_name/LOAD00000001.csv
database_schema_name/table_name/LOAD00000002.csv
...
database_schema_name/table_name/LOAD00000009.csv
database_schema_name/table_name/LOAD0000000A.csv
database_schema_name/table_name/LOAD0000000B.csv
...database_schema_name/table_name/LOAD0000000F.csv
database_schema_name/table_name/LOAD00000010.csv
...
```

You can specify the column delimiter, row delimiter, and other parameters using the extra connection attributes\. For more information on the extra connection attributes, see [Extra connection attributes when using Amazon S3 as a target for AWS DMS](#CHAP_Target.S3.Configuring) at the end of this section\.

When you use AWS DMS to replicate data changes using a CDC task, the first column of the \.csv or \.parquet output file indicates how the row data was changed as shown for the following \.csv file\.

```
I,101,Smith,Bob,4-Jun-14,New York
U,101,Smith,Bob,8-Oct-15,Los Angeles
U,101,Smith,Bob,13-Mar-17,Dallas
D,101,Smith,Bob,13-Mar-17,Dallas
```

For this example, suppose that there is an `EMPLOYEE` table in the source database\. AWS DMS writes data to the \.csv or \.parquet file, in response to the following events:
+ A new employee \(Bob Smith, employee ID 101\) is hired on 4\-Jun\-14 at the New York office\. In the \.csv or \.parquet file, the `I` in the first column indicates that a new row was `INSERT`ed into the EMPLOYEE table at the source database\.
+ On 8\-Oct\-15, Bob transfers to the Los Angeles office\. In the \.csv or \.parquet file, the `U` indicates that the corresponding row in the EMPLOYEE table was `UPDATE`d to reflect Bob's new office location\. The rest of the line reflects the row in the EMPLOYEE table as it appears after the `UPDATE`\. 
+ On 13\-Mar,17, Bob transfers again to the Dallas office\. In the \.csv or \.parquet file, the `U` indicates that this row was `UPDATE`d again\. The rest of the line reflects the row in the EMPLOYEE table as it appears after the `UPDATE`\.
+ After some time working in Dallas, Bob leaves the company\. In the \.csv or \.parquet file, the `D` indicates that the row was `DELETE`d in the source table\. The rest of the line reflects how the row in the EMPLOYEE table appeared before it was deleted\.

Note that by default for CDC, AWS DMS stores the row changes for each database table without regard to transaction order\. If you want to store the row changes in CDC files according to transaction order, you need to use S3 endpoint settings to specify this and the folder path where you want the CDC transaction files to be stored on the S3 target\. For more information, see [Capturing data changes \(CDC\) including transaction order on the S3 target](#CHAP_Target.S3.EndpointSettings.CdcPath)\.

To control the frequency of writes to an Amazon S3 target during a data replication task, you can configure the `cdcMaxBatchInterval` and `cdcMinFileSize` extra connection attributes\. This can result in better performance when analyzing the data without any additional overhead operations\. For more information, see [Extra connection attributes when using Amazon S3 as a target for AWS DMS](#CHAP_Target.S3.Configuring) 

**Topics**
+ [Prerequisites for using Amazon S3 as a target](#CHAP_Target.S3.Prerequisites)
+ [Limitations to using Amazon S3 as a target](#CHAP_Target.S3.Limitations)
+ [Security](#CHAP_Target.S3.Security)
+ [Using Apache Parquet to store Amazon S3 objects](#CHAP_Target.S3.Parquet)
+ [Amazon S3 object tagging](#CHAP_Target.S3.Tagging)
+ [Creating AWS KMS keys to encrypt Amazon S3 target objects](#CHAP_Target.S3.KMSKeys)
+ [Using date\-based folder partitioning](#CHAP_Target.S3.DatePartitioning)
+ [Parallel load of partitioned sources when using Amazon S3 as a target for AWS DMS](#CHAP_Target.S3.ParallelLoad)
+ [Endpoint settings when using Amazon S3 as a target for AWS DMS](#CHAP_Target.S3.EndpointSettings)
+ [Extra connection attributes when using Amazon S3 as a target for AWS DMS](#CHAP_Target.S3.Configuring)
+ [Indicating source DB operations in migrated S3 data](#CHAP_Target.S3.Configuring.InsertOps)
+ [Target data types for S3 Parquet](#CHAP_Target.S3.DataTypes)

## Prerequisites for using Amazon S3 as a target<a name="CHAP_Target.S3.Prerequisites"></a>

Before using Amazon S3 as a target, check that the following are true: 
+ The S3 bucket that you're using as a target is in the same AWS Region as the DMS replication instance you are using to migrate your data\.
+ The AWS account that you use for the migration has an IAM role with write and delete access to the S3 bucket you are using as a target\.
+ This role has tagging access so you can tag any S3 objects written to the target bucket\.
+ The IAM role has DMS \(dms\.amazonaws\.com\) added as *trusted entity*\. 

To set up this account access, ensure that the role assigned to the user account used to create the migration task has the following set of permissions\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:PutObject",
                "s3:DeleteObject",
                "s3:PutObjectTagging"
            ],
            "Resource": [
                "arn:aws:s3:::buckettest2/*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws:s3:::buckettest2"
            ]
        }
    ]
}
```

## Limitations to using Amazon S3 as a target<a name="CHAP_Target.S3.Limitations"></a>

The following limitations apply when using Amazon S3 as a target:
+ A VPCE\-enabled \(gateway VPC\) S3 bucket isn't currently supported\.
+ The following data definition language \(DDL\) commands are supported for change data capture \(CDC\): Truncate Table, Drop Table, Create Table, Rename Table, Add Column, Drop Column, Rename Column, and Change Column Data Type\.
**Note**  
A truncate DDL operation removes all files and corresponding table folders from an S3 bucket\. You can use task settings to disable that behavior and configure the way DMS handles DDL behavior during change data capture \(CDC\)\. For more information, see [Task settings for change processing DDL handling](CHAP_Tasks.CustomizingTasks.TaskSettings.DDLHandling.md)\.
+ Full LOB mode is not supported\.
+ Changes to the source table structure during full load are not supported\. Changes to data are supported during full load\.
+ Multiple tasks that replicate data from the same source table to the same target S3 endpoint bucket result in those tasks writing to the same file\. We recommend that you specify different target endpoints \(buckets\) if your data source is from the same table\.
+ `BatchApply` is not supported for an S3 endpoint\. Using Batch Apply \(for example, the `BatchApplyEnabled` target metadata task setting\) for an S3 target might result in loss of data\.
+ You can't use `datePartitionedEnabled` or `addColumnName` together with `PreserveTransactions` or `CdcPath`\.

## Security<a name="CHAP_Target.S3.Security"></a>

To use Amazon S3 as a target, the account used for the migration must have write and delete access to the Amazon S3 bucket that is used as the target\. Specify the Amazon Resource Name \(ARN\) of an IAM role that has the permissions required to access Amazon S3\. 

AWS DMS supports a set of predefined grants for Amazon S3, known as canned access control lists \(ACLs\)\. Each canned ACL has a set of grantees and permissions that you can use to set permissions for the Amazon S3 bucket\. You can specify a canned ACL using the `cannedAclForObjects` on the connection string attribute for your S3 target endpoint\. For more information about using the extra connection attribute `cannedAclForObjects`, see [Extra connection attributes when using Amazon S3 as a target for AWS DMS](#CHAP_Target.S3.Configuring)\. For more information about Amazon S3 canned ACLs, see [Canned ACL](http://docs.aws.amazon.com/AmazonS3/latest/dev/acl-overview.html#canned-acl)\.

The IAM role that you use for the migration must be able to perform the `s3:PutObjectAcl` API operation\.

## Using Apache Parquet to store Amazon S3 objects<a name="CHAP_Target.S3.Parquet"></a>

The comma\-separated value \(\.csv\) format is the default storage format for Amazon S3 target objects\. For more compact storage and faster queries, you can instead use Apache Parquet \(\.parquet\) as the storage format\.

Apache Parquet is an open\-source file storage format originally designed for Hadoop\. For more information on Apache Parquet, see [https://parquet.apache.org/](https://parquet.apache.org/)\.

To set \.parquet as the storage format for your migrated S3 target objects, you can use the following mechanisms:
+ Endpoint settings that you provide as parameters of a JSON object when you create the endpoint using the AWS CLI or the API for AWS DMS\. For more information, see [Endpoint settings when using Amazon S3 as a target for AWS DMS](#CHAP_Target.S3.EndpointSettings)\.
+ Extra connection attributes that you provide as a semicolon\-separated list when you create the endpoint\. For more information, see [Extra connection attributes when using Amazon S3 as a target for AWS DMS](#CHAP_Target.S3.Configuring)\.

## Amazon S3 object tagging<a name="CHAP_Target.S3.Tagging"></a>

You can tag Amazon S3 objects that a replication instance creates by specifying appropriate JSON objects as part of task\-table mapping rules\. For more information about requirements and options for S3 object tagging, including valid tag names, see [Object tagging](https://docs.aws.amazon.com/AmazonS3/latest/dev/object-tagging.html) in the *Amazon Simple Storage Service Developer Guide*\. For more information about table mapping using JSON, see [ Specifying table selection and transformations rules using JSON](CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.md)\.

You tag S3 objects created for specified tables and schemas by using one or more JSON objects of the `selection` rule type\. You then follow this `selection` object \(or objects\) by one or more JSON objects of the `post-processing` rule type with `add-tag` action\. These post\-processing rules identify the S3 objects that you want to tag and specify the names and values of the tags that you want to add to these S3 objects\.

You can find the parameters to specify in JSON objects of the `post-processing` rule type in the following table\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Target.S3.html)

When you specify multiple `post-processing` rule types to tag a selection of S3 objects, each S3 object is tagged using only one `tag-set` object from one post\-processing rule\. The particular tag set used to tag a given S3 object is the one from the post\-processing rule whose associated object locator best matches that S3 object\. 

For example, suppose that two post\-processing rules identify the same S3 object\. Suppose also that the object locator from one rule uses wildcards and the object locator from the other rule uses an exact match to identify the S3 object \(without wildcards\)\. In this case, the tag set associated with the post\-processing rule with the exact match is used to tag the S3 object\. If multiple post\-processing rules match a given S3 object equally well, the tag set associated with the first such post\-processing rule is used to tag the object\.

**Example Adding static tags to an S3 object created for a single table and schema**  
The following selection and post\-processing rules add three tags \(`tag_1`, `tag_2`, and `tag_3` with corresponding static values `value_1`, `value_2`, and `value_3`\) to a created S3 object\. This S3 object corresponds to a single table in the source named `STOCK` with a schema named `aat2`\.  

```
{
    "rules": [
        {
            "rule-type": "selection",
            "rule-id": "5",
            "rule-name": "5",
            "object-locator": {
                "schema-name": "aat2",
                "table-name": "STOCK"
            },
            "rule-action": "include"
        },
        {
            "rule-type": "post-processing",
            "rule-id": "41",
            "rule-name": "41",
            "rule-action": "add-tag",
            "object-locator": {
                "schema-name": "aat2",
                "table-name": "STOCK"
            },
            "tag-set": [
              {
                "key": "tag_1",
                "value": "value_1"
              },
              {
                "key": "tag_2",
                "value": "value_2"
              },
              {
                "key": "tag_3",
                "value": "value_3"
              }                                     
           ]
        }
    ]
}
```

**Example Adding static and dynamic tags to S3 objects created for multiple tables and schemas**  
The following example has one selection and two post\-processing rules, where input from the source includes all tables and all of their schemas\.  

```
{
    "rules": [
        {
            "rule-type": "selection",
            "rule-id": "1",
            "rule-name": "1",
            "object-locator": {
                "schema-name": "%",
                "table-name": "%"
            },
            "rule-action": "include"
        },
        {
            "rule-type": "post-processing",
            "rule-id": "21",
            "rule-name": "21",
            "rule-action": "add-tag",
            "object-locator": {
                "schema-name": "%",
                "table-name": "%",
            },
            "tag-set": [
              { 
                "key": "dw-schema-name",
                "value":"${schema-name}"
              },
              {
                "key": "dw-schema-table",
                "value": "my_prefix_${table-name}"
              }
            ]
        },
        {
            "rule-type": "post-processing",
            "rule-id": "41",
            "rule-name": "41",
            "rule-action": "add-tag",
            "object-locator": {
                "schema-name": "aat",
                "table-name": "ITEM",
            },
            "tag-set": [
              {
                "key": "tag_1",
                "value": "value_1"
              },
              {
                "key": "tag_2",
                "value": "value_2"
              }           ]
        }
    ]
}
```
The first post\-processing rule adds two tags \(`dw-schema-name` and `dw-schema-table`\) with corresponding dynamic values \(`${schema-name}` and `my_prefix_${table-name}`\) to almost all S3 objects created in the target\. The exception is the S3 object identified and tagged with the second post\-processing rule\. Thus, each target S3 object identified by the wildcard object locator is created with tags that identify the schema and table to which it corresponds in the source\.  
The second post\-processing rule adds `tag_1` and `tag_2` with corresponding static values `value_1` and `value_2` to a created S3 object that is identified by an exact\-match object locator\. This created S3 object thus corresponds to the single table in the source named `ITEM` with a schema named `aat`\. Because of the exact match, these tags replace any tags on this object added from the first post\-processing rule, which matches S3 objects by wildcard only\.

**Example Adding both dynamic tag names and values to S3 objects**  
The following example has two selection rules and one post\-processing rule\. Here, input from the source includes just the `ITEM` table in either the `retail` or `wholesale` schema\.  

```
{
    "rules": [
        {
            "rule-type": "selection",
            "rule-id": "1",
            "rule-name": "1",
            "object-locator": {
                "schema-name": "retail",
                "table-name": "ITEM"
            },
            "rule-action": "include"
        },
        {
            "rule-type": "selection",
            "rule-id": "1",
            "rule-name": "1",
            "object-locator": {
                "schema-name": "wholesale",
                "table-name": "ITEM"
            },
            "rule-action": "include"
        },
        {
            "rule-type": "post-processing",
            "rule-id": "21",
            "rule-name": "21",
            "rule-action": "add-tag",
            "object-locator": {
                "schema-name": "%",
                "table-name": "ITEM",
            },
            "tag-set": [
              { 
                "key": "dw-schema-name",
                "value":"${schema-name}"
              },
              {
                "key": "dw-schema-table",
                "value": "my_prefix_ITEM"
              },
              {
                "key": "${schema-name}_ITEM_tag_1",
                "value": "value_1"
              },
              {
                "key": "${schema-name}_ITEM_tag_2",
                "value": "value_2"
              }
            ]
    ]
}
```
The tag set for the post\-processing rule adds two tags \(`dw-schema-name` and `dw-schema-table`\) to all S3 objects created for the `ITEM` table in the target\. The first tag has the dynamic value `"${schema-name}"` and the second tag has a static value, `"my_prefix_ITEM"`\. Thus, each target S3 object is created with tags that identify the schema and table to which it corresponds in the source\.   
In addition, the tag set adds two additional tags with dynamic names \(`${schema-name}_ITEM_tag_1` and `"${schema-name}_ITEM_tag_2"`\)\. These have the corresponding static values `value_1` and `value_2`\. Thus, these tags are each named for the current schema, `retail` or `wholesale`\. You can't create a duplicate dynamic tag name in this object, because each object is created for a single unique schema name\. The schema name is used to create an otherwise unique tag name\.

## Creating AWS KMS keys to encrypt Amazon S3 target objects<a name="CHAP_Target.S3.KMSKeys"></a>

You can create and use custom AWS KMS keys to encrypt your Amazon S3 target objects\. After you create a KMS key, you can use it to encrypt objects using one of the following approaches when you create the S3 target endpoint:
+ Use the following options for S3 target objects \(with the default \.csv file storage format\) when you run the `create-endpoint` command using the AWS CLI\.

  ```
  --s3-settings '{"ServiceAccessRoleArn": "your-service-access-ARN", 
  "CsvRowDelimiter": "\n", "CsvDelimiter": ",", "BucketFolder": "your-bucket-folder", 
  "BucketName": "your-bucket-name", "EncryptionMode": "SSE_KMS", 
  "ServerSideEncryptionKmsKeyId": "your-KMS-key-ARN"}'
  ```

   Here, `your-KMS-key-ARN` is the Amazon Resource Name \(ARN\) for your KMS key\. For more information, see [Endpoint settings when using Amazon S3 as a target for AWS DMS](#CHAP_Target.S3.EndpointSettings)\.
+ Set the extra connection attribute `encryptionMode` to the value `SSE_KMS` and the extra connection attribute `serverSideEncryptionKmsKeyId` to the ARN for your KMS key\. For more information, see [Extra connection attributes when using Amazon S3 as a target for AWS DMS](#CHAP_Target.S3.Configuring)\.

To encrypt Amazon S3 target objects using a KMS key, you need an IAM role that has permissions to access the Amazon S3 bucket\. This IAM role is then accessed in a policy \(a key policy\) attached to the encryption key that you create\. You can do this in your IAM console by creating the following:
+ A policy with permissions to access the Amazon S3 bucket\.
+ An IAM role with this policy\.
+ A KMS key encryption key with a key policy that references this role\.

The following procedures describe how to do this\.

**To create an IAM policy with permissions to access the Amazon S3 bucket**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Policies** in the navigation pane\. The **Policies** page opens\.

1. Choose **Create policy**\. The **Create policy** page opens\.

1. Choose **Service** and choose **S3**\. A list of action permissions appears\.

1. Choose **Expand all** to expand the list and choose the following permissions at a minimum:
   + **ListBucket**
   + **PutObject**
   + **DeleteObject**

   Choose any other permissions you need, and then choose **Collapse all** to collapse the list\.

1. Choose **Resources** to specify the resources that you want to access\. At a minimum, choose **All resources** to provide general Amazon S3 resource access\.

1. Add any other conditions or permissions you need, then choose **Review policy**\. Check your results on the **Review policy** page\.

1. If the settings are what you need, enter a name for the policy \(for example, `DMS-S3-endpoint-access`\), and any description, then choose **Create policy**\. The **Policies** page opens with a message indicating that your policy has been created\.

1. Search for and choose the policy name in the **Policies** list\. The **Summary** page appears displaying JSON for the policy similar to the following\.

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Sid": "VisualEditor0",
               "Effect": "Allow",
               "Action": [
                   "s3:PutObject",
                   "s3:ListBucket",
                   "s3:DeleteObject"
               ],
               "Resource": "*"
           }
       ]
   }
   ```

You have now created the new policy to access Amazon S3 resources for encryption with a specified name, for example `DMS-S3-endpoint-access`\.

**To create an IAM role with this policy**

1. On your IAM console, choose **Roles** in the navigation pane\. The **Roles** detail page opens\.

1. Choose **Create role**\. The **Create role** page opens\.

1. With AWS service selected as the trusted entity, choose **DMS** as the service to use the IAM role\.

1. Choose **Next: Permissions**\. The **Attach permissions policies** view appears in the **Create role** page\.

1. Find and select the IAM policy for the IAM role that you created in the previous procedure \(`DMS-S3-endpoint-access`\)\.

1. Choose **Next: Tags**\. The **Add tags** view appears in the **Create role** page\. Here, you can add any tags you want\.

1. Choose **Next: Review**\. The **Review** view appears in the **Create role** page\. Here, you can verify the results\.

1. If the settings are what you need, enter a name for the role \(required, for example, `DMS-S3-endpoint-access-role`\), and any additional description, then choose **Create role**\. The **Roles** detail page opens with a message indicating that your role has been created\.

You have now created the new role to access Amazon S3 resources for encryption with a specified name, for example, `DMS-S3-endpoint-access-role`\.

**To create a KMS key encryption key with a key policy that references your IAM role**
**Note**  
For more information about how AWS DMS works with AWS KMS encryption keys, see [Setting an encryption key and specifying AWS KMS permissions](CHAP_Security.md#CHAP_Security.EncryptionKey)\.

1. Sign in to the AWS Management Console and open the AWS Key Management Service \(AWS KMS\) console at [https://console\.aws\.amazon\.com/kms](https://console.aws.amazon.com/kms)\.

1. To change the AWS Region, use the Region selector in the upper\-right corner of the page\.

1. In the navigation pane, choose **Customer managed keys**\.

1. Choose **Create key**\. The **Configure key** page opens\.

1. For **Key type**, choose **Symmetric**\.
**Note**  
When you create this key, you can only create a symmetric key, because all AWS services, such as Amazon S3, only work with symmetric encryption keys\.

1. Choose **Advanced Options**\. For **Key material origin**, make sure that **KMS** is chosen, then choose **Next**\. The **Add labels** page opens\.

1. For **Create alias and description**, enter an alias for the key \(for example, `DMS-S3-endpoint-encryption-key`\) and any additional description\.

1. For **Tags**, add any tags that you want to help identify the key and track its usage, then choose **Next**\. The **Define key administrative permissions** page opens showing a list of users and roles that you can choose from\.

1. Add the users and roles that you want to manage the key\. Make sure that these users and roles have the required permissions to manage the key\. 

1. For **Key deletion**, choose whether key administrators can delete the key, then choose **Next**\. The **Define key usage permissions** page opens showing an additional list of users and roles that you can choose from\.

1. For **This account**, choose the available users you want to perform cryptographic operations on Amazon S3 targets\. Also choose the role that you previously created in **Roles** to enable access to encrypt Amazon S3 target objects, for example `DMS-S3-endpoint-access-role`\)\.

1. If you want to add other accounts not listed to have this same access, for **Other AWS accounts**, choose **Add another AWS account**, then choose **Next**\. The **Review and edit key policy** page opens, showing the JSON for the key policy that you can review and edit by typing into the existing JSON\. Here, you can see where the key policy references the role and users \(for example, `Admin` and `User1`\) that you chose in the previous step\. You can also see the different key actions permitted for the different principals \(users and roles\), as shown in the example following\.

   ```
   {
     "Id": "key-consolepolicy-3",
     "Version": "2012-10-17",
     "Statement": [
       {
         "Sid": "Enable IAM User Permissions",
         "Effect": "Allow",
         "Principal": {
           "AWS": [
             "arn:aws:iam::111122223333:root"
           ]
         },
         "Action": "kms:*",
         "Resource": "*"
       },
       {
         "Sid": "Allow access for Key Administrators",
         "Effect": "Allow",
         "Principal": {
           "AWS": [
             "arn:aws:iam::111122223333:role/Admin"
           ]
         },
         "Action": [
           "kms:Create*",
           "kms:Describe*",
           "kms:Enable*",
           "kms:List*",
           "kms:Put*",
           "kms:Update*",
           "kms:Revoke*",
           "kms:Disable*",
           "kms:Get*",
           "kms:Delete*",
           "kms:TagResource",
           "kms:UntagResource",
           "kms:ScheduleKeyDeletion",
           "kms:CancelKeyDeletion"
         ],
         "Resource": "*"
       },
       {
         "Sid": "Allow use of the key",
         "Effect": "Allow",
         "Principal": {
           "AWS": [
             "arn:aws:iam::111122223333:role/DMS-S3-endpoint-access-role",
             "arn:aws:iam::111122223333:role/Admin",
             "arn:aws:iam::111122223333:role/User1"
           ]
         },
         "Action": [
           "kms:Encrypt",
           "kms:Decrypt",
           "kms:ReEncrypt*",
           "kms:GenerateDataKey*",
           "kms:DescribeKey"
         ],
         "Resource": "*"
       },
       {
         "Sid": "Allow attachment of persistent resources",
         "Effect": "Allow",
         "Principal": {
           "AWS": [
             "arn:aws:iam::111122223333:role/DMS-S3-endpoint-access-role",
             "arn:aws:iam::111122223333:role/Admin",
             "arn:aws:iam::111122223333:role/User1"
           ]
         },
         "Action": [
           "kms:CreateGrant",
           "kms:ListGrants",
           "kms:RevokeGrant"
         ],
         "Resource": "*",
         "Condition": {
           "Bool": {
             "kms:GrantIsForAWSResource": true
           }
         }
       }
     ]
   ```

1. Choose **Finish**\. The **Encryption keys** page opens with a message indicating that your KMS key has been created\.

You have now created a new KMS key with a specified alias \(for example, `DMS-S3-endpoint-encryption-key`\)\. This key enables AWS DMS to encrypt Amazon S3 target objects\.

## Using date\-based folder partitioning<a name="CHAP_Target.S3.DatePartitioning"></a>

AWS DMS supports S3 folder partitions based on a transaction commit date when you use Amazon S3 as your target endpoint\. Using date\-based folder partitioning, you can write data from a single source table to a time\-hierarchy folder structure in an S3 bucket\. By partitioning folders when creating an S3 target endpoint, you can do the following:
+ Better manage your S3 objects
+ Limit the size of each S3 folder
+ Optimize data lake queries or other subsequent operations

You can enable date\-based folder partitioning when you create an S3 target endpoint\. You can enable it when you either migrate existing data and replicate ongoing changes \(full load \+ CDC\), or replicate data changes only \(CDC only\)\. Use the following target endpoint settings:
+ `DatePartitionEnabled` – Specifies partitioning based on dates\. Set this Boolean option to `true` to partition S3 bucket folders based on transaction commit dates\. 

  You can't use this setting with `PreserveTransactions` or `CdcPath`\.

  The default value is `false`\. 
+ `DatePartitionSequence` – Identifies the sequence of the date format to use during folder partitioning\. Set this ENUM option to `YYYYMMDD`, `YYYYMMDDHH`, `YYYYMM`, `MMYYYYDD`, or `DDMMYYYY`\. The default value is `YYYYMMDD`\. Use this setting when `DatePartitionedEnabled` is set to `true.`
+ `DatePartitionDelimiter` – Specifies a date separation delimiter to use during folder partitioning\. Set this ENUM option to `SLASH`, `DASH`, `UNDERSCORE`, or `NONE`\. The default value is `SLASH`\. Use this setting when `DatePartitionedEnabled` is set to `true`\.

The following example shows how to enable date\-based folder partitioning, with default values for the data partition sequence and the delimiter\. It uses the `--s3-settings '{json-settings}'` option of the AWS CLI\.`create-endpoint` command\. 

```
   --s3-settings '{"DatePartitionEnabled": true,"DatePartitionSequence": "YYYYMMDD","DatePartitionDelimiter": "SLASH"}'
```

## Parallel load of partitioned sources when using Amazon S3 as a target for AWS DMS<a name="CHAP_Target.S3.ParallelLoad"></a>

You can configure a parallel full load of partitioned data sources to Amazon S3 targets\. This approach improves the load times for migrating partitioned data from supported source database engines to the S3 target\. To improve the load times of partitioned source data, you create S3 target subfolders mapped to the partitions of every table in the source database\. These partition\-bound subfolders allow AWS DMS to run parallel processes to populate each subfolder on the target\.

To configure a parallel full load of an S3 target, S3 supports three `parallel-load` rule types for the `table-settings` rule of table mapping:
+ `partitions-auto`
+ `partitions-list`
+ `ranges`

For more information on these parallel\-load rule types, see [Table and collection settings rules and operations](CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Tablesettings.md)\.

For the `partitions-auto` and `partitions-list` rule types, AWS DMS uses each partition name from the source endpoint to identify the target subfolder structure, as follows\.

```
bucket_name/bucket_folder/database_schema_name/table_name/partition_name/LOADseq_num.csv
```

Here, the subfolder path where data is migrated and stored on the S3 target includes an additional `partition_name` subfolder that corresponds to a source partition with the same name\. This `partition_name` subfolder then stores one or more `LOADseq_num.csv` files containing data migrated from the specified source partition\. Here, `seq_num` is the sequence number postfix on the \.csv file name, such as `00000001` in the \.csv file with the name, `LOAD00000001.csv`\.

However, some database engines, such as MongoDB and DocumentDB, don't have the concept of partitions\. For these database engines, AWS DMS adds the running source segment index as a prefix to the target \.csv file name, as follows\.

```
.../database_schema_name/table_name/SEGMENT1_LOAD00000001.csv
.../database_schema_name/table_name/SEGMENT1_LOAD00000002.csv
...
.../database_schema_name/table_name/SEGMENT2_LOAD00000009.csv
.../database_schema_name/table_name/SEGMENT3_LOAD0000000A.csv
```

Here, the files `SEGMENT1_LOAD00000001.csv` and `SEGMENT1_LOAD00000002.csv` are named with the same running source segment index prefix, `SEGMENT1`\. They're named as so because the migrated source data for these two \.csv files is associated with the same running source segment index\. On the other hand, the migrated data stored in each of the target `SEGMENT2_LOAD00000009.csv` and `SEGMENT3_LOAD0000000A.csv` files is associated with different running source segment indexes\. Each file has its file name prefixed with the name of its running segment index, `SEGMENT2` and `SEGMENT3`\.

For the `ranges` parallel\-load type, you define the column names and column values using the `columns` and `boundaries` settings of the `table-settings` rules\. With these rules, you can specify partitions corresponding to segment names, as follows\.

```
"parallel-load": {
    "type": "ranges",
    "columns": [
         "region",
         "sale"
    ],
    "boundaries": [
          [
               "NORTH",
               "1000"
          ],
          [
               "WEST",
               "3000"
          ]
    ],
    "segment-names": [
          "custom_segment1",
          "custom_segment2",
          "custom_segment3"
    ]
}
```

Here, the `segment-names` setting defines names for three partitions to migrate data in parallel on the S3 target\. The migrated data is parallel\-loaded and stored in \.csv files under the partition subfolders in order, as follows\.

```
.../database_schema_name/table_name/custom_segment1/LOAD[00000001...].csv
.../database_schema_name/table_name/custom_segment2/LOAD[00000001...].csv
.../database_schema_name/table_name/custom_segment3/LOAD[00000001...].csv
```

Here, AWS DMS stores a series of \.csv files in each of the three partition subfolders\. The series of \.csv files in each partition subfolder is named incrementally starting from `LOAD00000001.csv` until all the data is migrated\.

In some cases, you might not explicitly name partition subfolders for a `ranges` parallel\-load type using the `segment-names` setting\. In these case, AWS DMS applies the default of creating each series of \.csv files under its `table_name` subfolder\. Here, AWS DMS prefixes the file names of each series of \.csv files with the name of the running source segment index, as follows\.

```
.../database_schema_name/table_name/SEGMENT1_LOAD[00000001...].csv
.../database_schema_name/table_name/SEGMENT2_LOAD[00000001...].csv
.../database_schema_name/table_name/SEGMENT3_LOAD[00000001...].csv
...
.../database_schema_name/table_name/SEGMENTZ_LOAD[00000001...].csv
```

## Endpoint settings when using Amazon S3 as a target for AWS DMS<a name="CHAP_Target.S3.EndpointSettings"></a>

You can use endpoint settings to configure your Amazon S3 target similar to using extra connection attributes\. You can specify these settings when you create the target endpoint using the `create-endpoint` command in the AWS CLI, with the `--s3-settings 'json-settings'` option\. Here, `json-settings` is a JSON object containing parameters to specify the settings\. 

You can also specify a \.json file containing the same `json-settings` object, for example, as in the following: `--s3-settings file:///your-file-path/my_s3_settings.json`\. Here, `my_s3_settings.json` is the name of a \.json file that contains the same `json-settings object`\.

The parameter names for endpoint settings are the same as the names for equivalent extra connections attributes, except that the parameter names for endpoint settings have initial caps\. However, not all S3 target endpoint settings using extra connection attributes are available using the `--s3-settings` option of the `create-endpoint` command\. For more information about the available settings for the `create-endpoint` CLI comment, see [create\-endpoint](https://docs.aws.amazon.com/cli/latest/reference/dms/create-endpoint.html) in the *AWS CLI Command Reference* for AWS DMS\. For general information about these settings, see the equivalent extra connection attributes in [Extra connection attributes when using Amazon S3 as a target for AWS DMS](#CHAP_Target.S3.Configuring)\.

You can use S3 target endpoint settings to configure the following:
+ A custom KMS key to encrypt your S3 target objects\.
+ Parquet files as the storage format for S3 target objects\.
+ Change data capture \(CDC\) including transaction order on the S3 target\.

### AWS KMS key settings for data encryption<a name="CHAP_Target.S3.EndpointSettings.KMSkeys"></a>

The following examples show configuring a custom KMS key to encrypt your S3 target objects\. To start, you might run the following `create-endpoint` CLI command\.

```
aws dms create-endpoint --endpoint-identifier s3-target-endpoint --engine-name s3 --endpoint-type target 
--s3-settings '{"ServiceAccessRoleArn": "your-service-access-ARN", "CsvRowDelimiter": "\n", 
"CsvDelimiter": ",", "BucketFolder": "your-bucket-folder", 
"BucketName": "your-bucket-name", 
"EncryptionMode": "SSE_KMS", 
"ServerSideEncryptionKmsKeyId": "arn:aws:kms:us-east-1:111122223333:key/72abb6fb-1e49-4ac1-9aed-c803dfcc0480"}'
```

Here, the JSON object specified by `--s3-settings` option defines two parameters\. One is an `EncryptionMode` parameter with the value `SSE_KMS`\. The other is an `ServerSideEncryptionKmsKeyId` parameter with the value of `arn:aws:kms:us-east-1:111122223333:key/72abb6fb-1e49-4ac1-9aed-c803dfcc0480`\. This value is an Amazon Resource Name \(ARN\) for your custom KMS key\. For an S3 target, you also specify additional settings\. These identify the server access role, provide delimiters for the default CSV object storage format, and give the bucket location and name to store S3 target objects\.

By default, S3 data encryption occurs using S3 server\-side encryption\. For the previous example's S3 target, this is also equivalent to specifying its endpoint settings as in the following example\.

```
aws dms create-endpoint --endpoint-identifier s3-target-endpoint --engine-name s3 --endpoint-type target
--s3-settings '{"ServiceAccessRoleArn": "your-service-access-ARN", "CsvRowDelimiter": "\n", 
"CsvDelimiter": ",", "BucketFolder": "your-bucket-folder", 
"BucketName": "your-bucket-name", 
"EncryptionMode": "SSE_S3"}'
```

For more information about working with S3 server\-side encryption, see [Protecting data using server\-side encryption](https://docs.aws.amazon.com/AmazonS3/latest/dev/serv-side-encryption.html)\.

**Note**  
You can also use the CLI `modify-endpoint` command to change the value of the `EncryptionMode` parameter for an existing endpoint from `SSE_KMS` to `SSE_S3`\. But you can’t change the `EncryptionMode` value from `SSE_S3` to `SSE_KMS`\.

### Settings for using \.parquet files to store S3 target objects<a name="CHAP_Target.S3.EndpointSettings.Parquet"></a>

The default format for creating S3 target objects is \.csv files\. The following examples show some endpoint settings for specifying \.parquet files as the format for creating S3 target objects\. You can specify the \.parquet files format with all the defaults, as in the following example\.

```
aws dms create-endpoint --endpoint-identifier s3-target-endpoint --engine-name s3 --endpoint-type target 
--s3-settings '{"ServiceAccessRoleArn": "your-service-access-ARN", "DataFormat": "parquet"}'
```

Here, the `DataFormat` parameter is set to `parquet` to enable the format with all the S3 defaults\. These defaults include a dictionary encoding \(`"EncodingType: "rle-dictionary"`\) that uses a combination of bit\-packing and run\-length encoding to more efficiently store repeating values\.

You can add additional settings for options other than the defaults as in the following example\.

```
aws dms create-endpoint --endpoint-identifier s3-target-endpoint --engine-name s3 --endpoint-type target
--s3-settings '{"ServiceAccessRoleArn": "your-service-access-ARN", "BucketFolder": "your-bucket-folder",
"BucketName": "your-bucket-name", "CompressionType": "GZIP", "DataFormat": "parquet", "EncodingType: "plain-dictionary", "DictPageSizeLimit": 3,072,000,
"EnableStatistics": false }'
```

Here, in addition to parameters for several standard S3 bucket options and the `DataFormat` parameter, the following additional \.parquet file parameters are set:
+ `EncodingType` – Set to a dictionary encoding \(`plain-dictionary`\) that stores values encountered in each column in a per\-column chunk of the dictionary page\.
+ `DictPageSizeLimit` – Set to a maximum dictionary page size of 3 MB\.
+ `EnableStatistics` – Disables the default that enables the collection of statistics about Parquet file pages and row groups\.

### Capturing data changes \(CDC\) including transaction order on the S3 target<a name="CHAP_Target.S3.EndpointSettings.CdcPath"></a>

By default when AWS DMS runs a CDC task, it stores all the row changes logged in your source database \(or databases\) in one or more files for each table\. Each set of files containing changes for the same table reside in a single target directory associated with that table\. AWS DMS creates as many target directories as database tables migrated to the Amazon S3 target endpoint\. The files are stored on the S3 target in these directories without regard to transaction order\. For more information on the file naming conventions, data contents, and format, see [Using Amazon S3 as a target for AWS Database Migration Service](#CHAP_Target.S3)\.

To capture source database changes in a manner that also captures the transaction order, you can specify S3 endpoint settings that direct AWS DMS to store the row changes for *all* database tables in one or more \.csv files created depending on transaction size\. These \.csv *transaction files* contain all row changes listed sequentially in transaction order for all tables involved in each transaction\. These transaction files reside together in a single *transaction directory* that you also specify on the S3 target\. In each transaction file, the transaction operation and the identify of the database and source table for each row change is stored as part of the row data as follows\. 

```
operation,table_name,database_schema_name,field_value,...
```

Here, `operation` is the transaction operation on the changed row, `table_name` is the name of the database table where the row is changed, `database_schema_name` is the name of the database schema where the table resides, and `field_value` is the first of one or more field values that specify the data for the row\.

The example following of a transaction file shows changed rows for one or more transactions that involve two tables\.

```
I,Names_03cdcad11a,rdsTempsdb,13,Daniel
U,Names_03cdcad11a,rdsTempsdb,23,Kathy
D,Names_03cdcad11a,rdsTempsdb,13,Cathy
I,Names_6d152ce62d,rdsTempsdb,15,Jane
I,Names_6d152ce62d,rdsTempsdb,24,Chris
I,Names_03cdcad11a,rdsTempsdb,16,Mike
```

Here, the transaction operation on each row is indicated by `I` \(insert\), `U` \(update\), or `D` \(delete\) in the first column\. The table name is the second column value \(for example, `Names_03cdcad11a`\)\. The name of the database schema is the value of the third column \(for example, `rdsTempsdb`\)\. And the remaining columns are populated with your own row data \(for example, `13,Daniel`\)\.

In addition, AWS DMS names the transaction files it creates on the Amazon S3 target using a time stamp according to the following naming convention\.

```
CDC_TXN-timestamp.csv
```

Here, `timestamp` is the time when the transaction file was created, as in the following example\. 

```
CDC_TXN-20201117153046033.csv
```

This time stamp in the file name ensures that the transaction files are created and listed in transaction order when you list them in their transaction directory\.

**Note**  
When capturing data changes in transaction order, AWS DMS always stores the row changes in \.csv files regardless of the value of the `DataFormat` S3 setting on the target\. AWS DMS doesn't save data changes in transaction order using \.parquet files\.

To control the frequency of writes to an Amazon S3 target during a data replication task, you can configure the `cdcMaxBatchInterval` and `cdcMinFileSize` extra connection attributes\. This can result in better performance when analyzing the data without any additional overhead operations\. For more information, see [Extra connection attributes when using Amazon S3 as a target for AWS DMS](#CHAP_Target.S3.Configuring) 

**To tell AWS DMS to store all row changes in transaction order**

1. Set the `PreserveTransactions` S3 setting on the target to `true`\.

1. Set the `CdcPath` S3 setting on the target to a relative folder path where you want AWS DMS to store the \.csv transaction files\.

   AWS DMS creates this path either under the default S3 target bucket and working directory or under the bucket and bucket folder that you specify using the `BucketName` and `BucketFolder` S3 settings on the target\.

## Extra connection attributes when using Amazon S3 as a target for AWS DMS<a name="CHAP_Target.S3.Configuring"></a>

You can specify the following options as extra connection attributes\. If you have multiple connection attribute settings, separate them from each other by semicolons with no additional white space\.


| **Option** | **Description** | 
| --- | --- | 
| csvNullValue |  An optional parameter that specifies how AWS DMS treats null values\. While handling the null value, you can use this parameter to pass a user\-defined string as null when writing to the target\. For example, when target columns are not nullable, you can use this option to differentiate between the empty string value and the null value\. So, if you set this parameter value to the empty string \("" or ''\), AWS DMS treats the empty string as the null value instead of `NULL`\. Default value: `NULL` Valid values: any valid string Example: `csvNullValue='';`  | 
| addColumnName |  An optional parameter that when set to `true` or `y` you can use to add column name information to the \.csv output file\. You can't use this parameter with `PreserveTransactions` or `CdcPath`\. Default value: `false` Valid values: `true`, `false`, `y`, `n` Example: `addColumnName=true;`  | 
| bucketFolder |  An optional parameter to set a folder name in the S3 bucket\. If provided, target objects are created as \.csv or \.parquet files in the path `bucketFolder/schema_name/table_name/`\. If this parameter isn't specified, then the path used is `schema_name/table_name/`\.  Example: `bucketFolder=testFolder;`  | 
| bucketName |  The name of the S3 bucket where S3 target objects are created as \.csv or \.parquet files\. Example: `bucketName=buckettest;`  | 
| cannedAclForObjects |  A value that enables AWS DMS to specify a predefined \(canned\) access control list for objects created in the S3 bucket as \.csv or \.parquet files\. For more information about Amazon S3 canned ACLs, see [Canned ACL](http://docs.aws.amazon.com/AmazonS3/latest/dev/acl-overview.html#canned-acl) in the *Amazon S3 Developer Guide\.* Default value: NONE Valid values for this attribute are: NONE; PRIVATE; PUBLIC\_READ; PUBLIC\_READ\_WRITE; AUTHENTICATED\_READ; AWS\_EXEC\_READ; BUCKET\_OWNER\_READ; BUCKET\_OWNER\_FULL\_CONTROL\. Example: `cannedAclForObjects=PUBLIC_READ;`  | 
| cdcInsertsOnly |  An optional parameter during a change data capture \(CDC\) load to write only INSERT operations to the comma\-separated value \(\.csv\) or columnar storage \(\.parquet\) output files\. By default \(the `false` setting\), the first field in a \.csv or \.parquet record contains the letter I \(INSERT\), U \(UPDATE\), or D \(DELETE\)\. This letter indicates whether the row was inserted, updated, or deleted at the source database for a CDC load to the target\. If `cdcInsertsOnly` is set to `true` or `y`, only INSERTs from the source database are migrated to the \.csv or \.parquet file\.  For \.csv format only, how these INSERTS are recorded depends on the value of `includeOpForFullLoad`\. If `includeOpForFullLoad` is set to `true`, the first field of every CDC record is set to I to indicate the INSERT operation at the source\. If `includeOpForFullLoad` is set to `false`, every CDC record is written without a first field to indicate the INSERT operation at the source\. For more information about how these parameters work together, see [Indicating source DB operations in migrated S3 data](#CHAP_Target.S3.Configuring.InsertOps)\. Default value: `false` Valid values: `true`, `false`, `y`, `n` Example: `cdcInsertsOnly=true;`  | 
| cdcInsertsAndUpdates |  Enables a change data capture \(CDC\) load to write INSERT and UPDATE operations to \.csv or \.parquet \(columnar storage\) output files\. The default setting is `false`, but when `cdcInsertsAndUpdates` is set to `true` or `y`, INSERTs and UPDATEs from the source database are migrated to the \.csv or \.parquet file\.  For \.csv file format only, how these INSERTs and UPDATEs are recorded depends on the value of the `includeOpForFullLoad` parameter\. If `includeOpForFullLoad` is set to `true`, the first field of every CDC record is set to either `I` or `U` to indicate INSERT and UPDATE operations at the source\. But if `includeOpForFullLoad` is set to `false`, CDC records are written without an indication of INSERT or UPDATE operations at the source\.   For more information about how these parameters work together, see [Indicating source DB operations in migrated S3 data](#CHAP_Target.S3.Configuring.InsertOps)\.  `cdcInsertsOnly` and `cdcInsertsAndUpdates` can't both be set to true for the same endpoint\. Set either `cdcInsertsOnly` or `cdcInsertsAndUpdates` to `true` for the same endpoint, but not both\.   Default value: `false` Valid values: `true`, `false`, `y`, `n` Example: `cdcInsertsAndUpdates=true;`  | 
|  `CdcPath`  |  Specifies the folder path of CDC files\. For an S3 source, this setting is required if a task captures change data; otherwise, it's optional\. If `CdcPath` is set, DMS reads CDC files from this path and replicates the data changes to the target endpoint\. For an S3 target if you set `PreserveTransactions` to true, DMS verifies that you have set this parameter to a folder path on your S3 target where DMS can save the transaction order for the CDC load\. DMS creates this CDC folder path in either your S3 target working directory or the S3 target location specified by `BucketFolder` and `BucketName`\. You can't use this parameter with `datePartitionedEnabled` or `addColumnName`\. Type: String For example, if you specify `CdcPath` as `MyChangedData`, and you specify `BucketName` as `MyTargetBucket` but do not specify `BucketFolder`, DMS creates the following CDC folder path: `MyTargetBucket/MyChangedData`\.  If you specify the same `CdcPath`, and you specify `BucketName` as `MyTargetBucket` and `BucketFolder` as `MyTargetData`, DMS creates the following CDC folder path: `MyTargetBucket/MyTargetData/MyChangedData`\. This setting is supported in AWS DMS versions 3\.4\.2 and later\. When capturing data changes in transaction order, DMS always stores the row changes in \.csv files regardless of the value of the DataFormat S3 setting on the target\. DMS doesn't save data changes in transaction order using \.parquet files\.   | 
|  `cdcMaxBatchInterval`  |  Maximum interval length condition, defined in seconds, to output a file to Amazon S3\. Default Value: 60 seconds When `cdcMaxBatchInterval` is specified and `cdcMinFileSize` is specified, the file write is triggered by whichever parameter condition is met first within an AWS DMS CloudFormation template\.  | 
|  `cdcMinFileSize`  |  Minimum file size condition as defined in kilobytes to output a file to Amazon S3\. Default Value: 32000 KB When `cdcMinFileSize` is specified and `cdcMaxBatchInterval` is specified, the file write is triggered by whichever parameter condition is met first within an AWS DMS CloudFormation template\.  | 
|  `PreserveTransactions`  |  If set to `true`, DMS saves the transaction order for change data capture \(CDC\) on the Amazon S3 target specified by `CdcPath`\. You can't use this parameter with `datePartitionedEnabled` or `addColumnName`\. Type: Boolean When capturing data changes in transaction order, DMS always stores the row changes in \.csv files regardless of the value of the DataFormat S3 setting on the target\. DMS doesn't save data changes in transaction order using \.parquet files\. This setting is supported in AWS DMS versions 3\.4\.2 and later   | 
| includeOpForFullLoad |  An optional parameter during a full load to write the INSERT operations to the comma\-separated value \(\.csv\) output files only\.  For full load, records can only be inserted\. By default \(the `false` setting\), there is no information recorded in these output files for a full load to indicate that the rows were inserted at the source database\. If `includeOpForFullLoad` is set to `true` or `y`, the INSERT is recorded as an I annotation in the first field of the \.csv file\.  This parameter works together with `cdcInsertsOnly` or `cdcInsertsAndUpdates` for output to \.csv files only\. For more information about how these parameters work together, see [Indicating source DB operations in migrated S3 data](#CHAP_Target.S3.Configuring.InsertOps)\.  Default value: `false` Valid values: `true`, `false`, `y`, `n` Example: `includeOpForFullLoad=true;`  | 
| compressionType |  An optional parameter when set to `GZIP` uses GZIP to compress the target \.csv or \.parquet files\. When this parameter is set to the default, it leaves the files uncompressed\. Default value: `NONE` Valid values: `GZIP` or `NONE` Example: `compressionType=GZIP;`  | 
| csvDelimiter |  The delimiter used to separate columns in \.csv source files\. The default is a comma \(,\)\. Example: `csvDelimiter=,;`  | 
| csvRowDelimiter |  The delimiter used to separate rows in the \.csv source files\. The default is a newline \(\\n\)\. Example: `csvRowDelimiter=\n;`  | 
|   `maxFileSize`   |  A value that specifies the maximum size \(in KB\) of any \.csv file to be created while migrating to an S3 target during full load\. Default value: 1,048,576 KB \(1 GB\) Valid values: 1–1,048,576 Example: `maxFileSize=512`  | 
| rfc4180 |  An optional parameter used to set behavior to comply with RFC for data migrated to Amazon S3 using \.csv file format only\. When this value is set to `true` or `y` using Amazon S3 as a target, if the data has quotation marks, commas, or newline characters in it, AWS DMS encloses the entire column with an additional pair of double quotation marks \("\)\. Every quotation mark within the data is repeated twice\. This formatting complies with RFC 4180\. Default value: `true` Valid values: `true`, `false`, `y`, `n` Example: `rfc4180=false;`  | 
| encryptionMode |  The server\-side encryption mode that you want to encrypt your \.csv or \.parquet object files copied to S3\. The valid values are `SSE_S3` \(S3 server\-side encryption\) or `SSE_KMS` \(KMS key encryption\)\. If you choose `SSE_KMS`, set the `serverSideEncryptionKmsKeyId` parameter to the Amazon Resource Name \(ARN\) for the KMS key to be used for encryption\.  You can also use the CLI `modify-endpoint` command to change the value of the `encryptionMode` attribute for an existing endpoint from `SSE_KMS` to `SSE_S3`\. But you can’t change the `encryptionMode` value from `SSE_S3` to `SSE_KMS`\.  Default value: `SSE_S3` Valid values: `SSE_S3` or `SSE_KMS` Example: `encryptionMode=SSE_S3;`  | 
| serverSideEncryptionKmsKeyId |  If you set `encryptionMode` to `SSE_KMS`, set this parameter to the Amazon Resource Name \(ARN\) for the KMS key\. You can find this ARN by selecting the key alias in the list of AWS KMS keys created for your account\. When you create the key, you must associate specific policies and roles associated with this KMS key\. For more information, see [Creating AWS KMS keys to encrypt Amazon S3 target objects](#CHAP_Target.S3.KMSKeys)\. Example: `serverSideEncryptionKmsKeyId=arn:aws:kms:us-east-1:111122223333:key/72abb6fb-1e49-4ac1-9aed-c803dfcc0480;`  | 
| dataFormat |  The output format for the files that AWS DMS uses to create S3 objects\. For Amazon S3 targets, AWS DMS supports either \.csv or \.parquet files\. The \.parquet files have a binary columnar storage format with efficient compression options and faster query performance\. For more information about \.parquet files, see [https://parquet.apache.org/](https://parquet.apache.org/)\. Default value: `csv` Valid values: `csv` or `parquet` Example: `dataFormat=parquet;`  | 
| encodingType |  The Parquet encoding type\. The encoding type options include the following: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Target.S3.html) Default value: `rle-dictionary` Valid values: `rle-dictionary`, `plain`, or `plain-dictionary` Example: `encodingType=plain-dictionary;`  | 
| DictPageSizeLimit |  The maximum allowed size, in bytes, for a dictionary page in a \.parquet file\. If a dictionary page exceeds this value, the page uses plain encoding\. Default value: 1,024,000 \(1 MB\) Valid values: Any valid integer value Example: `DictPageSizeLimit=2,048,000;`  | 
| rowGroupLength |  The number of rows in one row group of a \.parquet file\. Default value: 10,024 \(10 KB\) Valid values: Any valid integer value Example: `rowGroupLength=20,048;`  | 
| dataPageSize |  The maximum allowed size, in bytes, for a data page in a \.parquet file\. Default value: 1,024,000 \(1 MB\) Valid values: Any valid integer value Example: `dataPageSize=2,048,000;`  | 
| parquetVersion |  The version of the \.parquet file format\. Default value: `PARQUET_1_0` Valid values: `PARQUET_1_0` or `PARQUET_2_0` Example: `parquetVersion=PARQUET_2_0;`  | 
| enableStatistics |  Set to `true` or `y` to enable statistics about \.parquet file pages and row groups\. Default value: `true` Valid values: `true`, `false`, `y`, `n` Example: `enableStatistics=false;`  | 
| timestampColumnName |  An optional parameter to include a timestamp column in the S3 target endpoint data\. AWS DMS includes an additional `STRING` column in the \.csv or \.parquet object files of your migrated data when you set `timestampColumnName` to a nonblank value\. For a full load, each row of this timestamp column contains a timestamp for when the data was transferred from the source to the target by DMS\.  For a CDC load, each row of the timestamp column contains the timestamp for the commit of that row in the source database\. The string format for this timestamp column value is `yyyy-MM-dd HH:mm:ss.SSSSSS`\. By default, the precision of this value is in microseconds\. For a CDC load, the rounding of the precision depends on the commit timestamp supported by DMS for the source database\. When the `addColumnName` parameter is set to `true`, DMS also includes the name for the timestamp column that you set as the nonblank value of `timestampColumnName`\. Example: `timestampColumnName=TIMESTAMP;`  | 
| useTaskStartTimeForFullLoadTimestamp |  When set to `true`, this parameter uses the task start time as the timestamp column value instead of the time data is written to target\. For full load, when `useTaskStartTimeForFullLoadTimestamp` is set to `true`, each row of the timestamp column contains the task start time\. For CDC loads, each row of the timestamp column contains the transaction commit time\. When `useTaskStartTimeForFullLoadTimestamp` is set to `false`, the full load timestamp in the timestamp column increments with the time data arrives at the target\. Default value: `false` Valid values: `true`, `false` Example: `useTaskStartTimeForFullLoadTimestamp=true;` `useTaskStartTimeForFullLoadTimestamp=true` helps make the S3 target ` timestampColumnName` for a full load sortable with `timestampColumnName` for a CDC load\.  | 
| parquetTimestampInMillisecond |  An optional parameter that specifies the precision of any `TIMESTAMP` column values written to an S3 object file in \.parquet format\. When this attribute is set to `true` or `y`, AWS DMS writes all `TIMESTAMP` columns in a \.parquet formatted file with millisecond precision\. Otherwise, DMS writes them with microsecond precision\. Currently, Amazon Athena and AWS Glue can handle only millisecond precision for `TIMESTAMP` values\. Set this attribute to true for \.parquet formatted S3 endpoint object files only if you plan to query or process the data with Athena or AWS Glue\.    AWS DMS writes any `TIMESTAMP` column values written to an S3 file in \.csv format with microsecond precision\.   The setting of this attribute has no effect on the string format of the timestamp column value inserted by setting the `timestampColumnName` attribute\.    Default value: `false` Valid values: `true`, `false`, `y`, `n` Example: `parquetTimestampInMillisecond=true;`  | 

## Indicating source DB operations in migrated S3 data<a name="CHAP_Target.S3.Configuring.InsertOps"></a>

When AWS DMS migrates records to an S3 target, it can create an additional field in each migrated record\. This additional field indicates the operation applied to the record at the source database\. 

For a full load when `includeOpForFullLoad` is `true` and the output format is \.csv, DMS always creates an additional first field in each \.csv record\. This field contains the letter I \(INSERT\) to indicate that the row was inserted at the source database\. For a CDC load when `cdcInsertsOnly` is `false` \(the default\), DMS also always creates an additional first field in each \.csv or \.parquet record\. This field contains the letter I \(INSERT\), U \(UPDATE\), or D \(DELETE\) to indicate whether the row was inserted, updated, or deleted at the source database\.

When the output format is \.csv only, if and how DMS creates and sets this first field also depends on the settings of `includeOpForFullLoad` and `cdcInsertsOnly` or `cdcInsertsAndUpdates`\.

In the following table, you can see how the settings of the `includeOpForFullLoad` and `cdcInsertsOnly` attributes work together to affect the setting of migrated records in this format\. 


| With these parameter settings | DMS sets target records as follows for \.csv output  | includeOpForFullLoad | cdcInsertsOnly | For full load | For CDC load | 
| --- | --- | --- | --- | --- | --- | 
| true | true | Added first field value set to I | Added first field value set to I | 
| false | false | No added field | Added first field value set to I, U, or D | 
| false | true | No added field | No added field | 
| true | false | Added first field value set to I | Added first field value set to I, U, or D | 

When `includeOpForFullLoad` and `cdcInsertsOnly` are set to the same value, the target records are set according to the attribute that controls record settings for the current migration type\. That attribute is `includeOpForFullLoad` for full load and `cdcInsertsOnly` for CDC load\.

When `includeOpForFullLoad` and `cdcInsertsOnly` are set to different values, AWS DMS makes the target record settings consistent for both CDC and full load\. It does this by making the record settings for a CDC load conform to the record settings for any earlier full load specified by `includeOpForFullLoad`\. 

In other words, suppose that a full load is set to add a first field to indicate an inserted record\. In this case, a following CDC load is set to add a first field that indicates an inserted, updated, or deleted record as appropriate at the source\. In contrast, suppose that a full load is set to *not* add a first field to indicate an inserted record\. In this case, a CDC load is also set to not add a first field to each record regardless of its corresponding record operation at the source\.

Similarly, how DMS creates and sets an additional first field depends on the settings of `includeOpForFullLoad` and `cdcInsertsAndUpdates`\. In the following table, you can see how the settings of the `includeOpForFullLoad` and `cdcInsertsAndUpdates` attributes work together to affect the setting of migrated records in this format\. 


| With these parameter settings | DMS sets target records as follows for \.csv output  | includeOpForFullLoad | cdcInsertsAndUpdates | For full load | For CDC load | 
| --- | --- | --- | --- | --- | --- | 
| true | true | Added first field value set to I | Added first field value set to I or U | 
| false | false | No added field | Added first field value set to I, U, or D | 
| false | true | No added field | Added first field value set to I or U | 
| true | false | Added first field value set to I | Added first field value set to I, U, or D | 

## Target data types for S3 Parquet<a name="CHAP_Target.S3.DataTypes"></a>

The following table shows the Parquet target data types that are supported when using AWS DMS and the default mapping from AWS DMS data types\.

For additional information about AWS DMS data types, see [Data types for AWS Database Migration Service](CHAP_Reference.DataTypes.md)\.


|  AWS DMS data type  |  S3 parquet data type   | 
| --- | --- | 
| BYTES | BINARY | 
| DATE | DATE32 | 
| TIME | TIME32 | 
| DATETIME | TIMESTAMP | 
| INT1 | INT8 | 
| INT2 | INT16 | 
| INT4 | INT32 | 
| INT8 | INT64 | 
| NUMERIC | DECIMAL | 
| REAL4 | FLOAT | 
| REAL8 | DOUBLE | 
| STRING | STRING | 
| UINT1 | UINT8 | 
| UINT2 | UINT16 | 
| UINT4 | UINT32 | 
| UINT8 | UINT64 | 
| WSTRING | STRING | 
| BLOB | BINARY | 
| NCLOB | STRING | 
| CLOB | STRING | 
| BOOLEAN | BOOL | 