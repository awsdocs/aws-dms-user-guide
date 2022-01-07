# Collecting data for AWS DMS Studio Fleet Advisor<a name="CHAP_DMSStudio.Collecting"></a>


|  | 
| --- |
| AWS DMS Studio is in preview release for AWS Database Migration Service and is subject to change\. | 

You can start collecting data by choosing **Start collection** from the DMS Collector home page, or from the **Monitored objects** or **Data collection** page\. DMS Collector can use up to four parallel threads to connect to a single database instance\.

After data collection begins, you're redirected to the **Data collection** page, where collection queries are run and live progress is shown\. You can view overall collection health there or on the Data Collector home page\. If overall data collection health is less than 100 percent, you might need to fix issues related to the collection\.

You can review AWS DMS Collector application logs\. Find the PowerShell tasks logs in the `C:\ProgramData\Amazon\AWS DMS Collector\log` folder\. Find the Windows Service logs in the `C:\ProgramData\Amazon\AWS DMS Collector\DMSCollector.Logs` folder\. Because these folders have a `Hidden` attribute, you need to choose **Show hidden items** in Windows Explorer or use a direct link\.

On the **Data collection** page, you can see the collection status for each object\. If something isn't working properly, a message appears showing how many issues occurred\. To help determine a fix to an issue, you can check details\. The following tabs list potential issues: 
+ **Summary by query** – Shows status of tests like the Ping test\. You can filter results in the **Status** column\. The **Status** column provides a message indicating how many failures occurred during data collection\. 
+ **Summary by a monitored object** – Shows overall status per object\.
+ **Summary by query type** – Shows status for type of test, such as SQL, Secure Shell \(SSH\), or Windows Management Instrumentation \(WMI\) calls\.
+ **Summary by issue** – Shows all unique issues that occurred, with issue names and the number of times that each issue occurs\.

![\[Data collection page\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-dmsstudio-summary2.png)

To export the collection results, choose **Export to CSV**\.

After identifying issues and resolving them, choose **Start collection** and rerun the data collection process\. After performing data collection, the DMS Collector uses secure connections to upload collected data to a AWS DMS Studio Fleet Advisor inventory\. Fleet Advisor stores information in your Amazon S3 bucket\.