# Using inventories for analysis in AWS DMS Fleet Advisor<a name="CHAP_FleetAdvisor.Inventory"></a>

To check the feasibility of potential database migrations, you can work with inventories of discovered databases and schemas\. You can use the information in these inventories to understand which databases and schemas are good candidates for migration\.

You can access database and schema inventories on the console\. To do so, choose **Inventory** on the console\.

![\[console toggle\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-console-nav-22.png)

## Using a database inventory for analysis<a name="CHAP_FleetAdvisor.Inventory.Database"></a>

To view a list of all databases on all the discovered servers within your network from which data was collected, use the following procedure\.

**To view a list of databases on your network servers that data was collected from**

1. Choose **Inventory** on the console\.

   The **Inventory** page opens\.

1. Choose the **Databases** tab\. 

   A list of discovered databases appears\.  
![\[Database inventory\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-dmsstudio-inv-db.png)

1. Choose **Analyze inventories** to determine schema properties, such as similarity and complexity\. How much time the process takes depends on the number of objects to analyze, but it won't take more than one hour\. Results from the analysis are found on the **Schemas** tab located on the **Inventory** page\. 

   The entire inventory is analyzed together to identify duplicate schemas\.

## Using a schema inventory for analysis<a name="CHAP_FleetAdvisor.Inventory.Schema"></a>

You can view a list of database schemas discovered on servers within your network from which the data was collected\. Perform the following procedure\.

**To view a list of schemas on your network servers that data was collected from**

1. Choose **Inventory** on the console\. The **Inventory** page opens\.

1. Choose the **Schemas** tab\. A list of schemas appears\.

1. Select a schema in the list to view information about it, including server, database, size, and complexity\.

   For each schema, you can view an object summary that provides information about object types, number of objects, object size, and lines of code\.

1. \(Optional\) Choose **Analyze inventories** to identify duplicate schemas\. DMS Fleet Advisor analyzes database schemas to define the intersection of their objects\. Schemas with intersections of more than 50 percent can be considered duplicates\. Detailed analysis results are provided in the columns, **Similarity** and **Original schema**, found on the **Schemas** tab of the **Inventory** page\.

1. You can export inventory information to a `.csv` file for further review\.

![\[Schema Inventory\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-dmsstudio-inv-schema.png)

To identify schemas to migrate and determine the migration target, you can use AWS Schema Conversion Tool \(AWS SCT\)\. For more information, see [Using a new project wizard in AWS SCT](https://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_UserInterface.html#CHAP_UserInterface.Wizard)\.

After you have identified schemas to migrate, you can convert schemas using AWS SCT or DMS Schema Conversion\. For more information about DMS Schema Conversion, see [Converting database schemas using DMS Schema Conversion](CHAP_SchemaConversion.md)\.