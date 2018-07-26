# Creating a Task Assessment Report<a name="CHAP_Tasks.AssessmentReport"></a>

The task assessment feature identifies data types that might not get migrated correctly\. During a task assessment, AWS DMS reads the source database schema and creates a list of data types\. It then compares this list to a pre\-defined list of data types supported by AWS DMS\. AWS DMS creates a report you can look at to see if your migration task has unsupported data types\. 

The task assessment report includes a summary that lists the unsupported data types and the column count for each one\. It includes a list of data structures in JSON for each unsupported data type\. You can use the report to modify the source data types and improve the migration success\.

There are two levels of unsupported data types\. Data types that are shown on the report as “not supported” can’t be migrated\. Data types that are shown on the report as “partially supported” might be converted to another data type and not migrate as you expect\.

For example, the following is a sample task assessment report\.

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

The latest task assessment report can be viewed from the **Assessment** tab on the **Tasks page** on the AWS console\. AWS DMS stores previous task assessment reports in an Amazon S3 bucket\. The Amazon S3 bucket name is in the following format\.

```
dms-<customerId>-<customerDNS>
```

The report is stored in the bucket in a folder named with the task name\. The report’s file name is the date of the assessment in the format yyyy\-mm\-dd\-hh\-mm\. You can view and compare previous task assessment reports from the Amazon S3 console\.

AWS DMS also creates an AWS Identity and Access Management \(IAM\) role to allow access to the S3 bucket; the role name is dms\-access\-for\-tasks\. The role uses the `AmazonDMSRedshiftS3Role` policy\.

You can enable the task assessment feature using the AWS console, the AWS CLI, or the DMS API:
+ On the console, choose **Task Assessment** when creating or modifying a task\. To view the task assessment report using the console, choose the task on the **Tasks** page and choose the **Assessment results** tab in the details section\.
+ The CLI commands are `start-replication-task-assessment` to begin a task assessment and `describe-replication-task-assessment-results` to receive the task assessment report in JSON format\.
+ The AWS DMS API uses the `StartReplicationTaskAssessment` action to begin a task assessment and the `DescribeReplicationTaskAssessment` action to receive the task assessment report in JSON format\.