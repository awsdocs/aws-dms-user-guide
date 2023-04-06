# Managing monitored objects<a name="fa-managing-objects"></a>

You can select objects to monitor when you run the server discovery process as described in [Discovering OS and database servers](fa-discovery.md)\. Also, you can manually manage objects, such as operating system \(OS\) servers and database servers\. You can perform the following actions to manage monitored objects:
+ Add new objects to monitor
+ Remove existing objects
+ Edit existing objects
+ Export and import a list of objects to monitor
+ Check connections to objects
+ Start data collection

For example, you can manually add an object to monitor\.

**To add an object to monitor manually**

1. On the **Monitored Objects** page, choose **\+Server**\. The **Add monitored object** dialog box opens\.

1. Add information about the server, then choose **Save**\.

You can also use a `.csv` file to import a large list of objects to monitor\. Use the following `.csv` file format to import a list of objects to DMS data collector\.

```
Hostname - Hostname or IP address of Monitored Object
Port - TCP port of Monitored Object  
Engine: (one of the following)         
        • Microsoft SQL Server         
        • Microsoft Windows
        • Oracle Database        
        • Linux         
        • MySQL Server         
        • PostgreSQL 
Connection type: (one of the following)         
        • Login/Password Authentication         
        • Windows Authentication         
        • Key-Based Authentication
Domain name:(Windows authentication)         
        • Use domain name for the account
User name  
Password
```

**To import a \.csv file with a list of objects to monitor**

1. Choose **Import**\. The **Import monitored objects** page opens\.

1. Browse to the `.csv` file that you want to import, then choose **Next**\.

You can view all of the objects and select those that you want to begin collecting metadata from\.

## Associating an OS server with a manually added database<a name="fa-managing-objects-associate"></a>

DMS Fleet Advisor can't collect performance metrics directly from MySQL and PostgreSQL databases\. To collect metrics required for target recommendations, DMS Fleet Advisor uses OS metrics where your databases run\.

When you manually add MySQL and PostgreSQL databases to the list of monitored objects, DMS data collector can't identify the OS servers where these databases run\. Because of this issue, you should associate your MySQL and PostgreSQL databases with OS servers\.

You don't need to manually associate OS servers with databases that DMS Fleet Advisor has automatically discovered\.

**To associate an OS server with your database**

1. In the DMS data collector navigation pane, choose **Monitored objects**\.

1. On the **Monitored objects** page, choose the **Database servers** tab\. A list of database servers appears\.

1. Select the check box next to the MySQL or PostgreSQL database server that you added manually\.

1. Choose **Actions**, then choose **Edit**\. The **Edit database** dialog box opens\.

1. If your DMS data collector has already discovered the OS server where this database runs, then choose **Auto detect**\. DMS data collector runs a SQL script to automatically identify the OS server where your database runs\. Then, DMS data collector associates this OS server with your database\. Skip the next step and save the database configuration that you edited\.

   If the DMS data collector can't automatically identify the OS server for your database, make sure that you use the correct credentials and provide database access permissions\. Optionally, you can add the OS server manually\.

1. To add your OS server manually, choose **\+Add OS server**\. The **Add host OS server** dialog box opens\.

   Add information about your OS server, then choose **Save**\.

1. In the **Edit database** dialog box, choose **Verify connection** to make sure that your DMS data collector can connect to the OS server\.

1. After you verify the connection, choose **Save**\.

If you change the associated OS server for your source database, then DMS Fleet Advisor uses the updated metrics to generate recommendations\. However, the Amazon CloudWatch charts display the old data for your database server\. For more information about CloudWatch charts, see [Recommendation details](fa-recommendations-view.md)\.