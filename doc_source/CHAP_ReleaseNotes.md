# Release notes for AWS SCT<a name="CHAP_ReleaseNotes"></a>

This section contains release notes for AWS SCT, starting with version 1\.0\.640\.

## Release notes for AWS SCT Build 657<a name="CHAP_ReleaseNotes.657"></a>


| Source | Target | What's new, enhanced, or fixed | 
| --- | --- | --- | 
| All | All | Upgraded Apache Log4j to version 2\.17 to address security vulnerability issues\.  | 
| All | Amazon Redshift | Improved schema optimization projects, where key management statistics weren't saved in the AWS SCT project\.  | 
| Amazon Redshift | Amazon Redshift | Fixed a problem with the server information update\.  | 
| Apache Cassandra | Amazon DynamoDB | Fixed an issue with mapping rules when using the AWS SCT command\-line interface\.  | 
| Apache Cassandra | Amazon DynamoDB | Resolved an issue when the migration task wasn't created because of an updated title in the certificate\.  | 
| Microsoft SQL Server |  Aurora PostgreSQL PostgreSQL  | Fixed a problem so that action item 7672 doesn't appear during the conversion of Microsoft SQL Server procedures with dynamic SQL\.  | 
|  Azure SQL Database Microsoft SQL Server  |  Aurora PostgreSQL PostgreSQL  | Improved conversion of table\-valued functions\.  | 
|  Azure SQL Database Microsoft SQL Server  |  Aurora PostgreSQL PostgreSQL  | Resolved an issue where the `OUT` argument in a stored procedure with the default return value wasn't converted to the `INOUT` argument\.  | 
| Greenplum Database | Amazon Redshift | Improved optimization strategies by finding the most used tables and columns from the `QueryLog` table\.  | 
| Microsoft SQL Server |  Aurora PostgreSQL PostgreSQL  | Fixed problems with conversion of the following: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_ReleaseNotes.html)  | 
| Microsoft SQL Server |  Aurora PostgreSQL PostgreSQL  | Improved conversion of views with unsupported functions\.  | 
| Microsoft SQL Server |  Aurora PostgreSQL PostgreSQL  | Fixed an issue where unsupported functions as an argument to another function were incorrectly converted\.  | 
| Microsoft SQL Server | Babelfish for Aurora PostgreSQL | Improved conversion of transition table references\.  | 
| Microsoft SQL Server DW | Amazon Redshift | Added the aggregate functions category to the source database metadata tree\.  | 
| Microsoft SQL Server DW | Amazon Redshift | Improved conversion of the `TIME` data type\.  | 
|  Azure Synapse Analytics Greenplum Netezza Microsoft SQL Server DW Snowflake Teradata  | Amazon Redshift | Fixed an issue where `DROP` and `CREATE` scripts weren't saved when using a virtual target database platform\.  | 
| Microsoft SQL Server Integration Services | AWS Glue | Resolved an issue where the scripts of source objects didn't display in the UI\.  | 
| Netezza | Amazon Redshift | Improved optimization strategies by choosing the fact table and appropriate dimensions for collocation\.  | 
| Oracle |  Aurora PostgreSQL PostgreSQL  | Resolved an issue to correctly convert Oracle triggers, which use sequence numbers\.  | 
| Oracle |  Aurora PostgreSQL PostgreSQL  | Improved conversion of views with public database links\.  | 
| Oracle DW | Amazon Redshift | Improved optimization strategies by analyzing the cardinality of index columns\.  | 
| Oracle DW | Amazon Redshift | Fixed an issue where custom user\-defined scalar functions with string concatenation were incorrectly converted\.  | 
| Snowflake | Amazon Redshift | Fixed an issue where the **Save as SQL** option didn't display in the UI\.  | 
| Teradata | Amazon Redshift | Fixed an issue where statistic collection failed with the `LOADER ERROR` exception\.  | 
| Teradata | Amazon Redshift | Fixed an issue where the **Create report** option didn't display in the UI\.  | 
| Teradata | Amazon Redshift | Improved conversion of the `CAST` function\.  | 
| Teradata | Amazon Redshift | Fixed a broken conversion for `ST_Line_Interpolate_Point`\. | 
| Teradata | Amazon Redshift | Removed an unexpected value from the Python library path\. | 
| Teradata | Amazon Redshift RSQL | Fixed a resolver error that appeared during the conversion of multiple FastLoad scripts\. | 
| Teradata BTEQ | Amazon Redshift RSQL | Improved conversion of the `DATABASE` command and geometry data types\. | 
| Teradata BTEQ | AWS Glue | Fixed an issue with an incorrect synchronization of the source and target scripts in the UI\. | 

**Issues resolved:**
+ General improvements\.

## Release notes for AWS SCT Build 656<a name="CHAP_ReleaseNotes.656"></a>


| Source | Target | What's new, enhanced, or fixed | 
| --- | --- | --- | 
| All | All | Added support of multiple source and target databases within one project\. Users can now create mapping rules to match different database schemas and target platforms in the same project\.  | 
| All | All |  UI improvements:  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_ReleaseNotes.html)  | 
| Cassandra | Amazon DynamoDB | Resolved a search issue where the `CASSANDRA_HOME` variable didn't include a slash \(`/`\) after `cassandra.yaml` or the `conf` folder\. | 
| Cassandra | Amazon DynamoDB | Added support of the Amazon Machine Image \(AMI\) for Amazon Linux 2\. | 
| Cassandra | Amazon DynamoDB | Improved error message provided when an incorrect key is given for Cassandra\. | 
| Cassandra | Amazon DynamoDB | Improved conversion by changing a property in the `cassandra-env.yaml` file depending on the version of the target database\. | 
| Cassandra | Amazon DynamoDB | Increased the Java version on the target Cassandra Datacenter to 1\.8\.0\. | 
| Greenplum | Amazon Redshift | Improved optimization strategies in **Project Settings**\. | 
| Greenplum | Amazon Redshift | Resolved a data migration issue where objects weren't applied to database with this error: `An I/O error occurred while sending to the backend`\. | 
|  Greenplum Microsoft SQL Server DW  | Amazon Redshift | Resolved an issue where the `Apply RTRIM to string columns` option didn't display in the UI\. | 
| Microsoft SQL Server | Babelfish for Aurora PostgreSQL | Added support for Babelfish for Aurora PostgreSQL as a target platform\. Users can now create an assessment report to estimate the migration from SQL Server to Babelfish for Aurora PostgreSQL\. | 
| Netezza | Amazon Redshift | Improved optimization strategies in **Project Settings**\. | 
| SAP ASE |  Aurora PostgreSQL PostgreSQL  | Implemented ability to generate unique names for indexes\. | 
| SAP ASE |  Aurora PostgreSQL PostgreSQL  | Fixed an issue with a duplicate index column in the target script\. | 
| Snowflake | Amazon Redshift | Resolved a problem where Hide empty schemas, Hide empty databases, and Hide system databases/schemas options weren't displayed in the UI\. | 
| Teradata | Amazon Redshift RSQL | Added support for conversion of Teradata MultiLoad job scripts to Amazon Redshift RSQL scripts\. | 
| Teradata | Amazon Redshift RSQL | Fixed a problem with conversion of substitution variables in FastLoad and FastExport scripts\. | 
| Teradata | Amazon Redshift RSQL | Fixed an issue where action items didn't display in the **Action Items** tab after switching from the **Summary** tab\. | 
| Teradata | Amazon Redshift RSQL | Resolved an issue where an error occurs after generating report during FastExport scripts conversion\. | 
| Teradata | Amazon Redshift RSQL | Resolved formatting issues after Shell scripts conversion\. | 
| Teradata | Amazon Redshift RSQL | Fixed a problem so that AI 13177 is now commented in converted script\. | 
| Teradata | Amazon Redshift | Fixed a broken conversion of temporal tables\. | 
| Teradata | Amazon Redshift | Improved conversion of the `SET QUERY_BAND` statement\. | 
| Teradata | Amazon Redshift | Fixed a broken conversion of the `NORMALIZE` operation\. | 
| Vertica | Amazon Redshift | Improved the description of AI 17008\. | 

