# Saving your database migration assessment report<a name="assessment-reports-save"></a>

After you create a database migration assessment report, you can save a copy of this report as a PDF or comma\-separated value \(CSV\) files\.

**To save a database migration assessment report as a PDF file**

1. Choose **Export**, then choose **PDF**\. Review the dialog box, and choose **Export to PDF**\. 

1. DMS Schema Conversion creates an archive with your PDF file and stores this archive in your Amazon S3 bucket\. To change the Amazon S3 bucket, edit the schema conversion settings in your instance profile\.

1. Open the assessment report file in your Amazon S3 bucket\.

**To save a database migration assessment report as CSV files**

1. Choose **Export**, then choose **CSV**\. Review the dialog box, and choose **Export to CSV**\. 

1. DMS Schema Conversion creates an archive with CSV files and stores this archive in your Amazon S3 bucket\. To change the Amazon S3 bucket, edit the schema conversion settings in your instance profile\.

1. Open the assessment report files in your Amazon S3 bucket\.

The PDF file contains both the summary and action item information\.

When you export your assessment report to CSV, DMS Schema Conversion creates three CSV files\. 

 The first CSV file contains the following information about action items:
+ Category
+ Occurrence
+ Action item
+ Subject
+ Group
+ Description
+ Documentation references
+ Recommended action
+ Line
+ Position
+ Source
+ Target
+ Server IP address and port
+ Database
+ Schema

 The second CSV file includes the `Action_Items_Summary` suffix in its name and contains the following information:
+ Schema
+ Action item
+ Number of occurrences
+ Learning curve efforts, which is the amount of effort required to design an approach to converting each action item
+ Efforts to convert an occurrence of the action item, which shows the effort required to convert each action item, following the designed approach
+ Action item description
+ Recommended action

The values that indicate the level of required efforts are based on a weighted scale, ranging from low \(least\) to high \(most\)\.

 The third CSV file includes `Summary` in its name and contains the following information:
+ Category
+ Number of objects
+ Objects automatically converted
+ Objects with simple actions
+ Objects with medium\-complexity actions
+ Objects with complex actions
+ Total lines of code