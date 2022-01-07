# Using Microsoft SQL Server as a source for AWS SCT<a name="CHAP_Source.SQLServer"></a>

You can use AWS SCT to convert schemas, database code objects, and application code from SQL Server to the following targets: 
+ Amazon RDS for MySQL
+ Amazon Aurora MySQL\-Compatible Edition
+ Amazon RDS for PostgreSQL
+ Amazon Aurora PostgreSQL\-Compatible Edition
+ Amazon RDS for SQL Server
+ Amazon RDS for MariaDB

You can use AWS SCT to create an assessment report for the migration of schemas, database code objects, and application code from SQL Server to Babelfish for Aurora PostgreSQL\.

For more information, see the following sections:

**Topics**
+ [Permissions required when using Microsoft SQL Server as a source](#CHAP_Source.SQLServer.Permissions)
+ [Using Windows Authentication when using Microsoft SQL Server as a source](#CHAP_Source.SQLServer.Permissions.WinAuth)
+ [Connecting to SQL Server as a source](#CHAP_Source.SQLServer.Connecting)
+ [Converting SQL Server to MySQL](CHAP_Source.SQLServer.ToMySQL.md)
+ [Converting SQL Server to PostgreSQL](CHAP_Source.SQLServer.ToPostgreSQL.md)
+ [Converting SQL Server to Amazon RDS for SQL Server](CHAP_Source.SQLServer.ToRDSSQLServer.md)

## Permissions required when using Microsoft SQL Server as a source<a name="CHAP_Source.SQLServer.Permissions"></a>

The privileges required for Microsoft SQL Server as a source are listed following: 
+ VIEW DEFINITION 
+ VIEW DATABASE STATE 

Repeat the grant for each database whose schema you are converting\. 

## Using Windows Authentication when using Microsoft SQL Server as a source<a name="CHAP_Source.SQLServer.Permissions.WinAuth"></a>

If your application runs on a Windows\-based intranet, you might be able to use Windows Authentication for database access\. Windows Authentication uses the current Windows identity established on the operating system thread to access the SQL Server database\. You can then map the Windows identity to a SQL Server database and permissions\. To connect to SQL Server using Windows Authentication, you must specify the Windows identity that your application is using\. You must also grant the Windows identity access to the SQL Server database\.

SQL Server has two modes of access: Windows Authentication mode and Mixed Mode\. Windows Authentication mode enables Windows Authentication and disables SQL Server Authentication\. Mixed Mode enables both Windows Authentication and SQL Server Authentication\. Windows Authentication is always available and cannot be disabled\. For more information about Windows Authentication, see the Microsoft Windows documentation\. 

The possible example for creating a user in TEST\_DB is shown following\.

```
USE [TEST_DB]
CREATE USER [TestUser] FOR LOGIN [TestDomain\TestUser]
GRANT VIEW DEFINITION TO [TestUser]
GRANT VIEW DATABASE STATE TO [TestUser]
```

### Using Windows Authentication with a JDBC connection<a name="CHAP_Source.SQLServer.Permissions.WinAuth.JDBC"></a>

The JDBC driver does not support Windows Authentication when the driver is used on non\-Windows operating systems\. Windows Authentication credentials, such as user name and password, are not automatically specified when connecting to SQL Server from non\-Windows operating systems\. In such cases, the applications must use SQL Server Authentication instead\.

In JDBC connection string, the parameter `integratedSecurity` must be specified to connect using Windows Authentication\. The JDBC driver supports Integrated Windows Authentication on Windows operating systems through the `integratedSecurity` connection string parameter\.

To use integrated authentication

1. Install the JDBC driver\.

1. Copy the `sqljdbc_auth.dll` file to a directory on the Windows system path on the computer where the JDBC driver is installed\.

   The` sqljdbc_auth.dll` files are installed in the following location:

   <*installation directory*>\\sqljdbc\_<*version*>\\<*language*>\\auth\\

When you try to establish a connection to SQL Server database using Windows Authentication, you might get this error: This driver is not configured for integrated authentication\. This problem can be solved by performing the following actions:
+ Declare two variables that point to the installed path of your JDBC:

  \-variable name: SQLJDBC\_HOME; variable value: D:\\lib\\JDBC4\.1\\enu \(where your sqljdbc4\.jar exists\);

  \-variable name: SQLJDBC\_AUTH\_HOME; variable value: D\\lib\\JDBC4\.1\\enu\\auth\\x86 \(if you are running 32bit OS\) or D\\lib\\JDBC4\.1\\enu\\auth\\x64 \(if you are running 64bit OS\)\. This is where your sqljdbc\_auth\.dll is located\. 
+ Copy sqljdbc\_auth\.dll to the folder where your JDK/JRE is running\. You may copy to lib folder, bin folder, and so on\. As an example, you might copy to the following folder\.

  ```
  [JDK_INSTALLED_PATH]\bin;
   [JDK_INSTALLED_PATH]\jre\bin;
   [JDK_INSTALLED_PATH]\jre\lib;
   [JDK_INSTALLED_PATH]\lib;
  ```
+ Ensure that in your JDBC library folder, you have only the SQLJDBC4\.jar file\. Remove any other sqljdbc\*\.jar files from that folder \(or copy them to another folder\)\. If you are adding the driver as part of your program, ensure that you add only SQLJDBC4\.jar as the driver to use\.
+ Copy sqljdbc\_auth\.dll the file in the folder with your application\.

**Note**  
If you are running a 32\-bit Java Virtual Machine \(JVM\), use the sqljdbc\_auth\.dll file in the x86 folder, even if the operating system is the x64 version\. If you are running a 64\-bit JVM on a x64 processor, use the sqljdbc\_auth\.dll file in the x64 folder\.

When you connect to a SQL Server database, you can choose either **Windows Authentication** or **SQL Server Authentication** for the **Authentication** option\.

## Connecting to SQL Server as a source<a name="CHAP_Source.SQLServer.Connecting"></a>

Use the following procedure to connect to your Microsoft SQL Server source database with the AWS Schema Conversion Tool\. 

**To connect to a Microsoft SQL Server source database**

1. In the AWS Schema Conversion Tool, choose **Add source**\. 

1. Choose **Microsoft SQL Server**, then choose **Next**\. 

    The **Add source** dialog box appears\.  
![\[Microsoft SQL Server connection information\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/images/source-sql-server.png)

1. Provide the Microsoft SQL Server source database connection information\. Use the instructions in the following table\.   
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_Source.SQLServer.html)

1. Choose **Test Connection** to verify that AWS SCT can connect to your source database\. 

1. Choose **Connect** to connect to your source database\.