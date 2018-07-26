# Setting Up a Network for a Replication Instance<a name="CHAP_ReplicationInstance.VPC"></a>

AWS DMS always creates the replication instance in a VPC based on Amazon Virtual Private Cloud \(Amazon VPC\)\. You specify the VPC where your replication instance is located\. You can use your default VPC for your account and AWS Region, or you can create a new VPC\. The VPC must have two subnets in at least one Availability Zone\.

The Elastic Network Interface \(ENI\) allocated for the replication instance in your VPC must be associated with a security group that has rules that allow all traffic on all ports to leave \(egress\) the VPC\. This approach allows communication from the replication instance to your source and target database endpoints, as long as correct egress rules are enabled on the endpoints\. We recommend that you use the default settings for the endpoints, which allows egress on all ports to all addresses\.

The source and target endpoints access the replication instance that is inside the VPC either by connecting to the VPC or by being inside the VPC\. The database endpoints must include network access control lists \(ACLs\) and security group rules \(if applicable\) that allow incoming access from the replication instance\. Depending on the network configuration you are using, you can use the replication instance VPC security group, the replication instance's private or public IP address, or the NAT gateway's public IP address\. These connections form a network that you use for data migration\.

## Network Configurations for Database Migration<a name="CHAP_ReplicationInstance.VPC.Configurations"></a>

You can use several different network configurations with AWS Database Migration Service\. The following are common configurations for a network used for database migration\.

