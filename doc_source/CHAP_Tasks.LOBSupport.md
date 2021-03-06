# Setting LOB support for source databases in an AWS DMS task<a name="CHAP_Tasks.LOBSupport"></a>

Large binary objects \(LOBs\) can sometimes be difficult to migrate between systems\. AWS DMS offers a number of options to help with the tuning of LOB columns\. To see which and when data types are considered LOBs by AWS DMS, see the AWS DMS documentation\.

When you migrate data from one database to another, you might take the opportunity to rethink how your LOBs are stored, especially for heterogeneous migrations\. If you want to do so, there's no need to migrate the LOB data\.

If you decide to include LOBs, you can then decide the other LOB settings:
+ The LOB mode determines how LOBs are handled:
  + **Full LOB mode** – In full LOB mode AWS DMS migrates all LOBs from source to target regardless of size\. In this configuration, AWS DMS has no information about the maximum size of LOBs to expect\. Thus, LOBs are migrated one at a time, piece by piece\. Full LOB mode can be quite slow\.
  + **Limited LOB mode** – In limited LOB mode, you set a maximum size LOB that AWS DMS should accept\. Doing so allows AWS DMS to pre\-allocate memory and load the LOB data in bulk\. LOBs that exceed the maximum LOB size are truncated and a warning is issued to the log file\. In limited LOB mode, you get significant performance gains over full LOB mode\. We recommend that you use limited LOB mode whenever possible\.
  + **Inline LOB mode** – In inline LOB mode, you set the maximum size LOB that AWS DMS will transfer inline\. LOBs smaller than the specified size are transferred inline\. LOBs larger than the specified size are replicated using full LOB mode\. You can select this option when you need to replicate both small and large LOBs, and most of the LOBs are small\.
**Note**  
With Oracle, LOBs are treated as VARCHAR data types whenever possible\. This approach means that AWS DMS fetches them from the database in bulk, which is significantly faster than other methods\. The maximum size of a VARCHAR in Oracle is 64 K\. Therefore, a limited LOB size of less than 64 K is optimal when Oracle is your source database\.
+ When a task is configured to run in limited LOB mode, the **Max LOB size \(K\)** option sets the maximum size LOB that AWS DMS accepts\. Any LOBs that are larger than this value is truncated to this value\.
+ When a task is configured to use full LOB mode, AWS DMS retrieves LOBs in pieces\. The **LOB chunk size \(K\)** option determines the size of each piece\. When setting this option, pay particular attention to the maximum packet size allowed by your network configuration\. If the LOB chunk size exceeds your maximum allowed packet size, you might see disconnect errors\.
+ When a task is configured to run in inline LOB mode, the `InlineLobMaxSize` setting determines which LOBs DMS transfers inline\.

For information on the task settings to specify these options, see [Target metadata task settings](CHAP_Tasks.CustomizingTasks.TaskSettings.TargetMetadata.md)