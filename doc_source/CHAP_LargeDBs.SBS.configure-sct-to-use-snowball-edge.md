# Step 7: Configure AWS SCT to use Snowball Edge<a name="CHAP_LargeDBs.SBS.configure-sct-to-use-snowball-edge"></a>

Your AWS SCT service profile must be updated to use the AWS DMS agent, which is a local AWS DMS that works on\-premises\.

**To update the AWS SCT profile to work with the AWS DMS agent**

1. Start AWS SCT\.

1. Choose **Settings**, **Global Settings**, **AWS Service Profiles**\.

1. Choose **Add New AWS Service Profile**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/dms/latest/userguide/images/snowball-AWSserviceprofile.png)

1. Add the following profile information\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_LargeDBs.SBS.configure-sct-to-use-snowball-edge.html)

1. After you have entered the information, choose **Test Connection** to verify that AWS SCT can connect to the Amazon S3 bucket\.

   The **OLTP Local & AWS DMS Data Migration** section in the pop\-up window should show all entries with a status of **Pass**\. If the test fails, the failure is probably because the account you are using is missing privileges to access the Amazon S3 bucket\.

1. If the test passes, choose **OK** and then **OK** again to close the window and dialog box\.

1. Choose **Import job**, choose the Snowball Edge job from the list, and then choose **OK**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/dms/latest/userguide/images/snowball-import-job.png)

Now configure AWS SCT to use the Snowball Edge\. Enter the IP address of the Snowball Edge, the listening port on the device \(the default is 8080\), and the Snowball Edge access keys and secret keys you retrieved earlier\. Choose **OK** to save your changes\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/dms/latest/userguide/images/snowball-configure.png)