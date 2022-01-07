# Discovering OS and database servers to monitor in AWS DMS Studio<a name="CHAP_DMSStudio.Discovering"></a>


|  | 
| --- |
| AWS DMS Studio is in preview release for AWS Database Migration Service and is subject to change\. | 

You can use your on\-premises DMS Collector to find and list all available servers in your network that you can monitor\. We recommend that you discover all operating system \(OS\) servers before discovering databases on those servers\. To discover OS servers, the user needs permission to execute remote PowerShell, Secure Shell \(SSH\), and Windows Management Instrumentation \(WMI\) scripts and commands and access the Windows registry\. To discover database servers in your network and collect data from them, the user needs read\-only administrator permissions for remote database connection\. 

To get started with the AWS DMS Fleet Advisor, complete the following tasks:
+ Discover all OS servers in your network that you can monitor\.
+ Select specific OS servers as objects to monitor\.
+ Verify connections for monitored OS servers\.
+ Discover Microsoft SQL Server, MySQL, Oracle, and PostgreSQL databases running on OS servers\.
+ Select databases to monitor\.
+ Verify connections to the monitored databases\.

**To discover OS servers in your network that you can monitor**

1. From the DMS Collector navigation pane, choose **Discovery**\. \(To display the navigation pane, choose the menu icon at upper left hand corner of the DMS Collector Home page\.\) 

   The **Discovery** page opens\.

1. Make sure that the **OS servers **tab is chosen, then choose **Run discovery**\. The **Discovery parameters** dialog box appears\. 

1. Enter the LDAP servers that you want to use to scan your network\.

1. Choose **Run discovery**\. The page becomes populated with a list of all OS servers discovered within your network, regardless of whether they're running a database\.

   It's important to run discovery for all OS servers before running discovery for databases on those servers\. Your credentials make discovery possible for host servers first, then discovery of databases that reside on them follows\.

From the list of discovered OS servers in your network, you can now select the servers that you want to monitor\.

**To select OS servers as objects to monitor**

1. On the **Discovery** page, choose the **OS servers** tab\.

1. From the list of discovered OS servers shown, choose all the servers that you intend to monitor by selecting the check box next to each server\.

1. Choose** Add to monitored objects**\.

You can view the list of OS servers to monitor from the **Monitor objects** page\. From there, you can verify connections\.

**To verify connections of selected OS servers to monitor**

1. From the DMS Collector navigation pane, choose **Monitored objects**\. 

1. From the **Monitored objects** page, choose the **All** tab\. A list of discovered OS servers to be monitored appears\.

1. Select all the OS servers listed by selecting the box at the top of the column\.

1. Choose **Verify connection**\. For each server object, results appear in the **Connections status** column\.

1. Select all connections that are undefined \(blank\) or resulted in **Failure**, and choose **Edit**\. The **Edit monitored objects** dialog box opens\.

1. Enter your credentials for **Login** and **Password**, and choose **Save**\. The **Override credentials** dialog box opens\.

1. Choose **Overwrite**\. DMS Collector verifies and updates the status for each connection as **Success**\.

You can now discover databases that reside on servers that you selected to monitor\.

**Discover databases running on servers**

1. From the DMS Collector navigation pane, choose **Discovery**\. 

1. Choose the **Database servers** tab, and choose **Run discovery**\. The **Discovery parameter** dialog opens\.

1. In the **Discovery parameter** dialog, for **Discovered by**, choose **Monitored objects**\. For **Servers**, choose **Select all**\.

1. Choose **Run discovery**\. The page becomes populated with a list of all databases that reside on the OS servers that you chose to monitor\.

View information such as database address, server name, and database engine to help you select databases to monitor\.

**To select databases to monitor**

1. From the **Discovery** page, choose the **Database servers** tab\.

1. From the list of discovered databases shown, choose all the databases you intend to monitor by selecting the check box next to the database\.

1. Choose ** Add to monitored objects**\.

You can now verify the connections to the databases you chose to monitor\.

**Verify connections to monitored databases**

1. From the DMS Collector navigation pane, choose **Monitored objects**\. 

1. From the **Monitored objects** page, choose the **Database servers** tab\. A list of discovered database servers you chose to monitor is shown\.

1. Select all the database servers listed by checking the box at the top of the check box column\.

1. Choose **Verify connection**\. Results are shown in the **Connections status** column for each database\.

1. Select all connections that are undefined \(blank\) or resulted in **Failure**, and choose **Edit**\. The **Edit monitored objects** dialog opens\.

1. Enter your **Login** and **Password** credentials\. And choose **Save**\. The **Change credentials** dialog opens\.

1. Choose **Overwrite**\. DMS Collector verifies and updates the status for each connection as **Success**\.

After discovering OS servers and databases to monitor, you can also perform actions to manage monitored objects\.