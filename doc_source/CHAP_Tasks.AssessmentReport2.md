# Starting and viewing data type assessments<a name="CHAP_Tasks.AssessmentReport2"></a>

A data type assessment identifies data types in a source database that might not get migrated correctly\. During this assessment, AWS DMS reads the source database schemas for a migration task and creates a list of the column data types\. It then compares this list to a predefined list of data types supported by AWS DMS\. AWS DMS creates a report that you can look at to see if your migration task has any unsupported data types\.

AWS DMS supports creating data type assessment reports for the following relational databases:
+ Oracle
+ SQL Server 
+ PostgreSQL
+ MySQL
+ MariaDB
+ Amazon Aurora

You can start and view a data type assessment report using the CLI and SDKs to access the AWS DMS API:
+ The CLI uses the [https://docs.aws.amazon.com/cli/latest/reference/dms/start-replication-task-assessment](https://docs.aws.amazon.com/cli/latest/reference/dms/start-replication-task-assessment) command to start a data type assessment and uses the [https://docs.aws.amazon.com/cli/latest/reference/dms/describe-replication-task-assessment-results](https://docs.aws.amazon.com/cli/latest/reference/dms/describe-replication-task-assessment-results) command to view the latest data type assessment report in JSON format\.
+ The AWS DMS API uses the [https://docs.aws.amazon.com/dms/latest/APIReference/API_StartReplicationTaskAssessment.html](https://docs.aws.amazon.com/dms/latest/APIReference/API_StartReplicationTaskAssessment.html) operation to start a data type assessment and uses the [https://docs.aws.amazon.com/dms/latest/APIReference/API_DescribeReplicationTaskAssessmentResults.html](https://docs.aws.amazon.com/dms/latest/APIReference/API_DescribeReplicationTaskAssessmentResults.html) operation to view the latest data type assessment report in JSON format\.

You can also view the latest data type assessment report in the AWS DMS Management Console\.

The data type assessment report is a single JSON file that includes a summary that lists the unsupported data types and the column count for each one\. It includes a list of data structures for each unsupported data type including the schemas, tables, and columns that have the unsupported data type\. You can use the report to modify the source data types and improve the migration success\.

There are two levels of unsupported data types\. Data types that appear on the report as not supported can't be migrated\. Data types that appear on the report as partially supported might be converted to another data type, but not migrate as you expect\.

The following example shows a sample data type assessment report that you might view\.

```
{
    "summary":{
        "task-name":"test15",
        "not-supported":{
            "data-type": [
                "sql-variant"
            ],
            "column-count":3
        },
        "partially-supported":{
            "data-type":[
                "float8",
                "jsonb"
            ],
            "column-count":2
        }
    },
    "types":[  
        {  
            "data-type":"float8",
            "support-level":"partially-supported",
            "schemas":[  
                {      
                    "schema-name":"schema1",
                    "tables":[  
                        {  
                            "table-name":"table1",
                            "columns":[  
                                "column1",
                                "column2"
                            ]
                        },
                        {  
                            "table-name":"table2",
                            "columns":[  
                                "column3",
                                "column4"
                            ]
                        }
                    ]
                },
                {  
                    "schema-name":"schema2",
                    "tables":[  
                        {  
                            "table-name":"table3",
                            "columns":[  
                                "column5",
                                "column6"
                            ]
                        },
                        {  
                            "table-name":"table4",
                            "columns":[  
                                "column7",
                                "column8"
                            ]
                        }
                    ]
                }
            ]
        },
        {  
            "datatype":"int8",
            "support-level":"partially-supported",
            "schemas":[  
                {  
                    "schema-name":"schema1",
                    "tables":[  
                        {  
                            "table-name":"table1",
                            "columns":[  
                                "column9",
                                "column10"
                            ]
                        },
                        {  
                            "table-name":"table2",
                            "columns":[  
                                "column11",
                                "column12"
                            ]
                        }
                    ]
                }
            ]
        }
    ]
}
```

To view the latest data type assessment report from the AWS DMS Management Console, use the **Assessment results** tab on the task page that opens when you select the **Identifier** for a given task on the **Database migration tasks** page\. 

AWS DMS also stores the latest and all previous data type assessments in an Amazon S3 bucket created by AWS DMS in your account\. The Amazon S3 bucket name has the following format, where *customerId* is your customer ID and *customerDNS* is an internal identifier\.

```
dms-customerId-customerDNS
```

**Note**  
By default, you can create up to 100 Amazon S3 buckets in each of your AWS accounts\. Since AWS DMS creates a bucket in your account, make sure it does not exceed your bucket limit\. Otherwise, the data type assessment will fail\.

All data type assessment reports for a given migration task are stored in a bucket folder named with the task identifier\. Each report's file name is the date of the data type assessment in the format yyyy\-mm\-dd\-hh\-mm\. You can view and compare previous data type assessment reports from the Amazon S3 Management Console\.

AWS DMS also creates an AWS Identity and Access Management \(IAM\) role to allow access to the S3 bucket created for these reports\. The role name is `dms-access-for-tasks`\. The role uses the `AmazonDMSRedshiftS3Role` policy\.