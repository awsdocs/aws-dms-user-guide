# Step 4: Analyze discovery data<a name="CHAP_DMSStudio_GettingStarted_Analyze"></a>

Next, you analyze your local database information, and get recommendations about how to migrate your data to AWS\.

**To analyze your local database information and get migration recommendations**

1. In the AWS DMS console, choose **Inventory** in the navigation pane\. 

1. On the **Inventory** page, choose the **Databases** tab\. Choose **Analyze inventories**\.

1. After analysis completes, choose the **Schemas** tab to see information and recommendations\.   
![\[Screenshot showing the database and schema information\]](http://docs.aws.amazon.com/dms/latest/userguide/images/dmsstudio_10.png)

   You use the analysis information to determine which databases should be migrated to AWS\. To determine good candidates for migration, check the following: 
   + The **Complexity** result shows how many lines of code are in the database\. Databases with a **Complexity** result of **Complex** are databases with a large code base and are likely candidates for migration\. This is because these databases are probably essential to your infrastructure\.
   + Schemas with a high **Similarity** score are usually duplicates of other schemas, such as duplicate databases used in staging\. Databases with a high **Similarity** score aren't good candidates for migration\. This is because these databases are probably redundant copies of your main databases\.