# Using service\-linked roles for AWS DMS<a name="using-service-linked-roles"></a>

AWS Database Migration Service uses AWS Identity and Access Management \(IAM\) [service\-linked roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_terms-and-concepts.html#iam-term-service-linked-role)\. A service\-linked role is a unique type of IAM role that is linked directly to AWS DMS\. Service\-linked roles are predefined by AWS DMS and include all the permissions that the service requires to call other AWS services on your behalf\. 

A service\-linked role makes setting up AWS DMS easier because you don’t have to manually add the necessary permissions\. AWS DMS defines the permissions of its service\-linked roles, and unless defined otherwise, only AWS DMS can assume its roles\. The defined permissions include the trust policy and the permissions policy, and that permissions policy can't be attached to any other IAM entity\.

You can delete a service\-linked role only after first deleting their related resources\. This protects your AWS DMS resources because you can't inadvertently remove permission to access the resources\.

For information about other services that support service\-linked roles, see [AWS Services That Work with IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_aws-services-that-work-with-iam.html) and look for the services that have **Yes **in the **Service\-linked roles** column\. Choose a **Yes** with a link to view the service\-linked role documentation for that service\.

**Service\-linked roles for AWS DMS features**

**Topics**
+ [Service\-linked roles for AWS DMS Fleet Advisor](slr-services-fa.md)
+ [Service\-linked role for AWS DMS Serverless](slr-services-sl.md)