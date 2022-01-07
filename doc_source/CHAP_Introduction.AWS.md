# Using AWS DMS with other AWS services<a name="CHAP_Introduction.AWS"></a>

You can use AWS DMS with several other AWS services:
+ You can use an Amazon EC2 instance or Amazon RDS DB instance as a target for a data migration\.
+ You can use the AWS Schema Conversion Tool \(AWS SCT\) to convert your source schema and SQL code into an equivalent target schema and SQL code\.
+ You can use Amazon S3 as a storage site for your data, or you can use it as an intermediate step when migrating large amounts of data\.
+ You can use AWS CloudFormation to set up your AWS resources for infrastructure management or deployment\. For example, you can provision AWS DMS resources such as replication instances, tasks, certificates, and endpoints\. You create a template that describes all the AWS resources that you want, and AWS CloudFormation provisions and configures those resources for you\.

## AWS DMS support for AWS CloudFormation<a name="CHAP_Introduction.AWS.CloudFormation"></a>

You can provision AWS DMS resources using AWS CloudFormation\. AWS CloudFormation is a service that helps you model and set up your AWS resources for infrastructure management or deployment\. For example, you can provision AWS DMS resources such as replication instances, tasks, certificates, and endpoints\. You create a template that describes all the AWS resources that you want and AWS CloudFormation provisions and configures those resources for you\.

As a developer or system administrator, you can create and manage collections of these resources that you can then use for repetitive migration tasks or deploying resources to your organization\. For more information about AWS CloudFormation, see [AWS CloudFormation concepts](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-whatis-concepts.html) in the *AWS CloudFormation User Guide\.*

AWS DMS supports creating the following AWS DMS resources using AWS CloudFormation:
+ [AWS::DMS::Certificate](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-dms-certificate.html)
+ [AWS::DMS::Endpoint](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-dms-endpoint.html)
+ [AWS::DMS::EventSubscription](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-dms-eventsubscription.html)
+ [AWS::DMS::ReplicationInstance](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-dms-replicationinstance.html)
+ [AWS::DMS::ReplicationSubnetGroup](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-dms-replicationsubnetgroup.html)
+ [AWS::DMS::ReplicationTask](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-dms-replicationtask.html)