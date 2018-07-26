# Using AWS DMS with Other AWS Services<a name="CHAP_Introduction.AWS"></a>

You can use AWS DMS with several other AWS services :
+ You can use an Amazon EC2 instance or Amazon RDS DB instance as a target for a data migration\.
+ You can use the AWS Schema Conversion Tool \(AWS SCT\) to convert you source schema and SQL code into an equivalent target schema and SQL code\.
+ You can use Amazon S3 as a storage site for your data or you can use it as an intermediate step when migrating large amounts of data\.
+ You can use AWS CloudFormation to set up your AWS resources for infrastructure management or deployment\. For example, you can provision AWS DMS resources such as replication instances, tasks, certificates, and endpoints\. You create a template that describes all the AWS resources that you want, and AWS CloudFormation provisions and configures those resources for you\.