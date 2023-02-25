# Creating database users for AWS DMS Fleet Advisor<a name="CHAP_FleetAdvisor.DBUser"></a>

This section describes how to create users for your source databases with the minimum permissions required for the DMS data collector\.

**Topics**
+ [Using a database user with AWS DMS Fleet Advisor](#CHAP_FleetAdvisor.DBUser.Using)
+ [Creating a database user with MySQL](#CHAP_FleetAdvisor.DBUser.MySQL)
+ [Creating a database user with Oracle](#CHAP_FleetAdvisor.DBUser.Oracle)
+ [Creating a database user with PostgreSQL](#CHAP_FleetAdvisor.DBUser.PostgreSQL)
+ [Creating a database user with Microsoft SQL Server](#CHAP_FleetAdvisor.DBUser.SQLServer)

## Using a database user with AWS DMS Fleet Advisor<a name="CHAP_FleetAdvisor.DBUser.Using"></a>

You can use a database user other than `root` with the DMS data collector\. Specify the user name and password after adding the database to the inventory, but before starting data collection\. For more information about adding databases to the inventory, see [Managing monitored objects](CHAP_FleetAdvisor.ManagingObjects.md)\.

## Creating a database user with MySQL<a name="CHAP_FleetAdvisor.DBUser.MySQL"></a>

To create a database user in a MySQL source database, use the following script\. Replace the sample values with your values\.

```
CREATE USER {your_user_name} identified BY '{your_password}';

GRANT REFERENCES ON *.* TO {your_user_name};
GRANT TRIGGER ON *.* TO {your_user_name};
GRANT EXECUTE ON *.* TO {your_user_name};

SELECT
    IF(round(Value1 + Value2 / 100 + Value3 / 10000, 4) > 5.0129, 'GRANT EVENT ON *.* TO {your_user_name};', 'SELECT ''Events are not applicable'';') sql_statement
INTO @stringStatement
FROM (
       SELECT
         substring_index(ver, '.', 1)                            value1,
         substring_index(substring_index(ver, '.', 2), '.', - 1) value2,
         substring_index(ver, '.', - 1)                          value3
       FROM  (
         SELECT
           IF((@@version regexp '[^0-9\.]+') != 0, @@innodb_version, @@version) AS ver
         FROM dual
       ) vercase
     ) v;

PREPARE sqlStatement FROM @stringStatement;
SET @stringStatement := NULL;
EXECUTE sqlStatement;
DEALLOCATE PREPARE sqlStatement;
```

## Creating a database user with Oracle<a name="CHAP_FleetAdvisor.DBUser.Oracle"></a>

To create a database user in an Oracle source database, use the following script\. Replace the sample values with your values\. For *\{your\_user\_name\}*, enter the user name in uppercase\.

To run this SQL script, connect to your Oracle database using `SYSDBA` privileges\. After you run this SQL script, connect to your database using the credentials of the user that you created with this script\. Also, use the credentials of this user to run the DMS data collector\.

The following script adds the `C##` prefix to the name of the user for Oracle multitenant container databases \(CDB\)\.

```
CREATE USER {your_user_name} IDENTIFIED BY "{your_password}";
GRANT CREATE SESSION TO {your_user_name};
GRANT SELECT ANY DICTIONARY TO {your_user_name};
BEGIN
    DBMS_NETWORK_ACL_ADMIN.CREATE_ACL(
        acl => '{your_user_name}' || '_Connect_Access.xml',
        description => 'Connect Network',
        principal => '{your_user_name}',
        is_grant => TRUE,
        privilege => 'resolve',
        start_date => NULL,
        end_date => NULL);

    DBMS_NETWORK_ACL_ADMIN.ASSIGN_ACL(
        acl => '{your_user_name}' || '_Connect_Access.xml',
        host => '*',
        lower_port => NULL,
        upper_port => NULL);
END;
```

## Creating a database user with PostgreSQL<a name="CHAP_FleetAdvisor.DBUser.PostgreSQL"></a>

To create a database user in a PostgreSQL source database, use the following script\. Replace the sample values with your values\.

```
CREATE USER "{your_user_name}" WITH LOGIN PASSWORD '{your_password}';
GRANT pg_read_all_settings TO "{your_user_name}";
```

## Creating a database user with Microsoft SQL Server<a name="CHAP_FleetAdvisor.DBUser.SQLServer"></a>

To create a database user in a Microsoft SQL Server source database, use the following script\. Replace the sample values with your values\.

```
USE master
GO

CREATE LOGIN [{your_user_name}] WITH PASSWORD=N'{your_password}', DEFAULT_DATABASE=[master], DEFAULT_LANGUAGE=[us_english], CHECK_EXPIRATION=OFF, CHECK_POLICY=OFF		-- Use for SQL login

GO

GRANT VIEW SERVER STATE TO [{your_user_name}]

GRANT VIEW ANY DEFINITION TO [{your_user_name}]

GRANT VIEW ANY DATABASE TO [{your_user_name}]

IF LEFT(CONVERT(SYSNAME,SERVERPROPERTY('ProductVersion')), CHARINDEX('.', CONVERT(SYSNAME,SERVERPROPERTY('ProductVersion')), 0)-1) >= 12
	EXECUTE('GRANT CONNECT ANY DATABASE TO [{your_user_name}]')

DECLARE @dbname VARCHAR(100)
DECLARE @statement NVARCHAR(max)

DECLARE db_cursor CURSOR
LOCAL FAST_FORWARD
FOR
  SELECT
    name
  FROM	MASTER.sys.databases
  WHERE	state = 0
    AND is_read_only = 0
OPEN db_cursor
FETCH NEXT FROM db_cursor INTO @dbname
    WHILE @@FETCH_STATUS = 0
BEGIN

  SELECT @statement = 'USE '+ quotename(@dbname) +';'+ '
    IF NOT EXISTS (SELECT * FROM sys.syslogins WHERE name = ''{your_user_name}'') OR NOT EXISTS (SELECT * FROM sys.sysusers WHERE name = ''{your_user_name}'')
	  CREATE USER [{your_user_name}] FOR LOGIN [{your_user_name}];

  EXECUTE sp_addrolemember N''db_datareader'', [{your_user_name}]'

  BEGIN TRY
    EXECUTE sp_executesql @statement
  END TRY
  BEGIN CATCH
    DECLARE @err NVARCHAR(255)

    SET @err = error_message()

    PRINT @dbname
    PRINT @err
  END CATCH

  FETCH NEXT FROM db_cursor INTO @dbname
END
CLOSE db_cursor
DEALLOCATE db_cursor
```