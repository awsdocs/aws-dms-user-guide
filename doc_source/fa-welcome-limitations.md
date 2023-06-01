# DMS Fleet Advisor Target Recommendation limitations<a name="fa-welcome-limitations"></a>

Limitations when using the DMS Fleet Advisor Target Recommendation feature include the following\.
+ DMS Fleet Advisor generates one\-to\-one recommendations\. For each source database, DMS Fleet Advisor determines a single target engine\. DMS Fleet Advisor doesn't handle multitenant servers and doesn't provide recommendations for running several databases on a single target DB instance\.
+ DMS Fleet Advisor doesn't provide recommendations about available database version upgrades\.
+ DMS Fleet Advisor generates recommendations for up to 100 databases at one time\.
+ You can install a data collector only in Windows\. Make sure that you also install \.NET Framework 4\.8 and PowerShell 6\.0 and higher\. For the hardware requirements, see [Installing a data collector](fa-data-collectors-install.md)\.
+ The DMS data collector requires permissions to run requests using LDAP protocol on your domain server\.
+ The DMS data collector requires the sudo SSH script running in Linux\.
+ The DMS data collector requires permissions to run remote PowerShell, Windows Management Instrumentation \(WMI\), WMI Query Language \(WQL\), and registry scripts in Windows\.
+ For MySQL and PostgreSQL, DMS Fleet Advisor can't collect performance metrics from your database\. Instead, DMS Fleet Advisor collects the OS server metrics\. Therefore, you can't generate recommendations based on utilization metrics for MySQL and PostgreSQL databases that run on Amazon RDS and Aurora\.