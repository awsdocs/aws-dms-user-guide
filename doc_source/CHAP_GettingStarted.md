# Getting started with AWS Database Migration Service<a name="CHAP_GettingStarted"></a>

In the following tutorial, you can find out how to perform a database migration with AWS Database Migration Service \(AWS DMS\)\.

To perform a database migration, take the following steps:

1. Set up your AWS account by following the steps in [Setting up for AWS Database Migration Service](CHAP_GettingStarted.SettingUp.md)\.

1. Create your sample databases and an Amazon EC2 client to populate your source database and test replication\. Also, create a virtual private cloud \(VPC\) based on the Amazon Virtual Private Cloud \(Amazon VPC\) service to contain your tutorial resources\. To create these resources, follow the steps in [Prerequisites for AWS Database Migration Service](CHAP_GettingStarted.Prerequisites.md)\.

1. Populate your source database using a [sample database creation script](https://github.com/aws-samples/aws-database-migration-samples)\. 

1. Use the AWS Schema Conversion Tool \(AWS SCT\) to convert the schema from the source database to the target database\. To convert the schema, follow the steps in [Migrate schema](CHAP_GettingStarted.SCT.md)\.

1. Create a replication instance to perform all the processes for the migration\. To do this and the following tasks, take the steps in [Replication](CHAP_GettingStarted.Replication.md)\.

1. Specify source and target database endpoints\.

1. Create a task to define what tables and replication processes you want to use, and start replication\.

1. Verify that replication is working by running queries on the target database\.