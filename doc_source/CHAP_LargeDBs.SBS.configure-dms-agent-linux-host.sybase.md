# ASE SAP Sybase<a name="CHAP_LargeDBs.SBS.configure-dms-agent-linux-host.sybase"></a>

The SAP Sybase ASE ODBC 64\-bit client should be installed\.

If the installation directory is `/opt/sap`, update the `site_arep_login.sh` script under the bin folder of the agent installation with the following\.

```
export SYBASE_HOME=/opt/sap
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$SYBASE_HOME/DataAccess64/ODBC/lib:$SYBASE_HOME/DataAccess/ODBC/lib:$SYBASE_HOME/OCS-16_0/lib:$SYBASE_HOME/OCS-16_0/lib3p64:$SYBASE_HOME/OCS-16_0/lib3p
```

The `/etc/odbcinst.ini` file should include the following entries\.

```
[Sybase]
Driver=/opt/sap/DataAccess64/ODBC/lib/libsybdrvodb.so
Description=Sybase ODBC driver
```