**Issues resolved:**
+ General improvements\.

## Release notes for AWS SCT Build 655<a name="CHAP_ReleaseNotes.655"></a>


| Source | Target | What's new, enhanced, or fixed | 
| --- | --- | --- | 
| Teradata | Amazon Redshift RSQL | Fixed a problem to ensure all assessment issues appear in reports when FastLoad or MultiLoad is used\. | 
| Teradata | Amazon Redshift RSQL | Fixed a problem to ensure the Save manifest to S3 action is enabled in offline mode when using FastLoad\. | 
| Teradata | Amazon Redshift RSQL | Fixed an issue to ensure mapping rules are applied for scripts like FastLoad\. | 
| Greenplum | Amazon Redshift | Increased the minimum supported driver version for Greenplum to 42\.2\.5\. | 
| Greenplum | Amazon Redshift | Added a connection to Greenplum via SSL with driver version 42\.2\.5 or higher\. | 
| Oracle DW | Amazon Redshift | Improved support for executing custom user\-defined scalar functions \(UDF\) within another UDF\. | 
| Oracle DW | Amazon Redshift | Fixed an issue where functions weren't applied to database with this error: `Failed to compile udf`\. | 
| Oracle DW | Amazon Redshift | Improved conversion by using appropriate type declarations such as, `pls-type` for %ROWTYPE parameters\. | 
| Teradata | Amazon Redshift RSQL | Resolved an issue where information type assessment issues didn't display in the report\. | 
| Teradata | Amazon Redshift RSQL | Resolved a transformer error after converting some scripts\. | 
| Teradata | Amazon Redshift RSQL |  Fixed a problem so that an issue is now commented in converted script\. | 
| Teradata | Amazon Redshift | Resolved an issue where FastExport \->EXPORT \-> 'null' displayed instead 'CAST' after conversion\. | 
| Teradata | Amazon Redshift | Resolved a problem where some functions of an extension pack failed when applied with `Cause:[JDBC Driver]String index out of range: 0` if using driver version 1\.2\.43 | 
| Teradata | Amazon Redshift | SET table conversion—SET table emulation added for insert\-select statements\. | 
| Teradata | Amazon Redshift | CAST—support additional data type casting\. | 
| Teradata | Amazon Redshift | Fixed a broken conversion for "other\_current\_time\_01" | 
| Teradata | Amazon Redshift | Teradata FastExport – Amazon Redshift RSQL: Improved conversion of Teradata FastExport commands—FIELD | 
| Teradata | Amazon Redshift | Teradata FastExport – Amazon Redshift RSQL: Improved conversion of Teradata FastExport commands—LAYOUT | 
| Oracle |  PostgreSQL Aurora PostgreSQL  | Resolved an issue where target script of objects with SAVE EXCEPTIONS STATEMENT changed after reconversion\. | 
| Oracle |  PostgreSQL Aurora PostgreSQL  | Resolved an issue where wrong field was specified in the ORDER BY clause after `proc_cursor_with_calc_columns` conversion\. | 
| Oracle |  PostgreSQL Aurora PostgreSQL  | Resolved: an extra aws\_oracle\_ext$array\_id$temporary variable declaration is required in an ASSOCIATIVE COLLECTION conversion\. | 
| Oracle |  PostgreSQL Aurora PostgreSQL  | Resolved: the wrong conversion of a PRIMARY KEY with the same name of an INDEX owned by the same table\. | 

**Issues resolved:**
+ General improvements\.

## Release notes for AWS SCT Build 654<a name="CHAP_ReleaseNotes.654"></a>


| Source | Target | What's new, enhanced, or fixed | 
| --- | --- | --- | 
| Oracle |  PostgreSQL Aurora PostgreSQL  | Resolved an issue with Hierarchical Query Pseudocolumns, PRIOR columns parsing error\. | 
| Oracle |  PostgreSQL Aurora PostgreSQL  | Resolved an issue to correctly convert a multi\-line comment containing a slash and asterisk \(/\*\)\. | 
| Oracle |  PostgreSQL Aurora PostgreSQL  | Added system view USER\_COL\_COMMENTS emulation to the extension pack\. | 
| Oracle |  PostgreSQL Aurora PostgreSQL  | Improved conversion of quoted literals\. | 
| DB2 LUW |  PostgreSQL Aurora PostgreSQL  | Improved conversion of LABEL statements that add or replace labels in the descriptions of tables, views, aliases, or columns\. | 
| Oracle | None | Substituted SYS\.USER$ system table with DBA\_USERS view, and improved queries\. | 
| Oracle DW | Amazon Redshift | Updated Oracle DW metadata queries\. | 
| Teradata BTEQ | Amazon Redshift RSQL | Resolved issue where "merge\_01" was incorrectly converted\. | 
| Teradata BTEQ | Amazon Redshift RSQL | Resolved issue so that End or Identify \(EOI\) appears at the end of a script on a new line\. | 
| Azure Synapse | Amazon Redshift | Improved error message provided when an incorrect password given for Azure Synapse\. | 
| Teradata | Amazon Redshift | Improved UPDATE statement conversion to carry forward the right alias name per Teradata standard\. | 
| Teradata | Amazon Redshift | Resolved a cursor conversion error where actions weren't received\. | 
| Teradata | Amazon Redshift | Resolved an issue where a TD\_NORMALIZE\_OVERLAP conversion was dropping rows\. | 
| Teradata | Amazon Redshift | Now supports strict date checking for the enhanced TO\_DATE function\. | 
| Teradata | Amazon Redshift | Improved conversion of Built\-in function TO\_NUMBER\(n\)\. | 
| Teradata | Amazon Redshift | Resolved an issue where the **Schemas** category was absent from metadata tree\. | 
| Greenplum | Amazon Redshift | Added **GP\_SEGMENT\_ID** selection to list when creating virtual partition for a Greenplum table\. | 
| Greenplum | Amazon Redshift | Resolved an issue where functions weren't applied on target\. | 
| MS SQL Server DW | Amazon Redshift | Resolved an issue where a transform error occurs after conversion without AI 9996\.  | 
| MS SQL Server DW | Amazon Redshift | Resolved an issue where an error was logged when opening the extension pack wizard\.  | 
| MS SQL Server DW | Amazon Redshift | Resolved an issue when an incorrect style of comments was used for Redshift Python functions\. | 
| Netezza | Amazon Redshift | Resolved an issue where a Netezza–Redshift extension pack with an AWS profile failed to create\. | 
| Teradata | Amazon Redshift RSQL | Improved conversion of FastLoad SESSIONS command\. | 
| Teradata | Amazon Redshift RSQL | Improved FastLoad scripts assessment reports\. | 
| Teradata | Amazon Redshift RSQL | Implemented FastLoad WRITER **Save to S3** action\. | 
| Teradata | Amazon Redshift RSQL | Resolved an issue where FastLoad **Save Script\\Save manifest to s3** buttons weren't active\. | 
| Teradata | Amazon Redshift RSQL | Resolved an issue where FastLoad multifile\_script only created one manifest file after conversion instead of the expected three files\. | 
| Teradata | Amazon Redshift RSQL | Resolved an issue where FastLoad had extra folders displayed in an S3 path\. | 
| Teradata | Amazon Redshift RSQL | Resolved an issue where FastLoad had the incorrect name of the manifest file in an S3 path\. | 

