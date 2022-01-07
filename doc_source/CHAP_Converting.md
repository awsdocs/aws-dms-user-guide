# Converting database schemas using the AWS SCT<a name="CHAP_Converting"></a>

You can use the AWS Schema Conversion Tool \(AWS SCT\) to convert your existing database schemas from one database engine to another\. Converting a database using the AWS SCT user interface can be fairly simple, but there are several things to consider before you do the conversion\. 

For example, you can use the AWS SCT to do the following: 
+ You can use AWS SCT to copy an existing on\-premises database schema to an Amazon RDS DB instance running the same engine\. You can use this feature to analyze potential cost savings of moving to the cloud and of changing your license type\. 
+ In some cases, database features can't be converted to equivalent Amazon RDS features\. If you host and self\-manage a database on the Amazon Elastic Compute Cloud \(Amazon EC2\) platform, you can emulate these features by substituting AWS services for them\.
+ AWS SCT automates much of the process of converting your online transaction processing \(OLTP\) database schema to an Amazon Relational Database Service \(Amazon RDS\) MySQL DB instance, an Amazon Aurora DB cluster, or a PostgreSQL DB instance\. The source and target database engines contain many different features and capabilities, and AWS SCT attempts to create an equivalent schema in your Amazon RDS DB instance wherever possible\. If no direct conversion is possible, AWS SCT provides a list of possible actions for you to take\. 

