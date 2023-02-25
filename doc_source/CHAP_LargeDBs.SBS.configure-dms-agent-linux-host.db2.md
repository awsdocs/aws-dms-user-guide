# DB2 LUW<a name="CHAP_LargeDBs.SBS.configure-dms-agent-linux-host.db2"></a>

To install the IBM DB2 for LUW driver on the agent machine, do the following:
+ Install the appropriate version of IBM Data Server Client on the AWS DMS Agent machine:
  + For IBM DB2 for LUW 10\.5, install client version 10\.5\.
  + For IBM DB2 for LUW 11\.1 and 11\.5, install client version 11\.1\.
+ Download and unzip the DB2 Server Software\. For example, `v10.5fp9_linuxx64_server_t.tar.gz` is the package for IBM DB2 for LUW version 10\.5\.
+ Install the DB2 client using the `db2_install command`\. When prompted, choose to install **CLIENT**\.
+ On the AWS DMS Agent machine, create a DB2 instance by running the following commands:

  ```
  adduser <db2_instance_name>
  /opt/ibm/db2/V10.5/instance/db2icrt <db2_instance_name>
  ```

  *Example:*

  ```
  adduser db2inst1
  /opt/ibm/db2/V10.5/instance/db2icrt db2inst1
  ```
+ Add the DB2 driver location to the Linux library path\. To do this, add the following lines to the `site_ arep_login.sh` file:

  ```
  export DB2_HOME=<db2_dirver_path>
  export DB2INSTANCE=<db2_instance_name>
  export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$DB2_HOME/lib64:$DB2_HOME/lib64/gskit_db2
  ```

  *Example:*

  ```
  export DB2_HOME=/opt/ibm/db2/V10.5
  export DB2INSTANCE=db2inst1
  export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$DB2_HOME/lib64:$DB2_HOME/lib64/gskit_db2
  ```
+ Make sure that the `/etc/odbcinst.ini` file contains an entry for IBM DB2 LUW, as shown in the following example:

  ```
  [IBM DB2 ODBC DRIVER]
  Driver = /opt/ibm/db2/V10.5/lib64/libdb2o.so 
  fileusage=1
  dontdlclose=1
  ```
+ Restart the AWS DMS Agent by running the following command in the `/opt/amazon/aws-schema-conversion-tool-dms-agent/bin` directory:

  ```
  ./aws-schema-conversion-tool-dms-agent restart
  ```