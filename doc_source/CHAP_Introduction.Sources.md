# Sources for AWS Database Migration Service<a name="CHAP_Introduction.Sources"></a>

You can use the following data stores as source endpoints for data migration using AWS Database Migration Service\.

**On\-premises and EC2 instance databases**
+ Oracle versions 10\.2 and later, 11g, and up to 12\.1, for the Enterprise, Standard, Standard One, and Standard Two editions
+ Microsoft SQL Server versions 2005, 2008, 2008R2, 2012, 2014, and 2016, for the Enterprise, Standard, Workgroup, and Developer editions\. The Web and Express editions are not supported\.
+ MySQL versions 5\.5, 5\.6, and 5\.7\.
+ MariaDB \(supported as a MySQL\-compatible data source\)\.
+ PostgreSQL version 9\.4 and later\.
+ MongoDB versions 2\.6\.x and 3\.x and later\.
+ SAP Adaptive Server Enterprise \(ASE\) versions 12\.5, 15, 15\.5, 15\.7, 16 and later\. 
+ Db2 LUW versions:
  + Version 9\.7, all Fix Packs are supported\.
  + Version 10\.1, all Fix Packs are supported\.
  + Version 10\.5, all Fix Packs except for Fix Pack 5 are supported\.

**Microsoft Azure**
+ Azure SQL Database\.

**Amazon RDS instance databases, and Amazon S3**
+ Oracle versions 11g \(versions 11\.2\.0\.3\.v1 and later\) and 12c, for the Enterprise, Standard, Standard One, and Standard Two editions\.
+ Microsoft SQL Server versions 2008R2, 2012, 2014, and 2016 for the Enterprise, Standard, Workgroup, and Developer editions\. The Web and Express editions are not supported\.
+ MySQL versions 5\.5, 5\.6, and 5\.7\.
+ MariaDB \(supported as a MySQL\-compatible data source\)\.
+ PostgreSQL 9\.4 and later\. Change data capture \(CDC\) is only supported for versions 9\.4\.9 and higher and 9\.5\.4 and higher\. The `rds.logical_replication` parameter, which is required for CDC, is supported only in these versions and later\. 
+ Amazon Aurora \(supported as a MySQL\-compatible data source\)\.
+ Amazon S3\.