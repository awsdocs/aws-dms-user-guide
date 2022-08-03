# Targets for AWS DMS<a name="CHAP_Introduction.Targets"></a>

You can use the following data stores as target endpoints for data migration using AWS DMS\.

**On\-premises and Amazon EC2 instance databases**
+ Oracle versions 10g, 11g, 12c, 18c, and 19c for the Enterprise, Standard, Standard One, and Standard Two editions\.
+ Microsoft SQL Server versions 2005, 2008, 2008R2, 2012, 2014, 2016, 2017, and 2019 for the Enterprise, Standard, Workgroup, and Developer editions\. The Web and Express editions are not supported\.
+ MySQL versions 5\.5, 5\.6, 5\.7, and 8\.0\.
+ MariaDB \(supported as a MySQL\-compatible data target\) versions 10\.0\.24 to 10\.0\.28, 10\.1, 10\.2, 10\.3 and 10\.4\.
**Note**  
Support for MariaDB as a target is available in all AWS DMS versions where MySQL is supported\.
+ PostgreSQL version 9\.4 and later \(for versions 9\.x\), 10\.x, 11\.x, 12\.x, 13\.x, and 14\.0\.
+ SAP Adaptive Server Enterprise \(ASE\) versions 15, 15\.5, 15\.7, 16 and later\.
+ Redis versions 6\.x\.

**Amazon RDS instance databases, Amazon Redshift, Amazon DynamoDB, Amazon S3, Amazon Elasticsearch Service, Amazon ElastiCache for Redis, Amazon Kinesis Data Streams, Amazon DocumentDB, Amazon Neptune, and Apache Kafka**
+ Oracle versions 11g \(versions 11\.2\.0\.3\.v1 and later\), 12c, 18c, and 19c for the Enterprise, Standard, Standard One, and Standard Two editions\.
+ Microsoft SQL Server versions 2012, 2014, 2016, 2017, and 2019 for the Enterprise, Standard, Workgroup, and Developer editions\. The Web and Express editions are not supported\.
+ MySQL versions 5\.5, 5\.6, 5\.7, and 8\.0\.
+ MariaDB \(supported as a MySQL\-compatible data target\) versions 10\.0\.24 to 10\.0\.28, 10\.1, 10\.2,10\.3 and 10\.4\.
**Note**  
Support for MariaDB as a target is available in all AWS DMS versions where MySQL is supported\.
+ PostgreSQL version 10\.x, 11\.x, 12\.x, 13\.x, and 14\.0\.
+ Amazon Aurora MySQL\-Compatible Edition
+ Amazon Aurora PostgreSQL\-Compatible Edition
+ Amazon Aurora Serverless v2
+ Amazon Redshift
+ Amazon S3
+ Amazon DynamoDB
+ Amazon Elasticsearch Service
+ Amazon ElastiCache for Redis
+ Amazon Kinesis Data Streams
+ Amazon DocumentDB \(with MongoDB compatibility\)
+ Amazon Neptune
+ Apache Kafka – [Amazon Managed Streaming for Apache Kafka \(Amazon MSK\)](http://aws.amazon.com/msk/) and [self\-managed Apache Kafka](https://kafka.apache.org/)
+ Babelfish \(version 1\.2\.0\) for Aurora PostgreSQL \(versions 13\.4, 13\.5, 13\.6\)

For information about working with a specific target, see [Working with AWS DMS endpoints](CHAP_Endpoints.md)\.