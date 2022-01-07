# Using data extraction agents<a name="agents"></a>

In some migration scenarios, the source and target databases are very different from one another and require additional data transformation\. AWS Schema Conversion Tool \(AWS SCT\) is extensible, so that you can address these scenarios using an agent\. An *agent is *an external program that's integrated with AWS SCT, but performs data transformation elsewhere \(such as on an Amazon EC2 instance\)\. In addition, an AWS SCT agent can interact with other AWS services on your behalf, such as creating and managing AWS Database Migration Service tasks for you\.

**Topics**
+ [Migrating data from an on\-premises data warehouse to Amazon Redshift](agents.dw.md)
+ [Migrating data from Apache Cassandra to Amazon DynamoDB](agents.cassandra.md)