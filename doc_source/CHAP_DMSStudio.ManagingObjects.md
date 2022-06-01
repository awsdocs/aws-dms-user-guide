# Managing monitored objects for DMS Studio<a name="CHAP_DMSStudio.ManagingObjects"></a>


|  | 
| --- |
| AWS DMS Studio is in preview release for AWS Database Migration Service and is subject to change\. | 

You can select objects to monitor when you run the server discovery process as described in [Discovering OS and database servers to monitor in AWS DMS Studio](CHAP_DMSStudio.Discovering.md)\. Also, you can manually manage objects such as operating\-system \(OS\) servers and database servers\. You can take these actions to manage monitored objects:
+ Add new objects to monitor\.
+ Remove existing objects\.
+ Edit existing objects\.
+ Export and import a list of objects to monitor\.
+ Check connections to objects\.
+ Start data collection\.

For example, you can manually add an object to monitor\.

**To add an object to monitor manually**

1. From the **Monitored Objects** page, choose **\+Server**\. The **Add monitored object screen** opens\. 

1. Add information about the server, and choose **Save**\. 

You can also use a `.csv` file to import a large list of objects to monitor\. Use the following `.csv` file format to import a list of objects to DMS Collector\.

```
Hostname - Hostname or IP address of Monitored Object
Port - TCP port of Monitored Object  
Host type: (one of the following)         
        • Microsoft SQL Server         
        • Microsoft Windows         
        • Linux         
        • MySQL Server         
        • PostgreSQL 
Connection type: (one of the following)         
        • Login/Password Authentication         
        • Windows Authentication         
        • Key-Based Authentication
Domain name:(Windows authentication)         
        • Use domain name for the account
Account Name  
Password
```

**To import a \.csv file with a list of objects to monitor**

1. Choose **Import**\. The **Import monitored objects** screen opens\.

1. Browse to the `.csv` file that you want to import, and chose **Next**\.

You can view all of the objects and choose those that you want to begin collecting data from\.