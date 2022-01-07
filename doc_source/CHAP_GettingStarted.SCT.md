# Migrating your source schema to your target database using AWS SCT<a name="CHAP_GettingStarted.SCT"></a>

In this section, you use the AWS Schema Conversion Tool to migrate your source schema to your target database\.

**To migrate your source schema to your target database**

1. Install the AWS Schema Conversion Tool\. For more information, see [ Installing, verifying, and updating the AWS SCT](https://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_Installing.html#CHAP_Installing.Procedure) in the *AWS Schema Conversion Tool User Guide*\. 

   When you install JDBC drivers for MySQL and PostgreSQL, note where the drivers get installed, in case the tool prompts you for their locations\.

1. Open the AWS Schema Conversion Tool\. Choose **File**, **New Project**\.

1. In the **New project** window, set the following values:
   + Set **Project name** to **DMSProject**\.
   + Leave **Location** as it is and **Transactional database \(OLTP\)** selected\.
   + Set **Source engine** to **MySQL**\.

   Choose **OK**\.

1. In the **Connect to MySQL** page, set the following values:
   + **Server name**: Enter the endpoint for the MySQL database that you noted previously\.
   + **Server port**: **3306**
   + **User name**: **admin**
   + **Password**: **changeit**

1. Choose **Connect to Amazon RDS for PostgreSQL**\. 

1. On the **Connect** page, set the following values:
   + **Server name**: Enter the endpoint for the PostgreSQL database that you noted previously\.
   + **Server port**: **5432**
   + **User name**: **postgres**
   + **Password**: **changeit**

1. In the **MySQL** window at left, under **Schemas**, open the context \(right\-click\) menu for **dms\_sample** and choose **Convert schema**\. Confirm the action\.

   After the tool converts the schema, the **dms\_sample** schema appears in the **Amazon RDS for PostgreSQL** panel on the right\.

1. In the **Amazon RDS for PostgreSQL** window at right, under **Schemas**, open the context \(right\-click\) menu for **dms\_sample** and choose **Apply to database**\. Confirm the action\.

Verify that the schema migration worked by using the following procedure\.

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