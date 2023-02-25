# Infrastructure security in AWS Database Migration Service<a name="infrastructure-security"></a>

As a managed service, AWS Database Migration Service is protected by the AWS global network security procedures that are described in the [Amazon Web Services: Overview of security processes](https://d0.awsstatic.com/whitepapers/Security/AWS_Security_Whitepaper.pdf) whitepaper\.

You use AWS published API calls to access AWS DMS through the network\. Clients must support Transport Layer Security \(TLS\) 1\.0 or later\. Clients must also support cipher suites with perfect forward secrecy \(PFS\) such as Ephemeral Diffie\-Hellman \(DHE\) or Elliptic Curve Ephemeral Diffie\-Hellman \(ECDHE\)\. Most modern systems such as Java 7 and later support these modes\.

Additionally, requests must be signed by using an access key ID and a secret access key that is associated with an AWS Identity and Access Management \(IAM\) principal\. Or you can use the [AWS Security Token Service](https://docs.aws.amazon.com/STS/latest/APIReference/Welcome.html) \(AWS STS\) to generate temporary security credentials to sign requests\.

You can call these API operations from any network location\. AWS DMS also supports resource\-based access policies, which can specify restrictions on actions and resources, for example, based on the source IP address\. In addition, you can use AWS DMS policies to control access from specific Amazon VPC endpoints or specific virtual private clouds \(VPCs\)\. Effectively, this isolates network access to a given AWS DMS resource from only the specific VPC within the AWS network\. For more information about using resource\-based access policies with AWS DMS, including examples, see [Fine\-grained access control using resource names and tags](CHAP_Security.FineGrainedAccess.md)\.

To confine your communications with AWS DMS within a single VPC, you can create a VPC interface endpoint that enables you to connect to AWS DMS through AWS PrivateLink\. AWS PrivateLink helps ensure that any call to AWS DMS and its associated results remain confined to the specific VPC for which your interface endpoint is created\. You can then specify the URL for this interface endpoint as an option with every AWS DMS command that you run using the AWS CLI or an SDK\. Doing this helps ensure that your entire communications with AWS DMS remain confined to the VPC and are otherwise invisible to the public internet\.

**To create an interface endpoint to access DMS in a single VPC**

1. Sign in to the AWS Management Console and open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. From the navigation pane, choose **Endpoints**\. This opens the **Create endpoints** page, where you can create the interface endpoint from a VPC to AWS DMS\.

1. Choose **AWS services**, then search for and choose a value for **Service Name**, in this case AWS DMS in the following form\.

   ```
   com.amazonaws.region.dms
   ```

   Here, *`region`* specifies the AWS Region where AWS DMS runs, for example `com.amazonaws.us-west-2.dms`\.

1. For **VPC**, choose the VPC to create the interface endpoint from, for example `vpc-12abcd34`\.

1. Choose a value for **Availability Zone** and for **Subnet ID**\. These values should indicate a location where your chosen AWS DMS endpoint can run, for example `us-west-2a (usw2-az1)` and `subnet-ab123cd4`\.

1. Choose **Enable DNS name** to create the endpoint with a DNS name\. This DNS name consists of the endpoint ID \(`vpce-12abcd34efg567hij`\) hyphenated with a random string \(`ab12dc34`\)\. These are separated from the service name by a dot in reverse dot\-separated order, with `vpce` added \(`dms.us-west-2.vpce.amazonaws.com`\)\. 

   An example is `vpce-12abcd34efg567hij-ab12dc34.dms.us-west-2.vpce.amazonaws.com`\.

1. For **Security group**, choose a group to use for the endpoint\.

   When you set up your security group, make sure to allow outbound HTTPS calls from within it\. For more information, see [Creating security groups](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html#CreatingSecurityGroups) in the *Amazon VPC User Guide*\. 

1. Choose either **Full Access** or a custom value for **Policy**\. For example, you might choose a custom policy similar to the following that restricts your endpoint's access to certain actions and resources\.

   ```
   {
     "Statement": [
       {
         "Action": "dms:*",
         "Effect": "Allow",
         "Resource": "*",
         "Principal": "*"
       },
       {
         "Action": [
           "dms:ModifyReplicationInstance",
           "dms:DeleteReplicationInstance"
         ],
         "Effect": "Deny",
         "Resource": "arn:aws:dms:us-west-2:<account-id>:rep:<replication-instance-id>",
         "Principal": "*"
       }
     ]
   }
   ```

   Here, the sample policy allows any AWS DMS API call, except for deleting or modifying a specific replication instance\.

You can now specify a URL formed using the DNS name created in step 6 as an option\. You specify this for every AWS DMS CLI command or API operation to access the service instance using the created interface endpoint\. For example, you might run the DMS CLI command `DescribeEndpoints` in this VPC as shown following\.

```
$ aws dms describe-endpoints --endpoint-url https://vpce-12abcd34efg567hij-ab12dc34.dms.us-west-2.vpce.amazonaws.com
```

If you enable the private DNS option, you don't have to specify the endpoint URL in the request\.

For more information on creating and using VPC interface endpoints \(including enabling the private DNS option\), see [Interface VPC endpoints \(AWS PrivateLink\)](https://docs.aws.amazon.com/vpc/latest/userguide/vpce-interface.html) in the *Amazon VPC User Guide*\.