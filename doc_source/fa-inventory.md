# Using inventories for analysis in AWS DMS Fleet Advisor<a name="fa-inventory"></a>

To check the feasibility of potential database migrations, you can work with inventories of discovered databases and schemas\. You can use the information in these inventories to understand which databases and schemas are good candidates for migration\.

You can access database and schema inventories on the console\. To do so, choose **Inventory** on the console\.

![\[The Inventory tab of the DMS console.\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-console-nav-22.png)

DMS Fleet Advisor analyzes your database schemas to determine the similarity of different schemas\. This analysis doesn't compare the actual code for objects\. DMS Fleet Advisor compares only the names of schema objects, such as functions and procedures, to identify similar objects in different database schemas\.

**Topics**
+ [Using a database inventory for analysis](fa-inventory-database.md)
+ [Using a schema inventory for analysis](fa-inventory-schema.md)