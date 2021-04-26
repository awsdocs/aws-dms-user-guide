# How AWS Database Migration Service works<a name="CHAP_Introduction"></a>

AWS Database Migration Service \(AWS DMS\) is a web service that you can use to migrate data from a source data store to a target data store\. These two data stores are called endpoints\. You can migrate between source and target endpoints that use the same database engine, such as from an Oracle database to an Oracle database\. You can also migrate between source and target endpoints that use different database engines, such as from an Oracle database to a PostgreSQL database\. The only requirement to use AWS DMS is that one of your endpoints must be on an AWS service\. You can't use AWS DMS to migrate from an on\-premises database to another on\-premises database\. 

For information on the cost of database migration, see the [AWS Database Migration Service pricing page](https://aws.amazon.com/dms/pricing/)\.

Use the following topics to better understand AWS DMS\.

**Topics**
+ [High\-level view of AWS DMS](CHAP_Introduction.HighLevelView.md)
+ [Components of AWS DMS](CHAP_Introduction.Components.md)
+ [Sources for AWS DMS](CHAP_Introduction.Sources.md)
+ [Targets for AWS DMS](CHAP_Introduction.Targets.md)
+ [Constructing an Amazon Resource Name \(ARN\) for AWS DMS](CHAP_Introduction.AWS.ARN.md)
+ [Using AWS DMS with other AWS services](CHAP_Introduction.AWS.md)