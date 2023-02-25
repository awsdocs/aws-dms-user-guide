# Step 5: Install the AWS DMS Agent<a name="CHAP_LargeDBs.SBS.install-dms-agent"></a>

Using Machine \#2 \(Connectivity\) from step 2, where the Edge client and the ODBC drivers are already installed, install and configure the AWS DMS Agent\. The AWS DMS Agent is provided as part of the [AWS SCT installation package](https://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_Installing.html), described in the *AWS Schema Conversion Tool User Guide*\.

After you finish this step, you should have two local machines prepared: 
+ Machine \#1 \(SCT\) with AWS SCT installed
+ Machine \#2 \(Connectivity\) with the Edge client, the ODBC drivers, and the DMS Agent installed

**To install the AWS DMS Agent**

1. In the AWS SCT installation directory, locate the RPM file called `aws-schema-conversion-tool-dms-agent-X.X.X-XX.x86_64.rpm`\.

   Copy it to Machine \#2 \(Connectivity\), the AWS DMS Agent machine\. SCT and the DMS Agent should be installed on separate machines\. AWS DMS Agent should be located on the same machine as the Edge client and the ODBC drivers\.

1. On Machine \#2 \(Connectivity\), run the following command to install the DMS Agent\. To simplify permissions, run this command as the `root` user\.

   ```
   sudo rpm -i aws-schema-conversion-tool-dms-agent-X.X.X-XX.x86_64.rpm
   ```

   This command uses the default installation location of `/opt/amazon/aws-schema-conversion-tool-dms-agent`\. To install the DMS Agent to a different location, use the following option\.

   ```
   sudo rpm --prefix installation_directory -i aws-schema-conversion-tool-dms-agent-X.X.X-XX.x86_64.rpm
   ```

1. To verify that the AWS DMS Agent is running, use the following command\.

   ```
   ps -ef | grep repctl
   ```

   The output of this command should show two processes running\.

   To configure the AWS DMS Agent, you must provide a password and port number\. You use the password later to register the AWS DMS Agent with AWS SCT, so keep it handy\. Pick an unused port number for the AWS DMS Agent to listen on for AWS SCT connections\. You might have to configure your firewall to allow connectivity\.

   Now configure the AWS DMS Agent using the `configure.sh` script\.

   ```
   sudo /opt/amazon/aws-schema-conversion-tool-dms-agent/bin/configure.sh
   ```

   The following prompt appears\. Enter the password\. When prompted, enter the password again to confirm it\.

   ```
   Configure the AWS Schema Conversion Tool DMS Agent server
   Note: you will use these parameters when configuring agent in AWS Schema Conversion Tool
   
   Please provide password for the server
   Use minimum 8 and up to 20 alphanumeric characters with at least one digit and one
       capital case character
   
   Password:
   ```

   The output is as follows\. Provide a port number\.

   ```
   chown: missing operand after 'amazon:amazon'
   Try 'chown --help' for more information.
   /opt/amazon/aws-schema-conversion-tool-dms-agent/bin/repctl:
       /opt/amazon/aws-schema-conversion-tool-dms-agent/lib/libcom_err.so.3: no version
       information available (required by
       /opt/amazon/aws-schema-conversion-tool-dms-agent/lib/libgssapi_krb5.so.2)
   /opt/amazon/aws-schema-conversion-tool-dms-agent/bin/repctl:
       /opt/amazon/aws-schema-conversion-tool-dms-agent/lib/libcom_err.so.3: no version
       information available (required by
       /opt/amazon/aws-schema-conversion-tool-dms-agent/lib/libkrb5.so.3)
   [setserverpassword command] Succeeded
   
   Please provide port number the server will listen on (default is 3554)
   Note: you will have to configure your firewall rules accordingly
   Port:
   ```

The output is as follows, confirming that the service is started\. 

```
Starting service...
/opt/amazon/aws-schema-conversion-tool-dms-agent/bin/repctl:
    /opt/amazon/aws-schema-conversion-tool-dms-agent/lib/libcom_err.so.3: no version
    information available (required by
    /opt/amazon/aws-schema-conversion-tool-dms-agent/lib/libgssapi_krb5.so.2)
/opt/amazon/aws-schema-conversion-tool-dms-agent/bin/repctl:
    /opt/amazon/aws-schema-conversion-tool-dms-agent/lib/libcom_err.so.3: no version
    information available (required by
    /opt/amazon/aws-schema-conversion-tool-dms-agent/lib/libkrb5.so.3)
AWS Schema Conversion Tool DMS Agent was sent a stop signal
AWS Schema Conversion Tool DMS Agent is no longer running
[service command] Succeeded
/opt/amazon/aws-schema-conversion-tool-dms-agent/bin/repctl:
    /opt/amazon/aws-schema-conversion-tool-dms-agent/lib/libcom_err.so.3: no version
    information available (required by
    /opt/amazon/aws-schema-conversion-tool-dms-agent/lib/libgssapi_krb5.so.2)
/opt/amazon/aws-schema-conversion-tool-dms-agent/bin/repctl:
    /opt/amazon/aws-schema-conversion-tool-dms-agent/lib/libcom_err.so.3: no version
    information available (required by
    /opt/amazon/aws-schema-conversion-tool-dms-agent/lib/libkrb5.so.3)
AWS Schema Conversion Tool DMS Agent was started as PID 1608
```

We recommend that you install the [AWS Command Line Interface](https://aws.amazon.com/cli/) \(AWS CLI\)\. Using the AWS CLI, you can interrogate the AWS Snowball Edge to see the data files written to the device\. You use the AWS credentials retrieved from the Edge to access the Edge device\. For example, you might run the following command\.

```
aws s3 ls --profile SnowballEdge --endpoint https://192.0.2.0 :8080 bucket-name --recursive
```

This command produces the following output\.

```
2018-08-20 10:55:31 53074692 streams/load00000001000573E166ACF4C0/00000001.fcd.gz
2018-08-20 11:14:37 53059667 streams/load00000001000573E166ACF4C0/00000002.fcd.gz
2018-08-20 11:31:42 53079181 streams/load00000001000573E166ACF4C0/00000003.fcd.gz
```

To stop the AWS DMS Agent, run the following command in the `/opt/amazon/aws-schema-conversion-tool-dms-agent/bin` directory\.

```
./aws-schema-conversion-tool-dms-agent stop
```

To start the AWS DMS Agent, run the following command in the `/opt/amazon/aws-schema-conversion-tool-dms-agent/bin` directory\.

```
./aws-schema-conversion-tool-dms-agent start
```