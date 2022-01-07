# Step 2: Download and install the AWS Schema Conversion Tool \(AWS SCT\)<a name="CHAP_LargeDBs.SBS.install-sct"></a>

You need two local machines to run this process, in addition to the Edge device\. 

Download the AWS Schema Conversion Tool app and install it on a local machine that can access AWS\. For instructions, which include information on compatible operating systems, see [Installing and updating the AWS Schema Conversion Tool](https://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_SchemaConversionTool.Installing.html)\.

On a different machine, where you plan to install the AWS DMS Agent, download and install the Snowball Edge client from [AWS Snowball Edge resources](https://aws.amazon.com/snowball-edge/resources/)\.

After you finish this step, you should have two machines: 
+ Machine \#1 \(SCT\), with AWS SCT installed
+ Machine \#2 \(Connectivity\), with the Edge client, where you plan to install the AWS DMS Agent and the ODBC drivers for the databases you are migrating