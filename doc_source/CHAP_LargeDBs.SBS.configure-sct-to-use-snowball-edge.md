# Step 7: Configure AWS SCT to use AWS Snowball Edge<a name="CHAP_LargeDBs.SBS.configure-sct-to-use-snowball-edge"></a>

Your AWS SCT service profile must be updated to use the AWS DMS agent, which is a local AWS DMS that works on\-premises\.

**To update the AWS SCT profile to work with the AWS DMS agent**

1. Start AWS SCT\.

1. Choose **Settings**, **Global Settings**, **AWS Service Profiles**\.

1. Choose **Add New AWS Service Profile**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/dms/latest/userguide/images/snowball-AWSserviceprofile.png)

1. Add the following profile information\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_LargeDBs.SBS.configure-sct-to-use-snowball-edge.html)

1. After you have entered the information, choose **Test Connection** to verify that AWS SCT can connect to the Amazon S3 bucket\.

   The **OLTP Local & AWS DMS Data Migration** section in the pop\-up window should show all entries with a status of **Pass**\. If the test fails, the failure is probably because the account you are using is missing privileges to access the Amazon S3 bucket\. The example following lists privileges\. 

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Sid": "Stmt1607372356645",
               "Action": [
                   "cloudwatch:Get*",
                   "cloudwatch:List*",
                   "cloudwatch:PutMetricData",
                   "dms:*",
                   "dynamodb:BatchGetItem",
                   "dynamodb:BatchWriteItem",
                   "dynamodb:CreateTable",
                   "dynamodb:DeleteItem",
                   "dynamodb:DeleteTable",
                   "dynamodb:DescribeLimits",
                   "dynamodb:DescribeReservedCapacity",
                   "dynamodb:DescribeReservedCapacityOfferings",
                   "dynamodb:DescribeTable",
                   "dynamodb:DescribeTimeToLive",
                   "dynamodb:GetItem",
                   "dynamodb:ListTables",
                   "dynamodb:ListTagsOfResource",
                   "dynamodb:PutItem",
                   "dynamodb:Query",
                   "dynamodb:Scan",
                   "dynamodb:UpdateItem",
                   "dynamodb:UpdateTable",
                   "ec2:CreateNetworkInterface",
                   "ec2:CreateTags",
                   "ec2:DeleteNetworkInterface",
                   "ec2:DeleteTags",
                   "ec2:DescribeAvailabilityZones",
                   "ec2:DescribeInternetGateways",
                   "ec2:DescribeNetworkInterfaces",
                   "ec2:DescribeRouteTables",
                   "ec2:DescribeSecurityGroups",
                   "ec2:DescribeSubnets",
                   "ec2:DescribeVpcAttribute",
                   "ec2:DescribeVpcEndpoints",
                   "ec2:DescribeVpcs",
                   "ec2:ModifyNetworkInterfaceAttribute",
                   "glue:*",
                   "iam:AttachRolePolicy",
                   "iam:CreateRole",
                   "iam:GetRole",
                   "iam:GetRolePolicy",
                   "iam:GetUserStatus",
                   "iam:ListRolePolicies",
                   "iam:PassRole",
                   "iam:GetUser",
                   "iam:PutRolePolicy",
                   "iam:SimulatePrincipalPolicy",
                   "iam:SimulatePrincipalPolicyStatus",
                   "kms:DescribeKey",
                   "kms:ListAliases",
                   "lambda:CreateFunction",
                   "lambda:ListFunctions",
                   "logs:AssociateKmsKey",
                   "logs:CreateLogGroup",
                   "logs:CreateLogStream",
                   "logs:DescribeLogGroups",
                   "logs:DescribeLogStreams",
                   "logs:FilterLogEvents",
                   "logs:GetLogEvents",
                   "logs:PutLogEvents",
                   "s3:CreateBucket",
                   "s3:DeleteObject",
                   "s3:GetBucketAcl",
                   "s3:GetBucketLocation",
                   "s3:GetObject",
                   "s3:ListAllMyBuckets",
                   "s3:ListBucket",
                   "s3:PutObject",
                   "snowball:DescribeJob",
                   "snowball:GetJobManifest",
                   "snowball:GetJobUnlockCode",
                   "snowball:ListJobs"
               ],
               "Effect": "Allow",
               "Resource": "*"
           }
   ```

1. If the test passes, choose **OK** and then **OK** again to close the window and dialog box\.

1. Choose **Import job**, choose the AWS Snowball Edge job from the list, and then choose **OK**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/dms/latest/userguide/images/snowball-import-job.png)

Now configure AWS SCT to use the AWS Snowball Edge\. Enter the IP address of the Snowball Edge, the listening port on the device \(the default is 8080\), and the AWS Snowball Edge access keys and secret keys you retrieved earlier\. Choose **OK** to save your changes\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/dms/latest/userguide/images/snowball-configure.png)