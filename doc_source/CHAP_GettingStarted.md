# Getting started with AWS Database Migration Service<a name="CHAP_GettingStarted"></a>

This tutorial describes how to perform a database migration\. 

To perform a database migration, you do the following steps:
+ Set up your AWS account by following the steps in the [Setting up for AWS Database Migration Service](CHAP_GettingStarted.SettingUp.md) topic\.
+ Create your sample databases, an Amazon EC2 client for populating your source database and testing replication, and a Amazon VPC to contain your tutorial resources\. To create these resources, follow the steps in the [Prerequisites](CHAP_GettingStarted.Prerequisites.md) topic\.
+ Populate the source database using a sample database creation script\.
+ Use the AWS Schema Conversion Tool to convert the schema from the source database to the target database\. To convert the schema, follow the steps in the [Migrate Schema](CHAP_GettingStarted.SCT.md) topic\.
+ Create a replication instance to perform all the processes for the migration\. To do this, and the following tasks, follow the steps in the [Replication](CHAP_GettingStarted.Replication.md) topic\.
+ Specify source and target database endpoints\.
+ Create a task to define what tables and replication processes you want to use, and start replication\.
+ Verify that replication is working by running queries on the target database\.