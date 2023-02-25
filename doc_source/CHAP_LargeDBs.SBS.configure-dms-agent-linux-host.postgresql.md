# PostgreSQL<a name="CHAP_LargeDBs.SBS.configure-dms-agent-linux-host.postgresql"></a>

Install `postgresql94-9.4.4-1PGDG.OS Version.x86_64.rpm`\. This package contains the psql executable\. For example, `postgresql94-9.4.4-1PGDG.rhel7.x86_64.rpm` is the package required for Red Hat 7\.

Install the ODBC driver `postgresql94-odbc-09.03.0400-1PGDG.OS version.x86_64` or above for Linux, where *OS version* is the OS of the agent machine\. For example, `postgresql94-odbc-09.03.0400-1PGDG.rhel7.x86_64` is the client required for Red Hat 7\.

Make sure that the `/etc/odbcinst.ini` file contains an entry for PostgreSQL, as shown in the following example\.

```
[PostgreSQL]
Description = PostgreSQL ODBC driver
Driver = /usr/pgsql-9.4/lib/psqlodbc.so 
Setup = /usr/pgsql-9.4/lib/psqlodbcw.so 
Debug = 0 
CommLog = 1 
UsageCount = 2
```