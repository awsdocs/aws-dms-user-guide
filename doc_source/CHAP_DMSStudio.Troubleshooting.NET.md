# Data collection issues related to network and server connections<a name="CHAP_DMSStudio.Troubleshooting.NET"></a>


|  | 
| --- |
| AWS DMS Studio is in preview release for AWS Database Migration Service and is subject to change\. | 

**NET: An exception occurred during a ping request\.**  
Check the name of the computer to see if it's in a state where it can't be resolved to an IP address\.  
For example, check if the computer is switched off, disconnected from the network, or decommissioned\.

**NET: Timed Out**  
Turn on the inbound firewall rule "File and Printer Sharing \(Echo Request \- ICMPv4\-In\)"\. For example:  
`* Inbound ICMPv4`

**NET: DestinationHostUnreachable**  
Check the IP address of the computer\. In particular, check if it's on the same subnet as the computer running DMS Fleet Advisor Collector and whether it responds to Address Resolution Protocol \(ARP\) requests\.   
If the computer is on a different subnet, then the IP address of the gateway can't be resolved to the media access control \(MAC\) address\.  
Also, check if the computer is switched off, disconnected from the network, or decommissioned\.