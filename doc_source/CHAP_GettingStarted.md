# Getting Started with AWS Database Migration Service<a name="CHAP_GettingStarted"></a>

AWS Database Migration Service \(AWS DMS\) helps you migrate databases to AWS easily and securely\. You can migrate your data to and from most widely used commercial and open\-source databases, such as Oracle, MySQL, and PostgreSQL\. The service supports homogeneous migrations such as Oracle to Oracle, and also heterogeneous migrations between different database platforms, such as Oracle to PostgreSQL or MySQL to Oracle\. 

For information on the cost of database migration using AWS Database Migration Service, see the [AWS Database Migration Service pricing page](http://aws.amazon.com/dms/pricing/)\.

**Topics**
+ [Start a Database Migration with AWS Database Migration Service](#CHAP_GettingStarted.Dashboard)
+ [Step 1: Welcome](#CHAP_GettingStarted.Welcome)
+ [Step 2: Create a Replication Instance](#CHAP_GettingStarted.ReplicationInstance)
+ [Step 3: Specify Source and Target Endpoints](#CHAP_GettingStarted.Endpoints)
+ [Step 4: Create a Task](#CHAP_GettingStarted.Tasks)
+ [Monitor Your Task](#CHAP_GettingStarted.Conclusion)

## Start a Database Migration with AWS Database Migration Service<a name="CHAP_GettingStarted.Dashboard"></a>

You can begin a database migration in several ways\. You can select the AWS DMS console wizard that will walk you through each step of the process, or you can do each step by selecting the appropriate task from the navigation pane\. You can also use the AWS CLI; for information on using the CLI with AWS DMS, see [ AWS CLI for AWS DMS\.](http://docs.aws.amazon.com/cli/latest/reference/dms/index.html)\.

To use the wizard, select **Getting started** for from the navigation pane on the AWS DMS console\. You can use the wizard to help create your first data migration\. Following the wizard process, you allocate a replication instance that performs all the processes for the migration, specify a source and a target database, and then create a task or set of tasks to define what tables and replication processes you want to use\. AWS DMS then creates your replication instance and performs the tasks on the data being migrated\.

Alternatively, you can create each of the components of an AWS DMS database migration by selecting the items from the navigation pane\. For a database migration, you must do the following:
+ Complete the tasks outlined in [Setting Up for AWS Database Migration Service](CHAP_SettingUp.md)
+ Allocate a replication instance that performs all the processes for the migration
+ Specify a source and a target database endpoint
+ Create a task or set of tasks to define what tables and replication processes you want to use

## Step 1: Welcome<a name="CHAP_GettingStarted.Welcome"></a>

If you start your database migration using the AWS DMS console wizard, you will see the Welcome page, which explains the process of database migration using AWS DMS\.

![\[Get started with AWS DMS\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-gs-wizard1.png)

**To start a database migration from the console's Welcome page**
+ Choose **Next**\.

## Step 2: Create a Replication Instance<a name="CHAP_GettingStarted.ReplicationInstance"></a>

Your first task in migrating a database is to create a replication instance that has sufficient storage and processing power to perform the tasks you assign and migrate data from your source database to the target database\. The required size of this instance varies depending on the amount of data you need to migrate and the tasks that you need the instance to perform\. For more information about replication instances, see [Working with an AWS DMS Replication Instance](CHAP_ReplicationInstance.md)\. 

The procedure following assumes that you have chosen the AWS DMS console wizard\. Note that you can also do this step by selecting **Replication instances** from the AWS DMS console's navigation pane and then selecting **Create replication instance**\.

**To create a replication instance by using the AWS console**

1. In the navigation pane, click **Replication instances**\.

1. Select **Create Replication Instance**\. 

1. On the **Create replication instance** page, specify your replication instance information\. The following table describes the settings\.   
![\[Create a replication instance\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-gs-wizard2.png)    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_GettingStarted.html)

1. Choose the **Advanced** tab, shown following, to set values for network and encryption settings if you need them\. The following table describes the settings\.  
![\[Advanced Tab\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-gs-wizard2A.png)    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_GettingStarted.html)

1. Specify the **Maintenance** settings\. The following table describes the settings\. For more information about maintenance settings, see [AWS DMS Maintenance Window](CHAP_ReplicationInstance.md#CHAP_ReplicationInstance.MaintenanceWindow)  
![\[Maintenance Tab\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-gs-wizard2B.png)    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_GettingStarted.html)

1. Choose **Create replication instance**\.

## Step 3: Specify Source and Target Endpoints<a name="CHAP_GettingStarted.Endpoints"></a>

While your replication instance is being created, you can specify the source and target data stores\. The source and target data stores can be on an Amazon Elastic Compute Cloud \(Amazon EC2\) instance, an Amazon Relational Database Service \(Amazon RDS\) DB instance, or an on\-premises database\.

The procedure following assumes that you have chosen the AWS DMS console wizard\. Note that you can also do this step by selecting **Endpoints** from the AWS DMS console's navigation pane and then selecting **Create endpoint**\. When using the console wizard, you create both the source and target endpoints on the same page\. When not using the console wizard, you create each endpoint separately\.

**To specify source or target database endpoints using the AWS console**

1. On the **Connect source and target database endpoints** page, specify your connection information for the source or target database\. The following table describes the settings\.  
![\[Create source and target DB endpoints\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-gs-wizard3.png)    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_GettingStarted.html)

1. Choose the **Advanced** tab, shown following, to set values for connection string and encryption key if you need them\. You can test the endpoint connection by choosing **Run test**\.  
![\[Create source or target DB endpoints\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-gs-wizard3a.png)    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_GettingStarted.html)

## Step 4: Create a Task<a name="CHAP_GettingStarted.Tasks"></a>

Create a task to specify what tables to migrate, to map data using a target schema, and to create new tables on the target database\. As part of creating a task, you can choose the type of migration: to migrate existing data, migrate existing data and replicate ongoing changes, or replicate data changes only\.

Using AWS DMS, you can specify precise mapping of your data between the source and the target database\. Before you specify your mapping, make sure you review the documentation section on data type mapping for your source and your target database\.

You can choose to start a task as soon as you finish specifying information for that task on the **Create task** page, or you can start the task from the Dashboard page once you finish specifying task information\.

The procedure following assumes that you have chosen the AWS DMS console wizard and specified replication instance information and endpoints using the console wizard\. Note that you can also do this step by selecting **Tasks** from the AWS DMS console's navigation pane and then selecting **Create task**\. 

**To create a migration task**

1. On the **Create Task** page, specify the task options\. The following table describes the settings\.  
![\[Create task\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-gs-wizard4.png)    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_GettingStarted.html)

1. Choose the **Task Settings** tab, shown following, and specify values for your target table, LOB support, and to enable logging\. The task settings shown depend on the **Migration type** value you select\. For example, when you select **Migrate existing data**, the following options are shown:  
![\[Task settings\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-gs-wizard4-settings.png)    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_GettingStarted.html)

   When you select **Migrate existing data and replicate** for **Migration type**, the following options are shown:  
![\[Task settings\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-gs-wizard4a-settings.png)    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_GettingStarted.html)

1. Choose the **Table mappings** tab, shown following, to set values for schema mapping and the mapping method\. If you choose **Custom**, you can specify the target schema and table values\. For more information about table mapping, see [Using Table Mapping to Specify Task Settings](CHAP_Tasks.CustomizingTasks.TableMapping.md)\.  
![\[Table mapping\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-gs-wizard4-tablemapping.png)

1. Once you have finished with the task settings, choose **Create task**\.

## Monitor Your Task<a name="CHAP_GettingStarted.Conclusion"></a>

If you select **Start task on create** when you create a task, your task begins immediately to migrate your data when you choose **Create task**\. You can view statistics and monitoring information for your task by choosing the running task from the AWS Management Console\. The following screenshot shows the table statistics of a database migration\. For more information about monitoring, see [Monitoring AWS DMS Tasks](CHAP_Monitoring.md)

![\[Status of replication\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-monitoring3.png)