# Using a PostgreSQL Database as a Target for AWS Database Migration Service<a name="CHAP_Target.PostgreSQL"></a>

You can migrate data to PostgreSQL databases using AWS DMS, either from another PostgreSQL database or from one of the other supported databases\. PostgreSQL versions 9\.3 and later are supported for on\-premises, Amazon RDS, Amazon Aurora with PostgreSQL compatibility, and EC2 instance databases\.

AWS DMS takes a table by table approach when migrating data from source to target in the Full Load phase\. Table order during the Full Load phase cannot be guaranteed\. Tables will be out of sync during the Full Load phase and while cached transactions for individual tables are being applied\. As a result, Active referential integrity constraints can result in task failure during the Full Load phase\.

In PostgreSQL, foreign keys \(referential integrity constraints\) are implemented using triggers\. During the full load phase, AWS DMS loads each table one at a time\. We strongly recommend that you disable foreign key constraints during a full load, using one of the following methods:

+ Temporarily disable all triggers from the instance, and finish the full load\.

+ Use the `session_replication_role` parameter in PostgreSQL\.

At any given time, a trigger can be in one of the following states: `origin`, `replica`, `always`, or `disabled`\. When the `session_replication_role` parameter is set to `replica`, only triggers in the `replica` state will be active, and they are fired when they are called\. Otherwise, the triggers remain inactive\. 

PostgreSQL has a failsafe mechanism to prevent a table from being truncated, even when `session_replication_role` is set\. You can use this as an alternative to disabling triggers, to help the full load run to completion\. To do this, set the target table preparation mode to `DO_NOTHING`\. \(Otherwise, DROP and TRUNCATE operations will fail when there are foreign key constraints\.\)

In Amazon RDS, you can control set this parameter using a parameter group\. For a PostgreSQL instance running on Amazon EC2, you can set the parameter directly\.

For additional details on working with a PostgreSQL database as a target for AWS DMS, see the following sections: 


+ [Limitations on Using PostgreSQL as a Target for AWS Database Migration Service](#CHAP_Target.PostgreSQL.Limitations)
+ [Security Requirements When Using a PostgreSQL Database as a Target for AWS Database Migration Service](#CHAP_Target.PostgreSQL.Security)
+ [Extra Connection Attributes When Using PostgreSQL as a Target for AWS DMS](#CHAP_Target.PostgreSQL.ConnectionAttrib)

## Limitations on Using PostgreSQL as a Target for AWS Database Migration Service<a name="CHAP_Target.PostgreSQL.Limitations"></a>

The following limitations apply when using a PostgreSQL database as a target for AWS DMS:

+ The JSON data type is converted to the Native CLOB data type\.

## Security Requirements When Using a PostgreSQL Database as a Target for AWS Database Migration Service<a name="CHAP_Target.PostgreSQL.Security"></a>

For security purposes, the user account used for the data migration must be a registered user in any PostgreSQL database that you use as a target\.

## Extra Connection Attributes When Using PostgreSQL as a Target for AWS DMS<a name="CHAP_Target.PostgreSQL.ConnectionAttrib"></a>

You can use extra connection attributes to configure your PostgreSQL target\. You specify these settings when you create the target endpoint\.

The following table shows the extra connection attributes you can use to configure PostgreSQL as a target for AWS DMS:

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Target.PostgreSQL.html)