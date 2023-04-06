# Generating target recommendations with AWS DMS Fleet Advisor<a name="fa-recommendations-generate"></a>

After you complete data collection and inventory of your database and analytics fleet, you can generate target recommendations in DMS Fleet Advisor\. To do so, choose source databases and configure the settings that the DMS Fleet Advisor Target Recommendations feature uses to determine the size of target instances\. Also, the DMS Fleet Advisor Target Recommendations feature uses the capacity and utilization metrics collected from your source databases\.

**To generate target recommendations**

1. Sign in to the AWS Management Console and open the AWS DMS console at [https://console\.aws\.amazon\.com/dms/v2/](https://console.aws.amazon.com/https://console.aws.amazon.com/dms/v2/)\.

   Make sure that you choose the AWS Region where you use the DMS Fleet Advisor\.

1. In the navigation pane, choose **Recommendations** under **Assess**, and then choose **Generate recommendations**\.

1. In the **Select source databases** panel, select the check boxes for the names of databases that you want to migrate to the AWS Cloud\.

   For **Search source databases**, enter the name of your database to filter your inventory\.

   DMS Fleet Advisor can generate recommendations for up to 100 databases at one time\.

1. For **Availability and durability**, choose the preferred deployment option\.

   To calculate target recommendations for your production databases, choose **Production \(Multi\-AZ\)**\. DMS Fleet Advisor includes two DB instances in different Availability Zones in your target recommendation\. This Multi\-AZ deployment option provides high availability, data redundancy, and failover support\.

   To calculate target recommendations for databases that you use for development or testing, choose **Dev/Test \(Single\-AZ\)**\. DMS Fleet Advisor includes a single DB instance in your target recommendation\. This Single\-AZ deployment option reduces maintenance costs\.

1. For **Target instance sizing**, choose the preferred option which DMS Fleet Advisor uses to calculate target recommendations\.

   To calculate target recommendations based on your source database or OS server configuration, choose **Total capacity**\. DMS Fleet Advisor uses such metrics as total CPU, memory, and disk capacity of your source databases or OS servers to generate target recommendations\. Then, DMS Fleet Advisor maps your database capacity metrics to the specifications of the closest Amazon RDS DB instance class\.

   To calculate target recommendations based on the actual utilization of your source database or OS server, choose **Resource utilization**\. DMS Fleet Advisor uses utilization metrics of the CPU, memory, and disk capacity of your source databases or OS servers to generate target recommendations\. From the utilization metrics, DMS Fleet Advisor computes the 95th percentile for each metric\. 95th percentile means that 95 percent of the data within the period is lower than this value\. Then, DMS Fleet Advisor maps these values to the closest Amazon RDS DB instance class\.

   We recommend that you use the **Resource utilization** option for more accurate recommendations\. To do so, make sure that you have collected the total capacity and resource utilization metrics\.

1. Choose **Generate**\.

DMS Fleet Advisor generates target recommendations for the selected databases\. For successfully generated recommendations, DMS Fleet Advisor sets the status to **Computed**\. Also, DMS Fleet Advisor uses the AWS Pricing Calculator to determine the estimated monthly cost for the recommended target DB instance\. Now, you can explore the generated recommendations in detail\. For more information, see [Recommendation details](fa-recommendations-view.md)\.

To estimate the total monthly cost for your data inventory, select the check boxes for databases that you plan to move to the cloud\. DMS Fleet Advisor displays the total estimated monthly cost and the summary of your target databases in the AWS Cloud\. DMS Fleet Advisor uses the AWS Price List Query API to provide pricing details for your information only\. Your actual fees depend on a variety of factors, including your actual usage of AWS services\. For more information about AWS service pricing, see [Cloud Services Pricing](https://aws.amazon.com/pricing/)\.