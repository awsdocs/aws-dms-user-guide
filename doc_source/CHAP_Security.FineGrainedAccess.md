# Fine\-Grained Access Control Using Resource Names and Tags<a name="CHAP_Security.FineGrainedAccess"></a>

You can use ARN\-based resource names and resource tags to manage access to AWS DMS resources\. You do this by defining permitted action or including conditional statements in IAM policies\. 

## Using Resource Names to Control Access<a name="CHAP_Security.FineGrainedAccess.ResourceName"></a>

You can create an IAM user account and assign a policy based on the AWS DMS resource's Amazon Resource Name \(ARN\)\.

The following policy denies access to the AWS DMS replication instance with the ARN *arn:aws:dms:us\-east\-1:152683116:rep:DOH67ZTOXGLIXMIHKITV*:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "dms:*"
            ],
            "Effect": "Deny",
            "Resource": "arn:aws:dms:us-east-1:152683116:rep:DOH67ZTOXGLIXMIHKITV"
        }
    ]
}
```

For example, the following commands would fail when the policy is in effect:

```
$ aws dms delete-replication-instance 
   --replication-instance-arn "arn:aws:dms:us-east-1:152683116:rep:DOH67ZTOXGLIXMIHKITV"

A client error (AccessDeniedException) occurred when calling the DeleteReplicationInstance 
operation: User: arn:aws:iam::152683116:user/dmstestusr is not authorized to perform: 
dms:DeleteReplicationInstance on resource: arn:aws:dms:us-east-1:152683116:rep:DOH67ZTOXGLIXMIHKITV

$ aws dms modify-replication-instance 
   --replication-instance-arn "arn:aws:dms:us-east-1:152683116:rep:DOH67ZTOXGLIXMIHKITV"

A client error (AccessDeniedException) occurred when calling the ModifyReplicationInstance 
operation: User: arn:aws:iam::152683116:user/dmstestusr is not authorized to perform: 
dms:ModifyReplicationInstance on resource: arn:aws:dms:us-east-1:152683116:rep:DOH67ZTOXGLIXMIHKITV
```

You can also specify IAM policies that limit access to AWS DMS endpoints and replication tasks\.

The following policy limits access to an AWS DMS endpoint using the endpoint's ARN:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "dms:*"
            ],
            "Effect": "Deny",
            "Resource": "arn:aws:dms:us-east-1:152683116:endpoint:D6E37YBXTNHOA6XRQSZCUGX"
        }
    ]
}
```

For example, the following commands would fail when the policy using the endpoint's ARN is in effect:

```
$ aws dms delete-endpoint 
   --endpoint-arn "arn:aws:dms:us-east-1:152683116:endpoint:D6E37YBXTNHOA6XRQSZCUGX"

A client error (AccessDeniedException) occurred when calling the DeleteEndpoint operation: 
User: arn:aws:iam::152683116:user/dmstestusr is not authorized to perform: dms:DeleteEndpoint 
on resource: arn:aws:dms:us-east-1:152683116:endpoint:D6E37YBXTNHOA6XRQSZCUGX

$ aws dms modify-endpoint 
   --endpoint-arn "arn:aws:dms:us-east-1:152683116:endpoint:D6E37YBXTNHOA6XRQSZCUGX"     

A client error (AccessDeniedException) occurred when calling the ModifyEndpoint operation: 
User: arn:aws:iam::152683116:user/dmstestusr is not authorized to perform: dms:ModifyEndpoint 
on resource: arn:aws:dms:us-east-1:152683116:endpoint:D6E37YBXTNHOA6XRQSZCUGX
```

The following policy limits access to an AWS DMS task using the task's ARN:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "dms:*"
            ],
            "Effect": "Deny",
            "Resource": "arn:aws:dms:us-east-1:152683116:task:UO3YR4N47DXH3ATT4YMWOIT"
        }
    ]
}
```

For example, the following commands would fail when the policy using the task's ARN is in effect:

```
$ aws dms delete-replication-task 
   --replication-task-arn "arn:aws:dms:us-east-1:152683116:task:UO3YR4N47DXH3ATT4YMWOIT"

