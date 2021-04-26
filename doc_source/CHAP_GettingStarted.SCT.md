# Use AWS SCT to Migrate the Source Schema to the Target Database<a name="CHAP_GettingStarted.SCT"></a>

In this section, you use the AWS Schema Conversion Tool to migrate the source schema to the target database\.

1. Install the AWS Schema Conversion Tool\. See [ Installing, verifying, and updating the AWS SCT](https://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_Installing.html#CHAP_Installing.Procedure)\. When you install JDBC drivers for MySQL and PostgreSQL, note where the drivers get installed, in case the tool prompts you for their locations\.

1. Open the AWS Schema Conversion Tool\. Choose **File**, **New Project\.\.\.**\.

1. In the **New project** window, set the following values:
   + **Project name**: **DMSProject**
   + Leave **Location** as it is and **Transactional database \(OLTP\)** selected\.
   + **Source engine**: **MySQL**

   Choose **OK**\.

1. In the **Connect to MySQL** page, set the following values:
   + **Server name**: Enter the endpoint for the MySQL database you recorded previously\.
   + **Server port**: **3306**
   + **User name**: **admin**
   + **Password**: **changeit**

1. Choose **Connect to Amazon RDS for PostgreSQL**\. 

1. In the **Connect** page, set the following values:
   + **Server name**: Enter the endpoint for the PostgreSQL database you recorded previously\.
   + **Server port**: **5432**
   + **username**: **postgres**
   + **Password**: **changeit**

1. In the **MySQL** window on the left, right click on **dms\_sample** under **Schemas** and choose **Convert schema**\. Confirm the action\.

   After the tool converts the schema, the **dms\_sample** schema is visible in the **Amazon RDS for PostgreSQL** panel on the right\.

1. In the **Amazon RDS for PostgreSQL** window on the right, right\-click on **dms\_sample** under ***Schemas*** and choose **Apply to database**\. Confirm the action\.

To verify that the schema migration worked, do the following:

1. Connect to your Amazon EC2 client\.

1. Start the PSQL client with the following command\. Specify your PostgreSQL database endpoint, and provide the database password when prompted:

   ```
   psql \
      --host=dms-postgresql.abcdefg12345.us-west-2.rds.amazonaws.com \
      --port=5432 \
      --username=postgres \
      --password \
      --dbname=dms_sample
   ```

1. Query one of the \(empty\) tables to verify that the SCT applied the schema correctly:

   ```
   dms_sample=> SELECT * from dms_sample.player;
    id | sport_team_id | last_name | first_name | full_name
   ----+---------------+-----------+------------+-----------
   (0 rows)
   ```