# Setting Up for AWS Database Migration Service<a name="CHAP_SettingUp"></a>

Before you use AWS Database Migration Service \(AWS DMS\) for the first time, complete the following tasks:

1. [Sign Up for AWS](#CHAP_SettingUp.SignUp)

1. [Create an IAM User](#CHAP_SettingUp.IAM)

1. [Migration Planning for AWS Database Migration Service](#CHAP_SettingUp.MigrationPlanning)

## Sign Up for AWS<a name="CHAP_SettingUp.SignUp"></a>

When you sign up for Amazon Web Services \(AWS\), your AWS account is automatically signed up for all services in AWS, including AWS DMS\. You are charged only for the services that you use\.

With AWS DMS, you pay only for the resources you use\. The AWS DMS replication instance that you create will be live \(not running in a sandbox\)\. You will incur the standard AWS DMS usage fees for the instance until you terminate it\. For more information about AWS DMS usage rates, see the [AWS DMS product page](http://aws.amazon.com/dms)\. If you are a new AWS customer, you can get started with AWS DMS for free; for more information, see [AWS Free Usage Tier](http://aws.amazon.com/free/)\.

If you close your AWS account, all AWS DMS resources and configurations associated with your account are deleted after two days\. These resources include all replication instances, source and target endpoint configuration, replication tasks, and SSL certificates\. If after two days you decide to use AWS DMS again, you recreate the resources you need\.

If you have an AWS account already, skip to the next task\.

If you do not have an AWS account, use the following procedure to create one\.

**To sign up for AWS**

1. Open [https://aws\.amazon\.com/](https://aws.amazon.com/) and choose **Create an AWS Account**\.

1. Follow the online instructions\.

Note your AWS account number, because you'll need it for the next task\.

## Create an IAM User<a name="CHAP_SettingUp.IAM"></a>

Services in AWS, such as AWS DMS, require that you provide credentials when you access them, so that the service can determine whether you have permission to access its resources\. The console requires your password\. You can create access keys for your AWS account to access the command line interface or API\. However, we don't recommend that you access AWS using the credentials for your AWS account; we recommend that you use AWS Identity and Access Management \(IAM\) instead\. Create an IAM user, and then add the user to an IAM group with administrative permissions or and grant this user administrative permissions\. You can then access AWS using a special URL and the credentials for the IAM user\.

If you signed up for AWS but have not created an IAM user for yourself, you can create one using the IAM console\.

**To create an IAM user for yourself and add the user to an Administrators group**

1. Use your AWS account email address and password to sign in as the *[AWS account root user](http://docs.aws.amazon.com/IAM/latest/UserGuide/id_root-user.html)* to the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.
**Note**  
We strongly recommend that you adhere to the best practice of using the **Administrator** IAM user below and securely lock away the root user credentials\. Sign in as the root user only to perform a few [account and service management tasks](http://docs.aws.amazon.com/general/latest/gr/aws_tasks-that-require-root.html)\.

1. In the navigation pane of the console, choose **Users**, and then choose **Add user**\.

1. For **User name**, type **Administrator**\.

1. Select the check box next to **AWS Management Console access**, select **Custom password**, and then type the new user's password in the text box\. You can optionally select **Require password reset** to force the user to create a new password the next time the user signs in\.

1. Choose **Next: Permissions**\.

1. On the **Set permissions** page, choose **Add user to group**\.

1. Choose **Create group**\.

1. In the **Create group** dialog box, for **Group name** type **Administrators**\.

1. For **Filter policies**, select the check box for **AWS managed \- job function**\.

1. In the policy list, select the check box for **AdministratorAccess**\. Then choose **Create group**\.

1. Back in the list of groups, select the check box for your new group\. Choose **Refresh** if necessary to see the group in the list\.

1. Choose **Next: Review** to see the list of group memberships to be added to the new user\. When you are ready to proceed, choose **Create user**\.

You can use this same process to create more groups and users, and to give your users access to your AWS account resources\. To learn about using policies to restrict users' permissions to specific AWS resources, go to [Access Management](http://docs.aws.amazon.com/IAM/latest/UserGuide/access.html) and [Example Policies](http://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_examples.html)\.

To sign in as this new IAM user, sign out of the AWS console, then use the following URL, where *your\_aws\_account\_id* is your AWS account number without the hyphens \(for example, if your AWS account number is `1234-5678-9012`, your AWS account ID is `123456789012`\):

```
https://your_aws_account_id.signin.aws.amazon.com/console/
```

Enter the IAM user name and password that you just created\. When you're signed in, the navigation bar displays "*your\_user\_name* @ *your\_aws\_account\_id*"\.

If you don't want the URL for your sign\-in page to contain your AWS account ID, you can create an account alias\. On the IAM dashboard, choose **Customize** and type an alias, such as your company name\. To sign in after you create an account alias, use the following URL\.

```
https://your_account_alias.signin.aws.amazon.com/console/
```

To verify the sign\-in link for IAM users for your account, open the IAM console and check under **AWS Account Alias** on the dashboard\.

## Migration Planning for AWS Database Migration Service<a name="CHAP_SettingUp.MigrationPlanning"></a>

When planning a database migration using AWS Database Migration Service, consider the following:
+ You will need to configure a network that connects your source and target databases to a AWS DMS replication instance\. This can be as simple as connecting two AWS resources in the same VPC as the replication instance to more complex configurations such as connecting an on\-premises database to an Amazon RDS DB instance over VPN\. For more information, see [Network Configurations for Database Migration](CHAP_ReplicationInstance.VPC.md#CHAP_ReplicationInstance.VPC.Configurations)
+ **Source and Target Endpoints** – You will need to know what information and tables in the source database need to be migrated to the target database\. AWS DMS supports basic schema migration, including the creation of tables and primary keys\. However, AWS DMS doesn't automatically create secondary indexes, foreign keys, user accounts, and so on in the target database\. Note that, depending on your source and target database engine, you may need to set up supplemental logging or modify other settings for a source or target database\. See the [Sources for Data Migration](CHAP_Source.md) and [Targets for Data Migration](CHAP_Target.md) sections for more information\.
+ **Schema/Code Migration** – AWS DMS doesn't perform schema or code conversion\. You can use tools such as Oracle SQL Developer, MySQL Workbench, or pgAdmin III to convert your schema\. If you want to convert an existing schema to a different database engine, you can use the AWS Schema Conversion Tool\. It can create a target schema and also can generate and create an entire schema: tables, indexes, views, and so on\. You can also use the tool to convert PL/SQL or TSQL to PgSQL and other formats\. For more information on the AWS Schema Conversion Tool, see [ AWS Schema Conversion Tool ](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_SchemaConversionTool.Installing.html)\.
+ **Unsupported Data Types** – Some source data types need to be converted into the equivalent data types for the target database\. See the source or target section for your data store to find more information on supported data types\. 