A client error (AccessDeniedException) occurred when calling the DeleteReplicationTask operation: 
User: arn:aws:iam::152683116:user/dmstestusr is not authorized to perform: dms:DeleteReplicationTask 
on resource: arn:aws:dms:us-east-1:152683116:task:UO3YR4N47DXH3ATT4YMWOIT
```

## Using Tags to Control Access<a name="CHAP_Security.FineGrainedAccess.Tags"></a>

AWS DMS defines a set of common key/value pairs that are available for use in customer defined policies without any additional tagging requirements\. For more information about tagging AWS DMS resources, see [Tagging Resources in AWS Database Migration Service](CHAP_Tagging.md)\. 

The following lists the standard tags available for use with AWS DMS: 
+  aws:CurrentTime – Represents the request date and time, allowing the restriction of access based on temporal criteria\. 
+  aws:EpochTime – This tag is similar to the aws:CurrentTime tag above, except that the current time is represented as the number of seconds elapsed since the Unix Epoch\. 
+  aws:MultiFactorAuthPresent – This is a boolean tag that indicates whether or not the request was signed via multi\-factor authentication\. 
+  aws:MultiFactorAuthAge – Provides access to the age of the multi\-factor authentication token \(in seconds\)\. 
+  aws:principaltype \- Provides access to the type of principal \(user, account, federated user, etc\.\) for the current request\. 
+  aws:SourceIp \- Represents the source ip address for the user issuing the request\. 
+  aws:UserAgent – Provides information about the client application requesting a resource\. 
+  aws:userid – Provides access to the ID of the user issuing the request\. 
+  aws:username – Provides access to the name of the user issuing the request\. 
+  dms:InstanceClass – Provides access to the compute size of the replication instance host\(s\)\. 
+  dms:StorageSize \- Provides access to the storage volume size \(in GB\)\. 

You can also define your own tags\. Customer\-defined tags are simple key/value pairs that are persisted in the AWS Tagging service and can be added to AWS DMS resources, including replication instances, endpoints, and tasks\. These tags are matched via IAM "Conditional" statements in policies, and are referenced using a specific conditional tag\. The tag keys are prefixed with "dms", the resource type, and the "tag" prefix\. The following shows the tag format:

```
dms:{resource type}-tag/{tag key}={tag value}
```

For example, suppose you want to define a policy that only allows an API call to succeed for a replication instance that contains the tag "stage=production"\. The following conditional statement would match a resource with the given tag:

```
"Condition":
{
    "streq":
        {
            "dms:rep-tag/stage":"production"
        }
}
```

You would add the following tag to a replication instance that would match this policy condition: 

```
stage production
```

In addition to tags already assigned to AWS DMS resources, policies can also be written to limit the tag keys and values that may be applied to a given resource\. In this case, the tag prefix would be "req"\. 

For example, the following policy statement would limit the tags that a user can assign to a given resource to a specific list of allowed values: 

```
 "Condition":
{
    "streq":
        {
            "dms:req-tag/stage": [ "production", "development", "testing" ]
        }
}
```

The following policy examples limit access to an AWS DMS resource based on resource tags\.

The following policy limits access to a replication instance where the tag value is "Desktop" and the tag key is "Env":

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "dms:*"
            ],
            "Effect": "Deny",
            "Resource": "*",
            "Condition": {
                "StringEquals": {
                    "dms:rep-tag/Env": [
                        "Desktop"
                    ]
                }
            }
        }
    ]
}
```

The following commands succeed or fail based on the IAM policy that restricts access when the tag value is "Desktop" and the tag key is "Env":

```
$ aws dms list-tags-for-resource 
   --resource-name arn:aws:dms:us-east-1:152683116:rep:46DHOU7JOJYOJXWDOZNFEN 
   --endpoint-url http://localhost:8000                                   
{
    "TagList": [
        {
            "Value": "Desktop", 
            "Key": "Env"
        }
    ]
}

$ aws dms delete-replication-instance 
   --replication-instance-arn "arn:aws:dms:us-east-1:152683116:rep:46DHOU7JOJYOJXWDOZNFEN"
A client error (AccessDeniedException) occurred when calling the DeleteReplicationInstance 
operation: User: arn:aws:iam::152683116:user/dmstestusr is not authorized to perform: 
dms:DeleteReplicationInstance on resource: arn:aws:dms:us-east-1:152683116:rep:46DHOU7JOJYOJXWDOZNFEN

$ aws dms modify-replication-instance 
   --replication-instance-arn "arn:aws:dms:us-east-1:152683116:rep:46DHOU7JOJYOJXWDOZNFEN" 

A client error (AccessDeniedException) occurred when calling the ModifyReplicationInstance 
operation: User: arn:aws:iam::152683116:user/dmstestusr is not authorized to perform: 
dms:ModifyReplicationInstance on resource: arn:aws:dms:us-east-1:152683116:rep:46DHOU7JOJYOJXWDOZNFEN

$ aws dms add-tags-to-resource 
   --resource-name arn:aws:dms:us-east-1:152683116:rep:46DHOU7JOJYOJXWDOZNFEN 
   --tags Key=CostCenter,Value=1234 

A client error (AccessDeniedException) occurred when calling the AddTagsToResource 
operation: User: arn:aws:iam::152683116:user/dmstestusr is not authorized to perform: 
dms:AddTagsToResource on resource: arn:aws:dms:us-east-1:152683116:rep:46DHOU7JOJYOJXWDOZNFEN

$ aws dms remove-tags-from-resource 
   --resource-name arn:aws:dms:us-east-1:152683116:rep:46DHOU7JOJYOJXWDOZNFEN 
   --tag-keys Env             

A client error (AccessDeniedException) occurred when calling the RemoveTagsFromResource 
operation: User: arn:aws:iam::152683116:user/dmstestusr is not authorized to perform: 
dms:RemoveTagsFromResource on resource: arn:aws:dms:us-east-1:152683116:rep:46DHOU7JOJYOJXWDOZNFEN
```

