# Creating the IAM Roles to Use With the AWS CLI and AWS DMS API<a name="CHAP_Security.APIRole"></a>

If you use the AWS CLI or the AWS DMS API for your database migration, you must add three IAM roles to your AWS account before you can use the features of AWS DMS\. Two of these are `dms-vpc-role` and `dms-cloudwatch-logs-role`\. If you use Amazon Redshift as a target database, you must also add the IAM role `dms-access-for-endpoint` to your AWS account\.

Updates to managed policies are automatic\. If you are using a custom policy with the IAM roles, be sure to periodically check for updates to the managed policy in this documentation\. You can view the details of the managed policy by using a combination of the `get-policy` and `get-policy-version` commands\.

For example, the following `get-policy` command retrieves information on the role\.

```
aws iam get-policy --policy-arn arn:aws:iam::aws:policy/service-role/AmazonDMSVPCManagementRole
```

The information returned from the command is as follows\.

```
{
    "Policy": {
        "PolicyName": "AmazonDMSVPCManagementRole", 
        "Description": "Provides access to manage VPC settings for AWS managed customer configurations", 
        "CreateDate": "2015-11-18T16:33:19Z", 
        "AttachmentCount": 1, 
        "IsAttachable": true, 
        "PolicyId": "ANPAJHKIGMBQI4AEFFSYO", 
        "DefaultVersionId": "v3", 
        "Path": "/service-role/", 
        "Arn": "arn:aws:iam::aws:policy/service-role/AmazonDMSVPCManagementRole", 
        "UpdateDate": "2016-05-23T16:29:57Z"
    }
}
```

The following `get-policy-version` command retrieves policy information\.

```
aws iam get-policy-version --policy-arn arn:aws:iam::aws:policy/service-role/AmazonDMSVPCManagementRole --version-id v3
```

The information returned from the command is as follows\.

```
{
    "PolicyVersion": {
        "CreateDate": "2016-05-23T16:29:57Z", 
        "VersionId": "v3", 
        "Document": {
        "Version": "2012-10-17", 
        "Statement": [
            {
                "Action": [
                    "ec2:CreateNetworkInterface", 
                    "ec2:DescribeAvailabilityZones", 
                    "ec2:DescribeInternetGateways", 
                    "ec2:DescribeSecurityGroups", 
                    "ec2:DescribeSubnets", 
                    "ec2:DescribeVpcs", 
                    "ec2:DeleteNetworkInterface", 
                    "ec2:ModifyNetworkInterfaceAttribute"
                ], 
                "Resource": "*", 
                "Effect": "Allow"
            }
        ]
    }, 
    "IsDefaultVersion": true
    }
}
```

The same commands can be used to get information on the `AmazonDMSCloudWatchLogsRole` and the `AmazonDMSRedshiftS3Role` managed policy\.

**Note**  
If you use the AWS DMS console for your database migration, these roles are added to your AWS account automatically\.

The following procedures create the `dms-vpc-role`, `dms-cloudwatch-logs-role`, and `dms-access-for-endpoint` IAM roles\.

**To create the `dms-vpc-role` IAM role for use with the AWS CLI or AWS DMS API**

1.  Create a JSON file with the IAM policy following\. Name the JSON file `dmsAssumeRolePolicyDocument.json`\. 

   ```
   {
      "Version": "2012-10-17",
      "Statement": [
      {
        "Effect": "Allow",
        "Principal": {
           "Service": "dms.amazonaws.com"
        },
      "Action": "sts:AssumeRole"
      }
    ]
   }
   ```

    Create the role using the AWS CLI using the following command\.

   ```
   aws iam create-role --role-name dms-vpc-role --assume-role-policy-document file://dmsAssumeRolePolicyDocument.json                    
   ```

1.  Attach the `AmazonDMSVPCManagementRole` policy to `dms-vpc-role` using the following command\.

   ```
   aws iam attach-role-policy --role-name dms-vpc-role --policy-arn arn:aws:iam::aws:policy/service-role/AmazonDMSVPCManagementRole                    
   ```

**To create the `dms-cloudwatch-logs-role` IAM role for use with the AWS CLI or AWS DMS API**

1.  Create a JSON file with the IAM policy following\. Name the JSON file `dmsAssumeRolePolicyDocument2.json`\. 

   ```
   {
      "Version": "2012-10-17",
      "Statement": [
      {
        "Effect": "Allow",
        "Principal": {
           "Service": "dms.amazonaws.com"
        },
      "Action": "sts:AssumeRole"
      }
    ]
   }
   ```

    Create the role using the AWS CLI using the following command\.

   ```
   aws iam create-role --role-name dms-cloudwatch-logs-role --assume-role-policy-document file://dmsAssumeRolePolicyDocument2.json                    
   ```

1.  Attach the `AmazonDMSCloudWatchLogsRole` policy to `dms-cloudwatch-logs-role` using the following command\.

   ```
   aws iam attach-role-policy --role-name dms-cloudwatch-logs-role --policy-arn arn:aws:iam::aws:policy/service-role/AmazonDMSCloudWatchLogsRole                    
   ```

If you use Amazon Redshift as your target database, you must create the IAM role `dms-access-for-endpoint` to provide access to Amazon S3 \(S3\)\.

**To create the `dms-access-for-endpoint` IAM role for use with Amazon Redshift as a target database**

1. Create a JSON file with the IAM policy following\. Name the JSON file `dmsAssumeRolePolicyDocument3.json`\. 

   ```
    {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Sid": "1",
         "Effect": "Allow",
         "Principal": {
           "Service": "dms.amazonaws.com"
         },
         "Action": "sts:AssumeRole"
       },
       {
         "Sid": "2",
         "Effect": "Allow",
         "Principal": {
           "Service": "redshift.amazonaws.com"
         },
         "Action": "sts:AssumeRole"
       }
     ]
   }
   ```

1.  Create the role using the AWS CLI using the following command\.

   ```
     aws iam create-role --role-name dms-access-for-endpoint --assume-role-policy-document file://dmsAssumeRolePolicyDocument3.json                   
   ```

1.  Attach the `AmazonDMSRedshiftS3Role` policy to `dms-access-for-endpoint` role using the following command\.

   ```
   aws iam attach-role-policy --role-name dms-access-for-endpoint \
       --policy-arn arn:aws:iam::aws:policy/service-role/AmazonDMSRedshiftS3Role
   ```

You should now have the IAM policies in place to use the AWS CLI or AWS DMS API\.