# Targets for Data Migration<a name="CHAP_Target"></a>

AWS Database Migration Service \(AWS DMS\) can use many of the most popular databases as a target for data replication\. The target can be on an Amazon Elastic Compute Cloud \(Amazon EC2\) instance, an Amazon Relational Database Service \(Amazon RDS\) instance, or an on\-premises database\. 

**Note**  
Regardless of the source storage engine \(MyISAM, MEMORY, and so on\), AWS DMS creates a MySQL\-compatible target table as an InnoDB table by default\. If you need to have a table that uses a storage engine other than InnoDB, you can manually create the table on the MySQL\-compatible target and migrate the table using the "do nothing" mode\. For more information about the "do nothing" mode, see [Full Load Task Settings](CHAP_Tasks.CustomizingTasks.TaskSettings.FullLoad.md)\.

The databases include the following: 

**On\-premises and EC2 instance databases**
+ Oracle versions 10g, 11g, 12c, for the Enterprise, Standard, Standard One, and Standard Two editions
+ Microsoft SQL Server versions 2005, 2008, 2008R2, 2012, 2014, and 2016, for the Enterprise, Standard, Workgroup, and Developer editions\. The Web and Express editions are not supported\.
+ MySQL versions 5\.5, 5\.6, and 5\.7
+ MariaDB \(supported as a MySQL\-compatible data target\)
+ PostgreSQL versions 9\.4 and later
+ SAP Adaptive Server Enterprise \(ASE\) versions 15, 15\.5, 15\.7, 16 and later

**Amazon RDS instance databases, Amazon Redshift, Amazon S3, and Amazon DynamoDB**
+ Amazon RDS Oracle versions 11g \(versions 11\.2\.0\.3\.v1 and later\) and 12c, for the Enterprise, Standard, Standard One, and Standard Two editions
+ Amazon RDS Microsoft SQL Server versions 2008R2, 2012, and 2014, for the Enterprise, Standard, Workgroup, and Developer editions\. The Web and Express editions are not supported\.
+ Amazon RDS MySQL versions 5\.5, 5\.6, and 5\.7
+ Amazon RDS MariaDB \(supported as a MySQL\-compatible data target\)
+ Amazon RDS PostgreSQL versions 9\.4 and later
+ Amazon Aurora with MySQL compatibility
+ Amazon Aurora with PostgreSQL compatibility
+ Amazon Redshift
+ Amazon S3
+ Amazon DynamoDB

**Topics**
+ [Using an Oracle Database as a Target for AWS Database Migration Service](CHAP_Target.Oracle.md)
+ [Using a Microsoft SQL Server Database as a Target for AWS Database Migration Service](CHAP_Target.SQLServer.md)
+ [Using a PostgreSQL Database as a Target for AWS Database Migration Service](CHAP_Target.PostgreSQL.md)
+ [Using a MySQL\-Compatible Database as a Target for AWS Database Migration Service](CHAP_Target.MySQL.md)
+ [Using an Amazon Redshift Database as a Target for AWS Database Migration Service](CHAP_Target.Redshift.md)
+ [Using a SAP ASE Database as a Target for AWS Database Migration Service](CHAP_Target.SAP.md)
+ [Using Amazon S3 as a Target for AWS Database Migration Service](CHAP_Target.S3.md)
+ [Using an Amazon DynamoDB Database as a Target for AWS Database Migration Service](CHAP_Target.DynamoDB.md)