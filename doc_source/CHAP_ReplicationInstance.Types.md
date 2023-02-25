# Choosing the right AWS DMS replication instance for your migration<a name="CHAP_ReplicationInstance.Types"></a>

AWS DMS creates the replication instance on an Amazon EC2 instance\. AWS DMS currently supports the T2, T3, C4, C5, C6i, R4, R5 and R6i Amazon EC2 instance classes for replication instances:
+ T2 instances are burstable performance instances that provide a baseline level of CPU performance with the ability to burst above the baseline\. The baseline performance and ability to burst are governed by CPU credits\. T2 instances receive CPU credits continuously at a set rate depending on the instance size\. They accumulate CPU credits when they are idle and consume CPU credits when they are active\. 

  T2 instances are a good choice for a variety of general\-purpose workloads\. These include microservices, low\-latency interactive applications, small and medium databases, virtual desktops, development, build and stage environments, code repositories, and product prototypes\.
+ T3 instances are the next\-generation burstable general\-purpose instance type\. This type provides a baseline level of CPU performance with the ability to burst CPU usage at any time for as long as required\. T3 instances offer a balance of compute, memory, and network resources and are designed for applications with moderate CPU usage that experience temporary spikes in use\. T3 instances accumulate CPU credits when a workload is operating below baseline threshold\. Each earned CPU credit provides the T3 instance the opportunity to burst with the performance of a full CPU core for one minute when needed\. 

  T3 instances can burst at any time for as long as required in `unlimited` mode\. For more information on `unlimited` mode, see [Working with unlimited mode for burstable performance instances](#CHAP_ReplicationInstance.Types.UnlimitedMode)\.
+ C4 instances are optimized for compute\-intensive workloads and deliver very cost\-effective high performance at a low price per compute ratio\. They achieve significantly higher packet per second \(PPS\) performance, lower network jitter, and lower network latency\. AWS DMS can also be CPU\-intensive, especially when performing heterogeneous migrations and replications such as migrating from Oracle to PostgreSQL\. C4 instances can be a good choice for these situations\.
+ C5 instances are the next\-generation instance type to deliver cost\-effective high performance at a low price per compute ratio for running advanced compute\-intensive workloads\. This includes workloads such as high\-performance web servers, high\-performance computing \(HPC\), batch processing, ad serving, highly scalable multiplayer gaming, and video encoding\. Other workloads C5 instances are suited to include scientific modeling, distributed analytics, and machine and deep learning inference\. The C5 instances are available with a choice of processors from Intel and AMD\.
+ C6i instances offer up to 15% better compute price performance over comparable Gen5 instances for a wide variety of workloads, and always\-on memory encryption\. C6i instances are an ideal fit for compute\-intensive workloads such as batch processing, distributed analytics, high performance computing \(HPC\), ad serving, highly scalable multiplayer gaming, and video encoding\.
+ R4 instances are memory optimized for memory\-intensive workloads\. Ongoing migrations or replications of high\-throughput transaction systems using AWS DMS can also consume large amounts of CPU and memory\. R4 instances include more memory per vCPU than earlier generation instance types\.
+ R5 instances are the next generation of memory\-optimized instance types for Amazon EC2\. R5 instances are well\-suited for memory\-intensive applications such as high performance databases, distributed web scale in\-memory caches, midsize in\-memory databases, real time big data analytics, and other enterprise applications\. Ongoing migrations or replications of high\-throughput transaction systems using AWS DMS can also consume large amounts of CPU and memory\.
+ R6i instances offer up to 15% better compute price performance over comparable Gen5 instances for a wide variety of workloads, and always\-on memory encryption\. R6i instances are SAP Certified and are ideal for workloads such as SQL and noSQL databases, distributed web scale in\-memory caches like Memcached and Redis, in\-memory databases like SAP HANA, and real time big data analytics like Hadoop and Spark clusters\.

Each replication instance has a specific configuration of memory and vCPU\. The following table shows the configuration for each replication instance type\. For pricing information, see the [AWS Database Migration Service service pricing page](https://aws.amazon.com/dms/pricing/)\.


|  Replication instance type  |  vCPU  |  Memory \(GiB\)  | 
| --- | --- | --- | 
|  General Purpose  | 
|  dms\.t2\.micro  |  1  |  1  | 
|  dms\.t2\.small  |  1  |  2  | 
|  dms\.t2\.medium  |  2  |  4  | 
|  dms\.t2\.large  |  2  |  8  | 
|  dms\.t3\.micro  |  2  |  1  | 
|  dms\.t3\.small  |  2  |  2  | 
|  dms\.t3\.medium  |  2  |  4  | 
|  dms\.t3\.large  |  2  |  8  | 
|  Compute Optimized  | 
|  dms\.c4\.large  |  2  |  3\.75  | 
|  dms\.c4\.xlarge  |  4  |  7\.5  | 
|  dms\.c4\.2xlarge  |  8  |  15  | 
|  dms\.c4\.4xlarge  |  16  |  30  | 
|  dms\.c5\.large  |  2  |  4  | 
|  dms\.c5\.xlarge  |  4  |  8  | 
|  dms\.c5\.2xlarge  |  8  |  16  | 
|  dms\.c5\.4xlarge  |  16  |  32  | 
|  dms\.c5\.9xlarge  |  36  | 72 | 
|  dms\.c5\.12xlarge  |  48  | 96 | 
|  dms\.c5\.18xlarge  |  72  | 144 | 
|  dms\.c5\.24xlarge  |  96  | 192 | 
|  dms\.c6i\.large  |  2  |  4  | 
|  dms\.c6i\.xlarge  |  4  |  8  | 
|  dms\.c6i\.2xlarge  |  8  |  16  | 
|  dms\.c6i\.4xlarge  |  16  |  32  | 
|  dms\.c6i\.8xlarge  |  32  | 64 | 
|  dms\.c6i\.12xlarge  |  48  | 96 | 
|  dms\.c6i\.16xlarge  |  64  | 128 | 
|  dms\.c6i\.24xlarge  |  96  | 192 | 
|  dms\.c6i\.32xlarge  |  128  | 256 | 
|  Memory Optimized  | 
|  dms\.r4\.large  |  2  |  15\.25  | 
|  dms\.r4\.xlarge  |  4  |  30\.5  | 
|  dms\.r4\.2xlarge  |  8  |  61  | 
|  dms\.r4\.4xlarge  |  16  |  122  | 
|  dms\.r4\.8xlarge  |  32  |  244  | 
|  dms\.r5\.large  |  2  |  16  | 
|  dms\.r5\.xlarge  |  4  |  32  | 
|  dms\.r5\.2xlarge  |  8  |  64  | 
|  dms\.r5\.4xlarge  |  16  |  128  | 
|  dms\.r5\.8xlarge  |  32  |  256  | 
|  dms\.r5\.12xlarge  |  48  |  384  | 
|  dms\.r5\.16xlarge  |  64  |  512  | 
|  dms\.r5\.24xlarge  |  96  |  768  | 
|  dms\.r6i\.large  |  2  |  16  | 
|  dms\.r6i\.xlarge  |  4  |  32  | 
|  dms\.r6i\.2xlarge  |  8  |  64  | 
|  dms\.r6i\.4xlarge  |  16  |  128  | 
|  dms\.r6i\.8xlarge  |  32  |  256  | 
|  dms\.r6i\.12xlarge  |  48  |  384  | 
|  dms\.r6i\.16xlarge  |  64  |  512  | 
|  dms\.r6i\.24xlarge  |  96  |  768  | 
|  dms\.r6i\.32xlarge  |  128  |  1024  | 

**Topics**
+ [Deciding what instance class to use](#CHAP_ReplicationInstance.Types.Deciding)
+ [Working with unlimited mode for burstable performance instances](#CHAP_ReplicationInstance.Types.UnlimitedMode)

## Deciding what instance class to use<a name="CHAP_ReplicationInstance.Types.Deciding"></a>

To help determine which replication instance class might work best for you, let's look at the change data capture \(CDC\) process that AWS DMS uses\.

Let's assume that you're running a full load plus CDC task \(bulk load plus ongoing replication\)\. In this case, the task has its own SQLite repository to store metadata and other information\. Before AWS DMS starts a full load, these steps occur: 
+ AWS DMS starts capturing changes for the tables it's migrating from the source engine's transaction log \(we call these *cached changes*\)\. After full load is done, these cached changes are collected and applied on the target\. Depending on the volume of cached changes, these changes can directly be applied from memory, where they are collected first, up to a set threshold\. Or they can be applied from disk, where changes are written when they can't be held in memory\. 
+ After cached changes are applied, by default AWS DMS starts a transactional apply process on the target instance\. 

During the applied cached changes phase and ongoing replications phase, AWS DMS uses two stream buffers, one each for incoming and outgoing data\. AWS DMS also uses an important component called a *sorter,* which is another memory buffer\. Following are two important uses of the sorter component \(which has others\): 
+ It tracks all transactions and makes sure that it forwards only relevant transactions to the outgoing buffer\. 
+ It makes sure that transactions are forwarded in the same commit order as on the source\. 

As you can see, we have three important memory buffers in this architecture for CDC in AWS DMS\. If any of these buffers experience memory pressure, the migration can have performance issues that can potentially cause failures\.

When you plug heavy workloads with a high number of transactions per second \(TPS\) into this architecture, you can find the extra memory provided by R5 and R6i instances useful\. You can use R5 and R6i instances to hold a large number of transactions in memory and prevent memory\-pressure issues during ongoing replications\.

## Working with unlimited mode for burstable performance instances<a name="CHAP_ReplicationInstance.Types.UnlimitedMode"></a>

A burstable performance instance configured as `unlimited`, such as a T3 instance, can sustain high CPU utilization for any period of time whenever required\. The hourly instance price can automatically cover all CPU usage spikes\. It does so if the average CPU utilization of the instance is at or below the baseline over a rolling 24\-hour period or the instance lifetime, whichever is shorter\.

For the vast majority of general\-purpose workloads, instances configured as `unlimited` give enough performance without any additional charges\. If the instance runs at higher CPU utilization for a prolonged period, it can do so for a flat additional rate per vCPU\-hour\. For information about T3 instance pricing, see "T3 CPU Credits" in [AWS Database Migration Service](http://aws.amazon.com/dms/pricing/)\.

For more information on `unlimited` mode for T3 instances, see [Unlimited mode for burstable performance instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/burstable-performance-instances-unlimited-mode.html) in the *Amazon EC2 User Guide for Linux Instances*\.

**Important**  
If you use a `dms.t3.micro` instance under the [AWS Free Tier](http://aws.amazon.com/free/) offer and use it in `unlimited` mode, charges might apply\. In particular, charges might apply if your average utilization over a rolling 24\-hour period exceeds the baseline utilization of the instance\. For more information, see [Baseline utilization](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/burstable-credits-baseline-concepts.html#baseline_performance) in the *Amazon EC2 User Guide for Linux Instances*\.  
T3 instances launch as `unlimited` by default\. If the average CPU usage over a 24\-hour period exceeds the baseline, you incur charges for surplus credits\. In some cases, you might launch T3 Spot Instances as `unlimited` and plan to use them immediately and for a short duration\. If you do so with no idle time for accruing CPU credits, you incur charges for surplus credits\. We recommend that you launch your T3 Spot Instances in standard mode to avoid paying higher costs\. For more information, see [Surplus credits can incur charges](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/burstable-performance-instances-unlimited-mode-concepts.html#unlimited-mode-surplus-credits), [T3 Spot Instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-spot-limits.html#t3-spot-instances), and [Standard mode for burstable performance instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/burstable-performance-instances-standard-mode.html) in the *Amazon EC2 User Guide for Linux Instances*\.