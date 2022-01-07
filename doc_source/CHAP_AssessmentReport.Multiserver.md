# Using the multiserver assessor to create an aggregate report<a name="CHAP_AssessmentReport.Multiserver"></a>

To help determine the best target direction for your environment, you can use the multiserver assessor\. 

The *multiserver assessor* evaluates multiple servers based on input that you provide for each schema definition that you want to assess\. Your schema definition contains database server connection parameters and the full name of each schema\. After assessing each schema, the assessor produces a summary aggregated report that shows the estimated complexity for each possible migration target\. 

## Using a CSV file for input<a name="CHAP_AssessmentReport.Multiserver.Input"></a>

To provide connection parameters as input, use a comma\-separated value \(CSV\) file as shown in the following example\.

```
Name,Description,Server IP,Port,Service Name,SID,Source Engine,Schema Names,Login,Password,Target Engines
MS SQL Server XyzDB,DMSA,ec2-xx-xx-xx-xxx.us-west-2.compute.amazonaws.com,1433,,,MSSQL,XyzDB.dbo;XyzDB.Aggregation,********,********,MSSQL;POSTGRESQL
MS SQL Server DMS_SAMPLE,,xxx.xxx.xx.xxx,11433,,,MSSQL,dms_sample.dbo,********,********,AURORA MYSQL
Oracle DMS_SAMPLE,,xxx.xxx.xx.xxx,5508,ORCLABC1,,ORACLE,DMS_SAMPLE,********,********,ORACLE;AURORA POSTGRESQL
Oracle DW STAR,,xx.xxx.xxx.xx,1521,star,,ORACLE,XDBSTAR,********,********,MYSQL;MARIA_DB
Oracle ACME,,xx.xxx.xxx.xx,1523,ORCL,,ORACLE,TG,********,********,ORACLE
Teradata foo,,xx.xxx.xxx.xx,11025,,,TERADATA,foo_INT_SL_GCCT_META;foo_INT_SL_SHARED_WORK,********,********,REDSHIFT
SAP ASE EFFG,,xx.xxx.xxx.xx,65234,,,SYBASE_ASE,effg_ref_c.dbo;effg_ref_exmon.dbo;effg_ref_g.dbo;effg_ref_hub.dbo;effg_ref_t.dbo;effg_ref_tmeta.dbo,********,********,POSTGRESQL
SAP ASE Sys,,xx.xxx.xxx.xx,65234,,,SYBASE_ASE,sys.dbo,********,********,AURORA POSTGRESQL
```

The CSV file that you provide for an assessment contains a column called `Target Engines`\. To enter one or more AWS database targets, use the `Target Engines` column\. Separate multiple target engines by using semicolons like this, `MYSQL;MARIA_DB`\. The number of targets affects the time it takes to run the assessment\.

## Output for an aggregated assessment report<a name="CHAP_AssessmentReport.Multiserver.Agreggated"></a>

The aggregated report that the multiserver assessor produces is a CSV file with the following columns: 
+ `Server IP`
+ `Name`
+ `Description`
+ `Schema Name`
+ `Code object conversion % for DATABASE_NAME`
+ `Storage object conversion % for DATABASE_NAME`
+ `Syntax elements conversion % for DATABASE_NAME`
+ `Conversion complexity for DATABASE_NAME`

The following example shows these columns when the CSV file is opened in a spreadsheet\.

![\[New Multiuser Assessment\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/images/aggregate_rpt_columns.png)

To gather information, AWS SCT runs full assessment reports and then aggregates reports by schemas\.

The report shows the following three fields to indicate the conversion % after the assessment\. 

**Code object conversion % **  
The ratio of number of code objects \(such as procedures, functions, views, and so on\) relative to the total number of code objects in the schema that can be converted automatically\. This field indicates the percentage of code objects that SCT can convert automatically or with minimal change\.

**Storage object conversion % **  
The ratio of number of storage objects \(such as tables, indexes, constraints, and so on\) relative to the total number of storage objects in the schema that can be converted automatically\. This field indicates the percentage of storage objects that SCT can convert automatically or with minimal change\.

**Syntax elements conversion % **  
The ratio of number of syntax elements \(such as a SELECT clause, FROM clause, DELETE clause, JOIN clause, and so on\) relative to the total number of syntax elements in the schema that can be converted automatically\. This field indicates the percentage of syntax elements that SCT can convert automatically\.

The conversion complexity calculation is based on the notion of action items\. An *action item* reflects a type of problem found in source code that you need to fix manually during migration to a particular target\. An action item can have multiple occurrences\.

## Performing a multiserver assessment<a name="CHAP_AssessmentReport.Multiserver.Procedure"></a>

Use the following procedure to perform a multiserver assessment with AWS SCT\. You don't need to create a new project in AWS SCT to perform a multiserver assessment\. Before you get started, make sure that you have prepared a \.csv file with database connection parameters\. 

**To perform a multiserver assessment and create an aggregated summary report**

1. In AWS SCT, choose **File**, **New multiserver assessment**\. The **New multiserver assessment** dialog box opens\.  
![\[New multiuser assessment access\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/images/new_assess_screen_v3.png)

1. Enter values for **Project name**, **Location** \(where to store reports\), and **Connection File** \(a \.csv file\)\.

1. Choose **Run**\. The number of target engines can impact the assessment run time\. 

   A progress bar appears indicating the pace of database assessment\.

1. Choose **Yes** if the following message is displayed: **Full analysis of all Database servers may take some time\. Do you want to proceed?**

   When the multiserver assessment report is done, a screen appears indicating so\.

1. Choose **Open Report** to view the aggregated summary assessment report\.

## Locating and viewing reports<a name="CHAP_AssessmentReport.Multiserver.Review"></a>

The multiserver assessment generates two types of reports: 
+ An aggregated report of all source databases\.
+ A detailed assessment report of target databases for each schema name in a source database\. 

Reports are stored in the directory that you chose for **Location** in the **New multiserver assessment** dialog box\. In the example following, reports appear in the directory `Multiserver Assessment4`\. 

To access the detailed reports, you can navigate the subdirectories, which are organized by source database, schema name, and target database engine\. 

![\[Customer report directory\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/images/customer_rpt_dir3.png)

Aggregated reports show information in four columns about conversion complexity of a target database\. The columns include information about conversion of code objects, storage objects, syntax elements, and conversion complexity\. In the example following, columns E through H provide information for an Amazon RDS for Microsoft SQL Server target database\. 

![\[Aggregate report one target\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/images/aggregate_rpt5.png)

The same four columns are appended to the reports for each additional target database engine specified\. In the example following, columns I through L are appended, showing the assessment results for an additional target database engine, Amazon RDS for PostgreSQL\.

![\[Aggregate report second target\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/images/additional_target_db5.png)

The following weighted scale is used to identify the level of complexity to perform a migration\. The number 1 represents the lowest level of complexity, and the number 10 represents the highest level of complexity\.


| Level | Minimum effort | Maximum effort | 
| --- | --- | --- | 
|  1  |  —  |  555  | 
|  2  |  556  |  1110  | 
|  3  |  1111  |  1665  | 
|  4  |  1666  |  2220  | 
|  5  |  2221  |  2775  | 
|  6  |  2776  |  3330  | 
|  7  |  3331  |  3885  | 
|  8  |  3886  |  4440  | 
|  9  |  4441  |  4995  | 
|  10  |  4996  |  —  | 