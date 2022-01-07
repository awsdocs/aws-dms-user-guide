# Migrating data from Apache Cassandra to Amazon DynamoDB<a name="agents.cassandra"></a>

You can use an AWS SCT data extraction agent to extract data from Apache Cassandra and migrate it to Amazon DynamoDB\. The agent runs on an Amazon EC2 instance, where it extracts data from Cassandra, writes it to the local file system, and uploads it to an Amazon S3 bucket\. You can then use AWS SCT to copy the data to DynamoDB\.

Amazon DynamoDB is a NoSQL database service\. To store data in DynamoDB, you create database tables and then upload data to those tables\. The AWS SCT extraction agent for Cassandra automates the process of creating DynamoDB tables that match their Cassandra counterparts, and then populating those DynamoDB tables with data from Cassandra\.

The process of extracting data can add considerable overhead to a Cassandra cluster\. For this reason, you don't run the extraction agent directly against your production data in Cassandra\. To avoid interfering with production applications, AWS SCT helps you create a *clone datacenter*—a standalone copy of the Cassandra data that you want to migrate to DynamoDB\. The agent can then read data from the clone and make it available to AWS SCT, without affecting your production applications\. 

When the data extraction agent runs, it reads data from the clone datacenter and writes it to an Amazon S3 bucket\. AWS SCT then reads the data from Amazon S3 and writes it to Amazon DynamoDB\.

The following diagram shows the supported scenario:

