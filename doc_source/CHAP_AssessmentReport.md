# Creating migration assessment reports with AWS SCT<a name="CHAP_AssessmentReport"></a>

An important part of the AWS Schema Conversion Tool is the report that it generates to help you convert your schema\. This *database migration assessment report* summarizes all of the schema conversion tasks and details the action items for schema that can't be converted to the DB engine of your target DB instance\. You can view the report in the application or export it as a comma\-separated value \(CSV\) or PDF file\.

If you add multiple source and target databases in a single project, AWS SCT aggregates the reports for all conversion pairs into one database migration assessment report\.

You can use virtual target database platforms to generate an assessment report and understand the complexity of migration to a selected database platform\. In this case, you don't need to connect to your target database platform\. For example, you can use Babelfish for Aurora PostgreSQL as a virtual target database platform to create a database migration assessment report\. For more information on virtual target database platforms, see [Using virtual targets](CHAP_Mapping.VirtualTargets.md)\.

The migration assessment report includes the following:
+ Executive summary
+ License evaluation
+ Cloud support, indicating any features in the source database not available on the target\.
+ Recommendations, including conversion of server objects, backup suggestions, and linked server changes

The report also includes estimates of the amount of effort that it will take to write the equivalent code for your target DB instance that can't be converted automatically\. 

If you use AWS SCT to migrate your existing schema to an Amazon RDS DB instance, the report can help you analyze requirements for moving to the AWS Cloud and for changing your license type\. 

**Topics**
+ [Creating a database migration assessment report](CHAP_AssessmentReport.Create.md)
+ [Viewing the assessment report](CHAP_AssessmentReport.View.md)
+ [Saving the assessment report](CHAP_AssessmentReport.Save.md)
+ [Using the multiserver assessor to create an aggregate report](CHAP_AssessmentReport.Multiserver.md)