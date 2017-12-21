# Setting Up<a name="CHAP_SettingUp"></a>

Before you use AWS Database Migration Service \(AWS DMS\) for the first time, you'll need to complete the following tasks:

1. [Sign Up for AWS](#CHAP_SettingUp.SignUp)

1. [Create an IAM User](#CHAP_SettingUp.IAM)

1. [Determine Requirements](#CHAP_SettingUp.Requirements)

## Sign Up for AWS<a name="CHAP_SettingUp.SignUp"></a>

When you sign up for Amazon Web Services \(AWS\), your AWS account is automatically signed up for all services in AWS, including AWS DMS\. You are charged only for the services that you use\.

With AWS DMS, you pay only for the resources you use\. The AWS DMS replication instance that you create will be live \(not running in a sandbox\)\. You will incur the standard AWS DMS usage fees for the instance until you terminate it\. For more information about AWS DMS usage rates, see the [AWS DMS product page](http://aws.amazon.com/dms)\. If you are a new AWS customer, you can get started with AWS DMS for free; for more information, see [AWS Free Usage Tier](http://aws.amazon.com/free/)\.

If you have an AWS account already, skip to the next task\. If you don't have an AWS account, use the following procedure to create one\.

**To create an AWS account**

1. Open [https://aws\.amazon\.com/](https://aws.amazon.com/), and then choose **Create an AWS Account**\.
**Note**  
This might be unavailable in your browser if you previously signed into the AWS Management Console\. In that case, choose **Sign in to a different account**, and then choose **Create a new AWS account**\.

1. Follow the online instructions\.

   Part of the sign\-up procedure involves receiving a phone call and entering a PIN using the phone keypad\.

Note your AWS account number, because you'll need it for the next task\.

## Create an IAM User<a name="CHAP_SettingUp.IAM"></a>

Services in AWS, such as AWS DMS, require that you provide credentials when you access them, so that the service can determine whether you have permission to access its resources\. The console requires your password\. You can create access keys for your AWS account to access the command line interface or API\. However, we don't recommend that you access AWS using the credentials for your AWS account; we recommend that you use AWS Identity and Access Management \(IAM\) instead\. Create an IAM user, and then add the user to an IAM group with administrative permissions or and grant this user administrative permissions\. You can then access AWS using a special URL and the credentials for the IAM user\.

If you signed up for AWS but have not created an IAM user for yourself, you can create one using the IAM console\.

**To create an IAM user for yourself and add the user to an Administrators group**

1. Use your AWS account email address and password to sign in to the [AWS Management Console](https://console.aws.amazon.com/) as the *[AWS account root user](http://docs.aws.amazon.com/IAM/latest/UserGuide/id_root-user.html)*\.

1. In the navigation pane of the console, choose **Users**, and then choose **Add user**\.

1. For **User name**, type ** Administrator**\.

1. Select the check box next to **AWS Management Console access**, select **Custom password**, and then type the new user's password in the text box\. You can optionally select **Require password reset** to force the user to select a new password the next time the user signs in\.

1. Choose **Next: Permissions**\.

1. On the **Set permissions for user** page, choose **Add user to group**\.

1. Choose **Create group**\.

1. In the **Create group** dialog box, type ** Administrators**\.

1. For **Filter**, choose **Job function**\.

1. In the policy list, select the check box for ** AdministratorAccess**\. Then choose **Create group**\.

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

## Determine Requirements<a name="CHAP_SettingUp.Requirements"></a>

A database migration requires thorough testing and a sound backup strategy\. To successfully migrate a database using AWS DMS, you must have expertise, time, and knowledge about your source database, your network, the AWS network, your requirements, and your target database schema, as described following\.

**Available Regions**  
Ensure that AWS DMS is available in the region that you need\. AWS DMS is currently available in the following regions:      
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_SettingUp.html)

**Expertise**  
A database migration requires expertise in several areas\.  

+ You should have a thorough knowledge of the database engine you are migrating from and the database engine you are migrating to\.

+ You should understand both the network you are migrating from and how that network connects to AWS\. 

+ You should have a thorough knowledge of the AWS service you are migrating to and the AWS Identity and Access Management \(IAM\) service\.

+ In most cases, it helps if you have an understanding of software architecture\.

**Time**  
Migration projects can take from two weeks to several months to complete\.  

+ A successful migration can require several iterations\.

+ The migration process can take more time than you anticipate\.

+ Do you have a hard date on when your database migration must be completed?

+ Migration planning can often take longer than the migration itself\.

**Knowledge of your source database**  
The size of your database and the data types it contains can have a dramatic impact on your migration\.  

+ How many schemas and tables does your database contain?

+ Does your database have any very large tables \(more than 5 GB in size\)?

+ Do you know what the transaction boundaries look like?

+ Does your database have data types that AWS DMS does not support?

+ Do you have LOBs in your tables? If so, how large are the LOBs?

+ Do your tables with LOBs have primary keys?

+ How busy is your source database?

+ What kind of users, roles, and permissions do you have on the source database?

+ When was the last time you vacuumed or compacted your source database?

**Knowledge of your network and the AWS network**  
You must connect the network that the source database uses to AWS\.  

+ How will your database access the AWS network?

+ Which Amazon Virtual Private Cloud \(Amazon VPC\) will you use?

+ Which Amazon Elastic Compute Cloud \(Amazon EC2\) security group will you use?

+ How much bandwidth will you need to move all your data?

**An understanding of your requirements**  
The following questions make up much of your migration planning:  

+ How much downtime can you afford?

+ Do you need the source database to be available after migration?

+ Do you know why you preferred one target database engine over another database engine?

+ What are your high availability requirements?

+ Does all the data needs to be migrated?

+ Does all the data need to be migrated to the same database?

+ Do you understand the benefits of using Amazon RDS \(automated backups, high availability, and so on\)?

+ Do you understand the limits of using Amazon RDS \(storage size, admin user, and so on\)?

+ What happens to your application during the migration process?

+ What is your contingency plan if the migration is unsuccessful?

**Knowledge of your target database schema**  
AWS DMS creates only tables and primary keys in the target database\. You must recreate any other database requirements\.  

+ You can use the AWS Schema Conversion Tool \(AWS SCT\) to migrate a database schema\. It works best when migrating from one database engine to a different database engine\. For more information on the AWS Schema Conversion Tool, see [ AWS Schema Conversion Tool ](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_SchemaConversionTool.Installing.html)\.

+ The AWS SCT does not support schema conversions from and to the same database engine type\. If you need to convert a schema when going to the same database engine, use the database engine's native tools for the conversion\.

+ The AWS SCT does not currently support orchestration\.

+ Postpone any schema changes until after the migration\.