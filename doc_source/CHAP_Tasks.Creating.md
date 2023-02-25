# Creating a task<a name="CHAP_Tasks.Creating"></a>

To create an AWS DMS migration task, you do the following:
+ Create a source endpoint, a target endpoint, and a replication instance before you create a migration task\. 
+ Choose a migration method:
  + **Migrating data to the target database** – This process creates files or tables in the target database and automatically defines the metadata that is required at the target\. It also populates the tables with data from the source\. The data from the tables is loaded in parallel for improved efficiency\. This process is the **Migrate existing data** option in the AWS Management Console and is called `Full Load` in the API\.
  + **Capturing changes during migration** – This process captures changes to the source database that occur while the data is being migrated from the source to the target\. When the migration of the originally requested data has completed, the change data capture \(CDC\) process then applies the captured changes to the target database\. Changes are captured and applied as units of single committed transactions, and you can update several different target tables as a single source commit\. This approach guarantees transactional integrity in the target database\. This process is the **Migrate existing data and replicate ongoing changes** option in the console and is called `full-load-and-cdc` in the API\.
  + **Replicating only data changes on the source database** – This process reads the recovery log file of the source database management system \(DBMS\) and groups together the entries for each transaction\. In some cases, AWS DMS can't apply changes to the target within a reasonable time \(for example, if the target isn't accessible\)\. In these cases, AWS DMS buffers the changes on the replication server for as long as necessary\. It doesn't reread the source DBMS logs, which can take a large amount of time\. This process is the **Replicate data changes only** option in the AWS DMS console\. 
+ Determine how the task should handle large binary objects \(LOBs\) on the source\. For more information, see [Setting LOB support for source databases in an AWS DMS task](CHAP_Tasks.LOBSupport.md)\.
+ Specify migration task settings\. These include setting up logging, specifying what data is written to the migration control table, how errors are handled, and other settings\. For more information about task settings, see [Specifying task settings for AWS Database Migration Service tasks](CHAP_Tasks.CustomizingTasks.TaskSettings.md)\.
+ Set up table mapping to define rules to select and filter data that you are migrating\. For more information about table mapping, see [Using table mapping to specify task settings](CHAP_Tasks.CustomizingTasks.TableMapping.md)\. Before you specify your mapping, make sure that you review the documentation section on data type mapping for your source and your target database\. 
+ Enable and run premigration task assessments before you run the task\. For more information about premigration assessments, see [Enabling and working with premigration assessments for a task](CHAP_Tasks.AssessmentReport.md)\.
+ Specify any required supplemental data for the task to migrate your data\. For more information, see [Specifying supplemental data for task settings](CHAP_Tasks.TaskData.md)\.

You can choose to start a task as soon as you finish specifying information for that task on the **Create task** page\. Alternatively, you can start the task from the Dashboard page later as well\.

The following procedure assumes that you have already specified replication instance information and endpoints\. For more information about setting up endpoints, see [Creating source and target endpoints](CHAP_Endpoints.Creating.md)\.

**To create a migration task**

1. Sign in to the AWS Management Console and open the AWS DMS console at [https://console\.aws\.amazon\.com/dms/v2/](https://console.aws.amazon.com/dms/v2/)\. 

   If you are signed in as an AWS Identity and Access Management \(IAM\) user, make sure that you have the appropriate permissions to access AWS DMS\. For more information about the permissions required, see [IAM permissions needed to use AWS DMS](security-iam.md#CHAP_Security.IAMPermissions)\.

1. On the navigation pane, choose **Database migration tasks**, and then choose **Create task**\.

1. On the **Create database migration task** page, in the **Task configuration** section, specify the task options\. The following table describes the settings\.  
![\[Create task\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-gs-wizard4.png)    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Tasks.Creating.html)

1. In the **Task Settings** section, specify values for editing your task, target table preparation mode, stop task, LOB settings, validation, and logging\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Tasks.Creating.html)

1. In the **Premigration assessment** section, choose whether to run a premigration assessment\. A premigration assessment warns you of potential migration issues before starting your database migration task\. For more information, see [Enabling and working with premigration assessments](CHAP_Tasks.AssessmentReport.md)\. 

1. In the **Migration task startup configuration** section, specify whether to start the task automatically after creation\.

1. In the **Tags** section, specify any tags you need to organize your task\. You can use tags to manage your IAM roles and policies, and track your DMS costs\. For more information, see [Tagging resources](CHAP_Tagging.md)\.

1. After you have finished with the task settings, choose **Create task**\.