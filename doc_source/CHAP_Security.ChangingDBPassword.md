# Changing the Database Password<a name="CHAP_Security.ChangingDBPassword"></a>

In most situations, changing the database password for your source or target endpoint is straightforward\. If you need to change the database password for an endpoint that you are currently using in a migration or replication task, the process is slightly more complex\. The procedure following shows how to do this\.

**To change the database password for an endpoint in a migration or replication task**

1. Sign in to the AWS Management Console and choose AWS DMS\. Note that if you are signed in as an AWS Identity and Access Management \(IAM\) user, you must have the appropriate permissions to access AWS DMS\. For more information on the permissions required, see [IAM Permissions Needed to Use AWS DMS](CHAP_Security.IAMPermissions.md)\.

1. In the navigation pane, choose **Tasks**\.

1. Choose the task that uses the endpoint you want to change the database password for, and then choose **Stop**\.

1. While the task is stopped, you can change the password of the database for the endpoint using the native tools you use to work with the database\.

1. Return to the DMS Management Console and choose **Endpoints** from the navigation pane\.

1. Choose the endpoint for the database you changed the password for, and then choose **Modify**\.

1. Type the new password in the **Password** box, and then choose **Modify**\.

1. Choose **Tasks** from the navigation pane\.

1. Choose the task that you stopped previously, and choose **Start/Resume**\.

1. Choose either **Start** or **Resume**, depending on how you want to continue the task, and then choose **Start task**\.