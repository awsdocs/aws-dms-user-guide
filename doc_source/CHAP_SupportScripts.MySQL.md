# Diagnostic support scripts for MySQL\-compatible databases<a name="CHAP_SupportScripts.MySQL"></a>

Following, you can find the diagnostic support scripts available to analyze an on\-premises or Amazon RDS for MySQL\-compatible database in your AWS DMS migration configuration\. These scripts work with either a source or target endpoint\. The scripts are all written to run on the MySQL SQL command line\. 

For information about installing the MySQL client, see [ Installing MySQL Shell](https://dev.mysql.com/doc/mysql-shell/8.0/en/mysql-shell-install.html) in the MySQL documentation\. For information about using the MySQL client, see [ Using MySQL Shell Commands](https://dev.mysql.com/doc/mysql-shell/8.0/en/mysql-shell-configuring.html) in the MySQL documentation\.

Before running a script, ensure that the user account that you use has the necessary permissions to access your MySQL\-compatible database\. Use the following procedure to create a user account and provide the minimum permissions needed to run this script\.

**To set up a user account with the minimum permissions to run these scripts**

1. Create the user to run the scripts\.

   ```
   create user 'username'@'hostname' identified by password;
   ```

1. Grant the `select` command on databases to analyze them\.

   ```
   grant select on database-name.* to username;
   grant replication client on *.* to username;
   ```

1. 

   ```
   grant execute on procedure mysql.rds_show_configuration to username;
   ```

The following topics describe how to download, review, and run each support script available for a MySQL\-compatible database\. They also describe how to review and upload the script output to your AWS Support case\.

**Topics**
+ [awsdms\_support\_collector\_MySQL\.sql script](#CHAP_SupportScripts.MySQL.Awsdms_Support_Collector_MySQL_Script)

## awsdms\_support\_collector\_MySQL\.sql script<a name="CHAP_SupportScripts.MySQL.Awsdms_Support_Collector_MySQL_Script"></a>

Download the [https://d2pwp9zz55emqw.cloudfront.net/scripts/awsdms_support_collector_MySQL.sql](https://d2pwp9zz55emqw.cloudfront.net/scripts/awsdms_support_collector_MySQL.sql) script\.

This script collects information about your MySQL\-compatible database configuration\. Remember to verify the checksum on the script, and if the checksum verifies, review the SQL code in the script to comment out any of the code that you are uncomfortable running\. After you are satisfied with the integrity and content of the script, you can run it\.

Run the script after connecting to your database environment using the command line\.

**To run this script and upload the results to your support case**

1. Connect to your database using the following `mysql` command\.

   ```
   mysql -h hostname -P port -u username database-name
   ```

1. Run the script using the following mysql `source` command\.

   ```
   mysql> source awsdms_support_collector_MySQL_compatible_DB.sql
   ```

   Review the generated report and remove any information that you are uncomfortable sharing\. When the content is acceptable for you to share, upload the file to your AWS Support case\. For more information on uploading this file, see [Working with diagnostic support scripts in AWS DMS](CHAP_SupportScripts.md)\.

**Note**  
If you already have a user account with required privileges described in [Diagnostic support scripts for MySQL\-compatible databases ](#CHAP_SupportScripts.MySQL), you can use the existing user account as well to run the script\.
Remember to connect to your database before running the script\.
The script generates its output in text format\.
Keeping security best practices in mind, if you create a new user account only to execute this MySQL diagnostic support script, we recommend that you delete this user account after successful execution of the script\.