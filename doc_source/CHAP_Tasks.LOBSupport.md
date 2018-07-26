# Setting LOB Support for Source Databases in the AWS DMS task<a name="CHAP_Tasks.LOBSupport"></a>

Large objects, \(LOBs\) can sometimes be difficult to migrate between systems\. AWS DMS offers a number of options to help with the tuning of LOB columns\. To see which and when datatypes are considered LOBS by AWS DMS, see the AWS DMS documentation\.

When you migrate data from one database to another, you might take the opportunity to rethink how your LOBs are stored, especially for heterogeneous migrations\. If you want to do so, there’s no need to migrate the LOB data\.

If you decide to include LOBs, you can then decide the other LOB settings:
+ The LOB mode determines how LOBs are handled:
  + **Full LOB mode** \- In **full LOB mode** AWS DMS migrates all LOBs from source to target regardless of size\. In this configuration, AWS DMS has no information about the maximum size of LOBs to expect\. Thus, LOBs are migrated one at a time, piece by piece\. Full LOB mode can be quite slow\.
  + **Limited LOB mode** \- In **limited LOB mode**, you set a maximum size LOB that AWS DMS should accept\. Doing so allows AWS DMS to pre\-allocate memory and load the LOB data in bulk\. LOBs that exceed the maximum LOB size are truncated and a warning is issued to the log file\. In** limited LOB mode** you get significant performance gains over **full LOB mode**\. We recommend that you use **limited LOB mode** whenever possible\.
**Note**  
With Oracle, LOBs are treated as VARCHAR data types whenever possible\. This approach means AWS DMS fetches them from the database in bulk, which is significantly faster than other methods\. The maximum size of a VARCHAR in Oracle is 64K, therefore a limited LOB size of less than 64K is optimal when Oracle is your source database\.
+ When a task is configured to run in **limited LOB mode**, the **Max LOB size \(K\)** option sets the maximum size LOB that AWS DMS accepts\. Any LOBs that are larger than this value will be truncated to this value\.
+ When a task is configured to use **full LOB mode**, AWS DMS retrieves LOBs in pieces\. The **LOB chunk size \(K\)** option determines the size of each piece\. When setting this option, pay particular attention to the maximum packet size allowed by your network configuration\. If the LOB chunk size exceeds your maximum allowed packet size, you might see disconnect errors\.