# Using inventories for analysis in AWS DMS Studio Fleet Advisor<a name="CHAP_DMSStudio.Inventory"></a>

To check the feasibility of potential database migrations, you can work with inventories of discovered databases and schemas\. You can use the information in these inventories to understand which databases and schemas are good candidates for migration\.

You can access database and schema inventories from the DMS Studio console\. To do so, choose **Fleet Advisor** and **Inventory** from the console\.

![\[DMS Studio console toggle\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-dmsstudio-console-2.png)

## Using a database inventory for analysis<a name="CHAP_DMSStudio.Inventory.Database"></a>

To view a list of all databases on all the discovered servers within your network from which data was collected, use the following procedure\.

**To view a list of databases on your network servers that data was collected from**

1. Choose **Fleet Advisor** and **Inventory** from the DMS Studio console\. 

   The **Inventory** page opens\. 

1. Choose the** Databases** tab\. 

   A list of discovered databases appears\.  
![\[Database inventory\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-dmsstudio-inv-db.png)

1. Choose **Analyze inventories** to determine schema properties such as similarity and complexity\. 

   To identify duplicate schemas, the entire inventory is analyzed together\.

## Using a schema inventory for analysis<a name="CHAP_DMSStudio.Inventory.Schema"></a>

To view a list of database schemas discovered on servers within your network from which data was collected, use the following procedure\.

**To view a list of schemas on your network servers that data was collected from**

1. Choose **Inventory** from the DMS Studio console\. The **Inventory** page opens\. 

1. Choose the **Schemas** tab\. A list of schemas appears\. 

1. Select a schema from the list to get information about it, including server, database, size, and complexity\.

   For each schema, an object summary provides information about object types, number of objects, object size, and lines of code\.

1. \(Optional\) Choose **Analyze inventories** to identify duplicate schemas\. DMS Fleet Advisor considers those schemas that are about 80 percent or more similar to each other to be duplicate schemas\.

![\[Schema Inventory\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-dmsstudio-inv-schema.png)

After you have identified schemas to migrate, you can convert schemas for migration by using the AWS Schema Conversion Tool, accessible from the DMS Studio console\. 