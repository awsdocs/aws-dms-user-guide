# Discover and evaluate databases for migration using AWS DMS Fleet Advisor<a name="CHAP_FleetAdvisor"></a>

AWS DMS Fleet Advisor collects data from multiple database environments to provide insight into your data infrastructure\. [DMS Fleet Advisor](http://aws.amazon.com/dms/fleet-advisor/) collects data from your on\-premises database and analytic servers from one or more central locations without the need to install it on every computer\. Currently, DMS Fleet Advisor supports Microsoft SQL Server, MySQL, Oracle, and PostgreSQL database servers\.

Based on data discovered from the network, you can build an inventory to define the list of database servers for further data collection\. As details about these servers, databases, and schemas are collected, you can analyze the feasibility of your intended database migrations\.

Use the following topics to better understand how to use AWS DMS Fleet Advisor\.

**Topics**
+ [Supported AWS Regions](#CHAP_FleetAdvisor.SupportedRegions)
+ [Creating required AWS resources for AWS DMS Fleet Advisor](CHAP_FleetAdvisor.Prerequisites.md)
+ [Creating database users for AWS DMS Fleet Advisor](CHAP_FleetAdvisor.DBUser.md)
+ [Creating a data collector for AWS DMS Fleet Advisor](CHAP_FleetAdvisor.DataCollector.md)
+ [Installing and configuring a data collector](CHAP_FleetAdvisor.InstallDataCollector.md)
+ [Discovering OS and database servers to monitor](CHAP_FleetAdvisor.Discovering.md)
+ [Managing monitored objects](CHAP_FleetAdvisor.ManagingObjects.md)
+ [Using SSL with AWS DMS Fleet Advisor](CHAP_FleetAdvisor.Ssl.md)
+ [Collecting data for AWS DMS Fleet Advisor](CHAP_FleetAdvisor.Collecting.md)
+ [Using inventories for analysis in AWS DMS Fleet Advisor](CHAP_FleetAdvisor.Inventory.md)
+ [Troubleshooting for DMS data collector](CHAP_FleetAdvisor.Troubleshooting.md)

## Supported AWS Regions<a name="CHAP_FleetAdvisor.SupportedRegions"></a>

You can use DMS Fleet Advisor in the following AWS Regions\.


| Region Name | Region | 
| --- | --- | 
| US East \(N\. Virginia\) | us\-east\-1 | 
| US East \(Ohio\) | us\-east\-2 | 
| US West \(N\. California\) | us\-west\-1 | 
| US West \(Oregon\) | us\-west\-2 | 
| Asia Pacific \(Hong Kong\) | ap\-east\-1 | 
| Asia Pacific \(Tokyo\) | ap\-northeast\-1 | 
| Asia Pacific \(Seoul\) | ap\-northeast\-2 | 
| Asia Pacific \(Mumbai\) | ap\-south\-1 | 
| Asia Pacific \(Singapore\) | ap\-southeast\-1 | 
| Asia Pacific \(Sydney\) | ap\-southeast\-2 | 
| Canada \(Central\) | ca\-central\-1 | 
| Europe \(Frankfurt\) | eu\-central\-1 | 
| Europe \(Stockholm\) | eu\-north\-1 | 
| Europe \(Ireland\) | eu\-west\-1 | 
| Europe \(London\) | eu\-west\-2 | 
| Europe \(Paris\) | eu\-west\-3 | 
| Canada \(Central\) | ca\-central\-1 | 
| South America \(São Paulo\) | sa\-east\-1 | 