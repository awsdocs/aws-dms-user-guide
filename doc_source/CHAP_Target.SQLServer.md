# Using a Microsoft SQL Server Database as a Target for AWS Database Migration Service<a name="CHAP_Target.SQLServer"></a>

You can migrate data to Microsoft SQL Server databases using AWS DMS\. With an SQL Server database as a target, you can migrate data from either another SQL Server database or one of the other supported databases\.

For on\-premises and Amazon EC2 instance databases, AWS DMS supports as a target SQL Server versions 2005, 2008, 2008R2, 2012, 2014, and 2016, for the Enterprise, Standard, Workgroup, and Developer editions\. The Web and Express editions are not supported\.

For Amazon RDS instance databases, AWS DMS supports as a target SQL Server versions 2008R2, 2012, 2014, and 2016, for the Enterprise, Standard, Workgroup, and Developer editions are supported\. The Web and Express editions are not supported\.

For additional details on working with AWS DMS and SQL Server target databases, see the following\.


+ [Limitations on Using SQL Server as a Target for AWS Database Migration Service](#CHAP_Target.SQLServer.Limitations)
+ [Security Requirements When Using SQL Server as a Target for AWS Database Migration Service](#CHAP_Target.SQLServer.Security)
+ [Extra Connection Attributes When Using SQLServer as a Target for AWS DMS](#CHAP_Target.SQLServer.ConnectionAttrib)

## Limitations on Using SQL Server as a Target for AWS Database Migration Service<a name="CHAP_Target.SQLServer.Limitations"></a>

The following limitations apply when using a SQL Server database as a target for AWS DMS:

+ When you manually create a SQL Server target table with a computed column, full load replication is not supported when using the BCP bulk\-copy utility\. To use full load replication, disable the **Use BCP for loading tables** option in the console's **Advanced** tab\.

+ When replicating tables with SQL Server spatial data types \(GEOMETRY and GEOGRAPHY\), AWS DMS replaces any spatial reference identifier \(SRID\) that you might have inserted with the default SRID\. The default SRID is 0 for GEOMETRY and 4326 for GEOGRAPHY\.

## Security Requirements When Using SQL Server as a Target for AWS Database Migration Service<a name="CHAP_Target.SQLServer.Security"></a>

The following describes the security requirements for using AWS DMS with a Microsoft SQL Server target\.

+ AWS DMS user account must have at least the db\_owner user role on the Microsoft SQL Server database you are connecting to\.

+ A Microsoft SQL Server system administrator must provide this permission to all AWS DMS user accounts\.

## Extra Connection Attributes When Using SQLServer as a Target for AWS DMS<a name="CHAP_Target.SQLServer.ConnectionAttrib"></a>

You can use extra connection attributes to configure your SQL Server target\. You specify these settings when you create the target endpoint\. The following table shows the extra connection attributes you can use when SQL Server is the target:

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Target.SQLServer.html)