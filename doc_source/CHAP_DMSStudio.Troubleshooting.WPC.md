# Data collection issues related to Windows web page composer<a name="CHAP_DMSStudio.Troubleshooting.WPC"></a>


|  | 
| --- |
| AWS DMS Studio is in preview release for AWS Database Migration Service and is subject to change\. | 

**WPC: The network path was not found**  
Turn on the inbound firewall rule "File and Printer Sharing \(SMB–In\)"\. For example:  
`* Inbound TCP/IP at local port 445`\.  
Also, start the Remote Registry service and set its start\-up type to Automatic\.

**WPC: Access is denied**  
Add the *DMS Fleet Advisor Collector* user to the Performance Monitor Users or Administrators group\.

**WPC: Category does not exist**  
Run `loader /r` to rebuild the performance counter cache, then restart your computer\.