# Storage for Amazon RDS<a name="CHAP_Storage"></a>

Most of Amazon RDS use Amazon Elastic Block Store \(Amazon EBS\) volumes for database and log storage\. The exception is Amazon Aurora, which uses our proprietary storage system\. Depending on the amount of storage requested, Amazon RDS automatically stripes across multiple Amazon EBS volumes to enhance IOPS performance\. Amazon RDS provides three types of storage with a range of storage and performance options, as described following\. 

For more information about pricing, see [Amazon RDS Pricing](https://aws.amazon.com/rds/pricing/)\. 

## Amazon RDS Storage Types<a name="Concepts.Storage"></a>

Amazon RDS provides three storage types: General Purpose \(SSD\), Provisioned IOPS \(input/output operations per second\), and magnetic\. They differ in performance characteristics and price, which means that you can tailor your storage performance and cost to the needs of your database workload\. When using the Provisioned IOPS and General Purpose \(SSD\) storage types, you can create MySQL, MariaDB, Microsoft SQL Server, PostgreSQL, and Oracle RDS DB instances with up to 16 TiB of storage\. 
+ **General Purpose \(SSD\)** – General Purpose \(SSD\), also called gp2, volumes offer cost\-effective storage that is ideal for a broad range of workloads\. These volumes deliver single\-digit millisecond latencies and the ability to burst to 3,000 IOPS for extended periods of time\. Baseline performance for these volumes is determined by the volume's size\. Baseline is 3 IOPS per GiB\. Thus, for larger volumes performance is better\. For example, baseline performance for a 100 GiB volume is 300 IOPS, whereas for a 1\-TiB volume it's 3000 IOPS\. Volumes of 3\.34 TiB and greater have a baseline performance of 10,000 IOPS\. Smaller volumes still perform well, because below 1 TiB volumes reserve a burst balance of 3000 IOPS\. This burst is constantly replenished at 3 IOPS per GiB per second\.

  For more information about general purpose \(SSD\) storage, including the storage size ranges, see [General Purpose \(SSD\) Storage](#Concepts.Storage.GeneralSSD)\. 
+ **Provisioned IOPS** – Provisioned IOPS storage is designed to meet the needs of I/O\-intensive workloads, particularly database workloads, that are sensitive to storage performance and consistency in random access I/O throughput\. You specify the amount of storage you want allocated, and then specify the amount of dedicated IOPS you want\. Amazon RDS delivers within 10 percent of the provisioned IOPS performance 99\.9 percent of the time over a given year\. 

  For more information about provisioned IOPS storage, including the storage size ranges, see [Provisioned IOPS Storage](#USER_PIOPS)\. 
+ **Magnetic** – Amazon RDS also supports magnetic storage for backward compatibility\. We recommend that you use General Purpose \(SSD\) or Provisioned IOPS for any new storage needs\. The maximum amount of storage allowed for DB instances on magnetic storage is less than that of the other storage types\. 

Several factors can affect the performance of Amazon EBS volumes, such as instance configuration, I/O characteristics, and workload demand\. For more information about getting the most out of your Provisioned IOPS volumes, see [Amazon EBS Volume Performance](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSPerformance.html)\. 

For existing DB instances, you might observe some I/O capacity improvement if you scale up your storage\. 

## Performance Metrics<a name="Concepts.Storage.Metrics"></a>

Amazon RDS provides several metrics that you can use to determine how your DB instance is performing\. You can view the metrics in the Amazon RDS Management Console by choosing your DB instance and then choosing **Show Monitoring**\. You can also use Amazon CloudWatch to monitor these metrics\. For more information, see [Viewing DB Instance Metrics](CHAP_Monitoring.md#USER_Monitoring)\. Enhanced Monitoring provides more detailed I/O metrics; for more information, see [Enhanced Monitoring](USER_Monitoring.OS.md)\.
+  **IOPS** – The number of I/O operations completed per second\. This metric is reported as the average IOPS for a given time interval\. Amazon RDS reports read and write IOPS separately on 1\-minute intervals\. Total IOPS is the sum of the read and write IOPS\. Typical values for IOPS range from zero to tens of thousands per second\. 
+  **Latency** – The elapsed time between the submission of an I/O request and its completion\. This metric is reported as the average latency for a given time interval\. Amazon RDS reports read and write latency separately on 1\-minute intervals in units of seconds\. Typical values for latency are in the millisecond \(ms\)\. For example, Amazon RDS reports 2 ms as 0\.002 seconds\. 
+  **Throughput** – The number of bytes per second transferred to or from disk\. This metric is reported as the average throughput for a given time interval\. Amazon RDS reports read and write throughput separately on 1\-minute intervals using units of megabytes per second \(MB/s\)\. Typical values for throughput range from zero to the I/O channel’s maximum bandwidth\. 
+  **Queue Depth** – The number of I/O requests in the queue waiting to be serviced\. These are I/O requests that have been submitted by the application but have not been sent to the device because the device is busy servicing other I/O requests\. Time spent waiting in the queue is a component of latency and service time \(not available as a metric\)\. This metric is reported as the average queue depth for a given time interval\. Amazon RDS reports queue depth in 1\-minute intervals\. Typical values for queue depth range from zero to several hundred\. 

## General Purpose \(SSD\) Storage<a name="Concepts.Storage.GeneralSSD"></a>

General purpose \(SSD\) storage offers cost\-effective storage that is ideal for small or medium\-sized database workloads\. The following are the storage size ranges for General purpose \(SSD\) DB instances: 
+ MySQL, MariaDB, and PostgreSQL DB instances: 20 GiB–16 TiB 
+ Oracle DB instances: 20 GiB–16 TiB 
+ SQL Server Enterprise and Standard editions: 200 GiB–16 TiB 
+ SQL Server Web and Express editions: 20 GiB–16 TiB 

General purpose \(SSD\) storage can deliver single\-digit millisecond latencies and the ability to burst to 3,000 IOPS for extended periods of time\. Between 100 IOPS \(for volumes of 33\.33 GiB and below\) and 10,000 IOPS \(for volumes of 3,334 GiB and above\), baseline performance increases at the rate of 3 IOPS per GiB of volume size\. Burst performance is determined by the instance I/O credit balance\. I/O credit balance is explained in more detail below\. 

Some workloads can exhaust the 3000 IOPS burst storage credit balance, so you should plan your strorage capacity to meet the needs of your workloads\.

### I/O Credits and Burst Performance<a name="CHAP_Storage.IO.Credits"></a>

General Purpose \(SSD\) storage performance is governed by volume size, which dictates the base performance level of the volume and how quickly it accumulates I/O credits\. Larger volumes have higher base performance levels and accumulate I/O credits faster\. *I/O credits* represent the available bandwidth that your General Purpose \(SSD\) storage can use to burst large amounts of I/O when more than the base level of performance is needed\. The more I/O credits your storage has for I/O, the more time it can burst beyond its base performance level and the better it performs when more performance is needed\.

When using General Purpose \(SSD\) storage, your DB instance receives an initial I/O credit balance of 5\.4 million I/O credits, which is enough to sustain a burst performance of 3,000 IOPS for 30 minutes\. This initial I/O credit balance is designed to provide a fast initial boot cycle for boot volumes and to provide a good bootstrapping experience for other applications\. Volumes earn I/O credits at the baseline performance rate of 3 IOPS per GiB of volume size\. For example, a 100 GiB SSD volume has a baseline performance of 300 IOPS\. 

When your storage requires more than the base performance I/O level, it uses I/O credits in the I/O credit balance to burst to the required performance level, up to a maximum of 3,000 IOPS\. Storage larger than 1,000 GiB has a base performance that is equal or greater than the maximum burst performance, so its I/O credit balance never depletes and it can burst indefinitely\. When your storage uses fewer I/O credits than it earns in a second, unused I/O credits are added to the I/O credit balance\. The maximum I/O credit balance for a DB instance using General Purpose \(SSD\) storage is equal to the initial I/O credit balance \(5\.4 million I/O credits\)\.

If your storage uses all of its I/O credit balance, its maximum performance remains at the base performance level until I/O demand drops below the base level and unused I/O credits are added to the I/O credit balance\. \(The *base performance level* is the rate at which your storage earns I/O credits\.\) The more storage, the greater the base performance is and the faster it replenishes the I/O credit balance\. 

**Note**  
Storage conversions between magnetic storage and General Purpose \(SSD\) storage can potentially deplete the initial 5\.4 million I/O credits \(3,000 IOPS X 30 Minutes\) allocated for General Purpose \(SSD\) storage\. When performing these storage conversions, the first 82 GiB of data is converted at approx\. 3,000 IOPS\. The remaining data is converted at the base performance rate of 3 IOPS per GiB of allocated General Purpose \(SSD\) storage\. This approach can result in longer conversion times\. You can provision more General Purpose \(SSD\) storage to increase your base I/O performance rate, thus improving the conversion time, but note that you cannot reduce storage size once it has been allocated\.

The following table lists several storage sizes\. For each, it lists the associated base performance of the storage, which is also the rate at which it accumulates I/O credits\. The table also lists the burst duration at the 3,000 IOPS maximum, when starting with a full I/O credit balance\. In addition, the table lists the time in seconds that the storage takes to refill an empty I/O credit balance\.


|  **Storage size \(GiB\)**  |  **Base Performance \(IOPS\)**  | **Maximum Burst Duration @ 3,000 IOPS \(seconds\)**  | **Seconds to Fill Empty I/O Credit Balance**  | 
| --- | --- | --- | --- | 
|  1  |  100  |  1,862  |  54,000  | 
|  100  |  300  |  2,000  |  18,000  | 
|  250  |  750  |  2,400  |  7,200  | 
|  500  |  1,500  |  3,600  |  3,600  | 
|  750  |  2,250  |  7,200  |  2,400  | 
|  1,000  |  3,000  |  Infinite  |  N/A  | 

The burst duration of your storage depends on the size of the storage, the burst IOPS required, and the I/O credit balance when the burst begins\. This relationship is shown in the equation following\.

```
                              (Credit balance)
   Burst duration =  ------------------------------------
                     (Burst IOPS) - 3(Storage size in GiB)
```

If you notice that your storage performance is frequently limited to the base level due to an empty I/O credit balance, consider allocating more General Purpose \(SSD\) storage with a higher base performance level\. Alternatively, you can switch to Provisioned IOPS storage for workloads that require sustained IOPS performance\.

For workloads with steady state I/O requirements, provisioning less than 100 GiB of General Purpose \(SSD\) storage might result in higher latencies if you exhaust your I/O credit balance\.

**Note**  
In general, most workloads never exceed the I/O credit balance\.

For a more detailed description of how baseline performance and I/O credit balance impact performance see [Understanding Burst vs\. Baseline Performance with Amazon RDS and GP2](https://aws.amazon.com/blogs/database/understanding-burst-vs-baseline-performance-with-amazon-rds-and-gp2/)\. 

## Provisioned IOPS Storage<a name="USER_PIOPS"></a>

For any production application that requires fast and consistent I/O performance, we recommend Provisioned IOPS \(input/output operations per second\) storage\. Provisioned IOPS storage is a storage type that delivers fast, predictable, and consistent throughput performance\. Provisioned IOPS storage is optimized for online transaction processing \(OLTP\) workloads that have consistent performance requirements\. Provisioned IOPS helps performance tuning\. 

When you create a DB instance, you specify an IOPS rate and storage space allocation\. Amazon RDS provisions that IOPS rate and storage for the lifetime of the DB instance or until you change it\. 

**Note**  
Your actual realized IOPS might vary from the value that you specify depending on your database workload, DB instance size, and the page size and channel bandwidth that are available for your DB engine\. For more information, see [Factors That Affect Realized IOPS Rates](#USER_PIOPS.Realized)\. 

The following table shows the IOPS and storage range for each database engine\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Storage.html)

The ratio of the requested IOPS rate to the amount of storage allocated is important, and depends on your database engine\. For example, for Oracle, the ratio should be between 1:1 and 50:1\. You can start by provisioning an Oracle DB instance with 1,000 IOPS and 200 GiB storage \(a ratio of 5:1\)\. You can then scale up to 2,000 IOPS with 200 GiB of storage \(a ratio of 10:1\)\. You can scale up to 30,000 IOPS with 6 TiB \(6144 GiB\) of storage \(a ratio of 5:1\), and you can scale up further if necessary\. 

You can modify an existing Oracle, MySQL, or MariaDB DB instance to use Provisioned IOPS storage\. You can also modify Provisioned IOPS storage settings\. 

### Using Provisioned IOPS Storage with Multi\-AZ, Read Replicas, Snapshots, VPC, and DB Instance Classes<a name="Overview.ProvisionedIOPS-support"></a>

For production OLTP use cases, we recommend that you use Multi\-AZ deployments for enhanced fault tolerance and Provisioned IOPS storage for fast and predictable performance\. In addition to Multi\-AZ deployments, Provisioned IOPS storage complements the following features:
+ Amazon VPC for network isolation and enhanced security\.
+ Read replicas – The type of storage on a read replica is independent of that on the master DB instance\. For example, if the master DB instance uses magnetic storage, you can add read replicas that use Provisioned IOPS storage and vice versa\. You might use magnetic storage–based read replicas with a master DB instance that uses Provisioned IOPS storage\. The performance of your read replicas in this case might differ considerably from that of a configuration where both the master DB instance and read replicas use Provisioned IOPS storage\. 
+ DB snapshots – If you are using a DB instance that uses Provisioned IOPS storage, you can use a DB snapshot to restore an identically configured DB instance\. You can do so regardless of whether the target DB instance uses magnetic storage or Provisioned IOPS storage\. If your DB instance uses magnetic storage, you can use a DB snapshot to restore only a DB instance that uses magnetic storage\. 
+ You can use Provisioned IOPS storage with any DB instance class\. However, smaller DB instance classes don't consistently make the best use of Provisioned IOPS storage\. For the best performance, we recommend that you use one of the DB instance types that are optimized for Provisioned IOPS storage\.

### Provisioned IOPS Storage Costs<a name="Overview.ProvisionedIOPS-cost"></a>

Because Provisioned IOPS storage reserves resources for your use, you are charged for the resources whether or not you use them in a given month\. When you use Provisioned IOPS storage, you are not charged the monthly Amazon RDS I/O charge\. If you prefer to pay only for I/O that you consume, a DB instance that uses magnetic storage might be a better choice\. 

For more information about pricing, see [Amazon RDS Pricing](https://aws.amazon.com/rds/pricing/)\. 

### Getting the Most Out of Amazon RDS Provisioned IOPS<a name="Overview.ProvisionedIOPS.gettingthemostoutofpiops"></a>

Using Provisioned IOPS storage increases the number of I/O requests that the system can process concurrently\. Increased concurrency allows for decreased latency since I/O requests spend less time in a queue\. Decreased latency allows for faster database commits, which improves response time and allows for higher database throughput\. 

For example, consider a heavily loaded OLTP database provisioned for 10,000 Provisioned IOPS that runs consistently at the channel limit of 105 Mbps throughput for reads\. The workload isn’t perfectly balanced, so there is some unused write channel bandwidth\. The instance would consume less than 10,000 IOPS but would still benefit from increasing capacity to 20,000 Provisioned IOPS\. 

Increasing Provisioned IOPS capacity from 10,000 to 20,000 doubles the system’s capacity for concurrent I/O\.  Increased concurrency means decreased latency, which allows transactions to complete faster, so the database transaction rate increases\. Read and write latency would improve by different amounts and the system would settle into a new equilibrium based on whichever resource becomes constrained first\. 

It is possible for Provisioned IOPS consumption to actually *decrease* under these conditions even though the database transaction rate can be much higher\. For example, you can see write requests decline accompanied by an increase in write throughput\. That’s a good indicator that your database is making better use of group commit\.  More write throughput and the same write IOPS means log writes have become larger but are still less than 256 KB\. More write throughput and fewer write I/O means log writes have become larger and are averaging larger than 32 KB since those I/O requests consume more than one I/O of Provisioned IOPS capacity\. 

### Provisioned IOPS Storage Support in the AWS CLI and Amazon RDS API<a name="Overview.ProvisionedIOPS-CLIandAPI"></a>

The AWS CLI supports Provisioned IOPS storage in the following commands:
+ [http://docs.aws.amazon.com/cli/latest/reference/rds/create-db-snapshot.html](http://docs.aws.amazon.com/cli/latest/reference/rds/create-db-snapshot.html) – The output shows the IOPS value\.
+ [http://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html](http://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) – Includes the input parameter `iops`, and the output shows the IOPS rate\.
+ [http://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html](http://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) – Includes the input parameter `iops`, and the output shows the IOPS rate\.
+ [http://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-from-db-snapshot.html](http://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-from-db-snapshot.html) – Includes the input parameter `iops`, and the output shows current IOPS rate\. If **Apply Immediately** was specified, the output also shows the pending IOPS rate\.
+ [http://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-to-point-in-time.html](http://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-to-point-in-time.html) – Includes the input parameter `iops`, and the output shows the IOPS rate\.
+ [http://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance-read-replica.html](http://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance-read-replica.html) – Includes the input parameter `iops`, and the output shows the IOPS rate\.

The Amazon RDS API supports Provisioned IOPS storage in the following actions:
+ [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference//API_CreateDBInstance.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference//API_CreateDBInstance.html) – Includes the input parameter `iops`, and the output shows the IOPS rate\.
+ [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference//API_CreateDBInstanceReadReplica.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference//API_CreateDBInstanceReadReplica.html) – Includes the input parameter `iops`, and the output shows the IOPS rate\.
+ [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference//API_CreateDBSnapshot.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference//API_CreateDBSnapshot.html) – The output shows the IOPS rate\.
+ [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference//API_ModifyDBInstance.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference//API_ModifyDBInstance.html) – Includes the input parameter `iops`, and the output shows the IOPS rate\.
+ [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference//API_RestoreDBInstanceFromDBSnapshot.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference//API_RestoreDBInstanceFromDBSnapshot.html) – Includes the input parameter `iops`, and the output shows current IOPS rate\. If **Apply Immediately** was specified, the output also shows the pending IOPS rate\.
+ [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference//API_RestoreDBInstanceToPointInTime.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference//API_RestoreDBInstanceToPointInTime.html) – Includes the input parameter `iops`, and the output shows the IOPS rate\.

## Adding Storage and Changing Storage Type for MariaDB, MySQL, Oracle, and PostgreSQL<a name="CHAP_Storage.AddingChanging"></a>

For existing DB instances, you might observe some I/O capacity improvement If you run these types of DB instances on current\-generation or next\-generation instance classes, scaling up storage typically only takes a few minutes\. Similarly, converting to a different storage type completes in a short amount of time after a brief DB instance outage\. For more information about current\-generation and next\-generation instance classes, see [DB Instance Class](Concepts.DBInstanceClass.md)\. 

After you modify the storage for a DB instance, the status of the DB instance is **storage\-optimization**\. The DB instance is fully operational after a storage modification\. However, you can't make further storage modifications for either six hours or while the DB instance status is **storage\-optimization**, whichever is longer\. 

## Adding Storage and Changing Storage Type for Microsoft SQL Server<a name="CHAP_Storage.AddingChangingSQL"></a>

For existing DB instances, you might observe some I/O capacity improvement if you scale up your storage\. You can modify a DB instance to use additional storage and you can convert to a different storage type\. 

When you modify your DB instance to increase the allocated storage, a short outage of a few minutes may occur\. After that, the DB instance is online but in the storage\-optimization state\. Performance may be degraded during storage optimization\. The storage optimization process is usually short, but can sometimes take up to and even beyond 24 hours\. Once Amazon RDS begins to modify your SQL Server DB instance to increase the storage size or type, you can't submit another request to increase the storage size or type for six hours, or while the status is storage\-optimization\. There is no impact to other operations, such as backups and snapshots, during storage optimization\. 

For more information, see [Modifying a DB Instance Running the Microsoft SQL Server Database Engine](USER_ModifyInstance.SQLServer.md)\. 

## Facts About Amazon RDS Storage<a name="CHAP_Storage.FactsAbout"></a>

The following points are important facts you should know about Amazon RDS storage: 
+ Maximum channel bandwidth depends on the DB instance class\.
+ I/O size doesn't affect the IOPS values reported by the metrics, which are based solely on the number of I/Os over time\. This functionality means that it is possible to consume all of the IOPS provisioned with fewer I/Os than specified if the I/O sizes are larger than 32 KB\. For example, a system provisioned for 5,000 IOPS can attain a maximum of 2,500 IOPS with 64 KB I/O or 1,250 IOPS with 128 KB I/O\. 

  Magnetic storage doesn't provision I/O capacity, so all I/O sizes are counted as a single I/O\. General purpose storage provisions I/O capacity based on the size of the volume\. For more information on general purpose storage throughput, see [General Purpose \(SSD\) Volumes](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSVolumeTypes.html#EBSVolumeTypes_gp2)\.
+ Provisioned IOPS provides a way to reserve I/O capacity by specifying IOPS\. Like any other system capacity attribute, maximum throughput under load is constrained by the resource that is consumed first\. That resource might be IOPS, channel bandwidth, CPU, memory, or database internal resources\. 

### Other Factors That Impact Storage Performance<a name="CHAP_Storage.Other.Factors"></a>

All of the following system\-related activities consume I/O capacity and might reduce database instance performance while in progress:
+ Multi\-AZ peer creation
+ Read replica creation
+ Scaling storage

System resources can constrain the throughput of a DB instance, but there can be other reasons for a bottleneck\. If you encounter the following situations, the database might be the issue:
+ The channel throughput limit is not reached\.
+ Queue depths are consistently low\.
+ CPU utilization is under 80 percent\.
+ There is free memory available\.
+ Your application has dozens of threads all submitting transactions as fast as the database takes them, but there is clearly unused I/O capacity\.

If there isn’t at least one system resource that is at or near a limit, and adding threads doesn’t increase the database transaction rate, the bottleneck is most likely contention in the database\. The most common forms are row lock and index page lock contention, but there are many other possibilities\. If this is your situation, you should seek the advice of a database performance tuning expert\. 

## Factors That Affect Realized IOPS Rates<a name="USER_PIOPS.Realized"></a>

Your actual realized IOPS rate might vary from the amount that you provision depending on page size and network bandwidth, which are determined in part by your DB engine\. It is also affected by DB instance size and database workload\.

### Page Size and Channel Bandwidth<a name="USER_PIOPS.Realized.PageSize"></a>

The theoretical maximum IOPS rate is also a function of database I/O page size and available channel bandwidth\. MySQL and MariaDB use a page size of 16 KB\. Oracle, PostgreSQL \(default\), and SQL Server use 8 KB\. On a DB instance with a full duplex I/O channel bandwidth of 1000 megabits per second \(Mbps\), the maximum IOPS for page I/O is about 8,000 IOPS total for both directions \(input/output channel\) for 16 KB I/O\. It is 16,000 IOPS total for both directions for 8 KB I/O\. 

If traffic on one of the channels reaches capacity, available IOPS on the other channel cannot be reallocated\. As a result, the attainable IOPS rate is less than the provisioned IOPS rate\. 

Each page read or write action constitutes one I/O operation\. Database operations that read or write more than a single page use multiple I/O operations for each database operation\. I/O requests larger than 32 KB are treated as more than one I/O for the purposes of PIOPS capacity consumption\. A 40 KB I/O request consumes 1\.25 I/Os, a 48 KB request consumes 1\.5 I/Os, a 64 KB request consumes 2 I/Os, and so on\. The I/O request isn't split into separate I/Os; all I/O requests are presented to the storage device unchanged\. For example, if the database submits a 128 KB I/O request, it goes to the storage device as a single 128 KB I/O request\. However, it consumes the same amount of PIOPS capacity as four 32 KB I/O requests\.

### DB Instance Classes for Provisioned IOPS<a name="USER_PIOPS.Realized.DBInstanceClass"></a>

If you are using Provisioned IOPS storage, we recommend that you use the M4, M3, R4, R3, and M2 DB instance classes\. These instance classes are optimized for Provisioned IOPS storage; other instance classes are not\. 

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Storage.html)

\* The r3\.8xlarge DB instance class has a 10\-gigabit network interface that doesn't offer Amazon EBS optimization\. Therefore, dedicated EBS bandwidth is not available in the r3\.8xlarge DB instance class\. However, you can use all of that bandwidth for traffic to EBS if your application isn’t pushing other network traffic that contends with EBS\.

\*\* This value is a rounded approximation based on a 100 percent read\-only workload, and it's provided as a baseline configuration aid\. EBS\-optimized connections are full\-duplex\. They can drive more throughput and IOPS in a 50/50 read/write workload where both communication lanes are used\. In some cases, network, file system, and EBS encryption overhead can reduce the maximum throughput and IOPS available\.

### Database Workload<a name="USER_PIOPS.Realized.DatabaseWorkload"></a>

System activities such as automated backups, DB snapshots, and scale storage operations might consume some I/O, which reduces the overall capacity available for normal database operations\. If your database design results in concurrency issues, locking, or other forms of database contention, you might not be able to directly use all the bandwidth that you provision\. 

If you provision IOPS capacity to meet your peak workload demand, during the nonpeak periods your application probably consumes fewer IOPS on average than provisioned\. 

To help you verify that you are making the best use of your Provisioned IOPS storage, we have added a new CloudWatch Metric called Disk Queue Depth\. If your application is maintaining an average queue depth of approximately 5 outstanding I/O operations per 1000 IOPS that you provision, you can assume that you are consuming the capacity that you provisioned\. For example, if you provisioned 10,000 IOPS, you should have a minimum of 50 outstanding I/O operations in order to use the capacity you provisioned\.

## Amazon RDS Storage Limitations<a name="Storage.Limitations"></a>

The following are Amazon RDS storage limitations:
+ You can't decrease allocated storage for a DB instance\.
+ If your DB instance is running on a previous generation instance class, adding storage or converting to a different storage type can take time and might slightly reduce the performance of your DB instance\. So, you should plan when to make these changes\.

  Although your DB instance is available for reads and writes when adding storage, you might experience degraded performance until the process is complete\. Adding storage might take several hours; the duration of the process depends on several factors such as database load, storage size, storage type, and amount of IOPS provisioned\. Typical scale storage time, depending on the size of the source volume, is between one and two hours, but can take up to several days in some cases\. During the scaling process, the DB instance is available for use but might experience performance degradation\. While storage is being added, nightly backups are suspended and no other Amazon RDS operations can take place, including modify, reboot, delete, create Read Replica, and create DB Snapshot\.

  For more information about DB instance classes, see [DB Instance Class](Concepts.DBInstanceClass.md)\. 
  
+ For any type of instance class \(next generation, current generation, or previous generation\), storage conversions to or from magnetic storage and any other type of storage can take a long time\. Storage conversions between magnetic storage and general purpose \(SSD\) storage can potentially deplete the initial 5\.4 million I/O credits \(3,000 IOPS X 30 minutes\) allocated for general purpose \(SSD\) storage\. When performing these storage conversions, the first 82 GiB of data is converted at approximately 3,000 IOPS\. The remaining data is converted at the base performance rate of 3 IOPS per GiB of allocated general purpose \(SSD\) storage\. This approach can result in longer conversion times\.

