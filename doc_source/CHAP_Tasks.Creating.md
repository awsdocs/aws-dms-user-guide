# Creating a Task<a name="CHAP_Tasks.Creating"></a>

There are several things you must do to create an AWS DMS migration task:
+ Create a source endpoint, a target endpoint, and a replication instance before you create a migration task\. 
+ Select a migration method:
  + **Migrating Data to the Target Database** – This process creates files or tables in the target database, automatically defines the metadata that is required at the target, and populates the tables with data from the source\. The data from the tables is loaded in parallel for improved efficiency\. This process is the **Migrate existing data** option in the AWS console and is called `Full Load` in the API\.
  + **Capturing Changes During Migration** – This process captures changes to the source database that occur while the data is being migrated from the source to the target\. When the migration of the originally requested data has completed, the change data capture \(CDC\) process then applies the captured changes to the target database\. Changes are captured and applied as units of single committed transactions, and several different target tables can be updated as a single source commit\. This approach guarantees transactional integrity in the target database\. This process is the **Migrate existing data and replicate ongoing changes** option in the AWS console and is called `full-load-and-cdc` in the API\.
  + **Replicating Only Data Changes on the Source Database** – This process reads the recovery log file of the source database management system \(DBMS\) and groups together the entries for each transaction\. If AWS DMS can't apply changes to the target within a reasonable time \(for example, if the target is not accessible\), AWS DMS buffers the changes on the replication server for as long as necessary\. It doesn't reread the source DBMS logs, which can take a large amount of time\. This process is the **Replicate data changes only** option in the AWS DMS console\. 
+ Determine how the task should handle large binary objects \(LOBs\) on the source\. For more information, see [Setting LOB Support for Source Databases in the AWS DMS task](CHAP_Tasks.LOBSupport.md)\.
+ Specify migration task settings\. These include setting up logging, specifying what data is written to the migration control table, how errors are handled, and other settings\. For more information about task settings, see [Specifying Task Settings for AWS Database Migration Service Tasks](CHAP_Tasks.CustomizingTasks.TaskSettings.md)\.
+ Set up table mapping to define rules to select and filter data that you are migrating\. For more information about table mapping, see [Using Table Mapping to Specify Task Settings](CHAP_Tasks.CustomizingTasks.TableMapping.md)\. Before you specify your mapping, make sure you review the documentation section on data type mapping for your source and your target database\. 

You can choose to start a task as soon as you finish specifying information for that task on the **Create task** page, or you can start the task from the Dashboard page once you finish specifying task information\.

The procedure following assumes that you have chosen the AWS DMS console wizard and specified replication instance information and endpoints using the console wizard\. Note that you can also do this step by selecting **Tasks** from the AWS DMS console's navigation pane and then selecting **Create task**\. 

**To create a migration task**

1. On the **Create Task** page, specify the task options\. The following table describes the settings\.  
![\[Create task\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-gs-wizard4.png)    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Tasks.Creating.html)

1. Choose the **Task Settings** tab, shown following, and specify values for your target table, LOB support, and to enable logging\. The task settings shown depend on the **Migration type** value you select\. For example, when you select **Migrate existing data**, the following options are shown:  
![\[Task settings\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-gs-wizard4-settings.png)    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Tasks.Creating.html)

   When you select **Migrate existing data and replicate** for **Migration type**, the following options are shown:  
![\[Task settings\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-gs-wizard4a-settings.png)    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Tasks.Creating.html)

1. Choose the **Table mappings** tab, shown following, to set values for schema mapping and the mapping method\. If you choose **Custom**, you can specify the target schema and table values\. For more information about table mapping, see [Using Table Mapping to Specify Task Settings](CHAP_Tasks.CustomizingTasks.TableMapping.md)\.  
![\[Table mapping\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-gs-wizard4-tablemapping.png)

1. Once you have finished with the task settings, choose **Create task**\.