**Issues resolved:**
+ General improvements\.

## Release notes for AWS SCT Build 653<a name="CHAP_ReleaseNotes.653"></a>


| Source | Target | What's new, enhanced, or fixed | 
| --- | --- | --- | 
| Oracle |  PostgreSQL Aurora PostgreSQL  | Implemented ability to convert dynamic SQL created in called functions or procedures\. | 
| Oracle |  PostgreSQL Aurora PostgreSQL  | Improved Dynamic SQL conversion: In\-parameters as bind variables\. | 
| Oracle DW 18, 19 | Amazon Redshift | Oracle to Redshift conversion improvements implemented: enhanced conversion built\-ins\. Aggregate LISTAGG; Analytic LISTAGG\. | 
| Oracle DW 18,19 | Amazon Redshift | Oracle to Redshift conversion improvements implemented: Query new features\. | 
| Vertica | Amazon Redshift | Vertica to Redshift conversion improvements implemented: SSL to JDBC connection with SSL=true\. | 
| MS SQL Server DW | Amazon Redshift | MS SQL Server to Redshift conversion improvements: External Tables\. | 
| Teradata | Amazon Redshift | Teradata to Redshift conversion improvements: INTERVAL data types arithmetic operations\. | 
| Teradata | Amazon Redshift | Teradata to Redshift conversion improvements: Support for lateral column aliases\. | 
| Oracle | None | The following Loader queries now use `DBA_USERS` instead of `SYS.USER$`:  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_ReleaseNotes.html)  | 
| Teradata | Amazon Redshift | Improved alignment of comments when SCT converts Teradata macros to Redshift stored procedures\. | 
| Oracle DW | Amazon Redshift | Improved conversion of Date/Timestamp format elements: `TO_DATE`, `TO_TIMESTAMP`, and `TO_TIMESTAMP_TZ` | 
| Teradata | Amazon Redshift | Resolved Teradata cursor conversion error\. | 
| Teradata | Amazon Redshift | Resolved issue that caused attributes of `TD_NORMALIZE_OVERLAP` to be dropped during conversion\. | 
| Teradata | Amazon Redshift | Resolved an issue where `MAX` function was ignored when SCT converted a query\. | 
| Teradata | Amazon Redshift | SCT now converts Teradata CHARACTERS function to Redshift LENGTH function\. | 
| Teradata | Amazon Redshift | SCT now supports conversion of FORMAT to TO\_CHAR for most commonly used formats\. | 
| All | All | Improved conversion of encrypted routines\.  | 

**Issues resolved:**
+ General improvements\.

## Release notes for AWS SCT Build 652<a name="CHAP_ReleaseNotes.652"></a>


| Source | Target | What's new, enhanced, or fixed | 
| --- | --- | --- | 
| Microsoft SQL Server | PostgreSQL | Added app locking for `sp_getapplock` and `sp_releaseapplock` functions\. | 
| None | Amazon Redshift | Command Line Interface \(CLI\) improvement: implemented Script Command mode\. | 
| Oracle |  PostgreSQL Aurora PostgreSQL  | Implemented routine parameters sampling inside dynamic SQL\. | 
| Oracle |  PostgreSQL Aurora PostgreSQL  | Conversion improvements to dynamic SQL created in called functions or procedures\. | 
|  Microsoft SQL Server Oracle DB2 LUW  | Aurora PostgreSQL | Each lambda function is deployed and configured via policy only once, and common lambda functions are reused for all possible sources\. | 
| DB2 LUW | PostgreSQL | Resolved issue that caused error message, "9996 — Severity critical — Transformer error occurred" when using DB2 LUW as source\. | 
| Teradata | Amazon Redshift | Support for recursive table expressions in forthcoming Amazon Redshift launch\. | 
| Azure Synapse | Amazon Redshift | Implemented schema optimization rules\. | 
| Teradata | Amazon Redshift | Support Time Zone conversion from Teradata macros to Redshift stored procedures\. | 
| Teradata | Amazon Redshift | Support arithmetic on PERIOD values\.  | 
| Teradata | Amazon Redshift | Support conversion of Teradata recursive common table expressions \(RECURSIVE CTE\)\. | 
| Teradata | Amazon Redshift | Support case sensitive identifiers via the user setting, `enable_case_sensitive_identifier`\. So, "COLUMN\_NAME" and "Column\_Name" become different column names\. | 
| Teradata | Amazon Redshift | Resolved Decimal data type issue so that Decimal fields convert with the same precision\.  | 
| Teradata | Amazon Redshift | Resolved issue with interval arithmetic conversion so that interval arithmetic subtraction converts correctly\.  | 
| Teradata | Amazon Redshift | Improved Teradata NUMBER to DATE type casting\. | 
| Teradata | Amazon Redshift | Improved Teradata DATE to NUMBER type casting | 
| Teradata BTEQ | Amazon Redshift | Improved PERIOD data type conversion\. | 
| Teradata | Amazon Redshift | Resolved issue with loading metadata for a table with GEOMETRY columns so that it now loads from Teradata correctly\. | 
| Teradata | Amazon Redshift | Support conversion of merge statements when converting Teradata macros to Redshift stored procedures\.  | 
| Teradata | Amazon Redshift | Improved conversion of simple macros when migrating from Teradata to Redshift\.  | 
| Teradata | Amazon Redshift | Ensured the conversion of Teradata UPDATE statements carry forward the right alias name per Teradata standard\.  | 

**Issues resolved:**
+ General improvements\.

## Release notes for AWS SCT Build 651<a name="CHAP_ReleaseNotes.651"></a>


