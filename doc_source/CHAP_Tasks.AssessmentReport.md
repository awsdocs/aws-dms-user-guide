# Enabling and working with premigration assessments for a task<a name="CHAP_Tasks.AssessmentReport"></a>

A premigration assessment evaluates specified components of a database migration task to help identify any problems that might prevent a migration task from running as expected\. This assessment gives you a chance to identify issues before you run a new or modified task\. You can then fix problems before they occur while running the migration task itself\. This can avoid delays in completing a given database migration needed to repair data and your database environment\.

AWS DMS provides access to two different types of premigration assessments\. The first type of premigration assessment, a premigration assessment run, is a functional superset of the second type, a data type assessment\. They are described in the following topics:

**Note**  
If you do a premigration assessment run that includes the data type assessment, you don't need to do a data type assessment separately\.

1. [Specifying, starting, and viewing premigration assessment runs](CHAP_Tasks.AssessmentReport1.md) – A premigration assessment run specifies one or more individual assessments to run based on a new or existing migration task configuration\. Each individual assessment evaluates a specific element of a supported relational source or target database depending on considerations such as the migration type, supported objects, index configuration, and other task settings, such as table mappings that identify the schemas and tables to migrate\. 

   For example, an individual assessment might evaluate what source data types or primary key formats can and can't be migrated, possibly based on the AWS DMS engine version\. You can start and view the results of the latest assessment run and view the results of all prior assessment runs for a task either using the AWS DMS Management Console or using the AWS CLI and SDKs to access the AWS DMS API\. You can also view the results of prior assessment runs for a task in an Amazon S3 bucket that you have selected for AWS DMS to store these results\.
**Note**  
The number and types of available individual assessments can increase over time\. For more information about periodic updates, see [Specifying individual assessments](CHAP_Tasks.AssessmentReport1.md#CHAP_Tasks.AssessmentReport1.Individual)\. 

1. [Starting and viewing data type assessments](CHAP_Tasks.AssessmentReport2.md) – A data type assessment returns the results of a single type of premigration assessment in a single JSON structure: the data types that might not be migrated correctly in a supported relational source database instance\. This report returns the results for all problem data types found in the columns of every schema and table in the source database that is mapped for migration\. You can create and view the results of the latest data type assessment using the AWS CLI and SDKs to access the AWS DMS API\. You can also view the results of the latest data type assessment using the AWS DMS Management Console\. You can view the results of prior data type assessments in an Amazon S3 bucket for your account where AWS DMS stores these reports\.

## Storing premigration assessment runs in an S3 Bucket<a name="CHAP_Tasks.AssessmentReport.IAM"></a>

The following Identity and Access Management \(IAM\) policy allows DMS to store preassessment results in the S3 bucket that you create\.

**To access an S3 bucket for a premigration assessment**

1. Create a service role using IAM and attach an IAM policy like the following to your service role\. For information about creating a service role in the console, see [ Creating a role for an AWS service \(console\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-service.html#roles-creatingrole-service-console)\.

   ```
   //Policy to access S3 bucket
   
   {
      "Version":"2012-10-17",
      "Statement":[
         {
            "Effect":"Allow",
            "Action":[
               "s3:PutObject",
               "s3:DeleteObject",
               "s3:GetObject",
               "s3:PutObjectTagging"
            ],
            "Resource":[
               "arn:aws:s3:::my-bucket/*"
            ]
         },
         {
            "Effect":"Allow",
            "Action":[
               "s3:ListBucket",
               "s3:GetBucketLocation"
            ],
            "Resource":[
               "arn:aws:s3:::my-bucket"
            ]
         }
      ]
   }
   ```

1. Edit the trust relationship and attach the following IAM role to your service role, allowing DMS to assume the role\.

   ```
   {
      "Version":"2012-10-17",
      "Statement":[
         {
            "Sid":"",
            "Effect":"Allow",
            "Principal":{
               "Service":"dms.amazonaws.com"
            },
            "Action":"sts:AssumeRole"
         }
      ]
   }
   ```