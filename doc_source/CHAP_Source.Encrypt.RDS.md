# Encrypting Amazon RDS and Amazon Aurora connections in AWS SCT<a name="CHAP_Source.Encrypt.RDS"></a>

To open encrypted connections to Amazon RDS or Amazon Aurora databases from an application, you need to import AWS root certificates into some form of key storage\. You can download the root certificates from AWS at [Using SSL/TLS to Encrypt a Connection to a DB Instance](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/UsingWithRDS.SSL.html) in the *Amazon RDS User Guide\.* 

Two options are available, a root certificate that works for all AWS Regions and a certificate bundle that contains both the old and new root certificates\.

Depending on which you want to use, follow the steps in one of the two following procedures\.

**To import the certificate or certificates into the Windows system storage**

1. Download a certificate or certificates from one of the following sources:
   + A root certificate that works for all AWS Regions from [https://s3.amazonaws.com/rds-downloads/rds-ca-2019-root.pem](https://s3.amazonaws.com/rds-downloads/rds-ca-2019-root.pem)\.
   + A certificate bundle that contains both the old and new root certificates from [https://s3.amazonaws.com/rds-downloads/rds-ca-2019-root.pem](https://s3.amazonaws.com/rds-downloads/rds-ca-2019-root.pem)\.

1. In your Windows search window, enter **Manage computer certificates**\. When prompted as to whether to let the application make changes to your computer, choose **Yes**\.

1. When the certificates window opens, if needed expand **Certificated \- Local Computer** so you can see the list of certificates\. Open the context \(right\-click\) menu for **Trusted Root Certification Authorities**, then choose **All Tasks**, **Import**\.

1. Choose **Next**, then **Browse**, and find the `*.pem` file that you downloaded in step 1\. Choose **Open** to select the certificate file, choose **Next**, and then choose **Finish**\.
**Note**  
To find the file, change the file type in the browse window to **All files \(\*\.\*\)**, because `.pem` is not a standard certificate extension\.

1. In the Microsoft Management Console, expand **Certificates**\. Then expand **Trusted Root Certification Authorities**, choose **Certificates**, and find the certificate to confirm that it exists\.

1. Restart your computer\.

**To import the certificate or certificates into the Java KeyStore**

1. Download the certificate or certificates from one of the following sources:
   + A root certificate that works for all AWS Regions from [https://s3\.amazonaws\.com/rds\-downloads/rds\-ca\-2019\-root\.pem](https://s3.amazonaws.com/rds-downloads/rds-ca-2019-root.pem)\.
   + A certificate bundle that contains both the old and new root certificates from [https://s3\.amazonaws\.com/rds\-downloads/rds\-ca\-2019\-root\.pem](https://s3.amazonaws.com/rds-downloads/rds-ca-2019-root.pem)\.

1. If you downloaded the certificate bundle, split it into individual certificates files\. To do so, place each certificate block, beginning with `-----BEGIN CERTIFICATE-----` and ending with `-----END CERTIFICATE-----` into a separate `*.pem` files\. After you have created a separate `*.pem` file for each certificate, you can safely remove the certificate bundle file\.

1. Open a command window or terminal session in the directory where you downloaded the certificate, and run the following command for every `*.pem` file that you created in the previous step\.

   ```
   keytool -importcert -file <filename>.pem -alias <filename>.pem -keystore storename
   ```  
**Example**  

   The following example assumes that you downloaded the `rds-ca-2019-root.pem` file\.

   ```
   keytool -importcert -file rds-ca-2019-root.pem -alias rds-ca-2019-root.pem -keystore trust-2019.ks
   Enter keystore password:
   Re-enter new password:
   Owner: CN=Amazon RDS Root 2019 CA, OU=Amazon RDS, O="Amazon Web Services, Inc.", ST=Washington, L=Seattle, C=US
   Issuer: CN=Amazon RDS Root 2019 CA, OU=Amazon RDS, O="Amazon Web Services, Inc.", ST=Washington, L=Seattle, C=US
   Serial number: c73467369250ae75
   Valid from: Thu Aug 22 19:08:50 CEST 2019 until: Thu Aug 22 19:08:50 CEST 2024
   Certificate fingerprints:
   SHA1: D4:0D:DB:29:E3:75:0D:FF:A6:71:C3:14:0B:BF:5F:47:8D:1C:80:96
   SHA256: F2:54:C7:D5:E9:23:B5:B7:51:0C:D7:9E:F7:77:7C:1C:A7:E6:4A:3C:97:22:E4:0D:64:54:78:FC:70:AA:D0:08
   Signature algorithm name: SHA256withRSA
   Subject Public Key Algorithm: 2048-bit RSA key
   Version: 3
   
   Extensions:
   
   #1: ObjectId: 2.5.29.35 Criticality=false
   AuthorityKeyIdentifier [
   KeyIdentifier [
   0000: 73 5F 60 D8 BC CB 03 98   F4 2B 17 34 2E 36 5A A6  s_`......+.4.6Z.
   0010: 60 FF BC 1F                                        `...
   ]
   ]
   
   #2: ObjectId: 2.5.29.19 Criticality=true
   BasicConstraints:[
   CA:true
   PathLen:2147483647
   ]
   
   #3: ObjectId: 2.5.29.15 Criticality=true
   KeyUsage [
   Key_CertSign
   Crl_Sign
   ]
   
   #4: ObjectId: 2.5.29.14 Criticality=false
   SubjectKeyIdentifier [
   KeyIdentifier [
   0000: 73 5F 60 D8 BC CB 03 98   F4 2B 17 34 2E 36 5A A6  s_`......+.4.6Z.
   0010: 60 FF BC 1F                                        `...
   ]
   ]
   
   Trust this certificate? [no]:  yes
   Certificate was added to keystore
   ```

1. Add the keystore as a trust store in AWS SCT\. To do so, from the main menu choose **Settings, General Settings**, **Security**, **Trust Store**, and then choose **Select existing trust store**\. 

   After adding the trust store, you can use it to configure an SSL enabled connection when you create an AWS SCT connection to the database\. In the AWS SCT **Connect to database** dialog, choose **Use SSL** and choose the trust store entered previously\.