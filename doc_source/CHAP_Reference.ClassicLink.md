# Using ClassicLink with AWS Database Migration Service<a name="CHAP_Reference.ClassicLink"></a>

 You can use ClassicLink, in conjunction with a proxy server, to connect an Amazon RDS DB instance that is not in a VPC to a AWS DMS replication server and DB instance that reside in a VPC\. 

The following procedure shows how to use ClassicLink to connect an Amazon RDS source DB instance that is not in a VPC to a VPC containing an AWS DMS replication instance and a target DB instance\. 

+ Create an AWS DMS replication instance in a VPC\. \(All replication instances are created in a VPC\)\.

+ Associate a VPC security group to the replication instance and the target DB instance\. When two instances share a VPC security group, they can communicate with each other by default\.

+ Set up a proxy server on an EC2 Classic instance\.

+ Create a connection using ClassicLink between the proxy server and the VPC\.

+ Create AWS DMS endpoints for the source and target databases\.

+ Create an AWS DMS task\.

**To use ClassicLink to migrate a database on a DB instance not in a VPC to a database on a DB instance in a VPC**

1. Step 1: Create an AWS DMS replication instance\.

   To create a AWS DMS replication instance and assign a VPC security group:

   1. Sign in to the AWS Management Console and choose AWS Database Migration Service\. Note that if you are signed in as an AWS Identity and Access Management \(IAM\) user, you must have the appropriate permissions to access AWS DMS\. For more information on the permissions required for database migration, see [IAM Permissions Needed to Use AWS DMS](CHAP_Security.IAMPermissions.md)\.

   1. On the **Dashboard** page, choose **Replication Instance**\. Follow the instructions at [Step 2: Create a Replication Instance](CHAP_GettingStarted.md#CHAP_GettingStarted.ReplicationInstance) to create a replication instance\.

   1.  After you have created the AWS DMS replication instance, open the EC2 service console\. Select **Network Interfaces** from the navigation pane\. 

   1. Select the *DMSNetworkInterface*, and then choose **Change Security Groups** from the **Actions** menu\.

   1. Select the security group you want to use for the replication instance and the target DB instance\.

1.  Step 2: Associate the security group from the last step with the target DB instance\. 

   To associate a security group with a DB instance

   1. Open the Amazon RDS service console\. Select **Instances** from the navigation pane\.

   1.  Select the target DB instance\. From **Instance Actions**, select **Modify**\. 

   1. For the **Security Group** parameter, select the security group you used in the previous step\.

   1. Select **Continue**, and then **Modify DB Instance**\.

1. Step 3: Set up a proxy server on an EC2 Classic instance using NGINX\. Use an AMI of your choice to launch an EC2 Classic instance\. The example below is based on the AMI Ubuntu Server 14\.04 LTS \(HVM\)\. 

   To set up a proxy server on an EC2 Classic instance

   1. Connect to the EC2 Classic instance and install NGINX using the following commands:

      ```
      Prompt> sudo apt-get update
      Prompt> sudo wget http://nginx.org/download/nginx-1.9.12.tar.gz
      Prompt> sudo tar -xvzf nginx-1.9.12.tar.gz 
      Prompt> cd nginx-1.9.12
      Prompt> sudo apt-get install build-essential
      Prompt> sudo apt-get install libpcre3 libpcre3-dev
      Prompt> sudo apt-get install zlib1g-dev
      Prompt> sudo ./configure --with-stream
      Prompt> sudo make
      Prompt> sudo make install
      ```

   1.  Edit the NGINX daemon file, /etc/init/nginx\.conf, using the following code: 

      ```
      # /etc/init/nginx.conf – Upstart file
      
      description "nginx http daemon"
      author "email"
      
      start on (filesystem and net-device-up IFACE=lo)
      stop on runlevel [!2345]
      
      env DAEMON=/usr/local/nginx/sbin/nginx
      env PID=/usr/local/nginx/logs/nginx.pid
      
      expect fork
      respawn
      respawn limit 10 5
      
      pre-start script
              $DAEMON -t
              if [ $? -ne 0 ]
                      then exit $?
              fi
      end script
      
      exec $DAEMON
      ```

   1. Create an NGINX configuration file at /usr/local/nginx/conf/nginx\.conf\. In the configuration file, add the following:

      ```
      # /usr/local/nginx/conf/nginx.conf - NGINX configuration file
      
      worker_processes  1;
      
      events {
          worker_connections  1024;
      }
      
      stream {
        server {
          listen <DB instance port number>;
      proxy_pass <DB instance identifier>:<DB instance port number>;
          }
      }
      ```

   1. From the command line, start NGINX using the following commands:

      ```
      Prompt> sudo initctl reload-configuration
      Prompt> sudo initctl list | grep nginx
      Prompt> sudo initctl start nginx
      ```

1. Step 4: Create a ClassicLink connection between the proxy server and the target VPC that contains the target DB instance and the replication instance

   Use ClassicLink to connect the proxy server with the target VPC

   1. Open the EC2 console and select the EC2 Classic instance that is running the proxy server\.

   1. Select **ClassicLink** under **Actions**, then select **Link to VPC**\.

   1.  Select the security group you used earlier in this procedure\. 

   1. Select **Link to VPC**\.

1. Step 5: Create AWS DMS endpoints using the procedure at [Step 3: Specify Source and Target Endpoints](CHAP_GettingStarted.md#CHAP_GettingStarted.Endpoints)\. You must use the internal EC2 DNS hostname of the proxy as the server name when specifying the source endpoint\.

1.  Step 6: Create a AWS DMS task using the procedure at [Step 4: Create a Task](CHAP_GettingStarted.md#CHAP_GettingStarted.Tasks)\. 