# Using SSL with AWS DMS Fleet Advisor<a name="fa-using-ssl"></a>

To protect your data, AWS DMS Fleet Advisor can use SSL to access your databases\.

## Supported databases<a name="fa-using-ssl-databases"></a>

AWS DMS Fleet Advisor supports using SSL to access following databases:
+ Microsoft SQL Server
+ MySQL
+ PostgreSQL

## Setting up SSL<a name="fa-using-ssl-setup"></a>

To use SSL to access your database, configure your database server to support SSL\. For more information, see the following documentation for your database:
+ SQL Server: [ Enable encrypted connections to the Database Engine](https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine?view=sql-server-2017) 
+ MySQL: [ Configuring MySQL to Use Encrypted Connections](https://dev.mysql.com/doc/refman/5.7/en/using-encrypted-connections.html) 
+ PostgreSQL: [ Secure TCP/IP Connections with SSL](https://www.postgresql.org/docs/current/ssl-tcp.html) 

To use SSL to connect to your database, select **Trust server certificate** and **Use SSL** when adding a server manually\. For a MySQL database, you can use a custom certificate\. To use a custom certificate, select the **Verify CA** check box\. For information about adding a server, see [Managing monitored objects](fa-managing-objects.md)\.

## Checking the Server Certificate Authority \(CA\) Certificate for SQL Server<a name="fa-using-ssl-check"></a>

If you want to validate your Server Certificate Authority \(CA\) Certificate for SQL Server, then clear **Trust server certificate** when you add the server\. If your server uses a well\-known CA, and the CA is installed by default on your OS, then verification should work normally\. If DMS Fleet Advisor can't connect to your database server, install the CA certificate that your database server uses\. For more information, see [ Configure client](https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine?view=sql-server-2017#configure-client)\. 