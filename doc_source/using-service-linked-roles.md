# Using service\-linked roles for DMS Fleet Advisor<a name="using-service-linked-roles"></a>

AWS DMS Fleet Advisor uses AWS Identity and Access Management \(IAM\) [service\-linked roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_terms-and-concepts.html#iam-term-service-linked-role)\. A service\-linked role is a unique type of IAM role that is linked directly to DMS Fleet Advisor\. Service\-linked roles are predefined by DMS Fleet Advisor and include all the permissions that the service requires to call other AWS services on your behalf\. 

A service\-linked role makes setting up DMS Fleet Advisor easier because you don’t have to manually add the necessary permissions\. DMS Fleet Advisor defines the permissions of its service\-linked roles, and unless defined otherwise, only DMS Fleet Advisor can assume its roles\. The defined permissions include the trust policy and the permissions policy, and that permissions policy can't be attached to any other IAM entity\.

You can delete a service\-linked role only after first deleting their related resources\. This protects your DMS Fleet Advisor resources because you can't inadvertently remove permission to access the resources\.

For information about other services that support service\-linked roles, see [AWS Services That Work with IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_aws-services-that-work-with-iam.html) and look for the services that have **Yes **in the **Service\-linked roles** column\. Choose a **Yes** with a link to view the service\-linked role documentation for that service\.

## Service\-linked role permissions for DMS Fleet Advisor<a name="slr-permissions"></a>

DMS Fleet Advisor uses the service\-linked role named **AWSServiceRoleForDMSFleetAdvisor** – DMS Fleet Advisor uses this service\-linked role to manage Amazon CloudWatch metrics\. This service\-linked role is attached to the following managed policy: `AWSDMSFleetAdvisorServiceRolePolicy`\. For updates to this policy, see [AWS managed policies for AWS Database Migration Service](security-iam-awsmanpol.md)\.

The AWSServiceRoleForDMSFleetAdvisor service\-linked role trusts the following services to assume the role:
+ `dms-fleet-advisor.amazonaws.com`

The role permissions policy named AWSDMSFleetAdvisorServiceRolePolicy allows DMS Fleet Advisor to complete the following actions on the specified resources:
+ Action: `cloudwatch:PutMetricData` on `all AWS resources`

  This permission allows principals to publish metric data points to Amazon CloudWatch\. AWS DMS Fleet Advisor requires this permission to display charts with database metrics from CloudWatch\.

The following code example shows the AWSDMSFleetAdvisorServiceRolePolicy policy that you use to create the AWSDMSFleetAdvisorServiceRolePolicy role\.

```
{
    "Version": "2012-10-17",
    "Statement": {
        "Effect": "Allow",
        "Resource": "*",
        "Action": "cloudwatch:PutMetricData",
        "Condition": {
            "StringEquals": {
                "cloudwatch:namespace": "AWS/DMS/FleetAdvisor"
            }
        }
    }
}
```

You must configure permissions to allow an IAM entity, such as a user, group, or role, to create, edit, or delete a service\-linked role\. For more information, see [Service\-linked role permissions](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html#service-linked-role-permissions) in the *IAM User Guide*\.

## Creating a service\-linked role for DMS Fleet Advisor<a name="create-slr"></a>

You can use the IAM console to create a service\-linked role with the **DMS – Fleet Advisor** use case\. In the AWS CLI or the AWS API, create a service\-linked role with the `dms-fleet-advisor.amazonaws.com` service name\. For more information, see [Creating a service\-linked role](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html#create-service-linked-role) in the *IAM User Guide*\. If you delete this service\-linked role, you can use this same process to create the role again\.

Make sure that you create this role before you create a data collector\. DMS Fleet Advisor uses this role to display charts with database metrics in the AWS Management Console\. For more information, see [Creating a data collector](fa-data-collectors-create.md)\.

## Editing a service\-linked role for DMS Fleet Advisor<a name="edit-slr"></a>

DMS Fleet Advisor doesn't allow you to edit the AWSServiceRoleForDMSFleetAdvisor service\-linked role\. After you create a service\-linked role, you can't change the name of the role because various entities might reference the role\. However, you can edit the description of the role using IAM\. For more information, see [Editing a service\-linked role](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html#edit-service-linked-role) in the *IAM User Guide*\.

## Deleting a service\-linked role for DMS Fleet Advisor<a name="delete-slr"></a>

If you no longer need to use a feature or service that requires a service\-linked role, we recommend that you delete that role\. Thus, you don’t have an unused entity that isn't actively monitored or maintained\. However, you must clean up the resources for your service\-linked role before you can manually delete it\.

**Note**  
If the DMS Fleet Advisor service is using the role when you try to delete the resources, then the deletion might fail\. If that happens, wait for a few minutes and try the operation again\.

**To delete DMS Fleet Advisor resources used by the AWSServiceRoleForDMSFleetAdvisor**

1. Sign in to the AWS Management Console and open the AWS DMS console at [https://console\.aws\.amazon\.com/dms/v2/](https://console.aws.amazon.com/https://console.aws.amazon.com/dms/v2/)\.

1. In the navigation pane, choose **Data collectors** under **Discover**\. The **Data collectors** page opens\.

1. Choose your data collector and choose **Delete**\.

1. To confirm deletion, enter the data collector name in the text input field\. Next, choose **Delete**\.

**Important**  
When you delete a DMS data collector, DMS Fleet Advisor deletes all databases from Inventory that you discovered using this collector\.

After you delete all data collectors, you can delete the service\-linked role\.

**To manually delete the service\-linked role using IAM**

Use the IAM console, the AWS CLI, or the AWS API to delete the AWSServiceRoleForDMSFleetAdvisor service\-linked role\. For more information, see [Deleting a service\-linked role](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html#delete-service-linked-role) in the *IAM User Guide*\.

## Supported regions for DMS Fleet Advisor service\-linked roles<a name="slr-regions"></a>

DMS Fleet Advisor supports using service\-linked roles in all of the regions where the service is available\. For more information, see [Supported AWS Regions](CHAP_FleetAdvisor.md#CHAP_FleetAdvisor.SupportedRegions)\.