# Discovering databases for migration using data collectors<a name="fa-data-collectors"></a>

To discover your source data infrastructure, you can use AWS DMS data collectors\. A *data collector* is a Windows application that you install in your local environment\. This application connects to your data environment and collects metadata and performance metrics from your on\-premises database and analytic servers\. Then DMS Fleet Advisor uses your data collector to build an inventory of servers, databases, and schemas that you can migrate to the AWS Cloud\.

AWS DMS has the maximum number of data collectors that you can create for your AWS account\. See the following section for information about AWS DMS service quotas [Quotas for AWS Database Migration Service](CHAP_Limits.md)\.

You can run the DMS data collector in Windows\. However, your DMS data collector can collect data from all supported database vendors regardless of the OS server where they run\.

The DMS data collector uses a protected RTPS protocol with TLS encryption to establish a secure connection with DMS Fleet Advisor\. Therefore, your data is encrypted during transit by default\.

**Topics**
+ [Permissions for a DMS data collector](#fa-data-collectors-permissions)
+ [Creating a data collector for AWS DMS Fleet Advisor](fa-data-collectors-create.md)
+ [Installing and configuring a data collector](fa-data-collectors-install.md)
+ [Discovering OS and database servers to monitor](fa-discovery.md)
+ [Managing monitored objects](fa-managing-objects.md)
+ [Using SSL with AWS DMS Fleet Advisor](fa-using-ssl.md)
+ [Collecting data for AWS DMS Fleet Advisor](fa-collecting.md)
+ [Troubleshooting for DMS data collector](fa-collectors-troubleshooting.md)

## Permissions for a DMS data collector<a name="fa-data-collectors-permissions"></a>

The database users that you create for the DMS data collector should have read permissions\. However, in some cases, the database user requires the `EXECUTE` permission\. For more information, see [Creating database users for AWS DMS Fleet Advisor](fa-database-users.md)\.

The DMS data collector requires additional permissions to run the discovery scripts\.
+ For OS discovery, the DMS data collector needs credentials for the domain server to run requests using the LDAP protocol\.
+ For database discovery in Linux, the DMS data collector needs credentials with `sudo SSH` grants\. Also, you should configure your Linux servers to allow running remote SSH scripts\.
+ For database discovery in Windows, the DMS data collector needs credentials with grants to run Windows Management Instrumentation \(WMI\) and WMI Query Language \(WQL\) queries and read the registry\. Also, you should configure your Windows servers to allow running remote WMI, WQL, and PowerShell scripts\.