| Source | Target | What's new, enhanced, or fixed | 
| --- | --- | --- | 
| All | All | Enhanced AWS SCT reports to update links to the recommended conversion action items listed\. | 
| MS SQL Server | PostgreSQL | Added support for conversion of `STR()` function\. | 
| MS SQL Server | PostgreSQL | Added support for converting the bitwise EXOR operator \(`^` in Microsoft SQL Server\) to PostgreSQL as the `#` operator\.  | 
| Oracle | PostgreSQL | Resolved an issue where the AWS SCT extension pack `aws_oracle_ext.UNISTR(null)` function hung for `NULL` on a PostgreSQL target\. AWS SCT now handles the `NULL`\. | 
| Teradata BTEQ | Amazon Redshift RSQL | Conversion improvements made to resolve an issue where conversion of Amazon Redshift RSQL MERGE gave a transform error\. | 
| Oracle DW | Amazon Redshift | Implemented enhanced built\-ins\. | 
| Oracle DW | Amazon Redshift | Added metadata feature driven enhancements, including Auto\-List partitioning \(TBL\_PART\_LIST\_AUTO\), Multi\-column List \(TBL\_PART\_MULTI\_LIST\) and Interval\-Reference \(TBL\_PART\_RANGE\_INTVAL\_REF\)\. | 
| none | Amazon Redshift | Increased partition table limits of physical partitions used for `UNION ALL` conversions\. | 
| Teradata | Amazon Redshift | Conversion improvements made to the scope of Assessment reports\. | 
| Teradata | Amazon Redshift | Conversion improvements made to complex Teradata MACRO conversions\. | 
| Teradata | Amazon Redshift | Improved conversion of Teradata macros to Amazon Redshift stored procedures while commenting out unsupported SQL\. | 
| Teradata | Amazon Redshift | Resolved an issue where conversion of Teradata macros to Amazon Redshift stored procedures resulted in the wrong alias name references\. | 
| Teradata | Amazon Redshift | Improved conversion of Teradata `QUALIFY` statement\. | 
| Teradata | Amazon Redshift | Improved conversion to carry forward comments to Amazon Redshift and retain a history of changes performed on the view\. | 
| Teradata | Amazon Redshift | Resolved an issue where the RESET WHEN clause didn't result in the correct conversion\. | 
| Teradata BTEQ | Amazon Redshift | Improved conversion of BTEQ scripts that contain MERGE statements\. | 
| Teradata | Amazon Redshift | Added built\-in functions to improve conversion of PERIOD data type fields\. | 
| Microsoft SQL Server | Amazon Redshift | Enhanced transformation data type mapping for TIME data type\. | 
| All | All | Added access to the initial publication of the *AWS Schema Conversion Tool CLI Reference* manual in PDF format\. See [AWS Schema Conversion Tool CLI Reference](https://s3.amazonaws.com/publicsctdownload/AWS+SCT+CLI+Reference.pdf)\.  | 

**Issues resolved:**
+ General improvements\.

## Release notes for AWS SCT Build 650<a name="CHAP_ReleaseNotes.650"></a>


| Source | Target | What's new, enhanced, or fixed | 
| --- | --- | --- | 
| All | All | Updated and enhanced use of extractor agents, including: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_ReleaseNotes.html) For more information, see [Using data extraction agents](agents.md)\.  | 
| All | Amazon RDS PostgreSQL 13 | AWS SCT now supports Amazon RDS PostgreSQL 13 as target\. | 
| Microsoft SQL Server | Aurora PostgreSQL | Improved conversion of a result set from an Microsoft SQL Server procedure to an Aurora PostgreSQL target\. | 
| Oracle DW | Amazon Redshift | Implemented Oracle to Amazon Redshift conversion improvements\. | 
| Oracle DW | Amazon Redshift | Implemented improvements to converting dynamic SQL statements\. | 
| Oracle DW | Amazon Redshift | Implemented improvements to SQL UDF conversion\.  | 
| Oracle DW | Amazon Redshift | Clarified message that AWS SCT doesn't support conversion of EXTERNAL TABLES\. | 
| Oracle DW | Amazon Redshift | Enhanced built\-in conversion functions\.  | 
| Teradata BTEQ | Amazon Redshift RSQL | Improved handling substitution parameters inside BTEQ scripts while using AWS SCT GUI\. | 
|  Microsoft SQL Server DW Microsoft SQL Server Azure Azure Synapse  | All | Upgraded the minimum supported JDBC driver version for Microsoft SQL Server, Azure, Azure Synapse\. | 

**Issues resolved:**
+ Teradata: Macro conversion additional improvements \[RESOLVED\]
+ Special characters escaped in the target causing SQL errors and re\-work needed to place them back \[RESOLVED\]
+ General improvements

## Release notes for AWS SCT Build 649<a name="CHAP_ReleaseNotes.649"></a>


| Source | Target | What's new, enhanced, or fixed | 
| --- | --- | --- | 
| Microsoft SQL Server DW | Amazon Redshift | MSSQL to Amazon Redshift conversion improvements to support temporal tables\. | 
| Oracle DW | Amazon Redshift | Implemented built\-in function enhancements, such as: Conversion functions [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_ReleaseNotes.html)  | 
| Oracle DW | Amazon Redshift | Implemented function enhancements for Approximate Query Processing, such as: Aggregate functions [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_ReleaseNotes.html)  | 
| Teradata | Amazon Redshift | Implemented conversion enhancements for Teradata auto sort and distribution key selection\. The DB engine automatically selects distribution and sort keys\. Introduced a radio button labeled **Use Amazon Redshift automatic table tuning** to **Current projects settings** >** Optimization strategies** > **Initial Key Selection Strategy** dialog\.  | 
| Teradata | Amazon Redshift | Enhanced AWS SCT table loader to ensure AWS SCT loads all tables from Teradata\. | 
| Teradata | Amazon Redshift | Implemented conversion enhancements so that Amazon Redshift supports correlated subquery patterns that include a simple WHERE NOT EXISTS clause\. | 
| Teradata | Amazon Redshift | Added support for use of ECHO commands in macros\. | 
| DB2 LUW | PostgreSQLAurora PostgreSQL | Implemented support for DYNAMIC RESULTS SETS conversion, including: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_ReleaseNotes.html)  | 
| Microsoft SQL Server Oracle DB2 LUW SAP ASE | Aurora PostgreSQL | Implemented support for current Aurora RDS PostgreSQL as target\. | 
| Microsoft SQL Server Oracle DB2 LUW SAP ASE | MariaDB | Implemented support for MariaDB 10\.5 as target\. | 
| Microsoft SQL Server | MariaDB | Implemented support of INSERT\-RETURNING which returns a result set of the inserted rows\. | 
| Oracle | Aurora PostgreSQL | Added support of the XMLFOREST function for converting from Oracle to Aurora PostgreSQL\. | 

**Issues resolved:**
+ General improvements\.

## Release notes for AWS SCT Build 648<a name="CHAP_ReleaseNotes.648"></a>


| Source | Target | What's new, enhanced, or fixed | 
| --- | --- | --- | 
| Oracle |  PostgreSQL Amazon Aurora PostgreSQL\-Compatible Edition  | Aurora PostgreSQL extension pack custom apply mode implemented: operators for numeric/date and text types\. | 
|  Oracle Microsoft SQL Server DB2 LUW  |  Aurora PostgreSQL  |  Aurora PostgreSQL Lambda Invoke configuration implemented: aws\_lambda extension creation; IAM role assignment to the Aurora PostgreSQL cluster\. [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_ReleaseNotes.html)  | 
| Oracle | PostgreSQL | FORALL statement conversion refactoring implemented: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_ReleaseNotes.html)  | 
| Oracle DW 18, 19 | Amazon Redshift | Oracle to Amazon Redshift conversion improvements implemented: enhanced conversion built\-ins\. Aggregate LISTAGG; Analytic LISTAGG\. | 
| Oracle DW 18,19 | Amazon Redshift | Oracle to Amazon Redshift conversion improvements implemented: Query new features\. | 
| Vertica | Amazon Redshift | Vertica to Amazon Redshift conversion improvements implemented: SSL to JDBC connection with SSL=true\. | 
| Microsoft SQL Server DW | Amazon Redshift | Microsoft SQL Server to Redshift conversion improvements: External Tables\. | 
| Teradata | Amazon Redshift | Teradata to Redshift conversion improvements: INTERVAL data types arithmetic operations\. | 
| Teradata | Amazon Redshift | Teradata to Redshift conversion improvements: Support for lateral column aliases\. | 

**Issues resolved:**
+ General improvements

## Release notes for AWS SCT Build 647<a name="CHAP_ReleaseNotes.647"></a>


