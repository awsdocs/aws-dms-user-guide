# Constructing an Amazon Resource Name \(ARN\) for AWS DMS<a name="CHAP_Introduction.AWS.ARN"></a>

If you use the AWS CLI or AWS DMS API to automate your database migration, then you work with Amazon Resource Name \(ARNs\)\. Each resource that is created in Amazon Web Services is identified by an ARN, which is a unique identifier\. If you use the AWS CLI or AWS DMS API to set up your database migration, you supply the ARN of the resource that you want to work with\. 

An ARN for an AWS DMS resource uses the following syntax:

`arn:aws:dms:region:account number:resourcetype:resourcename`

In this syntax, the following apply:
+ *`region`* is the ID of the AWS Region where the AWS DMS resource was created, such as `us-west-2`\.

  The following table shows AWS Region names and the values that you should use when constructing an ARN\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Introduction.AWS.ARN.html)
+ `account number` is your account number with dashes omitted\. To find your account number, sign in to your AWS account at http://aws\.amazon\.com, choose **My Account/Console**, and then choose **My Account**\.
+ **`resourcetype`** is the type of AWS DMS resource\.

  The following table shows the resource types to use when constructing an ARN for a particular AWS DMS resource\.     
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Introduction.AWS.ARN.html)
+ *`resourcename`* is the resource name assigned to the AWS DMS resource\. This is a generated arbitrary string\.

The following table shows examples of ARNs for AWS DMS resources\. Here, we assume an AWS account of 123456789012, which were created in the US East \(N\. Virginia\) Region, and has a resource name\. 

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Introduction.AWS.ARN.html)