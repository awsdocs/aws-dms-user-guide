# Discovering and evaluating databases for migration with AWS DMS Fleet Advisor<a name="CHAP_FleetAdvisor"></a>

You can use DMS Fleet Advisor to collect metadata and performance metrics from multiple database environments\. These collected metrics provide insight into your data infrastructure\. [DMS Fleet Advisor](http://aws.amazon.com/dms/fleet-advisor/) collects metadata and metrics from your on\-premises database and analytic servers from one or more central locations without the need to install it on every computer\. Currently, DMS Fleet Advisor supports Microsoft SQL Server, MySQL, Oracle, and PostgreSQL database servers\.

Based on data discovered from the network, you can build an inventory to define the list of database servers for further data collection\. After AWS DMS collects information about your servers, databases, and schemas, you can analyze the feasibility of your intended database migrations\.

For databases in your inventory that you plan to migrate to the AWS Cloud, DMS Fleet Advisor generates right\-sized target recommendations\. To generate target recommendations, DMS Fleet Advisor considers the metrics from your data collector and preferred settings\. After DMS Fleet Advisor generates recommendations, you can view detailed information for each target database configuration\. Your organization's database engineers and administrators can use DMS Fleet Advisor Target Recommendations to plan the migration of their on\-premises databases to AWS\. Also, you can explore different available migration options and export these recommendations\. Finally, you can use the AWS Pricing Calculator to further optimize the cost\.

For the list of supported source databases, see [Sources for DMS Fleet Advisor](CHAP_Introduction.Sources.md#CHAP_Introduction.Sources.FleetAdvisor)\.

For the list of databases that DMS Fleet Advisor uses to generate target recommendations, see [Targets for DMS Fleet Advisor](CHAP_Introduction.Targets.md#CHAP_Introduction.Targets.FleetAdvisor)\.

The following diagram illustrates the AWS DMS Fleet Advisor Target Recommendations process\.

![\[DMS Fleet Advisor target recommendations architecture diagram.\]](http://docs.aws.amazon.com/dms/latest/userguide/images/dms-fleet-advisor-diagram.png)

Use the following topics to better understand how to use AWS DMS Fleet Advisor\.

**Topics**
+ [Supported AWS Regions](#CHAP_FleetAdvisor.SupportedRegions)
+ [Getting started with DMS Fleet Advisor](fa-getting-started.md)
+ [Setting up AWS DMS Fleet Advisor](fa-prerequisites.md)
+ [Discovering databases for migration using data collectors](fa-data-collectors.md)
+ [Using inventories for analysis in AWS DMS Fleet Advisor](fa-inventory.md)
+ [Using the AWS DMS Fleet Advisor Target Recommendations feature](fa-recommendations.md)

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