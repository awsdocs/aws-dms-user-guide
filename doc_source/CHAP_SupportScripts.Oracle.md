# Oracle diagnostic support scripts<a name="CHAP_SupportScripts.Oracle"></a>

Following, you can find the diagnostic support scripts available to analyze an on\-premises or Amazon RDS for Oracle database in your AWS DMS migration configuration\. These scripts work with either a source or target endpoint\. The scripts are all written to run in the SQL\*Plus command\-line utility\. For more information on using this utility, see [A Using SQL Command Line](https://docs.oracle.com/cd/B25329_01/doc/appdev.102/b25108/xedev_sqlplus.htm) in the Oracle documentation\.

Before running the script, ensure that the user account that you use has the necessary permissions to access your Oracle database\. The permissions settings shown assume a user created as follows\.

```
CREATE USER script_user IDENTIFIED BY password;
```

For an on\-premises database, set the minimum permissions as shown following for `script_user`\.

```
GRANT CREATE SESSION TO script_user;
GRANT SELECT on V$DATABASE to script_user;
GRANT SELECT on V$VERSION to script_user;
GRANT SELECT on GV$SGA to script_user;
GRANT SELECT on GV$INSTANCE to script_user;
GRANT SELECT on GV$DATAGUARD_CONFIG to script_user;
GRANT SELECT on GV$LOG to script_user;
GRANT SELECT on DBA_TABLESPACES to script_user;
GRANT SELECT on DBA_DATA_FILES to script_user;
GRANT SELECT on DBA_SEGMENTS to script_user;
GRANT SELECT on DBA_LOBS to script_user;
GRANT SELECT on V$ARCHIVED_LOG to script_user;
GRANT SELECT on DBA_TAB_MODIFICATIONS to script_user;
GRANT SELECT on DBA_TABLES to script_user;
GRANT SELECT on DBA_TAB_PARTITIONS to script_user;
GRANT SELECT on DBA_MVIEWS to script_user;
GRANT SELECT on DBA_OBJECTS to script_user;
GRANT SELECT on DBA_TAB_COLUMNS to script_user;
GRANT SELECT on DBA_LOG_GROUPS to script_user;
GRANT SELECT on DBA_LOG_GROUP_COLUMNS to script_user;
GRANT SELECT on V$ARCHIVE_DEST to script_user;
GRANT SELECT on DBA_SYS_PRIVS to script_user;
GRANT SELECT on DBA_TAB_PRIVS to script_user;
GRANT SELECT on DBA_TYPES to script_user;
GRANT SELECT on DBA_CONSTRAINTS to script_user;
GRANT SELECT on V$TRANSACTION to script_user;
GRANT SELECT on GV$ASM_DISK_STAT to script_user;
GRANT SELECT on GV$SESSION to script_user;
GRANT SELECT on GV$SQL to script_user;
GRANT SELECT on DBA_ENCRYPTED_COLUMNS to script_user;

GRANT EXECUTE on dbms_utility to script_user;
```

For an Amazon RDS database, set the minimum permissions as shown following\.

```
GRANT CREATE SESSION TO script_user;
exec rdsadmin.rdsadmin_util.grant_sys_object('V_$DATABASE','script_user','SELECT');
exec rdsadmin.rdsadmin_util.grant_sys_object('V_$VERSION','script_user','SELECT');
exec rdsadmin.rdsadmin_util.grant_sys_object('GV_$SGA','script_user','SELECT');
exec rdsadmin.rdsadmin_util.grant_sys_object('GV_$INSTANCE','script_user','SELECT');
exec rdsadmin.rdsadmin_util.grant_sys_object('GV_$DATAGUARD_CONFIG','script_user','SELECT');
exec rdsadmin.rdsadmin_util.grant_sys_object('GV_$LOG','script_user','SELECT');
exec rdsadmin.rdsadmin_util.grant_sys_object('DBA_TABLESPACES','script_user','SELECT');
exec rdsadmin.rdsadmin_util.grant_sys_object('DBA_DATA_FILES','script_user','SELECT');
exec rdsadmin.rdsadmin_util.grant_sys_object('DBA_SEGMENTS','script_user','SELECT');
exec rdsadmin.rdsadmin_util.grant_sys_object('DBA_LOBS','script_user','SELECT');
exec rdsadmin.rdsadmin_util.grant_sys_object('V_$ARCHIVED_LOG','script_user','SELECT');
exec rdsadmin.rdsadmin_util.grant_sys_object('DBA_TAB_MODIFICATIONS','script_user','SELECT');
exec rdsadmin.rdsadmin_util.grant_sys_object('DBA_TABLES','script_user','SELECT');
exec rdsadmin.rdsadmin_util.grant_sys_object('DBA_TAB_PARTITIONS','script_user','SELECT');
exec rdsadmin.rdsadmin_util.grant_sys_object('DBA_MVIEWS','script_user','SELECT');
exec rdsadmin.rdsadmin_util.grant_sys_object('DBA_OBJECTS','script_user','SELECT');
exec rdsadmin.rdsadmin_util.grant_sys_object('DBA_TAB_COLUMNS','script_user','SELECT');
exec rdsadmin.rdsadmin_util.grant_sys_object('DBA_LOG_GROUPS','script_user','SELECT');
exec rdsadmin.rdsadmin_util.grant_sys_object('DBA_LOG_GROUP_COLUMNS','script_user','SELECT');
exec rdsadmin.rdsadmin_util.grant_sys_object('V_$ARCHIVE_DEST','script_user','SELECT');
exec rdsadmin.rdsadmin_util.grant_sys_object('DBA_SYS_PRIVS','script_user','SELECT');
exec rdsadmin.rdsadmin_util.grant_sys_object('DBA_TAB_PRIVS','script_user','SELECT');
exec rdsadmin.rdsadmin_util.grant_sys_object('DBA_TYPES','script_user','SELECT');
exec rdsadmin.rdsadmin_util.grant_sys_object('DBA_CONSTRAINTS','script_user','SELECT');
exec rdsadmin.rdsadmin_util.grant_sys_object('V_$TRANSACTION','script_user','SELECT');
exec rdsadmin.rdsadmin_util.grant_sys_object('GV_$ASM_DISK_STAT','script_user','SELECT');
exec rdsadmin.rdsadmin_util.grant_sys_object('GV_$SESSION','script_user','SELECT');
exec rdsadmin.rdsadmin_util.grant_sys_object('GV_$SQL','script_user','SELECT');
exec rdsadmin.rdsadmin_util.grant_sys_object('DBA_ENCRYPTED_COLUMNS','script_user','SELECT');


exec rdsadmin.rdsadmin_util.grant_sys_object('DBMS_UTILITY','script_user','EXECUTE');
```

Following, you can find descriptions how to download, review, and run each SQL\*Plus support script available for Oracle\. You can also find how to review and upload the output to your AWS Support case\.

**Topics**
+ [awsdms\_support\_collector\_oracle\.sql script](#CHAP_SupportScripts.Oracle.Awsdms_Support_Collector_Oracle_Script)

## awsdms\_support\_collector\_oracle\.sql script<a name="CHAP_SupportScripts.Oracle.Awsdms_Support_Collector_Oracle_Script"></a>

Download the [https://d2pwp9zz55emqw.cloudfront.net/scripts/awsdms_support_collector_oracle.sql](https://d2pwp9zz55emqw.cloudfront.net/scripts/awsdms_support_collector_oracle.sql) script\.

This script collects information about your Oracle database configuration\. Remember to verify the checksum on the script, and if the checksum verifies, review the SQL code in the script to comment out any of the code that you are uncomfortable running\. After you are satisfied with the integrity and content of the script, you can run it\.

**To run the script and upload the results to your support case**

1. Run the script from your database environment using the following SQL\*Plus command line\.

   ```
   SQL> @awsdms_support_collector_oracle.sql
   ```

   The script displays a brief description and a prompt to either continue or abort the run\. Press \[Enter\] to continue\.

1. At the following prompt, enter the name of only one of the schemas that you want to migrate\.

1. At the following prompt, enter the name of the user \(*script\_user*\) that you have defined to connect to the database\.

1. At the following prompt, enter the number of days of data you want to examine, or accept the default\. The script then collects the specified data from your database\.

   After the script completes, it displays the name of the output HTML file, for example `dms_support_oracle-2020-06-22-13-20-39-ORCL.html`\. The script saves this file in your working directory\.

1. Review this HTML file and remove any information that you are uncomfortable sharing\. When the HTML is acceptable for you to share, upload the file to your AWS Support case\. For more information on uploading this file, see [Working with diagnostic support scripts in AWS DMS](CHAP_SupportScripts.md)\.