# Using an Oracle Database as a Target for AWS Database Migration Service<a name="CHAP_Target.Oracle"></a>

You can migrate data to Oracle database targets using AWS DMS, either from another Oracle database or from one of the other supported databases\.You can use SSL to encrypt connections between your Oracle endpoint and the replication instance\. For more information on using SSL with an Oracle endpoint, see [Using SSL With AWS Database Migration Service](CHAP_Security.SSL.md)\.

AWS DMS supports Oracle versions 10g, 11g, and 12c for on\-premises and EC2 instances for the Enterprise, Standard, Standard One, and Standard Two editions as targets\. AWS DMS supports Oracle versions 11g \(versions 11\.2\.0\.3\.v1 and later\) and 12c for Amazon RDS instance databases for the Enterprise, Standard, Standard One, and Standard Two editions\.

When using Oracle as a target, we assume the data should be migrated into the schema/user which is used for the target connection\. If you want to migrate data to a different schema, you'll need to use a schema transformation to do so\. For example, if my target endpoint connects to the user RDSMASTER and you wish to migrate from the user PERFDATA to PERFDATA, you'll need to create a transformation as follows:

```
{
   "rule-type": "transformation",
   "rule-id": "2",
   "rule-name": "2",
   "rule-action": "rename",
   "rule-target": "schema",
   "object-locator": {
   "schema-name": "PERFDATA"
},
"value": "PERFDATA"
}
```

For more information about transformations, see [ Selection and Transformation Table Mapping using JSON](CHAP_Tasks.CustomizingTasks.TableMapping.md#CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation)\.

For additional details on working with Oracle databases as a target for AWS DMS, see the following sections: 


+ [Limitations on Oracle as a Target for AWS Database Migration Service](#CHAP_Target.Oracle.Limitations)
+ [User Account Privileges Required for Using Oracle as a Target](#CHAP_Target.Oracle.Privileges)
+ [Configuring an Oracle Database as a Target for AWS Database Migration Service](#CHAP_Target.Oracle.Configuration)
+ [Extra Connection Attributes When Using Oracle as a Target for AWS DMS](#CHAP_Target.Oracle.ConnectionAttrib)

## Limitations on Oracle as a Target for AWS Database Migration Service<a name="CHAP_Target.Oracle.Limitations"></a>

Limitations when using Oracle as a target for data migration include the following:

+ AWS DMS does not create schema on the target Oracle database\. You have to create any schemas you want on the target Oracle database\. The schema name must already exist for the Oracle target\. Tables from source schema are imported to user/schema, which AWS DMS uses to connect to the target instance\. You must create multiple replication tasks if you have to migrate multiple schemas\. 

+ AWS DMS doesn't support the `Use direct path full load` option for tables with INDEXTYPE CONTEXT\. As a workaround, you can use array load\. 

+ In Batch Optimized Apply mode, loading into the net changes table uses Direct Path, which doesn't support XMLType\. As a workaround, you can use Transactional Apply mode\.

## User Account Privileges Required for Using Oracle as a Target<a name="CHAP_Target.Oracle.Privileges"></a>

To use an Oracle target in an AWS Database Migration Service task, for the user account specified in the AWS DMS Oracle database definitions you need to grant the following privileges in the Oracle database:

+ SELECT ANY TRANSACTION 

+ SELECT on V$NLS\_PARAMETERS 

+ SELECT on V$TIMEZONE\_NAMES 

+ SELECT on ALL\_INDEXES 

+ SELECT on ALL\_OBJECTS 

+ SELECT on DBA\_OBJECTS

+ SELECT on ALL\_TABLES 

+ SELECT on ALL\_USERS 

+ SELECT on ALL\_CATALOG 

+ SELECT on ALL\_CONSTRAINTS 

+ SELECT on ALL\_CONS\_COLUMNS 

+ SELECT on ALL\_TAB\_COLS 

+ SELECT on ALL\_IND\_COLUMNS 

+ DROP ANY TABLE 

+ SELECT ANY TABLE

+ INSERT ANY TABLE 

+ UPDATE ANY TABLE

+ CREATE ANY VIEW

+ DROP ANY VIEW

+ CREATE ANY PROCEDURE

+ ALTER ANY PROCEDURE

+ DROP ANY PROCEDURE

+ CREATE ANY SEQUENCE

+ ALTER ANY SEQUENCE

+ DROP ANY SEQUENCE 

For the requirements specified following, grant the additional privileges named:

+ To use a specific table list, grant SELECT on any replicated table and also ALTER on any replicated table\.

+ To allow a user to create a table in his default tablespace, grant the privilege GRANT UNLIMITED TABLESPACE\.

+ For logon, grant the privilege CREATE SESSION\.

+ If you are using a direct path, grant the privilege LOCK ANY TABLE\.

+ If the "DROP and CREATE table" or "TRUNCATE before loading" option is selected in the full load settings, and the target table schema is different from that for the AWS DMS user, grant the privilege DROP ANY TABLE\.

+ To store changes in change tables or an audit table when the target table schema is different from that for the AWS DMS user, grant the privileges CREATE ANY TABLE and CREATE ANY INDEX\.

### Read Privileges Required for AWS Database Migration Service on the Target Database<a name="CHAP_Target.Oracle.Privileges.Read"></a>

The AWS DMS user account must be granted read permissions for the following DBA tables:

+ SELECT on DBA\_USERS

+ SELECT on DBA\_TAB\_PRIVS

+ SELECT on DBA\_OBJECTS

+ SELECT on DBA\_SYNONYMS

+ SELECT on DBA\_SEQUENCES

+ SELECT on DBA\_TYPES

+ SELECT on DBA\_INDEXES

+ SELECT on DBA\_TABLES

+ SELECT on DBA\_TRIGGERS

If any of the required privileges cannot be granted to V$xxx, then grant them to V\_$xxx\.

## Configuring an Oracle Database as a Target for AWS Database Migration Service<a name="CHAP_Target.Oracle.Configuration"></a>

Before using an Oracle database as a data migration target, you must provide an Oracle user account to AWS DMS\. The user account must have read/write privileges on the Oracle database, as specified in the section [User Account Privileges Required for Using Oracle as a Target](#CHAP_Target.Oracle.Privileges)\.

## Extra Connection Attributes When Using Oracle as a Target for AWS DMS<a name="CHAP_Target.Oracle.ConnectionAttrib"></a>

You can use extra connection attributes to configure your Oracle target\. You specify these settings when you create the target endpoint\.

The following table shows the extra connection attributes available when using Oracle as a target:

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Target.Oracle.html)