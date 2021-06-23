# SQL Server diagnostic support scripts<a name="CHAP_SupportScripts.SQLServer"></a>

Following, you can find a description of the diagnostic support scripts available to analyze an on\-premises or Amazon RDS for SQL Server database in your AWS DMS migration configuration\. These scripts work with either a source or target endpoint\. For an on\-premises database, run these scripts in the sqlcmd command\-line utility\. For more information on using this utility, see [sqlcmd \- Use the utility](https://docs.microsoft.com/en-us/sql/ssms/scripting/sqlcmd-use-the-utility?view=sql-server-ver15) in the Microsoft documentation\. 

For an Amazon RDS database, you can't connect using the sqlcmd command\-line utility\. Instead, run these scripts using any client tool that connects to Amazon RDS SQL Server\.

Before running the script, ensure that the user account that you use has the necessary permissions to access your SQL Server database\. For both an on\-premises and an Amazon RDS database, you can use the same permissions you use to access your SQL Server database without the `SysAdmin` role\.

**To set up the minimum permissions to run for an on\-premises SQL Server database**

1. Create a new SQL Server account with password authentication using SQL Server Management Studio \(SSMS\), for example `on-prem-user`\.

1. In the **User Mappings** section of SSMS, choose the **MSDB** and **MASTER** databases \(which gives public permission\), and assign the `DB_OWNER` role to the database where you want to run the script\.

1. Open the context \(right\-click\) menu for the new account, and choose **Security** to explicitly grant the `Connect SQL` privilege\. 

1. Run the grant commands following\.

   ```
   GRANT VIEW SERVER STATE TO on-prem-user;
   USE MSDB;
   GRANT SELECT ON MSDB.DBO.BACKUPSET TO on-prem-user;
   GRANT SELECT ON MSDB.DBO.BACKUPMEDIAFAMILY TO on-prem-user;
   GRANT SELECT ON MSDB.DBO.BACKUPFILE TO on-prem-user;
   ```

**To run with the minimum permissions for an Amazon RDS SQL Server database**

1. Create a new SQL Server account with password authentication using SQL Server Management Studio \(SSMS\), for example `rds-user`\.

1. In the **User Mappings** section of SSMS, choose the **MSDB** database \(which gives public permission\), and assign the `DB_OWNER` role to the database where you want to run the script\.

1. Open the context \(right\-click\) menu for the new account, and choose **Security** to explicitly grant the `Connect SQL` privilege\.

1. Run the grant commands following\.

   ```
   GRANT VIEW SERVER STATE TO rds-user;
   USE MSDB;
   GRANT SELECT ON MSDB.DBO.BACKUPSET TO rds-user;
   GRANT SELECT ON MSDB.DBO.BACKUPMEDIAFAMILY TO rds-user;
   GRANT SELECT ON MSDB.DBO.BACKUPFILE TO rds-user;
   ```

The following topics describe how to download, review, and run each support script available for SQL Server\. They also describe how to review and upload the script output to your AWS Support case\.

**Topics**
+ [awsdms\_support\_collector\_sql\_server\.sql script](#CHAP_SupportScripts.SQLServer.Awsdms_Support_Collector_SQLServer_Script)

## awsdms\_support\_collector\_sql\_server\.sql script<a name="CHAP_SupportScripts.SQLServer.Awsdms_Support_Collector_SQLServer_Script"></a>

Download the [https://d2pwp9zz55emqw.cloudfront.net/scripts/awsdms_support_collector_sql_server.sql](https://d2pwp9zz55emqw.cloudfront.net/scripts/awsdms_support_collector_sql_server.sql) script\.

**Note**  
Run this SQL Server diagnostic support script on SQL Server 2014 and later versions only\.

This script collects information about your SQL Server database configuration\. Remember to verify the checksum on the script, and if the checksum verifies, review the SQL code in the script to comment out any of the code that you are uncomfortable running\. After you are satisfied with the integrity and content of the script, you can run it\.

**To run the script for an on\-premises SQL Server database**

1. Run the script using the following sqlcmd command line\.

   ```
   sqlcmd -Uon-prem-user -Ppassword -SDMS-SQL17AG-N1 -y 0 
   -iC:\Users\admin\awsdms_support_collector_sql_server.sql -oC:\Users\admin\DMS_Support_Report_SQLServer.html -dsqlserverdb01
   ```

   The specified sqlcmd command parameters include the following:
   + `-U` – Database user name\.
   + `-P` – Database user password\.
   + `-S` – SQL Server database server name\.
   + `-y` – Maximum width of columns output from the sqlcmd utility\. A value of 0 specifies columns of unlimited width\.
   + `-i` – Path of the support script to run, in this case `awsdms_support_collector_sql_server.sql`\.
   + `-o` – Path of the output HTML file, with a file name that you specify, containing the collected database configuration information\.
   + `-d` – SQL Server database name\.

1. After the script completes, review the output HTML file and remove any information that you are uncomfortable sharing\. When the HTML is acceptable for you to share, upload the file to your AWS Support case\. For more information on uploading this file, see [Working with diagnostic support scripts in AWS DMS](CHAP_SupportScripts.md)\.

With Amazon RDS for SQL Server, you can't connect using the sqlcmd command line utility, so use the following procedure\.

**To run the script for an RDS SQL Server database**

1. Run the script using any client tool that allows you to connect to RDS SQL Server as the `Master` user and save the output as an HTML file\.

1. Review the output HTML file and remove any information that you are uncomfortable sharing\. When the HTML is acceptable for you to share, upload the file to your AWS Support case\. For more information on uploading this file, see [Working with diagnostic support scripts in AWS DMS](CHAP_SupportScripts.md)\.