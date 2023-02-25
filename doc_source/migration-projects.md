# Working with migration projects in DMS Schema Conversion<a name="migration-projects"></a>

After you create data providers, your instance profile, and other AWS resources, you can create a migration project for DMS Schema Conversion\. A *migration project* describes your source and target data providers, your instance profile, and migration rules\. You can create multiple migration projects for different source and target data providers\.

The migration project is the foundation of your work with DMS Schema Conversion\. Here, you assess the objects of your source data provider and convert them to a format compatible with the target database\. Then, you can apply converted code to your target data provider or save it as a SQL script\.

Migration projects in DMS Schema Conversion are serverless only\. AWS DMS automatically provisions the cloud resources for your migration projects\.

You can create up to 3 migration projects in DMS Schema Conversion\.

**Topics**
+ [Creating migration projects in DMS Schema Conversion](migration-projects-create.md)
+ [Managing migration projects in DMS Schema Conversion](migration-projects-manage.md)
+ [Specifying migration project settings in DMS Schema Conversion](migration-projects-settings.md)