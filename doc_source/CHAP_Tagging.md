# Tagging Resources in AWS Database Migration Service<a name="CHAP_Tagging"></a>

You can use tags in AWS Database Migration Service \(AWS DMS\) to add metadata to your resources\. In addition, you can use these tags with AWS Identity and Access Management \(IAM\) policies to manage access to AWS DMS resources and to control what actions can be applied to the AWS DMS resources\. Finally, you can use these tags to track costs by grouping expenses for similarly tagged resources\. 

All AWS DMS resources can be tagged: 
+ Replication instances
+ Endpoints
+ Replication tasks
+ Certificates

An AWS DMS tag is a name\-value pair that you define and associate with an AWS DMS resource\. The name is referred to as the key\. Supplying a value for the key is optional\. You can use tags to assign arbitrary information to an AWS DMS resource\. A tag key could be used, for example, to define a category, and the tag value could be a item in that category\. For example, you could define a tag key of "project" and a tag value of "Salix", indicating that the AWS DMS resource is assigned to the Salix project\. You could also use tags to designate AWS DMS resources as being used for test or production by using a key such as environment=test or environment =production\. We recommend that you use a consistent set of tag keys to make it easier to track metadata associated with AWS DMS resources\. 

Use tags to organize your AWS bill to reflect your own cost structure\. To do this, sign up to get your AWS account bill with tag key values included\. Then, to see the cost of combined resources, organize your billing information according to resources with the same tag key values\. For example, you can tag several resources with a specific application name, and then organize your billing information to see the total cost of that application across several services\. For more information, see [Cost Allocation and Tagging](http://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/cost-alloc-tags.html) in *About AWS Billing and Cost Management*\.

Each AWS DMS resource has a tag set, which contains all the tags that are assigned to that AWS DMS resource\. A tag set can contain as many as ten tags, or it can be empty\. If you add a tag to an AWS DMS resource that has the same key as an existing tag on resource, the new value overwrites the old value\. 

AWS does not apply any semantic meaning to your tags; tags are interpreted strictly as character strings\. AWS DMS might set tags on an AWS DMS resource, depending on the settings that you use when you create the resource\. 

The following list describes the characteristics of an AWS DMS tag\.
+ The tag key is the required name of the tag\. The string value can be from 1 to 128 Unicode characters in length and cannot be prefixed with "aws:" or "dms:"\. The string might contain only the set of Unicode letters, digits, white\-space, '\_', '\.', '/', '=', '\+', '\-' \(Java regex: "^\(\[\\\\p\{L\}\\\\p\{Z\}\\\\p\{N\}\_\.:/=\+\\\\\-\]\*\)$"\)\.
+ The tag value is an optional string value of the tag\. The string value can be from 1 to 256 Unicode characters in length and cannot be prefixed with "aws:" or "dms:"\. The string might contain only the set of Unicode letters, digits, white\-space, '\_', '\.', '/', '=', '\+', '\-' \(Java regex: "^\(\[\\\\p\{L\}\\\\p\{Z\}\\\\p\{N\}\_\.:/=\+\\\\\-\]\*\)$"\)\.

  Values do not have to be unique in a tag set and can be null\. For example, you can have a key\-value pair in a tag set of project/Trinity and cost\-center/Trinity\.

You can use the AWS CLI or the AWS DMS API to add, list, and delete tags on AWS DMS resources\. When using the AWS CLI or the AWS DMS API, you must provide the Amazon Resource Name \(ARN\) for the AWS DMS resource you want to work with\. For more information about constructing an ARN, see [Constructing an Amazon Resource Name \(ARN\) for AWS DMS](CHAP_Introduction.AWS.ARN.md)\.

Note that tags are cached for authorization purposes\. Because of this, additions and updates to tags on AWS DMS resources might take several minutes before they are available\. 

## API<a name="CHAP_Tagging.API"></a>

You can add, list, or remove tags for a AWS DMS resource using the AWS DMS API\.
+ To add a tag to an AWS DMS resource, use the [ `AddTagsToResource`](http://docs.aws.amazon.com/dms/latest/APIReference//API_AddTagsToResource.html) operation\.
+ To list tags that are assigned to an AWS DMS resource, use the [http://docs.aws.amazon.com/dms/latest/APIReference//API_ListTagsForResource.html](http://docs.aws.amazon.com/dms/latest/APIReference//API_ListTagsForResource.html) operation\.
+ To remove tags from an AWS DMS resource, use the [http://docs.aws.amazon.com/dms/latest/APIReference//API_RemoveTagsFromResource.html](http://docs.aws.amazon.com/dms/latest/APIReference//API_RemoveTagsFromResource.html) operation\.

To learn more about how to construct the required ARN, see [Constructing an Amazon Resource Name \(ARN\) for AWS DMS](CHAP_Introduction.AWS.ARN.md)\.

When working with XML using the AWS DMS API, tags use the following schema:

```
<Tagging>
  <TagSet>
  	<Tag>
  		<Key>Project</Key>
  		<Value>Trinity</Value>
  	</Tag>
  	<Tag>
  		<Key>User</Key>
  		<Value>Jones</Value>
  	</Tag>
  </TagSet>
</Tagging>
```

The following table provides a list of the allowed XML tags and their characteristics\. Note that values for Key and Value are case dependent\. For example, project=Trinity and PROJECT=Trinity are two distinct tags\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Tagging.html)