# Creating database migration assessment reports with DMS Schema Conversion<a name="assessment-reports"></a>

An important part of DMS Schema Conversion is the report that it generates to help you convert your schema\. This *database migration assessment report* summarizes all of the schema conversion tasks\. It also details the action items for schema that can't be converted to the DB engine of your target DB instance\. You can view the report in the AWS DMS console or save a copy of this report as a PDF or comma\-separated value \(CSV\) files\.

The migration assessment report includes the following:
+ An executive summary
+ Recommendations, including conversion of server objects, backup suggestions, and linked server changes

 When you have items that DMS Schema Conversion can't converted automatically, the report provides estimates of how much effort is required to write the equivalent code for your target DB instance\.

**Topics**
+ [Creating a database migration assessment report](assessment-reports.create.md)
+ [Viewing your database migration assessment report](assessment-reports-view.md)
+ [Saving your database migration assessment report](assessment-reports-save.md)