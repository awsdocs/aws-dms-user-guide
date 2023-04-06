# Data collection issues related to Windows webpage composer<a name="fa-collectors-troubleshooting-wpc"></a>

**WPC: The network path was not found**  
Turn on the inbound firewall rule "File and Printer Sharing \(SMB–In\)"\. For example:  
`* Inbound TCP/IP at local port 445`\.  
Also, start the Remote Registry service and set its start\-up type to Automatic\.

**WPC: Access is denied**  
Add the DMS data collector user to the Performance Monitor Users or Administrators group\.

**WPC: Category does not exist**  
Run `loader /r` to rebuild the performance counter cache, then restart your computer\.

**Note**  
For information about troubleshooting issues when migrating data using AWS Database Migration Service \(AWS DMS\), see [Troubleshooting and diagnostic support](https://docs.aws.amazon.com/dms/latest/userguide/CHAP_Troubleshooting)\.  