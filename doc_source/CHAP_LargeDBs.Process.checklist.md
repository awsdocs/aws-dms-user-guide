# Migration checklist<a name="CHAP_LargeDBs.Process.checklist"></a>

To make things easier during migration, you can use the following checklist to create a list of the items that you need during the migration\.

```
----------------------------------------------------------------
DMS Migration Checklist
----------------------------------------------------------------

   This checklist is for my schemas named:  

   The database engine that my schemas reside on is:  

----------------------------------------------------------------

   AWS Region for the migration: 

   Name of migration job that you created in the AWS Snowball Management Console:  

   S3 bucket (and folder) for this job:  

   IAM role that has access to the S3 Bucket and the target database on AWS:  

----------------------------------------------------------------

   Path to the installation directory of AWS SCT (needed for a future step):  

   Name/IP of Machine #1 (SCT):  

   Name/IP of Machine #2 (Connectivity):  

----------------------------------------------------------------

   IP address of your Snowball Edge:  

   Port for the Snowball Edge: 

   Unlock code for the Snowball Edge device:  

   Path to the manifest file:  

   Output of the command snowballEdge get-secret-access-key:
      AWS access key ID:  
      AWS secret access Key:  

----------------------------------------------------------------

   Confirm ODBC drivers is installed on Machine #2 (Connectivity):  

----------------------------------------------------------------

   Confirm DMS Agent is installed on Machine #2 (Connectivity):  

   Confirm DMS Agent is running two processes:  

   DMS Agent password:  

   DMS Agent port number:  

   Confirm that your firewall allows connectivity:  

----------------------------------------------------------------

   Name of SCT project:

----------------------------------------------------------------

   Confirm that DMS Agent is registered with SCT:  

   New agent or service profile name that you provided:  

----------------------------------------------------------------

   Confirm local and DMS task exists:  

   Task name that you provided:  

----------------------------------------------------------------

   Confirm: 

      DMS Agent connects to the following:
         __ The source database
         __ The staging S3 bucket
         __ The Edge device

      DMS task connects to the following:
         __ The staging  S3 bucket
         __ The target database on AWS

----------------------------------------------------------------

   Confirm the following:
      __ Stopped Edge client
      __ Powered off Edge device
      __ Returned Edge device to AWS
```