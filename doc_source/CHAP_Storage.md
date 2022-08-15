# Amazon RDS DB instance storage<a name="CHAP_Storage"></a>

DB instances for Amazon RDS for MySQL, MariaDB, PostgreSQL, Oracle, and Microsoft SQL Server use Amazon Elastic Block Store \(Amazon EBS\) volumes for database and log storage\. Depending on the amount of storage requested, Amazon RDS automatically stripes across multiple Amazon EBS volumes to enhance performance\. 

For more information about instance storage pricing, see [Amazon RDS pricing](https://aws.amazon.com/rds/pricing/)\.

## Amazon RDS storage types<a name="Concepts.Storage"></a>

Amazon RDS provides three storage types: General Purpose SSD \(also known as gp2\), Provisioned IOPS SSD \(also known as io1\), and magnetic \(also known as standard\)\. They differ in performance characteristics and price, which means that you can tailor your storage performance and cost to the needs of your database workload\. You can create MySQL, MariaDB, Oracle, and PostgreSQL RDS DB instances with up to 64 tebibytes \(TiB\) of storage\. You can create SQL Server RDS DB instances with up to 16 TiB of storage\. For this amount of storage, use the Provisioned IOPS SSD and General Purpose SSD storage types\.

The following list briefly describes the three storage types: 
+ **General Purpose SSD** – General Purpose SSD volumes offer cost\-effective storage that is ideal for a broad range of workloads\. These volumes deliver single\-digit millisecond latencies and the ability to burst to 3,000 IOPS for extended periods of time\. Baseline performance for these volumes is determined by the volume's size\.

  For more information about General Purpose SSD storage, including the storage size ranges, see [General Purpose SSD storage](#Concepts.Storage.GeneralSSD)\.
+ **Provisioned IOPS** – Provisioned IOPS storage is designed to meet the needs of I/O\-intensive workloads, particularly database workloads, that require low I/O latency and consistent I/O throughput\.

  For more information about provisioned IOPS storage, including the storage size ranges, see [Provisioned IOPS SSD storage](#USER_PIOPS)\.
+ **Magnetic** – Amazon RDS also supports magnetic storage for backward compatibility\. We recommend that you use General Purpose SSD or Provisioned IOPS for any new storage needs\. The maximum amount of storage allowed for DB instances on magnetic storage is less than that of the other storage types\. For more information, see [Magnetic storage](#CHAP_Storage.Magnetic)\.

Several factors can affect the performance of Amazon EBS volumes, such as instance configuration, I/O characteristics, and workload demand\. For more information about getting the most out of your Provisioned IOPS volumes, see [Amazon EBS volume performance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSPerformance.html)\. 

## General Purpose SSD storage<a name="Concepts.Storage.GeneralSSD"></a>

General Purpose SSD storage offers cost\-effective storage that is acceptable for most database workloads\. The following are the storage size ranges for General Purpose SSD DB instances:
+ MariaDB, MySQL, Oracle, and PostgreSQL database instances: 20 GiB–64 TiB
+ SQL Server Enterprise, Standard, Web, and Express Editions: 20 GiB–16 TiB

Baseline I/O performance for General Purpose SSD storage is 3 IOPS for each GiB, with a minimum of 100 IOPS\. This relationship means that larger volumes have better performance\. For example, baseline performance for a 100\-GiB volume is 300 IOPS\. Baseline performance for a 1\-TiB volume is 3,000 IOPS\. Maximum baseline performance for a gp2 volume \(5\.34 TiB and greater\) is 16,000 IOPS\.

Volumes below 1 TiB in size also have ability to burst to 3,000 IOPS for extended periods of time\. Instance I/O credit balance determines burst performance\. For more information about instance I/O credits, see [I/O credits and burst performance](#CHAP_Storage.IO.Credits)\.

Many workloads never deplete the burst balance, making General Purpose SSD an ideal storage choice for many workloads\. However, some workloads can exhaust the 3,000 IOPS burst storage credit balance, so you should plan your storage capacity to meet the needs of your workloads\.

For gp2 volumes larger than 1 TiB, the baseline performance is greater than the burst performance\. For such volumes, burst is often irrelevant because the baseline performance is better than the 3,000 IOPS burst performance\. However, for DB instances larger than 1 TiB where the storage is *striped* across four Amazon EBS volumes, burst performance of up to 12,000 IOPS can be seen\. This applies to RDS database engines other than Microsoft SQL Server, which doesn't support volume striping\.

**Note**  
DB instances that use General Purpose SSD storage can experience much longer latency after read replica creation, Multi\-AZ conversion, and DB snapshot restoration than instances that use Provisioned IOPS storage\. If you need a DB instance with minimum latency after these operations, we recommend using Provisioned IOPS storage\.

### I/O credits and burst performance<a name="CHAP_Storage.IO.Credits"></a>

General Purpose SSD storage performance is governed by volume size, which dictates the base performance level of the volume and how quickly it accumulates I/O credits\. Larger volumes have higher base performance levels and accumulate I/O credits faster\. *I/O credits* represent the available bandwidth that your General Purpose SSD storage can use to burst large amounts of I/O when more than the base level of performance is needed\. The more I/O credits your storage has for I/O, the more time it can burst beyond its base performance level and the better it performs when your workload requires more performance\.

When using General Purpose SSD storage, your DB instance receives an initial I/O credit balance of 5\.4 million I/O credits\. This initial credit balance is enough to sustain a burst performance of 3,000 IOPS for 30 minutes\. This balance is designed to provide a fast initial boot cycle for boot volumes and to provide a good bootstrapping experience for other applications\. Volumes earn I/O credits at the baseline performance rate of 3 IOPS for each GiB of volume size\. For example, a 100\-GiB SSD volume has a baseline performance of 300 IOPS\. 

When your storage requires more than the base performance I/O level, it uses I/O credits in the I/O credit balance to burst to the required performance level\. Such a burst goes to a maximum of 3,000 IOPS\. Storage larger than 1,000 GiB has a base performance that is equal or greater than the maximum burst performance\. When your storage uses fewer I/O credits than it earns in a second, unused I/O credits are added to the I/O credit balance\. The maximum I/O credit balance for a DB instance using General Purpose SSD storage is equal to the initial I/O credit balance \(5\.4 million I/O credits\)\.

Suppose that your storage uses all of its I/O credit balance\. If so, its maximum performance remains at the base performance level until I/O demand drops below the base level and unused I/O credits are added to the I/O credit balance\. \(The *base performance level* is the rate at which your storage earns I/O credits\.\) The more storage, the greater the base performance is and the faster it replenishes the I/O credit balance\. 

**Note**  
Storage conversions between magnetic storage and General Purpose SSD storage can potentially deplete your I/O credit balance, resulting in longer conversion times\. For more information about scaling storage, see [Working with storage for Amazon RDS DB instances](USER_PIOPS.StorageTypes.md)\.

The burst duration of your storage depends on the size of the storage, the burst IOPS required, and the I/O credit balance when the burst begins\. This relationship is shown in the equation following\.

```
                              (Credit balance)
   Burst duration =  --------------------------------------
                     (Burst IOPS) - 3*(Storage size in GiB)
```

You might notice that your storage performance is frequently limited to the base level due to an empty I/O credit balance\. If so, consider allocating more General Purpose SSD storage with a higher base performance level\. Alternatively, you can switch to Provisioned IOPS storage for workloads that require sustained IOPS performance\.

For workloads with steady state I/O requirements, provisioning less than 100 GiB of General Purpose SSD storage might result in higher latencies if you exhaust your I/O credit balance\.

**Note**  
In general, most workloads never exceed the I/O credit balance\.

For a more detailed description of how baseline performance and I/O credit balance affect performance see [Understanding burst vs\. baseline performance with Amazon RDS and GP2](http://aws.amazon.com/blogs/database/understanding-burst-vs-baseline-performance-with-amazon-rds-and-gp2/)\. 

## Provisioned IOPS SSD storage<a name="USER_PIOPS"></a>

For a production application that requires fast and consistent I/O performance, we recommend Provisioned IOPS \(input/output operations per second\) storage\. Provisioned IOPS storage is a storage type that delivers predictable performance, and consistently low latency\. Provisioned IOPS storage is optimized for online transaction processing \(OLTP\) workloads that have consistent performance requirements\. Provisioned IOPS helps performance tuning of these workloads\. 

**Note**  
Your database workload might not be able to achieve 100 percent of the IOPS that you have provisioned\. For more information, see [Factors that affect storage performance](#CHAP_Storage.Other.Factors)\.

When you create a DB instance, you specify the IOPS rate and the size of the volume\. Amazon RDS provides that IOPS rate for the DB instance until you change it\.
+ The ratio of IOPS to allocated storage \(in GiB\) must be from 1–50 on RDS for SQL Server, and 0\.5–50 on other RDS DB engines\.
+ If you're using storage autoscaling, the same ratios between IOPS and maximum storage threshold \(in GiB\) also apply\.

  For more information on storage autoscaling, see [Managing capacity automatically with Amazon RDS storage autoscaling](USER_PIOPS.StorageTypes.md#USER_PIOPS.Autoscaling)\.

The following table shows the range of Provisioned IOPS and storage size range for each database engine\.

<a name="rds-provisioned-iops-storage-range-reference"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Storage.html)

**Note**  
 For SQL Server, the maximum 64,000 IOPS is guaranteed only on [Nitro\-based instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-types.html#ec2-nitro-instances) that are on the m5, m5d, r5, r5b, r5d, and z1d instance types\. Other instance families guarantee performance up to 32,000 IOPS\.  
For Oracle, you can provision the maximum 256,000 IOPS only on the r5b instance type\.  
For PostgreSQL, the maximum IOPS on the db\.m5\.8xlarge, db\.m5\.16xlarge, db\.r5\.8xlarge, and db\.r5\.16xlarge instance classes is 40,000\.

**Important**  
Depending on the instance class you're using, you might see lower IOPS performance than the maximum that RDS allows you to provision\. For specific information on IOPS performance for DB instance classes, see [Amazon EBS\-optimized instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-optimized.html)\. We recommend that you determine the maximum IOPS for the instance class before setting a Provisioned IOPS value for your DB instance\.

### Combining Provisioned IOPS storage with Multi\-AZ deployments or read replicas<a name="Overview.ProvisionedIOPS-support"></a>

For production OLTP use cases, we recommend that you use Multi\-AZ deployments for enhanced fault tolerance with Provisioned IOPS storage for fast and predictable performance\.

You can also use Provisioned IOPS SSD storage with read replicas for MySQL, MariaDB or PostgreSQL\. The type of storage for a read replica is independent of that on the primary DB instance\. For example, you might use General Purpose SSD for read replicas with a primary DB instance that uses Provisioned IOPS SSD storage to reduce costs\. However, your read replica's performance in this case might differ from that of a configuration where both the primary DB instance and the read replicas use Provisioned IOPS SSD storage\.

### Provisioned IOPS storage costs<a name="Overview.ProvisionedIOPS-cost"></a>

With Provisioned IOPS storage, you are charged for the provisioned resources whether or not you use them in a given month\. 

For more information about pricing, see [Amazon RDS pricing](https://aws.amazon.com/rds/pricing/)\. 

### Getting the best performance from Amazon RDS Provisioned IOPS SSD storage<a name="Overview.ProvisionedIOPS.gettingthemostoutofpiops"></a>

If your workload is I/O constrained, using Provisioned IOPS SSD storage can increase the number of I/O requests that the system can process concurrently\. Increased concurrency allows for decreased latency because I/O requests spend less time in a queue\. Decreased latency allows for faster database commits, which improves response time and allows for higher database throughput\. 

Provisioned IOPS SSD storage provides a way to reserve I/O capacity by specifying IOPS\. However, as with any other system capacity attribute, its maximum throughput under load is constrained by the resource that is consumed first\. That resource might be network bandwidth, CPU, memory, or database internal resources\. 

## Magnetic storage<a name="CHAP_Storage.Magnetic"></a>

Amazon RDS also supports magnetic storage for backward compatibility\. We recommend that you use General Purpose SSD or Provisioned IOPS SSD for any new storage needs\. The following are some limitations for magnetic storage: 
+ Doesn't allow you to scale storage when using the SQL Server database engine\.
+ Doesn't support storage autoscaling\.
+ Doesn't support elastic volumes\.
+ Limited to a maximum size of 3 TiB\.
+ Limited to a maximum of 1,000 IOPS\.

## Monitoring storage performance<a name="Concepts.Storage.Metrics"></a>

Amazon RDS provides several metrics that you can use to determine how your DB instance is performing\. You can view the metrics on the summary page for your instance in Amazon RDS Management Console\. You can also use Amazon CloudWatch to monitor these metrics\. For more information, see [Viewing metrics in the Amazon RDS console](USER_Monitoring.md)\. Enhanced Monitoring provides more detailed I/O metrics; for more information, see [Monitoring OS metrics with Enhanced Monitoring](USER_Monitoring.OS.md)\.

The following metrics are useful for monitoring storage for your DB instance: 
+  **IOPS** – The number of I/O operations completed each second\. This metric is reported as the average IOPS for a given time interval\. Amazon RDS reports read and write IOPS separately on 1\-minute intervals\. Total IOPS is the sum of the read and write IOPS\. Typical values for IOPS range from zero to tens of thousands per second\.
+  **Latency** – The elapsed time between the submission of an I/O request and its completion\. This metric is reported as the average latency for a given time interval\. Amazon RDS reports read and write latency separately at 1\-minute intervals\. Typical values for latency are in milliseconds \(ms\)\.
+  **Throughput** – The number of bytes each second that are transferred to or from disk\. This metric is reported as the average throughput for a given time interval\. Amazon RDS reports read and write throughput separately on 1\-minute intervals using units of megabytes per second \(MB/s\)\. Typical values for throughput range from zero to the I/O channel's maximum bandwidth\.
+  **Queue Depth** – The number of I/O requests in the queue waiting to be serviced\. These are I/O requests that have been submitted by the application but have not been sent to the device because the device is busy servicing other I/O requests\. Time spent waiting in the queue is a component of latency and service time \(not available as a metric\)\. This metric is reported as the average queue depth for a given time interval\. Amazon RDS reports queue depth in 1\-minute intervals\. Typical values for queue depth range from zero to several hundred\.

Measured IOPS values are independent of the size of the individual I/O operation\. This means that when you measure I/O performance, you should look at the throughput of the instance, not simply the number of I/O operations\.

## Factors that affect storage performance<a name="CHAP_Storage.Other.Factors"></a>

System activities, database workload, and DB instance class can affect storage performance\.

### System activities<a name="other-factors-system"></a>

The following system\-related activities consume I/O capacity and might reduce DB instance performance while in progress:
+ Multi\-AZ standby creation
+ Read replica creation
+ Changing storage types

### Database workload<a name="other-factors-workload"></a>

In some cases, your database or application design results in concurrency issues, locking, or other forms of database contention\. In these cases, you might not be able to use all the provisioned bandwidth directly\. In addition, you might encounter the following workload\-related situations:
+ The throughput limit of the underlying instance type is reached\.
+ Queue depth is consistently less than 1 because your application is not driving enough I/O operations\.
+ You experience query contention in the database even though some I/O capacity is unused\.

In some cases, there isn't a system resource that is at or near a limit, and adding threads doesn't increase the database transaction rate\. In such cases, the bottleneck is most likely contention in the database\. The most common forms are row lock and index page lock contention, but there are many other possibilities\. If this is your situation, seek the advice of a database performance tuning expert\. 

### DB instance class<a name="other-factors-instance"></a>

To get the most performance out of your Amazon RDS DB instance, choose a current generation instance type with enough bandwidth to support your storage type\. For example, you can choose Amazon EBS–optimized instances and instances with 10\-gigabit network connectivity\.

**Important**  
Depending on the instance class you're using, you might see lower IOPS performance than the maximum that you can provision with RDS\. For specific information on IOPS performance for DB instance classes, see [Amazon EBS–optimized instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-optimized.html)\. We recommend that you determine the maximum IOPS for the instance class before setting a Provisioned IOPS value for your DB instance\.

We encourage you to use the latest generation of instances to get the best performance\. Previous generation DB instances can also have lower maximum storage\.

Some older 32\-bit file systems might have lower storage capacities\. To determine the storage capacity of your DB instance, you can use the [describe\-valid\-db\-instance\-modifications](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-valid-db-instance-modifications.html) AWS CLI command\.

The following list shows the maximum storage that most DB instance classes can scale to for each database engine:
+ MariaDB: 64 TiB
+ Microsoft SQL Server: 16 TiB
+ MySQL: 64 TiB
+ Oracle: 64 TiB
+ PostgreSQL: 64 TiB

The following table shows some exceptions for maximum storage \(in TiB\)\. All RDS for Microsoft SQL Server DB instances have a maximum storage of 16 TiB, so there are no entries for SQL Server\.


****  

| Instance class | MariaDB | MySQL | Oracle | PostgreSQL | 
| --- | --- | --- | --- | --- | 
| db\.m3 – standard instance classes | 
| db\.m3\.2xlarge | N/A | 6 | N/A | 6 | 
| db\.m3\.xlarge | N/A | 6 | N/A | 6 | 
| db\.m3\.large | N/A | 6 | N/A | 6 | 
| db\.m3\.medium | N/A | 32 | N/A | 32 | 
| db\.t4g – burstable\-performance instance classes | 
| db\.t4g\.medium | 16 | 16 | N/A | 32 | 
| db\.t4g\.small | 16 | 16 | N/A | 16 | 
| db\.t4g\.micro | 6 | 6 | N/A | 6 | 
| db\.t3 – burstable\-performance instance classes | 
| db\.t3\.medium | 16 | 16 | 32 | 32 | 
| db\.t3\.small | 16 | 16 | 32 | 16 | 
| db\.t3\.micro | 6 | 6 | 32 | 6 | 
| db\.t2 – burstable\-performance instance classes | 
| db\.t2\.medium | 32 | 32 | N/A | 32 | 
| db\.t2\.small | 16 | 16 | N/A | 16 | 
| db\.t2\.micro | 6 | 6 | N/A | 6 | 

For more details about all instance classes supported, see [Previous generation DB instances](http://aws.amazon.com/rds/previous-generation/)\.
