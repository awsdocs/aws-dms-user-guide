# Managing monitored objects<a name="CHAP_FleetAdvisor.ManagingObjects"></a>

You can select objects to monitor when you run the server discovery process as described in [Discovering OS and database servers](CHAP_FleetAdvisor.Discovering.md)\. Also, you can manually manage objects such as operating system \(OS\) servers and database servers\. You can perform the following actions to manage monitored objects:
+ Add new objects to monitor
+ Remove existing objects
+ Edit existing objects
+ Export and import a list of objects to monitor
+ Check connections to objects
+ Start data collection

For example, you can manually add an object to monitor\.

**To add an object to monitor manually**

1. On the **Monitored Objects** page, choose **\+Server**\. The **Add monitored object screen** opens\.

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

You can view all of the objects and select those that you want to begin collecting data from\.