![\[Extraction agent architecture\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/images/extraction-agent-cassandra.png)

If you are new to Cassandra, be aware of the following important terminology:
+ A *node* is a single computer \(physical or virtual\) running the Cassandra software\.
+ A *server* is a logical entity composed of up to 256 nodes\.
+ A *rack* represents one or more servers\.
+ A *datacenter* is a collection of racks\.
+ A *cluster* is a collection of data centers\.

For more information, go to the [Wikipedia page](https://en.wikipedia.org/wiki/Apache_Cassandra) for Apache Cassandra\.

Use the information in the following topics to learn how to migrate data from Apache Cassandra to Amazon DynamoDB:

**Topics**
+ [Prerequisites for migrating from Cassandra to DynamoDB](#agents.cassandra.prereqs)
+ [Create a new AWS SCT project](#agents.cassandra.new-project)
+ [Create a clone datacenter](#agents.cassandra.clone-datacenter)
+ [Install, configure, and run the data extraction agent](#agents.cassandra.run-extractor)
+ [Migrate data from the clone datacenter to Amazon DynamoDB](#agents.cassandra.migrate-to-ddb)
+ [Post\-migration activities](#agents.cassandra.post-migration)

## Prerequisites for migrating from Cassandra to DynamoDB<a name="agents.cassandra.prereqs"></a>

Before you begin, you will need to perform several pre\-migration tasks, as described in this section\.

### Supported Cassandra versions<a name="agents.cassandra.prereqs.cassandra-version"></a>

AWS SCT supports the following Apache Cassandra versions:
+ 3\.11\.2
+ 3\.1\.1
+ 3\.0
+ 2\.1\.20

Other versions of Cassandra aren't supported\.

### Amazon S3 settings<a name="agents.cassandra.prereqs.s3"></a>

When the AWS SCT data extraction agent runs, it reads data from your clone datacenter and writes it to an Amazon S3 bucket\. Before you continue, you must provide the credentials to connect to your AWS account and your Amazon S3 bucket\. You store your credentials and bucket information in a profile in the global application settings, and then associate the profile with your AWS SCT project\. If necessary, choose Global Settings to create a new profile\. For more information, see [Storing AWS service profiles in the AWS SCT](CHAP_UserInterface.md#CHAP_UserInterface.Profiles)\. 

### Amazon EC2 Instance for clone datacenter<a name="agents.cassandra.prereqs.ec2"></a>

As part of the migration process, you'll need to create a clone of an existing Cassandra datacenter\. This clone will run on an Amazon EC2 instance that you provision in advance\. The instance will run a standalone Cassandra installation, for hosting your clone datacenter independently of your existing Cassandra datacenter\.

 The new Amazon EC2 instance must meet the following requirements:
+ Operating system: either Ubuntu or CentOS\.
+ Must have Java JDK 8 installed\. \(Other versions are not supported\.\)

To launch a new instance, go to the Amazon EC2 Management Console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\. 

### Security settings<a name="agents.cassandra.prereqs.keystore-and-truststore"></a>

AWS SCT communicates with the data extraction agent using Secure Sockets Layer \(SSL\)\. To enable SSL, set up a trust store and key store:

****

1. Launch AWS SCT\.

1. From the **Settings** menu, choose **Global Settings**\.

1. Choose the **Security** tab as shown following\.  
![\[The Security tab on the Global Settings dialog box\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/images/SecuritySettings.png)

1. Choose **Generate Trust and Key Store**, or choose **Select existing Trust and Key Store**\. 

   If you choose **Generate Trust and Key Store**, you then specify the name and password for the trust and key stores, and the path to the location for the generated files\. You use these files in later steps\. 

   If you choose **Select existing Trust and Key Store**, you then specify the password and file name for the trust and key stores\. You use these files in later steps\. 

1. After you have specified the trust store and key store, choose **OK** to close the **Global Settings** dialog box\. 

## Create a new AWS SCT project<a name="agents.cassandra.new-project"></a>

After you have performed the steps in [Prerequisites for migrating from Cassandra to DynamoDB](#agents.cassandra.prereqs), you're ready to create a new AWS SCT project for your migration\. Follow these steps:

1. From the **File** menu, choose **New project**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/images/cassandra-new-project.png)

   Add the following information:    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/agents.cassandra.html)

   Choose **OK** to create your AWS SCT project\.

1. From the menu bar, choose **Connect to Cassandra**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/images/cassandra-connect-to-source.png)

   Add the following information:    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/agents.cassandra.html)

1. Choose **OK**\. AWS SCT tests the connection to ensure that it can access your Cassandra cluster\.

1. From the menu bar, choose **Connect to Amazon DynamoDB**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/images/cassandra-connect-to-target.png)

   Add the following information:    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/agents.cassandra.html)

1. Choose **OK**\. AWS SCT tests the connection to ensure that it can access DynamoDB\.

## Create a clone datacenter<a name="agents.cassandra.clone-datacenter"></a>

To avoid interfering with production applications that use your Cassandra cluster, AWS SCT will create a clone datacenter and copy your production data into it\. The clone datacenter acts as a staging area, so that AWS SCT can perform further migration activities using the clone rather than your production datacenter\.

To begin the cloning process, follow this procedure:

1. In the AWS SCT window, on the left\-hand side \(source\), expand the **Datacenters** node and choose one of your existing Cassandra datacenters\.

1. From the **Actions** menu, choose **Clone Datacenter for Extract**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/images/cassandra-clone-datacenter.png)

1. Read the introductory text, and then choose **Next** to continue\.

1. In the **Clone Datacenter for Extract** window, add the following information:    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/agents.cassandra.html)

   Choose **Next** to continue\. AWS SCT connects to the Cassandra node, where it runs the `nodetool status` command\.

1. In the **Source Cluster Parameters** window, accept the default values, and choose **Next** to continue\.

1. In the **Node Parameters** window, verify the connection details for all of the nodes in the source cluster\. AWS SCT will fill in some of these details automatically; however, you must supply any missing information\.
**Note**  
Instead of entering all of the data here, you can bulk\-upload it instead\. To do this, choose **Export** to create a *\.csv* file\. You can then edit this file, adding a new line for each node in your cluster\. When you are done, choose **Upload**\. AWS SCT will read the *\.csv* file and use it to populate the **Node parameters **window\.

   Choose **Next** to continue\. AWS SCT verifies that the node configuration is valid\.

1. In the **Configure Target Datacenter** window, review the default values\. In particular, note the **Datacenter suffix** field: When AWS SCT creates your clone datacenter, it will be named similarly to the source datacenter, but with the suffix that you provide\. For example, if the source datacenter is named `my_datacenter`, then a suffix of `_tgt` would cause the clone to be named `my_datacenter_tgt`\.

1. While still in the **Configure Target Datacenter** window, choose **Add new node**:   
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/images/cassandra-add-new-node.png)

1. In the **Add New Node** window, add the information needed to connect to the Amazon EC2 instance that you created in [Amazon EC2 Instance for clone datacenter](#agents.cassandra.prereqs.ec2)\.

   When the settings are as you want them, choose **Add**\. The node appears in the list:  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/images/cassandra-list-of-nodes.png)

1. Choose **Next** to continue\. The following confirmation box appears:  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/images/cassandra-confirm-reboot.png)

   Choose **OK** to continue\. AWS SCT reboots your source datacenter, one node at a time\.

1. Review the information in the **Datacenter Synchronization** window\. If your cluster is running Cassandra version 2, then AWS SCT copies all of the data to the clone datacenter\. If your cluster is running Cassandra version 3, then you can choose which keyspace\(s\) you want to copy to the clone datacenter\.

1. When you are ready to begin replicating data to your clone datacenter, choose **Start**\.

   Data replication will begin immediately\. AWS SCT displays a progress bar so that you can monitor the replication process\. Note that replication can take a long time, depending on how much data is in the source datacenter\. If you need to cancel the operation before it's fully complete, choose **Cancel**\.

   When the replication is complete, choose **Next** to continue\.

   

1. In the Summary window, AWS SCT displays a report showing the state of your Cassandra cluster, along with next steps\.

   Review the information in this report, and then choose **Finish** to complete the wizard\.

## Install, configure, and run the data extraction agent<a name="agents.cassandra.run-extractor"></a>

Now that you have a clone of your datacenter, you are ready to begin using the AWS SCT data extraction agent for Cassandra\. This agent is available as part of the AWS SCT distribution \(for more information, see [Installing, verifying, and updating AWS SCT](CHAP_Installing.md)\)\.

**Note**  
We recommend that you run the agent on an Amazon EC2 instance\. The Amazon EC2 instance must meet the following requirements:  
Operating system: either Ubuntu or CentOS\.
8 virtual CPUs, at a minimum\.
At least 16GB of RAM\.
If you don't already have an Amazon EC2 instance that meets these requirements, go to the Amazon EC2 Management Console \([https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\) and launch a new instance before proceeding\. 

Follow this procedure to install, configure, and run the AWS SCT data extraction agent for Cassandra:

1. Log in to your Amazon EC2 instance\.

1. Verify that you are running Java 1\.8\.*x* is installed:

   ```
   java -version 
   ```

1. Install the `sshfs` package:

   ```
   sudo yum install sshfs 
   ```

1. Install the `expect` package:

   ```
   sudo yum install expect 
   ```

1. Edit the `/etc/fuse.conf` file, and uncomment the string `user_allow_other`:

   ```
   # mount_max = 1000
   user_allow_other
   ```

1. The AWS SCT data extraction agent for Cassandra is available as part of the AWS SCT distribution \(for more information, see [Installing, verifying, and updating AWS SCT](CHAP_Installing.md)\)\. You can find the agent in the \.zip file that contains the AWS SCT installer file, in the `agents` directory\. The following builds of the agent are available:    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/agents.cassandra.html)

   Choose the file that's appropriate for your Amazon EC2 instance\. Use the `scp` utility to upload that file to your Amazon EC2 instance\.