**Topics**
+ [Configuration with All Database Migration Components in One VPC](#CHAP_ReplicationInstance.VPC.Configurations.ScenarioAllVPC)
+ [Configuration with Two VPCs](#CHAP_ReplicationInstance.VPC.Configurations.ScenarioVPCPeer)
+ [Configuration for a Network to a VPC Using AWS Direct Connect or a VPN](#CHAP_ReplicationInstance.VPC.Configurations.ScenarioDirect)
+ [Configuration for a Network to a VPC Using the Internet](#CHAP_ReplicationInstance.VPC.Configurations.ScenarioInternet)
+ [Configuration with an Amazon RDS DB instance not in a VPC to a DB instance in a VPC Using ClassicLink](#CHAP_ReplicationInstance.VPC.Configurations.ClassicLink)

### Configuration with All Database Migration Components in One VPC<a name="CHAP_ReplicationInstance.VPC.Configurations.ScenarioAllVPC"></a>

The simplest network for database migration is for the source endpoint, the replication instance, and the target endpoint to all be in the same VPC\. This configuration is a good one if your source and target endpoints are on an Amazon RDS DB instance or an Amazon EC2 instance\.

The following illustration shows a configuration where a database on an Amazon EC2 instance connects to the replication instance and data is migrated to an Amazon RDS DB instance\.

![\[AWS Database Migration Service All in one VPC example\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-scenarioAllVPC.png)

The VPC security group used in this configuration must allow ingress on the database port from the replication instance\. You can do this by either ensuring that the security group used by the replication instance has ingress to the endpoints, or by explicitly allowing the private IP address of the replication instance\. 

### Configuration with Two VPCs<a name="CHAP_ReplicationInstance.VPC.Configurations.ScenarioVPCPeer"></a>

If your source endpoint and target endpoints are in different VPCs, you can create your replication instance in one of the VPCs and then link the two VPCs by using VPC peering\.

A VPC peering connection is a networking connection between two VPCs that enables routing using each VPC’s private IP addresses as if they were in the same network\. We recommend this method for connecting VPCs within an AWS Region\. You can create VPC peering connections between your own VPCs or with a VPC in another AWS account within the same AWS Region\. For more information about VPC peering, see [VPC Peering](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/vpc-peering.html) in the *Amazon VPC User Guide*\.

The following illustration shows an example configuration using VPC peering\. Here, the source database on an Amazon EC2 instance in a VPC connects by VPC peering to a VPC\. This VPC contains the replication instance and the target database on an Amazon RDS DB instance\.

![\[AWS Database Migration Service replication instance\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-scenarioVPCPeer.png)

The VPC security groups used in this configuration must allow ingress on the database port from the replication instance\.

### Configuration for a Network to a VPC Using AWS Direct Connect or a VPN<a name="CHAP_ReplicationInstance.VPC.Configurations.ScenarioDirect"></a>

Remote networks can connect to a VPC using several options such as AWS Direct Connect or a software or hardware VPN connection\. These options are often used to integrate existing on\-site services, such as monitoring, authentication, security, data, or other systems, by extending an internal network into the AWS cloud\. By using this type of network extension, you can seamlessly connect to AWS\-hosted resources such as a VPC\.

The following illustration shows a configuration where the source endpoint is an on\-premises database in a corporate data center\. It is connected by using AWS Direct Connect or a VPN to a VPC that contains the replication instance and a target database on an Amazon RDS DB instance\.

![\[AWS Database Migration Service replication instance\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-scenarioDirect.png)

In this configuration, the VPC security group must include a routing rule that sends traffic destined for a specific IP address or range to a host\. This host must be able to bridge traffic from the VPC into the on\-premises VPN\. In this case, the NAT host includes its own security group settings that must allow traffic from the replication instance’s private IP address or security group into the NAT instance\. 

### Configuration for a Network to a VPC Using the Internet<a name="CHAP_ReplicationInstance.VPC.Configurations.ScenarioInternet"></a>

If you don't use a VPN or AWS Direct Connect to connect to AWS resources, you can use the Internet to migrate a database to an Amazon EC2 instance or Amazon RDS DB instance\. This configuration involves a public replication instance in a VPC with an internet gateway that contains the target endpoint and the replication instance\.

![\[AWS Database Migration Service replication instance\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-scenarioInternet.png)

To add an Internet gateway to your VPC, see [Attaching an Internet Gateway](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_Internet_Gateway.html#Add_IGW_Attach_Gateway) in the *Amazon VPC User Guide*\.

The VPC security group must include routing rules that send traffic not destined for the VPC by default to the Internet gateway\. In this configuration, the connection to the endpoint appears to come from the public IP address of the replication instance, not the private IP address\. 

### Configuration with an Amazon RDS DB instance not in a VPC to a DB instance in a VPC Using ClassicLink<a name="CHAP_ReplicationInstance.VPC.Configurations.ClassicLink"></a>

You can use ClassicLink with a proxy server to connect an Amazon RDS DB instance that is not in a VPC to an AWS DMS replication server and DB instance that reside in a VPC\. 

ClassicLink allows you to link an EC2\-Classic DB instance to a VPC in your account, within the same AWS Region\. After you've created the link, the source DB instance can communicate with the replication instance inside the VPC using their private IP addresses\. 

Because the replication instance in the VPC cannot directly access the source DB instance on the EC2\-Classic platform using ClassicLink, you must use a proxy server\. The proxy server connects the source DB instance to the VPC containing the replication instance and target DB instance\. The proxy server uses ClassicLink to connect to the VPC\. Port forwarding on the proxy server allows communication between the source DB instance and the target DB instance in the VPC\. 

![\[AWS Database Migration Service using ClassicLink\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-scenarioClassicLink.png)

#### Using ClassicLink with AWS Database Migration Service<a name="CHAP_ReplicationInstance.VPC.Configurations.ClassicLink.Using"></a>

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

## Creating a Replication Subnet Group<a name="CHAP_ReplicationInstance.VPC.Subnets"></a>

As part of the network to use for database migration, you need to specify what subnets in your Amazon Virtual Private Cloud \(Amazon VPC\) you plan to use\. A *subnet* is a range of IP addresses in your VPC in a given Availability Zone\. These subnets can be distributed among the Availability Zones for the AWS Region where your VPC is located\.

You create a replication instance in a subnet that you select, and you can manage what subnet a source or target endpoint uses by using the AWS DMS console\.

You create a replication subnet group to define which subnets to use\. You must specify at least one subnet in two different Availability Zones\.

**To create a replication subnet group**

1. Sign in to the AWS Management Console and choose AWS Database Migration Service\. If you are signed in as an AWS Identity and Access Management \(IAM\) user, you must have the appropriate permissions to access AWS DMS\. For more information on the permissions required for database migration, see [IAM Permissions Needed to Use AWS DMS](CHAP_Security.IAMPermissions.md)\.

1. In the navigation pane, choose **Subnet Groups**\.

1. Choose **Create Subnet Group**\. 

1. On the **Edit Replication Subnet Group** page, shown following, specify your replication subnet group information\. The following table describes the settings\.  
![\[AWS Database Migration Service replication instance\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-repsubnetgroup.png)    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_ReplicationInstance.VPC.html)

1. Choose **Add** to add the subnets to the replication subnet group\.

1. Choose **Create**\.