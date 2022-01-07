# Converting using the Python API for AWS Glue<a name="CHAP-converting-aws-glue-api-process"></a>

In the following sections, you can find a description of a conversion that calls AWS Glue API operations in Python\. For more information, see [Program AWS Glue ETL scripts in Python](https://docs.aws.amazon.com//glue/latest/dg/aws-glue-programming-python.html) in the* AWS Glue Developer Guide\.*

## Step 1: Create a database<a name="CHAP-converting-aws-glue-step-api-create-db"></a>

The first step is to create a new database in an AWS Glue Data Catalog by using the [AWS SDK API](https://docs.aws.amazon.com/glue/latest/webapi/API_CreateDatabase.html)\. When you define a table in the Data Catalog, you add it to a database\. A database is used to organize the tables in AWS Glue\. 

The following example demonstrates the `create_database` method of the Python API for AWS Glue\.

```
response = client.create_database(
    DatabaseInput={
        'Name': 'database_name’,
        'Description': 'description',
        'LocationUri': 'string',
        'Parameters': {         
            'parameter-name': 'parameter value'
        }
    }
)
```

If you are using Amazon Redshift, the database name is formed as follows\.

```
{redshift_cluster_name}_{redshift_database_name}_{redshift_schema_name}
```

The full name of Amazon Redshift cluster for this example is as follows:

```
rsdbb03.apq1mpqso.us-west-2.redshift.amazonaws.com
```

The following shows an example of a well\-formed database name\. In this case `rsdbb03` is the name, which is the first part of the full name of the cluster endpoint\. The database is named `dev` and the schema is `ora_glue`\.

```
rsdbb03_dev_ora_glue
```

## Step 2: Create a connection<a name="CHAP-converting-aws-glue-step-api-connection"></a>

Create a new connection in a Data Catalog by using the [AWS SDK API](https://docs.aws.amazon.com/glue/latest/webapi/API_CreateConnection.html)\.

The following example demonstrates using the [https://docs.aws.amazon.com/glue/latest/dg/aws-glue-api-catalog-connections.html](https://docs.aws.amazon.com/glue/latest/dg/aws-glue-api-catalog-connections.html) method of the Python API for AWS Glue\. 

 

```
response = client.create_connection(
    ConnectionInput={
        'Name': 'Redshift_abcde03.aabbcc112233.us-west-2.redshift.amazonaws.com_dev',
        'Description': 'Created from SCT',
        'ConnectionType': 'JDBC',
        'ConnectionProperties': {
            'JDBC_CONNECTION_URL': 'jdbc:redshift://aabbcc03.aabbcc112233.us-west-2.redshift.amazonaws.com:5439/dev',
            'USERNAME': 'user_name',
            'PASSWORD': 'password'
        },
        'PhysicalConnectionRequirements': {
            'AvailabilityZone': 'us-west-2c',
            'SubnetId': 'subnet-a1b23c45',
            'SecurityGroupIdList': [
                'sg-000a2b3c', 'sg-1a230b4c', 'sg-aba12c3d', 'sg-1abb2345'
            ]
        }
    }
)
```

The parameters used in `create_connection` are as follows:
+ `Name` \(UTF\-8 string\) – required\. For Amazon Redshift, the connection name is formed as follows: `Redshift_{Endpoint-name}_{redshift-database-name}`, for example:` Redshift_abcde03_dev`
+ `Description` \(UTF\-8 string\) – your description of the connection\.
+ `ConnectionType` \(UTF\-8 string\) – required; the type of connection\. Currently, only JDBC is supported; SFTP is not supported\.
+ `ConnectionProperties` \(dict\) – required; a list of key\-value pairs used as parameters for this connection, including the JDBC connection URL, the user name, and the password\.
+ `PhysicalConnectionRequirements` \(dict\) – physical connection requirements, which include the following:
  + `SubnetId` \(UTF\-8 string\) – the ID of the subnet used by the connection\.
  + `SecurityGroupIdList` \(list\) – the security group ID list used by the connection\.
  + `AvailabilityZone` \(UTF\-8 string\) – required; the Availability Zone that contains the endpoint\. This parameter is deprecated\.

## Step 3: Creating an AWS Glue crawler<a name="CHAP-converting-aws-glue-step-api-crawler"></a>

Next, you create an AWS Glue crawler to populate the AWS Glue catalog\. For more information, see [Cataloging tables with a crawler](https://docs.aws.amazon.com/glue/latest/dg/add-crawler.html) in the AWS Glue Developer Guide\. The first step in adding a crawler is to create a new database in a Data Catalog by using the [AWS SDK API](https://docs.aws.amazon.com//glue/latest/webapi/API_CreateCrawler.html)\. Before you begin, you must first delete any previous version of it by using the `delete_crawler` operation\.

When you create your crawler, a few considerations apply:
+ For the crawler name, use the format `<redshift_node_name>_<redshift_database_name>_<redshift_shema_name>`, for example: `abcde03_dev_ora_glue`
+ Use an IAM role that already exists\. For more information on creating IAM roles, see [Creating IAM roles](https://docs.aws.amazon.com//IAM/latest/UserGuide/id_roles_create.html) in the *IAM User Guide\.*
+ Use the name of the database that you created in the previous steps\.
+ Use the `ConnectionName` parameter, which is required\.
+ For the `path` parameter, use the path to the JDBC target, for example: `dev/ora_glue/%`

The following example deletes an existing crawler and then creates a new one by using the Python API for AWS Glue\. 

```
response = client.delete_crawler(
    Name='crawler_name'
)

response = client.create_crawler(
    Name='crawler_name',
    Role= ‘IAM_role’,
    DatabaseName='database_name’,
    Description='string',
    Targets={
        'S3Targets': [
            {
                'Path': 'string',
                'Exclusions': [
                    'string',
                ]
            },
        ],
        'JdbcTargets': [
            {
                'ConnectionName': ‘ConnectionName’,
                'Path': ‘Include_path’,
                'Exclusions': [
                    'string',
                ]
            },
        ]
    },
    Schedule='string',
    Classifiers=[
        'string',
    ],
    TablePrefix='string',
    SchemaChangePolicy={
        'UpdateBehavior': 'LOG'|'UPDATE_IN_DATABASE',
        'DeleteBehavior': 'LOG'|'DELETE_FROM_DATABASE'|'DEPRECATE_IN_DATABASE'
    },
    Configuration='string'
)
```

Create and then run a crawler that connects to one or more data stores, determines the data structures, and writes tables into the Data Catalog\. You can run your crawler on a schedule, as shown following\.

```
response = client.start_crawler(
    Name='string'
)
```

Because we're using Amazon Redshift as our target for this example, Amazon Redshift data types map to AWS Glue data types in the following way after the crawler runs\.


|  |  | 
| --- |--- |
| Amazon Redshift data type | AWS Glue data type | 
| smallint | smallint | 
| integer | int | 
| bigint | bigint | 
| decimal | decimal\(18,0\) | 
| decimal\(p,s\) | decimal\(p,s\) | 
| real | double | 
| double precision | double | 
| boolean | boolean | 
| char | string | 
| varchar | string | 
| varchar\(n\) | string | 
| date | date | 
| timestamp | timestamp | 
| timestamptz | timestamp | 