**Topics**
+ [Creating migration rules in the AWS SCT](#CHAP_Converting.MigrationRules)
+ [Converting your schema by using the AWS SCT](#CHAP_Converting.Convert)
+ [Handling manual conversions in the AWS SCT](#CHAP_Converting.Manual)
+ [Updating and refreshing your converted schema in the AWS SCT](#CHAP_Converting.UpdateRefresh)
+ [Saving and applying your converted schema in the AWS SCT](#CHAP_Converting.SaveAndApply)
+ [Comparing database schemas](CHAP_Converting.SchemaCompare.md)
+ [Finding related transformed objects](CHAP_Converting.RelatedObjects.md)

AWS SCT supports the following online transaction processing \(OLTP\) conversions\. 


| Source database | Target database | 
| --- | --- | 
|  IBM Db2 LUW \(versions 9\.1, 9\.5, 9\.7, 10\.5, 11\.1, and 11\.5\)  |  Amazon Aurora MySQL\-Compatible Edition, Amazon Aurora PostgreSQL\-Compatible Edition, MariaDB 10\.5, MySQL, PostgreSQL  | 
| Microsoft Azure SQL Database |  Aurora MySQL, Aurora PostgreSQL, MySQL, PostgreSQL  | 
|  Microsoft SQL Server \(version 2008 R2 and later\)  |  Aurora MySQL, Aurora PostgreSQL, MariaDB 10\.5, Microsoft SQL Server, MySQL, PostgreSQL   | 
|  MySQL \(version 5\.5 and later\)  |  Aurora PostgreSQL, MySQL, PostgreSQL You can migrate schema and data from MySQL to an Aurora MySQL DB cluster without using AWS SCT\. For more information, see [Migrating data to an Amazon Aurora DB cluster](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Aurora.Migrate.html)\.   | 
|  Oracle \(version 10\.2 and later\)  |   Aurora MySQL, Aurora PostgreSQL, MariaDB 10\.5, MySQL, Oracle, PostgreSQL   | 
|  PostgreSQL \(version 9\.1 and later\)  |  Aurora MySQL, Aurora PostgreSQL, MySQL, PostgreSQL  | 
| SAP ASE \(12\.5, 15\.0, 15\.5, 15\.7, and 16\.0\) |   Aurora MySQL, Aurora PostgreSQL, MariaDB 10\.5, MySQL, PostgreSQL   | 

For information about converting a data warehouse schema, see [Converting data warehouse schemas to Amazon Redshift using the AWS SCT](CHAP_Converting.DW.md)\. 

To convert your database schema to Amazon RDS, you take the following high\-level steps: 
+  [Creating migration rules in the AWS SCT](#CHAP_Converting.MigrationRules) – Before you convert your schema with AWS SCT, you can set up rules that change the data type of columns, move objects from one schema to another, and change the names of objects\. 
+ [Converting your schema by using the AWS SCT](#CHAP_Converting.Convert) – AWS SCT creates a local version of the converted schema for you to review, but it doesn't apply it to your target DB instance until you are ready\. 
+ [Creating migration assessment reports with AWS SCT](CHAP_AssessmentReport.md) – AWS SCT creates a database migration assessment report that details the schema elements that can't be converted automatically\. You can use this report to identify where you need to create a schema in your Amazon RDS DB instance that is compatible with your source database\. 
+ [Handling manual conversions in the AWS SCT](#CHAP_Converting.Manual) – If you have schema elements that can't be converted automatically, you have two choices: update the source schema and then convert again, or create equivalent schema elements in your target Amazon RDS DB instance\. 
+ [Updating and refreshing your converted schema in the AWS SCT](#CHAP_Converting.UpdateRefresh) – You can update your AWS SCT project with the most recent schema from your source database\. 
+ [Saving and applying your converted schema in the AWS SCT](#CHAP_Converting.SaveAndApply) – When you are ready, have AWS SCT apply the converted schema in your local project to your target Amazon RDS DB instance\. 

## Creating migration rules in the AWS SCT<a name="CHAP_Converting.MigrationRules"></a>

Before you convert your schema with the AWS SCT, you can set up migration rules\. *Migration rules* can do such things as change the data type of columns, move objects from one schema to another, and change the names of objects\. For example, suppose that you have a set of tables in your source schema named `test_TABLE_NAME`\. You can set up a rule that changes the prefix `test_` to the prefix `demo_` in the target schema\. 

**Note**  
You can only create migration rules for different source and target database engines\. 

You can create migration rules that perform the following tasks: 
+ Change data type 
+ Move objects 
+ Rename objects 
+ Add, remove, or replace a prefix 
+ Add, remove, or replace a suffix 

You can create migration rules for the following objects: 
+ Database 
+ Schema 
+ Table 
+ Column 

### Creating migration rules<a name="CHAP_Converting.MigrationRules.Map"></a>

You can create migration rules and save the rules as part of your project\. With your project open, use the following procedure to create migration rules\. 

**To create migration rules**

1. On the **View** menu, choose **Mapping view**\. 

1.  In **Server mappings**, choose a pair of source and target servers\. 

1. Choose **New migration rule**\. The **Transformation rules** dialog box appears\. 

1. Choose **Add new rule**\. A new row is added to the list of rules\. 

1. Configure your rule:

   1. For **Name**, enter a name for your rule\. 

   1. For **For**, choose the type of object that the rule applies to\. 

   1. For **where**, enter a filter to apply to objects before applying the migration rule\. The where clause is evaluated by using a like clause\. You can enter an exact name to select one object, or you can enter a pattern to select multiple objects\. 

      The fields available for the **where** clause are different depending on the type of the object\. For example, if the object type is schema there is only one field available, for the schema name\. 

   1. For **Actions**, choose the type of migration rule that you want to create\. 

   1. Depending on the rule type, enter one or two additional values\. For example, to rename an object, enter the new name of the object\. To replace a prefix, enter the old prefix and the new prefix\. 

1. After you have configured your migration rule, choose **Save** to save your rule\. You can also choose **Cancel** to cancel your changes\.   
![\[The transformation rules dialog box\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/images/transformation-rules.png)

1. After you are done adding, editing, and deleting rules, choose **Save All** to save all your changes\. 

1. Choose **Close** to close the **Transformation rules** dialog box\. 

You can use the toggle icon to turn off a migration rule without deleting it\. You can use the copy icon to duplicate an existing migration rule\. You can use the pencil icon to edit an existing migration rule\. You can use the delete icon to delete an existing migration rule\. To save any changes you make to your migration rules, choose **Save All**\. 

### Exporting migration rules<a name="CHAP_Converting.MigrationRules.Export"></a>

If you use AWS DMS to migrate your data from your source database to your target database, you can provide information about your migration rules to AWS DMS\. For more information about tasks, see [Working with AWS Database Migration Service replication tasks](https://docs.aws.amazon.com/dms/latest/userguide/CHAP_Tasks.html)\. 

**To export migration rules**

1. In the AWS Schema Conversion Tool, choose **Mapping View** on the **View** menu\. 

1.  In **Migration rules**, choose a migration rule and then choose **Modify migration rule**\. 

1.  Choose **Export script for AWS DMS**\. 

1. Browse to the location where you want to save your script, and then choose **Save**\. Your migration rules are saved as a JSON script that can be consumed by AWS DMS\. 

## Converting your schema by using the AWS SCT<a name="CHAP_Converting.Convert"></a>

After you have connected your project to both your source database and your target Amazon RDS DB instance, your AWS Schema Conversion Tool project displays the schema from your source database in the left panel\. The schema is presented in a tree\-view format, and each node of the tree is lazy loaded\. When you choose a node in the tree view, AWS SCT requests the schema information from your source database at that time\. 

You can choose schema items from your source database and then convert the schema to equivalent schema for the DB engine of your target DB instance\. You can choose any schema item from your source database to convert\. If the schema item that you choose depends on a parent item, then AWS SCT also generates the schema for the parent item\. For example, suppose that you choose a table to convert\. If so, AWS SCT generates the schema for the table, and the database that the table is in\. 

### Converting schema<a name="CHAP_Converting.Convert.Procedure"></a>

To convert schema from your source database, choose a schema object to convert from the left panel of your project\. Open the context \(right\-click\) menu for the object, and then choose **Convert schema**, as shown following\. 

![\[Convert schema\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/images/transform_schema.png)

After you have converted the schema from your source database, you can choose schema items from the left panel of your project and view the converted schema in the center panels of your project\. The lower\-center panel displays the properties of and the SQL command to create the converted schema, as shown following\. 

![\[Choose source schema item\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/images/select_schema_item.png)

After you have converted your schema, you can save your project\. The schema information from your source database is saved with your project\. This functionality means that you can work offline without being connected to your source database\. AWS SCT connects to your source database to update the schema in your project if you choose **Refresh from Database** for your source database\. For more information, see [Updating and refreshing your converted schema in the AWS SCT](#CHAP_Converting.UpdateRefresh)\. 

You can create a database migration assessment report of the items that can't be converted automatically\. The assessment report is useful for identifying and resolving schema items that can't be converted automatically\. For more information, see [Creating migration assessment reports with AWS SCT](CHAP_AssessmentReport.md)\. 

When AWS SCT generates a converted schema, it doesn't immediately apply it to the target DB instance\. Instead, the converted schema is stored locally until you are ready to apply it to the target DB instance\. For more information, see [Applying your converted schema](#CHAP_Converting.Applying)\. 

### Editing converted schema<a name="CHAP_Converting.Edit"></a>

You can edit converted schema and save the changes as part of your project\.

**To edit converted schema**

1. In the left panel that displays the schema from your source database, choose the schema item that you want to edit the converted schema for\. 

1. In the lower\-center panel that displays the converted schema for the selected item, choose the **SQL** tab\. 

1. In the text displayed for the **SQL** tab, change the schema as needed\. The schema is automatically saved with your project as you update it\.   
![\[Refresh the schema from the target DB instance\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/images/edit_converted_schema.png)

The changes that you make to converted schema are stored with your project as you make updates\. If you newly convert a schema item from your source database, and you have made updates to previously converted schema for that item, those existing updates are replaced by the newly converted schema item based on your source database\. 

### Clearing a converted schema<a name="CHAP_Converting.Clear"></a>

Until you apply the schema to your target DB instance, AWS SCT only stores the converted schema locally in your project\. You can clear the planned schema from your project by choosing the tree\-view node for your DB instance, and then choosing **Refresh from Database**\. Because no schema has been written to your target DB instance, refreshing from the database removes the planned schema elements in your AWS SCT project to match what exists in your source DB instance\. 

## Handling manual conversions in the AWS SCT<a name="CHAP_Converting.Manual"></a>

The assessment report includes a list of items that can't be converted automatically to the database engine of your target Amazon RDS DB instance\. For each item that can't be converted, there is an action item on the **Action Items** tab\. 

You can respond to the action items in the assessment report in the following ways: 
+ Modify your source database schema\.
+ Modify your target database schema\.

### Modifying your source schema<a name="CHAP_Converting.Manual.Source"></a>

For some items, it might be easier to modify the database schema in your source database to a schema that can be converted automatically\. First, verify that the new changes are compatible with your application architecture, then update the schema in your source database\. Finally, refresh your project with the updated schema information\. You can then convert your updated schema, and generate a new database migration assessment report\. The action items no longer appear for the items that changed in the source schema\. 

The advantage of this process is that your updated schema is always available when you refresh from your source database\. 

### Modifying your target schema<a name="CHAP_Converting.Manual.Target"></a>

For some items, it might be easier to apply the converted schema to your target database, and then add equivalent schema items manually to your target database for the items that couldn't be converted automatically\. You can write all of the schema that can be converted automatically to your target DB instance by applying the schema\. For more information, see [Saving and applying your converted schema in the AWS SCT](#CHAP_Converting.SaveAndApply)\. 

The schema that are written to your target DB instance don't contain the items that can't be converted automatically\. After applying the schema to your target DB instance, you can then manually create schema in your target DB instance that are equivalent to those in the source database\. The action items in the database migration assessment report contain suggestions for how to create the equivalent schema\. 

**Warning**  
If you manually create schema in your target DB instance, save a copy of any manual work that you do\. If you apply the converted schema from your project to your target DB instance again, it overwrites the manual work you have done\. 

In some cases, you can't create equivalent schema in your target DB instance\. You might need to re\-architect a portion of your application and database to use the functionality that is available from the DB engine for your target DB instance\. In other cases, you can simply ignore the schema that can't be converted automatically\. 

## Updating and refreshing your converted schema in the AWS SCT<a name="CHAP_Converting.UpdateRefresh"></a>

You can update both the source schema and the target schema in your AWS Schema Conversion Tool project\. 
+ **Source** – If you update the schema for your source database, AWS SCT replaces the schema in your project with the latest schema from your source database\. Using this functionality, you can update your project if changes have been made to the schema of your source database\. 
+ **Target** – If you update the schema for your target Amazon RDS DB instance, AWS SCT replaces the schema in your project with the latest schema from your target DB instance\. If you haven't applied any schema to your target DB instance, AWS SCT clears the converted schema from your project\. You can then convert the schema from your source database for a clean target DB instance\. 

You update the schema in your AWS SCT project by choosing **Refresh from Database**\. 

**Note**  
When you refresh your schema, AWS SCT loads metadata only as it is needed\. To fully load all of your database's schema, open the context \(right\-click\) menu for your schema, and choose **Load schema**\. For example, you can use this option to load metadata for your database all at once, and then work offline\.

## Saving and applying your converted schema in the AWS SCT<a name="CHAP_Converting.SaveAndApply"></a>

When the AWS Schema Conversion Tool generates converted schema \(as shown in [Converting your schema by using the AWS SCT](#CHAP_Converting.Convert)\), it doesn't immediately apply the converted schema to the target DB instance\. Instead, converted schema are stored locally in your project until you are ready to apply them to the target DB instance\. Using this functionality, you can work with schema items that can't be converted automatically to your target DB engine\. For more information on items that can't be converted automatically, see [Creating migration assessment reports with AWS SCT](CHAP_AssessmentReport.md)\. 

You can optionally have the tool save your converted schema to a file as a SQL script prior to applying the schema to your target DB instance\. You can also have the tool apply the converted schema directly to your target DB instance\. 

### Saving your converted schema to a file<a name="CHAP_Converting.Saving"></a>

You can save your converted schema as SQL scripts in a text file\. By using this approach, you can modify the generated SQL scripts from AWS SCT to address items that the tool can't convert automatically\. You can then run your updated scripts on your target DB instance to apply your converted schema to your target database\. 

**To save your converted schema as SQL scripts**

1.  Choose your schema and open the context \(right\-click\) menu\. 

1.  Choose **Save as SQL**\. 

1.  Enter the name of the file and choose **Save**\. 

1.  Save your converted schema using one of the following options: 
   + **Single file**
   + **Single file per stage**
   + **Single file per statement**

**To choose the format of the SQL script**

1. On the **Settings** menu, choose **Project settings**\. 

1.  Choose **Save scripts**\. 

1.  For **Vendor**, choose the database platform\. 

1.  For **Save SQL scripts to**, choose how you want to save your database schema script\. 

1.  Choose **OK** to save the settings\. 

### Applying your converted schema<a name="CHAP_Converting.Applying"></a>

When you are ready to apply your converted schema to your target Amazon RDS DB instance, choose the schema element from the right panel of your project\. Open the context \(right\-click\) menu for the schema element, and then choose **Apply to database**, as shown following\. 

![\[Apply to database\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/images/write_to_database.png)

### The extension pack schema<a name="CHAP_Converting.SaveAndApply.Ext"></a>

The first time that you apply your converted schema to your target DB instance, AWS SCT adds an additional schema to your target DB instance\. This schema implements system functions of the source database that are required when writing your converted schema to your target DB instance\. The schema is called the extension pack schema\. 

Don't modify the extension pack schema, or you might encounter unexpected results in the converted schema that is written to your target DB instance\. When your schema is fully migrated to your target DB instance, and you no longer need AWS SCT, you can delete the extension pack schema\. 

The extension pack schema is named according to your source database as follows: 
+ IBM Db2 LUW: `aws_db2_ext`
+ Microsoft SQL Server: `aws_sqlserver_ext`
+ MySQL: `aws_mysql_ext`
+ Oracle: `aws_oracle_ext`
+ PostgreSQL: `aws_postgresql_ext`
+ SAP ASE: `aws_sapase_ext`

For more information, see [Using the AWS Lambda functions from the AWS SCT extension pack](CHAP_ExtensionPack.md#CHAP_ExtensionPack.OLTP)\. 