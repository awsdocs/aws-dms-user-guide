# Sources for AWS DMS<a name="CHAP_Introduction.Sources"></a>

You can use the following data stores as source endpoints for data migration using AWS DMS\.

**On\-premises and EC2 instance databases**
+ Oracle versions 10\.2 and later \(for versions 10\.x\), 11g and up to 12\.2, 18c, and 19c for the Enterprise, Standard, Standard One, and Standard Two editions
+ Microsoft SQL Server versions 2005, 2008, 2008R2, 2012, 2014, 2016, 2017, and 2019\. 
  + The Enterprise, Standard, Workgroup, Developer, and Web editions are supported for full\-load only replication\.
  + The Enterprise, Standard \(version 2016 and later\), and Developereditions are supported for CDC \(ongoing\) replication\.
  + The Express edition is not supported\.
+ MySQL versions 5\.5, 5\.6, 5\.7, and 8\.0\.
**Note**  
Support for MySQL 8\.0 as a source is available in AWS DMS versions 3\.4\.0 and later, except when the transaction payload is compressed\. Support for Google Cloud for MySQL 8\.0 as a source is available in AWS DMS versions 3\.6\.0 and later\.
+ MariaDB \(supported as a MySQL\-compatible data source\) versions 10\.0 \(only versions 10\.0\.24 and later\), 10\.2, 10\.3, 10\.4, and 10\.5\.
**Note**  
Support for MariaDB as a source is available in all AWS DMS versions where MySQL is supported\.
+ PostgreSQL version 9\.4 and later \(for versions 9\.x\), 10\.x, 11\.x, 12\.x, 13\.x and 14\.x\.
+ MongoDB versions 3\.x, 4\.0, 4\.2, and 4\.4\. 
+ SAP Adaptive Server Enterprise \(ASE\) versions 12\.5, 15, 15\.5, 15\.7, 16 and later\. 
+ IBM Db2 for Linux, UNIX, and Windows \(Db2 LUW\) versions:
  + Version 9\.7, all fix packs are supported\.
  + Version 10\.1, all fix packs are supported\.
  + Version 10\.5, all fix packs except for Fix Pack 5 are supported\.
  + Version 11\.1, all fix packs are supported\.
  + Version 11\.5, Mods \(0\-8\) with only Fix Pack Zero supported
  + IBM Db2 for z/OS version 12

**Third\-party managed database services:**
+ Microsoft Azure SQL Database
+ Google Cloud for MySQL versions 5\.6, 5\.7, and 8\.0\.

**Amazon RDS instance databases, and Amazon Simple Storage Service \(Amazon S3\)**
+ Oracle versions 11g \(versions 11\.2\.0\.4 and later\) and up to 12\.2, 18c, and 19c for the Enterprise, Standard, Standard One, and Standard Two editions\.
+ Microsoft SQL Server versions 2012, 2014, 2016, 2017, and 2019 for the Enterprise, Standard, Workgroup, and Developer editions\. The Web and Express editions are not supported\.
+ MySQL versions 5\.5, 5\.6, 5\.7, and 8\.0\.
**Note**  
Support for MySQL 8\.0 as a source is available in AWS DMS versions 3\.4\.0 and later, except when the transaction payload is compressed\.
+ MariaDB \(supported as a MySQL\-compatible data source\) versions 10\.0\.24 to 10\.0\.28, 10\.1, 10\.2, and 10\.3, and 10\.4
**Note**  
Support for MariaDB as a source is available in all AWS DMS versions where MySQL is supported\.
+ PostgreSQL version 10\.x, 11\.x, 12\.x, 13\.x, and 14\.x\. 
+ Amazon Aurora with MySQL compatibility \(supported as a MySQL\-compatible data source\)\.
+ Amazon Aurora with PostgreSQL compatibility \(supported as a PostgreSQL\-compatible data source\)\.
+ Amazon S3\.
+ Amazon DocumentDB \(with MongoDB compatibility\) versions 3\.6 and 4\.0\.

For information about working with a specific source, see [Working with AWS DMS endpoints](CHAP_Endpoints.md)\.