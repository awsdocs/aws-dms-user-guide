# Using Teradata as a source for AWS SCT<a name="CHAP_Source.Teradata"></a>

You can use AWS SCT to convert schemas, code objects, and application code from Teradata to Amazon Redshift\.

## Privileges for Teradata as a source<a name="CHAP_Source.Teradata.Permissions"></a>

The privileges required for Teradata as a source are listed following: 
+ SELECT ON DBC 
+ SELECT ON SYSUDTLIB 
+ SELECT ON SYSLIB 
+ SELECT ON *<source\_database>* 
+ CREATE PROCEDURE ON *<source\_database>* 

In the example preceding, replace the *<source\_database>* placeholder with the name of the source database\.

AWS SCT requires the CREATE PROCEDURE privilege to perform HELP PROCEDURE against all procedures in the source database\. AWS SCT doesn't use this privilege to create any new objects in your source Teradata database\.

## Connecting to Teradata as a source<a name="CHAP_Source.Teradata.Connecting"></a>

Use the following procedure to connect to your Teradata source database with the AWS Schema Conversion Tool\. 

**To connect to a Teradata source database**

1. In the AWS Schema Conversion Tool, choose **Add source**\. 

1. Choose **Teradata**, then choose **Next**\. 

   The **Add source** dialog box appears\.  
![\[Connect to source database\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/images/file_connect_to_teradata.png)

1. Provide the Teradata source database connection information\. Use the instructions in the following table\.   
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_Source.Teradata.html)

1. Choose **Test Connection** to verify that AWS SCT can connect to your source database\. 

1. Choose **Connect** to connect to your source database\.

### Using LDAP authentication with a Teradata source<a name="CHAP_Source.Teradata.Connecting.LDAP"></a>

To set up Lightweight Directory Access Protocol \(LDAP\) authentication for Teradata users who run Microsoft Active Directory in Windows, use the following procedure\. 

In the following procedure, the Active Directory domain is `test.local.com`\. The Windows server is `DC`, and it's configured with default settings\. The user account created in Active Directory is `test_ldap`, and the account uses the password `test_ldap`\.

**To set up LDAP authentication for Teradata users who run Microsoft Active Directory in Windows**

1. In the `/opt/teradata/tdat/tdgss/site` directory, edit the file `TdgssUserConfigFile.xml` \. Change the LDAP section to the following\.

   ```
   AuthorizationSupported="no"
   
   LdapServerName="DC.test.local.com"
   LdapServerPort="389"
   LdapServerRealm="test.local.com"
   LdapSystemFQDN="dc= test, dc= local, dc=com"
   LdapBaseFQDN="dc=test, dc=local, dc=com"
   ```

1. Apply the changes by running the configuration as follows\.

   ```
   #cd /opt/teradata/tdgss/bin
   #./run_tdgssconfig
   ```

1. Test the configuration by running the following command\.

   ```
   # /opt/teradata/tdat/tdgss/14.10.03.01/bin/tdsbind -u test_ldap -w test_ldap
   ```

   The output should be similar to the following\.

   ```
   LdapGroupBaseFQDN: dc=Test, dc=local, dc=com
   LdapUserBaseFQDN: dc=Test, dc=local, dc=com
   LdapSystemFQDN: dc= test, dc= local, dc=com
   LdapServerName: DC.test.local.com
   LdapServerPort: 389
   LdapServerRealm: test.local.com
   LdapClientUseTls: no
   LdapClientTlsReqCert: never
   LdapClientMechanism: SASL/DIGEST-MD5
   LdapServiceBindRequired: no
   LdapClientTlsCRLCheck: none
   LdapAllowUnsafeServerConnect: yes
   UseLdapConfig: no
   AuthorizationSupported: no
   FQDN: CN=test, CN=Users, DC=Anthem, DC=local, DC=com
   AuthUser: ldap://DC.test.local.com:389/CN=test1,CN=Users,DC=test,DC=local,DC=com
   DatabaseName: test
   Service: tdsbind
   ```

1. Restart TPA using the following command\.

   ```
   #tpareset -f "use updated TDGSSCONFIG GDO"
   ```

1. Create the same user in the Teradata database as in Active Directory, as shown following\.

   ```
   CREATE USER test_ldap AS PERM=1000, PASSWORD=test_ldap;
   GRANT LOGON ON ALL TO test WITH NULL PASSWORD;
   ```

If you change the user password in Active Directory for your LDAP user, specify this new password during connection to Teradata in LDAP mode\. In DEFAULT mode, you connect to Teradata by using the LDAP user name and any password\.