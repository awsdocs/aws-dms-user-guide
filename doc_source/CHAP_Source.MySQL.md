# Using a MySQL\-Compatible Database as a Source for AWS DMS<a name="CHAP_Source.MySQL"></a>

You can migrate data from one or many MySQL, MariaDB, or Amazon Aurora MySQL databases using AWS Database Migration Service\. With a MySQL\-compatible database as a source, you can migrate data to either another MySQL\-compatible database or one of the other supported databases\. MySQL versions 5\.5, 5\.6, and 5\.7, as well as MariaDB and Amazon Aurora MySQL, are supported for on\-premises, Amazon RDS, and Amazon EC2 instance databases\. To enable change data capture \(CDC\) with Amazon RDS MySQL, you must use Amazon RDS MySQL version 5\.6 or higher\.

**Note**  
Regardless of the source storage engine \(MyISAM, MEMORY, etc\.\), AWS DMS creates the MySQL\-compatible target table as an InnoDB table by default\. If you need to have a table that uses a storage engine other than InnoDB, you can manually create the table on the MySQL\-compatible target and migrate the table using the "Do Nothing" mode\. For more information about the "Do Nothing" mode, see [Full Load Task Settings](CHAP_Tasks.CustomizingTasks.TaskSettings.FullLoad.md)\.

You can use SSL to encrypt connections between your MySQL\-compatible endpoint and the replication instance\. For more information on using SSL with a MySQL\-compatible endpoint, see [Using SSL With AWS Database Migration Service](CHAP_Security.SSL.md)\.

For additional details on working with MySQL\-compatible databases and AWS DMS, see the following sections\.


+ [Prerequisites for Using a MySQL Database as a Source for AWS DMS](#CHAP_Source.MySQL.Prerequisites)
+ [Prerequisites for Using an Amazon RDS for MySQL Database as a Source for AWS DMS](#CHAP_Source.MySQL.RDSPrerequisites)
+ [Limitations on Using a MySQL Database as a Source for AWS DMS](#CHAP_Source.MySQL.Limitations)
+ [Security Requirements for Using a MySQL Database as a Source for AWS DMS](#CHAP_Source.MySQL.Security)
+ [Configuring a MySQL Database as a Source for AWS DMS](#CHAP_Source.MySQL.Configuration)
+ [Extra Connection Attributes When Using MySQL as a Source for AWS DMS](#CHAP_Source.MySQL.ConnectionAttrib)

## Prerequisites for Using a MySQL Database as a Source for AWS DMS<a name="CHAP_Source.MySQL.Prerequisites"></a>

Before you begin to work with a MySQL database as a source for AWS DMS, make sure that you have the following prerequisites:

+ A MySQL account with the required security settings\. For more information, see [Security Requirements for Using a MySQL Database as a Source for AWS DMS](#CHAP_Source.MySQL.Security)\.

+ A MySQL\-compatible database with the tables that you want to replicate accessible in your network\.

  + MySQL Community Edition

  + MySQL Standard Edition

  + MySQL Enterprise Edition

  + MySQL Cluster Carrier Grade Edition

  + MariaDB

  + Amazon Aurora MySQL

+ If your source is an Amazon RDS MySQL or MariaDB DB instance or an Amazon Aurora MySQL cluster, you must enable automatic backups\. For more information on setting up automatic backups, see the *[Amazon RDS User Guide](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithAutomatedBackups.html)*\.

+ If you use change data capture \(CDC\), you must enable and configure binary logging\. To enable binary logging, the following parameters must be configured in MySQL’s `my.ini` \(Windows\) or `my.cnf` \(UNIX\) file:    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Source.MySQL.html)

+ To use change data capture \(CDC\) with an Amazon RDS MySQL DB instance as a source, AWS DMS needs access to the binary logs\. Amazon RDS is fairly aggressive in clearing binary logs from the DB instance\. To use CDC with a MySQL DB instance on Amazon RDS, you should increase the amount of time the binary logs remain on the MySQL DB instance\. For example, to increase log retention to 24 hours, you would run the following command: 

  ```
   call mysql.rds_set_configuration('binlog retention hours', 24);
  ```

+ To replicate clustered \(NDB\) tables using AWS DMS, the following parameters must be configured in MySQL’s `my.ini` \(Windows\) or `my.cnf` \(UNIX\) file\. Replicating clustered \(NDB\) tables is required only when you use CDC\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Source.MySQL.html)

## Prerequisites for Using an Amazon RDS for MySQL Database as a Source for AWS DMS<a name="CHAP_Source.MySQL.RDSPrerequisites"></a>

When using an Amazon RDS for MySQL database as a source for AWS DMS, make sure that you have the following prerequisites:

+ You must enable automatic backups\. For more information on setting up automatic backups, see the *[Amazon RDS User Guide](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithAutomatedBackups.html)*\.

+ To use change data capture \(CDC\) with an Amazon RDS MySQL DB instance as a source, AWS DMS needs access to the binary logs\. Amazon RDS is fairly aggressive in clearing binary logs from the DB instance\. To use CDC with a MySQL DB instance on Amazon RDS, you should increase the amount of time the binary logs remain on the MySQL DB instance to 24 hours or greater\. For example, to increase log retention to 24 hours, you would run the following command: 

  ```
   call mysql.rds_set_configuration('binlog retention hours', 24);
  ```

+ The `binlog_format `parameter should be set to "ROW\."

## Limitations on Using a MySQL Database as a Source for AWS DMS<a name="CHAP_Source.MySQL.Limitations"></a>

When using a MySQL database as a source, AWS DMS doesn't support the following:

+ The DDL statements Truncate Partition, Drop Table, and Rename Table\.

+ Using an `ALTER TABLE <table_name> ADD COLUMN <column_name>` statement to add columns to the beginning or the middle of a table\.

+  Capturing changes from tables whose names contain both uppercase and lowercase characters if the source MySQL instance is installed on an OS with a file system where file names are treated as case in\-sensitive, for example Windows and OS X using HFS\+\.

+ The AR\_H\_USER header column\.

+ The AUTO\_INCREMENT attribute on a column is not migrated to a target database column\.

+ Capturing changes when the binary logs are not stored on standard block storage\. For example, CDC does not work when the binary logs are stored on Amazon S3\.

## Security Requirements for Using a MySQL Database as a Source for AWS DMS<a name="CHAP_Source.MySQL.Security"></a>

As a security requirement, the AWS DMS user must have the ReplicationAdmin role with the following privileges:

+ **REPLICATION CLIENT** – This privilege is required for change data capture \(CDC\) tasks only\. In other words, full\-load\-only tasks don't require this privilege\.

+ **REPLICATION SLAVE** – This privilege is required for change data capture \(CDC\) tasks only\. In other words, full\-load\-only tasks don't require this privilege\.

+ **SUPER** – This privilege is required only in MySQL versions prior to 5\.6\.6\.

The AWS DMS user must also have SELECT privileges for the source tables designated for replication\.

## Configuring a MySQL Database as a Source for AWS DMS<a name="CHAP_Source.MySQL.Configuration"></a>

You can use extra connection attributes to configure the MySQL source\. For more information about MySQL extra connection attributes, see [Using Extra Connection Attributes with AWS Database Migration Service](CHAP_Introduction.ConnectionAttributes.md)\.

## Extra Connection Attributes When Using MySQL as a Source for AWS DMS<a name="CHAP_Source.MySQL.ConnectionAttrib"></a>

You can use extra connection attributes to configure your MySQL source\. You specify these settings when you create the source endpoint\.

The following table shows the extra connection attributes available when using MySQL as a source for AWS DMS:

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Source.MySQL.html)