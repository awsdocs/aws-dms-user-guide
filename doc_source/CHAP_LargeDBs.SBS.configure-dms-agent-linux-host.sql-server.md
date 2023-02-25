# Microsoft SQL Server<a name="CHAP_LargeDBs.SBS.configure-dms-agent-linux-host.sql-server"></a>

Install the Microsoft ODBC Driver\.

For ODBC 17 drivers, update the `site_arep_login.sh` script located under the bin folder of the agent installation with the following code\.

```
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/opt/microsoft/msodbcsql17/lib64/
```

For ODBC drivers earlier than ODBC 17, update the `site_arep_login.sh` script with the following code\.

```
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/opt/microsoft/msodbcsql/lib64/
```