| Source | Target | What's new, enhanced, or fixed | 
| --- | --- | --- | 
| Microsoft SQL Server | Microsoft SQL Server |  RDS now supports Database Mail feature\.  | 
| Microsoft SQL Server | MySQL |  Implementing the maximum name of each type of identifier — The maximum length of object names \(for example, tables, constraints, columns\) in SQL Server is 128 characters\. The maximum length of object names in MySQL is 64 characters\. To write converted objects to the MySQL database you need to shorten their names\. To prevent duplicate names after cutting, you need to add "checksum" of the original object name to the new names\. Cut names longer than 64 characters as follows: `[first N chars]() + "" + [checksum]()`  `[first N chars] = 64 - 1 - [length of checksum string] ` For example: example\_of\_a\_test\_schema\_with\_a\_name\_length\_greater\_than\_64\_characters ?? example\_of\_a\_test\_schema\_with\_a\_name\_length\_greater\_than\_64\_9703   | 
| Oracle | MySQL/Aurora MySQL |  Implemented load and conversion of comments on storage objects\. For example, processing of comments on Tables, and processing of comments on Table/View columns\.  | 
| Teradata | Amazon Redshift |  Added support for TIME data type conversion\.  | 
| Teradata | Amazon Redshift |  Conversion improvements — TD\_NORMALIZE\_OVERLAP implemented\.  | 
| Microsoft SQL Server DW | Amazon Redshift |  Conversion improvements — SELECT with WITH clause; SELECT without FROM  | 
| All | All |  AWS SCT Data Migration Service Assessor \(DMSA\) — This new feature enables you to evaluate multiple servers and receive a summary report that shows the best target direction for your environment\.  | 
| All | All |  AWS SCT Wizard — Target comparison now shows differences between targets in a single table view\.  | 
| All | All |  Tree Filter UI — Redesigned metadata filter handles more complex filtering patterns\.  | 
| All | All |   Assessment Report — Redesigned **Warning** section provides a better description and clearer understanding of an issue\.  | 

**Issues resolved:**
+ General improvements
+ Data Extractors — Subtask failed with ConcurrentModificationException \[RESOLVED\]\.
+ Microsoft SQL Server to MySQL — max identifier lengths \[RESOLVED\]\.

## Release notes for AWS SCT Build 646<a name="CHAP_ReleaseNotes.646"></a>


| Source | Target | What's new, enhanced, or fixed | 
| --- | --- | --- | 
| Oracle | PostgreSQL |  Improved TM format model implementation\.  | 
| Oracle | PostgreSQL |  SP format mask implementation provides basic support for SP suffix, only for the English language\.  | 
| Oracle | PostgreSQL |  Oracle long object names handling — AWS SCT now handles Oracle long object names according to target max identifier length attribute\.  | 
|  | Amazon Redshift |  Amazon Redshift encoding AZ64 with AWS SCT — Added compression encoding AZ64 for some data types  | 
| Teradata | Amazon Redshift |  Added support for Implicit transactions conversion\.  | 
| Teradata | Amazon Redshift |  Added support for Teradata geospatial built\-in functions: `ST_LineString` Methods  | 
| Greenplum | Amazon Redshift |  Greenplum sequence conversion — Added the next items to the Properties tabs: **min value**, **max value**, **increment**, **cycle**\.  | 
| Greenplum | Amazon Redshift |  Resolver — Added "char" data type resolving\.  | 
| Greenplum | Amazon Redshift |  Character conversion length — Updated PL/pgSQL conversion for character type\.  | 
| Greenplum | Amazon Redshift |  Resolved an issue with Greenplum distribution key selection where a table had DISTRIBUTION KEY but AWS SCT couldn't recognise and fetch table as RANDOMLY DISTRIBUTED\.   | 
| Teradata | Amazon Redshift |  Teradata cursor support — Added support for cursors conversion\.  | 
| Teradata | Amazon Redshift |  Identity columns — Added support for Identity columns conversion\.  | 
| Teradata | Amazon Redshift |  INTERVAL data types — Added support for INTERVAL data types conversion\.  | 

**Issues resolved:**
+ General improvements
+ Greenplum: Unable to run conversion due to the error in the log \[RESOLVED\]\.
+ MSSQL — PostgreSQL: Transformer error when converting LAG function \[RESOLVED\]\.
+ MSSQL — PostgreSQL: SCOPE\_IDENTITY \[RESOLVED\]\.
+ AWS SCT hanging in DW projects \[RESOLVED\]\.
+ Need mapping rule to remove additional space on the column name in AWS SCT \[RESOLVED\]\.

## Release notes for AWS SCT Build 645<a name="CHAP_ReleaseNotes.645"></a>


| Source | Target | What's new, enhanced, or fixed | 
| --- | --- | --- | 
| SQL SSIS | AWS Glue |  Added support for ETL SSIS to AWS Glue conversion\. For more information, see [Converting SSIS to AWS Glue using AWS SCT](CHAP-converting-aws-glue-ssis.md)\.  | 
| Teradata | Amazon Redshift | Provide solution to resolve Teradata non\-fully qualified views \(view names or non\-fully qualified objects within the view\)\. | 
| Teradata | Amazon Redshift | Added support of ASCII function to compute nodes\. | 
| Teradata | Amazon Redshift | When AWS SCT spots multi\-byte data in a Teradata `CHAR` defined as `CHAR(N)`, it is converted to `VARCHAR(3*N)` in Amazon Redshift\. | 
| Teradata | Amazon Redshift | Provide Teradata `CAST` conversion between dates and numbers\. [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_ReleaseNotes.html)  | 
| Teradata | Amazon Redshift | Support conversion of Teradata `PERIOD` data types into two Amazon Redshift `TIMESTAMP` columns: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_ReleaseNotes.html)  | 
| Teradata | Amazon Redshift | Support conversion of Teradata `RANK` function with `RESET WHEN` clause\.  | 
| Teradata | Amazon Redshift | Improved support of CAST in explicit data type conversions, and implicit CASTs on expressions\. | 
| Teradata | Amazon Redshift | Report unsupported correlated subquery patterns\. For more information, see [Correlated subqueries](https://docs.aws.amazon.com/redshift/latest/dg/r_correlated_subqueries.html) in the *Amazon Redshift Database Developer Guide\.*  | 
| *none* | Amazon Redshift | Improved tables limit support for RA3 node types\. | 
| Teradata | Amazon Redshift | Added support for Teradata geospatial data extraction\. For more information, see [Querying spatial data in Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/dg/geospatial-overview.html) in the *Amazon Redshift Database Developer Guide\.*  | 
| Microsoft SQL Server | PostgreSQL | Added the option, `convert_procedures_to_function`\. | 

**Issues resolved:**
+ General improvements

## Release notes for AWS SCT Build 644<a name="CHAP_ReleaseNotes.644"></a>

Changes for AWS SCT releases 1\.0\.643 are merged into AWS SCT 1\.0\.644 release\. 


| Source | Target | What's new, enhanced, or fixed | 
| --- | --- | --- | 
| Teradata | Amazon Redshift |  Multiple conversion improvements\. [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_ReleaseNotes.html) Added special AWS SCT CLI commands that can parse the provided sql/bteq scripts and generate a report on the number of syntax structures encountered in the source code\. [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_ReleaseNotes.html) Added an assessment report action item: Teradata columns with custom date formats are not supported in Amazon Redshift\.  | 
| Oracle | PostgreSQL/Aurora PostgreSQL |  Added functionality to save extension pack installation scripts\. Changed severity level for AI 5334\. Improved performance of using a record as package variable `IMPLEMENTATION`\. Added `XMLAGG` aggregation function support  | 
| IBM Db2 | PostgreSQL/Aurora PostgreSQL |  Added load and conversion of comments on storage objects implementation\.  | 
| MS SQL DW | Amazon Redshift |  Conversion improvement: Resolved issue with `PATINDEX`\. UI improvements:  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_ReleaseNotes.html)  | 
| Vertica | Amazon Redshift |  UI improvement: Save as SQL for source tree implementation\.  | 

**Issues resolved:**
+ General improvements to conversions between Teradata and Amazon Redshift
+ General bug fixing and UI improvements

