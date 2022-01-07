# AWS Schema Conversion Tool User Guide

-----
*****Copyright &copy; Amazon Web Services, Inc. and/or its affiliates. All rights reserved.*****

-----
Amazon's trademarks and trade dress may not be used in 
     connection with any product or service that is not Amazon's, 
     in any manner that is likely to cause confusion among customers, 
     or in any manner that disparages or discredits Amazon. All other 
     trademarks not owned by Amazon are the property of their respective
     owners, who may or may not be affiliated with, connected to, or 
     sponsored by Amazon.

-----
## Contents
+ [What is the AWS Schema Conversion Tool?](CHAP_Welcome.md)
+ [Installing, verifying, and updating AWS SCT](CHAP_Installing.md)
+ [Using the AWS SCT user interface](CHAP_UserInterface.md)
+ [Getting started with AWS SCT](CHAP_GettingStarted.md)
+ [Sources for AWS SCT](CHAP_Source.md)
   + [Encrypting Amazon RDS and Amazon Aurora connections in AWS SCT](CHAP_Source.Encrypt.RDS.md)
   + [Using Apache Cassandra as a source for AWS SCT](CHAP_Source.Cassandra.md)
   + [Using Azure SQL Database as a source for AWS SCT](CHAP_Source.AzureSQL.md)
   + [Using IBM Db2 LUW as a source for AWS SCT](CHAP_Source.DB2LUW.md)
      + [Converting DB2 LUW to Amazon RDS for PostgreSQL or Amazon Aurora PostgreSQL-Compatible Edition](CHAP_Source.DB2LUW.ToPostgreSQL.md)
   + [Using MySQL as a source for AWS SCT](CHAP_Source.MySQL.md)
   + [Using Oracle Database as a source for AWS SCT](CHAP_Source.Oracle.md)
      + [Converting Oracle to RDS for PostgreSQL or Amazon Aurora PostgreSQL](CHAP_Source.Oracle.ToPostgreSQL.md)
      + [Converting Oracle to RDS for MySQL or Aurora MySQL](CHAP_Source.Oracle.ToMySQL.md)
      + [Converting Oracle to Amazon RDS for Oracle](CHAP_Source.Oracle.ToRDSOracle.md)
   + [Using PostgreSQL as a source for AWS SCT](CHAP_Source.PostgreSQL.md)
   + [Using Microsoft SQL Server as a source for AWS SCT](CHAP_Source.SQLServer.md)
      + [Converting SQL Server to MySQL](CHAP_Source.SQLServer.ToMySQL.md)
      + [Converting SQL Server to PostgreSQL](CHAP_Source.SQLServer.ToPostgreSQL.md)
      + [Converting SQL Server to Amazon RDS for SQL Server](CHAP_Source.SQLServer.ToRDSSQLServer.md)
   + [Using SAP ASE (Sybase ASE) as a source for AWS SCT](CHAP_Source.SAP.md)
   + [Data warehouse sources for AWS Schema Conversion Tool](CHAP_Source-Data-Warehouses.md)
      + [Using Amazon Redshift as a source for AWS SCT](CHAP_Source.Redshift.md)
      + [Using Azure Synapse Analytics as a source for AWS SCT](CHAP_Source.AzureSynapse.md)
      + [Using Greenplum Database as a source for AWS SCT](CHAP_Source.Greenplum.md)
      + [Using Netezza as a source for AWS SCT](CHAP_Source.Netezza.md)
      + [Using Oracle Data Warehouse as a source for AWS SCT](CHAP_Source.OracleDW.md)
      + [Using Snowflake as a source for AWS SCT](CHAP_Source.Snowflake.md)
      + [Using Microsoft SQL Server Data Warehouse as a source for AWS SCT](CHAP_Source.SQLServerDW.md)
      + [Using Teradata as a source for AWS SCT](CHAP_Source.Teradata.md)
      + [Using Vertica as a source for AWS SCT](CHAP_Source.Vertica.md)
+ [Creating mapping rules in AWS SCT](CHAP_Mapping.md)
   + [Adding a new mapping rule](CHAP_Mapping.New.md)
   + [Editing mapping rules](CHAP_Mapping.Edit.md)
   + [Using virtual targets](CHAP_Mapping.VirtualTargets.md)
   + [Limitations to using multiple servers in a single AWS SCT project](CHAP_Mapping.Limitations.md)
+ [Creating conversion reports](CHAP-Reports.md)
   + [Creating migration assessment reports with AWS SCT](CHAP_AssessmentReport.md)
      + [Creating a database migration assessment report](CHAP_AssessmentReport.Create.md)
      + [Viewing the assessment report](CHAP_AssessmentReport.View.md)
         + [Assessment report summary](CHAP_AssessmentReport.Summary.md)
         + [Assessment report action items](CHAP_AssessmentReport.ActionItems.md)
         + [Assessment report warning message](CHAP_AssessmentReport.WarningMessage.md)
      + [Saving the assessment report](CHAP_AssessmentReport.Save.md)
      + [Using the multiserver assessor to create an aggregate report](CHAP_AssessmentReport.Multiserver.md)
+ [Converting database schemas using the AWS SCT](CHAP_Converting.md)
   + [Comparing database schemas](CHAP_Converting.SchemaCompare.md)
   + [Finding related transformed objects](CHAP_Converting.RelatedObjects.md)
+ [Converting data warehouse schemas to Amazon Redshift using the AWS SCT](CHAP_Converting.DW.md)
   + [Optimizing Amazon Redshift by using the AWS SCT](CHAP_Converting.DW.RedshiftOpt.md)
+ [Converting ETL processes to AWS Glue](CHAP-converting-aws-glue.md)
   + [Converting using AWS Glue in the AWS SCT](CHAP-converting-aws-glue-ui-process.md)
   + [Converting using the Python API for AWS Glue](CHAP-converting-aws-glue-api-process.md)
   + [Converting SSIS to AWS Glue using AWS SCT](CHAP-converting-aws-glue-ssis.md)
+ [Using AWS SCT with AWS DMS](CHAP_DMSIntegration.md)
+ [Using data extraction agents](agents.md)
   + [Migrating data from an on-premises data warehouse to Amazon Redshift](agents.dw.md)
   + [Migrating data from Apache Cassandra to Amazon DynamoDB](agents.cassandra.md)
+ [Converting application SQL using the AWS SCT](CHAP_Converting.App.md)
+ [Using the AWS SCT extension pack](CHAP_ExtensionPack.md)
+ [Best practices for the AWS SCT](CHAP_BestPractices.md)
+ [Troubleshooting issues with the AWS SCT](CHAP_Troubleshooting.md)
+ [AWS SCT Reference](CHAP_Reference.md)
+ [Release notes for AWS SCT](CHAP_ReleaseNotes.md)
+ [Document history](WhatsNew.md)