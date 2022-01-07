# Using an Amazon Redshift database as a target for AWS Database Migration Service<a name="CHAP_Target.Redshift"></a>

You can migrate data to Amazon Redshift databases using AWS Database Migration Service\. Amazon Redshift is a fully managed, petabyte\-scale data warehouse service in the cloud\. With an Amazon Redshift database as a target, you can migrate data from all of the other supported source databases\.

 The Amazon Redshift cluster must be in the same AWS account and same AWS Region as the replication instance\. 

During a database migration to Amazon Redshift, AWS DMS first moves data to an Amazon S3 bucket\. When the files reside in an Amazon S3 bucket, AWS DMS then transfers them to the proper tables in the Amazon Redshift data warehouse\. AWS DMS creates the S3 bucket in the same AWS Region as the Amazon Redshift database\. The AWS DMS replication instance must be located in that same AWS Region \. 

If you use the AWS CLI or DMS API to migrate data to Amazon Redshift, set up an AWS Identity and Access Management \(IAM\) role to allow S3 access\. For more information about creating this IAM role, see [Creating the IAM roles to use with the AWS CLI and AWS DMS API](CHAP_Security.md#CHAP_Security.APIRole)\.

The Amazon Redshift endpoint provides full automation for the following:
+ Schema generation and data type mapping
+ Full load of source database tables
+ Incremental load of changes made to source tables
+ Application of schema changes in data definition language \(DDL\) made to the source tables
+ Synchronization between full load and change data capture \(CDC\) processes\.

AWS Database Migration Service supports both full load and change processing operations\. AWS DMS reads the data from the source database and creates a series of comma\-separated value \(\.csv\) files\. For full\-load operations, AWS DMS creates files for each table\. AWS DMS then copies the table files for each table to a separate folder in Amazon S3\. When the files are uploaded to Amazon S3, AWS DMS sends a copy command and the data in the files are copied into Amazon Redshift\. For change\-processing operations, AWS DMS copies the net changes to the \.csv files\. AWS DMS then uploads the net change files to Amazon S3 and copies the data to Amazon Redshift\.

For additional details on working with Amazon Redshift as a target for AWS DMS, see the following sections: 

**Topics**
+ [Prerequisites for using an Amazon Redshift database as a target for AWS Database Migration Service](#CHAP_Target.Redshift.Prerequisites)
+ [Privileges required for using Redshift as a target](#CHAP_Target.Redshift.Privileges)
+ [Limitations on using Amazon Redshift as a target for AWS Database Migration Service](#CHAP_Target.Redshift.Limitations)
+ [Configuring an Amazon Redshift database as a target for AWS Database Migration Service](#CHAP_Target.Redshift.Configuration)
+ [Using enhanced VPC routing with an Amazon Redshift as a target for AWS Database Migration Service](#CHAP_Target.Redshift.EnhancedVPC)
+ [Creating and using AWS KMS keys to encrypt Amazon Redshift target data](#CHAP_Target.Redshift.KMSKeys)
+ [Endpoint settings when using Amazon Redshift as a target for AWS DMS](#CHAP_Target.Redshift.EndpointSettings)
+ [Extra connection attributes when using Amazon Redshift as a target for AWS DMS](#CHAP_Target.Redshift.ConnectionAttrib)
+ [Multithreaded task settings for Amazon Redshift](#CHAP_Target.Redshift.ParallelApply)
+ [Target data types for Amazon Redshift](#CHAP_Target.Redshift.DataTypes)

## Prerequisites for using an Amazon Redshift database as a target for AWS Database Migration Service<a name="CHAP_Target.Redshift.Prerequisites"></a>

The following list describes the prerequisites necessary for working with Amazon Redshift as a target for data migration:
+ Use the AWS Management Console to launch an Amazon Redshift cluster\. Note the basic information about your AWS account and your Amazon Redshift cluster, such as your password, user name, and database name\. You need these values when creating the Amazon Redshift target endpoint\. 
+ The Amazon Redshift cluster must be in the same AWS account and the same AWS Region as the replication instance\.
+ The AWS DMS replication instance needs network connectivity to the Amazon Redshift endpoint \(hostname and port\) that your cluster uses\.
+ AWS DMS uses an Amazon S3 bucket to transfer data to the Amazon Redshift database\. For AWS DMS to create the bucket, the console uses an IAM role, `dms-access-for-endpoint`\. If you use the AWS CLI or DMS API to create a database migration with Amazon Redshift as the target database, you must create this IAM role\. For more information about creating this role, see [Creating the IAM roles to use with the AWS CLI and AWS DMS API](CHAP_Security.md#CHAP_Security.APIRole)\. 
+ AWS DMS converts BLOBs, CLOBs, and NCLOBs to a VARCHAR on the target Amazon Redshift instance\. Amazon Redshift doesn't support VARCHAR data types larger than 64 KB, so you can't store traditional LOBs on Amazon Redshift\. 
+ Set the target metadata task setting [BatchApplyEnabled](CHAP_Tasks.CustomizingTasks.TaskSettings.ChangeProcessingTuning.md) to `true` for AWS DMS to handle changes to Amazon Redshift target tables during CDC\. A Primary Key on both the source and target table is required\. Without a Primary Key, changes are applied statement by statement\. And that can adversely affect task performance during CDC by causing target latency and impacting the cluster commit queue\. 

## Privileges required for using Redshift as a target<a name="CHAP_Target.Redshift.Privileges"></a>

Use the GRANT command to define access privileges for a user or user group\. Privileges include access options such as being able to read data in tables and views, write data, and create tables\. For more information about using GRANT with Amazon Redshift, see [GRANT](https://docs.aws.amazon.com//redshift/latest/dg/r_GRANT.html) in the * Amazon Redshift Database Developer Guide*\. 

The following is the syntax to give specific privileges for a table, database, schema, function, procedure, or language\-level privileges on Amazon Redshift tables and views\.

```
GRANT { { SELECT | INSERT | UPDATE | DELETE | REFERENCES } [,...] | ALL [ PRIVILEGES ] }
    ON { [ TABLE ] table_name [, ...] | ALL TABLES IN SCHEMA schema_name [, ...] }
    TO { username [ WITH GRANT OPTION ] | GROUP group_name | PUBLIC } [, ...]

GRANT { { CREATE | TEMPORARY | TEMP } [,...] | ALL [ PRIVILEGES ] }
    ON DATABASE db_name [, ...]
    TO { username [ WITH GRANT OPTION ] | GROUP group_name | PUBLIC } [, ...]

GRANT { { CREATE | USAGE } [,...] | ALL [ PRIVILEGES ] }
    ON SCHEMA schema_name [, ...]
    TO { username [ WITH GRANT OPTION ] | GROUP group_name | PUBLIC } [, ...]

GRANT { EXECUTE | ALL [ PRIVILEGES ] }
    ON { FUNCTION function_name ( [ [ argname ] argtype [, ...] ] ) [, ...] | ALL FUNCTIONS IN SCHEMA schema_name [, ...] }
    TO { username [ WITH GRANT OPTION ] | GROUP group_name | PUBLIC } [, ...]

GRANT { EXECUTE | ALL [ PRIVILEGES ] }
    ON { PROCEDURE procedure_name ( [ [ argname ] argtype [, ...] ] ) [, ...] | ALL PROCEDURES IN SCHEMA schema_name [, ...] }
    TO { username [ WITH GRANT OPTION ] | GROUP group_name | PUBLIC } [, ...]

GRANT USAGE 
    ON LANGUAGE language_name [, ...]
    TO { username [ WITH GRANT OPTION ] | GROUP group_name | PUBLIC } [, ...]
```

The following is the syntax for column\-level privileges on Amazon Redshift tables and views\. 

```
GRANT { { SELECT | UPDATE } ( column_name [, ...] ) [, ...] | ALL [ PRIVILEGES ] ( column_name [,...] ) }
     ON { [ TABLE ] table_name [, ...] }
     TO { username | GROUP group_name | PUBLIC } [, ...]
```

The following is the syntax for the ASSUMEROLE privilege granted to users and groups with a specified role\.

```
GRANT ASSUMEROLE
    ON { 'iam_role' [, ...] | ALL }
    TO { username | GROUP group_name | PUBLIC } [, ...]
    FOR { ALL | COPY | UNLOAD } [, ...]
```

## Limitations on using Amazon Redshift as a target for AWS Database Migration Service<a name="CHAP_Target.Redshift.Limitations"></a>

When using an Amazon Redshift database as a target, AWS DMS doesn't support the following:
+ The following DDL is not supported:

  ```
  ALTER TABLE table name MODIFY COLUMN column name data type;
  ```
+  AWS DMS cannot migrate or replicate changes to a schema with a name that begins with underscore \(\_\)\. If you have schemas that have a name that begins with an underscore, use mapping transformations to rename the schema on the target\. 
+  Amazon Redshift doesn't support VARCHARs larger than 64 KB\. LOBs from traditional databases can't be stored in Amazon Redshift\.
+  Applying a DELETE statement to a table with a multi\-column primary key is not supported when any of the primary key column names use a reserved word\. Go [here](https://docs.aws.amazon.com/redshift/latest/dg/r_pg_keywords.html) to see a list of Amazon Redshift reserved words\.
+ You may experience performance issues if your source system performs UPDATE operations on the primary key of a source table\. These performance issues occur when applying changes to the target\. This is because UPDATE \(and DELETE\) operations depend on the primary key value to identify the target row\. If you update the primary key of a source table, your task log will contain messages like the following:

  ```
  Update on table 1 changes PK to a PK that was previously updated in the same bulk update.
  ```
+ DMS doesn't support custom DNS names when configuring an endpoint for a Redshift cluster, and you need to use the Amazon provided DNS name\. Since the Amazon Redshift cluster must be in the same AWS account and Region as the replication instance, validation fails if you use a custom DNS endpoint\.

## Configuring an Amazon Redshift database as a target for AWS Database Migration Service<a name="CHAP_Target.Redshift.Configuration"></a>

AWS Database Migration Service must be configured to work with the Amazon Redshift instance\. The following table describes the configuration properties available for the Amazon Redshift endpoint\.


| Property | Description | 
| --- | --- | 
| server | The name of the Amazon Redshift cluster you are using\. | 
| port | The port number for Amazon Redshift\. The default value is 5439\. | 
| username | An Amazon Redshift user name for a registered user\. | 
| password | The password for the user named in the username property\. | 
| database | The name of the Amazon Redshift data warehouse \(service\) you are working with\. | 

If you want to add extra connection string attributes to your Amazon Redshift endpoint, you can specify the `maxFileSize` and `fileTransferUploadStreams` attributes\. For more information on these attributes, see [Extra connection attributes when using Amazon Redshift as a target for AWS DMS](#CHAP_Target.Redshift.ConnectionAttrib)\.

## Using enhanced VPC routing with an Amazon Redshift as a target for AWS Database Migration Service<a name="CHAP_Target.Redshift.EnhancedVPC"></a>

If you use Enhanced VPC Routing with your Amazon Redshift target, all COPY traffic between your Amazon Redshift cluster and your data repositories goes through your VPC\. Because Enhanced VPC Routing affects the way that Amazon Redshift accesses other resources, COPY commands might fail if you haven't configured your VPC correctly\.

AWS DMS can be affected by this behavior because it uses the COPY command to move data in S3 to an Amazon Redshift cluster\.

Following are the steps AWS DMS takes to load data into an Amazon Redshift target:

1. AWS DMS copies data from the source to \.csv files on the replication server\.

1. AWS DMS uses the AWS SDK to copy the \.csv files into an S3 bucket on your account\.

1. AWS DMS then uses the COPY command in Amazon Redshift to copy data from the \.csv files in S3 to an appropriate table in Amazon Redshift\.

If Enhanced VPC Routing is not enabled, Amazon Redshift routes traffic through the internet, including traffic to other services within the AWS network\. If the feature is not enabled, you do not have to configure the network path\. If the feature is enabled, you must specifically create a network path between your cluster's VPC and your data resources\. For more information on the configuration required, see [Enhanced VPC routing](https://docs.aws.amazon.com/redshift/latest/mgmt/enhanced-vpc-routing.html) in the Amazon Redshift documentation\. 

## Creating and using AWS KMS keys to encrypt Amazon Redshift target data<a name="CHAP_Target.Redshift.KMSKeys"></a>

You can encrypt your target data pushed to Amazon S3 before it is copied to Amazon Redshift\. To do so, you can create and use custom AWS KMS keys\. You can use the key you created to encrypt your target data using one of the following mechanisms when you create the Amazon Redshift target endpoint:
+ Use the following option when you run the `create-endpoint` command using the AWS CLI\.

  ```
  --redshift-settings '{"EncryptionMode": "SSE_KMS", "ServerSideEncryptionKmsKeyId": "your-kms-key-ARN"}'
  ```

  Here, `your-kms-key-ARN` is the Amazon Resource Name \(ARN\) for your KMS key\. For more information, see [Endpoint settings when using Amazon Redshift as a target for AWS DMS](#CHAP_Target.Redshift.EndpointSettings)\.
+ Set the extra connection attribute `encryptionMode` to the value `SSE_KMS` and the extra connection attribute `serverSideEncryptionKmsKeyId` to the ARN for your KMS key\. For more information, see [Extra connection attributes when using Amazon Redshift as a target for AWS DMS](#CHAP_Target.Redshift.ConnectionAttrib)\.

To encrypt Amazon Redshift target data using a KMS key, you need an AWS Identity and Access Management \(IAM\) role that has permissions to access Amazon Redshift data\. This IAM role is then accessed in a policy \(a key policy\) attached to the encryption key that you create\. You can do this in your IAM console by creating the following:
+ An IAM role with an AWS\-managed policy\.
+ A KMS key with a key policy that references this role\.

The following procedures describe how to do this\.

**To create an IAM role with the required AWS\-managed policy**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Roles**\. The **Roles** page opens\.

1. Choose **Create role**\. The **Create role** page opens\.

1. With **AWS service** chosen as the trusted entity, choose **DMS** as the service to use the role\.

1. Choose **Next: Permissions**\. The **Attach permissions policies** page appears\.

1. Find and select the `AmazonDMSRedshiftS3Role` policy\.

1. Choose **Next: Tags**\. The **Add tags** page appears\. Here, you can add any tags you want\.

1. Choose **Next: Review** and review your results\.

1. If the settings are what you need, enter a name for the role \(for example, `DMS-Redshift-endpoint-access-role`\), and any additional description, then choose **Create role**\. The **Roles** page opens with a message indicating that your role has been created\.

You have now created the new role to access Amazon Redshift resources for encryption with a specified name, for example `DMS-Redshift-endpoint-access-role`\.

**To create an AWS KMS encryption key with a key policy that references your IAM role**
**Note**  
For more information about how AWS DMS works with AWS KMS encryption keys, see [Setting an encryption key and specifying AWS KMS permissions](CHAP_Security.md#CHAP_Security.EncryptionKey)\.

1. Sign in to the AWS Management Console and open the AWS Key Management Service \(AWS KMS\) console at [https://console\.aws\.amazon\.com/kms](https://console.aws.amazon.com/kms)\.

1. To change the AWS Region, use the Region selector in the upper\-right corner of the page\.

1. In the navigation pane, choose **Customer managed keys**\.

1. Choose **Create key**\. The **Configure key** page opens\.

1. For **Key type**, choose **Symmetric**\.
**Note**  
When you create this key, you can only create a symmetric key, because all AWS services, such as Amazon Redshift, only work with symmetric encryption keys\.

1. Choose **Advanced Options**\. For **Key material origin**, make sure that **KMS** is chosen, then choose **Next**\. The **Add labels** page opens\.

1. For **Create alias and description**, enter an alias for the key \(for example, `DMS-Redshift-endpoint-encryption-key`\) and any additional description\.

1. For **Tags**, add any tags that you want to help identify the key and track its usage, then choose **Next**\. The **Define key administrative permissions** page opens showing a list of users and roles that you can choose from\.

1. Add the users and roles that you want to manage the key\. Make sure that these users and roles have the required permissions to manage the key\. 

1. For **Key deletion**, choose whether key administrators can delete the key, then choose **Next**\. The **Define key usage permissions** page opens showing an additional list of users and roles that you can choose from\.

1. For **This account**, choose the available users you want to perform cryptographic operations on Amazon Redshift targets\. Also choose the role that you previously created in **Roles** to enable access to encrypt Amazon Redshift target objects, for example `DMS-Redshift-endpoint-access-role`\)\.

1. If you want to add other accounts not listed to have this same access, for **Other AWS accounts**, choose **Add another AWS account**, then choose **Next**\. The **Review and edit key policy** page opens, showing the JSON for the key policy that you can review and edit by typing into the existing JSON\. Here, you can see where the key policy references the role and users \(for example, `Admin` and `User1`\) that you chose in the previous step\. You can also see the different key actions permitted for the different principals \(users and roles\), as shown in the following example\.

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
             "arn:aws:iam::111122223333:role/DMS-Redshift-endpoint-access-role",
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
             "arn:aws:iam::111122223333:role/DMS-Redshift-endpoint-access-role",
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

1. Choose **Finish**\. The **Encryption keys** page opens with a message indicating that your AWS KMS key has been created\.

You have now created a new KMS key with a specified alias \(for example, `DMS-Redshift-endpoint-encryption-key`\)\. This key enables AWS DMS to encrypt Amazon Redshift target data\.

## Endpoint settings when using Amazon Redshift as a target for AWS DMS<a name="CHAP_Target.Redshift.EndpointSettings"></a>

You can use endpoint settings to configure your Amazon Redshift target similar to using extra connection attributes\. You can specify these settings when you create the target endpoint using the `create-endpoint` command in the AWS CLI, with the `--redshift-settings "json-settings"` option\. Here, `json-settings` is a JSON object containing parameters to specify the settings\. You can also specify a \.json file containing the same `json-settings` object, for example, as in the following: `--redshift-settings file:///your-file-path/my_redshift_settings.json`\. Here, `my_redshift_settings.json` is the name of a \.json file that contains the same `json-settings object`\.

The parameter names for endpoint settings are the same as the names for equivalent extra connections attributes, except that the parameter names for endpoint settings have initial caps\. Also, not all Amazon Redshift target endpoint settings using extra connection attributes are available using the `--redshift-settings` option of the `create-endpoint` command\. For more information about the available settings in an AWS CLI call to `create-endpoint`, see [create\-endpoint](https://docs.aws.amazon.com/cli/latest/reference/dms/create-endpoint.html) in the *AWS CLI Command Reference* for AWS DMS\. For more information on these settings, see the equivalent extra connection attributes in [Extra connection attributes when using Amazon Redshift as a target for AWS DMS](#CHAP_Target.Redshift.ConnectionAttrib)\.

You can use Amazon Redshift target endpoint settings to configure the following:
+ A custom AWS KMS data encryption key\. You can then use this key to encrypt your data pushed to Amazon S3 before it is copied to Amazon Redshift\.
+ A custom S3 bucket as intermediate storage for data migrated to Amazon Redshift\.

### KMS key settings for data encryption<a name="CHAP_Target.Redshift.EndpointSettings.KMSkeys"></a>

The following examples show configuring a custom KMS key to encrypt your data pushed to S3\. To start, you might make the following `create-endpoint` call using the AWS CLI\.

```
aws dms create-endpoint --endpoint-identifier redshift-target-endpoint --endpoint-type target 
--engine-name redshift --username your-username --password your-password 
--server-name your-server-name --port 5439 --database-name your-db-name 
--redshift-settings '{"EncryptionMode": "SSE_KMS", 
"ServerSideEncryptionKmsKeyId": "arn:aws:kms:us-east-1:111122223333:key/24c3c5a1-f34a-4519-a85b-2debbef226d1"}'
```

Here, the JSON object specified by `--redshift-settings` option defines two parameters\. One is an `EncryptionMode` parameter with the value `SSE_KMS`\. The other is an `ServerSideEncryptionKmsKeyId` parameter with the value `arn:aws:kms:us-east-1:111122223333:key/24c3c5a1-f34a-4519-a85b-2debbef226d1`\. This value is an Amazon Resource Name \(ARN\) for your custom KMS key\.

By default, S3 data encryption occurs using S3 server\-side encryption\. For the previous example's Amazon Redshift target, this is also equivalent of specifying its endpoint settings, as in the following example\.

```
aws dms create-endpoint --endpoint-identifier redshift-target-endpoint --endpoint-type target 
--engine-name redshift --username your-username --password your-password 
--server-name your-server-name --port 5439 --database-name your-db-name 
--redshift-settings '{"EncryptionMode": "SSE_S3"}'
```

For more information about working with S3 server\-side encryption, see [Protecting data using server\-side encryption](https://docs.aws.amazon.com/AmazonS3/latest/dev/serv-side-encryption.html) in the *Amazon Simple Storage Service Developer Guide\.*

**Note**  
You can also use the CLI `modify-endpoint` command to change the value of the `EncryptionMode` parameter for an existing endpoint from `SSE_KMS` to `SSE_S3`\. But you can’t change the `EncryptionMode` value from `SSE_S3` to `SSE_KMS`\.

### Amazon S3 bucket settings<a name="CHAP_Target.Redshift.EndpointSettings.S3Buckets"></a>

When you migrate data to an Amazon Redshift target endpoint, AWS DMS uses a default Amazon S3 bucket as intermediate task storage before copying the migrated data to Amazon Redshift\. For example, the examples shown for creating an Amazon Redshift target endpoint with a AWS KMS data encryption key use this default S3 bucket \(see [KMS key settings for data encryption](#CHAP_Target.Redshift.EndpointSettings.KMSkeys)\)\. 

You can instead specify a custom S3 bucket for this intermediate storage by including the following parameters in the value of your `--redshift-settings` option on the AWS CLI `create-endpoint` command:
+ `BucketName` – A string you specify as the name of the S3 bucket storage\.
+ `BucketFolder` – \(Optional\) A string you can specify as the name of the storage folder in the specified S3 bucket\.
+ `ServiceAccessRoleArn` – The ARN of an IAM role that permits administrative access to the S3 bucket\. Typically, you create this role based on the `AmazonDMSRedshiftS3Role` policy\. For an example, see the procedure to create an IAM role with the required AWS\-managed policy in [Creating and using AWS KMS keys to encrypt Amazon Redshift target data](#CHAP_Target.Redshift.KMSKeys)\.
**Note**  
If you specify the ARN of a different IAM role using the `--service-access-role-arn` option of the `create-endpoint` command, this IAM role option takes precedence\.

The following example shows how you might use these parameters to specify a custom Amazon S3 bucket in the following `create-endpoint` call using the AWS CLI\. 

```
aws dms create-endpoint --endpoint-identifier redshift-target-endpoint --endpoint-type target 
--engine-name redshift --username your-username --password your-password 
--server-name your-server-name --port 5439 --database-name your-db-name 
--redshift-settings '{"ServiceAccessRoleArn": "your-service-access-ARN", 
"BucketName": "your-bucket-name", "BucketFolder": "your-bucket-folder-name"}'
```

## Extra connection attributes when using Amazon Redshift as a target for AWS DMS<a name="CHAP_Target.Redshift.ConnectionAttrib"></a>

You can use extra connection attributes to configure your Amazon Redshift target\. You specify these settings when you create the target endpoint\. If you have multiple connection attribute settings, separate them from each other by semicolons with no additional white space\.

The following table shows the extra connection attributes available when Amazon Redshift is the target\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Target.Redshift.html)

## Multithreaded task settings for Amazon Redshift<a name="CHAP_Target.Redshift.ParallelApply"></a>

You can improve performance of full load and change data capture \(CDC\) tasks for an Amazon Redshift target endpoint by using multithreaded task settings\. They enable you to specify the number of concurrent threads and the number of records to store in a buffer\.

### Multithreaded full load task settings for Amazon Redshift<a name="CHAP_Target.Redshift.ParallelApply.FullLoad"></a>

To promote full load performance, you can use the following `ParallelLoad*` task settings:
+ `ParallelLoadThreads` – Specifies the number of concurrent threads that DMS uses during a full load to push data records to an Amazon Redshift target endpoint\. The default value is zero \(0\) and the maximum value is 32\.

  You can use the `enableParallelBatchInMemoryCSVFiles` attribute set to `false` when using the `ParallelLoadThreads` task setting\. The attribute improves performance of larger multithreaded full load tasks by having DMS write to disk instead of memory\. The default value is `true`\.
+ `ParallelLoadBufferSize` – Specifies the maximum data record requests while using parallel load threads with Redshift target\. The default value is 100 and the maximum value is 1,000\. We recommend you use this option when ParallelLoadThreads > 1 \(greater than one\)\.

**Note**  
Support for the use of `ParallelLoad*` task settings during FULL LOAD to Amazon Redshift target endpoints is available in AWS DMS versions 3\.4\.5 and later\.  
The `ReplaceInvalidChars` Redshift endpoint setting is not supported for use during change data capture \(CDC\) or during a parallel load enabled FULL LOAD migration task\. It is supported for FULL LOAD migration when parallel load isn’t enabled\. For more information see [RedshiftSettings](https://docs.aws.amazon.com/dms/latest/APIReference/API_RedshiftSettings.html) in the *AWS Database Migration Service API Reference*

### Multithreaded CDC task settings for Amazon Redshift<a name="CHAP_Target.Redshift.ParallelApply.CDC"></a>

To promote CDC performance, you can use the following `ParallelApply*` task settings:
+ `ParallelApplyThreads` – Specifies the number of concurrent threads that AWS DMS uses during a CDC load to push data records to a Amazon Redshift target endpoint\. The default value is zero \(0\) and the maximum value is 32\.
+ `ParallelApplyBufferSize` – Specifies the maximum data record requests while using parallel apply threads with Redshift target\. The default value is 100 and the maximum value is 1,000\. We recommend to use this option when ParallelApplyThreads > 1 \(greater than one\)\. 

  To obtain the most benefit for Redshift as a target, we recommend that the value of `ParallelApplyBufferSize` be at least two times \(double or more\) the number of `ParallelApplyThreads`\.

**Note**  
Support for the use of `ParallelApply*` task settings during CDC to Amazon Redshift target endpoints is available in AWS DMS versions 3\.4\.3 and later\.

The level of parallelism applied depends on the correlation between the total *batch size* and the *maximum file size* used to transfer data\. When using multithreaded CDC task settings with a Redshift target, benefits are gained when batch size is large in relation to the maximum file size\. For example, you can use the following combination of endpoint and task settings to tune for optimal performance\. 

```
// Redshift endpoint setting
                
        MaxFileSize=250000;

// Task settings

        BatchApplyEnabled=true;
        BatchSplitSize =8000;
        BatchApplyTimeoutMax =1800;
        BatchApplyTimeoutMin =1800;
        ParallelApplyThreads=32;
        ParallelApplyBufferSize=100;
```

Using the settings above, a customer with a heavy transactional workload benefits by their 8000 MB batch buffer getting filled in 1800 seconds, utilizing 32 parallel threads with a 250 MB maximum file size\.

For more information, see [Change processing tuning settings](CHAP_Tasks.CustomizingTasks.TaskSettings.ChangeProcessingTuning.md)\.

**Note**  
DMS queries that run during ongoing replication to a Redshift cluster can share the same WLM \(workload management\) queue with other application queries that are running\. So, consider properly configuring WLM properties to influence performance during ongoing replication to a Redshift target\. For example, if other parallel ETL queries are running, DMS runs slower and performance gains are lost\.

## Target data types for Amazon Redshift<a name="CHAP_Target.Redshift.DataTypes"></a>

The Amazon Redshift endpoint for AWS DMS supports most Amazon Redshift data types\. The following table shows the Amazon Redshift target data types that are supported when using AWS DMS and the default mapping from AWS DMS data types\.

For additional information about AWS DMS data types, see [Data types for AWS Database Migration Service](CHAP_Reference.DataTypes.md)\.


| AWS DMS data types | Amazon Redshift data types | 
| --- | --- | 
| BOOLEAN | BOOL | 
| BYTES | VARCHAR \(Length\) | 
| DATE | DATE | 
| TIME | VARCHAR\(20\) | 
| DATETIME |  If the scale is => 0 and =< 6, depending on Redshift target column type, then one of the following: TIMESTAMP \(s\) TIMESTAMPTZ \(s\) — If source timestamp contains a zone offset \(such as in SQL Server or Oracle\) it converts to UTC on insert/update\. If it doesn't contain an offset, then time is considered in UTC already\. If the scale is => 7 and =< 9, then:  VARCHAR \(37\) | 
| INT1 | INT2 | 
| INT2 | INT2 | 
| INT4 | INT4 | 
| INT8 | INT8 | 
| NUMERIC | If the scale is => 0 and =< 37, then:  NUMERIC \(p,s\)  If the scale is => 38 and =< 127, then:  VARCHAR \(Length\) | 
| REAL4 | FLOAT4 | 
| REAL8 | FLOAT8 | 
| STRING | If the length is 1–65,535, then use VARCHAR \(length in bytes\)  If the length is 65,536–2,147,483,647, then use VARCHAR \(65535\) | 
| UINT1 | INT2 | 
| UINT2 | INT2 | 
| UINT4 | INT4 | 
| UINT8 | NUMERIC \(20,0\) | 
| WSTRING |  If the length is 1–65,535, then use NVARCHAR \(length in bytes\)  If the length is 65,536–2,147,483,647, then use NVARCHAR \(65535\) | 
| BLOB | VARCHAR \(maximum LOB size \*2\)  The maximum LOB size cannot exceed 31 KB\. Amazon Redshift doesn't support VARCHARs larger than 64 KB\. | 
| NCLOB | NVARCHAR \(maximum LOB size\)  The maximum LOB size cannot exceed 63 KB\. Amazon Redshift doesn't support VARCHARs larger than 64 KB\. | 
| CLOB | VARCHAR \(maximum LOB size\)  The maximum LOB size cannot exceed 63 KB\. Amazon Redshift doesn't support VARCHARs larger than 64 KB\. | 