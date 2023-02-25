# Using secrets to access AWS Database Migration Service endpoints<a name="security_iam_secretsmanager"></a>

For AWS DMS, a *secret* is an encrypted key that you can use to represent a set of user credentials to authenticate, through *secret authentication*, the database connection for a supported AWS DMS source or target endpoint\. For an Oracle endpoint that also uses Oracle Automatic Storage Management \(ASM\), AWS DMS requires an additional secret that represents the user credentials to access Oracle ASM\.

You can create the secret or secrets that AWS DMS requires for secret authentication using AWS Secrets Manager, a service for securely creating, storing, and retrieving credentials to access applications, services, and IT resources in the cloud and on premise\. This includes support for automatic periodic rotation of the encrypted secret value without your intervention, providing an extra level of security for your credentials\. Enabling secret value rotation in AWS Secrets Manager also ensures that this secret value rotation happens without any effect on any database migration that relies on the secret\. For secretly authenticating an endpoint database connection, create a secret whose identity or ARN you assign to `SecretsManagerSecretId`, which you include in your endpoint settings\. For secretly authenticating Oracle ASM as part of an Oracle endpoint, create a secret whose identity or ARN you assign to `SecretsManagerOracleAsmSecretId`, which you also include in your endpoint settings\.

For more information on AWS Secrets Manager, see [What Is AWS Secrets Manager?](https://docs.aws.amazon.com/secretsmanager/latest/userguide/intro.html) in the *AWS Secrets Manager User Guide*\.

AWS DMS supports secret authentication for the following on\-premise or AWS\-managed databases on supported source and target endpoints:
+ Amazon DocumentDB
+ IBM Db2 LUW
+ Microsoft SQL Server
+ MongoDB
+ MySQL
+ Oracle
+ PostgreSQL
+ Amazon Redshift
+ SAP ASE

For connection to any of these databases, you have the choice of entering one of the following sets of values, but not both, as part of your endpoint settings:
+ Clear\-text values to authenticate the database connection using the `UserName`, `Password`, `ServerName`, and `Port` settings\. For an Oracle endpoint that also uses Oracle ASM, include additional clear\-text values to authenticate ASM using the `AsmUserName`, `AsmPassword`, and `AsmServerName` settings\.
+ Secret authentication using values for the `SecretsManagerSecretId` and `SecretsManagerAccessRoleArn` settings\. For an Oracle endpoint using Oracle ASM, include additional values for the `SecretsManagerOracleAsmSecretId` and `SecretsManagerOracleAsmAccessRoleArn` settings\. The secret values for these settings can include the following for: 
  + `SecretsManagerSecretId` – The full Amazon Resource Name \(ARN\), partial ARN, or friendly name of a secret that you have created for endpoint database access in the AWS Secrets Manager\.
  + `SecretsManagerAccessRoleArn` – The ARN of a secret access role that you have created in IAM to provide AWS DMS access to this `SecretsManagerSecretId` secret on your behalf\.
  + `SecretsManagerOracleAsmSecretId` – The full Amazon Resource Name \(ARN\), partial ARN, or friendly name of a secret that you have created for Oracle ASM access in the AWS Secrets Manager\.
  + `SecretsManagerOracleAsmAccessRoleArn` – The ARN of a secret access role that you have created in IAM to provide AWS DMS access to this `SecretsManagerOracleAsmSecretId` secret on your behalf\.
**Note**  
You can also use a single secret access role to provide AWS DMS access to both the `SecretsManagerSecretId` secret and the `SecretsManagerOracleAsmSecretId` secret\. If you create this single secret access role for both secrets, ensure that you assign the same ARN for this access role to both `SecretsManagerAccessRoleArn` and `SecretsManagerOracleAsmAccessRoleArn`\. For example, if your secret access role for both secrets has its ARN assigned to the variable, `ARN2xsecrets`, you can set these ARN settings as follows:  

  ```
  SecretsManagerAccessRoleArn = ARN2xsecrets;
  SecretsManagerOracleAsmAccessRoleArn = ARN2xsecrets;
  ```

  For more information on creating these values, see [Using the AWS Management Console to create a secret and secret access role](#security_iam_secretsmanager.console)\.

After you have created and specified the required secret and secret access\-role endpoint settings for your endpoints, update the permissions on the user accounts that will run the `CreateEndpoint` or `ModifyEndpoint` API request with these secret details\. Ensure that these account permissions include the `IAM:GetRole` permission on the secret access role and the `SecretsManager:DescribeSecret` permission on the secret\. AWS DMS requires these permissions to validate both the access role and its secret\.

**To provide and verify required user permissions**

1. Sign in to the AWS Management Console and open the AWS Identity and Access Management console at [https://console.aws.amazon.com/iam/](https://console.aws.amazon.com/iam/)\.

1. Choose **Users**, then select the **User ID** used for making `CreateEndpoint` and `ModifyEndpoint` API calls\.

1. From the **Permissions **tab, choose **\{\} JSON**\.

1. Make sure the user has the permissions shown following\.

   ```
   {
   	"Statement": [{
   			"Effect": "Allow",
   			"Action": [
   				"iam:GetRole",
   				"iam:PassRole"
   			],
   			"Resource": "SECRET_ACCESS_ROLE_ARN"
   		},
   		{
   			"Effect": "Allow",
   			"Action": "secretsmanager:DescribeSecret",
   			"Resource": "SECRET_ARN"
   		}
   	]
   }
   ```

1. If the user doesn't have those permission, add the permissions\.

1. If you're using an IAM Role for making DMS API calls, repeat the steps above for the respective role\.

1. Open a terminal and use the AWS CLI to validate that permissions are given correctly by assuming the Role or User used above\.

   1. Validate user’s permission on the SecretAccessRole using the IAM `get-role` command\.

      ```
      aws iam get-role --role-name ROLE_NAME
      ```

      Replace *ROLE\_NAME* with the name of `SecretsManagerAccessRole`\.

      If the command returns an error message, make sure the permissions were given correctly\.

   1. Validate user’s permission on the secret using the Secrets Manager `describe-secret` command\.

      ```
      aws secretsmanager describe-secret --secret-id SECRET_NAME OR SECRET_ARN --region=REGION_NAME
      ```

      User can be the friendly name, partial ARN or the full ARN\. For more information, see [describe\-secret](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/describe-secret.html)\.

      If the command returns an error message, make sure the permissions were given correctly\.

## Using the AWS Management Console to create a secret and secret access role<a name="security_iam_secretsmanager.console"></a>

You can use the AWS Management Console to create a secret for endpoint authentication and to create the policy and role to allow AWS DMS to access the secret on your behalf\.

**To create a secret using the AWS Management Console that AWS DMS can use to authenticate a database for source and target endpoint connections**

1. Sign in to the AWS Management Console and open the AWS Secrets Manager console at [https://console.aws.amazon.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. Choose **Store a new secret**\.

1. Under **Select secret type** on the **Store a new secret** page, choose **Other type of secrets**, then choose **Plaintext**\.
**Note**  
This is the only place that you need to enter clear text credentials to connect to your endpoint database from this point forward\.

1. In the **Plaintext** field: 
   + For a secret whose identity you assign to `SecretsManagerSecretId`, enter the following JSON structure\.

     ```
     {
       "username": db_username,
       "password": db_user_password,
       "port": db_port_number,
       "host": db_server_name
     }
     ```
**Note**  
This is the minimum list of JSON members required to authenticate the endpoint database\. You can add any additional JSON endpoint settings as JSON members in all lower case that you want\. However, AWS DMS ignores any additional JSON members for endpoint authentication\.

     Here, `db_username` is the name of the user accessing the database, `db_user_password` is the password of the database user, `db_port_number` is the port number to access the database, and `db_server_name` is the database server name \(address\) on the web, as in the following example\.

     ```
     {
       "username": "admin",
       "password": "some_password",
       "port": "8190",
       "host": "oracle101.abcdefghij.us-east-1.rds.amazonaws.com"
     }
     ```
   + For a secret whose identity you assign to `SecretsManagerOracleAsmSecretId`, enter the following JSON structure\.

     ```
     {
       "asm_user": asm_username,
       "asm_password": asm_user_password,
       "asm_server": asm_server_name
     }
     ```
**Note**  
This is the minimum list of JSON members required to authenticate Oracle ASM for an Oracle endpoint\. It is also the complete list that you can specify based on the available Oracle ASM endpoint settings\.

     Here, `asm_username` is the name of the user accessing Oracle ASM, `asm_user_password` is the password of the Oracle ASM user, and `asm_server_name` is the Oracle ASM server name \(address\) on the web, including the port, as in the following example\.

     ```
     { 
       "asm_user": "oracle_asm_user", 
       "asm_password": "oracle_asm_password",
       "asm_server": "oracle101.abcdefghij.us-east-1.rds.amazonaws.com:8190/+ASM" 
     }
     ```

1. Select an AWS KMS encryption key to encrypt the secret\. You can accept the default encryption key created for your service by AWS Secrets Manager or select a AWS KMS key that you create\.

1. Specify a name to reference this secret and an optional description\. This is the friendly name that you use as the value for `SecretsManagerSecretId` or `SecretsManagerOracleAsmSecretId`\.

1. If you want to enable automatic rotation in the secret, you need to select or create an AWS Lambda function with permission to rotate the credentials for the secret as described\. However, before setting automatic rotation to use your Lambda function, ensure that the configuration settings for the function add the following four characters to the value of the `EXCLUDE_CHARACTERS` environment variable\.

   ```
   ;.:+
   ```

   AWS DMS doesn't allow these characters in passwords used for endpoint credentials\. Configuring your Lambda function to exclude them prevents AWS Secrets Manager from generating these characters as part of its rotated password values\. After you set automatic rotation to use your Lambda function, AWS Secrets Manager immediately rotates the secret to validate your secret configuration\.
**Note**  
Depending on your database engine configuration, your database might not fetch the rotated credentials\. In this case, you need to manually restart the task to refresh the credentials\.

1. Review and store your secret in AWS Secrets Manager\. You can then look up each secret by its friendly name in AWS Secrets Manager, then retrieve the secret ARN as the value for `SecretsManagerSecretId` or `SecretsManagerOracleAsmSecretId` as appropriate to authenticate access to your endpoint database connection and Oracle ASM \(if used\)\.

**To create the secret access policy and role to set your `SecretsManagerAccessRoleArn` or `SecretsManagerOracleAsmAccessRoleArn`, which allows AWS DMS to access AWS Secrets Manager to access your appropriate secret**

1. Sign in to the AWS Management Console and open the AWS Identity and Access Management \(IAM\) console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. Choose **Policies**, then choose **Create policy**\.

1. Choose **JSON** and enter the following policy to enable access to and decryption of your secret\.

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Effect": "Allow",
               "Action": "secretsmanager:GetSecretValue",
               "Resource": secret_arn,
           },
           {
                "Effect": "Allow",
                "Action": [
                           "kms:Decrypt",
                           "kms:DescribeKey"
                         ],
                "Resource": kms_key_arn,
           }
        ]
   }
   ```

   Here, `secret_arn` is the ARN of your secret, which you can get from either `SecretsManagerSecretId` or `SecretsManagerOracleAsmSecretId` as appropriate, and `kms_key_arn` is the ARN of the AWS KMS key that you are using to encrypt your secret, as in the following example\.

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Effect": "Allow",
               "Action": "secretsmanager:GetSecretValue",
               "Resource": "arn:aws:secretsmanager:us-east-2:123456789012:secret:MySQLTestSecret-qeHamH"
           },
           {
                "Effect": "Allow",
                "Action": [
                           "kms:Decrypt",
                           "kms:DescribeKey"
                         ],
                "Resource": "arn:aws:kms:us-east-2:123456789012:key/761138dc-0542-4e58-947f-4a3a8458d0fd"
           }
        ]
   }
   ```
**Note**  
If you use the default encryption key created by AWS Secrets Manager, you do not have to specify the AWS KMS permissions for `kms_key_arn`\.  
If you want your policy to provide access to both secrets, simply specify an additional JSON resource object for the other *secret\_arn*\.

1. Review and create the policy with a friendly name and optional description\.

1. Choose **Roles**, then choose **Create role**\.

1. Choose **AWS service** as the type of trusted entity\.

1. Choose **DMS** from the list of services as the trusted service, then choose **Next: Permissions**\.

1. Look up and attach the policy you created in step 4, then proceed through adding any tags and review your role\. At this point, edit the trust relationships for the role to use your AWS DMS regional service principal as the trusted entity\. This principal has the following format\.

   ```
   dms.region-name.amazonaws.com
   ```

   Here, *`region-name`* is the name of your region, such as `us-east-1`\. Thus, an AWS DMS regional service principal for this region follows\.

   ```
   dms.us-east-1.amazonaws.com
   ```

1. After editing the trusted entity for the role, create the role with a friendly name and optional description\. You can now look up your new role by its friendly name in IAM, then retrieve the role ARN as the `SecretsManagerAccessRoleArn` or `SecretsManagerOracleAsmAccessRoleArn` value to authenticate your endpoint database connection\.

**To use secrets manager with a replication instance in a private subnet**

1. Create a secret manager VPC endpoint and note the DNS for the endpoint\. For more information about creating a secrets manager VPC endpoint, see [Connecting to Secrets Manager through a VPC endpoint](https://docs.aws.amazon.com/secretsmanager/latest/userguide/vpc-endpoint-overview.html#vpc-endpoint) []()in the *AWS Secrets Manager User Guide\.*

1. Attach the replication instance security group to the secret manager VPC endpoint\.

1. For the replication instance security group egress rules, allow all traffic for destination `0.0.0.0/0`\.

1. Set the endpoint extra connection attribute, `secretsManagerEndpointOverride=secretsManager endpoint DNS` to provide the secret manager VPC endpoint DNS, as shown in the following example\.

   ```
   secretsManagerEndpointOverride=vpce-1234a5678b9012c-12345678.secretsmanager.eu-west-1.vpce.amazonaws.com
   ```