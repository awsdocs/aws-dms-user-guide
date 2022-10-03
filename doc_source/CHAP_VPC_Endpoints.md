# Configuring VPC endpoints as AWS DMS source and target endpoints<a name="CHAP_VPC_Endpoints"></a>

AWS DMS supports Amazon virtual private cloud \(VPC\) endpoints as sources and targets\. AWS DMS can connect to any AWS source or target database with Amazon VPC endpoints as long as explicitly defined routes to these source and target databases are defined in their AWS DMS VPC\.

By supporting Amazon VPC endpoints, AWS DMS makes it easier to maintain end\-to\-end network security for all replication tasks without additional networking configuration and setup\. Using VPC endpoints for all source and target endpoints ensures that all your traffic remains within your VPC and under your control\. Upgrades to AWS DMS versions 3\.4\.7 and higher require that you configure AWS DMS to use VPC endpoints or to use public routes to all your source and target endpoints that interact with the Amazon Web Services following:
+ Amazon S3
+ Amazon Kinesis
+ AWS Secrets Manager
+ Amazon DynamoDB
+ Amazon Redshift
+ Amazon OpenSearch Service

You might need VPC endpoints to support AWS DMS starting with version 3\.4\.7 as described following\.

## Who is impacted when migrating to AWS DMS versions 3\.4\.7 and higher?<a name="CHAP_VPC_Endpoints.Users_Impacted"></a>

You are impacted if you are using one or more of the previously listed AWS DMS endpoints, and these endpoints are not publicly routable or they don’t have VPC endpoints already associated with them\.

## Who is not impacted when migrating to AWS DMS versions 3\.4\.7 and higher?<a name="CHAP_VPC_Endpoints.Users_Not_Impacted"></a>

You are not impacted if:
+ You aren't using one or more of the previously listed AWS DMS endpoints\.
+ You are using any of the previously listed endpoints and they are publically routable\.
+ You are using any of the previously listed endpoints and they have VPC endpoints associated with them\.

## Preparing a migration to AWS DMS versions 3\.4\.7 and higher<a name="CHAP_VPC_Endpoints.User_Mitigation"></a>

To prevent AWS DMS task failures when you are using any of the endpoints described previously, take one of the steps following prior to upgrading AWS DMS to version 3\.4\.7 or higher:
+ Make the impacted AWS DMS endpoints publicly routable\. For example, add an Internet Gateway \(IGW\) route to any VPC already used by your AWS DMS replication instance to make all its source and target endpoints publicly routable\.
+ Create VPC endpoints to access all source and target endpoints used by AWS DMS as described following\.

For any existing VPC endpoints that you use for your AWS DMS source and target endpoints, ensure that they use a trust policy that conforms with the XML policy document, `dms-vpc-role`\. For more information on this XML policy document, see [Creating the IAM roles to use with the AWS CLI and AWS DMS API](CHAP_Security.md#CHAP_Security.APIRole)\.

**Otherwise, to ensure that all source and target endpoints used by your AWS DMS replication instances are configured as VPC endpoints:**

1. Sign in to the AWS Management Console and open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. On the VPC console menu bar, choose the same AWS Region as your AWS DMS replication instance\.

1. In the VPC navigation pane, choose **Endpoints**\.

1. In **Endpoints**, choose **Create endpoint**\.

1. You can optionally specify a name tag\. For example, **my\-endpoint\-DynamoDB\-01**\.

1. Under **Services** for S3 or DynamoDB only, choose a **Service Name** with its **Type** set to **Gateway**\.

1. Under **VPC**, choose the same VPC as our AWS DMS replication instance to create the endpoint\.

1. Under **Route Tables**, choose all available **Route Table ID** values\.

1. To specify access control, under **Policy**, choose **Full access**\. If you want to use a policy creation tool to specify your own access control, choose **Custom**\. In any case, use a trust policy that conforms with the XML policy document, `dms-vpc-role`\. For more information on this XML policy document, see [Creating the IAM roles to use with the AWS CLI and AWS DMS API](CHAP_Security.md#CHAP_Security.APIRole)\.

1. Under **Endpoints**, verify that your newly created VPC endpoint **Status** is **Available**\.

For more information on configuring VPC endpoints for an AWS DMS replication instance, see [Network configurations for database migration](CHAP_ReplicationInstance.VPC.md#CHAP_ReplicationInstance.VPC.Configurations)\. For more information on creating interface VPC endpoints for accessing AWS services generally, see [Access an AWS service using an interface VPC endpoint](https://docs.aws.amazon.com/vpc/latest/privatelink/create-interface-endpoint.html) in the *AWS PrivateLink Guide*\. For information on AWS DMS regional availability for VPC endpoints, see the [AWS Region Table](http://aws.amazon.com/about-aws/global-infrastructure/regional-product-services/)\.