The following policy limits access to a AWS DMS endpoint where the tag value is "Desktop" and the tag key is "Env":

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "dms:*"
            ],
            "Effect": "Deny",
            "Resource": "*",
            "Condition": {
                "StringEquals": {
                    "dms:endpoint-tag/Env": [
                        "Desktop"
                    ]
                }
            }
        }
    ]
}
```

The following commands succeed or fail based on the IAM policy that restricts access when the tag value is "Desktop" and the tag key is "Env":

```
$ aws dms list-tags-for-resource 
   --resource-name arn:aws:dms:us-east-1:152683116:endpoint:J2YCZPNGOLFY52344IZWA6I
{
    "TagList": [
        {
            "Value": "Desktop", 
            "Key": "Env"
        }
    ]
}

$ aws dms delete-endpoint 
   --endpoint-arn "arn:aws:dms:us-east-1:152683116:endpoint:J2YCZPNGOLFY52344IZWA6I"

A client error (AccessDeniedException) occurred when calling the DeleteEndpoint 
operation: User: arn:aws:iam::152683116:user/dmstestusr is not authorized to perform: 
dms:DeleteEndpoint on resource: arn:aws:dms:us-east-1:152683116:endpoint:J2YCZPNGOLFY52344IZWA6I

$ aws dms modify-endpoint 
   --endpoint-arn "arn:aws:dms:us-east-1:152683116:endpoint:J2YCZPNGOLFY52344IZWA6I"    

A client error (AccessDeniedException) occurred when calling the ModifyEndpoint 
operation: User: arn:aws:iam::152683116:user/dmstestusr is not authorized to perform: 
dms:ModifyEndpoint on resource: arn:aws:dms:us-east-1:152683116:endpoint:J2YCZPNGOLFY52344IZWA6I

$ aws dms add-tags-to-resource 
   --resource-name arn:aws:dms:us-east-1:152683116:endpoint:J2YCZPNGOLFY52344IZWA6I 
   --tags Key=CostCenter,Value=1234

A client error (AccessDeniedException) occurred when calling the AddTagsToResource 
operation: User: arn:aws:iam::152683116:user/dmstestusr is not authorized to perform: 
dms:AddTagsToResource on resource: arn:aws:dms:us-east-1:152683116:endpoint:J2YCZPNGOLFY52344IZWA6I

$ aws dms remove-tags-from-resource 
   --resource-name arn:aws:dms:us-east-1:152683116:endpoint:J2YCZPNGOLFY52344IZWA6I 
   --tag-keys Env

A client error (AccessDeniedException) occurred when calling the RemoveTagsFromResource 
operation: User: arn:aws:iam::152683116:user/dmstestusr is not authorized to perform: 
dms:RemoveTagsFromResource on resource: arn:aws:dms:us-east-1:152683116:endpoint:J2YCZPNGOLFY52344IZWA6I
```

The following policy limits access to a replication task where the tag value is "Desktop" and the tag key is "Env":

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "dms:*"
            ],
            "Effect": "Deny",
            "Resource": "*",
            "Condition": {
                "StringEquals": {
                    "dms:task-tag/Env": [
                        "Desktop"
                    ]
                }
            }
        }
    ]
}
```

The following commands succeed or fail based on the IAM policy that restricts access when the tag value is "Desktop" and the tag key is "Env":

```
$ aws dms list-tags-for-resource 
   --resource-name arn:aws:dms:us-east-1:152683116:task:RB7N24J2XBUPS3RFABZTG3
{
    "TagList": [
        {
            "Value": "Desktop", 
            "Key": "Env"
        }
    ]
}

$ aws dms delete-replication-task 
   --replication-task-arn "arn:aws:dms:us-east-1:152683116:task:RB7N24J2XBUPS3RFABZTG3"

A client error (AccessDeniedException) occurred when calling the DeleteReplicationTask 
operation: User: arn:aws:iam::152683116:user/dmstestusr is not authorized to perform: 
dms:DeleteReplicationTask on resource: arn:aws:dms:us-east-1:152683116:task:RB7N24J2XBUPS3RFABZTG3

$ aws dms add-tags-to-resource 
   --resource-name arn:aws:dms:us-east-1:152683116:task:RB7N24J2XBUPS3RFABZTG3 
   --tags Key=CostCenter,Value=1234

A client error (AccessDeniedException) occurred when calling the AddTagsToResource 
operation: User: arn:aws:iam::152683116:user/dmstestusr is not authorized to perform: 
dms:AddTagsToResource on resource: arn:aws:dms:us-east-1:152683116:task:RB7N24J2XBUPS3RFABZTG3

$ aws dms remove-tags-from-resource 
   --resource-name arn:aws:dms:us-east-1:152683116:task:RB7N24J2XBUPS3RFABZTG3 
   --tag-keys Env

A client error (AccessDeniedException) occurred when calling the RemoveTagsFromResource 
operation: User: arn:aws:iam::152683116:user/dmstestusr is not authorized to perform: 
dms:RemoveTagsFromResource on resource: arn:aws:dms:us-east-1:152683116:task:RB7N24J2XBUPS3RFABZTG3
```