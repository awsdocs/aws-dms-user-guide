# MySQL<a name="CHAP_LargeDBs.SBS.configure-dms-agent-linux-host.mysql"></a>

Install MySQL Connector/ODBC for Linux, version 5\.2\.6 or later\.

Make sure that the `/etc/odbcinst.ini` file contains an entry for MySQL, as shown in the following example\.

```
[MySQL ODBC 5.2.6 Unicode Driver]
Driver = /usr/lib64/libmyodbc5w.so 
UsageCount = 1
```

The /etc/odbcinst\.ini file contains information about ODBC drivers available to users and can be edited by hand\. If necessary, you can use the `odbcinst –j` command to look for the `/etc/odbcinst.ini` file, as shown in the following example\.

```
$ odbcinst -j

DRIVERS............: /etc/odbcinst.ini
```