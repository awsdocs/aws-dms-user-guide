# Targets for AWS DMS<a name="CHAP_Introduction.Targets"></a>

You can use different target data stores in different AWS DMS features\. The following sections contain the lists of supported target data stores for each AWS DMS feature\.

**Topics**
+ [Target endpoints for data migration](#CHAP_Introduction.Targets.DataMigration)
+ [Target databases for DMS Fleet Advisor](#CHAP_Introduction.Targets.FleetAdvisor)
+ [Target data providers for DMS Schema Conversion](#CHAP_Introduction.Targets.SchemaConversion)

## Target endpoints for data migration<a name="CHAP_Introduction.Targets.DataMigration"></a>

You can use the following data stores as target endpoints for data migration using AWS DMS\.

**On\-premises and Amazon EC2 instance databases**
+ Oracle versions 10g, 11g, 12c, 18c, and 19c for the Enterprise, Standard, Standard One, and Standard Two editions
+ Microsoft SQL Server versions 2005, 2008, 2008R2, 2012, 2014, 2016, 2017, and 2019 for the Enterprise, Standard, Workgroup, Web, and Developer editions
**Note**  
AWS DMS doesn't support SQL Server Web and Express editions\.
+ MySQL versions 5\.5, 5\.6, 5\.7, and 8\.0
+ MariaDB \(supported as a MySQL\-compatible data target\) versions 10\.0\.24 to 10\.0\.28, 10\.1, 10\.2, 10\.3 and 10\.4
**Note**  
Support for MariaDB as a target is available in all AWS DMS versions where MySQL is supported\.
+ PostgreSQL version 9\.4 and higher \(for versions 9\.x\), 10\.x, 11\.x, 12\.x, 13\.x, and 14\.x
+ SAP Adaptive Server Enterprise \(ASE\) versions 15, 15\.5, 15\.7, 16, and higher
+ Redis versions 6\.x

**Amazon RDS instance databases, Amazon Redshift, Amazon DynamoDB, Amazon S3, Amazon OpenSearch Service, Amazon ElastiCache for Redis, Amazon Kinesis Data Streams, Amazon DocumentDB, Amazon Neptune, and Apache Kafka**
+ Oracle versions 11g \(versions 11\.2\.0\.3\.v1 and higher\), 12c, 18c, and 19c for the Enterprise, Standard, Standard One, and Standard Two editions
+ Microsoft SQL Server versions 2012, 2014, 2016, 2017, and 2019 for the Enterprise, Standard, Workgroup, and Developer editions
**Note**  
AWS DMS doesn't support SQL Server Web and Express editions\.
+ MySQL versions 5\.5, 5\.6, 5\.7, and 8\.0
+ MariaDB \(supported as a MySQL\-compatible data target\) versions 10\.0\.24 to 10\.0\.28, 10\.1, 10\.2, 10\.3 and 10\.4
**Note**  
Support for MariaDB as a target is available in all AWS DMS versions where MySQL is supported\.
+ PostgreSQL version 10\.x, 11\.x, 12\.x, 13\.x, and 14\.x
+ Amazon Aurora MySQL\-Compatible Edition
+ Amazon Aurora PostgreSQL\-Compatible Edition
+ Amazon Aurora Serverless v2
+ Amazon Redshift
+ Amazon S3
+ Amazon DynamoDB
+ Amazon OpenSearch Service
+ Amazon ElastiCache for Redis
+ Amazon Kinesis Data Streams
+ Amazon DocumentDB \(with MongoDB compatibility\)
+ Amazon Neptune
+ Apache Kafka – [Amazon Managed Streaming for Apache Kafka \(Amazon MSK\)](http://aws.amazon.com/msk/) and [self\-managed Apache Kafka](https://kafka.apache.org/)
+ Babelfish \(version 1\.2\.0 and higher\) for Aurora PostgreSQL \(versions 13\.4 and higher\)

For information about working with a specific target, see [Working with AWS DMS endpoints](CHAP_Endpoints.md)\.

For information about supported source endpoints, see [Source endpoints for data migration](CHAP_Introduction.Sources.md#CHAP_Introduction.Sources.DataMigration)\.

## Target databases for DMS Fleet Advisor<a name="CHAP_Introduction.Targets.FleetAdvisor"></a>

DMS Fleet Advisor generates target recommendations using the latest version of the following target databases\.
+ Amazon Aurora MySQL
+ Amazon Aurora PostgreSQL
+ Amazon RDS for MySQL
+ Amazon RDS for Oracle
+ Amazon RDS for PostgreSQL
+ Amazon RDS for SQL Server

For information about target recommendations in DMS Fleet Advisor, see [Using the AWS DMS Fleet Advisor Target Recommendations feature](fa-recommendations.md)\.

For information about supported source databases, see [Source databases for DMS Fleet Advisor](CHAP_Introduction.Sources.md#CHAP_Introduction.Sources.FleetAdvisor)\.

## Target data providers for DMS Schema Conversion<a name="CHAP_Introduction.Targets.SchemaConversion"></a>

DMS Schema Conversion supports the following data providers as targets for your migration projects\.
+ Amazon Aurora MySQL 8\.0\.23
+ Amazon Aurora PostgreSQL 14\.5
+ Amazon RDS for MySQL 8\.0\.23
+ Amazon RDS for PostgreSQL 14\.x

For information about working with a specific target, see [Creating target data providers in DMS Schema Conversion](data-providers-target.md)\.

For information about supported source databases, see [Source data providers for DMS Schema Conversion](CHAP_Introduction.Sources.md#CHAP_Introduction.Sources.SchemaConversion)\.