# Sources for Data Migration<a name="CHAP_Source"></a>

AWS Database Migration Service \(AWS DMS\) can use many of the most popular databases as a source for data replication\. The database source can be on an Amazon Elastic Compute Cloud \(Amazon EC2\) instance, an Amazon Relational Database Service \(Amazon RDS\) instance, or an on\-premises database\. In addition, you can use S3 as a source\.

Valid sources for AWS DMS include the following:

**On\-premises and Amazon EC2 instance databases**

+ Oracle versions 10\.2 and later, 11g, and up to 12\.1, for the Enterprise, Standard, Standard One, and Standard Two editions

+ Microsoft SQL Server versions 2005, 2008, 2008R2, 2012, 2014, and 2016, for the Enterprise, Standard, Workgroup, and Developer editions\. The Web and Express editions are not supported\.

+ MySQL versions 5\.5, 5\.6, and 5\.7

+ MariaDB \(supported as a MySQL\-compatible data source\)

+ PostgreSQL 9\.3 and later

+ SAP Adaptive Server Enterprise \(ASE\) 15\.7 and later

+ MongoDB versions 2\.6\.x and 3\.x and later

**Microsoft Azure**

+ AWS DMS supports full data load when using Azure SQL Database as a source\. Change Data Capture \(CDC\) is not supported\. 

**Amazon RDS instance databases**

+ Oracle versions 11g \(versions 11\.2\.0\.3\.v1 and later\), and 12c, for the Enterprise, Standard, Standard One, and Standard Two editions

+ Microsoft SQL Server versions 2008R2, 2012, and 2014, for the Enterprise and Standard editions\. Note that change data capture \(CDC\) operations are not supported\. The Web, Workgroup, Developer, and Express editions are not supported\.

+ MySQL versions 5\.5, 5\.6, and 5\.7\. Note that Change Data Capture \(CDC\) is only supported for versions 5\.6 and later\.

+ PostgreSQL 9\.4 and later\. Note that Change Data Capture \(CDC\) is only supported for versions 9\.4\.9 and higher and 9\.5\.4 and higher\. The `rds.logical_replication` parameter, which is required for CDC, is supported only in these versions and later\. 

+ MariaDB \(supported as a MySQL\-compatible data source\)

+ Amazon Aurora with MySQL compatibility

**Amazon S3**

+ AWS DMS supports full data load and change data capture \(CDC\) when using Amazon S3 as a source\.


+ [Using an Oracle Database as a Source for AWS DMS](CHAP_Source.Oracle.md)
+ [Using a Microsoft SQL Server Database as a Source for AWS DMS](CHAP_Source.SQLServer.md)
+ [Using Microsoft Azure SQL Database as a Source for AWS DMS](CHAP_Source.AzureSQL.md)
+ [Using a PostgreSQL Database as a Source for AWS DMS](CHAP_Source.PostgreSQL.md)
+ [Using a MySQL\-Compatible Database as a Source for AWS DMS](CHAP_Source.MySQL.md)
+ [Using a SAP ASE Database as a Source for AWS DMS](CHAP_Source.SAP.md)
+ [Using MongoDB as a Source for AWS DMS](CHAP_Source.MongoDB.md)
+ [Using Amazon S3 as a Source for AWS DMS](CHAP_Source.S3.md)