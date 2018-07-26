# Using SSL With AWS Database Migration Service<a name="CHAP_Security.SSL"></a>

You can encrypt connections for source and target endpoints by using Secure Sockets Layer \(SSL\)\. To do so, you can use the AWS DMS Management Console or AWS DMS API to assign a certificate to an endpoint\. You can also use the AWS DMS console to manage your certificates\. 

Not all databases use SSL in the same way\. Amazon Aurora with MySQL compatibility uses the server name, the endpoint of the primary instance in the cluster, as the endpoint for SSL\. An Amazon Redshift endpoint already uses an SSL connection and does not require an SSL connection set up by AWS DMS\. An Oracle endpoint requires additional steps; for more information, see [SSL Support for an Oracle Endpoint](#CHAP_Security.SSL.Oracle)\.

**Topics**
+ [Limitations on Using SSL with AWS Database Migration Service](#CHAP_Security.SSL.Limitations)
+ [Managing Certificates](#CHAP_Security.SSL.ManagingCerts)
+ [Enabling SSL for a MySQL\-compatible, PostgreSQL, or SQL Server Endpoint](#CHAP_Security.SSL.Procedure)
+ [SSL Support for an Oracle Endpoint](#CHAP_Security.SSL.Oracle)

To assign a certificate to an endpoint, you provide the root certificate or the chain of intermediate CA certificates leading up to the root \(as a certificate bundle\), that was used to sign the server SSL certificate that is deployed on your endpoint\. Certificates are accepted as PEM formatted X509 files, only\. When you import a certificate, you receive an Amazon Resource Name \(ARN\) that you can use to specify that certificate for an endpoint\. If you use Amazon RDS, you can download the root CA and certificate bundle provided by Amazon RDS at [ https://s3\.amazonaws\.com/rds\-downloads/rds\-combined\-ca\-bundle\.pem](https://s3.amazonaws.com/rds-downloads/rds-combined-ca-bundle.pem)\. 

You can choose from several SSL modes to use for your SSL certificate verification\. 
+ **none** – The connection is not encrypted\. This option is not secure, but requires less overhead\.
+ **require** – The connection is encrypted using SSL \(TLS\) but no CA verification is made\. This option is more secure, and requires more overhead\. 
+ **verify\-ca** – The connection is encrypted\. This option is more secure, and requires more overhead\. This option verifies the server certificate\. 
+ **verify\-full** – The connection is encrypted\. This option is more secure, and requires more overhead\. This option verifies the server certificate and verifies that the server hostname matches the hostname attribute for the certificate\. 

Not all SSL modes work with all database endpoints\. The following table shows which SSL modes are supported for each database engine\.


| DB Engine | **none** | **require** | **verify\-ca** | **verify\-full** | 
| --- | --- | --- | --- | --- | 
| MySQL/MariaDB/Amazon Aurora MySQL | Default | Not supported | Supported | Supported | 
| Microsoft SQL Server | Default | Supported | Not Supported | Supported | 
| PostgreSQL | Default | Supported | Supported | Supported | 
| Amazon Redshift | Default | SSL not enabled | SSL not enabled | SSL not enabled | 
| Oracle | Default | Not supported | Supported | Not Supported | 
| SAP ASE | Default | SSL not enabled | SSL not enabled | Supported | 
| MongoDB | Default | Supported | Not Supported | Supported | 
| Db2 LUW | Default | Not Supported | Supported | Not Supported | 

## Limitations on Using SSL with AWS Database Migration Service<a name="CHAP_Security.SSL.Limitations"></a>
+ SSL connections to Amazon Redshift target endpoints are not supported\. AWS DMS uses an S3 bucket to transfer data to the Redshift database\. This transmission is encrypted by Amazon Redshift by default\. 
+  SQL timeouts can occur when performing CDC tasks with SSL\-enabled Oracle endpoints\. If you have this issue, where CDC counters don't reflect the expected numbers, set the `MinimumTransactionSize` parameter from the `ChangeProcessingTuning` section of task settings to a lower value, starting with a value as low as 100\. For more information about the `MinimumTransactionSize` parameter, see [Change Processing Tuning Settings](CHAP_Tasks.CustomizingTasks.TaskSettings.ChangeProcessingTuning.md)\.
+ Certificates can only be imported in the \.PEM and \.SSO \(Oracle wallet\) formats\.
+ If your server SSL certificate is signed by an intermediate CA, make sure the entire certificate chain leading from the intermediate CA up to the root CA is imported as a single \.PEM file\.
+ If you are using self\-signed certificates on your server, choose **require** as your SSL mode\. The **require** SSL mode implicitly trusts the server’s SSL certificate and will not try to validate that the certificate was signed by a CA\. 

## Managing Certificates<a name="CHAP_Security.SSL.ManagingCerts"></a>

You can use the DMS console to view and manage your SSL certificates\. You can also import your certificates using the DMS console\.

![\[AWS Database Migration Service SSL certificate management\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-certificatemgr.png)

## Enabling SSL for a MySQL\-compatible, PostgreSQL, or SQL Server Endpoint<a name="CHAP_Security.SSL.Procedure"></a>

You can add an SSL connection to a newly created endpoint or to an existing endpoint\.

**To create an AWS DMS endpoint with SSL**

1. Sign in to the AWS Management Console and choose AWS Database Migration Service\.
**Note**  
If you are signed in as an AWS Identity and Access Management \(IAM\) user, you must have the appropriate permissions to access AWS DMS\. For more information on the permissions required for database migration, see [IAM Permissions Needed to Use AWS DMS](CHAP_Security.IAMPermissions.md)\.

1. In the navigation pane, choose **Certificates**\.

1. Choose **Import Certificate**\.

1. Upload the certificate you want to use for encrypting the connection to an endpoint\.
**Note**  
You can also upload a certificate using the AWS DMS console when you create or modify an endpoint by selecting **Add new CA certificate** on the **Create database endpoint** page\.

1. Create an endpoint as described in [Step 3: Specify Source and Target Endpoints](CHAP_GettingStarted.md#CHAP_GettingStarted.Endpoints)

**To modify an existing AWS DMS endpoint to use SSL:**

1. Sign in to the AWS Management Console and choose AWS Database Migration Service\.
**Note**  
If you are signed in as an AWS Identity and Access Management \(IAM\) user, you must have the appropriate permissions to access AWS DMS\. For more information on the permissions required for database migration, see [IAM Permissions Needed to Use AWS DMS](CHAP_Security.IAMPermissions.md)\.

1. In the navigation pane, choose **Certificates**\.

1. Choose **Import Certificate**\.

1. Upload the certificate you want to use for encrypting the connection to an endpoint\.
**Note**  
You can also upload a certificate using the AWS DMS console when you create or modify an endpoint by selecting **Add new CA certificate** on the **Create database endpoint** page\.

1. In the navigation pane, choose **Endpoints**, select the endpoint you want to modify, and choose **Modify**\.

1. Choose an **SSL mode**\.

    If you select either the **verify\-ca** or **verify\-full** mode, you must specify the **CA certificate** that you want to use, as shown following\.   
![\[AWS Database Migration Service SSL certificate management\]](http://docs.aws.amazon.com/dms/latest/userguide/images/datarep-certificate2.png)

     

1. Choose **Modify**\.

1. When the endpoint has been modified, select the endpoint and choose **Test connection** to determine if the SSL connection is working\.

After you create your source and target endpoints, create a task that uses these endpoints\. For more information on creating a task, see [Step 4: Create a Task](CHAP_GettingStarted.md#CHAP_GettingStarted.Tasks)\. 

## SSL Support for an Oracle Endpoint<a name="CHAP_Security.SSL.Oracle"></a>

Oracle endpoints in AWS DMS support `none` and `verify-ca` SSL modes\. To use SSL with an Oracle endpoint, you must upload the Oracle wallet for the endpoint instead of \.pem certificate files\. 

**Topics**
+ [Using an Existing Certificate for Oracle SSL](#CHAP_Security.SSL.Oracle.Existing)
+ [Using a Self\-Signed Certificate for Oracle SSL](#CHAP_Security.SSL.Oracle.SelfSigned)

### Using an Existing Certificate for Oracle SSL<a name="CHAP_Security.SSL.Oracle.Existing"></a>

To use an existing Oracle client installation to create the Oracle wallet file from the CA certificate file, do the following steps\.

**To use an existing Oracle client installation for Oracle SSL with AWS DMS**

1. Set the ORACLE\_HOME system variable to the location of your dbhome\_1 directory by running the following command:

   ```
   prompt>export ORACLE_HOME=/home/user/app/user/product/12.1.0/dbhome_1                        
   ```

1. Append $ORACLE\_HOME/lib to the LD\_LIBRARY\_PATH system variable\.

   ```
   prompt>export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$ORACLE_HOME/lib                        
   ```

1. Create a directory for the Oracle wallet at $ORACLE\_HOME/ssl\_wallet\.

   ```
   prompt>mkdir $ORACLE_HOME/ssl_wallet
   ```

1. Put the CA certificate \.pem file in the ssl\_wallet directory\. Amazon RDS customers can download the RDS CA certificates file from [ https://s3\.amazonaws\.com/rds\-downloads/rds\-ca\-2015\-root\.pem](https://s3.amazonaws.com/rds-downloads/rds-ca-2015-root.pem)\. 

1. Run the following commands to create the Oracle wallet:

   ```
   prompt>orapki wallet create -wallet $ORACLE_HOME/ssl_wallet -auto_login_only
   
   prompt>orapki wallet add -wallet $ORACLE_HOME/ssl_wallet -trusted_cert –cert 
      $ORACLE_HOME/ssl_wallet/ca-cert.pem -auto_login_only
   ```

When you have completed the steps previous, you can import the wallet file with the ImportCertificate API by specifying the certificate\-wallet parameter\. You can then use the imported wallet certificate when you select `verify-ca` as the SSL mode when creating or modifying your Oracle endpoint\.

**Note**  
 Oracle wallets are binary files\. AWS DMS accepts these files as\-is\. 

### Using a Self\-Signed Certificate for Oracle SSL<a name="CHAP_Security.SSL.Oracle.SelfSigned"></a>

To use a self\-signed certificate for Oracle SSL, do the following\.

**To use a self\-signed certificate for Oracle SSL with AWS DMS**

1. Create a directory you will use to work with the self\-signed certificate\.

   ```
   mkdir <SELF_SIGNED_CERT_DIRECTORY>
   ```

1. Change into the directory you created in the previous step\.

   ```
   cd <SELF_SIGNED_CERT_DIRECTORY>
   ```

1. Create a root key\.

   ```
   openssl genrsa -out self-rootCA.key 2048
   ```

1. Self sign a root certificate using the root key you created in the previous step\.

   ```
   openssl req -x509 -new -nodes -key self-rootCA.key 
       -sha256 -days 1024 -out self-rootCA.pem
   ```

1. Create an Oracle wallet directory for the Oracle database\.

   ```
   mkdir $ORACLE_HOME/self_signed_ssl_wallet
   ```

1. Create a new Oracle wallet\.

   ```
   orapki wallet create -wallet $ORACLE_HOME/self_signed_ssl_wallet 
        -pwd <password> -auto_login_local
   ```

1. Add the root certificate to the Oracle wallet\.

   ```
   orapki wallet add -wallet $ORACLE_HOME/self_signed_ssl_wallet 
        -trusted_cert -cert self-rootCA.pem -pwd <password>
   ```

1. List the contents of the Oracle wallet\. The list should include the root certificate\. 

   ```
   orapki wallet display -wallet $ORACLE_HOME/self_signed_ssl_wallet
   ```

1. Generate the Certificate Signing Request \(CSR\) using the ORAPKI utility\.

   ```
   orapki wallet add -wallet $ORACLE_HOME/self_signed_ssl_wallet 
        -dn "CN=dms" -keysize 2048 -sign_alg sha256 -pwd <password>
   ```

1. Run the following command\.

   ```
    openssl pkcs12 -in ewallet.p12 -nodes -out nonoracle_wallet.pem
   ```

1. Put 'dms' as the common name\.

   ```
   openssl req -new -key nonoracle_wallet.pem -out certrequest.csr
   ```

1. Get the certificate signature\.

   ```
   openssl req -noout -text -in self-signed-oracle.csr  | grep -i signature
   ```

1. If the output from step 12 is `sha256WithRSAEncryption`, then run the following code\.

   ```
   openssl x509 -req -in self-signed-oracle.csr -CA self-rootCA.pem 
   -CAkey self-rootCA.key -CAcreateserial 
   -out self-signed-oracle.crt -days 365 -sha256
   ```

1. If the output from step 12 is `md5WithRSAEncryption`, then run the following code\.

   ```
   openssl x509 -req -in certrequest.csr -CA self-rootCA.pem 
   -CAkey self-rootCA.key -CAcreateserial 
   -out certrequest.crt -days 365 -sha256
   ```

1. Add the certificate to the wallet\.

   ```
   orapki wallet add -wallet $ORACLE_HOME/self_signed_ssl_wallet -user_cert 
   -cert certrequest.crt -pwd <password>
   ```

1. Configure *sqlnet\.ora* file \($ORACLE\_HOME/network/admin/sqlnet\.ora\)\.

   ```
   WALLET_LOCATION =
      (SOURCE =
        (METHOD = FILE)
        (METHOD_DATA =
          (DIRECTORY = <ORACLE_HOME>/self_signed_ssl_wallet)
        )
      ) 
   
   SQLNET.AUTHENTICATION_SERVICES = (NONE)
   SSL_VERSION = 1.0
   SSL_CLIENT_AUTHENTICATION = FALSE
   SSL_CIPHER_SUITES = (SSL_RSA_WITH_AES_256_CBC_SHA)
   ```

1. Stop the Oracle listener\.

   ```
   lsnrctl stop
   ```

1. Add entries for SSL in the *listener\.ora* file \(\($ORACLE\_HOME/network/admin/listener\.ora\)\.

   ```
   SSL_CLIENT_AUTHENTICATION = FALSE
   WALLET_LOCATION =
     (SOURCE =
       (METHOD = FILE)
       (METHOD_DATA =
         (DIRECTORY = <ORACLE_HOME>/self_signed_ssl_wallet)
       )
     )
   
   SID_LIST_LISTENER =
    (SID_LIST =
     (SID_DESC =
      (GLOBAL_DBNAME = <SID>)
      (ORACLE_HOME = <ORACLE_HOME>)
      (SID_NAME = <SID>)
     )
    )
   
   LISTENER =
     (DESCRIPTION_LIST =
       (DESCRIPTION =
         (ADDRESS = (PROTOCOL = TCP)(HOST = localhost.localdomain)(PORT = 1521))
         (ADDRESS = (PROTOCOL = TCPS)(HOST = localhost.localdomain)(PORT = 1522))
         (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
       )
     )
   ```

1. Configure the* tnsnames\.ora* file \($ORACLE\_HOME/network/admin/tnsnames\.ora\)\.

   ```
   <SID>=
   (DESCRIPTION=
           (ADDRESS_LIST = 
                   (ADDRESS=(PROTOCOL = TCP)(HOST = localhost.localdomain)(PORT = 1521))
           )
           (CONNECT_DATA =
                   (SERVER = DEDICATED)
                   (SERVICE_NAME = <SID>)
           )
   )
   
   <SID>_ssl=
   (DESCRIPTION=
           (ADDRESS_LIST = 
                   (ADDRESS=(PROTOCOL = TCPS)(HOST = localhost.localdomain)(PORT = 1522))
           )
           (CONNECT_DATA =
                   (SERVER = DEDICATED)
                   (SERVICE_NAME = <SID>)
           )
   )
   ```

1. Restart the Oracle listener\.

   ```
   lsnrctl start
   ```

1. Show the Oracle listener status\.

   ```
   lsnrctl status
   ```

1. Test the SSL connection to the database from localhost using sqlplus and the SSL tnsnames entry\.

   ```
   sqlplus -L <ORACLE_USER>@<SID>_ssl
   ```

1. Verify that you successfully connected using SSL\.

   ```
   SELECT SYS_CONTEXT('USERENV', 'network_protocol') FROM DUAL;
   
   SYS_CONTEXT('USERENV','NETWORK_PROTOCOL')
   --------------------------------------------------------------------------------
   tcps
   ```

1. Change directory to the directory with the self\-signed certificate\.

   ```
   cd <SELF_SIGNED_CERT_DIRECTORY>
   ```

1. Create a new client Oracle wallet that AWS DMS will use\.

   ```
   orapki wallet create -wallet ./ -auto_login_only
   ```

1. Add the self\-signed root certificate to the Oracle wallet\.

   ```
   orapki wallet add -wallet ./ -trusted_cert -cert rootCA.pem -auto_login_only
   ```

1. List the contents of the Oracle wallet that AWS DMS will use\. The list should include the self\-signed root certificate\.

   ```
   orapki wallet display -wallet ./
   ```

1. Upload the Oracle wallet you just created to AWS DMS\.