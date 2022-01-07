# Best practices for the AWS SCT<a name="CHAP_BestPractices"></a>

Following, you can find information on best practices and options for using the AWS Schema Conversion Tool\. 

## General memory management and performance options<a name="CHAP_BestPractices.Memory"></a>

You can configure the AWS Schema Conversion Tool with different memory performance settings\. Increasing memory speeds up the performance of your conversion but uses more memory resources on your desktop\. 

To set your memory management option, choose **Global Settings** from the **Settings** menu, and choose the **Performance and Memory** tab\. Choose one of the following options: 
+ **Fast conversion, but large memory consumption** – This option optimizes for speed of the conversion, but might require more memory for the object reference cache\. 
+ **Low memory consumption, but slower conversion** – This option minimizes the amount of memory used, but results in a slower conversion\. Use this option if your desktop has a limited amount of memory\. 
+ **Balance speed with memory consumption** – This option optimizes provides a balance between memory use and conversion speed\. 

## Configuring additional memory<a name="CHAP_BestPractices.JVM"></a>

For converting large database schemas, for example a database with 3,500 stored procedures, you can configure the amount of memory available to the AWS Schema Conversion Tool\. 

**To modify the amount of memory AWS SCT consumes**

1. Locate the folder where the configuration file is \(`C:\Program Files\AWS Schema Conversion Tool\App`\)\. 

1. Open the configuration file `AWS Schema Conversion Tool.cfg` with Notepad or your favorite text editor\. 

1. Edit the `JavaOptions` section to set the minimum and maximum memory available\. The following example sets the minimum to 4 GB and the maximum to 40 GB, 

   ```
   1. [JavaOptions]
   2. -Xmx48960M
   3. -Xms4096M
   ```

## Increasing logging information<a name="CHAP_BestPractices.Logging"></a>

In addition to managing the memory settings of AWS SCT, you can increase the logging information produced by AWS SCT when converting new projects\. Although increasing logging information might slow conversion slightly, the changes can help you provide robust information to AWS Support if errors arise\.

**To change logging settings**

1. On the **Settings** menu, choose **Global Settings**\.  

1. In the **Global settings** window, choose **Logging**\.  

1. For **Debug mode**, choose **True**\. 

1. Choose key items to increase the logging information\.

   For example, to help with key problem areas during conversion, set **Parser**, **Type Mapping**, and **User Interface** to **TRACE**\.

You can also configure the log location\. If information becomes too verbose for the file system where logs are streaming, change to a location with sufficient space to capture logs\.

To transmit logs to AWS Support, go to the directory where the logs are stored, and compress up all the files into a manageable single \.zip file\. Then upload the \.zip file with your support case\. When the initial analysis is completed and ongoing development resumes, return **Debug mode** to **false** to eliminate the verbose logging\. Then increase conversion speed\.

**Tip**  
To manage the log size and streamline reporting issues, remove the logs or move them to another location after a successful conversion\. Doing this ensures that only the relevant errors and information are transmitted to AWS Support and keeps the log file system from filling\.