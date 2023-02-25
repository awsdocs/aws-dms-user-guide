# Step 2: Configure your data providers<a name="getting-started-data-providers"></a>

Next, you create data providers that describe your source and target databases\. For each data provider, you specify a data store type and location information\. You don't store your database credentials in a data provider\.

**To create a data provider for a source on\-premises database**

1. Sign in to the AWS Management Console, and open the AWS DMS console\.

1. In the navigation pane, choose **Data providers**, and then choose **Create data provider**\.

1. For **Configuration**, choose **Enter manually**\.

1. For **Name**, enter a unique name for your source data provider\. For example, enter **sc\-source**\.

1. For **Engine type**, choose the type of database engine for your data provider\.

1. Provide your connection information for the source database\. The connection parameters depend on your source database engine\. For more information, see [Data providers](data-providers.md)\.

1. For **Secure Socket Layer \(SSL\) mode**, choose the type of SSL enforcement\.

1. Choose **Create data provider**\.

**To create a data provider for a target Amazon RDS database**

1. Sign in to the AWS Management Console and open the AWS DMS console\.

1. In the navigation pane, choose **Data providers**, and then choose **Create data provider**\.

1. For **Configuration**, choose **RDS database instance**\.

1. For **Database from RDS**, choose **Browse**, and choose your database\. DMS Schema Conversion automatically retrieves the information about the engine type, server name, and port\.

1. For **Name**, enter a unique name for your target data provider\. For example, enter **sc\-target**\.

1. For **Database name**, enter the name of your database\.

1. For **Secure Socket Layer \(SSL\) mode**, choose the type of SSL enforcement\.

1. Choose **Create data provider**\.