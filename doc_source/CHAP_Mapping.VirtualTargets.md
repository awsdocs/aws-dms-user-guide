# Using virtual targets<a name="CHAP_Mapping.VirtualTargets"></a>

You can see how AWS SCT converts your source database schema to any supported target database platform\. To do so, you don't need to connect to an existing target database\. Instead, you can use a virtual target database platform in a mapping rule\. 

 AWS SCT supports the following virtual target database platforms: 
+ Amazon Redshift and AWS Glue
+ Aurora MySQL
+ Aurora PostgreSQL
+ Babelfish for Aurora PostgreSQL
+ MariaDB
+ Microsoft SQL Server
+ MySQL
+ Oracle
+ PostgreSQL

 If you use Babelfish for Aurora PostgreSQL as a target database platform, you can only create a database migration assessment report\. For more information, see [Creating migration assessment reports with AWS SCT](CHAP_AssessmentReport.md)\. 

 If you use a virtual target database platform, you can save converted code to a file\. For more information, see [Saving your converted schema to a file](CHAP_Converting.md#CHAP_Converting.Saving)\. 