1. Install the AWS SCT data extraction agent for Cassandra\. \(Replace `n.n.n` with the build number\.\)
   + For Ubuntu:

     ```
     sudo dpkg -i aws-cassandra-extractor-n.n.n.deb 
     ```
   + For CentOS:

     ```
     sudo yum install aws-cassandra-extractor-n.n.n.x86_64.rpm 
     ```

   During the installation process, you'll be asked to select the Cassandra version you want to work with\. Choose version **3** or **2**, as appropriate\.

1. After the installation completes, review the following directories to ensure that they were created successfully:
   + `/var/log/cassandra-data-extractor/`—for extraction agent logs\.
   + `/mnt/cassandra-data-extractor/`—for mounting home and data folders\.
   + `/etc/cassandra-data-extractor/`—for the agent configuration file \(`agent-settings.yaml`\)\.

1. To enable the agent to communicate with AWS SCT, you must have a key store and a trust store available\. \(You created these in [Security settings](#agents.cassandra.prereqs.keystore-and-truststore)\.\) Use the `scp` utility to upload these files to your Amazon EC2 instance\.

   The configuration utility \(see next step\) requires you to specify the key store and trust store, so you need to have them available\.

1. Run the configuration utility:

   ```
   sudo java -jar /usr/share/aws-cassandra-extractor/aws-cassandra-extractor.jar  --configure 
   ```

   The utility will prompt you for several configuration values\. You can use the following example as a guide, while substituting your own values:

   ```
   Enter the number of data providers nodes [1]: 1
   Enter IP for Cassandra node 1: 34.220.73.140
   Enter SSH port for Cassandra node <34.220.73.140> [22]: 22
   Enter SSH login for Cassandra node <34.220.73.140> : centos
   Enter SSH password for Cassandra node <34.220.73.140> (optional):
   Is the connection to the node using a SSH private key? Y/N [N] : Y
   Enter the path to the private SSH key for Cassandra node <34.220.73.140>: /home/centos/my-ec2-private-key.pem
   Enter passphrase for SSH private key for Cassandra node <34.220.73.140> (optional):
   Enter the path to the cassandra.yaml file location on the node <34.220.73.140>: /etc/cassandra/conf/
   Enter the path to the Cassandra data directories on the node <34.220.73.140>: /u01/cassandra/data
   ===== Mounting process started =====
   Node [34.220.73.140] mounting started.
   Will be executed command:
   sudo sshfs ubuntu@34.220.73.140:/etc/cassandra/ /mnt/aws-cassandra-data-extractor/34.220.73.140_node/conf/ -p 22 -o allow_other -o StrictHostKeyChecking=no -o IdentityFile=/home/ubuntu/dbbest-ec2-oregon_s.pem > /var/log/aws-cassandra-data-extractor/dmt-cassandra-v3/conf_34.220.73.140.log 2>&1
   Will be executed command:
   sudo sshfs ubuntu@34.220.73.140:/u01/cassandra/data/ /mnt/aws-cassandra-data-extractor/34.220.73.140_node/data/data -p 22 -o allow_other -o StrictHostKeyChecking=no -o IdentityFile=/home/ubuntu/dbbest-ec2-oregon_s.pem > /var/log/aws-cassandra-data-extractor/dmt-cassandra-v3/data_34.220.73.140.log 2>&1
   ===== Mounting process was over =====
   Enable SSL communication Y/N [N] : Y
   Path to key store: /home/centos/Cassandra_key
   Key store password:123456
   Re-enter the key store password:123456
   Path to trust store: /home/centos/Cassandra_trust
   Trust store password:123456
   Re-enter the trust store password:123456
   Enter the path to the output local folder: /home/centos/out_data
   === Configuration aws-agent-settings.yaml successful completed === 
   If you want to add new nodes or change it parameters, you should edit the configuration file /etc/aws-cassandra-data-extractor/dmt-cassandra-v3/aws-agent-settings.yaml
   ```
**Note**  
When the configuration utility has completed, you might see the following message:  
 `Change the SSH private keys permission to 600 to secure them. You can also set permissions to 400.`  
You can use the `chmod` command to change the permissions, as in this example:  

   ```
   chmod 400 /home/centos/my-ec2-private-key.pem 
   ```

1. After the configuration utility completes, review the following directories and files:
   + `/etc/cassandra-data-extractor/agent-settings.yaml`—the settings file for the agent\.
   + `$HOME/out_data`—a directory for extraction output files\.
   + `/mnt/cassandra-data-extractor/34.220.73.140_node/conf`—an empty Cassandra home folder\. \(Replace `34.220.73.140` with your actual IP address\.\)
   + `/mnt/cassandra-data-extractor/34.220.73.140_node/data/data`—an empty Cassandra data file\. \(Replace `34.220.73.140` with your actual IP address\.\)

   If these directories aren't mounted, use the following command to mount them:

   ```
   sudo java -jar /usr/share/aws-cassandra-extractor/aws-cassandra-extractor.jar -mnt 
   ```

1. Mount the Cassandra home and data directories:

   ```
   sudo java -jusr/share/cassandra-extractor/rest-extraction-service.jar -mnt 
   ```

   After the mounting process is complete, review the Cassandra home folder and Cassandra data file directory as shown in the following example\. \(Replace `34.220.73.140` with your actual IP address\.\)

   ```
   ls -l /mnt/cassandra-data-extractor/34.220.73.140_node/conf
   ls -l /mnt/cassandra-data-extractor/34.220.73.140_node/data/data
   ```

1. Start the AWS SCT data extraction agent for Cassandra:

   ```
   sudo systemctl start aws-cassandra-extractor 
   ```
**Note**  
By default, the agent runs on port 8080\. You can change this by editing the `agent-settings.yaml` file\.

## Migrate data from the clone datacenter to Amazon DynamoDB<a name="agents.cassandra.migrate-to-ddb"></a>

You are now ready to perform the migration from the clone datacenter to Amazon DynamoDB, using AWS SCT\. AWS SCT manages the workflows among the AWS SCT data extraction agent for Cassandra, AWS Database Migration Service \(AWS DMS\), and DynamoDB You perform the migration process entirely within the AWS SCT interface, and AWS SCT manages all of the external components on your behalf\.

To migrate your data, follow this procedure:

1. From the **View** menu, choose **Data migration view**\. 

1. Choose the **Agents** tab\.

1. If you haven't yet registered the AWS SCT data extraction agent, you'll see the following message:  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/images/cassandra-register-agent.png)

   Choose **Register**\.

1. In the **New agent registration** window, add the following information:    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/agents.cassandra.html)

   If you are using SSL, choose the **SSL** tab and add the following information:    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/agents.cassandra.html)

   When the settings are as you want them, choose **Register**\. AWS SCT will attempt to connect with the AWS SCT data extraction agent for Cassandra\.

1. On the left side of the AWS SCT window, choose the Cassandra datacenter that you created in [Create a clone datacenter](#agents.cassandra.clone-datacenter)\.

1. From the **Actions** menu, choose **Create Local & DMS Task**\.

1. In the **Create Local & DMS Task** window, enter the following information:    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/agents.cassandra.html)

   When the settings are as you want them, choose **Create**\.

1. Choose the **Tasks** tab, where you should see the task you created\. To start the task choose **Start**\.

   You can monitor the task progress, as shown in the following screenshot:  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/images/cassandra-monitor-task.png)

## Post\-migration activities<a name="agents.cassandra.post-migration"></a>

If you are finished with the migration and want to delete the migration task, do the following:

1. Choose the **Tasks** tab\.

1. If your task is currently running, choose **Stop**\.

1. To delete the task, choose **Delete**\.

If you no longer need to use the AWS SCT data extraction agent for Cassandra, do the following:

1. Choose the **Agents** tab\.

1. Choose the agent you no longer need\.

1. Choose **Unregister**\.