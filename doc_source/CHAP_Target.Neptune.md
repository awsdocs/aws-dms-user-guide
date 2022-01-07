# Using Amazon Neptune as a target for AWS Database Migration Service<a name="CHAP_Target.Neptune"></a>

Amazon Neptune is a fast, reliable, fully managed graph database service that makes it easy to build and run applications that work with highly connected datasets\. The core of Neptune is a purpose\-built, high\-performance graph database engine\. This engine is optimized for storing billions of relationships and querying the graph with milliseconds latency\. Neptune supports the popular graph query languages Apache TinkerPop Gremlin and W3C's SPARQL\. For more information on Amazon Neptune, see [What is Amazon Neptune?](https://docs.aws.amazon.com/neptune/latest/userguide/intro.html) in the *Amazon Neptune User Guide*\. 

Without a graph database such as Neptune, you probably model highly connected data in a relational database\. Because the data has potentially dynamic connections, applications that use such data sources have to model connected data queries in SQL\. This approach requires you to write an extra layer to convert graph queries into SQL\. Also, relational databases come with schema rigidity\. Any changes in the schema to model changing connections require downtime and additional maintenance of the query conversion to support the new schema\. The query performance is also another big constraint to consider while designing your applications\.

Graph databases can greatly simplify such situations\. Free from a schema, a rich graph query layer \(Gremlin or SPARQL\) and indexes optimized for graph queries increase flexibility and performance\. The Amazon Neptune graph database also has enterprise features such as encryption at rest, a secure authorization layer, default backups, Multi\-AZ support, read replica support, and others\.

Using AWS DMS, you can migrate relational data that models a highly connected graph to a Neptune target endpoint from a DMS source endpoint for any supported SQL database\.

For more details, see the following\.

