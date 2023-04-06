# Creating database users for AWS DMS Fleet Advisor<a name="fa-database-users"></a>

This section describes how to create users for your source databases with the minimum permissions required for the DMS data collector\.

**Topics**
+ [Using a database user with AWS DMS Fleet Advisor](#fa-database-users-using)
+ [Creating a database user with MySQL](#fa-database-users-mysql)
+ [Creating a database user with Oracle](#fa-database-users-oracle)
+ [Creating a database user with PostgreSQL](#fa-database-users-postgresql)
+ [Creating a database user with Microsoft SQL Server](#fa-database-users-sql-server)
+ [Deleting database users](#fa-database-users-drop)

## Using a database user with AWS DMS Fleet Advisor<a name="fa-database-users-using"></a>

You can use a database user other than `root` with the DMS data collector\. Specify the user name and password after adding the database to the inventory, but before you run your data collector\. For more information about adding databases to the inventory, see [Managing monitored objects](fa-managing-objects.md)\. 

After you finish using the DMS data collector, you can delete the database users that you created\. For more information, see [Deleting database users](#fa-database-users-drop)\.

**Important**  
In the following examples, replace *\{your\_user\_name\}* with the name of the database user that you created for your database\. Then, replace *\{your\_password\}* with a secure password\.

## Creating a database user with MySQL<a name="fa-database-users-mysql"></a>

To create a database user in a MySQL source database, use the following script\. Make sure that you keep one version of the `GRANT` statement that depends on the version of your MySQL database\.

```
CREATE USER {your_user_name} identified BY '{your_password}';

GRANT PROCESS ON *.* TO {your_user_name};
GRANT REFERENCES ON *.* TO {your_user_name};
GRANT TRIGGER ON *.* TO {your_user_name};
GRANT EXECUTE ON *.* TO {your_user_name};
                
# For MySQL versions lower than 8.0, use the following statement.
GRANT SELECT, CREATE TEMPORARY TABLES, DROP ON `temp`.* TO {your_user_name};

# For MySQL versions 8.0 and higher, use the following statement.
GRANT SELECT, CREATE TEMPORARY TABLES, DROP ON `mysql`.* TO {your_user_name};

GRANT SELECT ON performance_schema.* TO {your_user_name};

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

## Creating a database user with Oracle<a name="fa-database-users-oracle"></a>

To create a database user in an Oracle source database, use the following script\.

To run this SQL script, connect to your Oracle database using `SYSDBA` privileges\. After you run this SQL script, connect to your database using the credentials of the user that you created with this script\. Also, use the credentials of this user to run the DMS data collector\.

The following script adds the `C##` prefix to the name of the user for Oracle multitenant container databases \(CDB\)\.

```
CREATE USER {your_user_name} IDENTIFIED BY "{your_password}";
GRANT CREATE SESSION TO {your_user_name};
GRANT SELECT ANY DICTIONARY TO {your_user_name};
GRANT SELECT ON DBA_WM_SYS_PRIVS TO {your_user_name};
BEGIN
    DBMS_NETWORK_ACL_ADMIN.CREATE_ACL(
        acl => UPPER('{your_user_name}') || '_Connect_Access.xml',
        description => 'Connect Network',
        principal => UPPER('{your_user_name}'),
        is_grant => TRUE,
        privilege => 'resolve',
        start_date => NULL,
        end_date => NULL);

    DBMS_NETWORK_ACL_ADMIN.ASSIGN_ACL(
        acl => UPPER('{your_user_name}') || '_Connect_Access.xml',
        host => '*',
        lower_port => NULL,
        upper_port => NULL);
END;
```

## Creating a database user with PostgreSQL<a name="fa-database-users-postgresql"></a>

To create a database user in a PostgreSQL source database, use the following script\.

```
CREATE USER "{your_user_name}" WITH LOGIN PASSWORD '{your_password}';
GRANT pg_read_all_settings TO "{your_user_name}";

-- For PostgreSQL versions 10 and higher, add the following statement.
GRANT EXECUTE ON FUNCTION pg_ls_waldir() TO "{your_user_name}";
```

## Creating a database user with Microsoft SQL Server<a name="fa-database-users-sql-server"></a>

To create a database user in a Microsoft SQL Server source database, use the following script\.

```
USE master
GO

IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = N'{your_user_name}')

	CREATE LOGIN [{your_user_name}] WITH PASSWORD=N'{your_password}', DEFAULT_DATABASE=[master], DEFAULT_LANGUAGE=[us_english], CHECK_EXPIRATION=OFF, CHECK_POLICY=OFF

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

USE msdb
GO

GRANT EXECUTE ON dbo.agent_datetime TO [{your_user_name}]
```

## Deleting database users<a name="fa-database-users-drop"></a>

After you complete all data collection tasks, you can delete the database users that you created for the DMS data collector\. You can use the following scripts to delete the users with minimum permissions from your databases\.

To delete the user from your MySQL database, run the following script\.

```
DROP USER IF EXISTS "{your_user_name}";
```

To delete the user from your Oracle database, run the following script\.

```
DECLARE
  -- Input parameters, please set correct value
  cnst$user_name CONSTANT VARCHAR2(255) DEFAULT '{your_user_name}';

  -- System variables, please, don't change
  var$is_exists INTEGER DEFAULT 0;
BEGIN
  SELECT COUNT(hal.acl) INTO var$is_exists
  FROM dba_host_acls hal
  WHERE hal.acl LIKE '%' || UPPER(cnst$user_name) || '_Connect_Access.xml';
  IF var$is_exists > 0 THEN
    DBMS_NETWORK_ACL_ADMIN.DROP_ACL(
      acl => UPPER(cnst$user_name) || '_Connect_Access.xml');
  END IF;
  SELECT COUNT(usr.username) INTO var$is_exists
  FROM all_users usr
  WHERE usr.username = UPPER(cnst$user_name);
  IF var$is_exists > 0 THEN
    EXECUTE IMMEDIATE 'DROP USER ' || cnst$user_name || ' CASCADE';
  END IF;
END;
```

To delete the user from your PostgreSQL database, run the following script\.

```
DROP USER IF EXISTS "{your_user_name}";
```

To delete the user from your SQL Server database, run the following script\.

```
USE msdb
GO

REVOKE EXECUTE ON dbo.agent_datetime TO [{your_user_name}]

USE master
GO

DECLARE @dbname VARCHAR(100)
DECLARE @statement NVARCHAR(max)

DECLARE db_cursor CURSOR
LOCAL FAST_FORWARD
FOR
SELECT
  name
FROM MASTER.sys.databases
WHERE state = 0
  AND is_read_only = 0
    OPEN db_cursor
FETCH NEXT FROM db_cursor INTO @dbname
  WHILE @@FETCH_STATUS = 0
BEGIN

SELECT @statement = 'USE '+ quotename(@dbname) +';'+ '
  EXECUTE sp_droprolemember N''db_datareader'', [{your_user_name}]

  IF EXISTS (SELECT * FROM sys.syslogins WHERE name = ''{your_user_name}'') 
    OR EXISTS (SELECT * FROM sys.sysusers WHERE name = ''{your_user_name}'')
    DROP USER [{your_user_name}];'

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

GO

IF EXISTS (SELECT * FROM sys.sql_logins WHERE name = N'{your_user_name}')
  DROP LOGIN [{your_user_name}] 	-- Use for SQL login

GO
```