## Release notes for AWS SCT Build 642<a name="CHAP_ReleaseNotes.642"></a>

Changes for AWS Schema Conversion Tool release 1\.0\.642\.

**Note**  
AWS Schema Conversion Tool \(AWS SCT\) build 1\.0\.642 changes are applicable to Windows, Ubuntu, and Fedora\. There is no 1\.0\.642 build for macOS\.


| Source | Target | What's new, enhanced, or fixed | 
| --- | --- | --- | 
|  Vertica | Amazon Redshift |  Enable exporting of SQL scripts from a Vertica database to Amazon Redshift\.  | 
|  Oracle | MariaDB/SQL MODE=ORACLE/MySQL/Amazon Aurora MySQL |  Implemented the PL/SQL declaration section in the WITH clause\.  | 
|  Oracle | PostgreSQL/Aurora PostgreSQL |  Added support for `DBMS_SESSION.RESET_PACKAGE` and `DBMS_SESSION.MODIFY_PACKAGE`\.  | 

**Issues resolved:**
+ Assessment Report Enhancement
+ Assessment Report UI Enhancement
+ Add ability to change JVM settings from UI
+ General improvements

## Release notes for AWS SCT build 641<a name="CHAP_ReleaseNotes.641"></a>

Changes for AWS Schema Conversion Tool release 1\.0\.641\.

**Note**  
AWS Schema Conversion Tool \(AWS SCT\) build 1\.0\.641 changes are applicable to Windows, Ubuntu, and Fedora\. There is no 1\.0\.641 build for macOS\.


| Source | Target | What's new, enhanced, or fixed | 
| --- | --- | --- | 
|  Oracle/MS SQL/MySQL/PostgreSQL/Db2 LUW | All |  Produce Time Report calculations in the \.csv file\.  | 
| Teradata | Amazon Redshift |  Added support for CSUM function\. Added support for Teradata geospatial data types\.  | 
| Teradata | All | Added support for converting IDENTITY columns\. | 
| Greenplum | Amazon Redshift |  Added support for distribution style AUTO during Greenplum table conversion\.  | 
| SAP ASE | All |  Produce Time Report calculations in the \.csv file\.  | 

Resolved:
+ Various bug fixes\.
+ Various performance improvements\.

## Release notes for AWS SCT Build 640<a name="CHAP_ReleaseNotes.640"></a>

Changes for AWS SCT releases 1\.0\.633, 1\.0\.634, 1\.0\.635, 1\.0\.636, 1\.0\.637, 1\.0\.638, 1\.0\.639, and 1\.0\.640 are merged into AWS SCT 1\.0\.640 release\. 

**Note**  
AWS SCT build 1\.0\.640 changes are applicable to Windows, Ubuntu, and Fedora\. They don't apply to macOS\.  
You can't install AWS SCT version 1\.0\.640 or later on Apple macOS\. AWS SCT version 1\.0\.632 was the last version to support installation on Apple macOS\.

In the following tables, you can find lists of the features and bug fixes for the AWS Schema Conversion Tool versions that have been combined into release 1\.0\.640\. These tables group features and bug fixes by the source engine\. 

