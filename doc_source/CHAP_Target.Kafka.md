# Using Apache Kafka as a target for AWS Database Migration Service<a name="CHAP_Target.Kafka"></a>

You can use AWS DMS to migrate data to an Apache Kafka cluster\. Apache Kafka is a distributed streaming platform\. You can use Apache Kafka for ingesting and processing streaming data in real\-time\.

AWS also offers Amazon Managed Streaming for Apache Kafka \(Amazon MSK\) to use as an AWS DMS target\. Amazon MSK is a fully managed Apache Kafka streaming service that simplifies the implementation and management of Apache Kafka instances\. It works with open\-source Apache Kafka versions, and you access Amazon MSK instances as AWS DMS targets exactly like any Apache Kafka instance\. For more information, see [What is Amazon MSK?](https://docs.aws.amazon.com/msk/latest/developerguide/what-is-msk.html) in the* Amazon Managed Streaming for Apache Kafka Developer Guide\.*

A Kafka cluster stores streams of records in categories called topics that are divided into partitions\. *Partitions* are uniquely identified sequences of data records \(messages\) in a topic\. Partitions can be distributed across multiple brokers in a cluster to enable parallel processing of a topic’s records\. For more information on topics and partitions and their distribution in Apache Kafka, see [Topics and logs](https://kafka.apache.org/documentation/#intro_topics) and [Distribution](https://kafka.apache.org/documentation/#intro_distribution)\.

AWS Database Migration Service publishes records to a Kafka topic using JSON\. During conversion, AWS DMS serializes each record from the source database into an attribute\-value pair in JSON format\.

To migrate your data from any supported data source to a target Kafka cluster, you use object mapping\. With object mapping, you determine how to structure the data records in the target topic\. You also define a partition key for each table, which Apache Kafka uses to group the data into its partitions\. 

When AWS DMS creates tables on an Apache Kafka target endpoint, it creates as many tables as in the source database endpoint\. AWS DMS also sets several Apache Kafka parameter values\. The cost for the table creation depends on the amount of data and the number of tables to be migrated\.

**Apache Kafka endpoint settings**

You can specify connection details through endpoint settings in the AWS DMS console, or the `--kafka-settings` option in the CLI\. The requirements for each setting follow:
+ `Broker` – Specify the locations of one or more brokers in your Kafka cluster in the form of a comma\-separated list of each `broker-hostname:port`\. An example is `"ec2-12-345-678-901.compute-1.amazonaws.com:2345,ec2-10-987-654-321.compute-1.amazonaws.com:9876"`\. This setting can specify the locations of any or all brokers in the cluster\. The cluster brokers all communicate to handle the partitioning of data records migrated to the topic\.
+ `Topic` – \(Optional\) Specify the topic name with a maximum length of 255 letters and symbols\. You can use period \(\.\), underscore \(\_\), and minus \(\-\)\. Topic names with a period \(\.\) or underscore \(\_\) can collide in internal data structures\. Use either one, but not both of these symbols in the topic name\. If you don't specify a topic name, AWS DMS uses `"kafka-default-topic"` as the migration topic\.
**Note**  
To have AWS DMS create either a migration topic you specify or the default topic, set `auto.create.topics.enable = true` as part of your Kafka cluster configuration\. For more information, see [Limitations when using Apache Kafka as a target for AWS Database Migration Service](#CHAP_Target.Kafka.Limitations)
+ `MessageFormat` – The output format for the records created on the endpoint\. The message format is `JSON` \(default\) or `JSON_UNFORMATTED` \(a single line with no tab\)\.
+ `MessageMaxBytes` – The maximum size in bytes for records created on the endpoint\. The default is 1,000,000\.
**Note**  
You can only use the AWS CLI/SDK to change `MessageMaxBytes` to a non\-default value\. For example, to modify your existing Kafka endpoint and change `MessageMaxBytes`, use the following command\.  

  ```
  aws dms modify-endpoint --endpoint-arn your-endpoint 
  --kafka-settings Broker="broker1-server:broker1-port,broker2-server:broker2-port,...",
  Topic=topic-name,MessageMaxBytes=integer-of-max-message-size-in-bytes
  ```
+ `IncludeTransactionDetails` – Provides detailed transaction information from the source database\. This information includes a commit timestamp, a log position, and values for `transaction_id`, `previous_transaction_id`, and `transaction_record_id` \(the record offset within a transaction\)\. The default is `false`\.
+ `IncludePartitionValue` – Shows the partition value within the Kafka message output, unless the partition type is `schema-table-type`\. The default is `false`\.
+ `PartitionIncludeSchemaTable` – Prefixes schema and table names to partition values, when the partition type is `primary-key-type`\. Doing this increases data distribution among Kafka partitions\. For example, suppose that a `SysBench` schema has thousands of tables and each table has only limited range for a primary key\. In this case, the same primary key is sent from thousands of tables to the same partition, which causes throttling\. The default is `false`\.
+ `IncludeTableAlterOperations` – Includes any data definition language \(DDL\) operations that change the table in the control data, such as `rename-table`, `drop-table`, `add-column`, `drop-column`, and `rename-column`\. The default is `false`\. 
+ `IncludeControlDetails` – Shows detailed control information for table definition, column definition, and table and column changes in the Kafka message output\. The default is `false`\.
+ `IncludeNullAndEmpty` – Include NULL and empty columns in the target\. The default is `false`\.
+ `SecurityProtocol` – Sets a secure connection to a Kafka target endpoint using Transport Layer Security \(TLS\)\. Options include `ssl-authentication`, `ssl-encryption`, and `sasl-ssl`\. Using `sasl-ssl` requires `SaslUsername` and `SaslPassword`\.

You can use settings to help increase the speed of your transfer\. To do so, AWS DMS supports a multithreaded full load to an Apache Kafka target cluster\. AWS DMS supports this multithreading with task settings that include the following:
+ `MaxFullLoadSubTasks` – Use this option to indicate the maximum number of source tables to load in parallel\. AWS DMS loads each table into its corresponding Kafka target table using a dedicated subtask\. The default is 8; the maximum value is 49\.
+ `ParallelLoadThreads` – Use this option to specify the number of threads that AWS DMS uses to load each table into its Kafka target table\. The maximum value for an Apache Kafka target is 32\. You can ask to have this maximum limit increased\.
+ `ParallelLoadBufferSize` – Use this option to specify the maximum number of records to store in the buffer that the parallel load threads use to load data to the Kafka target\. The default value is 50\. The maximum value is 1,000\. Use this setting with `ParallelLoadThreads`\. `ParallelLoadBufferSize` is valid only when there is more than one thread\.
+ `ParallelLoadQueuesPerThread` – Use this option to specify the number of queues each concurrent thread accesses to take data records out of queues and generate a batch load for the target\. The default is 1\. However, for Kafka targets of various payload sizes, the valid range is 5–512 queues per thread\.

You can improve the performance of change data capture \(CDC\) for real\-time data streaming target endpoints like Kafka using task settings to modify the behaviour of the `PutRecords` API call\. To do this, you can specify the number of concurrent threads, queues per thread, and the number of records to store in a buffer using `ParallelApply*` task settings\. For example, suppose you want to perform a CDC load and apply 128 threads in parallel\. You also want to access 64 queues per thread, with 50 records stored per buffer\. 

To promote CDC performance, AWS DMS supports these task settings:
+ `ParallelApplyThreads` – Specifies the number of concurrent threads that AWS DMS uses during a CDC load to push data records to a Kafka target endpoint\. The default value is zero \(0\) and the maximum value is 32\.
+ `ParallelApplyBufferSize` – Specifies the maximum number of records to store in each buffer queue for concurrent threads to push to a Kafka target endpoint during a CDC load\. The default value is 50 and the maximum value is 1,000\. Use this option when `ParallelApplyThreads` specifies more than one thread\. 
+ `ParallelApplyQueuesPerThread` – Specifies the number of queues that each thread accesses to take data records out of queues and generate a batch load for a Kafka endpoint during CDC\.

When using `ParallelApply*` task settings, the `partition-key-type` default is the `primary-key` of the table, not `schema-name.table-name`\.

## Connecting to Kafka using Transport Layer Security \(TLS\)<a name="CHAP_Target.Kafka.TLS"></a>

A Kafka cluster accepts secure connections using Transport Layer Security \(TLS\)\. With DMS, you can use any one of the following three security protocol options to secure a Kafka endpoint connection\.

**SSL encryption \(`server-encryption`\)**  
Clients validate server identity through the server’s certificate\. Then an encrypted connection is made between server and client\.

**SSL authentication \(`mutual-authentication`\)**  
Server and client validate the identity with each other through their own certificates\. Then an encrypted connection is made between server and client\.

**SASL\-SSL \(`mutual-authentication`\)**  
The Simple Authentication and Security Layer \(SASL\) method replaces the client’s certificate with a user name and password to validate a client identity\. Specifically, you provide a user name and password that the server has registered so that the server can validate the identity of a client\. Then an encrypted connection is made between server and client\.

**Important**  
Apache Kafka and Amazon MSK accept resolved certificates\. This is a known limitation of Kafka and Amazon MSK to be addressed\. For more information, see [Apache Kafka issues, KAFKA\-3700](https://issues.apache.org/jira/browse/KAFKA-3700)\.  
If you're using Amazon MSK, consider using access control lists \(ACLs\) as a workaround to this known limitation\. For more information about using ACLs, see [Apache Kafka ACLs](https://docs.aws.amazon.com/msk/latest/developerguide/msk-acls.html) section of *Amazon Managed Streaming for Apache Kafka Developer Guide*\.  
If you're using a self\-managed Kafka cluster, see [Comment dated 21/Oct/18](https://issues.apache.org/jira/browse/KAFKA-3700?focusedCommentId=16658376) for information about configuring your cluster\.

### Using SSL encryption with Amazon MSK or a self\-managed Kafka cluster<a name="CHAP_Target.Kafka.TLS.SSLencryption"></a>

You can use SSL encryption to secure an endpoint connection to Amazon MSK or a self\-managed Kafka cluster\. When you use the SSL encryption authentication method, clients validate a server's identity through the server’s certificate\. Then an encrypted connection is made between server and client\.

**To use SSL encryption to connect to Amazon MSK**
+ Set the security protocol endpoint setting \(`SecurityProtocol`\) using the `ssl-encryption` option when you create your target Kafka endpoint\. 

  The JSON example following sets the security protocol as SSL encryption\.

```
"KafkaSettings": {
    "SecurityProtocol": "ssl-encryption", 
}
```

**To use SSL encryption for a self\-managed Kafka cluster**

1. If you're using a private Certification Authority \(CA\) in your on\-premises Kafka cluster, upload your private CA cert and get an Amazon Resource Name \(ARN\)\. 

1. Set the security protocol endpoint setting \(`SecurityProtocol`\) using the `ssl-encryption` option when you create your target Kafka endpoint\. The JSON example following sets the security protocol as `ssl-encryption`\.

   ```
   "KafkaSettings": {
       "SecurityProtocol": "ssl-encryption", 
   }
   ```

1. If you're using a private CA, set `SslCaCertificateArn` in the ARN you got in the first step above\.

### Using SSL authentication<a name="CHAP_Target.Kafka.TLS.SSLauthentication"></a>

You can use SSL authentication to secure an endpoint connection to Amazon MSK or a self\-managed Kafka cluster\.

To enable client authentication and encryption using SSL authentication to connect to Amazon MSK, do the following:
+ Prepare a private key and public certificate for Kafka\.
+ Upload certificates to the DMS certificate manager\.
+ Create a Kafka target endpoint with corresponding certificate ARNs specified in Kafka endpoint settings\.

**To prepare a private key and public certificate for Amazon MSK**

1. Create an EC2 instance and set up a client to use authentication as described in steps 1 through 9 in the [Client Authentication](https://docs.aws.amazon.com/msk/latest/developerguide/msk-authentication.html) section of *Amazon Managed Streaming for Apache Kafka Developer Guide*\.

   After you complete those steps, you have a Certificate\-ARN \(the public certificate ARN saved in ACM\), and a private key contained within a `kafka.client.keystore.jks` file\.

1. Get the public certificate and copy the certificate to the `signed-certificate-from-acm.pem` file, using the command following:

   ```
   aws acm-pca get-certificate --certificate-authority-arn Private_CA_ARN --certificate-arn Certificate_ARN
   ```

   That command returns information similar to the example following:

   ```
   {"Certificate": "123", "CertificateChain": "456"}
   ```

   You then copy your equivalent of `"123"` to the `signed-certificate-from-acm.pem` file\.

1. Get the private key by importing the `msk-rsa` key from `kafka.client.keystore.jks to keystore.p12`, as shown in the following example\.

   ```
   keytool -importkeystore \
   -srckeystore kafka.client.keystore.jks \
   -destkeystore keystore.p12 \
   -deststoretype PKCS12 \
   -srcalias msk-rsa-client \
   -deststorepass test1234 \
   -destkeypass test1234
   ```

1. Use the following command to export `keystore.p12` into `.pem` format\. 

   ```
   Openssl pkcs12 -in keystore.p12 -out encrypted-private-client-key.pem –nocerts
   ```

   The **Enter PEM pass phrase** message appears and identifies the key that is applied to encrypt the certificate\.

1. Remove bag attributes and key attributes from the `.pem` file to make sure that the first line starts with the following string\.

   ```
                                   ---BEGIN ENCRYPTED PRIVATE KEY---
   ```

**To upload a public certificate and private key to the DMS certificate manager and test the connection to Amazon MSK**

1. Upload to DMS certificate manager using the following command\.

   ```
   aws dms import-certificate --certificate-identifier signed-cert --certificate-pem file://path to signed cert
   aws dms import-certificate --certificate-identifier private-key —certificate-pem file://path to private key
   ```

1. Create an Amazon MSK target endpoint and test connection to make sure that TLS authentication works\.

   ```
   aws dms create-endpoint --endpoint-identifier $endpoint-identifier --engine-name kafka --endpoint-type target --kafka-settings 
   '{"Broker": "b-0.kafka260.aaaaa1.a99.kafka.us-east-1.amazonaws.com:0000", "SecurityProtocol":"ssl-authentication", 
   "SslClientCertificateArn": "arn:aws:dms:us-east-1:012346789012:cert:",
   "SslClientKeyArn": "arn:aws:dms:us-east-1:0123456789012:cert:","SslClientKeyPassword":"test1234"}'
   aws dms test-connection -replication-instance-arn=$rep_inst_arn —endpoint-arn=$kafka_tar_arn_msk
   ```

**Important**  
You can use SSL authentication to secure a connection to a self\-managed Kafka cluster\. In some cases, you might use a private Certification Authority \(CA\) in your on\-premises Kafka cluster\. If so, upload your CA chain, public certificate, and private key to the DMS certificate manager\. Then, use the corresponding Amazon Resource Name \(ARN\) in your endpoint settings when you create your on\-premises Kafka target endpoint\.

**To prepare a private key and signed certificate for a self\-managed Kafka cluster**

1. Generate a key pair as shown in example following\.

   ```
   keytool -genkey -keystore kafka.server.keystore.jks -validity 300 -storepass your-keystore-password 
   -keypass your-key-passphrase -dname "CN=your-cn-name" 
   -alias alias-of-key-pair -storetype pkcs12 -keyalg RSA
   ```

1. Generate a Certificate Sign Request \(CSR\)\. 

   ```
   keytool -keystore kafka.server.keystore.jks -certreq -file server-cert-sign-request-rsa -alias on-premise-rsa -storepass your-key-store-password 
   -keypass your-key-password
   ```

1. Use the CA in your cluster truststore to sign the CSR\. If you don't have a CA, you can create your own private CA\.

   ```
   openssl req -new -x509 -keyout ca-key -out ca-cert -days validate-days                            
   ```

1. Import `ca-cert` into the server truststore and keystore\. If you don't have a truststore, use the command following to create the truststore and import `ca-cert `into it\. 

   ```
   keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert
   keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert
   ```

1. Sign the certificate\.

   ```
   openssl x509 -req -CA ca-cert -CAkey ca-key -in server-cert-sign-request-rsa -out signed-server-certificate.pem 
   -days validate-days -CAcreateserial -passin pass:ca-password
   ```

1. Import the signed certificate to the keystore\.

   ```
   keytool -keystore kafka.server.keystore.jks -import -file signed-certificate.pem -alias on-premise-rsa -storepass your-keystore-password 
   -keypass your-key-password
   ```

1. Use the following command to import the `on-premise-rsa` key from `kafka.server.keystore.jks` to `keystore.p12`\.

   ```
   keytool -importkeystore \
   -srckeystore kafka.server.keystore.jks \
   -destkeystore keystore.p12 \
   -deststoretype PKCS12 \
   -srcalias on-premise-rsa \
   -deststorepass your-truststore-password \
   -destkeypass your-key-password
   ```

1. Use the command following to export `keystore.p12` into `.pem` format\.

   ```
   Openssl pkcs12 -in keystore.p12 -out encrypted-private-server-key.pem –nocerts
   ```

1. Upload `encrypted-private-server-key.pem`, `signed-certificate.pem`, and `ca-cert` to the DMS certificate manager\.

1. Create an endpoint by using the returned ARNs\.

   ```
   aws dms create-endpoint --endpoint-identifier $endpoint-identifier --engine-name kafka --endpoint-type target --kafka-settings 
   '{"Broker": "b-0.kafka260.aaaaa1.a99.kafka.us-east-1.amazonaws.com:9092", "SecurityProtocol":"ssl-authentication", 
   "SslClientCertificateArn": "your-client-cert-arn","SslClientKeyArn": "your-client-key-arn","SslClientKeyPassword":"your-client-key-password", 
   "SslCaCertificateArn": "your-ca-certificate-arn"}'
                               
   aws dms test-connection -replication-instance-arn=$rep_inst_arn —endpoint-arn=$kafka_tar_arn_msk
   ```

### Using SASL\-SSL authentication to connect to Amazon MSK<a name="CHAP_Target.Kafka.TLS.SSL-SASL"></a>

The Simple Authentication and Security Layer \(SASL\) method uses a user name and password to validate a client identity, and makes an encrypted connection between server and client\.

To use SASL, you first create a secure user name and password when you set up your Amazon MSK cluster\. For a description how to set up a secure user name and password for an Amazon MSK cluster, see [Setting up SASL/SCRAM authentication for an Amazon MSK cluster](https://docs.aws.amazon.com/msk/latest/developerguide/msk-password.html#msk-password-tutorial) in the *Amazon Managed Streaming for Apache Kafka Developer Guide*\.

Then, when you create your Kafka target endpoint, set the security protocol endpoint setting \(`SecurityProtocol`\) using the `sasl-ssl` option\. You also set `SaslUsername` and `SaslPassword` options\. Make sure these are consistent with the secure user name and password that you created when you first set up your Amazon MSK cluster, as shown in JSON example following\.

```
                   
"KafkaSettings": {
    "SecurityProtocol": "sasl-ssl",
    "SaslUsername":"Amazon MSK cluster secure user name",
    "SaslPassword":"Amazon MSK cluster secure password"                    
}
```

**Note**  
Currently, AWS DMS supports only public CA backed SASL/SSL\. DMS doesn't support SASL/SSL for use with self\-managed Kafka that is backed by private CA\. 

## Using a before image to view original values of CDC rows for Apache Kafka as a target<a name="CHAP_Target.Kafka.BeforeImage"></a>

When writing CDC updates to a data\-streaming target like Kafka you can view a source database row's original values before change by an update\. To make this possible, AWS DMS populates a *before image* of update events based on data supplied by the source database engine\. 

Different source database engines provide different amounts of information for a before image: 
+ Oracle provides updates to columns only if they change\. 
+ PostgreSQL provides only data for columns that are part of the primary key \(changed or not\)\. 
+ MySQL generally provides data for all columns \(changed or not\)\.

To enable before imaging to add original values from the source database to the AWS DMS output, use either the `BeforeImageSettings` task setting or the `add-before-image-columns` parameter\. This parameter applies a column transformation rule\. 

`BeforeImageSettings` adds a new JSON attribute to every update operation with values collected from the source database system, as shown following\.

```
"BeforeImageSettings": {
    "EnableBeforeImage": boolean,
    "FieldName": string,  
    "ColumnFilter": pk-only (default) / non-lob / all (but only one)
}
```

**Note**  
Apply `BeforeImageSettings` to full load plus CDC tasks \(which migrate existing data and replicate ongoing changes\), or to CDC only tasks \(which replicate data changes only\)\. Don't apply `BeforeImageSettings` to tasks that are full load only\.

For `BeforeImageSettings` options, the following applies:
+ Set the `EnableBeforeImage` option to `true` to enable before imaging\. The default is `false`\. 
+ Use the `FieldName` option to assign a name to the new JSON attribute\. When `EnableBeforeImage` is `true`, `FieldName` is required and can't be empty\.
+ The `ColumnFilter` option specifies a column to add by using before imaging\. To add only columns that are part of the table's primary keys, use the default value, `pk-only`\. To add only columns that are not of LOB type, use `non-lob`\. To add any column that has a before image value, use `all`\. 

  ```
  "BeforeImageSettings": {
      "EnableBeforeImage": true,
      "FieldName": "before-image",
      "ColumnFilter": "pk-only"
    }
  ```

### Using a before image transformation rule<a name="CHAP_Target.Kafka.BeforeImage.Transform-Rule"></a>

As as an alternative to task settings, you can use the `add-before-image-columns` parameter, which applies a column transformation rule\. With this parameter, you can enable before imaging during CDC on data streaming targets like Kafka\.

By using `add-before-image-columns` in a transformation rule, you can apply more fine\-grained control of the before image results\. Transformation rules enable you to use an object locator that gives you control over tables selected for the rule\. Also, you can chain transformation rules together, which allows different rules to be applied to different tables\. You can then manipulate the columns produced by using other rules\. 

**Note**  
Don't use the `add-before-image-columns` parameter together with the `BeforeImageSettings` task setting within the same task\. Instead, use either the parameter or the setting, but not both, for a single task\.

A `transformation` rule type with the `add-before-image-columns` parameter for a column must provide a `before-image-def` section\. The following shows an example\.

```
    {
      "rule-type": "transformation",
      …
      "rule-target": "column",
      "rule-action": "add-before-image-columns",
      "before-image-def":{
        "column-filter": one-of  (pk-only / non-lob / all),
        "column-prefix": string,
        "column-suffix": string,
      }
    }
```

The value of `column-prefix` is prepended to a column name, and the default value of `column-prefix` is `BI_`\. The value of `column-suffix` is appended to the column name, and the default is empty\. Don't set both `column-prefix` and `column-suffix` to empty strings\.

Choose one value for `column-filter`\. To add only columns that are part of table primary keys, choose `pk-only` \. Choose `non-lob` to only add columns that are not of LOB type\. Or choose `all` to add any column that has a before\-image value\.

### Example for a before image transformation rule<a name="CHAP_Target.Kafka.BeforeImage.Example"></a>

The transformation rule in the following example adds a new column called `BI_emp_no` in the target\. So a statement like `UPDATE employees SET emp_no = 3 WHERE emp_no = 1;` populates the `BI_emp_no` field with 1\. When you write CDC updates to Amazon S3 targets, the `BI_emp_no` column makes it possible to tell which original row was updated\.

```
{
  "rules": [
    {
      "rule-type": "selection",
      "rule-id": "1",
      "rule-name": "1",
      "object-locator": {
        "schema-name": "%",
        "table-name": "%"
      },
      "rule-action": "include"
    },
    {
      "rule-type": "transformation",
      "rule-id": "2",
      "rule-name": "2",
      "rule-target": "column",
      "object-locator": {
        "schema-name": "%",
        "table-name": "employees"
      },
      "rule-action": "add-before-image-columns",
      "before-image-def": {
        "column-prefix": "BI_",
        "column-suffix": "",
        "column-filter": "pk-only"
      }
    }
  ]
}
```

For information on using the `add-before-image-columns` rule action, see [ Transformation rules and actions](CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Transformations.md)\.

## Limitations when using Apache Kafka as a target for AWS Database Migration Service<a name="CHAP_Target.Kafka.Limitations"></a>

The following limitations apply when using Apache Kafka as a target:
+ AWS DMS supports a maximum message size of 1 MiB for a Kafka target\.
+ Make sure to configure both your AWS DMS replication instance and your Kafka cluster in the same virtual private cloud \(VPC\) based on Amazon VPC and in the same security group\. The Kafka cluster can either be an Amazon MSK instance or your own Kafka instance running on Amazon EC2\. For more information, see [Setting up a network for a replication instance](CHAP_ReplicationInstance.VPC.md)\.
**Note**  
To specify a security group for Amazon MSK, on the **Create cluster** page, choose **Advanced settings**, select **Customize settings**, and select the security group or accept the default if it is the same as for your replication instance\.
+ Specify a Kafka configuration file for your cluster with properties that allow AWS DMS to automatically create new topics\. Include the setting, `auto.create.topics.enable = true`\. If you are using Amazon MSK, you can specify the default configuration when you create your Kafka cluster, then change the `auto.create.topics.enable` setting to `true`\. For more information about the default configuration settings, see [The default Amazon MSK configuration](https://docs.aws.amazon.com/msk/latest/developerguide/msk-default-configuration.html) in the *Amazon Managed Streaming for Apache Kafka Developer Guide*\. If you need to modify an existing Kafka cluster created using Amazon MSK, run the AWS CLI command `aws kafka create-configuration` to update your Kafka configuration, as in the example following:

  ```
  14:38:41 $ aws kafka create-configuration --name "kafka-configuration" --kafka-versions "2.2.1" --server-properties file://~/kafka_configuration
  {
      "LatestRevision": {
          "Revision": 1,
          "CreationTime": "2019-09-06T14:39:37.708Z"
      },
      "CreationTime": "2019-09-06T14:39:37.708Z",
      "Name": "kafka-configuration",
      "Arn": "arn:aws:kafka:us-east-1:111122223333:configuration/kafka-configuration/7e008070-6a08-445f-9fe5-36ccf630ecfd-3"
  }
  ```

  Here, `//~/kafka_configuration` is the configuration file you have created with the required property settings\.

  If you are using your own Kafka instance installed on Amazon EC2, modify the Kafka cluster configuration with similar property settings, including `auto.create.topics.enable = true`, using the options provided with your instance\.
+ AWS DMS publishes each update to a single record in the source database as one data record \(message\) in a given Kafka topic regardless of transactions\.
+ AWS DMS supports the following two forms for partition keys:
  + `SchemaName.TableName`: A combination of the schema and table name\.
  + `${AttributeName}`: The value of one of the fields in the JSON, or the primary key of the table in the source database\.

## Using object mapping to migrate data to a Kafka topic<a name="CHAP_Target.Kafka.ObjectMapping"></a>

AWS DMS uses table\-mapping rules to map data from the source to the target Kafka topic\. To map data to a target topic, you use a type of table\-mapping rule called object mapping\. You use object mapping to define how data records in the source map to the data records published to a Kafka topic\. 

Kafka topics don't have a preset structure other than having a partition key\.

To create an object\-mapping rule, specify `rule-type` as `object-mapping`\. This rule specifies what type of object mapping you want to use\. 

The structure for the rule is as follows\.

```
{
    "rules": [
        {
            "rule-type": "object-mapping",
            "rule-id": "id",
            "rule-name": "name",
            "rule-action": "valid object-mapping rule action",
            "object-locator": {
                "schema-name": "case-sensitive schema name",
                "table-name": ""
            }
        }
    ]
}
```

AWS DMS currently supports `map-record-to-record` and `map-record-to-document` as the only valid values for the `rule-action` parameter\. The `map-record-to-record` and `map-record-to-document` values specify what AWS DMS does by default to records that aren't excluded as part of the `exclude-columns` attribute list\. These values don't affect the attribute mappings in any way\. 

Use `map-record-to-record` when migrating from a relational database to a Kafka topic\. This rule type uses the `taskResourceId.schemaName.tableName` value from the relational database as the partition key in the Kafka topic and creates an attribute for each column in the source database\. When using `map-record-to-record`, for any column in the source table not listed in the `exclude-columns` attribute list, AWS DMS creates a corresponding attribute in the target topic\. This corresponding attribute is created regardless of whether that source column is used in an attribute mapping\. 

One way to understand `map-record-to-record` is to see it in action\. For this example, assume that you are starting with a relational database table row with the following structure and data\.


| FirstName | LastName | StoreId | HomeAddress | HomePhone | WorkAddress | WorkPhone | DateofBirth | 
| --- | --- | --- | --- | --- | --- | --- | --- | 
| Randy | Marsh | 5 | 221B Baker Street | 1234567890 | 31 Spooner Street, Quahog  | 9876543210 | 02/29/1988 | 

To migrate this information from a schema named `Test` to a Kafka topic, you create rules to map the data to the target topic\. The following rule illustrates the mapping\. 

```
{
    "rules": [
        {
            "rule-type": "selection",
            "rule-id": "1",
            "rule-name": "1",
            "rule-action": "include",
            "object-locator": {
                "schema-name": "Test",
                "table-name": "%"
            }
        },
        {
            "rule-type": "object-mapping",
            "rule-id": "2",
            "rule-name": "DefaultMapToKafka",
            "rule-action": "map-record-to-record",
            "object-locator": {
                "schema-name": "Test",
                "table-name": "Customers"
            }
        }
    ]
}
```

Given a Kafka topic and a partition key \(in this case, `taskResourceId.schemaName.tableName`\), the following illustrates the resulting record format using our sample data in the Kafka target topic: 

```
  {
     "FirstName": "Randy",
     "LastName": "Marsh",
     "StoreId":  "5",
     "HomeAddress": "221B Baker Street",
     "HomePhone": "1234567890",
     "WorkAddress": "31 Spooner Street, Quahog",
     "WorkPhone": "9876543210",
     "DateOfBirth": "02/29/1988"
  }
```

### Restructuring data with attribute mapping<a name="CHAP_Target.Kafka.AttributeMapping"></a>

You can restructure the data while you are migrating it to a Kafka topic using an attribute map\. For example, you might want to combine several fields in the source into a single field in the target\. The following attribute map illustrates how to restructure the data\.

```
{
    "rules": [
        {
            "rule-type": "selection",
            "rule-id": "1",
            "rule-name": "1",
            "rule-action": "include",
            "object-locator": {
                "schema-name": "Test",
                "table-name": "%"
            }
        },
        {
            "rule-type": "object-mapping",
            "rule-id": "2",
            "rule-name": "TransformToKafka",
            "rule-action": "map-record-to-record",
            "target-table-name": "CustomerData",
            "object-locator": {
                "schema-name": "Test",
                "table-name": "Customers"
            },
            "mapping-parameters": {
                "partition-key-type": "attribute-name",
                "partition-key-name": "CustomerName",
                "exclude-columns": [
                    "firstname",
                    "lastname",
                    "homeaddress",
                    "homephone",
                    "workaddress",
                    "workphone"
                ],
                "attribute-mappings": [
                    {
                        "target-attribute-name": "CustomerName",
                        "attribute-type": "scalar",
                        "attribute-sub-type": "string",
                        "value": "${lastname}, ${firstname}"
                    },
                    {
                        "target-attribute-name": "ContactDetails",
                        "attribute-type": "document",
                        "attribute-sub-type": "json",
                        "value": {
                            "Home": {
                                "Address": "${homeaddress}",
                                "Phone": "${homephone}"
                            },
                            "Work": {
                                "Address": "${workaddress}",
                                "Phone": "${workphone}"
                            }
                        }
                    }
                ]
            }
        }
    ]
}
```

To set a constant value for `partition-key`, specify a `partition-key` value\. For example, you might do this to force all the data to be stored in a single partition\. The following mapping illustrates this approach\. 

```
{
    "rules": [
        {
            "rule-type": "selection",
            "rule-id": "1",
            "rule-name": "1",
            "object-locator": {
                "schema-name": "Test",
                "table-name": "%"
            },
            "rule-action": "include"
        },
        {
            "rule-type": "object-mapping",
            "rule-id": "1",
            "rule-name": "TransformToKafka",
            "rule-action": "map-record-to-document",
            "object-locator": {
                "schema-name": "Test",
                "table-name": "Customer"
            },
            "mapping-parameters": {
                "partition-key": {
                    "value": "ConstantPartitionKey"
                },
                "exclude-columns": [
                    "FirstName",
                    "LastName",
                    "HomeAddress",
                    "HomePhone",
                    "WorkAddress",
                    "WorkPhone"
                ],
                "attribute-mappings": [
                    {
                        "attribute-name": "CustomerName",
                        "value": "${FirstName},${LastName}"
                    },
                    {
                        "attribute-name": "ContactDetails",
                        "value": {
                            "Home": {
                                "Address": "${HomeAddress}",
                                "Phone": "${HomePhone}"
                            },
                            "Work": {
                                "Address": "${WorkAddress}",
                                "Phone": "${WorkPhone}"
                            }
                        }
                    },
                    {
                        "attribute-name": "DateOfBirth",
                        "value": "${DateOfBirth}"
                    }
                ]
            }
        }
    ]
}
```

**Note**  
The `partition-key` value for a control record that is for a specific table is `TaskId.SchemaName.TableName`\. The `partition-key` value for a control record that is for a specific task is that record's `TaskId`\. Specifying a `partition-key` value in the object mapping has no impact on the `partition-key` for a control record\.

### Message format for Apache Kafka<a name="CHAP_Target.Kafka.Messageformat"></a>

The JSON output is simply a list of key\-value pairs\. 

**RecordType**  
The record type can be either data or control\. *Data records *represent the actual rows in the source\. *Control records* are for important events in the stream, for example a restart of the task\.

**Operation**  
For data records, the operation can be `create`, `read`, `update`, or `delete`\.  
For control records, the operation can be `TruncateTable` or `DropTable`\. 

**SchemaName**  
The source schema for the record\. This field can be empty for a control record\.

**TableName**  
The source table for the record\. This field can be empty for a control record\.

**Timestamp**  
The timestamp for when the JSON message was constructed\. The field is formatted with the ISO 8601 format\.