# Data collection issues related to Windows web page composer<a name="CHAP_DMSStudio.Troubleshooting.WPC"></a>

**WPC: The network path was not found**  
Turn on the inbound firewall rule "File and Printer Sharing \(SMB–In\)"\. For example:  
`* Inbound TCP/IP at local port 445`\.  
Also, start the Remote Registry service and set its start\-up type to Automatic\.

**WPC: Access is denied**  
Add the *DMS Fleet Advisor Collector* user to the Performance Monitor Users or Administrators group\.

**WPC: Category does not exist**  
Run `loader /r` to rebuild the performance counter cache, then restart your computer\.

**Note**  
For information about troubleshooting issues when migrating data using AWS Database Migration Service \(AWS DMS\), see [Troubleshooting migration tasks in AWS Database Migration Service](CHAP_Troubleshooting.md) 