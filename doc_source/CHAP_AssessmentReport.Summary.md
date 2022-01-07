# Assessment report summary<a name="CHAP_AssessmentReport.Summary"></a>

The **Summary** tab displays the summary information from the database migration assessment report\. It shows items that were converted automatically, and items that were not converted automatically\.

![\[Assessment report summary\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/images/summary_tab.png)

For schema items that can't be converted automatically to the target database engine, the summary includes an estimate of the effort required to create schema items in your target DB instance that are equivalent to those in your source\. 

The report categorizes the estimated time to convert these schema items as follows: 
+ **Simple** – Actions that can be completed in less than two hours\. 
+ **Medium** – Actions that are more complex and can be completed in two to six hours\. 
+ **Significant** – Actions that are very complex and take more than six hours to complete\. 

The section **License Evaluation and Cloud Support** contains information about moving your existing on\-premises database schema to an Amazon RDS DB instance running the same engine\. For example, if you want to change license types, this section of the report tells you which features from your current database should be removed\. 

![\[License evaluation and cloud support section\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/images/license-evaluation-and-cloud-support.png)