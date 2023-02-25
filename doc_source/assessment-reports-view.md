# Viewing your database migration assessment report<a name="assessment-reports-view"></a>

After you create an assessment report, DMS Schema Conversion adds information in the following tabs:
+ **Summary**
+ **Action items**

The **Summary** tab shows the number of items that DMS Schema Conversion can automatically convert\.

The **Action items** tab shows items that DMS Schema Conversion can't convert automatically, and provides recommendations about how to manage these items\.

## Assessment report summary<a name="assessment-reports-view-summary"></a>

The **Summary** tab displays the summary information from the database migration assessment report\. It shows the number of items that DMS Schema Conversion can automatically convert for database storage objects and database code objects\.

In most cases, DMS Schema Conversion can't automatically convert all schema items to the target database engine\. The **Summary** tab provides an estimate of the required effort to create schema items in your target DB instance that are equivalent to those in your source\.

To see the conversion summary for database storage objects such as tables, sequences, constraints, data types, and so on, choose **Database storage objects**\. 

To see the conversion summary for database code objects such as procedures, functions, views, triggers, and so on, choose **Database code objects**\.

To change the scope of the assessment report, select the required node in the source database tree\. DMS Schema Conversion updates the assessment report summary to match the selected scope\.

## Assessment report action items<a name="assessment-reports-view-action-items"></a>

The **Action items** tab contains a list of items that DMS Schema Conversion can't automatically convert to a format compatible with the target database engine\. For each action item, DMS Schema Conversion provides the description of the issue and the recommended action\. DMS Schema Conversion groups similar action items and displays the number of occurrences\.

To view the code for the related database object, select an action item in the list\.