# Using Azure Synapse Analytics as a source for AWS SCT<a name="CHAP_Source.AzureSynapse"></a>

You can use AWS SCT to convert schemas, code objects, and application code from Azure Synapse Analytics to Amazon Redshift\. 

## Privileges for Azure Synapse Analytics as a source<a name="CHAP_Source.AzureSynapse.Permissions"></a>

The privileges required for Azure Synapse Analytics data warehouse as a source are listed following: 
+ VIEW DEFINITION 
+ VIEW DATABASE STATE 

Repeat the grant for each database whose schema you are converting\. 

## Connecting to Azure Synapse Analytics as a source<a name="CHAP_Source.AzureSynapse.Connecting"></a>

Use the following procedure to connect to your Azure Synapse Analytics source data warehouse with the AWS Schema Conversion Tool\. 

**To connect to a Azure Synapse Analytics source data warehouse**

1. In the AWS Schema Conversion Tool, choose **Add source**\. 

1. Choose **Azure Synapse Analytics**, then choose **Next**\. 

   The **Add source** dialog box appears\.

1. Provide the Azure Synapse Analytics source data warehouse connection information\. Use the instructions in the following table\.   
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_Source.AzureSynapse.html)

1. Choose **Test Connection** to verify that AWS SCT can connect to your source database\. 

1. Choose **Connect** to connect to your source database\.