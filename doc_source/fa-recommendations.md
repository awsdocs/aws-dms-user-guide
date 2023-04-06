# Using the AWS DMS Fleet Advisor Target Recommendations feature<a name="fa-recommendations"></a>

To explore and choose an optimal migration target, you can generate target recommendations for your source on\-premises databases in DMS Fleet Advisor\. A *recommendation* includes one or more possible AWS target engines that you can choose for the migration of your source on\-premises database\. From these possible target engines, DMS Fleet Advisor suggests a single target engine as the right\-sized migration destination\. To determine this right\-sized migration destination, DMS Fleet Advisor uses the inventory metadata and metrics collected by your data collector\.

You can use recommendations before the start of a migration to discover migration options, save costs, and reduce risks\. You can export recommendations as a comma separated values \(CSV\) file, and share it with key stakeholders to facilitate decision making\. Then, you can use the AWS Pricing Calculator to further optimize maintenance costs\. For more information, see [https://calculator\.aws/\#/](https://calculator.aws/#/)\.

You can't modify target recommendations in DMS Fleet Advisor\. Thus, you can't use DMS Fleet Advisor for what\-if analysis\. *What\-if analysis* is the process of changing the target parameters to see how those changes affect the pricing estimate of your recommendation\. You can run what\-if analysis in the AWS Pricing Calculator using the recommended target parameters as the starting point in the AWS Pricing Calculator\. For more information, see [https://calculator\.aws/\#/](https://calculator.aws/#/)\.

We recommend that you consider the DMS Fleet Advisor recommendation is a starting point in your migration planning\. You can then decide to change the recommended instance parameters to optimize the cost or performance of your database workloads\.

**Topics**
+ [Recommended target instances](#fa-recommendations-instances)
+ [How does DMS Fleet Advisor determine target instance specifications for the recommendation?](#fa-recommendations-how-it-works)
+ [Generating target recommendations with AWS DMS Fleet Advisor](fa-recommendations-generate.md)
+ [Exploring details of target recommendations with AWS DMS Fleet Advisor](fa-recommendations-view.md)
+ [Exporting target recommendations with AWS DMS Fleet Advisor](fa-recommendations-export.md)
+ [Troubleshooting for target recommendations](fa-recommendations-troubleshooting.md)
+ [DMS Fleet Advisor Target Recommendation limitations](fa-welcome-limitations.md)

## Recommended target instances<a name="fa-recommendations-instances"></a>

For target recommendations, DMS Fleet Advisor considers the following general purpose, memory\-optimized, and burstable performance Amazon RDS DB instances\.
+ db\.m5
+ db\.m6i
+ db\.r5
+ db\.r5\.Nlarge
+ db\.r6i
+ db\.t3
+ db\.x1
+ db\.x1e
+ db\.z1d

For more information about Amazon RDS DB instance classes, see [DB instance classes](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Concepts.DBInstanceClass.html) in the *Amazon RDS User Guide*\.

## How does DMS Fleet Advisor determine target instance specifications for the recommendation?<a name="fa-recommendations-how-it-works"></a>

DMS Fleet Advisor can generate recommendations based on either database capacity or utilization\.
+ If you choose to generate the recommendation based on database capacity, then DMS Fleet Advisor maps the existing database capacity to the specifications of the closest instance class\.
+ If you choose to generate the recommendation based on resource utilization, then DMS Fleet Advisor determines the 95th percentile value for such metrics as CPU, memory, IO throughput, and IOPS\. 95th percentile means that 95 percent of the collected data is lower than this value\. Then, DMS Fleet Advisor maps these values to the specifications of the closest instance class\.

To determine the size of the target database, DMS Fleet Advisor collects information about the size of your source database\. Then, DMS Fleet Advisor recommends using the same size for the target storage\. If your source database storage is overprovisioned, then the recommended size of the target storage will also be overprovisioned\.

If you want to migrate data using AWS DMS, then you might need to increase IOPS provisioning for your target DB instance\. When DMS Fleet Advisor generates target recommendations, the service considers only your source database metrics\. DMS Fleet Advisor doesn't consider additional IOPS that you might need to run data migration tasks\. For more information, see [Migration tasks run slowly](CHAP_Troubleshooting.md#CHAP_Troubleshooting.General.SlowTask)\.

To estimate the IOPS costs, DMS Fleet Advisor uses a one\-to\-one mapping of your source IOPS usage as a baseline\. DMS Fleet Advisor considers the peak load as the baseline value and 100% utilization for IOPS pricing\.

For PostgreSQL and MySQL source databases, DMS Fleet Advisor can include Aurora and Amazon RDS DB instances in the target recommendations\. If an Aurora configuration maps to the source requirements, then DMS Fleet Advisor marks this option as recommended\.