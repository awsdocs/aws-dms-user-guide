# Step 2: Run discovery<a name="CHAP_DMSStudio_GettingStarted_Run"></a>


|  | 
| --- |
| AWS DMS Studio is in preview release for AWS Database Migration Service and is subject to change\. | 

Next, you run the discovery process for your local database servers, databases, and schemas\.

If you specified your local database manually rather than configuring a domain controller, skip to [Step 3: Collect and verify data](CHAP_DMSStudio_GettingStarted_Collect.md)\.

## Run discovery on OS servers<a name="CHAP_DMSStudio_GettingStarted_Run_OSServers"></a>

**To run discovery to find local OS servers**

1. From the navigation pane, choose **Discovery**\. On the **OS Servers** tab, choose **Run discovery**\.

1. In the **Discovery parameters** dialog, verify that the **LDAP servers** field is correct, and chooose **Run discovery**\.

1. When the discovery process is complete, the page shows your local computer, and any other servers on your local network\. Choose your local computer, and then choose **Add to monitored objects**\. 

1. From the navigation pane, choose **Monitored objects**\. Choose **Verify connection**\. Verify that the connection to your local computer is valid\. If the connection is not valid, choose **Edit**, and update the connection credentials\.

## Run discovery on database servers<a name="CHAP_DMSStudio_GettingStarted_Run_DBServers"></a>

1. From the navigation pane, choose **Discovery**\.

1. Choose the **Database servers** tab\. Choose **Run discovery**\. The **Discovery parameters** dialog appears\.  
![\[Screenshot showing the local server\]](http://docs.aws.amazon.com/dms/latest/userguide/images/dmsstudio_061.png)

1. You can run database discovery with the following options:
   + **LDAP servers**: Runs database discovery on your entire network using your domain credentials\. Servers not added to the domain won't be discovered\.
   + **LDAP servers, Select from discovered OS servers**: Runs database discovery on one or more discovered OS servers\. 
   + **Monitored objects**: Runs database discovery on monitored objects\. You can specify credentials for individual OS servers using this option\.

1. Choose **Save**\. Confirm the action\.

1. Choose your local server from the list of monitored objects, and choose **Run discovery**\.

When discovery is complete, DMS Collector displays the **localhost:3306** database\.

## Run discovery on databases<a name="CHAP_DMSStudio_GettingStarted_Run_DB"></a>

1. Choose all of the discovered objects, and then choose **Add to monitored objects** again\.

1. Choose all of the discovered objects, and then choose **Verify connection**\. 

   DMS Collector displays the successful connection\. If a connection is not valid, choose **Edit**, and update the connection credentials\.

All server and database objects and credentials are now configured\. In the next step, you collect information about your data environment\.