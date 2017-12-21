# Using a MySQL\-Compatible Database as a Target for AWS Database Migration Service<a name="CHAP_Target.MySQL"></a>

You can migrate data to MySQL databases using AWS DMS, either from another MySQL database or from one of the other supported databases\.

MySQL versions 5\.5, 5\.6, and 5\.7, as well as MariaDB and Aurora MySQL, are supported\.

For additional details on working with MySQL databases as a target for AWS DMS, see the following sections\. 


+ [Prerequisites for Using a MySQL\-Compatible Database as a Target for AWS Database Migration Service](#CHAP_Target.MySQL.Prerequisites)
+ [Limitations on Using MySQL as a Target for AWS Database Migration Service](#CHAP_Target.MySQL.Limitations)
+ [Security Requirements When Using MySQL as a Target for AWS Database Migration Service](#CHAP_Target.MySQL.Security)
+ [Extra Connection Attributes When Using MySQL as a Target for AWS DMS](#CHAP_Target.MySQL.ConnectionAttrib)

## Prerequisites for Using a MySQL\-Compatible Database as a Target for AWS Database Migration Service<a name="CHAP_Target.MySQL.Prerequisites"></a>

Before you begin to work with a MySQL database as a target for AWS DMS, make sure that you have the following prerequisites:

+ A MySQL account with the required security settings\. For more information, see [Security Requirements When Using MySQL as a Target for AWS Database Migration Service](#CHAP_Target.MySQL.Security)\.

+ A MySQL database with the tables that you want to replicate accessible in your network\. AWS DMS supports the following MySQL editions:

  + MySQL Community Edition

  + MySQL Standard Edition

  + MySQL Enterprise Edition

  + MySQL Cluster Carrier Grade Edition

  + MariaDB

  + Amazon Aurora with MySQL compatibility

+  During a load, you should consider disabling foreign keys\. In order to disable foreign key checks on a MySQL\-compatible database during a load, you can add the following command to the **Extra Connection Attributes** in the **Advanced** section of the target MySQL, Aurora MySQL, MariaDB endpoint connection information: 

  ```
  initstmt=SET FOREIGN_KEY_CHECKS=0                    
  ```

## Limitations on Using MySQL as a Target for AWS Database Migration Service<a name="CHAP_Target.MySQL.Limitations"></a>

When using a MySQL database as a target, AWS DMS doesn't support the following:

+ The DDL statements Truncate Partition, Drop Table, and Rename Table\.

+ Using an `ALTER TABLE <table_name> ADD COLUMN <column_name>` statement to add columns to the beginning or the middle of a table\.

In addition, when you update a column's value to its existing value, MySQL returns a `0 rows affected` warning\. In contrast, Oracle performs an update of one row in this case\. The MySQL result generates an entry in the awsdms\_apply\_exceptions control table and the following warning:

```
Some changes from the source database had no impact when applied to
the target database. See awsdms_apply_exceptions table for details.
```

## Security Requirements When Using MySQL as a Target for AWS Database Migration Service<a name="CHAP_Target.MySQL.Security"></a>

When using MySQL as a target for data migration, you must provide MySQL account access to the AWS DMS user account\. This user must have read/write privileges in the MySQL database\.

To create the necessary privileges, run the following commands:

```
CREATE USER '<user acct>'@'%' IDENTIFIED BY <user password>';
GRANT ALTER, CREATE, DROP, INDEX, INSERT, UPDATE, DELETE, SELECT ON myschema.* TO '<user acct>'@'%';
GRANT ALL PRIVILEGES ON awsdms_control.* TO '<user acct>'@'%';
```

## Extra Connection Attributes When Using MySQL as a Target for AWS DMS<a name="CHAP_Target.MySQL.ConnectionAttrib"></a>

You can use extra connection attributes to configure your MySQL target\. You specify these settings when you create the target endpoint\.

The following table shows extra configuration settings you can use when creating a MySQL target for AWS DMS:

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Target.MySQL.html)