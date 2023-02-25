# Migrating your source schema to your target database using AWS SCT<a name="CHAP_GettingStarted.SCT"></a>

In this section, you use the AWS Schema Conversion Tool to migrate your source schema to your target database\. Alternatively, you can use DMS Schema Conversion to convert your source database schemas\. For more information, see [Getting started with DMS Schema Conversion](getting-started.md)\.

**To migrate your source schema to your target database with AWS SCT**

1. Install the AWS Schema Conversion Tool\. For more information, see [ Installing, verifying, and updating the AWS SCT](https://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_Installing.html#CHAP_Installing.Procedure) in the *AWS Schema Conversion Tool User Guide*\. 

   When you download JDBC drivers for MySQL and PostgreSQL, note where you save the drivers, in case the tool prompts you for their locations\.

1. Open the AWS Schema Conversion Tool\. Choose **File**, then choose **New project**\.

1. In the **New project** window, set the following values:
   + Set **Project name** to **DMSProject**\.
   + Keep **Location** as it is to store your AWS SCT project in the default folder\.

   Choose **OK**\.

1. Choose **Add source** to add a source MySQL database to your project, then choose **MySQL**, and choose **Next**\.

1. In the **Add source** page, set the following values:
   + **Connection name**: **source**
   + **Server name**: Enter the endpoint for the MySQL database that you noted previously\.
   + **Server port**: **3306**
   + **User name**: **admin**
   + **Password**: **changeit**

1. Choose **Add target** to add a target Amazon RDS for PostgreSQL database to your project, then choose **Amazon RDS for PostgreSQL**\. Choose **Next**\.

1. In the **Add target** page, set the following values:
   + **Connection name**: **target**
   + **Server name**: Enter the endpoint for the PostgreSQL database that you noted previously\.
   + **Server port**: **5432**
   + **Database**: Enter the name of your PostgreSQL database\.
   + **User name**: **postgres**
   + **Password**: **changeit**

1. In the left pane, choose **dms\_sample** under **Schemas**\. In the right pane, choose your target Amazon RDS for PostgreSQL database\. Choose **Create mapping**\. You can add multiple mapping rules to a single AWS SCT project\. For more information about mapping rules, see [Creating mapping rules](https://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_Mapping.html)\. 

1. Choose **Main view**\.

1. In the left pane, choose **dms\_sample** under **Schemas**\. Open the context \(right\-click\) menu and choose **Convert schema**\. Confirm the action\.

   After the tool converts the schema, the **dms\_sample** schema appears in the right pane\.

1. In the right pane, under **Schemas**, open the context \(right\-click\) menu for **dms\_sample** and choose **Apply to database**\. Confirm the action\.

Verify that the schema migration completed\. Perform the following steps\.

**To check your schema migration**

1. Connect to your Amazon EC2 client\.

1. Start the PSQL client with the following command\. Specify your PostgreSQL database endpoint, and provide the database password when prompted\.

   ```
   psql \
      --host=dms-postgresql.abcdefg12345.us-west-2.rds.amazonaws.com \
      --port=5432 \
      --username=postgres \
      --password \
      --dbname=dms_sample
   ```

1. Query one of the \(empty\) tables to verify that AWS SCT applied the schema correctly,

   ```
   dms_sample=> SELECT * from dms_sample.player;
    id | sport_team_id | last_name | first_name | full_name
   ----+---------------+-----------+------------+-----------
   (0 rows)
   ```