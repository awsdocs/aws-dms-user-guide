# Document history<a name="WhatsNew"></a>

The following table describes the important changes to the AWS Database Migration Service user guide documentation after January 2018\.

You can subscribe to an RSS feed to be notified of updates to this documentation\. For more details on AWS DMS version releases, see [AWS DMS release notes](CHAP_ReleaseNotes.md), 

| Change | Description | Date | 
| --- |--- |--- |
| [AWS DMS Studio](https://docs.aws.amazon.com/dms/latest/userguide/CHAP_DMSStudio.html) | AWS DMS Studio makes it easy to manage database migrations from start to finish\. Using AWS DMS Studio, you can accelerate your database migrations by providing an integrated experience through assessment, conversion, and data migration\. | December 1, 2021 | 
| [Getting Started Tutorial](https://docs.aws.amazon.com/dms/latest/userguide/CHAP_GettingStarted.html) | An update for the Getting Started Tutorial for AWS DMS\. The tutorial uses a MySQL database as a source and a PostgreSQL database as a target\. | May 20, 2021 | 
| [Support for Amazon Neptune as a target](https://docs.aws.amazon.com/dms/latest/userguide/CHAP_Target.Neptune.html) | Added support for Amazon Neptune as a target for data migration\. | June 1, 2020 | 
| [Support for Apache Kafka as a target](https://docs.aws.amazon.com/dms/latest/userguide/CHAP_Target.Kafka.html) | Added support for Apache Kafka as a target for data migration\. | March 20, 2020 | 
| [Updated security content](https://docs.aws.amazon.com/dms/latest/userguide/CHAP_Security.html) | Updated and standardized security content as a response to customer requests\. | December 20, 2019 | 
| [Migrating with AWS Snowball Edge](https://docs.aws.amazon.com/dms/latest/userguide/CHAP_LargeDBs.html) | Added support for using AWS Snowball Edge to migrate large databases\. | January 24, 2019 | 
| [Support for Amazon DocumentDB \(with MongoDB compatibility\) as a target](https://docs.aws.amazon.com/dms/latest/userguide/CHAP_Target.DocumentDB.html) | Added support for Amazon DocumentDB \(with MongoDB compatibility\) as a target for data migration\. | January 9, 2019 | 
| [Support for Amazon Elasticsearch Service and Amazon Kinesis Data Streams as targets](https://docs.aws.amazon.com/dms/latest/userguide/CHAP_Target.html) | Added support for Amazon ES and Kinesis Data Streams as targets for data migration\. | November 15, 2018 | 
| [CDC native start support](https://docs.aws.amazon.com/dms/latest/userguide/CHAP_Task.CDC.html) | Added support for native start points when using change data capture \(CDC\)\. | June 28, 2018 | 
| [R4 support](https://docs.aws.amazon.com/dms/latest/userguide/CHAP_ReplicationInstance.html#CHAP_ReplicationInstance.InDepth) | Added support for R4 replication instance classes\. | May 10, 2018 | 
| [Db2 LUW support](https://docs.aws.amazon.com/dms/latest/userguide/CHAP_Source.DB2.html) | Added support for IBM Db2 LUW as a source for data migration\. | April 26, 2018 | 
| [Task log support](https://docs.aws.amazon.com/dms/latest/userguide/CHAP_ReplicationInstance.html#CHAP_ReplicationInstance.InDepth) | Added support for seeing task log usage and purging task logs\. | February 8, 2018 | 
| [SQL Server as target support](https://docs.aws.amazon.com/dms/latest/userguide/CHAP_Source.SQLServer.html) | Added support for Amazon RDS for Microsoft SQL Server as a source\. | February 6, 2018 | 

## Earlier updates<a name="WhatsNew.Previous"></a>

The following table describes the important changes to the AWS Database Migration Service user guide documentation before January 2018\.


| Change | Description | Date changed | 
| --- | --- | --- | 
| New feature | Added support for using AWS DMS with AWS Snowball to migrate large databases\. For more information, see [Migrating large data stores using AWS Database Migration Service and AWS Snowball Edge](CHAP_LargeDBs.md)\. | November 17, 2017 | 
| New feature | Added support for task assessment report and data validation\. For more information about the task assessment report, see [Enabling and working with premigration assessments for a task](CHAP_Tasks.AssessmentReport.md)\. For more information about data validation, see [ Data validation task settings](CHAP_Tasks.CustomizingTasks.TaskSettings.DataValidation.md)\. | November 17, 2017 | 
| New feature | Added support for AWS CloudFormation templates\. For more information, see [AWS DMS support for AWS CloudFormation](CHAP_Introduction.AWS.md#CHAP_Introduction.AWS.CloudFormation)\. | July 11, 2017 | 
| New feature | Added support for using Amazon Dynamo as a target\. For more information, see [Using an Amazon DynamoDB database as a target for AWS Database Migration Service](CHAP_Target.DynamoDB.md)\. | April 10, 2017 | 
| New feature | Added support for using MongoDB as a source\. For more information, see [Using MongoDB as a source for AWS DMS](CHAP_Source.MongoDB.md)\. | April 10, 2017 | 
| New feature | Added support for using Amazon S3 as a target\. For more information, see [Using Amazon S3 as a target for AWS Database Migration Service](CHAP_Target.S3.md)\. | March 27, 2017 | 
| New feature | Adds support for reloading database tables during a migration task\. For more information, see [Reloading tables during a task](CHAP_Tasks.ReloadTables.md)\. | March 7, 2017 | 
| New feature | Added support for events and event subscriptions\. For more information, see [Working with events and notifications in AWS Database Migration Service](CHAP_Events.md)\. | January 26, 2017 | 
| New feature | Added support for SSL endpoints for Oracle\. For more information, see [SSL support for an Oracle endpoint](CHAP_Source.Oracle.md#CHAP_Security.SSL.Oracle)\. | December 5, 2016 | 
| New feature | Added support for using change data capture \(CDC\) with an Amazon RDS PostgreSQL DB instance\. For more information, see [Working with AWS\-managed PostgreSQL databases as DMS sources](CHAP_Source.PostgreSQL.md#CHAP_Source.PostgreSQL.RDSPostgreSQL)\. | September 14, 2016 | 
| New Region support | Added support for the Asia Pacific \(Mumbai\), Asia Pacific \(Seoul\), and South America \(São Paulo\) regions\. For a list of supported AWS Regions, see [What is AWS Database Migration Service?](Welcome.md)\. | August 3, 2016 | 
| New feature | Added support for ongoing replication\. For more information, see [Ongoing replication](CHAP_BestPractices.md#CHAP_BestPractices.OnGoingReplication)\. | July 13, 2016 | 
| New feature | Added support for secured connections using SSL\. For more information, see [Using SSL with AWS Database Migration Service](CHAP_Security.md#CHAP_Security.SSL)\. | July 13, 2016 | 
| New feature | Added support for SAP Adaptive Server Enterprise \(ASE\) as a source or target endpoint\. For more information, see [Using an SAP ASE database as a source for AWS DMS](CHAP_Source.SAP.md) and [Using a SAP ASE database as a target for AWS Database Migration Service](CHAP_Target.SAP.md)\. | July 13, 2016 | 
| New feature | Added support for filters to move a subset of rows from the source database to the target database\. For more information, see [Using source filters](CHAP_Tasks.CustomizingTasks.Filters.md)\. | May 2, 2016 | 
| New feature | Added support for Amazon Redshift as a target endpoint\. For more information, see [Using an Amazon Redshift database as a target for AWS Database Migration Service](CHAP_Target.Redshift.md)\. | May 2, 2016 | 
| General availability | Initial release of AWS Database Migration Service\. | March 14, 2016 | 
| Public preview release | Released the preview documentation for AWS Database Migration Service\. | January 21, 2016 | 