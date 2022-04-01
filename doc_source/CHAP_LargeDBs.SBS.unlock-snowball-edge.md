# Step 3: Unlock the Snowball Edge device<a name="CHAP_LargeDBs.SBS.unlock-snowball-edge"></a>

When the Edge device arrives, prepare it for use\. 

Follow the steps outlined in the section *[Getting started with an AWS Snowball Edge device](https://docs.aws.amazon.com/snowball/latest/developer-guide/getting-started.html)* in the *AWS Snowball Edge Developer Guide\.* 

You can also check out the [AWS Snowball Edge getting started marketing page](https://aws.amazon.com/snowball-edge/getting-started/) for more resources\. 

Power the device on, [connect it to your local network](https://docs.aws.amazon.com/snowball/latest/developer-guide/using-device.html), record the IP address of the Edge device, and [obtain the unlock code and manifest file](https://docs.aws.amazon.com/snowball/latest/developer-guide/get-credentials.html) from the Snowball Edge console\. In the console, choose your job, choose **View job details**, and then **Credentials**\. Save both the client unlock code and the manifest file\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/dms/latest/userguide/images/snowball-edge-unlock.png)

On the Edge device screen, get the IP of the Edge device from the **Connection** tab\. Then unlock the device by using the `snowballEdge` `unlock` command with the IP and the credentials information\. The following example shows the sample syntax for this command\. 

```
snowballEdge unlock -i IP_Address -m Local_path_to_manifest_file -u 29_character_unlock_code
```

Following is an example command\.

```
snowballEdge unlock \
    -i 192.0.2.0 \
    -m /Downloads/JID2EXAMPLE-0c40-49a7-9f53-916aEXAMPLE81-manifest.bin \
    -u 12345-abcde-12345-ABCDE-12345
```

Finally, [retrieve the Snowball Edge access key and secret key](https://docs.aws.amazon.com/snowball/latest/developer-guide/using-client-commands.html#client-credentials) from the device using the Edge client\. The following shows example input and output for the command to get the access key\.

**Example input**

```
snowballEdge list-access-keys \
    --endpoint https://192.0.2.0 \
    --manifest-file Path_to_manifest_file \
    --unlock-code 12345-abcde-12345-ABCDE-12345
```

**Example output**

```
{
    "AccessKeyIds" : [ "AKIAIOSFODNN7EXAMPLE" ]
}
```

The following shows example input and output for the command to get the secret key\.

**Example input**

```
snowballEdge get-secret-access-key \
    --access-key-id AKIAIOSFODNN7EXAMPLE \
    --endpoint https://192.0.2.0 \
    --manifest-file /Downloads/JID2EXAMPLE-0c40-49a7-9f53-916aEXAMPLE81-manifest.bin \
    --unlock-code 12345-abcde-12345-ABCDE-12345
```

**Example output**

```
[snowballEdge]
aws_access_key_id = AKIAIOSFODNN7EXAMPLE
aws_secret_access_key = wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
```

When the Snowball Edge is ready to use, you can interact with it directly by using the AWS CLI or S3 SDK Adapter for Snowball\. This adapter also works with the Edge device\. 