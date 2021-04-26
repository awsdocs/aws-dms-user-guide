# Oracle<a name="CHAP_LargeDBs.SBS.configure-dms-agent-linux-host.oracle"></a>

Install Oracle Instant Client for Linux \(x86\-64\) version 11\.2\.0\.3\.0 or later\.

In addition, if not already included in your system, you need to create a symbolic link in the `$ORACLE_HOME\lib directory`\. This link should be called `libclntsh.so`, and should point to a specific version of this file\. For example, on an Oracle 12c client you use the following\.

```
lrwxrwxrwx 1 oracle oracle 63 Oct 2 14:16 libclntsh.so ->/u01/app/oracle/home/lib/libclntsh.so.12.1 
```

In addition, the `LD_LIBRARY_PATH` environment variable should be appended with the Oracle lib directory and added to the `site_arep_login.sh` script under the lib folder of the installation\. Add this script if it doesn't exist\.

```
vi /opt/amazon/aws-schema-conversion-tool-dms-agent/bin/site_arep_login.sh

export ORACLE_HOME=/usr/lib/oracle/12.2/client64; 
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$ORACLE_HOME/lib
```