**Topics**
+ [Overview of migrating to Amazon Neptune as a target](#CHAP_Target.Neptune.MigrationOverview)
+ [Specifying endpoint settings for Amazon Neptune as a target](#CHAP_Target.Neptune.EndpointSettings)
+ [Creating an IAM service role for accessing Amazon Neptune as a target](#CHAP_Target.Neptune.ServiceRole)
+ [Specifying graph\-mapping rules using Gremlin and R2RML for Amazon Neptune as a target](#CHAP_Target.Neptune.GraphMapping)
+ [Data types for Gremlin and R2RML migration to Amazon Neptune as a target](#CHAP_Target.Neptune.DataTypes)
+ [Limitations of using Amazon Neptune as a target](#CHAP_Target.Neptune.Limitations)

## Overview of migrating to Amazon Neptune as a target<a name="CHAP_Target.Neptune.MigrationOverview"></a>

Before starting a migration to a Neptune target, create the following resources in your AWS account:
+ A Neptune cluster for the target endpoint\. 
+ A SQL relational database supported by AWS DMS for the source endpoint\. 
+ An Amazon S3 bucket for the target endpoint\. Create this S3 bucket in the same AWS Region as your Neptune cluster\. AWS DMS uses this S3 bucket as intermediate file storage for the target data that it bulk loads to the Neptune database\. For more information on creating an S3 bucket, see [Creating a bucket](https://docs.aws.amazon.com/AmazonS3/latest/gsg/CreatingABucket.html) in the *Amazon Simple Storage Service Getting Started Guide\.*
+ A virtual private cloud \(VPC\) endpoint for S3 in the same VPC as the Neptune cluster\. 
+ An AWS Identity and Access Management \(IAM\) role that includes an IAM policy\. This policy should specify the `GetObject`, `PutObject`, `DeleteObject` and `ListObject` permissions to the S3 bucket for your target endpoint\. This role is assumed by both AWS DMS and Neptune with IAM access to both the target S3 bucket and the Neptune database\. For more information, see [Creating an IAM service role for accessing Amazon Neptune as a target](#CHAP_Target.Neptune.ServiceRole)\.

After you have these resources, setting up and starting a migration to a Neptune target is similar to any full load migration using the console or DMS API\. However, a migration to a Neptune target requires some unique steps\.

**To migrate an AWS DMS relational database to Neptune**

1. Create a replication instance as described in [Creating a replication instance](CHAP_ReplicationInstance.Creating.md)\.

1. Create and test a SQL relational database supported by AWS DMS for the source endpoint\.

1. Create and test the target endpoint for your Neptune database\. 

   To connect the target endpoint to the Neptune database, specify the server name for either the Neptune cluster endpoint or the Neptune writer instance endpoint\. Also, specify the S3 bucket folder for AWS DMS to store its intermediate files for bulk load to the Neptune database\. 

   During migration, AWS DMS stores all migrated target data in this S3 bucket folder up to a maximum file size that you specify\. When this file storage reaches this maximum size, AWS DMS bulk loads the stored S3 data into the target database\. It clears the folder to enable storage of any additional target data for subsequent loading to the target database\. For more information on specifying these settings, see [Specifying endpoint settings for Amazon Neptune as a target](#CHAP_Target.Neptune.EndpointSettings)\.

1. Create a full\-load replication task with the resources created in steps 1–3 and do the following: 

   1. Use task table mapping as usual to identify specific source schemas, tables, and views to migrate from your relational database using appropriate selection and transformation rules\. For more information, see [Using table mapping to specify task settings](CHAP_Tasks.CustomizingTasks.TableMapping.md)\. 

   1. Specify target mappings by choosing one of the following to specify mapping rules from source tables and views to your Neptune target database graph:
      + Gremlin JSON – For information on using Gremlin JSON to load a Neptune database, see [Gremlin load data format](https://docs.aws.amazon.com/neptune/latest/userguide/bulk-load-tutorial-format-gremlin.html) in the *Amazon Neptune User Guide*\.
      + SPARQL RDB to Resource Description Framework Mapping Language \(R2RML\) – For information on using SPARQL R2RML, see the W3C specification [R2RML: RDB to RDF mapping language](https://www.w3.org/TR/r2rml/)\.

   1. Do one of the following:
      + Using the AWS DMS console, specify graph\-mapping options using **Graph mapping rules** on the **Create database migration task** page\. 
      + Using the AWS DMS API, specify these options using the `TaskData` request parameter of the `CreateReplicationTask` API call\. 

      For more information and examples using Gremlin JSON and SPARQL R2RML to specify graph\-mapping rules, see [Specifying graph\-mapping rules using Gremlin and R2RML for Amazon Neptune as a target](#CHAP_Target.Neptune.GraphMapping)\.

1. Start the replication for your migration task\.

## Specifying endpoint settings for Amazon Neptune as a target<a name="CHAP_Target.Neptune.EndpointSettings"></a>

To create or modify a target endpoint, you can use the console or the `CreateEndpoint` or `ModifyEndpoint` API operations\. 

For a Neptune target in the AWS DMS console, specify **Endpoint\-specific settings** on the **Create endpoint** or **Modify endpoint** console page\. For `CreateEndpoint` and `ModifyEndpoint`, specify request parameters for the `NeptuneSettings` option\. The following example shows how to do this using the CLI\. 

```
dms create-endpoint --endpoint-identifier my-neptune-target-endpoint
--endpoint-type target --engine-name neptune 
--server-name my-neptune-db.cluster-cspckvklbvgf.us-east-1.neptune.amazonaws.com 
--port 8192
--neptune-settings 
     '{"ServiceAccessRoleArn":"arn:aws:iam::123456789012:role/myNeptuneRole",
       "S3BucketName":"my-bucket",
       "S3BucketFolder":"my-bucket-folder",
       "ErrorRetryDuration":57,
       "MaxFileSize":100, 
       "MaxRetryCount": 10, 
       "IAMAuthEnabled":false}‘
```

Here, the CLI `--server-name` option specifies the server name for the Neptune cluster writer endpoint\. Or you can specify the server name for a Neptune writer instance endpoint\. 

The `--neptune-settings` option request parameters follow:
+ `ServiceAccessRoleArn` – \(Required\) The Amazon Resource Name \(ARN\) of the service role that you created for the Neptune target endpoint\. For more information, see [Creating an IAM service role for accessing Amazon Neptune as a target](#CHAP_Target.Neptune.ServiceRole)\.
+ `S3BucketName` – \(Required\) The name of the S3 bucket where DMS can temporarily store migrated graph data in \.csv files before bulk loading it to the Neptune target database\. DMS maps the SQL source data to graph data before storing it in these \.csv files\.
+ `S3BucketFolder` – \(Required\) A folder path where you want DMS to store migrated graph data in the S3 bucket specified by `S3BucketName`\.
+ `ErrorRetryDuration` – \(Optional\) The number of milliseconds for DMS to wait to retry a bulk load of migrated graph data to the Neptune target database before raising an error\. The default is 250\.
+ `MaxFileSize` – \(Optional\) The maximum size in KB of migrated graph data stored in a \.csv file before DMS bulk loads the data to the Neptune target database\. The default is 1,048,576 KB \(1 GB\)\. If successful, DMS clears the bucket, ready to store the next batch of migrated graph data\.
+ `MaxRetryCount` – \(Optional\) The number of times for DMS to retry a bulk load of migrated graph data to the Neptune target database before raising an error\. The default is 5\.
+ `IAMAuthEnabled` – \(Optional\) If you want IAM authorization enabled for this endpoint, set this parameter to `true` and attach the appropriate IAM policy document to your service role specified by `ServiceAccessRoleArn`\. The default is `false`\.

## Creating an IAM service role for accessing Amazon Neptune as a target<a name="CHAP_Target.Neptune.ServiceRole"></a>

To access Neptune as a target, create a service role using IAM\. Depending on your Neptune endpoint configuration, attach to this role some or all of the following IAM policy and trust documents\. When you create the Neptune endpoint, you provide the ARN of this service role\. Doing so enables AWS DMS and Amazon Neptune to assume permissions to access both Neptune and its associated Amazon S3 bucket\.

If you set the `IAMAuthEnabled` parameter in `NeptuneSettings` to `true` in your Neptune endpoint configuration, attach an IAM policy like the following to your service role\. If you set `IAMAuthEnabled` to `false`, you can ignore this policy\.

```
// Policy to access Neptune

    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Sid": "VisualEditor0",
                "Effect": "Allow",
                "Action": "neptune-db:*",
                "Resource": "arn:aws:neptune-db:us-east-1:123456789012:cluster-CLG7H7FHK54AZGHEH6MNS55JKM/*"
            }
        ]
    }
```

The preceding IAM policy allows full access to the Neptune target cluster specified by `Resource`\.

Attach an IAM policy like the following to your service role\. This policy allows DMS to temporarily store migrated graph data in the S3 bucket that you created for bulk loading to the Neptune target database\.

```
//Policy to access S3 bucket

{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "ListObjectsInBucket0",
            "Effect": "Allow",
            "Action": "s3:ListBucket",
            "Resource": [
                "arn:aws:s3:::my-bucket"
            ]
        },
        {
            "Sid": "AllObjectActions",
            "Effect": "Allow",
            "Action":
                "s3:GetObject",
                "s3:PutObject",
                "s3:DeleteObject",
        
            "Resource": [
                "arn:aws:s3:::my-bucket/"
            ],
        {
            "Sid": "ListObjectsInBucket1",
            "Effect": "Allow",
            "Action": "s3:ListBucket",
            "Resource": [
                "arn:aws:s3:::my-bucket",
                "arn:aws:s3:::my-bucket/"
            ]
        }
    ]
}
```

The preceding IAM policy allows your account to query the contents of the S3 bucket \(`arn:aws:s3:::my-bucket`\) created for your Neptune target\. It also allows your account to fully operate on the contents of all bucket files and folders \(`arn:aws:s3:::my-bucket/`\)\.

Edit the trust relationship and attach the following IAM role to your service role to allow both AWS DMS and Amazon Neptune database service to assume the role\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "",
      "Effect": "Allow",
      "Principal": {
        "Service": "dms.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    },
    {
      "Sid": "neptune",
      "Effect": "Allow",
      "Principal": {
        "Service": "rds.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

For information about specifying this service role for your Neptune target endpoint, see [Specifying endpoint settings for Amazon Neptune as a target](#CHAP_Target.Neptune.EndpointSettings)\.

## Specifying graph\-mapping rules using Gremlin and R2RML for Amazon Neptune as a target<a name="CHAP_Target.Neptune.GraphMapping"></a>

The graph\-mapping rules that you create specify how data extracted from an SQL relational database source is loaded into a Neptune database cluster target\. The format of these mapping rules differs depending on whether the rules are for loading property\-graph data using Apache TinkerPop Gremlin or Resource Description Framework \(RDF\) data using R2RML\. Following, you can find information about these formats and where to learn more\.

You can specify these mapping rules when you create the migration task using either the console or DMS API\. 

Using the console, specify these mapping rules using **Graph mapping rules** on the **Create database migration task** page\. In **Graph mapping rules**, you can enter and edit the mapping rules directly using the editor provided\. Or you can browse for a file that contains the mapping rules in the appropriate graph\-mapping format\. 

Using the API, specify these options using the `TaskData` request parameter of the `CreateReplicationTask` API call\. Set `TaskData` to the path of a file containing the mapping rules in the appropriate graph\-mapping format\.

### Graph\-mapping rules for generating property\-graph data using Gremlin<a name="CHAP_Target.Neptune.GraphMapping.Gremlin"></a>

Using Gremlin to generate the property\-graph data, specify a JSON object with a mapping rule for each graph entity to be generated from the source data\. The format of this JSON is defined specifically for bulk loading Amazon Neptune\. The following template shows what each rule in this object looks like\.

```
{
    "rules": [
        {
            "rule_id": "(an identifier for this rule)",
            "rule_name": "(a name for this rule)",
            "table_name": "(the name of the table or view being loaded)",
            "vertex_definitions": [
                {
                    "vertex_id_template": "{col1}",
                    "vertex_label": "(the vertex to create)",
                    "vertex_definition_id": "(an identifier for this vertex)",
                    "vertex_properties": [
                        {
                            "property_name": "(name of the property)",
                            "property_value_template": "{col2} or text",
                            "property_value_type": "(data type of the property)"
                        }
                    ]
                }
            ]
        },
        {
            "rule_id": "(an identifier for this rule)",
            "rule_name": "(a name for this rule)",
            "table_name": "(the name of the table or view being loaded)",
            "edge_definitions": [
                {
                    "from_vertex": {
                        "vertex_id_template": "{col1}",
                        "vertex_definition_id": "(an identifier for the vertex referenced above)"
                    },
                    "to_vertex": {
                        "vertex_id_template": "{col3}",
                        "vertex_definition_id": "(an identifier for the vertex referenced above)"
                    },
                    "edge_id_template": {
                        "label": "(the edge label to add)",
                        "template": "{col1}_{col3}"
                    },
                    "edge_properties":[
                        {
                            "property_name": "(the property to add)",
                            "property_value_template": "{col4} or text",
                            "property_value_type": "(data type like String, int, double)"
                        }
                    ]
                }
            ]
        }
    ]
}
```

The presence of a vertex label implies that the vertex is being created here\. Its absence implies that the vertex is created by a different source, and this definition is only adding vertex properties\. Specify as many vertex and edge definitions as required to specify the mappings for your entire relational database source\.

A sample rule for an `employee` table follows\.

```
{
    "rules": [
        {
            "rule_id": "1",
            "rule_name": "vertex_mapping_rule_from_nodes",
            "table_name": "nodes",
            "vertex_definitions": [
                {
                    "vertex_id_template": "{emp_id}",
                    "vertex_label": "employee",
                    "vertex_definition_id": "1",
                    "vertex_properties": [
                        {
                            "property_name": "name",
                            "property_value_template": "{emp_name}",
                            "property_value_type": "String"
                        }
                    ]
                }
            ]
        },
        {
            "rule_id": "2",
            "rule_name": "edge_mapping_rule_from_emp",
            "table_name": "nodes",
            "edge_definitions": [
                {
                    "from_vertex": {
                        "vertex_id_template": "{emp_id}",
                        "vertex_definition_id": "1"
                    },
                    "to_vertex": {
                        "vertex_id_template": "{mgr_id}",
                        "vertex_definition_id": "1"
                    },
                    "edge_id_template": {
                        "label": "reportsTo",
                        "template": "{emp_id}_{mgr_id}"
                    },
                    "edge_properties":[
                        {
                            "property_name": "team",
                            "property_value_template": "{team}",
                            "property_value_type": "String"
                        }
                    ]
                }
            ]
        }
    ]
}
```

Here, the vertex and edge definitions map a reporting relationship from an `employee` node with employee ID \(`EmpID`\) and an `employee` node with a manager ID \(`managerId`\)\.

For more information about creating graph\-mapping rules using Gremlin JSON, see [Gremlin load data format](https://docs.aws.amazon.com/neptune/latest/userguide/bulk-load-tutorial-format-gremlin.html) in the *Amazon Neptune User Guide*\.

### Graph\-mapping rules for generating RDF/SPARQL data<a name="CHAP_Target.Neptune.GraphMapping.R2RML"></a>

If you are loading RDF data to be queried using SPARQL, write the graph\-mapping rules in R2RML\. R2RML is a standard W3C language for mapping relational data to RDF\. In an R2RML file, a *triples map* \(for example, `<#TriplesMap1>` following\) specifies a rule for translating each row of a logical table to zero or more RDF triples\. A *subject map* \(for example, any `rr:subjectMap` following\) specifies a rule for generating the subjects of the RDF triples generated by a triples map\. A *predicate\-object map* \(for example, any `rr:predicateObjectMap` following\) is a function that creates one or more predicate\-object pairs for each logical table row of a logical table\.

A simple example for a `nodes` table follows\.

```
@prefix rr: <http://www.w3.org/ns/r2rml#>.
@prefix ex: <http://example.com/ns#>.

<#TriplesMap1>
    rr:logicalTable [ rr:tableName "nodes" ];
    rr:subjectMap [
        rr:template "http://data.example.com/employee/{id}";
        rr:class ex:Employee;
    ];
    rr:predicateObjectMap [
        rr:predicate ex:name;
        rr:objectMap [ rr:column "label" ];
    ]
```

In the previous example, the mapping defines graph nodes mapped from a table of employees\.

Another simple example for a `Student` table follows\.

```
@prefix rr: <http://www.w3.org/ns/r2rml#>.
@prefix ex: <http://example.com/#>.
@prefix foaf: <http://xmlns.com/foaf/0.1/>.
@prefix xsd: <http://www.w3.org/2001/XMLSchema#>.

<#TriplesMap2>
    rr:logicalTable [ rr:tableName "Student" ];
    rr:subjectMap   [ rr:template "http://example.com/{ID}{Name}";
                      rr:class foaf:Person ];
    rr:predicateObjectMap [
        rr:predicate ex:id ;
        rr:objectMap  [ rr:column "ID";
                        rr:datatype xsd:integer ]
    ];
    rr:predicateObjectMap [
        rr:predicate foaf:name ;
        rr:objectMap  [ rr:column "Name" ]
    ].
```

In the previous example, the mapping defines graph nodes mapping friend\-of\-a\-friend relationships between persons in a `Student` table\.

For more information about creating graph\-mapping rules using SPARQL R2RML, see the W3C specification [R2RML: RDB to RDF mapping language](https://www.w3.org/TR/r2rml/)\.

## Data types for Gremlin and R2RML migration to Amazon Neptune as a target<a name="CHAP_Target.Neptune.DataTypes"></a>

AWS DMS performs data type mapping from your SQL source endpoint to your Neptune target in one of two ways\. Which way you use depends on the graph mapping format that you're using to load the Neptune database: 
+ Apache TinkerPop Gremlin, using a JSON representation of the migration data\.
+ W3C's SPARQL, using an R2RML representation of the migration data\. 

For more information on these two graph mapping formats, see [Specifying graph\-mapping rules using Gremlin and R2RML for Amazon Neptune as a target](#CHAP_Target.Neptune.GraphMapping)\.

Following, you can find descriptions of the data type mappings for each format\.

### SQL source to Gremlin target data type mappings<a name="CHAP_Target.Neptune.DataTypes.Gremlin"></a>

The following table shows the data type mappings from a SQL source to a Gremlin formatted target\. 

AWS DMS maps any unlisted SQL source data type to a Gremlin `String`\.



[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Target.Neptune.html)

For more information on the Gremlin data types for loading Neptune, see [Gremlin data types](https://docs.aws.amazon.com/neptune/latest/userguide/bulk-load-tutorial-format-gremlin.html#bulk-load-tutorial-format-gremlin-datatypes) in the *Neptune User Guide\.*

### SQL source to R2RML \(RDF\) target data type mappings<a name="CHAP_Target.Neptune.DataTypes.R2RML"></a>

The following table shows the data type mappings from a SQL source to an R2RML formatted target\.

All listed RDF data types are case\-sensitive, except RDF literal\. AWS DMS maps any unlisted SQL source data type to an RDF literal\. 

An *RDF literal* is one of a variety of literal lexical forms and data types\. For more information, see [RDF literals](https://www.w3.org/TR/2004/REC-rdf-concepts-20040210/#section-Graph-Literal) in the W3C specification *Resource Description Framework \(RDF\): Concepts and Abstract Syntax*\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Target.Neptune.html)

For more information on the RDF data types for loading Neptune and their mappings to SQL source data types, see [Datatype conversions](https://www.w3.org/TR/r2rml/#datatype-conversions) in the W3C specification *R2RML: RDB to RDF Mapping Language*\.

## Limitations of using Amazon Neptune as a target<a name="CHAP_Target.Neptune.Limitations"></a>

The following limitations apply when using Neptune as a target:
+ AWS DMS currently supports full load tasks only for migration to a Neptune target\. Change data capture \(CDC\) migration to a Neptune target isn't supported\.
+ Make sure that your target Neptune database is manually cleared of all data before starting the migration task, as in the following examples\.

  To drop all data \(vertices and edges\) within the graph, run the following Gremlin command\.

  ```
  gremlin> g.V().drop().iterate()
  ```

  To drop vertices that have the label `'customer'`, run the following Gremlin command\.

  ```
  gremlin> g.V().hasLabel('customer').drop()
  ```
**Note**  
It can take some time to drop a large dataset\. You might want to iterate `drop()` with a limit, for example, `limit(1000)`\.

  To drop edges that have the label `'rated'`, run the following Gremlin command\.

  ```
  gremlin> g.E().hasLabel('rated').drop()
  ```
**Note**  
It can take some time to drop a large dataset\. You might want to iterate `drop()` with a limit, for example `limit(1000)`\.
+ The DMS API operation `DescribeTableStatistics` can return inaccurate results about a given table because of the nature of Neptune graph data structures\.

  During migration, AWS DMS scans each source table and uses graph mapping to convert the source data into a Neptune graph\. The converted data is first stored in the S3 bucket folder specified for the target endpoint\. If the source is scanned and this intermediate S3 data is generated successfully, `DescribeTableStatistics` assumes that the data was successfully loaded into the Neptune target database\. But this isn't always true\. To verify that the data was loaded correctly for a given table, compare `count()` return values at both ends of the migration for that table\. 

  In the following example, AWS DMS has loaded a `customer` table from the source database, which is assigned the label `'customer'` in the target Neptune database graph\. You can make sure that this label is written to the target database\. To do this, compare the number of `customer` rows available from the source database with the number of `'customer'` labeled rows loaded in the Neptune target database after the task completes\.

  To get the number of customer rows available from the source database using SQL, run the following\.

  ```
  select count(*) from customer;
  ```

  To get the number of `'customer'` labeled rows loaded into the target database graph using Gremlin, run the following\.

  ```
  gremlin> g.V().hasLabel('customer').count()
  ```
+ Currently, if any single table fails to load, the whole task fails\. Unlike in a relational database target, data in Neptune is highly connected, which makes it impossible in many cases to resume a task\. If a task can't be resumed successfully because of this type of data load failure, create a new task to load the table that failed to load\. Before running this new task, manually clear the partially loaded table from the Neptune target\.
**Note**  
You can resume a task that fails migration to a Neptune target if the failure is recoverable \(for example, a network transit error\)\.
+ AWS DMS supports most standards for R2RML\. However, AWS DMS doesn't support certain R2RML standards, including inverse expressions, joins, and views\. A work\-around for an R2RML view is to create a corresponding custom SQL view in the source database\. In the migration task, use table mapping to choose the view as input\. Then map the view to a table that is then consumed by R2RML to generate graph data\.
+ When you migrate source data with unsupported SQL data types, the resulting target data can have a loss of precision\. For more information, see [Data types for Gremlin and R2RML migration to Amazon Neptune as a target](#CHAP_Target.Neptune.DataTypes)\.
+ AWS DMS doesn't support migrating LOB data into a Neptune target\.