# Using a database inventory for analysis<a name="fa-inventory-database"></a>

To view a list of all databases on all the discovered servers within your network from which data was collected, use the following procedure\.

**To view a list of databases on your network servers that data was collected from**

1. Choose **Inventory** on the console\.

   The **Inventory** page opens\.

1. Choose the **Databases** tab\. 

   A list of discovered databases appears\.  
![\[Database inventory.\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-dmsstudio-inv-db.png)

1. Choose **Analyze inventories** to determine schema properties, such as similarity and complexity\. The amount of time the process takes depends on the number of objects to analyze, but it won't take more than one hour\. Results from the analysis are found on the **Schemas** tab located on the **Inventory** page\. 

   The entire inventory is analyzed together to identify duplicate schemas\.