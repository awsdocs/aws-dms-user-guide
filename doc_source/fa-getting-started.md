# Getting started with DMS Fleet Advisor<a name="fa-getting-started"></a>

You can use DMS Fleet Advisor to discover your source on\-premises databases for migration to the AWS Cloud\. Then, you can determine the right migration target in the AWS Cloud for each of your on\-premises databases\. Use the following workflow to create an inventory of your source databases and generate target recommendations\.

1. Create an Amazon S3 bucket, IAM policies, roles, and users\. For more information, see [Creating required resources](fa-resources.md)\.

1. Create database users with the minimum permissions required for the DMS data collector\. For more information, see [Creating database users](fa-database-users.md)\.

1. Create and download a data collector\. For more information, see [Creating a data collector](fa-data-collectors-create.md)\.

1. Install the data collector in your local environment\. Next, configure your data collector to make sure that it can send the collected data to DMS Fleet Advisor\. For more information, see [Installing a data collector](fa-data-collectors-install.md)\.

1. Discover the OS and database servers in your data environment\. For more information, see [Discovering OS and database servers](fa-discovery.md)\.

1. Collect database metadata and resource utilization metrics\. For more information, see [Collecting data](fa-collecting.md)\.

1. Analyze your source databases and schemas\. DMS Fleet Advisor runs the large\-scale assessment of your databases to identify similar schemas\. For more information, see [Using inventories for analysis in AWS DMS Fleet Advisor](fa-inventory.md)\.

1. Generate, view, and save a local copy of the target recommendations for your source databases\. For more information, see [Target recommendations](fa-recommendations.md)\.

After you determine the migration target for each source database, you can use DMS Schema Conversion to convert your database schemas to a new platform\. Then, you can use AWS DMS to migrate data\. For more information, see [Converting database schemas using DMS Schema Conversion](CHAP_SchemaConversion.md) and [What is AWS Database Migration Service?](Welcome.md)

The following video introduces the DMS Fleet Advisor user interface and helps you get familiar with this service\.

[![AWS Videos](http://img.youtube.com/vi/https://www.youtube.com/embed/2UmTXVIlDLw/0.jpg)](http://www.youtube.com/watch?v=https://www.youtube.com/embed/2UmTXVIlDLw)