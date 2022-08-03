# Using SSL with AWS DMS Fleet Advisor<a name="CHAP_DMSStudio.Ssl"></a>

To protect your data, AWS DMS Fleet Advisor can use SSL to access your databases\.

## Supported Databases<a name="CHAP_DMSStudio.Ssl.Databases"></a>

AWS DMS Fleet Advisor supports using SSL to access following databases:
+ Microsoft SQL Server ™
+ MySQL
+ PostgreSQL

## Setting up SSL<a name="CHAP_DMSStudio.Ssl.Setup"></a>

To use SSL to access your database, configure your database server to support SSL\. For more information, see the following documentation for your database:
+ SQL Server: [ Enable encrypted connections to the Database Engine](https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine?view=sql-server-2017) 
+ MySQL: [ Configuring MySQL to Use Encrypted Connections](https://dev.mysql.com/doc/refman/5.7/en/using-encrypted-connections.html) 
+ PostgreSQL: [ Secure TCP/IP Connections with SSL](https://www.postgresql.org/docs/current/ssl-tcp.html) 

## Using SSL<a name="CHAP_DMSStudio.Ssl.Using"></a>

To use SSL to connect to your database, check **Trust server certificate** and **Use SSL** when adding a server manually\. For a MySQL databse, you can use a custom certificate\. To use a custom certificate, check the **Verify CA** checkbox\. For information about adding a server, see [Managing monitored objects](CHAP_DMSStudio.ManagingObjects.md)\.

## Checking the Server Certificate Authority \(CA\) Certificate for SQL Server<a name="CHAP_DMSStudio.Ssl.Databases.SQLServerCertificate"></a>

If you want to validate your SQL Server's server CA certificate, uncheck **Trust server certificate** when adding the server\. If your server uses a well\-known CA, and the CA is installed by default on your OS, then verification should work normally\. If Fleet Advisor can't connect to your database server, install the CA certificate that your database server uses\. For more information, see [ Configure client](https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine?view=sql-server-2017#configure-client)\. 