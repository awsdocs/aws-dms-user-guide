# Converting Oracle to RDS for PostgreSQL or Amazon Aurora PostgreSQL<a name="CHAP_Source.Oracle.ToPostgreSQL"></a>

When you convert an Oracle to RDS for PostgreSQL or Amazon Aurora PostgreSQL, be aware of the following\.

**Topics**
+ [Converting Oracle sequences](#CHAP_Source.Oracle.ToPostgreSQL.ConvertSequences)
+ [Converting Oracle ROWID](#CHAP_Source.Oracle.ToPostgreSQL.ConvertRowID)
+ [Converting Oracle dynamic SQL](#CHAP_Source.Oracle.ToPostgreSQL.DynamicSQL)
+ [Converting Oracle partitions](#CHAP_Source.Oracle.ToPostgreSQL.PG10Partitioning)

When converting Oracle system objects to PostgreSQL, AWS SCT performs conversions as shown in the following table\.


| Oracle system object | Description | Converted PostgreSQL object | 
| --- | --- | --- | 
| V$VERSION  | Displays version numbers of core library components in the Oracle Database | aws\_oracle\_ext\.v$version | 
| V$INSTANCE | A view that shows the state of the current instance\. | aws\_oracle\_ext\.v$instance | 

You can use AWS SCT to convert Oracle SQL\*Plus files to psql, which is a terminal\-based front\-end to PostgreSQL\. For more information, see [Converting application SQL using the AWS SCT](CHAP_Converting.App.md)\.

## Converting Oracle sequences<a name="CHAP_Source.Oracle.ToPostgreSQL.ConvertSequences"></a>

AWS SCT converts sequences from Oracle to PostgreSQL\. If you use sequences to maintain integrity constraints, make sure that new values of a migrated sequence don't overlap the existing values\.

**To populate converted sequences with the last value from the source database**

1. Open your AWS SCT project with Oracle as the source\.

1. Choose **Settings**, and then choose **Conversion settings**\. 

1. From the upper list, choose **Oracle**, and then choose **Oracle – PostgreSQL**\. AWS SCT displays all available settings for Oracle to PostgreSQL conversion\. 

1. Choose **Populate converted sequences with the last value generated on the source side**\.

1. Choose **OK** to save the settings and close the **Conversion settings** dialog box\. 

## Converting Oracle ROWID<a name="CHAP_Source.Oracle.ToPostgreSQL.ConvertRowID"></a>

 In an Oracle database, the ROWID pseudocolumn contains the address of the table row\. The ROWID pseudocolumn is unique to Oracle, so AWS SCT converts the ROWID pseudocolumn to a data column on PostgreSQL\. By using this conversion, you can keep the ROWID information\. 

When converting the ROWID pseudocolumn, AWS SCT can create a data column with the `bigint` data type\. If no primary key exists, AWS SCT sets the ROWID column as the primary key\. If a primary key exists, AWS SCT sets the ROWID column with a unique constraint\.

If your source database code includes operations with ROWID, which you can't run using a numeric data type, AWS SCT can create a data column with the `character varying` data type\.

**To create a data column for Oracle ROWID for a project**

1. Open your AWS SCT project with Oracle as the source\.

1. Choose **Settings**, and then choose **Conversion settings**\. 

1. From the upper list, choose **Oracle**, and then choose **Oracle – PostgreSQL**\. AWS SCT displays all available settings for Oracle to PostgreSQL conversion\. 

1. For **Generate row ID**, do one of the following: 
   + Choose **Generate as identity** to create a numeric data column\.
   + Choose **Generate as character domain type** to create a character data column\.

1. Choose **OK** to save the settings and close the **Conversion settings** dialog box\. 

## Converting Oracle dynamic SQL<a name="CHAP_Source.Oracle.ToPostgreSQL.DynamicSQL"></a>

 Oracle provides two ways to implement dynamic SQL: using an EXECUTE IMMEDIATE statement or calling procedures in the DBMS\_SQL package\. If your source Oracle database includes objects with dynamic SQL, use AWS SCT to convert Oracle dynamic SQL statements to PostgreSQL\.

**To convert Oracle dynamic SQL to PostgreSQL**

1. Open your AWS SCT project with Oracle as the source\.

1. Choose a database object that uses dynamic SQL in the Oracle source tree view\.

1. Open the context \(right\-click\) menu for the object, choose **Convert schema**, and agree to replace the objects if they exist\. The following screenshot shows the converted procedure below the Oracle procedure with dynamic SQL\.  
![\[Dynamic SQL conversion\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/images/dynamicsql1.png)

## Converting Oracle partitions<a name="CHAP_Source.Oracle.ToPostgreSQL.PG10Partitioning"></a>

AWS SCT currently supports the following partitioning methods: 
+ Range
+ List
+ Multicolumn range
+ Hash
+ Composite \(list\-list, range\-list, list\-range, list\-hash, range\-hash, hash\-hash\)