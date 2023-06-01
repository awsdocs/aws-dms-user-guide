# Amazon S3 target data validation<a name="CHAP_Validating_S3"></a>

AWS DMS supports validating replicated data in Amazon S3 targets\. Because AWS DMS stores replicated data as flat files in Amazon S3, we use [ Amazon Athena](https://docs.aws.amazon.com/athena/latest/ug/what-is.html) `CREATE TABLE AS SELECT` \(CTAS\) queries to validate data\. 

Queries on data that is stored in Amazon S3 are computationally intense\. Thus, AWS DMS runs validation on Amazon S3 data during change data capture \(CDC\) only once a day, at midnight \(00:00\) UTC\. Each daily validation that AWS DMS runs is called an *interval validation*\. During an interval validation, AWS DMS validates all of the change records that were migrated to the target Amazon S3 bucket for the previous 24 hours\. For more information about limitations for interval validation, see [Limitations for using S3 target validation](#CHAP_Validating_S3_limitations)\.

Amazon S3 target validation uses Amazon Athena, so additional costs apply\. For more information, see [Amazon Athena Pricing](https://aws.amazon.com/athena/pricing/)\.

**Topics**
+ [Prerequisites](#CHAP_Validating_S3_prerequisites)
+ [Permissions](#CHAP_Validating_S3_permissions)
+ [Limitations](#CHAP_Validating_S3_limitations)
+ [Validation only tasks](#CHAP_Validating_S3_only)

## S3 target validation prerequisites<a name="CHAP_Validating_S3_prerequisites"></a>

Before using S3 target validation, check the following settings and permissions:
+ Set the `DataFormat` value for your endpoint's [S3Settings](https://docs.aws.amazon.com/dms/latest/APIReference/API_S3Settings.html) to `parquet`\. For more information, see [Parquet settings for S3](CHAP_Target.S3.md#CHAP_Target.S3.EndpointSettings.Parquet)\. 
+ Ensure that the role assigned to the user account that was used to create the migration task has the correct set of permissions\. See [Permissions](#CHAP_Validating_S3_permissions) following\.

For tasks using ongoing replication \(CDC\), check the following settings:
+ Turn on supplemental logging so you have complete records in the CDC data\. For information about turning on supplemental logging, see [Automatically add supplemental logging to an Oracle source endpoint](CHAP_Troubleshooting.md#CHAP_Troubleshooting.Oracle.AutoSupplLogging) in the [Troubleshooting and diagnostic support](CHAP_Troubleshooting.md) section in this guide\.
+ Set the `TimestampColumnName` parameter for the target endpoint\. There are no limitations on the timestamp column name\. For more information, see [S3Settings](https://docs.aws.amazon.com/dms/latest/APIReference/API_S3Settings.html)\.
+ Set up date\-based folder partitioning for the target\. For more information, see [Using date\-based folder partitioning](CHAP_Target.S3.md#CHAP_Target.S3.DatePartitioning)\.

## Permissions for using S3 target validation<a name="CHAP_Validating_S3_permissions"></a>

To set up access for using S3 target validation, ensure that the role assigned to the user account that was used to create the migration task has the following set of permissions\. Replace the sample values with your values\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
                "athena:StartQueryExecution",
                "athena:GetQueryExecution",
                "athena:CreateWorkGroup"
            ],
            "Resource": "arn:aws:athena:<endpoint_region_code>:<account_id>:workgroup/dms_validation_workgroup_for_task_*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "glue:CreateDatabase",
                "glue:DeleteDatabase",
                "glue:GetDatabase",
                "glue:GetTables",
                "glue:CreateTable",
                "glue:DeleteTable",
                "glue:GetTable"
            ],
            "Resource": [
                "arn:aws:glue:<endpoint_region_code>:<account_id>:catalog",
                "arn:aws:glue:<endpoint_region_code>:<account_id>:database/aws_dms_s3_validation_*",
                "arn:aws:glue:<endpoint_region_code>:<account_id>:table/aws_dms_s3_validation_*/*",
                "arn:aws:glue:<endpoint_region_code>:<account_id>:userDefinedFunction/aws_dms_s3_validation_*/*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetBucketLocation",
                "s3:GetObject",
                "s3:ListBucketMultipartUploads",
                "s3:AbortMultipartUpload",
                "s3:ListMultipartUploadParts"
            ],
            "Resource": [
                "arn:aws:s3:::<bucket_name>",
                "arn:aws:s3:::<bucket_name>/*"
            ]
        }
    ]
}
```

## Limitations for using S3 target validation<a name="CHAP_Validating_S3_limitations"></a>

View the following additional limitations that apply when using S3 target validation\. For limitations that apply to all validations, see [Limitations](CHAP_Validating.md#CHAP_Validating.Limitations)\.
+ Your `DatePartitionSequence` value needs a Day component\. S3 target validation does not support the `YYYYMM` format\.
+ When interval validation is running during CDC, you may see false validation errors in the `awsdms_validation_failures_v1` table\. These errors occur because AWS DMS migrates changes that arrived during the interval validation into the next day's partition folder\. Normally, these changes are written into the current day's partition folder\. These false errors are a limitation of validating replication from a dynamic source database to a static target, such as Amazon S3\. To investigate these false errors, check for records near the end of the validation window \(00:00 UTC\), which is when these errors typically appear\. 

  To minimize the number of false errors, ensure that the `CDCLatencySource` for the task is low\. For information about monitoring latency, see [Replication task metrics](CHAP_Monitoring.md#CHAP_Monitoring.Metrics.Task)\. 
+ Tasks in the `failed` or `stopped` state don't validate the previous day's changes\. To minimize validation errors because of unexpected failures, create separate validation only tasks with the same table mappings, and source and target endpoints\. For more information about validation only tasks, see [Using validation only tasks with S3 target validation](#CHAP_Validating_S3_only)\.
+ The **Validation Status** column in table statistics reflects the state of the most recent interval validation\. As a result, a table which has mismatches might show up as validated after the next day's interval validation\. Check the `s3_validation_failures folder` in the target Amazon S3 bucket for mismatches that occurred more than a day ago\.

## Using validation only tasks with S3 target validation<a name="CHAP_Validating_S3_only"></a>

A *validation only task* runs validation on data that is to be migrated without running the migration\. 

Validation only tasks continue to run, even if the migration task stops, which ensures that AWS DMS doesn't miss the 00:00 UTC interval validation window\.

Using validation only tasks with Amazon S3 target endpoints has the following limitations:
+ Validation only tasks do not support full\-load only tasks\. AWS DMS only supports validation only tasks with CDC\.
+ Validation only tasks only validate changes since the last interval validation window \(00:00 UTC\)\. Validation only tasks don't validate full\-load data or CDC data from previous days\.