**Topics**
+ [Release 1\.0\.640 Oracle changes](#640.oracle)
+ [Release 1\.0\.640 Microsoft SQL Server changes](#640.mssql)
+ [Release 1\.0\.640 MySQL Changes](#640.mysql)
+ [Release 1\.0\.640 PostgreSQL changes](#640.postgresql)
+ [Release 1\.0\.640 Db2 LUW changes](#640.db2luw)
+ [Release 1\.0\.640 Teradata changes](#640.Teradata)
+ [Release 1\.0\.640 changes for other engines](#640.other)

### Release 1\.0\.640 Oracle changes<a name="640.oracle"></a>

The following table lists build 1\.0\.640 changes in which Oracle is the source engine\.


| Source | Target | What's new, enhanced, or fixed | 
| --- | --- | --- | 
| Oracle | PostgreSQL/Aurora PostgreSQL\-Compatible Edition |  Improved performance of the following functions when used in a WHERE clause: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_ReleaseNotes.html) | 
| Oracle | RDS MariaDB 10\.4 | Added RDS MariaDB 10\.4 support for all online transactional processing \(OLTP\) vendors\. | 
| Oracle | PostgreSQL/Aurora PostgreSQL |  Added support for DBMS\_UTILITY\.GET\_TIME\. Added the following emulations: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_ReleaseNotes.html) | 
|  Oracle |  MariaDB/MySQL/Aurora MySQL/Microsoft SQL Server Mode=Oracle/PostgreSQL/Aurora PostgreSQL/RDS Oracle |  Added sharing clause support for TABLE\(DATA,EXTENDED DATA\), VIEW\(DATA,EXTENDED DATA\), and SEQUENCE\(DATA\) | 
| Oracle |  PostgreSQL/Aurora PostgreSQL/Oracle RDS  |  The DEFAULT definition of a column can be extended to have the DEFAULT being applied for explicit NULL insertion\. The DEFAULT clause has a new ON NULL clause\. This new clause instructs the database to assign a specified default column value when an INSERT statement attempts to assign a value that evaluates to NULL\.  | 
| Oracle | MariaDB/MariaDB \(SQL MODE=ORACLE\) |  Added support for "Identity Columns," which automatically increment at the time of insertion\. | 
| All | All | Upgrade to Amazon Corretto JDK 11 from JDK 8\. For more information, including download links, see [What is Amazon Corretto 11?](https://docs.aws.amazon.com/corretto/latest/corretto-11-ug/what-is-corretto-11.html) in the *Amazon Corretto 11 User Guide\.* | 
| All | All | Added information to the assessment report about possible inconsistencies in the user's database\. | 
| Oracle | MariaDB 10\.2/MariaDB 10\.3/MySQL/Aurora MySQL/PostgreSQL/Aurora PostgreSQL | The `DEFAULT` clause has a new `ON NULL` clause, which instructs the database to assign a specified default column value when an INSERT statement attempts to assign a value that evaluates to `NULL`\. | 
| Oracle | Oracle RDS/MySQL/Aurora MySQL/PostgreSQL/Aurora PostgreSQL | Added support for `IDENTITY` columns\. | 
|  Oracle | MySQL 8\.x | Added support for CHECK constraint\. | 
| Oracle | PostgreSQL/Aurora PostgreSQL |  Implemented checking ANYDATA IS NULL/IS NOT NULL using extension pack routine\. Implemented the emulation of the VALUE function used in a query based on the TABLE function of XMLSequence\. Added DBMS\_LOB support for the following built\-in routines: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_ReleaseNotes.html)  | 
| All | SQL Server |  SQL Server 2019: Added support for new index attribute OPTIMIZE\_FOR\_SEQUENTIAL\_KEY\. SQL Server 2017: Added support for Graph Databases Node and Edge table types\. SQL Server 2016: Added support for TEMPORAL TABLES\.  | 
| All | All | Implemented ability to override physical partitions with virtual partitions\. Data warehouse extractors extract data according to created virtual partitions\. | 
| Oracle | Amazon Redshift |  Implemented conversion of cursor attributes in nested blocks\. Amazon Redshift doesn't support collections\. Related variables are converted as VARCHAR\. All collection operations other than assigning one variable to another are rejected, including initiation and collection elements access\. Implemented Amazon Redshift distribution style = AUTO\.  | 
| Oracle | PostgreSQL/Aurora PostgreSQL |  If a nonreserved word in Oracle is reserved in PostgreSQL, then the following is true: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_ReleaseNotes.html) Implemented the ability to use functions as input to LTRIM, RTRIM, and TRIM functions\. SELECT DISTINCT, ORDER BY expressions must appear in select list\. For cursor parameters that follow after a parameter with a DEFAULT value, AWS SCT adds DEFAULT IS NULL clause Source OUT cursor parameters are converted to IN cursor parameters\. Reimplemented package variable by adding the "Package variables logic implementation" option under "Conversion settings"\. Available settings are: "session variables" and "plv8 global objects"\. The default is "session variables"\. Implemented AUTONOMOUS\_TRANSACTION pragma support with dblink and pg\_background\.  | 
| Oracle | All | Implemented view SYS\_%\_TAB\_COMMENTS\. | 
| Oracle | PostgreSQL | Variable inputs to filters aren't supported in PostgreSQL\. When converting from Oracle to PostgreSQL, if a variable filter is encountered an exception is now reported\. | 
| Oracle | Amazon Redshift |  Implemented stored code FOR\.\.LOOP Cursor conversion improvements\. Implemented stored code invocation of function/procedures with default parameters\. Implemented stored code ability to UPDATE with alias without WHERE clause\. Implemented stored code functions preform additional cases with SELECT FROM dual\. Implemented stored code Table%ROWTYPE parameters and package variables\. Implemented stored code used of JAVA and external procedures\. Implemented standard Oracle package in stored code\.  | 

### Release 1\.0\.640 Microsoft SQL Server changes<a name="640.mssql"></a>

The following table lists build 1\.0\.640 changes in which Microsoft SQL Server is the source engine\.


| Source | Target | What's new, enhanced, or fixed | 
| --- | --- | --- | 
| Microsoft Azure/ Microsoft SQL Server | PostgreSQL/Aurora PostgreSQL/MySQL/Aurora MySQL |  Added support for COLUMN STORE indexes\.  | 
| Microsoft SQL Server | RDS MariaDB 10\.4 | Added RDS MariaDB 10\.4 support for all online transactional processing \(OLTP\) vendors\. | 
| Azure/SQL Server |  MariaDB/MySQL/Aurora MySQL/PostgreSQL/Aurora PostgreSQL  | Added support for the OPTIMIZE\_FOR\_SEQUENTIAL\_KEY index attribute\. | 
| Azure/SQL Server |  MySQL/Aurora MySQL/PostgreSQL/Aurora PostgreSQL  | Added support for Databases Node and Edge table types\. | 
| Azure/SQL Server |  MariaDB/MySQL/Aurora MySQL/PostgreSQL/Aurora PostgreSQL  | Added support for TEMPORAL TABLES\. | 
| All | All | Upgrade to Amazon Corretto JDK 11 from JDK 8\. For more information, including download links, see [What is Amazon Corretto 11?](https://docs.aws.amazon.com/corretto/latest/corretto-11-ug/what-is-corretto-11.html) in the *Amazon Corretto 11 User Guide\.* | 
| All | All | Added information to the assessment report about possible inconsistencies in the user's database\. | 
| Azure/SQL Server | MySQL/Aurora MySQL/PostgreSQL/Aurora PostgreSQL/MariaDB | Added support for DML processing for SQL Server Graph Architecture\. | 
| SQL Server | Aurora PostgreSQL | Added option to convert parameters without the `par_` prefix\. | 
|  Azure/SQL Server | MySQL 8\.x | Added support for CHECK constraint\. | 
| All | SQL Server |  SQL Server 2019: Added support for new index attribute OPTIMIZE\_FOR\_SEQUENTIAL\_KEY\. SQL Server 2017: Added support for Graph Databases Node and Edge table types\. SQL Server 2016: Added support for TEMPORAL TABLES\.  | 
| All | All | Implemented ability to override physical partitions with virtual partitions\. Data warehouse extractors extract data according to created virtual partitions\. | 
| SQL Server | AWS Glue \(Python shell\) |  Conversion improvements, including: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_ReleaseNotes.html)  | 
| Azure/SQL Server | PostgreSQL/Aurora PostgreSQL | Implemented making $TMP procedures optional\. | 
| SQL Server | MySQL/Aurora MySQL |  Extended arithmetic operations with dates\. Construction emulation 'TOP \(expression\) WITH TIES\. After calling procedures with the generated refcursor out, the refcursor now closes\. Setting a GLOBAL isolation level isn't supported in Aurora MySQL\. Only the session scope can be changed\. The default behavior of transactions is to use REPEATABLE READ and consistent reads\. Applications designed for use with READ COMMITTED may need to be modified\. Alternatively, they can explicitly change the default to READ COMMITTED\.  | 
| SQL Server | AWS Glue \(Python shell\) |  SQL Server statements produce a complete result set, but there are times when the results are best processed one row at a time\. Opening a cursor on a result set allows processing the result set one row at a time\. You can assign a cursor to a variable or parameter with a cursor data type\. Implemented enclosing a series of Transact\-SQL statements for stored code so that a group of Transact\-SQL statements can be run even though Python doesn't support SQL Server's BEGIN and END as control\-of\-flow\. SQL Server LABEL and GOTO statements aren't supported by AWS Glue\. If AWS SCT encounters a label in the code, it is skipped\. If AWS SCT encounters a GOTO statement, it is commented\.  | 
| SQL Server | Amazon Redshift |  Implemented conditional processing of Transact\-SQL statements for stored code by implementing the IF \.\.\. ELSE control\. Implemented enclosing a series of Transact\-SQL statements for stored code so that a group of Transact\-SQL statements can be run as a block\. Supports nested BEGIN \.\.\. END blocks\. Implemented SET and SELECT in stored code\. Implemented CREATE INDEX in Amazon Redshift \(which doesn't support indexes\) by creating a user\-specified sort key on the tables\.  | 

### Release 1\.0\.640 MySQL Changes<a name="640.mysql"></a>

The following table lists build 1\.0\.640 changes in which MySQL is the source engine\.


| Source | Target | What's new, enhanced, or fixed | 
| --- | --- | --- | 
|  MySQL  | PostgreSQL 12\.x | Added support for generated columns\. | 
| All | All | Upgrade to Amazon Corretto JDK 11 from JDK 8\. For more information, including download links, see [What is Amazon Corretto 11?](https://docs.aws.amazon.com/corretto/latest/corretto-11-ug/what-is-corretto-11.html) in the *Amazon Corretto 11 User Guide\.* | 
| All | All | Added information to the assessment report about possible inconsistencies in the user's database\. | 
| MySQL |  PostgreSQL/Aurora PostgreSQL 11\. |  Added support for the following: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_ReleaseNotes.html)  | 
| All | SQL Server |  SQL Server 2019: Added support for new index attribute OPTIMIZE\_FOR\_SEQUENTIAL\_KEY\. SQL Server 2017: Added support for Graph Databases Node and Edge table types\. SQL Server 2016: Added support for TEMPORAL TABLES\.  | 
| All | All | Implemented ability to override physical partitions with virtual partitions\. Data warehouse extractors extract data according to created virtual partitions\. | 

### Release 1\.0\.640 PostgreSQL changes<a name="640.postgresql"></a>

The following table lists build 1\.0\.640 changes in which PostgreSQL is the source engine\.


| Source | Target | What's new, enhanced, or fixed | 
| --- | --- | --- | 
| PostgreSQL | MySQL 8\.x |  MySQL now supports creation of functional index key parts that index expression values rather than column values\. Functional key parts enable indexing of values, such as JSON values, that can't be indexed otherwise\. MySQL now supports Now CTE and Recursive CTE\.  | 
| All | All | Upgrade to Amazon Corretto JDK 11 from JDK 8\. For more information, including download links, see [What is Amazon Corretto 11?](https://docs.aws.amazon.com/corretto/latest/corretto-11-ug/what-is-corretto-11.html) in the *Amazon Corretto 11 User Guide\.* | 
| All | All | Added information to the assessment report about possible inconsistencies in the user's database\. | 
| PostgreSQL 11\.x |  PostgreSQL/Aurora PostgreSQL 11\. |  Added support for the following: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_ReleaseNotes.html)  | 
| PostgreSQL | MySQL 8\.x |  Added MySQL support for descending indexes\. DESC in an index definition is no longer ignored, but causes storage of key values in descending order\. Added MySQL support the use of expressions as default values in data type specifications, including expressions as default values for the BLOB, TEXT, GEOMETRY, and JSON data types\. Several existing aggregate functions can now be used as window functions: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_ReleaseNotes.html) MySQL supports window functions that, for each row from a query, perform a calculation using rows related to that row\. [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_ReleaseNotes.html) | 
|  PostgreSQL | MySQL 8\.x | Added support for CHECK constraint\. | 
| All | SQL Server |  SQL Server 2019: Added support for new index attribute OPTIMIZE\_FOR\_SEQUENTIAL\_KEY\. SQL Server 2017: Added support for Graph Databases Node and Edge table types\. SQL Server 2016: Added support for TEMPORAL TABLES\.  | 
| All | All | Implemented ability to override physical partitions with virtual partitions\. Data warehouse extractors extract data according to created virtual partitions\. | 
|  PostgreSQL/Aurora PostgreSQL  | All |  Added system view sysindexes emulation\. If there is a SELECT statement in a procedure without specifying INTO, the parameter INOUT p\_refcur of type refcursor is created for a procedure on the target\. | 

### Release 1\.0\.640 Db2 LUW changes<a name="640.db2luw"></a>

The following table lists build 1\.0\.640 changes in which DB2 LUW is the source engine\.


| Source | Target | What's new, enhanced, or fixed | 
| --- | --- | --- | 
| DB2 LUW | RDS MariaDB 10\.4 | Added RDS MariaDB 10\.4 support for all online transactional processing \(OLTP\) vendors\. | 
| All | All | Upgrade to Amazon Corretto JDK 11 from JDK 8\. For more information, including download links, see [What is Amazon Corretto 11?](https://docs.aws.amazon.com/corretto/latest/corretto-11-ug/what-is-corretto-11.html) in the *Amazon Corretto 11 User Guide\.* | 
| All | All | Added information to the assessment report about possible inconsistencies in the user's database\. | 
| DB2 LUW | MySQL 8\.0\.17 | Added CHECK constraint support\. | 
| All | SQL Server |  SQL Server 2019: Added support for new index attribute OPTIMIZE\_FOR\_SEQUENTIAL\_KEY\. SQL Server 2017: Added support for Graph Databases Node and Edge table types\. SQL Server 2016: Added support for TEMPORAL TABLES\.  | 
| All | All | Implemented ability to override physical partitions with virtual partitions\. Data warehouse extractors extract data according to created virtual partitions\. | 

### Release 1\.0\.640 Teradata changes<a name="640.Teradata"></a>

The following table lists build 1\.0\.640 changes Teradata source engines\.


| Source | Target | What's new, enhanced, or fixed | 
| --- | --- | --- | 
| Teradata | Amazon Redshift |  Added support for the MERGE and QUALIFY statements\. Removed LOCKING ROWS FOR ACCESS clause from Teradata statements\. Added support for CAST function\.  | 
| All | All | Upgrade to Amazon Corretto JDK 11 from JDK 8\. For more information, including download links, see [What is Amazon Corretto 11?](https://docs.aws.amazon.com/corretto/latest/corretto-11-ug/what-is-corretto-11.html) in the *Amazon Corretto 11 User Guide\.* | 
| Teradata | Teradata | Implemented improvements in REGEXP\_INSTR\(\) and REGEXP\_SUBSTR\(\)\. | 
| All | All | Added information to the assessment report about possible inconsistencies in the user's database\. | 
| All | SQL Server |  SQL Server 2019: Added support for new index attribute OPTIMIZE\_FOR\_SEQUENTIAL\_KEY\. SQL Server 2017: Added support for Graph Databases Node and Edge table types\. SQL Server 2016: Added support for TEMPORAL TABLES\.  | 
| Teradata | All | Added support for REGEXP\_INSTR\(\) and REGEXP\_SUBSTR\(\)\. | 
| All | All | Implemented ability to override physical partitions with virtual partitions\. Data warehouse extractors extract data according to created virtual partitions\. | 
| Teradata | Amazon Redshift |  Implemented the ability to save SQL of the source tree into single file or multiple files by stage using the settings in Project Settings, Save as SQL and Apply, Dropdown list: Single file/Multiple files\. Implemented improvements in views and procedures conversions\.  | 
| Teradata | All | Added support for Teradata version 16\.20 | 

### Release 1\.0\.640 changes for other engines<a name="640.other"></a>

The following table lists build 1\.0\.640 changes for other source engines\.


| Source | Target | What's new, enhanced, or fixed | 
| --- | --- | --- | 
| Sybase | RDS MariaDB 10\.4 | Added RDS MariaDB 10\.4 support for all online transactional processing \(OLTP\) vendors\. | 
| SAP ASE | MariaDB |  Implemented the following: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_ReleaseNotes.html)  | 
|  SAP ASE  | PostgreSQL 12\.x | Added support for generated columns\. | 
| All | All | Upgrade to Amazon Corretto JDK 11 from JDK 8\. For more information, including download links, see [What is Amazon Corretto 11?](https://docs.aws.amazon.com/corretto/latest/corretto-11-ug/what-is-corretto-11.html) in the *Amazon Corretto 11 User Guide\.* | 
| All | All | Added information to the assessment report about possible inconsistencies in the user's database\. | 
| SAP ASE | MySQL 8\.0\.17 | Added CHECK constraint support\. | 
| All | SQL Server |  SQL Server 2019: Added support for new index attribute OPTIMIZE\_FOR\_SEQUENTIAL\_KEY\. SQL Server 2017: Added support for Graph Databases Node and Edge table types\. SQL Server 2016: Added support for TEMPORAL TABLES\.  | 
| Vertica | Amazon Redshift | Added support for distribution style = AUTO\. | 
| All | All | Implemented ability to override physical partitions with virtual partitions\. Data warehouse extractors extract data according to created virtual partitions\. | 
| Amazon Redshift | Amazon Redshift | Unsupported built\-in functions in DML statements are replaced with NULL as a placeholder\. | 
| Sybase | PostgreSQL | Added support for native functions\. | 
| SAP ASE | MySQL/Aurora MySQL |  The default isolation level for Aurora MySQL is REPEATABLE READ\. Setting a GLOBAL isolation level isn't supported in Aurora MySQL\. Only session scope can be changed\. The default behavior of transactions is to use REPEATABLE READ and consistent reads\. Applications designed to run with READ COMMITTED may need to be modified\. Or you can explicitly change the default to READ COMMITTED\.  | 
| SAP ASE | PostgreSQL | Added support for the CONVERT function\(optimistic\) without the extension pack\. | 
|  SAP ASE  | All |  Added system view sysindexes emulation\. If there is a SELECT statement in a procedure without specifying INTO, the parameter INOUT p\_refcur of type refcursor is created for a procedure on the target\. | 
| Greenplum | Amazon Redshift | Implemented CREATE TEMPORARY TABLE as follows: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_ReleaseNotes.html) | 