# PostgreSQL diagnostic support scripts<a name="CHAP_SupportScripts.PostgreSQL"></a>

Following, you can find the diagnostic support scripts available to analyze any PostgreSQL RDBMS \(on\-premises, Amazon RDS, or Aurora PostgreSQL\) in your AWS DMS migration configuration\. These scripts work with either a source or target endpoint\. The scripts are all written to run in the psql command\-line utility\. 

Before running these scripts, ensure that the user account that you use has the following necessary permissions to access any PostgreSQL RDBMS:
+ PostgreSQL 10\.x or later – A user account with execute permission on the `pg_catalog.pg_ls_waldir` function\.
+ PostgreSQL 9\.x or earlier – A user account with default permissions\.

We recommend using an existing account with the appropriate permissions to run these scripts\.

If you need to create a new user account or grant permissions to an existing account to run these scripts, you can execute the following SQL commands for any PostgreSQL RDBMS based on the PostgreSQL version\.

**To grant account permissions to run these scripts for a PostgreSQL 10\.x or later database**
+ Do one of the following:
  + For a new user account, run the following\.

    ```
    CREATE USER script_user WITH PASSWORD 'password';
    GRANT EXECUTE ON FUNCTION pg_catalog.pg_ls_waldir TO script_user;
    ```
  + For an existing user account, run the following\.

    ```
    GRANT EXECUTE ON FUNCTION pg_catalog.pg_ls_waldir TO script_user;
    ```

**To grant account permissions to run these scripts for a PostgreSQL 9\.x or earlier database**
+ Do one of the following:
  + For a new user account, run the following with default permissions\.

    ```
    CREATE USER script_user WITH PASSWORD password;
    ```
  + For an existing user account, use the existing permissions\.

**Note**  
These scripts do not support certain functionality related to finding WAL size for PostgreSQL 9\.x and earlier databases\. For more information, work with AWS Support\.

The following topics describe how to download, review, and run each support script available for PostgreSQL They also describe how to review and upload the script output to your AWS Support case\.

**Topics**
+ [awsdms\_support\_collector\_postgres\.sql script](#CHAP_SupportScripts.PostgreSQL.Awsdms_Support_Collector_PostgreSQL_Script)

## awsdms\_support\_collector\_postgres\.sql script<a name="CHAP_SupportScripts.PostgreSQL.Awsdms_Support_Collector_PostgreSQL_Script"></a>

Download the [https://d2pwp9zz55emqw.cloudfront.net/scripts/awsdms_support_collector_postgres.sql](https://d2pwp9zz55emqw.cloudfront.net/scripts/awsdms_support_collector_postgres.sql) script\.

This script collects information about your PostgreSQL database configuration\. Remember to verify the checksum on the script\. If the checksum verifies, review the SQL code in the script to comment out any of the code that you are uncomfortable running\. After you are satisfied with the integrity and content of the script, you can run it\.

**Note**  
You can run this script with psql client version 10 or later\.

You can use the following procedures to run this script either from your database environment or from the command line\. In either case, you can then upload your file to AWS Support later\.

**To run this script and upload the results to your support case**

1. Do one of the following:
   + Run the script from your database environment using the following psql command line\.

     ```
     dbname=# \i awsdms_support_collector_postgres.sql
     ```

     At the following prompt, enter the name of only one of the schemas that you want to migrate\.

     At the following prompt, enter the name of the user \(`script_user`\) that you have defined to connect to the database\.
   + Run the following script directly from the command line\. This option avoids any prompts prior to script execution\.

     ```
     psql -h database-hostname -p port -U script_user -d database-name -f awsdms_support_collector_postgres.sql
     ```

1. Review the output HTML file and remove any information that you are uncomfortable sharing\. When the HTML is acceptable for you to share, upload the file to your AWS Support case\. For more information on uploading this file, see [Working with diagnostic support scripts in AWS DMS](CHAP_SupportScripts.md)\.