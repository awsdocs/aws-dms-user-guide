# Saving the assessment report<a name="CHAP_AssessmentReport.Save"></a>

After you [create a database migration assessment report](CHAP_AssessmentReport.Create.md), you can save a local copy of the database migration assessment report as either a PDF file or a comma\-separated value \(CSV\) file\. 

**To save a database migration assessment report as a PDF file**

1. In the top menu, choose **View**, and then choose **Assessment Report view**\. 

1. Choose the **Summary** tab\. 

1. Choose **Save to PDF** at upper right\. 

**To save a database migration assessment report as a CSV file**

1. In the top menu, choose **View**, and then choose **Assessment Report view**\. 

1. Choose the **Summary** tab\. 

1. Choose **Save to CSV** at upper right\. 

 The PDF file contains both the summary and action item information, as shown in the following example\. 

![\[Database migration assessment report in the PDF file\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/images/assessment_report.png)

 When you choose the **Save to CSV** option, the AWS SCT creates three CSV files\. 

 The first CSV file contains the following information about action items:
+  Category 
+  Occurrence – The file name, line number, and position for the item
+  Action item number 
+  Subject 
+  Group 
+  Description 
+  Documentation references 
+  Recommended action 
+  Estimated complexity 

 The second CSV file includes the `Action_Items_Summary` suffix in its name and contains the information about the number of occurrences of all action items\. 

In the following example, values in the **Learning curve effort** column indicate the amount of effort needed to design an approach to converting each action item\. Values in the **Effort to convert an occurrence of the action item** column indicate the effort needed to convert each action item, following the designed approach\. The values used to indicate the level of effort needed are based on a weighted scale, ranging from low \(least\) to high \(most\)\. 

![\[Action item assessment report\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/images/action-item-cvs.png)

 The third CSV file includes `Summary` in its name and contains the following summary:
+  Category 
+  Number of objects 
+  Objects automatically converted 
+  Objects with simple actions 
+  Objects with medium\-complexity actions 
+  Objects with complex actions 
+  Total lines of code 