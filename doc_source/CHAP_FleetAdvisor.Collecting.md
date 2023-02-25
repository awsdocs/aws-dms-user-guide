# Collecting data for AWS DMS Fleet Advisor<a name="CHAP_FleetAdvisor.Collecting"></a>

You can start collecting data by choosing **Start collection** on the DMS data collector home page, or on the **Monitored objects** or **Data collection** page\. DMS data collector can use up to four parallel threads to connect to a single database instance\.

**Important**  
Before starting to collect data, view the **Software check** section on the DMS data collector home page\. Verify that all database engines that you want to monitor have **Passed**\. If some have **Failed**, and you have database servers with corresponding engines in your monitored objects list, fix the issue before proceeding\. Helpful tips can be found next to the **Failed** state listed in the **Software check** section\.

After data collection begins, you're redirected to the **Data collection** page, where collection queries are run and live progress is shown\. Here, you can view overall collection health or on the DMS data collector home page\. If overall data collection health is less than 100 percent, you might need to fix issues related to the collection\.

On the **Data collection** page, you can see the collection status for each object\. If something isn't working properly, a message appears showing how many issues occurred\. To help determine a fix to an issue, you can check details\. The following tabs list potential issues: 
+ **Summary by query** – Shows status of tests like the Ping test\. You can filter results in the **Status** column\. The **Status** column provides a message indicating how many failures occurred during data collection\.
+ **Summary by a monitored object** – Shows overall status per object\.
+ **Summary by query type** – Shows status for type of collector query, such as SQL, Secure Shell \(SSH\), or Windows Management Instrumentation \(WMI\) calls\.
+ **Summary by issue** – Shows all unique issues that occurred, with issue names and the number of times that each issue occurs\.

![\[Data collection page\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-dmsstudio-summary2.png)

To export the collection results, choose **Export to CSV**\.

After identifying issues and resolving them, choose **Start collection** and rerun the data collection process\. After performing data collection, the data collector uses secure connections to upload collected data to a DMS Fleet Advisor inventory\. DMS Fleet Advisor stores information in your Amazon S3 bucket\. For information about configuring credentials for data forwarding, see [Configuring credentials for data forwarding](CHAP_FleetAdvisor.InstallDataCollector.md#CHAP_FleetAdvisor.InstallDataCollector.Configure)\.