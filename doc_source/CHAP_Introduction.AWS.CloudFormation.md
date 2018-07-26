# AWS DMS Support for AWS CloudFormation<a name="CHAP_Introduction.AWS.CloudFormation"></a>

You can provision AWS Database Migration Service resources using AWS CloudFormation\. AWS CloudFormation is a service that helps you model and set up your AWS resources for infrastructure management or deployment\. For example, you can provision AWS DMS resources such as replication instances, tasks, certificates, and endpoints\. You create a template that describes all the AWS resources that you want and AWS CloudFormation provisions and configures those resources for you\.

As a developer or system administrator, you can create and manage collections of these resources that you can then use for repetitive migration tasks or deploying resources to your organization\. For more information about AWS CloudFormation, see [AWS CloudFormation Concepts](http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-whatis-concepts.html) in the *AWS CloudFormation User Guide\.*

AWS DMS supports creating the following AWS DMS resources using AWS CloudFormation:
+ [AWS::DMS::Certificate](http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-dms-certificate.html)
+ [AWS::DMS::Endpoint](http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-dms-endpoint.html)
+ [AWS::DMS::EventSubscription](http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-dms-eventsubscription.html)
+ [AWS::DMS::ReplicationInstance](http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-dms-replicationinstance.html)
+ [AWS::DMS::ReplicationSubnetGroup](http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-dms-replicationsubnet-group.html)
+ [AWS::DMS::ReplicationTask](http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-dms-replicationtask.html)