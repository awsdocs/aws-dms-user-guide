# Step 4: Create an assessment report<a name="getting-started-assessment"></a>

To assess the complexity of the migration, create the database migration assessment report\. This report includes the list of all database objects that DMS Schema Conversion can't convert automatically\.

**To create an assessment report**

1. Choose **Migration projects**, and then choose **sc\-project**\.

1. Choose **Schema conversion**, and then choose **Launch schema conversion**\.

1. In the source database pane, choose the database schema to assess\. Also, select the check box for the name of this schema\.

1. In the source database pane, choose **Assess** in the **Actions** menu\. The **Assess** dialog box appears\. 

1. Choose **Assess** in the dialog box to confirm your choice\.

   The **Summary** tab shows the number of items that DMS Schema Conversion can automatically convert for database storage objects and database code objects\.

1. Choose **Action items** to see the list of all database objects that DMS Schema Conversion can't convert automatically\. Review the recommended actions for each item\.

1. To save a copy of your assessment report, choose **Export results**\. Next, choose one of the following formats: **CSV** or **PDF**\. The **Export** dialog box appears\.

1. Choose **Export** to confirm your choice\.

1. Choose **S3 bucket**\. The Amazon S3 console opens\.

1. Choose **Download** to save your assessment report\.