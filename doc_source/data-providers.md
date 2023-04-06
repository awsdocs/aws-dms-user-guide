# Adding data providers for DMS Schema Conversion<a name="data-providers"></a>

Before you create your migration project in DMS Schema Conversion, you first add data providers\. A *data provider* stores a data store type and the location information about your database\.

After you add a data provider to your migration project, you provide the database credentials from AWS Secrets Manager\. DMS Schema Conversion uses this information to connect to a database\. Then DMS Schema Conversion converts schemas of your source data provider to a format that is compatible with your target data provider\. After you apply the converted code to your new target database, you can migrate the data using AWS DMS\.

AWS DMS has the maximum number of data providers that you can create for your AWS account\. See the following section for information about AWS DMS service quotas [Quotas for AWS Database Migration Service](CHAP_Limits.md)\.

**Topics**
+ [Creating data providers in DMS Schema Conversion](data-providers-create.md)
+ [Creating source data providers in DMS Schema Conversion](data-providers-source.md)
+ [Creating target data providers in DMS Schema Conversion](data-providers-target.md)