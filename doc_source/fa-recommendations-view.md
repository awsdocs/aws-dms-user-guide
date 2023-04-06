# Exploring details of target recommendations with AWS DMS Fleet Advisor<a name="fa-recommendations-view"></a>

After DMS Fleet Advisor generates target recommendations, you can view the key parameters of the recommended migration target in the **Recommendations** table\. These key parameters include the target engine, instance class, number of virtual CPUs, memory, storage, and storage type\. In addition to these parameters, DMS Fleet Advisor displays the estimated monthly cost of this recommended migration target\.

Each recommendation might include one or more possible AWS target engines\. If your recommendation includes several target engines, then AWS DMS marks one of them as recommended\. Also, AWS DMS displays the parameters and estimated monthly cost for this recommended option in the **Recommendations** table\.

To compare target recommendations to the utilization and capacity of your source database, explore your recommendations in detail\. Also, you can view the migration limitations for a selected recommendation\. These limitations include unsupported database features, action items, and other migration considerations\.

**To explore the recommendation in detail**

1. Generate target recommendations with DMS Fleet Advisor\. For more information, see [Generating target recommendations](fa-recommendations-generate.md)\.

1. Choose the recommendation name from the **Recommendations** table\. The recommendation page opens\.

1. If your recommendation includes more than one target options, then for **Target recommendations**, choose the target option\.

1. Expand the **Source utilization and capacity** section\. DMS Fleet Advisor displays resource utilization charts for the following metrics\.
   + Number of CPUs
   + Memory
   + I/O throughput
   + Input/output operations per second \(IOPS\)
   + Storage

   Use these charts to compare your source database metrics from your DMS data collector to the metrics of the selected target engine\.

   If you can't see charts after you expand the **Source utilization and capacity** section, make sure that you granted your IAM user with permissions to view Amazon CloudWatch dashboards\. For more information, see [Using Amazon CloudWatch dashboards](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch_Dashboards.html) in the *Amazon CloudWatch User Guide*\.

1. Choose the link with the name of your selected target engine\. The **Target detail** page opens\.

1. In the **Configuration** section, compare values of your source database parameters to the parameters of the target engine\. For the target engine, DMS Fleet Advisor displays the estimated monthly costs for your cloud resources\. DMS Fleet Advisor uses the AWS Price List Query API to provide pricing details for your information only\. Your actual fees depend on a variety of factors, including your actual usage of AWS services\. For more information about AWS service pricing, see [https://aws.amazon.com/pricing/](https://aws.amazon.com/pricing/)Cloud Services Pricing\.

1. In the **Migration limitations** section, view the migration limitations\. We recommend that you consider these limitations when you migrate your source database to the AWS Cloud\.