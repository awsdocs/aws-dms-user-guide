# Discovering OS and database servers to monitor<a name="CHAP_FleetAdvisor.Discovering"></a>

You can use the DMS data collector to find and list all available servers in your network\. Discovering all the available database servers in you network is recommended, but isn't required\. Optionally, you can manually add or upload the list of servers for further data collection\. For more information about manually adding a list of servers, see [Managing monitored objects](CHAP_FleetAdvisor.ManagingObjects.md)\.

We recommend that you discover all operating system \(OS\) servers before discovering databases on those servers\. To discover OS servers, you need permission to run remote PowerShell, Secure Shell \(SSH\), and Windows Management Instrumentation \(WMI\) scripts and commands, as well as access to the Windows registry\. To discover database servers in your network and collect data from them, you need read\-only administrator permissions for a remote database connection\. Make sure that you added an LDAP server before you proceed with discovery\. For more information, see [Configuring credentials for data forwarding](CHAP_FleetAdvisor.InstallDataCollector.md#CHAP_FleetAdvisor.InstallDataCollector.Configure)\.

To get started with the DMS data collector, complete the following tasks:
+ Discover all OS servers in your network\.
+ Add specific OS servers as objects to monitor\.
+ Verify connections for monitored OS servers\.
+ Discover Microsoft SQL Server, MySQL, Oracle, and PostgreSQL databases running on OS servers\.
+ Add database servers for data collection\.
+ Verify connections to the monitored databases\.

**To discover OS servers in your network that you can monitor**

1. In the DMS data collector navigation pane, choose **Discovery**\. To display the navigation pane, choose the menu icon in the upper\-left corner of the DMS data collector home page\.

   The **Discovery** page opens\.

1. Make sure that the **OS servers** tab is selected, then choose **Run discovery**\. The **Discovery parameters** dialog box appears\. 

1. Enter the LDAP servers that you want to use to scan your network\.

1. Choose **Run discovery**\. The page populates with a list of all OS servers discovered within your network, regardless of whether they're running a database\.

   We recommend that you run discovery for all OS servers before running discovery for databases on those servers\. Your credentials make discovery possible first for the host servers, then for the databases that reside on them\. You want to discover OS servers first before running discovery for databases on those servers\. Be aware that the credentials that you use for an LDAP server to find OS servers in your network might differ from the credentials required to discover databases on a particular OS server\. Therefore, we recommend that you add OS servers to monitored objects, verify the credentials and correct them if necessary, and then check connectivity before proceeding\.

In the list of discovered OS servers in your network, you can now select the servers that you want to add to monitored objects\.

**To select OS servers as objects to monitor**

1. On the **Discovery** page, choose the **OS servers** tab\.

1. In the list of discovered OS servers shown, select the check box next to each server that you want to monitor\.

1. Choose **Add to monitored objects**\.

You can view the list of OS servers to monitor and verify connections on the **Monitor objects** page\.

**To verify connections of selected OS servers to monitor**

1. In the DMS data collector navigation pane, choose **Monitored objects**\. 

1. On the **Monitored objects** page, choose the **All** tab\. A list of discovered OS servers to be monitored appears\.

1. Select the check box at the top of the column to choose all the listed OS servers\.

1. Choose **Verify connection**\. For each server object, view the results in the **Connections status** column\.

1. Select servers with a connection status other than **Success**, and choose **Edit**\. The **Edit monitored objects** dialog box opens\.

1. Correct any improper information, then choose **Save**\. The **Override credentials** dialog box opens\.

1. Choose **Overwrite**\. DMS data collector verifies and updates the status for each connection as **Success**\.

You can now discover databases that reside on servers that you selected to monitor\.

**Discover databases running on servers**

1. In the DMS data collector navigation pane, choose **Discovery**\. 

1. Choose the **Database servers** tab, and choose **Run discovery**\. The **Discovery parameter** dialog box opens\.

1. In the **Discovery parameter** dialog box, for **Discovered by**, choose **Monitored objects**\. For **Servers**, choose the OS servers on which you want to run database discovery\.

1. Choose **Run discovery**\. The page becomes populated with a list of all databases that reside on the OS servers that you choose to monitor\.

View information such as database address, server name, and database engine to help you select databases to monitor\.

**To select databases to monitor**

1. On the **Discovery** page, choose the **Database servers** tab\.

1. In the list of discovered databases shown, select the check box next to all the databases you want to monitor\.

1. Choose **Add to monitored objects**\.

You can now verify the connections to the databases you choose to monitor\.

**Verify connections to monitored databases**

1. In the DMS data collector navigation pane, choose **Monitored objects**\. 

1. On the **Monitored objects** page, choose the **Database servers** tab\. A list of discovered database servers you choose to monitor appears\.

1. Select the check box at the top of the column to choose all the listed database servers\.

1. Choose **Verify connection**\. For each database, view the results in the **Connections status** column\.

1. Select all connections that are undefined \(blank\) or resulted in **Failure**, then choose **Edit**\. The **Edit monitored objects** dialog box opens\.

1. Enter your **Login** and **Password** credentials, then choose **Save**\. The **Change credentials** dialog box opens\.

1. Choose **Overwrite**\. DMS data collector verifies and updates the status for each connection as **Success**\.

After discovering OS servers and databases to monitor, you can also perform actions to manage monitored objects\.