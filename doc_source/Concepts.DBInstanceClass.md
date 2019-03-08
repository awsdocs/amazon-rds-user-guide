# DB Instance Class<a name="Concepts.DBInstanceClass"></a>

The DB instance class determines the computation and memory capacity of an Amazon RDS DB instance\. The DB instance class you need depends on your processing power and memory requirements\. 

For more information about instance class pricing, see [Amazon RDS Pricing](https://aws.amazon.com/rds/pricing/)\. 

## DB Instance Class Types<a name="Concepts.DBInstanceClass.Types"></a>

Amazon RDS supports three types of instance classes: Standard, Memory Optimized, and Burstable Performance\. For more information about Amazon EC2 instance types, see [Instance Type](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-types.html) in the Amazon EC2 documentation\. 

The following are the Standard DB instance classes available:
+ **db\.m5** – Latest\-generation general\-purpose instance classes that provide a balance of compute, memory, and network resources, and are a good choice for many applications\. The db\.m5 instance classes provide more computing capacity than the previous db\.m4 instance classes\. 
+ **db\.m4** – Current\-generation general\-purpose instance classes that provide more computing capacity than the previous db\.m3 instance classes\. 
+ **db\.m3** – Previous\-generation general\-purpose instance classes that provide more computing capacity than the previous db\.m1 instance classes\. 
+ **db\.m1** – Previous\-generation general\-purpose instance classes\. 

The following are the Memory Optimized DB instance classes available:
+ **db\.x1e** – Latest\-generation instance classes optimized for memory\-intensive applications\. These offer one of the lowest price per GiB of RAM among the DB instance classes and up to 3,904 GiB of DRAM\-based instance memory\. The db\.x1e instance classes are available only in the following regions: US East \(N\. Virginia\), US West \(Oregon\), EU \(Ireland\), Asia Pacific \(Tokyo\), and Asia Pacific \(Sydney\)\.
+ **db\.x1** – Current\-generation instance classes optimized for memory\-intensive applications\. These offer one of the lowest price per GiB of RAM among the DB instance classes and up to 1,952 GiB of DRAM\-based instance memory\. 
+ **db\.r5** – Latest\-generation instance classes optimized for memory\-intensive applications\. These offer improved networking and Amazon Elastic Block Store \(Amazon EBS\) performance\. They are powered by the AWS Nitro System, a combination of dedicated hardware and lightweight hypervisor\.
+ **db\.r4** – Current\-generation instance classes optimized for memory\-intensive applications\. These offer improved networking and Amazon EBS performance\.
+ **db\.r3** – Previous\-generation instance classes that provide memory optimization and more computing capacity than the db\.m2 instance classes\. The db\.r3 instances classes are not available in the EU \(Paris\) region and the South America \(São Paulo\) region\. 
+ **db\.m2** – Previous\-generation memory\-optimized instance classes\. 

The following are the Burstable Performance DB instance classes available:
+ **db\.t3** – Latest\-generation instance classes that provide a baseline performance level, with the ability to burst to full CPU usage\. These instance classes provide more computing capacity than the previous db\.t2 instance classes\. 
+ **db\.t2** – Current\-generation instance classes that provide a baseline performance level, with the ability to burst to full CPU usage\. 

## Specifications for All Available DB Instance Classes<a name="Concepts.DBInstanceClass.Summary"></a>

The following table provides details of the Amazon RDS DB instance classes\. The table columns are explained after the table\. 


****  

| Instance Class | vCPU1 | ECU2 | Memory3 \(GiB\) | VPC Only4 | EBS Optimized5 | Max\. Bandwidth6 \(Mbps\) | Network Performance7 | MariaDB | Microsoft SQL Server8 | MySQL | Oracle9 | PostgreSQL10 | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| db\.m5 – Latest Generation Standard Instance Classes | 
| db\.m5\.24xlarge | 96 | 345 | 384 | Yes | Yes | 14,000 | 25 Gbps | Yes | No | Yes | Yes9 | Yes10 | 
| db\.m5\.12xlarge | 48 | 173 | 192 | Yes | Yes | 7,000 | 10 Gbps | Yes | No | Yes | Yes9 | Yes10 | 
| db\.m5\.4xlarge | 16 | 61 | 64 | Yes | Yes | 3,500 | Up to 10 Gigabit | Yes | No | Yes | Yes9 | Yes10 | 
| db\.m5\.2xlarge | 8 | 31 | 32 | Yes | Yes | 3,500 | Up to 10 Gigabit | Yes | No | Yes | Yes9 | Yes10 | 
| db\.m5\.xlarge | 4 | 15 | 16 | Yes | Yes | 3,500 | Up to 10 Gigabit | Yes | No | Yes | Yes9 | Yes10 | 
| db\.m5\.large | 2 | 10 | 8 | Yes | Yes | 3,500 | Up to 10 Gigabit | Yes | No | Yes | Yes9 | Yes10 | 
| db\.m4 – Current Generation Standard Instance Classes | 
| db\.m4\.16xlarge | 64 | 188 | 256 | Yes | Yes | 10,000 | 25 Gbps | Yes | Yes8 | MySQL 8\.0, 5\.7, 5\.6 | Yes9 | Yes | 
| db\.m4\.10xlarge | 40 | 124\.5 | 160 | Yes | Yes | 4,000 | 10 Gbps | Yes | Yes8 | Yes | Yes9 | Yes | 
| db\.m4\.4xlarge | 16 | 53\.5 | 64 | Yes | Yes | 2,000 | High | Yes | Yes8 | Yes | Yes9 | Yes | 
| db\.m4\.2xlarge | 8 | 25\.5 | 32 | Yes | Yes | 1,000 | High | Yes | Yes8 | Yes | Yes9 | Yes | 
| db\.m4\.xlarge | 4 | 13 | 16 | Yes | Yes | 750 | High | Yes | Yes8 | Yes | Yes9 | Yes | 
| db\.m4\.large | 2 | 6\.5 | 8 | Yes | Yes | 450 | Moderate | Yes | Yes8 | Yes | Yes9 | Yes | 
| db\.m3 – Previous Generation Standard Instance Classes | 
| db\.m3\.2xlarge | 8 | 26 | 30 | No | Yes | 1,000 | High | No | Yes8 | Yes | Yes9 | Yes | 
| db\.m3\.xlarge | 4 | 13 | 15 | No | Yes | 500 | High | No | Yes8 | Yes | Yes9 | Yes | 
| db\.m3\.large | 2 | 6\.5 | 7\.5 | No | No | — | Moderate | No | Yes8 | Yes | Yes9 | Yes | 
| db\.m3\.medium | 1 | 3 | 3\.75 | No | No | — | Moderate | No | Yes8 | Yes | Yes9 | Yes | 
| db\.m1 – Previous Generation Standard Instance Classes | 
| db\.m1\.xlarge | 4 | 4 | 15 | No | Yes | 450 | High | No | Yes8 | MySQL 5\.6, 5\.5 | Deprecated9 | PostgreSQL 9\.4, 9\.3 | 
| db\.m1\.large | 2 | 2 | 7\.5 | No | Yes | 450 | Moderate | No | Yes8 | MySQL 5\.6, 5\.5 | Deprecated9 | PostgreSQL 9\.4, 9\.3 | 
| db\.m1\.medium | 1 | 1 | 3\.75 | No | No | — | Moderate | No | Yes8 | MySQL 5\.6, 5\.5 | Deprecated9 | PostgreSQL 9\.4, 9\.3 | 
| db\.m1\.small | 1 | 1 | 1\.7 | No | No | — | Very Low | No | Yes8 | MySQL 5\.6, 5\.5 | Deprecated9 | PostgreSQL 9\.4, 9\.3 | 
| db\.x1e – Latest Generation Memory Optimized Instance Classes | 
| db\.x1e\.32xlarge | 128 | 340 | 3,904 | Yes | Yes | 14,000 | 25 Gbps | No | No | No | Yes9 | No | 
| db\.x1e\.16xlarge | 64 | 179 | 1,952 | Yes | Yes | 7,000 | 10 Gbps | No | No | No | Yes9 | No | 
| db\.x1e\.8xlarge | 32 | 91 | 976 | Yes | Yes | 3,500 | Up to 10 Gbps | No | No | No | Yes9 | No | 
| db\.x1e\.4xlarge | 16 | 47 | 488 | Yes | Yes | 1,750 | Up to 10 Gbps | No | No | No | Yes9 | No | 
| db\.x1e\.2xlarge | 8 | 23 | 244 | Yes | Yes | 1,000 | Up to 10 Gbps | No | No | No | Yes9 | No | 
| db\.x1e\.xlarge | 4 | 12 | 122 | Yes | Yes | 500 | Up to 10 Gbps | No | No | No | Yes9 | No | 
| db\.x1 – Current Generation Memory Optimized Instance Classes | 
| db\.x1\.32xlarge | 128 | 349 | 1,952 | Yes | Yes | 14,000 | 25 Gbps | No | No | No | Yes9 | No | 
| db\.x1\.16xlarge | 64 | 174\.5 | 976 | Yes | Yes | 7,000 | 10 Gbps | No | No | No | Yes9 | No | 
| db\.r5 – Latest Generation Memory Optimized Instance Classes | 
| db\.r5\.24xlarge | 96 | 347 | 768 | Yes | Yes | 14,000 | 25 Gbps | Yes | No | Yes | Yes9 | Yes10 | 
| db\.r5\.12xlarge | 48 | 173 | 384 | Yes | Yes | 7,000 | 10 Gbps | Yes | No | Yes | Yes9 | Yes10 | 
| db\.r5\.4xlarge | 16 | 71 | 128 | Yes | Yes | 3,500 | Up to 10 Gbps | Yes | No | Yes | Yes9 | Yes10 | 
| db\.r5\.2xlarge | 8 | 38 | 64 | Yes | Yes | Up to 3,500 | Up to 10 Gbps | Yes | No | Yes | Yes9 | Yes10 | 
| db\.r5\.xlarge | 4 | 19 | 32 | Yes | Yes | Up to 3,500 | Up to 10 Gbps | Yes | No | Yes | Yes9 | Yes10 | 
| db\.r5\.large | 2 | 10 | 16 | Yes | Yes | Up to 3,500 | Up to 10 Gbps | Yes | No | Yes | Yes9 | Yes10 | 
| db\.r4 – Current Generation Memory Optimized Instance Classes | 
| db\.r4\.16xlarge | 64 | 195 | 488 | Yes | Yes | 14,000 | 25 Gbps | Yes | Yes8 | MySQL 8\.0, 5\.7, 5\.6 | Yes9 | PostgreSQL 9\.6, 9\.5, 9\.4 | 
| db\.r4\.8xlarge | 32 | 99 | 244 | Yes | Yes | 7,000 | 10 Gbps | Yes | Yes8 | MySQL 8\.0, 5\.7, 5\.6 | Yes9 | PostgreSQL 9\.6, 9\.5, 9\.4 | 
| db\.r4\.4xlarge | 16 | 53 | 122 | Yes | Yes | 3,500 | Up to 10 Gbps | Yes | Yes8 | MySQL 8\.0, 5\.7, 5\.6 | Yes9 | PostgreSQL 9\.6, 9\.5, 9\.4 | 
| db\.r4\.2xlarge | 8 | 27 | 61 | Yes | Yes | 1,750 | Up to 10 Gbps | Yes | Yes8 | MySQL 8\.0, 5\.7, 5\.6 | Yes9 | PostgreSQL 9\.6, 9\.5, 9\.4 | 
| db\.r4\.xlarge | 4 | 13\.5 | 30\.5 | Yes | Yes | 875 | Up to 10 Gbps | Yes | Yes8 | MySQL 8\.0, 5\.7, 5\.6 | Yes9 | PostgreSQL 9\.6, 9\.5, 9\.4 | 
| db\.r4\.large | 2 | 7 | 15\.25 | Yes | Yes | 437 | Up to 10 Gbps | Yes | Yes8 | MySQL 8\.0, 5\.7, 5\.6 | Yes9 | PostgreSQL 9\.6, 9\.5, 9\.4 | 
| db\.r3 – Previous Generation Memory Optimized Instance Classes | 
| db\.r3\.8xlarge | 32 | 104 | 244 | No | No | — | 10 Gbps | Yes | Yes8 | Yes | Yes9 | Yes | 
| db\.r3\.4xlarge | 16 | 52 | 122 | No | Yes | 2,000 | High | Yes | Yes8 | Yes | Yes9 | Yes | 
| db\.r3\.2xlarge | 8 | 26 | 61 | No | Yes | 1,000 | High | Yes | Yes8 | Yes | Yes9 | Yes | 
| db\.r3\.xlarge | 4 | 13 | 30\.5 | No | Yes | 500 | Moderate | Yes | Yes8 | Yes | Yes9 | Yes | 
| db\.r3\.large | 2 | 6\.5 | 15\.25 | No | No | — | Moderate | Yes | Yes8 | Yes | Yes9 | Yes | 
| db\.m2 – Previous Generation Memory Optimized Instance Classes | 
| db\.m2\.4xlarge | 8 | 26 | 68\.4 | No | Yes | 1,000 | High | No | Yes8 | MySQL 5\.6, 5\.5 | Deprecated9 | PostgreSQL 9\.4, 9\.3 | 
| db\.m2\.2xlarge | 4 | 13 | 34\.2 | No | Yes | 500 | Moderate | No | Yes8 | MySQL 5\.6, 5\.5 | Deprecated9 | PostgreSQL 9\.4, 9\.3 | 
| db\.m2\.xlarge | 2 | 6\.5 | 17\.1 | No | No | — | Moderate | No | Yes8 | MySQL 5\.6, 5\.5 | Deprecated9 | PostgreSQL 9\.4, 9\.3 | 
| db\.t3 – Latest Generation Burstable Performance Instance Classes | 
| db\.t3\.2xlarge | 8 | Variable | 32 | Yes | Yes | 2,050 | Up to 5 Gigabit | Yes | No | Yes | Yes9 | Yes10 | 
| db\.t3\.xlarge | 4 | Variable | 16 | Yes | Yes | 2,050 | Up to 5 Gigabit | Yes | No | Yes | Yes9 | Yes10 | 
| db\.t3\.large | 2 | Variable | 8 | Yes | Yes | 2,050 | Up to 5 Gigabit | Yes | No | Yes | Yes9 | Yes10 | 
| db\.t3\.medium | 2 | Variable | 4 | Yes | Yes | 1,500 | Up to 5 Gigabit | Yes | No | Yes | Yes9 | Yes10 | 
| db\.t3\.small | 2 | Variable | 2 | Yes | Yes | 1,500 | Up to 5 Gigabit | Yes | No | Yes | Yes9 | Yes10 | 
| db\.t3\.micro | 2 | Variable | 1 | Yes | Yes | 1,500 | Up to 5 Gigabit | Yes | No | Yes | Yes9 | Yes10 | 
| db\.t2 – Current Generation Burstable Performance Instance Classes | 
| db\.t2\.2xlarge | 8 | Variable | 32 | Yes | No | — | Moderate | Yes | No | MySQL 8\.0, 5\.7, 5\.6 | Yes9 | PostgreSQL 9\.6, 9\.5, 9\.4 | 
| db\.t2\.xlarge | 4 | Variable | 16 | Yes | No | — | Moderate | Yes | No | MySQL 8\.0, 5\.7, 5\.6 | Yes9 | PostgreSQL 9\.6, 9\.5, 9\.4 | 
| db\.t2\.large | 2 | Variable | 8 | Yes | No | — | Moderate | Yes | Yes8 | Yes | Yes9 | Yes | 
| db\.t2\.medium | 2 | Variable | 4 | Yes | No | — | Moderate | Yes | Yes8 | Yes | Yes9 | Yes | 
| db\.t2\.small | 1 | Variable | 2 | Yes | No | — | Low | Yes | Yes8 | Yes | Yes9 | Yes | 
| db\.t2\.micro | 1 | Variable | 1 | Yes | No | — | Low | Yes | Yes8 | Yes | Yes9 | Yes | 

1. **vCPU** – The number of virtual central processing units \(CPUs\)\. A virtual CPU is a unit of capacity that you can use to compare DB instance classes\. Instead of purchasing or leasing a particular processor to use for several months or years, you are renting capacity by the hour\. Our goal is to make a consistent and specific amount of CPU capacity available, within the limits of the actual underlying hardware\. 

1. **ECU** – The relative measure of the integer processing power of an Amazon EC2 instance\. To make it easy for developers to compare CPU capacity between different instance classes, we have defined an Amazon EC2 Compute Unit\. The amount of CPU that is allocated to a particular instance is expressed in terms of these EC2 Compute Units\. One ECU currently provides CPU capacity equivalent to a 1\.0–1\.2 GHz 2007 Opteron or 2007 Xeon processor\. 

1. **Memory \(GiB\)** – The RAM memory, in gibibytes, allocated to the DB instance\. There is often a consistent ratio between memory and vCPU\. For example, the db\.m1 instance class has the same memory to vCPU ratio as the db\.m3 instance class, but for most use cases the db\.m3 instance class provides better, more consistent performance, than the db\.m1 instance class\. 

1. **VPC Only** – The instance class is supported only for DB instances that are in an Amazon Virtual Private Cloud \(VPC\)\. If your current DB instance is not in a VPC, and you want to use an instance class that requires a VPC, first move your DB instance into a VPC\. For more information, see [Moving a DB Instance Not in a VPC into a VPC](USER_VPC.md#USER_VPC.Non-VPC2VPC)\. 

1. **EBS\-Optimized** – The DB instance uses an optimized configuration stack and provides additional, dedicated capacity for I/O\. This optimization provides the best performance by minimizing contention between I/O and other traffic from your instance\. For more information about Amazon EBS–optimized instances, see [Amazon EBS–Optimized Instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSOptimized.html) in the Amazon EC2 documentation\. 

1. **Max\. Bandwidth \(Mbps\)** – The maximum bandwidth in megabits per second\. Divide by 8 to get the expected throughput in megabytes per second\. 
**Important**  
General Purpose SSD \(gp2\) volumes for Amazon RDS DB instances have a throughput limit of 250 MiB/s in most cases\. However, the throughput limit can vary depending on volume size\. For more information, see [Amazon EBS Volume Types](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSVolumeTypes.html) in the Amazon EC2 documentation\. For information on estimating bandwidth for gp2 storage, see [General Purpose SSD Storage](CHAP_Storage.md#Concepts.Storage.GeneralSSD)\.

1. **Network Performance** – The network speed relative to other DB instance classes\. 

1. **Microsoft SQL Server** – Instance class support varies according to the version and edition of SQL Server\. For instance class support by version and edition, see [DB Instance Class Support for Microsoft SQL Server](CHAP_SQLServer.md#SQLServer.Concepts.General.InstanceClasses)\. 

1. **Oracle** – Instance class support varies according to the version and edition of Oracle\. For instance class support by version and edition, see [DB Instance Class Support for Oracle](CHAP_Oracle.md#Oracle.Concepts.InstanceClasses)\. 

1. **PostgreSQL** – PostgreSQL versions 9\.6\.9 \(and above\) and 10\.4 \(and above\) are supported\. 

## Changing Your DB Instance Class<a name="Concepts.DBInstanceClass.Changing"></a>

You can change the CPU and memory available to a DB instance by changing its DB instance class\. To change the DB instance class, modify your DB instance by following the instructions for your specific database engine\. 
+ [Modifying a DB Instance Running the MariaDB Database Engine](USER_ModifyInstance.MariaDB.md)
+ [Modifying a DB Instance Running the Microsoft SQL Server Database Engine](USER_ModifyInstance.SQLServer.md)
+ [Modifying a DB Instance Running the MySQL Database Engine](USER_ModifyInstance.MySQL.md)
+ [Modifying a DB Instance Running the Oracle Database Engine](USER_ModifyInstance.Oracle.md)
+ [Modifying a DB Instance Running the PostgreSQL Database Engine](USER_ModifyPostgreSQLInstance.md)

MySQL DB instances created after April 23, 2014, can change to the db\.r3 instance class by modifying the DB instance just as with any other modification\. MySQL DB instances running MySQL versions 5\.5 and created before April 23, 2014, must first upgrade to MySQL version 5\.6\. For more information, see [Upgrading the MySQL DB Engine](USER_UpgradeDBInstance.MySQL.md)\. 

Some instance classes require that your DB instance is in a VPC\. If your current DB instance is not in a VPC, and you want to use an instance class that requires a VPC, first move your DB instance into a VPC\. For more information, see [Moving a DB Instance Not in a VPC into a VPC](USER_VPC.md#USER_VPC.Non-VPC2VPC)\. 

## Configuring the Processor for a DB Instance Class<a name="USER_ConfigureProcessor"></a>

Amazon RDS DB instance classes support Intel Hyper\-Threading Technology, which enables multiple threads to run concurrently on a single Intel Xeon CPU core\. Each thread is represented as a virtual CPU \(vCPU\) on the DB instance\. A DB instance has a default number of CPU cores, which varies according to DB instance type\. For example, a db\.m4\.xlarge DB instance type has two CPU cores and two threads per core by default—four vCPUs in total\.

**Note**  
Each vCPU is a hyperthread of an Intel Xeon CPU core\.

In most cases, you can find a DB instance class that has a combination of memory and number of vCPUs to suit your workloads\. However, you can also specify the following processor features to optimize your DB instance for specific workloads or business needs:
+ **Number of CPU cores** – You can customize the number of CPU cores for the DB instance\. You might do this to potentially optimize the licensing costs of your software with a DB instance that has sufficient amounts of RAM for memory\-intensive workloads but fewer CPU cores\.
+ **Threads per core** – You can disable Intel Hyper\-Threading Technology by specifying a single thread per CPU core\. You might do this for certain workloads, such as high\-performance computing \(HPC\) workloads\.

You can control the number of CPU cores and threads for each core separately\. You can set one or both in a request\. After a setting is associated with a DB instance, the setting persists until you change it\.

The processor settings for a DB instance are associated with snapshots of the DB instance\. When a snapshot is restored, its restored DB instance uses the processor feature settings used when the snapshot was taken\.

If you modify the DB instance class for a DB instance with nondefault processor settings, you must either specify default processor settings or explicitly specify processor settings when you modify the DB instance\. This requirement ensures that you are aware of the third\-party licensing costs that might be incurred when you modify the DB instance\.

There is no additional or reduced charge for specifying processor features on an Amazon RDS DB instance\. You're charged the same as for DB instances that are launched with default CPU configurations\.

You can configure the number of CPU cores and threads per core for the DB instance class when you perform the following operations:
+ Creating a DB instance
+ Modifying a DB instance
+ Restoring a DB instance from a snapshot
+ Restoring a DB instance to a point in time

**Note**  
When you modify a DB instance to configure the number of CPU cores or threads per core, there is a brief DB instance outage\.

### CPU Cores and Threads Per CPU Core Per DB Instance Class<a name="USER_ConfigureProcessor.CPUOptionsDBInstanceClass"></a>

In following table, you can find the DB instance classes that support setting a number of CPU cores and CPU threads per core\. You can also find the default value and the valid values for the number of CPU cores and CPU threads per core for each DB instance class\.


****  

| DB Instance Class | Default vCPUs | Default CPU Cores | Default Threads per Core | Valid Number of CPU Cores | Valid Number of Threads per Core | 
| --- | --- | --- | --- | --- | --- | 
|  db\.m5\.large  |  2  |  1  |  2  |  1  |  1, 2  | 
|  db\.m5\.xlarge  |  4  |  2  |  2  |  2  |  1, 2  | 
|  db\.m5\.2xlarge  |  8  |  4  |  2  |  2, 4  |  1, 2  | 
|  db\.m5\.4xlarge  |  16  |  8  |  2  |  2, 4, 6, 8  |  1, 2  | 
|  db\.m5\.12xlarge  |  48  |  24  |  2  |  2, 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24  |  1, 2  | 
|  db\.m5\.24xlarge  |  96  |  48  |  2  |  2, 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24, 26, 28, 30, 32, 34, 36, 38, 40, 42, 44, 46, 48  |  1, 2  | 
|  db\.m4\.10xlarge  |  40  |  20  |  2  |  2, 4, 6, 8, 10, 12, 14, 16, 18, 20  |  1, 2  | 
|  db\.m4\.16xlarge  |  64  |  32  |  2  |  2, 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24, 26, 28, 30, 32  |  1, 2  | 
|  db\.r3\.large  |  2  |  1  |  2  |  1  |  1, 2  | 
|  db\.r3\.xlarge  |  4  |  2  |  2  |  1, 2  |  1, 2  | 
|  db\.r3\.2xlarge  |  8  |  4  |  2  |  1, 2, 3, 4  |  1, 2  | 
|  db\.r3\.4xlarge  |  16  |  8  |  2  |  1, 2, 3, 4, 5, 6, 7, 8  |  1, 2  | 
|  db\.r3\.8xlarge  |  32  |  16  |  2  |  2, 4, 6, 8, 10, 12, 14, 16  |  1, 2  | 
|  db\.r5\.large  |  2  |  1  |  2  |  1  |  1  | 
|  db\.r5\.xlarge  |  4  |  2  |  2  |  2  |  1, 2  | 
|  db\.r5\.2xlarge  |  8  |  4  |  2  |  2, 4  |  1, 2  | 
|  db\.r5\.4xlarge  |  16  |  8  |  2  |  2, 4, 6, 8  |  1, 2  | 
|  db\.r5\.12xlarge  |  48  |  24  |  2  |  2, 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24  |  1, 2  | 
|  db\.r5\.24xlarge  |  96  |  48  |  2  |  2, 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24, 26, 28, 30, 32, 34, 36, 38, 40, 42, 44, 46, 48  |  1, 2  | 
|  db\.r4\.large  |  2  |  1  |  2  |  1  |  1, 2  | 
|  db\.r4\.xlarge  |  4  |  2  |  2  |  1, 2  |  1, 2  | 
|  db\.r4\.2xlarge  |  8  |  4  |  2  |  1, 2, 3, 4  |  1, 2  | 
|  db\.r4\.4xlarge  |  16  |  8  |  2  |  1, 2, 3, 4, 5, 6, 7, 8  |  1, 2  | 
|  db\.r4\.8xlarge  |  32  |  16  |  2  |  1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16  |  1, 2  | 
|  db\.r4\.16xlarge  |  64  |  32  |  2  |  2, 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24, 26, 28, 30, 32  |  1, 2  | 
|  db\.x1\.16xlarge  |  64  |  32  |  2  |  2, 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24, 26, 28, 30, 32  |  1, 2  | 
|  db\.x1\.32xlarge  |  128  |  64  |  2  |  4, 8, 12, 16, 20, 24, 28, 32, 36, 40, 44, 48, 52, 56, 60, 64  |  1, 2  | 
|  db\.x1e\.xlarge  |  4  |  2  |  2  |  1, 2  |  1, 2  | 
|  db\.x1e\.2xlarge  |  8  |  4  |  2  |  1, 2, 3, 4  |  1, 2  | 
|  db\.x1e\.4xlarge  |  16  |  8  |  2  |  1, 2, 3, 4, 5, 6, 7, 8  |  1, 2  | 
|  db\.x1e\.8xlarge  |  32  |  16  |  2  |  1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16  |  1, 2  | 
|  db\.x1e\.16xlarge  |  64  |  32  |  2  |  2, 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24, 26, 28, 30, 32  |  1, 2  | 
|  db\.x1e\.32xlarge  |  128  |  64  |  2  |  4, 8, 12, 16, 20, 24, 28, 32, 36, 40, 44, 48, 52, 56, 60, 64  |  1, 2  | 

**Note**  
Currently, you can configure the number of CPU cores and threads per core only for Oracle DB instances\. For information about the DB instance classes supported by different Oracle database editions, see [DB Instance Class Support for Oracle](CHAP_Oracle.md#Oracle.Concepts.InstanceClasses)\.  
For Oracle DB instances, configuring the number of CPU cores and threads per core is only supported with the Bring Your Own License \(BYOL\) licensing option\. For more information about Oracle licensing options, see [Oracle Licensing](CHAP_Oracle.md#Oracle.Concepts.Licensing)\.

### Setting the CPU Cores and Threads per CPU Core for a DB Instance Class<a name="USER_ConfigureProcessor.SettingCPUOptions"></a>

You can set the CPU cores and the threads per CPU core for a DB instance class using the AWS Management Console, the AWS CLI, or the RDS API\.

#### AWS Management Console<a name="USER_ConfigureProcessor.Console"></a>

When you are creating, modifying, or restoring a DB instance, you set the DB instance class in the AWS Management Console\. The **Instance specifications** section shows options for the processor\. The following image shows the processor features options\.

![\[Configure processor options\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/vcpu-config.png)

Set the following options to the appropriate values for your DB instance class under **Processor features**:
+ **Core count – **Set the number of CPU cores using this option\. The value must be equal to or less than the maximum number of CPU cores for the DB instance class\.
+ **Threads per core** – Specify **2** to enable multiple threads per core, or specify **1** to disable multiple threads per core\.

When you modify or restore a DB instance, you can also set the CPU cores and the threads per CPU core to the default settings for the selected DB instance class\.

When you view the details for a DB instance in the console, you can view the processor information for its DB instance class on the **Configuration** tab\. The following image shows a DB instance class with one CPU core and multiple threads per core enabled\.

![\[View processor options\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/vcpu-view.png)

For Oracle DB instances, the processor information only appears for Bring Your Own License \(BYOL\) DB instances\.

#### CLI<a name="USER_ConfigureProcessor.CLI"></a>

You can set the processor features for a DB instance when you run one of the following AWS CLI commands:
+ [create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html)
+ [modify\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html)
+ [restore\-db\-instance\-from\-db\-snapshot](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-from-db-snapshot.html)
+ [restore\-db\-instance\-from\-s3](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-from-s3.html)
+ [restore\-db\-instance\-to\-point\-in\-time](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-to-point-in-time.html)

To configure the processor of a DB instance class for a DB instance by using the AWS CLI, include the `--processor-features` option in the command\. Specify the number of CPU cores with the `coreCount` feature name, and specify whether multiple threads per core are enabled with the `threadsPerCore` feature name\. 

The option has the following syntax\.

```
--processor-features "Name=coreCount,Value=<value>" "Name=threadsPerCore,Value=<value>"            
```

**Example Setting the Number of CPU Cores for a DB Instance**  
The following example modifies `mydbinstance` by setting the number of CPU cores to 4\. The changes are applied immediately by using `--apply-immediately`\. If you want to apply the changes during the next scheduled maintenance window, omit the `--apply-immediately` option\.   
For Linux, OS X, or Unix:  

```
aws rds modify-db-instance \
    --processor-features "Name=coreCount,Value=4" \
    --apply-immediately
```
For Windows:  

```
aws rds modify-db-instance ^
    --processor-features "Name=coreCount,Value=4" ^
    --apply-immediately
```

**Example Setting the Number of CPU Cores and Disabling Multiple Threads for a DB Instance**  
The following example modifies `mydbinstance` by setting the number of CPU cores to `4` and disabling multiple threads per core\. The changes are applied immediately by using `--apply-immediately`\. If you want to apply the changes during the next scheduled maintenance window, omit the `--apply-immediately` option\.  
For Linux, OS X, or Unix:  

```
aws rds modify-db-instance \
    --processor-features "Name=coreCount,Value=4" "Name=threadsPerCore,Value=1" \
    --apply-immediately
```
For Windows:  

```
aws rds modify-db-instance ^
    --processor-features "Name=coreCount,Value=4" "Name=threadsPerCore,Value=1" ^
    --apply-immediately
```

**Example Viewing the Valid Processor Values for a DB Instance Class**  
You can view the valid processor values for a particular DB instance class by running the [describe\-orderable\-db\-instance\-options](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-orderable-db-instance-options.html) command and specifying the instance class for the `--db-instance-class` option\. For example, the output for the following command shows the processor options for the db\.r3\.large instance class\.  

```
aws rds describe-orderable-db-instance-options --engine oracle-ee --db-instance-class db.r3.large           
```
Following is sample output for the command in JSON format\.  

```
    {
                "SupportsIops": true,
                "MaxIopsPerGib": 50.0,
                "LicenseModel": "bring-your-own-license",
                "DBInstanceClass": "db.r3.large",
                "SupportsIAMDatabaseAuthentication": false,
                "MinStorageSize": 100,
                "AvailabilityZones": [
                    {
                        "Name": "us-west-2a"
                    },
                    {
                        "Name": "us-west-2b"
                    },
                    {
                        "Name": "us-west-2c"
                    }
                ],
                "EngineVersion": "12.1.0.2.v2",
                "MaxStorageSize": 32768,
                "MinIopsPerGib": 1.0,
                "MaxIopsPerDbInstance": 40000,
                "ReadReplicaCapable": false,
                "AvailableProcessorFeatures": [
                    {
                        "Name": "coreCount",
                        "DefaultValue": "1",
                        "AllowedValues": "1"
                    },
                    {
                        "Name": "threadsPerCore",
                        "DefaultValue": "2",
                        "AllowedValues": "1,2"
                    }
                ],
                "SupportsEnhancedMonitoring": true,
                "SupportsPerformanceInsights": false,
                "MinIopsPerDbInstance": 1000,
                "StorageType": "io1",
                "Vpc": false,
                "SupportsStorageEncryption": true,
                "Engine": "oracle-ee",
                "MultiAZCapable": true
    }
```
In addition, you can run the following commands for DB instance class processor information:  
+ [describe\-db\-instances](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instances.html) – Shows the processor information for the specified DB instance\.
+ [describe\-db\-snapshots](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-snapshots.html) – Shows the processor information for the specified DB snapshot\.
+ [describe\-valid\-db\-instance\-modifications](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-valid-db-instance-modifications.html) – Shows the valid modifications to the processor for the specified DB instance\.

**Example Returning to Default Processor Settings for a DB Instance**  
The following example modifies `mydbinstance` by returning its DB instance class to the default processor values for it\. The changes are applied immediately by using `--apply-immediately`\. If you want to apply the changes during the next scheduled maintenance window, omit the `--apply-immediately` option\.   
For Linux, OS X, or Unix:  

```
aws rds modify-db-instance \
    --use-default-processor-features \
    --apply-immediately
```
For Windows:  

```
aws rds modify-db-instance ^
    --use-default-processor-features ^
    --apply-immediately
```

**Example Returning to the Default Number of CPU Cores for a DB Instance**  
The following example modifies `mydbinstance` by returning its DB instance class to the default number of CPU cores for it\. The threads per core setting isn't changed\. The changes are applied immediately by using `--apply-immediately`\. If you want to apply the changes during the next scheduled maintenance window, omit the `--apply-immediately` option\.   
For Linux, OS X, or Unix:  

```
aws rds modify-db-instance \
    --processor-features "Name=coreCount,Value=DEFAULT" \
    --apply-immediately
```
For Windows:  

```
aws rds modify-db-instance ^
    --processor-features "Name=coreCount,Value=DEFAULT" ^
    --apply-immediately
```

**Example Returning to the Default Number of Threads Per Core for a DB Instance**  
The following example modifies `mydbinstance` by returning its DB instance class to the default number of threads per core for it\. The number of CPU cores setting isn't changed\. The changes are applied immediately by using `--apply-immediately`\. If you want to apply the changes during the next scheduled maintenance window, omit the `--apply-immediately` option\.  
For Linux, OS X, or Unix:  

```
aws rds modify-db-instance \
    --processor-features "Name=threadsPerCore,Value=DEFAULT" \
    --apply-immediately
```
For Windows:  

```
aws rds modify-db-instance ^
    --processor-features "Name=threadsPerCore,Value=DEFAULT" ^
    --apply-immediately
```

#### API<a name="USER_ConfigureProcessor.API"></a>

You can set the processor features for a DB instance when you call one of the following Amazon RDS API actions:
+ [CreateDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html)
+ [ModifyDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html)
+ [RestoreDBInstanceFromDBSnapshot](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBInstanceFromDBSnapshot.html)
+ [RestoreDBInstanceFromS3](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBInstanceFromS3.html)
+ [RestoreDBInstanceToPointInTime](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBInstanceToPointInTime.html)

To configure the processor features of a DB instance class for a DB instance by using the Amazon RDS API, include the `ProcessFeatures` parameter in the call\. 

The parameter has the following syntax\.

```
ProcessFeatures "Name=coreCount,Value=<value>" "Name=threadsPerCore,Value=<value>"            
```

Specify the number of CPU cores with the `coreCount` feature name, and specify whether multiple threads per core are enabled with the `threadsPerCore` feature name\. 

You can view the valid processor values for a particular instance class by running the [DescribeOrderableDBInstanceOptions](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeOrderableDBInstanceOptions.html) action and specifying the instance class for the `DBInstanceClass` parameter\.

In addition, you can use the following actions for DB instance class processor information:
+ [DescribeDBInstances](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBInstances.html) – Shows the processor information for the specified DB instance\.
+ [DescribeDBSnapshots](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBSnapshots.html) – Shows the processor information for the specified DB snapshot\.
+ [DescribeValidDBInstanceModifications](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeValidDBInstanceModifications.html) – Shows the valid modifications to the processor for the specified DB instance\.