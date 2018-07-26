# Working with AWS DMS Endpoints<a name="CHAP_Endpoints"></a>

An endpoint provides connection, data store type, and location information about your data store\. AWS Database Migration Service uses this information to connect to a data store and migrate data from a source endpoint to a target endpoint\. You can specify additional connection attributes for an endpoint by using extra connection attributes\. These attributes can control logging, file size, and other parameters; for more information about extra connection attributes, see the documentation section for your data store\. 

Following, you can find out more details about endpoints\.

**Topics**
+ [Sources for Data Migration](CHAP_Source.md)
+ [Targets for Data Migration](CHAP_Target.md)
+ [Creating Source and Target Endpoints](#CHAP_Endpoints.Creating)

## Creating Source and Target Endpoints<a name="CHAP_Endpoints.Creating"></a>

You can create source and target endpoints when you create your replication instance or you can create endpoints after your replication instance is created\. The source and target data stores can be on an Amazon Elastic Compute Cloud \(Amazon EC2\) instance, an Amazon Relational Database Service \(Amazon RDS\) DB instance, or an on\-premises database\.

The procedure following assumes that you have chosen the AWS DMS console wizard\. Note that you can also do this step by selecting **Endpoints** from the AWS DMS console's navigation pane and then selecting **Create endpoint**\. When using the console wizard, you create both the source and target endpoints on the same page\. When not using the console wizard, you create each endpoint separately\.

**To specify source or target database endpoints using the AWS console**

1. On the **Connect source and target database endpoints** page, specify your connection information for the source or target database\. The following table describes the settings\.  
![\[Create DB endpoints\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-create-endpoint1.png)    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Endpoints.html)

1. Choose the **Advanced** tab, shown following, to set values for connection string and encryption key if you need them\. You can test the endpoint connection by choosing **Run test**\.  
![\[Create source or target DB endpoints\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-create-endpoint2.png)    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Endpoints.html)