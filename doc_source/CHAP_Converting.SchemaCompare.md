# Comparing database schemas<a name="CHAP_Converting.SchemaCompare"></a>

If you made changes to your source or target schema after you migrated, you can compare the two database schemas using AWS SCT\. You can compare schemas for versions the same as or earlier than the source schema\. 

The following schema comparisons are supported:
+ Oracle to Oracle, versions 12\.1\.0\.2\.0, 11\.1\.0\.7\.0, 11\.2\.0\.1\.0, 10
+ SQL Server to SQL Server, versions 2016, 2014, 2012, 2008 RD2, 2008
+ PostgreSQL to PostgreSQL and Aurora PostgreSQL\-Compatible Edition, versions 9\.6, 9\.5\.9, 9\.5\.4
+ MySQL to MySQL, versions 5\.6\.36, 5\.7\.17, 5\.5

You specify settings for the schema comparison on the **Compare Schema** tab of the **Project Settings** page\.

![\[Schema compare settings\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/images/schema-compare-settings.png)

To compare schemas, you select the schemas, and AWS SCT indicates the objects that differ between the two schemas and the objects that don't\.

**To compare two schemas**

1. Open an existing AWS SCT project, or create a project and connect to the source and target endpoints\.

1. Choose the schema you want to compare\.

1. Open the context menu \(right\-click\) and choose **Compare Schema**\.

 AWS SCT indicates objects that are different between the two schemas by adding a black circle to the object's icon\.

![\[Schema compare result\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/images/schema-compare-results.png)

You can apply the results of the schema comparison to a single object, to a single category of objects, or to the entire schema\. Choose the box next to the category, object, or schema that you want to apply the results to\.