# Working with the AWS DMS diagnostic support AMI<a name="CHAP_SupportAmi"></a>

If you encounter a network\-related issue when working with AWS DMS, your support engineer might need more information about your network configuration\. We want to make sure that AWS Support gets as much of the required information as possible in the shortest possible time\. Therefore, we developed a prebuilt Amazon EC2 AMI with diagnostic tools to test your AWS DMS networking environment\.

The diagnostic tests installed on the Amazon machine image \(AMI\) include the following:
+ Virtual Private Cloud \(VPC\)
+ Network packet loss
+ Network latency
+ Maximum Transmission Unit \(MTU\) size

**Topics**
+ [Launch a new AWS DMS diagnostic Amazon EC2 instance](#CHAP_SupportAmi_Launch)
+ [Create an IAM role](#CHAP_SupportAmi_Iam)
+ [Run Diagnostic Tests](#CHAP_SupportAmi_Run)
+ [Next Steps](#CHAP_SupportAmi_NextSteps)
+ [AMI IDs by region](#CHAP_SupportAmi_AmiIDs)

**Note**  
If you experience performance issues with your Oracle source, you can evaluate the read performance of your Oracle redo or archive logs to find ways to improve performance\. For more information, see [Evaluating read performance of Oracle redo or archive logs](CHAP_Troubleshooting.md#CHAP_Troubleshooting.Oracle.ReadPerformUtil)\.

## Launch a new AWS DMS diagnostic Amazon EC2 instance<a name="CHAP_SupportAmi_Launch"></a>

In this section, you launch a new Amazon EC2 instance\. For information about how to launch an Amazon EC2 instance, see [Get started with Amazon EC2 Linux instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EC2_GetStarted.html) tutorial in the [Amazon EC2 User Guide](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/)\. 

Launch an Amazon EC2 instance with the following settings:
+ For **Application and OS Images \(Amazon Machine Image\)**, search for the **DMS\-DIAG\-AMI** AMI\. If you are logged on to the console, you can search for the AMI with [this query](https://us-east-1.console.aws.amazon.com/ec2/home?region=us-east-1#Images:visibility=public-images;search=:dms-diag-ami;v=3;) For the AMI ID of the AWS Diagnostic AMI in your region, see [AMI IDs by region](#CHAP_SupportAmi_AmiIDs) following\. 
+ For **Instance type**, we recommend you choose **t2\.micro**\.
+ For **Network Settings**, choose the same VPC that your replication instance uses\.

After the instance is active, connect to the instance\. For information about connecting to an Amazon EC2 Linux instance, see [Connect to your Linux instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstances.html)\.

## Create an IAM role<a name="CHAP_SupportAmi_Iam"></a>

If you want to run the diagnostic tests on your replication instance using the minimum required permissions, create an IAM role that uses the following permissions policy:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
                "dms:DescribeEndpoints",
                "dms:DescribeTableStatistics",
                "dms:DescribeReplicationInstances",
                "dms:DescribeReplicationTasks",
                "secretsmanager:GetSecretValue"
            ],
            "Resource": "*"
        }
    ]
}
```

Attach the role to a new IAM user\. For information about creating IAM roles, policies, and users, see the following sections in the [IAM User Guide](https://docs.aws.amazon.com/IAM/latest/UserGuide/):
+ [Getting Started with IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started.html)
+ [Creating IAM Roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create.html)
+ [Creating IAM Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create.html)

## Run Diagnostic Tests<a name="CHAP_SupportAmi_Run"></a>

After you have created an Amazon EC2 instance and connected to it, do the following to run diagnostic tests on your replication instance\.

1. Configure the AWS CLI:

   ```
   $ aws configure
   ```

   Provide the access credentials for the AWS user account you want to use to run the diagnostic tests\. Provide the Region for your VPC and replication instance\.

1. Display the available AWS DMS tasks in your Region\. Replace the sample Region with your Region\.

   ```
   $ dms-report -r us-east-1 -l
   ```

   This command displays the status of your tasks\.  
![\[Diagnostic tool showing task list.\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-diagami1.png)

1. Display task endpoints and settings\. Replace *<DMS\-Task\-ARN>* with your task Amazon Resource Name \(ARN\)\.

   ```
   $ dms-report -t <DMS-Task-ARN>
   ```

   This command displays the endpoints and settings of your task\.  
![\[Diagnostic tool showing endpoint list for task.\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-diagami2.png)

1. Run diagnostic tests\. Replace *<DMS\-Task\-ARN>* with your task ARN\.

   ```
   $ dms-report -t <DMS-Task-ARN> -n y
   ```

   This command displays diagnostic data about your replication instance's VPC, network packet transmission, network latency, and network Maximum Transmission Unit \(MTU\) size\.  
![\[Diagnostic tool showing network data.\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-diagami3.png)

## Next Steps<a name="CHAP_SupportAmi_NextSteps"></a>

The following sections describe troubleshooting information based on the results of the network diagnostic tests:

### VPC tests<a name="CHAP_SupportAmi_NextSteps_Vpc"></a>

This test verifies that the diagnostic Amazon EC2 instance is in the same VPC as the replication instance\. If the diagnostic Amazon EC2 instance is not in the same VPC as your replication instance, terminate it and create it again in the correct VPC\. You can't change the VPC of an Amazon EC2 instance after you create it\.

### Network packet loss tests<a name="CHAP_SupportAmi_NextSteps_Npl"></a>

This test sends 10 packets to the following endpoints and checks for packet loss: 
+ The AWS DMS Amazon EC2 metadata service on port 80
+ The source endpoint
+ The target endpoint

All packets should arrive successfully\. If any packets are lost, consult with a network engineer to determine the problem and find a solution\.

### Network latency tests<a name="CHAP_SupportAmi_NextSteps_Nl"></a>

This test sends 10 packets to the same endpoints as the previous test, and checks for packet latency\. All packets should have a latency of less than 100 milliseconds\. If any packets have a latency greater than 100 milliseconds, consult with a network engineer to determine the problem and find a solution\.

### Maximum Transmission Unit \(MTU\) size tests<a name="CHAP_SupportAmi_NextSteps_Mtu"></a>

This test detects the MTU size by using the Traceroute tool on the same endpoints as the previous test\. All of the packets in the test should have the same MTU size\. If any packets have a different MTU size, consult with a system specialist to determine the problem and find a solution\.

## AMI IDs by region<a name="CHAP_SupportAmi_AmiIDs"></a>

The following table shows the AMI ID for the AMS Diagnostic AMI in your region:


****  

| Region | Image ID | 
| --- | --- | 
| US East \(Ohio\) | ami\-0f9c6801e8cab9399 | 
| US West \(Oregon\) | ami\-01ceeab889d966e16 | 
| US East \(N\. Virginia\) | ami\-0617285c8257e8f6e | 
| US West \(N\. California\) | ami\-0983b989d0a31ed55 | 
| Canada \(Central\) | ami\-0ba9380b30eee288e | 
| Europe \(Ireland\) | ami\-05959fe4fcc448f01 | 
| Europe \(London\) | ami\-0de91dfa18b365c04 | 
| Europe \(Paris\) | ami\-0844ecd26f2cc9073 | 
| Europe \(Frankfurt\) | ami\-09bc69e6da9f3adf4 | 
| Europe \(Stockholm\) | ami\-01da340b09bf5ab78 | 
| Europe \(Milan\) | ami\-0dcfc51c17cfe7727 | 
| Europe \(Spain\) | ami\-0461c0b2e391c1280 | 
| Asia Pacific \(Singapore\) | ami\-04150ffe00fe300d6 | 
| Asia Pacific \(Sydney\) | ami\-049c1d1a9d72bafce | 
| Asia Pacific \(Jakarta\) | ami\-02189a4b8d3b2ee23 | 
| Asia Pacific \(Hong Kong\) | ami\-0543ae06eff17cb07 | 
| Asia Pacific \(Tokyo\) | ami\-07b91e8e25d7b7890 | 
| Asia Pacific \(Seoul\) | ami\-0657ad8cb9b5cf71c | 
| Asia Pacific \(Osaka\) | ami\-01ccf3e974933afdc | 
| Asia Pacific \(Mumbai\) | ami\-0d9fa24bbb8225e60 | 
| Middle East \(UAE\) | ami\-0e3dcf51dfe58929b | 
| Middle East \(Bahrain\) | ami\-0faad6f64101607f5 | 
| South America \(São Paulo\) | ami\-097dd866777097d96 | 