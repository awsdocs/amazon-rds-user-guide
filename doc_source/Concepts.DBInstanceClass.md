# DB Instance Class<a name="Concepts.DBInstanceClass"></a>

The DB instance class determines the computation and memory capacity of an Amazon RDS DB instance\. The DB instance class you need depends on your processing power and memory requirements\. 

For more information about instance class pricing, see [Amazon RDS Pricing](https://aws.amazon.com/rds/pricing/)\. 

## DB Instance Class Types<a name="Concepts.DBInstanceClass.Types"></a>

Amazon RDS supports three types of instance classes: Standard, Memory Optimized, and Burstable Performance\. For more information about Amazon EC2 instance types, see [Instance Type](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-types.html) in the Amazon EC2 documentation\. 

The following are the Standard DB instance classes available:
+ **db\.m4** – Latest\-generation instance classes that provide more computing capacity than the previous db\.m3 instance classes\. 
+ **db\.m3** – Previous\-generation instance classes that provide a balance of compute, memory, and network resources, and are a good choice for many applications\. The db\.m3 instance classes provide more computing capacity than the previous db\.m1 instance classes\. 
+ **db\.m1** – Previous\-generation general\-purpose instance classes\. 

The following are the Memory Optimized DB instance classes available:
+ **db\.x1e** – Latest\-generation instance classes optimized for memory\-intensive applications\. These offer one of the lowest price per GiB of RAM among the DB instance classes and up to 3,904 GiB of DRAM\-based instance memory\. The db\.x1e instance classes are available only in the following regions: US East \(N\. Virginia\), US West \(Oregon\), EU \(Ireland\), Asia Pacific \(Tokyo\), and Asia Pacific \(Sydney\)\. 
+ **db\.x1** – Current\-generation instance classes optimized for memory\-intensive applications\. These offer one of the lowest price per GiB of RAM among the DB instance classes and up to 1,952 GiB of DRAM\-based instance memory\. 
+ **db\.r4** – Current\-generation instance classes optimized for memory\-intensive applications\. These offer a better price per GiB of RAM than the db\.r3 instance classes\. 
+ **db\.r3** – Previous\-generation instance classes that provide memory optimization and more computing capacity than the db\.m2 instance classes\. The db\.r3 instances classes are not available in the South America \(São Paulo\) region\. 
+ **db\.m2** – Previous\-generation memory\-optimized instance classes\. 

The following are the Burstable Performance DB instance classes available:
+ **db\.t2** – Instance classes that provide a baseline performance level, with the ability to burst to full CPU usage\. 

## Specifications for All Available DB Instance Classes<a name="Concepts.DBInstanceClass.Summary"></a>

The following table provides details of the Amazon RDS DB instance classes\. The table columns are explained after the table\. 


****  

| Instance Class | vCPU1 | ECU2 | Memory3 \(GiB\) | VPC Only4 | EBS Optimized5 | Max\. Bandwidth6 \(Mbps\) | Network Performance7 | Aurora MySQL | Aurora PostgreSQL | MariaDB | Microsoft SQL Server8 | MySQL9 | Oracle10 | PostgreSQL | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| db\.m4 – Latest Generation Standard Instance Classes | 
| db\.m4\.16xlarge | 64 | 188 | 256 | Yes | Yes | 10,000 | 25 Gbps | No | No | Yes | Yes8 | MySQL 5\.7, 5\.69 | Yes10 | Yes | 
| db\.m4\.10xlarge | 40 | 124\.5 | 160 | Yes | Yes | 4,000 | 10 Gbps | No | No | Yes | Yes8 | Yes | Yes10 | Yes | 
| db\.m4\.4xlarge | 16 | 53\.5 | 64 | Yes | Yes | 2,000 | High | No | No | Yes | Yes8 | Yes | Yes10 | Yes | 
| db\.m4\.2xlarge | 8 | 25\.5 | 32 | Yes | Yes | 1,000 | High | No | No | Yes | Yes8 | Yes | Yes10 | Yes | 
| db\.m4\.xlarge | 4 | 13 | 16 | Yes | Yes | 750 | High | No | No | Yes | Yes8 | Yes | Yes10 | Yes | 
| db\.m4\.large | 2 | 6\.5 | 8 | Yes | Yes | 450 | Moderate | No | No | Yes | Yes8 | Yes | Yes10 | Yes | 
| db\.m3 – Previous Generation Standard Instance Classes | 
| db\.m3\.2xlarge | 8 | 26 | 30 | No | Yes | 1,000 | High | No | No | Yes | Yes8 | Yes | Yes10 | Yes | 
| db\.m3\.xlarge | 4 | 13 | 15 | No | Yes | 500 | High | No | No | Yes | Yes8 | Yes | Yes10 | Yes | 
| db\.m3\.large | 2 | 6\.5 | 7\.5 | No | No | — | Moderate | No | No | Yes | Yes8 | Yes | Yes10 | Yes | 
| db\.m3\.medium | 1 | 3 | 3\.75 | No | No | — | Moderate | No | No | Yes | Yes8 | Yes | Yes10 | Yes | 
| db\.m1 – Previous Generation Standard Instance Classes | 
| db\.m1\.xlarge | 4 | 4 | 15 | No | Yes | 450 | High | No | No | No | Yes8 | MySQL 5\.6, 5\.5 | Deprecated10 | PostgreSQL 9\.4, 9\.3 | 
| db\.m1\.large | 2 | 2 | 7\.5 | No | Yes | 450 | Moderate | No | No | No | Yes8 | MySQL 5\.6, 5\.5 | Deprecated10 | PostgreSQL 9\.4, 9\.3 | 
| db\.m1\.medium | 1 | 1 | 3\.75 | No | No | — | Moderate | No | No | No | Yes8 | MySQL 5\.6, 5\.5 | Deprecated10 | PostgreSQL 9\.4, 9\.3 | 
| db\.m1\.small | 1 | 1 | 1\.7 | No | No | — | Very Low | No | No | No | Yes8 | MySQL 5\.6, 5\.5 | Deprecated10 | PostgreSQL 9\.4, 9\.3 | 
| db\.x1e – Latest Generation Memory Optimized Instance Classes | 
| db\.x1e\.32xlarge | 128 | 340 | 3,904 | Yes | Yes | 14,000 | 25 Gbps | No | No | No | No | No | Yes10 | No | 
| db\.x1e\.16xlarge | 64 | 179 | 1,952 | Yes | Yes | 7,000 | 10 Gbps | No | No | No | No | No | Yes10 | No | 
| db\.x1e\.8xlarge | 32 | 91 | 976 | Yes | Yes | 3,500 | Up to 10 Gbps | No | No | No | No | No | Yes10 | No | 
| db\.x1e\.4xlarge | 16 | 47 | 488 | Yes | Yes | 1,750 | Up to 10 Gbps | No | No | No | No | No | Yes10 | No | 
| db\.x1e\.2xlarge | 8 | 23 | 244 | Yes | Yes | 1,000 | Up to 10 Gbps | No | No | No | No | No | Yes10 | No | 
| db\.x1e\.xlarge | 4 | 12 | 122 | Yes | Yes | 500 | Up to 10 Gbps | No | No | No | No | No | Yes10 | No | 
| db\.x1 – Current Generation Memory Optimized Instance Classes | 
| db\.x1\.32xlarge | 128 | 349 | 1,952 | Yes | Yes | 14,000 | 25 Gbps | No | No | No | No | No | Yes10 | No | 
| db\.x1\.16xlarge | 64 | 349 | 976 | Yes | Yes | 7,000 | 10 Gbps | No | No | No | No | No | Yes10 | No | 
| db\.r4 – Current Generation Memory Optimized Instance Classes | 
| db\.r4\.16xlarge | 64 | 195 | 488 | Yes | Yes | 14,000 | 25 Gbps | 1\.15 and later | Yes | Yes | Yes8 | MySQL 5\.7, 5\.69 | Yes10 | PostgreSQL 9\.6, 9\.5, 9\.4 | 
| db\.r4\.8xlarge | 32 | 99 | 244 | Yes | Yes | 7,000 | 10 Gbps | 1\.15 and later | Yes | Yes | Yes8 | MySQL 5\.7, 5\.69 | Yes10 | PostgreSQL 9\.6, 9\.5, 9\.4 | 
| db\.r4\.4xlarge | 16 | 53 | 122 | Yes | Yes | 3,500 | Up to 10 Gbps | 1\.15 and later | Yes | Yes | Yes8 | MySQL 5\.7, 5\.69 | Yes10 | PostgreSQL 9\.6, 9\.5, 9\.4 | 
| db\.r4\.2xlarge | 8 | 27 | 61 | Yes | Yes | 1,750 | Up to 10 Gbps | 1\.15 and later | Yes | Yes | Yes8 | MySQL 5\.7, 5\.69 | Yes10 | PostgreSQL 9\.6, 9\.5, 9\.4 | 
| db\.r4\.xlarge | 4 | 13\.5 | 30\.5 | Yes | Yes | 875 | Up to 10 Gbps | 1\.15 and later | Yes | Yes | Yes8 | MySQL 5\.7, 5\.69 | Yes10 | PostgreSQL 9\.6, 9\.5, 9\.4 | 
| db\.r4\.large | 2 | 7 | 15\.25 | Yes | Yes | 437 | Up to 10 Gbps | 1\.15 and later | Yes | Yes | Yes8 | MySQL 5\.7, 5\.69 | Yes10 | PostgreSQL 9\.6, 9\.5, 9\.4 | 
| db\.r3 – Previous Generation Memory Optimized Instance Classes | 
| db\.r3\.8xlarge | 32 | 104 | 244 | No | No | — | 10 Gbps | Yes | No | Yes | Yes8 | Yes | Yes10 | Yes | 
| db\.r3\.4xlarge | 16 | 52 | 122 | No | Yes | 2,000 | High | Yes | No | Yes | Yes8 | Yes | Yes10 | Yes | 
| db\.r3\.2xlarge | 8 | 26 | 61 | No | Yes | 1,000 | High | Yes | No | Yes | Yes8 | Yes | Yes10 | Yes | 
| db\.r3\.xlarge | 4 | 13 | 30\.5 | No | Yes | 500 | Moderate | Yes | No | Yes | Yes8 | Yes | Yes10 | Yes | 
| db\.r3\.large | 2 | 6\.5 | 15\.25 | No | No | — | Moderate | Yes | No | Yes | Yes8 | Yes | Yes10 | Yes | 
| db\.m2 – Previous Generation Memory Optimized Instance Classes | 
| db\.m2\.4xlarge | 8 | 26 | 68\.4 | No | Yes | 1,000 | High | No | No | No | Yes8 | MySQL 5\.6, 5\.5 | Deprecated10 | PostgreSQL 9\.4, 9\.3 | 
| db\.m2\.2xlarge | 4 | 13 | 34\.2 | No | Yes | 500 | Moderate | No | No | No | Yes8 | MySQL 5\.6, 5\.5 | Deprecated10 | PostgreSQL 9\.4, 9\.3 | 
| db\.m2\.xlarge | 2 | 6\.5 | 17\.1 | No | No | — | Moderate | No | No | No | Yes8 | MySQL 5\.6, 5\.5 | Deprecated10 | PostgreSQL 9\.4, 9\.3 | 
| db\.t2 – Current Generation Burstable Performance Instance Classes | 
| db\.t2\.2xlarge | 8 | 8 | 32 | Yes | No | — | Moderate | No | No | Yes | No | MySQL 5\.7, 5\.69 | Yes10 | PostgreSQL 9\.6, 9\.5, 9\.4 | 
| db\.t2\.xlarge | 4 | 4 | 16 | Yes | No | — | Moderate | No | No | Yes | No | MySQL 5\.7, 5\.69 | Yes10 | PostgreSQL 9\.6, 9\.5, 9\.4 | 
| db\.t2\.large | 2 | 2 | 8 | Yes | No | — | Moderate | No | No | Yes | Yes8 | Yes | Yes10 | Yes | 
| db\.t2\.medium | 2 | 2 | 4 | Yes | No | — | Moderate | Yes | No | Yes | Yes8 | Yes | Yes10 | Yes | 
| db\.t2\.small | 1 | 1 | 2 | Yes | No | — | Low | Yes | No | Yes | Yes8 | Yes | Yes10 | Yes | 
| db\.t2\.micro | 1 | 1 | 1 | Yes | No | — | Low | No | No | Yes | Yes8 | Yes | Yes10 | Yes | 

1. **vCPU** – The number of virtual central processing units \(CPUs\)\. A virtual CPU is a unit of capacity that you can use to compare DB instance classes\. Instead of purchasing or leasing a particular processor to use for several months or years, you are renting capacity by the hour\. Our goal is to provide a consistent amount of CPU capacity no matter what the actual underlying hardware\. 

1. **ECU** – The relative measure of the integer processing power of an Amazon EC2 instance\. To make it easy for developers to compare CPU capacity between different instance classes, we have defined an Amazon EC2 Compute Unit\. The amount of CPU that is allocated to a particular instance is expressed in terms of these EC2 Compute Units\. One ECU currently provides CPU capacity equivalent to a 1\.0–1\.2 GHz 2007 Opteron or 2007 Xeon processor\. 

1. **Memory \(GiB\)** – The RAM memory, in gibibytes, allocated to the DB instance\. There is often a consistent ratio between memory and vCPU\. For example, the db\.m1 instance class has the same memory to vCPU ratio as the db\.m3 instance class, but for most use cases the db\.m3 instance class provides better, more consistent performance, than the db\.m1 instance class\. 

1. **VPC Only** – The instance class is supported only for DB instances that are in an Amazon Virtual Private Cloud \(VPC\)\. If your current DB instance is not in a VPC, and you want to use an instance class that requires a VPC, first move your DB instance into a VPC\. For more information, see [Moving a DB Instance Not in a VPC into a VPC](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.Non-VPC2VPC)\. 

1. **EBS\-Optimized** – The DB instance uses an optimized configuration stack and provides additional, dedicated capacity for I/O\. This optimization provides the best performance by minimizing contention between I/O and other traffic from your instance\. For more information about Amazon EBS–optimized instances, see [Amazon EBS–Optimized Instances](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSOptimized.html) in the Amazon EC2 documentation\. 

1. **Max\. Bandwidth \(Mbps\)** – The maximum bandwidth in megabits per second\. Divide by 8 to get the expected throughput in megabytes per second\. 
**Important**  
For general purpose \(gp2\) storage, the maximum throughput is 1,280 Mbps \(160 MB/s\)\. For more information on estimating bandwidth for gp2 storage, see [General Purpose SSD Storage](CHAP_Storage.md#Concepts.Storage.GeneralSSD)

1. **Network Performance** – The network speed relative to other DB instance classes\. 

1. **Microsoft SQL Server** – Instance class support varies according to the version and edition of SQL Server\. For instance class support by version and edition, see [DB Instance Class Support for Microsoft SQL Server](CHAP_SQLServer.md#SQLServer.Concepts.General.InstanceClasses)\. 

1. **MySQL** – MySQL 5\.6\.27 does not support the following instance classes: m4\.16xlarge, db\.r4\.large, db\.r4\.xlarge, db\.r4\.2xlarge, db\.r4\.4xlarge, db\.r4\.8xlarge, db\.r4\.16xlarge t2\.xlarge, and t2\.2xlarge\. 

1. **Oracle** – Instance class support varies according to the version and edition of Oracle\. For instance class support by version and edition, see [DB Instance Class Support for Oracle](CHAP_Oracle.md#Oracle.Concepts.InstanceClasses)\. 

## Changing Your DB Instance Class<a name="Concepts.DBInstanceClass.Changing"></a>

You can change the CPU and memory available to a DB instance by changing its DB instance class\. To change the DB instance class, modify your DB instance by following the instructions for your specific database engine\. 
+ [Modifying a DB Instance Running the MariaDB Database Engine](USER_ModifyInstance.MariaDB.md)
+ [Modifying a DB Instance Running the Microsoft SQL Server Database Engine](USER_ModifyInstance.SQLServer.md)
+ [Modifying a DB Instance Running the MySQL Database Engine](USER_ModifyInstance.MySQL.md)
+ [Modifying a DB Instance Running the Oracle Database Engine](USER_ModifyInstance.Oracle.md)
+ [Modifying a DB Instance Running the PostgreSQL Database Engine](USER_ModifyPostgreSQLInstance.md)

MySQL DB instances created after April 23, 2014, can change to the db\.r3 instance class by modifying the DB instance just as with any other modification\. MySQL DB instances running MySQL versions 5\.5 and created before April 23, 2014, must first upgrade to MySQL version 5\.6\. For more information, see [Upgrading the MySQL DB Engine](USER_UpgradeDBInstance.MySQL.md)\. 

Some instance classes require that your DB instance is in a VPC\. If your current DB instance is not in a VPC, and you want to use an instance class that requires a VPC, first move your DB instance into a VPC\. For more information, see [Moving a DB Instance Not in a VPC into a VPC](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.Non-VPC2VPC)\. 

## Related Topics<a name="Concepts.DBInstanceClass.Related"></a>
+ [DB Instance RAM Recommendations](CHAP_BestPractices.md#CHAP_BestPractices.Performance.RAM)
+ [DB instance storage](CHAP_Storage.md)