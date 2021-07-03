# DB instance classes<a name="Concepts.DBInstanceClass"></a>

The DB instance class determines the computation and memory capacity of an Amazon RDS DB instance\. The DB instance class you need depends on your processing power and memory requirements\. 

For more information about instance class pricing, see [Amazon RDS pricing](https://aws.amazon.com/rds/pricing/)\. 

**Topics**
+ [DB instance class types](#Concepts.DBInstanceClass.Types)
+ [Supported DB engines for DB instance classes](#Concepts.DBInstanceClass.Support)
+ [Determining DB instance class support in AWS Regions](#Concepts.DBInstanceClass.RegionSupport)
+ [Changing your DB instance class](#Concepts.DBInstanceClass.Changing)
+ [Configuring the processor for a DB instance class](#USER_ConfigureProcessor)
+ [Hardware specifications for DB instance classes](#Concepts.DBInstanceClass.Summary)

## DB instance class types<a name="Concepts.DBInstanceClass.Types"></a>

Amazon RDS supports three types of instance classes: Standard, Memory Optimized, and Burstable Performance\. For more information about Amazon EC2 instance types, see [Instance types](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-types.html) in the Amazon EC2 documentation\. 

The following are the Standard DB instance classes available:
+ **db\.m6g** – General\-purpose instance classes powered by AWS Graviton2 processors\. These deliver balanced compute, memory, and networking for a broad range a general purpose workloads\.

  You can modify a DB instance to use one of the DB instance classes powered by AWS Graviton2 processors by completing the same steps as any other DB instance modification\.
+ **db\.m5d** – Newest generation instance classes that are optimized for low latency, very high random I/O performance, and high sequential read throughput\.
+ **db\.m5** – Latest generation general\-purpose instance classes that provide a balance of compute, memory, and network resources, and are a good choice for many applications\. The db\.m5 instance classes provide more computing capacity than the previous db\.m4 instance classes\. They are powered by the AWS Nitro System, a combination of dedicated hardware and lightweight hypervisor\. 
+ **db\.m4** – General\-purpose instance classes that provide more computing capacity than the previous db\.m3 instance classes\. 
+ **db\.m3** – General\-purpose instance classes that provide more computing capacity than the previous db\.m1 instance classes\. 
+ **db\.m1** – Earlier generation general\-purpose instance classes\. 

The following are the Memory Optimized DB instance classes available:
+ **db\.z1d** – Instance classes optimized for memory\-intensive applications\. These offer both high compute capacity and a high memory footprint\. High frequency z1d instances deliver a sustained all core frequency of up to 4\.0 GHz\.
+ **db\.x1e** – Instance classes optimized for memory\-intensive applications\. These offer one of the lowest price per gibibyte \(GiB\) of RAM among the DB instance classes and up to 3,904 GiB of DRAM\-based instance memory\.
+ **db\.x1** – Instance classes optimized for memory\-intensive applications\. These offer one of the lowest price per GiB of RAM among the DB instance classes and up to 1,952 GiB of DRAM\-based instance memory\. 
+ **db\.r6g** – Instance classes powered by AWS Graviton2 processors\. These are ideal for running memory\-intensive workloads in open\-source databases such as MySQL and PostgreSQL\.

  You can modify a DB instance to use one of the DB instance classes powered by AWS Graviton2 processors by completing the same steps as any other DB instance modification\.
+ **db\.r5b** – Instance classes that are memory\-optimized for throughput\-intensive applications\. Powered by the AWS Nitro System, db\.r5b instances deliver up to 60 Gbps bandwidth and 260,000 IOPS of EBS performance, which is the fastest block storage performance on EC2\.
+ **db\.r5d** – Instance classes that are optimized for low latency, very high random I/O performance, and high sequential read throughput\.
+ **db\.r5** – Latest generation instance classes optimized for memory\-intensive applications\. These offer improved networking and Amazon Elastic Block Store \(Amazon EBS\) performance\. They are powered by the AWS Nitro System, a combination of dedicated hardware and lightweight hypervisor\.
+ **db\.r4** – Instance classes optimized for memory\-intensive applications\. These offer improved networking and Amazon EBS performance\.
+ **db\.r3** – Instance classes that provide memory optimization\.
+ **db\.m2** – Earlier generation memory\-optimized instance classes\. 

The following are the Burstable Performance DB instance classes available:
+ **db\.t3** – Next generation instance classes that provide a baseline performance level, with the ability to burst to full CPU usage\. These instance classes provide more computing capacity than the previous db\.t2 instance classes\. They are powered by the AWS Nitro System, a combination of dedicated hardware and lightweight hypervisor\. 
+ **db\.t2** – Instance classes that provide a baseline performance level, with the ability to burst to full CPU usage\. 

**Note**  
The DB instance classes that use the AWS Nitro System \(db\.m5, db\.r5, db\.t3\) are throttled on combined read plus write workload\.

For DB instance class hardware specifications, see [Hardware specifications for DB instance classes ](#Concepts.DBInstanceClass.Summary)\.

## Supported DB engines for DB instance classes<a name="Concepts.DBInstanceClass.Support"></a>

The following are DB engine considerations for DB instance classes:

**MariaDB**  
The Graviton2 instance classes db\.m6g and db\.r6g are supported for all MariaDB 10\.5 versions and MariaDB version 10\.4\.13 and higher 10\.4 versions\.

**Microsoft SQL Server**  
Instance class support varies according to the version and edition of SQL Server\. For instance class support by version and edition, see [DB instance class support for Microsoft SQL Server](CHAP_SQLServer.md#SQLServer.Concepts.General.InstanceClasses)\. 

**MySQL**  
The Graviton2 instance classes db\.m6g and db\.r6g are supported for RDS for MySQL versions 8\.0\.17 and higher\.

**Oracle**  
Instance class support varies according to the Oracle Database version and edition\. RDS for Oracle supports additional memory\-optimized instance classes\. These classes have names of the form db\.r5\.*instance\_size*\.tpc*threads\_per\_core*\.mem*ratio*\. For the vCPU count and memory allocation for each optimized class, see [Supported Oracle DB instance classes](CHAP_Oracle.md#Oracle.Concepts.InstanceClasses.Supported)\.

**PostgreSQL**  
PostgreSQL versions 13 and higher support the db\.m6g, db\.m5, db\.r6g, db\.r5, db\.t3 instance classes\. Previous generations of classes are supported only by PostgreSQL versions lower than 13 and include db\.m4, db\.m3, db\.r4, db\.r3, and db\.t2\.

In the following table, you can find details about supported Amazon RDS DB instance classes for each Amazon RDS DB engine\.


****  

| Instance class | MariaDB | Microsoft SQL Server | MySQL | Oracle | PostgreSQL | 
| --- | --- | --- | --- | --- | --- | 
| db\.m6g – Standard instance classes powered by AWS Graviton2 processors | 
| db\.m6g\.16xlarge | All MariaDB 10\.5 versions and MariaDB version 10\.4\.13 & higher 10\.4 versions | No | MySQL 8\.0\.17 & higher | No | PostgreSQL 13,12\.3 & higher | 
| db\.m6g\.12xlarge | All MariaDB 10\.5 versions and MariaDB version 10\.4\.13 & higher 10\.4 versions | No | MySQL 8\.0\.17 & higher | No | PostgreSQL 13,12\.3 & higher | 
| db\.m6g\.8xlarge | All MariaDB 10\.5 versions and MariaDB version 10\.4\.13 & higher 10\.4 versions | No | MySQL 8\.0\.17 & higher | No | PostgreSQL 13,12\.3 & higher | 
| db\.m6g\.4xlarge | All MariaDB 10\.5 versions and MariaDB version 10\.4\.13 & higher 10\.4 versions | No | MySQL 8\.0\.17 & higher | No | PostgreSQL 13,12\.3 & higher | 
| db\.m6g\.2xlarge | All MariaDB 10\.5 versions and MariaDB version 10\.4\.13 & higher 10\.4 versions | No | MySQL 8\.0\.17 & higher | No | PostgreSQL 13,12\.3 & higher | 
| db\.m6g\.xlarge | All MariaDB 10\.5 versions and MariaDB version 10\.4\.13 & higher 10\.4 versions | No | MySQL 8\.0\.17 & higher | No | PostgreSQL 13,12\.3 & higher | 
| db\.m6g\.large | All MariaDB 10\.5 versions and MariaDB version 10\.4\.13 & higher 10\.4 versions | No | MySQL 8\.0\.17 & higher | No | PostgreSQL 13,12\.3 & higher | 
| db\.m5d – Newest generation standard instance classes | 
| db\.m5d\.24xlarge | No | Yes | No | No | No | 
| db\.m5d\.16xlarge | No | Yes | No | No | No | 
| db\.m5d\.12xlarge | No | Yes | No | No | No | 
| db\.m5d\.8xlarge | No | Yes | No | No | No | 
| db\.m5d\.4xlarge | No | Yes | No | No | No | 
| db\.m5d\.2xlarge | No | Yes | No | No | No | 
| db\.m5d\.xlarge | No | Yes | No | No | No | 
| db\.m5d\.large | No | Yes | No | No | No | 
| db\.m5 – Latest generation standard instance classes | 
| db\.m5\.24xlarge | Yes | Yes | Yes | Yes |  PostgreSQL 13, 12, 11, 10\.4 & higher, 9\.6\.9 & higher  | 
| db\.m5\.16xlarge | Yes | Yes | Yes | Yes |  PostgreSQL 13, 12, 11, 10\.4 & higher, 9\.6\.9 & higher  | 
| db\.m5\.12xlarge | Yes | Yes | Yes | Yes | PostgreSQL 13, 12, 11, 10\.4 & higher, 9\.6\.9 & higher | 
| db\.m5\.8xlarge | Yes | Yes | Yes | Yes | PostgreSQL 13, 12, 11, 10\.4 & higher, 9\.6\.9 & higher | 
| db\.m5\.4xlarge | Yes | Yes | Yes | Yes | PostgreSQL 13, 12, 11, 10\.4 & higher, 9\.6\.9 & higher | 
| db\.m5\.2xlarge | Yes | Yes | Yes | Yes | PostgreSQL 13, 12, 11, 10\.4 & higher, 9\.6\.9 & higher | 
| db\.m5\.xlarge | Yes | Yes | Yes | Yes | PostgreSQL 13, 12, 11, 10\.4 & higher, 9\.6\.9 & higher | 
| db\.m5\.large | Yes | Yes | Yes | Yes | PostgreSQL 13, 12, 11, 10\.4 & higher, 9\.6\.9 & higher | 
| db\.m4 – Standard instance classes | 
| db\.m4\.16xlarge | Yes | Yes | MySQL 8\.0, 5\.7, 5\.6 | Yes | Lower than PostgreSQL 13 | 
| db\.m4\.10xlarge | Yes | Yes | Yes | Yes | Lower than PostgreSQL 13 | 
| db\.m4\.4xlarge | Yes | Yes | Yes | Yes | Lower than PostgreSQL 13 | 
| db\.m4\.2xlarge | Yes | Yes | Yes | Yes | Lower than PostgreSQL 13 | 
| db\.m4\.xlarge | Yes | Yes | Yes | Yes | Lower than PostgreSQL 13 | 
| db\.m4\.large | Yes | Yes | Yes | Yes | Lower than PostgreSQL 13 | 
| db\.m3 – Standard instance classes | 
| db\.m3\.2xlarge | No | Yes | Yes | Deprecated | Lower than PostgreSQL 13 | 
| db\.m3\.xlarge | No | Yes | Yes | Deprecated | Lower than PostgreSQL 13 | 
| db\.m3\.large | No | Yes | Yes | Deprecated | Lower than PostgreSQL 13 | 
| db\.m3\.medium | No | Yes | Yes | Deprecated | Lower than PostgreSQL 13 | 
| db\.m1 – Standard instance classes | 
| db\.m1\.xlarge | No | Yes | Deprecated | Deprecated | Deprecated | 
| db\.m1\.large | No | Yes | Deprecated | Deprecated | Deprecated | 
| db\.m1\.medium | No | Yes | Deprecated | Deprecated | Deprecated | 
| db\.m1\.small | No | Yes | Deprecated | Deprecated | Deprecated | 
| db\.z1d – Memory\-optimized instance classes | 
| db\.z1d\.12xlarge | No | Yes | No | Yes | No | 
| db\.z1d\.6xlarge | No | Yes | No | Yes | No | 
| db\.z1d\.3xlarge | No | Yes | No | Yes | No | 
| db\.z1d\.2xlarge | No | Yes | No | Yes | No | 
| db\.z1d\.xlarge | No | Yes | No | Yes | No | 
| db\.z1d\.large | No | Yes | No | Yes | No | 
| db\.x1e – Memory\-optimized instance classes | 
| db\.x1e\.32xlarge | No | Yes | No | Yes | No | 
| db\.x1e\.16xlarge | No | Yes | No | Yes | No | 
| db\.x1e\.8xlarge | No | Yes | No | Yes | No | 
| db\.x1e\.4xlarge | No | Yes | No | Yes | No | 
| db\.x1e\.2xlarge | No | Yes | No | Yes | No | 
| db\.x1e\.xlarge | No | Yes | No | Yes | No | 
| db\.x1 – Memory\-optimized instance classes | 
| db\.x1\.32xlarge | No | Yes | No | Yes | No | 
| db\.x1\.16xlarge | No | Yes | No | Yes | No | 
| db\.r6g – Memory\-optimized instance classes powered by AWS Graviton2 processors | 
| db\.r6g\.16xlarge | All MariaDB 10\.5 versions and MariaDB version 10\.4\.13 & higher 10\.4 versions | No | MySQL 8\.0\.17 & higher | No | PostgreSQL 13, 12\.3 & higher | 
| db\.r6g\.12xlarge | All MariaDB 10\.5 versions and MariaDB version 10\.4\.13 & higher 10\.4 versions | No | MySQL 8\.0\.17 & higher | No | PostgreSQL 13, 12\.3 & higher | 
| db\.r6g\.8xlarge | All MariaDB 10\.5 versions and MariaDB version 10\.4\.13 & higher 10\.4 versions | No | MySQL 8\.0\.17 & higher | No | PostgreSQL 12\.3 & higher | 
| db\.r6g\.4xlarge | All MariaDB 10\.5 versions and MariaDB version 10\.4\.13 & higher 10\.4 versions | No | MySQL 8\.0\.17 & higher | No | PostgreSQL 13, 12\.3 & higher | 
| db\.r6g\.2xlarge | All MariaDB 10\.5 versions and MariaDB version 10\.4\.13 & higher 10\.4 versions | No | MySQL 8\.0\.17 & higher | No | PostgreSQL 13, 12\.3 & higher | 
| db\.r6g\.xlarge | All MariaDB 10\.5 versions and MariaDB version 10\.4\.13 & higher 10\.4 versions | No | MySQL 8\.0\.17 & higher | No | PostgreSQL 13, 12\.3 & higher | 
| db\.r6g\.large | All MariaDB 10\.5 versions and MariaDB version 10\.4\.13 & higher 10\.4 versions | No | MySQL 8\.0\.17 & higher | No | PostgreSQL 13, 12\.3 & higher | 
| db\.r5d – Newest Generation Memory Optimized Instance Classes | 
| db\.r5d\.24xlarge | No | Yes | No | No | No | 
| db\.r5d\.16xlarge | No | Yes | No |  No  | No | 
| db\.r5d\.12xlarge | No | Yes | No | No | No | 
| db\.r5d\.8xlarge | No | Yes | No |  No  | No | 
| db\.r5d\.4xlarge | No | Yes | No | No | No | 
| db\.r5d\.2xlarge | No | Yes | No | No | No | 
| db\.r5d\.xlarge | No | Yes | No | No | No | 
| db\.r5d\.large | No | Yes | No | No | No | 
| db\.r5b – Memory\-optimized instance classes | 
| db\.r5b\.24xlarge | No | Yes | No | Yes | No | 
| db\.r5b\.16xlarge | No | Yes | No |  Yes  | No | 
| db\.r5b\.12xlarge | No | Yes | No | Yes | No | 
| db\.r5b\.8xlarge | No | Yes | No |  Yes  | No | 
| db\.r5b\.4xlarge | No | Yes | No | Yes | No | 
| db\.r5b\.2xlarge | No | Yes | No | Yes | No | 
| db\.r5b\.xlarge | No | Yes | No | Yes | No | 
| db\.r5b\.large | No | Yes | No | Yes | No | 
| db\.r5 – Latest generation memory\-optimized instance classes preconfigured for high memory, storage, and I/O | 
| db\.r5\.12xlarge\.tpc2\.mem2x | No | No | No | Yes | No | 
| db\.r5\.8xlarge\.tpc2\.mem3x | No | No | No | Yes | No | 
| db\.r5\.6xlarge\.tpc2\.mem4x | No | No | No | Yes | No | 
| db\.r5\.4xlarge\.tpc2\.mem4x | No | No | No | Yes | No | 
| db\.r5\.4xlarge\.tpc2\.mem3x | No | No | No | Yes | No | 
| db\.r5\.4xlarge\.tpc2\.mem2x  | No | No | No | Yes | No | 
| db\.r5\.2xlarge\.tpc2\.mem8x | No | No | No | Yes | No | 
| db\.r5\.2xlarge\.tpc2\.mem4x | No | No | No | Yes | No | 
| db\.r5\.2xlarge\.tpc1\.mem2x | No | No | No | Yes | No | 
| db\.r5\.xlarge\.tpc2\.mem4x | No | No | No | Yes | No | 
| db\.r5\.xlarge\.tpc2\.mem2x | No | No | No | Yes | No | 
| db\.r5\.large\.tpc1\.mem2x | No | No | No | Yes | No | 
| db\.r5 – Latest generation memory\-optimized instance classes | 
| db\.r5\.24xlarge | Yes | Yes | Yes | Yes | PostgreSQL 13, 12, 11, 10\.4 & higher, 9\.6\.9 & higher | 
| db\.r5\.16xlarge | Yes | Yes | Yes | Yes | PostgreSQL 13, 12, 11, 10\.4 & higher, 9\.6\.9 & higher | 
| db\.r5\.12xlarge | Yes | Yes | Yes | Yes | PostgreSQL 13, 12, 11, 10\.4 & higher, 9\.6\.9 & higher | 
| db\.r5\.8xlarge | Yes | Yes | Yes |  Yes  | PostgreSQL 13, 12, 11, 10\.4 & higher, 9\.6\.9 & higher | 
| db\.r5\.4xlarge | Yes | Yes | Yes | Yes | PostgreSQL 13, 12, 11, 10\.4 & higher, 9\.6\.9 & higher | 
| db\.r5\.2xlarge | Yes | Yes | Yes | Yes | PostgreSQL 13, 12, 11, 10\.4 & higher, 9\.6\.9 & higher | 
| db\.r5\.xlarge | Yes | Yes | Yes | Yes | PostgreSQL 13, 12, 11, 10\.4 & higher, 9\.6\.9 & higher | 
| db\.r5\.large | Yes | Yes | Yes | Yes | PostgreSQL 13, 12, 11, 10\.4 & higher, 9\.6\.9 & higher | 
| db\.r4 – Memory\-optimized instance classes | 
| db\.r4\.16xlarge | Yes | Yes | MySQL 8\.0, 5\.7, 5\.6 | Yes | Lower than PostgreSQL 13 | 
| db\.r4\.8xlarge | Yes | Yes | MySQL 8\.0, 5\.7, 5\.6 | Yes | Lower than PostgreSQL 13 | 
| db\.r4\.4xlarge | Yes | Yes | MySQL 8\.0, 5\.7, 5\.6 | Yes | Lower than PostgreSQL 13 | 
| db\.r4\.2xlarge | Yes | Yes | MySQL 8\.0, 5\.7, 5\.6 | Yes | Lower than PostgreSQL 13 | 
| db\.r4\.xlarge | Yes | Yes | MySQL 8\.0, 5\.7, 5\.6 | Yes | Lower than PostgreSQL 13 | 
| db\.r4\.large | Yes | Yes | MySQL 8\.0, 5\.7, 5\.6 | Yes | Lower than PostgreSQL 13 | 
| db\.r3 – Memory\-optimized instance classes | 
| db\.r3\.8xlarge\*\* | Yes | Yes | Yes | Deprecated | Lower than PostgreSQL 13 | 
| db\.r3\.4xlarge | Yes | Yes | Yes | Deprecated | Lower than PostgreSQL 13 | 
| db\.r3\.2xlarge | Yes | Yes | Yes | Deprecated | Lower than PostgreSQL 13 | 
| db\.r3\.xlarge | Yes | Yes | Yes | Deprecated | Lower than PostgreSQL 13 | 
| db\.r3\.large | Yes | Yes | Yes | Deprecated | Lower than PostgreSQL 13 | 
| db\.m2 – Memory\-optimized instance classes | 
| db\.m2\.4xlarge | No | Yes | Deprecated | Deprecated | Deprecated | 
| db\.m2\.2xlarge | No | Yes | Deprecated | Deprecated | Deprecated | 
| db\.m2\.xlarge | No | Yes | Deprecated | Deprecated | Deprecated | 
| db\.t3 – Next generation burstable performance instance classes | 
| db\.t3\.2xlarge | Yes | Yes | Yes | Yes | PostgreSQL 13, 12, 11, 10, 9\.6\.9 & higher | 
| db\.t3\.xlarge | Yes | Yes | Yes | Yes | PostgreSQL 13, 12, 11, 10, 9\.6\.9 & higher | 
| db\.t3\.large | Yes | Yes | Yes | Yes | PostgreSQL 13, 12, 11, 10, 9\.6\.9 & higher | 
| db\.t3\.medium | Yes | Yes | Yes | Yes | PostgreSQL 13, 12, 11, 10, 9\.6\.9 & higher | 
| db\.t3\.small | Yes | Yes | Yes | Yes | PostgreSQL 13, 12, 11, 10, 9\.6\.9 & higher | 
| db\.t3\.micro | Yes | No | Yes | Yes | PostgreSQL 13, 12, 11, 10, 9\.6\.9 & higher | 
| db\.t2 – Burstable performance instance classes | 
| db\.t2\.2xlarge | Yes | No | MySQL 8\.0, 5\.7, 5\.6 | Deprecated | Lower than PostgreSQL 13 | 
| db\.t2\.xlarge | Yes | No | MySQL 8\.0, 5\.7, 5\.6 | Deprecated | Lower than PostgreSQL 13 | 
| db\.t2\.large | Yes | Yes | Yes | Deprecated | Lower than PostgreSQL 13 | 
| db\.t2\.medium | Yes | Yes | Yes | Deprecated | Lower than PostgreSQL 13 | 
| db\.t2\.small | Yes | Yes | Yes | Deprecated | Lower than PostgreSQL 13 | 
| db\.t2\.micro | Yes | Yes | Yes | Deprecated | Lower than PostgreSQL 13 | 

## Determining DB instance class support in AWS Regions<a name="Concepts.DBInstanceClass.RegionSupport"></a>

To determine the DB instance classes supported by each DB engine in a specific AWS Region, you can use the AWS Management Console, the [Amazon RDS Pricing](http://aws.amazon.com/rds/pricing/) page, or the [describe\-orderable\-db\-instance\-options](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-orderable-db-instance-options.html) command for the AWS Command Line Interface \(AWS CLI\)\.

**Note**  
When you perform operations with the AWS CLI, such as creating or modifying a DB instance, it automatically shows the supported DB instance classes for a specific DB engine, DB engine version, and AWS Region\.

**Contents**
+ [Using the Amazon RDS pricing page to determine DB instance class support in AWS Regions](#Concepts.DBInstanceClass.RegionSupport.PricingPage)
+ [Using the AWS CLI to determine DB instance class support in AWS Regions](#Concepts.DBInstanceClass.RegionSupport.CLI)
  + [Listing the DB instance classes that are supported by a specific DB engine version in an AWS Region](#Concepts.DBInstanceClass.RegionSupport.CLI.Example1)
  + [Listing the DB engine versions that support a specific DB instance class in an AWS Region](#Concepts.DBInstanceClass.RegionSupport.CLI.Example2)

### Using the Amazon RDS pricing page to determine DB instance class support in AWS Regions<a name="Concepts.DBInstanceClass.RegionSupport.PricingPage"></a>

You can use the [Amazon RDS Pricing](http://aws.amazon.com/rds/pricing/) page to determine the DB instance classes supported by each DB engine in a specific AWS Region\. 

**To use the pricing page to determine the DB instance classes supported by each engine in a Region**

1. Go to [Amazon RDS Pricing](http://aws.amazon.com/rds/pricing/)\.

1. Choose a DB engine\.

1. On the pricing page for the DB engine, choose **On\-Demand DB Instances** or **Reserved DB Instances**\.

1. To see the DB instance classes available in an AWS Region, choose the AWS Region in **Region**\.

   Other choices might be available for some DB engines, such as **Single\-AZ Deployment** or **Multi\-AZ Deployment**\.

### Using the AWS CLI to determine DB instance class support in AWS Regions<a name="Concepts.DBInstanceClass.RegionSupport.CLI"></a>

You can use the AWS CLI to determine which DB instance classes are supported for specific DB engines and DB engine versions in an AWS Region\. The following table shows the valid DB engine values\.


****  

| Engine names | Engine values in CLI commands | More information about versions | 
| --- | --- | --- | 
|  MariaDB  |  `mariadb`  |  [MariaDB on Amazon RDS versions](CHAP_MariaDB.md#MariaDB.Concepts.VersionMgmt)  | 
|  Microsoft SQL Server  |  `sqlserver-ee` `sqlserver-se` `sqlserver-ex` `sqlserver-web`  |  [Microsoft SQL Server versions on Amazon RDS](CHAP_SQLServer.md#SQLServer.Concepts.General.VersionSupport)  | 
|  MySQL  |  `mysql`  |  [MySQL on Amazon RDS versions](CHAP_MySQL.md#MySQL.Concepts.VersionMgmt)  | 
|  Oracle  |  `oracle-ee` `oracle-se2` `oracle-se`  |  [Oracle database engine release notes](Appendix.Oracle.PatchComposition.md)  | 
|  PostgreSQL  |  `postgres`  |  [Supported PostgreSQL database versions](CHAP_PostgreSQL.md#PostgreSQL.Concepts.General.DBVersions)  | 

For information about AWS Region names, see [AWS RegionsAvailability Zones](Concepts.RegionsAndAvailabilityZones.md#Concepts.RegionsAndAvailabilityZones.Regions)\.

The following examples demonstrate how to determine DB instance class support in an AWS Region using the [describe\-orderable\-db\-instance\-options](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-orderable-db-instance-options.html) AWS CLI command\.

**Note**  
To limit the output, these examples show results only for the General Purpose SSD \(`gp2`\) storage type\. If necessary, you can change the storage type to Provisioned IOPS \(`io1`\) or magnetic \(`standard`\) in the commands\.

**Topics**
+ [Listing the DB instance classes that are supported by a specific DB engine version in an AWS Region](#Concepts.DBInstanceClass.RegionSupport.CLI.Example1)
+ [Listing the DB engine versions that support a specific DB instance class in an AWS Region](#Concepts.DBInstanceClass.RegionSupport.CLI.Example2)

#### Listing the DB instance classes that are supported by a specific DB engine version in an AWS Region<a name="Concepts.DBInstanceClass.RegionSupport.CLI.Example1"></a>

To list the DB instance classes that are supported by a specific DB engine version in an AWS Region, run the following command\.

For Linux, macOS, or Unix:

```
aws rds describe-orderable-db-instance-options --engine engine --engine-version version \
    --query "*[].{DBInstanceClass:DBInstanceClass,StorageType:StorageType}|[?StorageType=='gp2']|[].{DBInstanceClass:DBInstanceClass}" \
    --output text \
    --region region
```

For Windows:

```
aws rds describe-orderable-db-instance-options --engine engine --engine-version version ^
    --query "*[].{DBInstanceClass:DBInstanceClass,StorageType:StorageType}|[?StorageType=='gp2']|[].{DBInstanceClass:DBInstanceClass}" ^
    --output text ^
    --region region
```

For example, the following command lists the supported DB instance classes for version 12\.4 of the RDS for PostgreSQL DB engine in US East \(N\. Virginia\)\.

For Linux, macOS, or Unix:

```
aws rds describe-orderable-db-instance-options --engine postgres --engine-version 12.4 \
    --query "*[].{DBInstanceClass:DBInstanceClass,StorageType:StorageType}|[?StorageType=='gp2']|[].{DBInstanceClass:DBInstanceClass}" \
    --output text \
    --region us-east-1
```

For Windows:

```
aws rds describe-orderable-db-instance-options --engine postgres --engine-version 12.4 ^
    --query "*[].{DBInstanceClass:DBInstanceClass,StorageType:StorageType}|[?StorageType=='gp2']|[].{DBInstanceClass:DBInstanceClass}" ^
    --output text ^
    --region us-east-1
```

#### Listing the DB engine versions that support a specific DB instance class in an AWS Region<a name="Concepts.DBInstanceClass.RegionSupport.CLI.Example2"></a>

To list the DB engine versions that support a specific DB instance class in an AWS Region, run the following command\.

For Linux, macOS, or Unix:

```
aws rds describe-orderable-db-instance-options --engine engine --db-instance-class DB_instance_class \
    --query "*[].{EngineVersion:EngineVersion,StorageType:StorageType}|[?StorageType=='gp2']|[].{EngineVersion:EngineVersion}" \
    --output text \
    --region region
```

For Windows:

```
aws rds describe-orderable-db-instance-options --engine engine --db-instance-class DB_instance_class ^
    --query "*[].{EngineVersion:EngineVersion,StorageType:StorageType}|[?StorageType=='gp2']|[].{EngineVersion:EngineVersion}" ^
    --output text ^
    --region region
```

For example, the following command lists the DB engine versions of the RDS for PostgreSQL DB engine that support the db\.r5\.large DB instance class in US East \(N\. Virginia\)\.

For Linux, macOS, or Unix:

```
aws rds describe-orderable-db-instance-options --engine postgres --db-instance-class db.r5.large \
    --query "*[].{EngineVersion:EngineVersion,StorageType:StorageType}|[?StorageType=='gp2']|[].{EngineVersion:EngineVersion}" \
    --output text \
    --region us-east-1
```

For Windows:

```
aws rds describe-orderable-db-instance-options --engine postgres --db-instance-class db.r5.large ^
    --query "*[].{EngineVersion:EngineVersion,StorageType:StorageType}|[?StorageType=='gp2']|[].{EngineVersion:EngineVersion}" ^
    --output text ^
    --region us-east-1
```

## Changing your DB instance class<a name="Concepts.DBInstanceClass.Changing"></a>

You can change the CPU and memory available to a DB instance by changing its DB instance class\. To change the DB instance class, modify your DB instance by following the instructions in [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\. 

Some instance classes require that your DB instance is in a VPC\. If your current DB instance isn't in a VPC, and you want to use an instance class that requires one, first move your DB instance into a VPC\. For more information, see [Moving a DB instance not in a VPC into a VPC](USER_VPC.md#USER_VPC.Non-VPC2VPC)\. 

## Configuring the processor for a DB instance class<a name="USER_ConfigureProcessor"></a>

Amazon RDS DB instance classes support Intel Hyper\-Threading Technology, which enables multiple threads to run concurrently on a single Intel Xeon CPU core\. Each thread is represented as a virtual CPU \(vCPU\) on the DB instance\. A DB instance has a default number of CPU cores, which varies according to DB instance class\. For example, a db\.m4\.xlarge DB instance class has two CPU cores and two threads per core by default—four vCPUs in total\.

**Note**  
Each vCPU is a hyperthread of an Intel Xeon CPU core\.

**Topics**
+ [Overview of configuring the processor](#USER_ConfigureProcessor.Overview)
+ [DB instance classes that support processor configuration](#USER_ConfigureProcessor.CPUOptionsDBInstanceClass)
+ [Setting the CPU cores and threads per CPU core for a DB instance class](#USER_ConfigureProcessor.SettingCPUOptions)

### Overview of configuring the processor<a name="USER_ConfigureProcessor.Overview"></a>

In most cases, you can find a DB instance class that has a combination of memory and number of vCPUs to suit your workloads\. However, you can also specify the following processor features to optimize your DB instance for specific workloads or business needs:
+ **Number of CPU cores** – You can customize the number of CPU cores for the DB instance\. You might do this to potentially optimize the licensing costs of your software with a DB instance that has sufficient amounts of RAM for memory\-intensive workloads but fewer CPU cores\.
+ **Threads per core** – You can disable Intel Hyper\-Threading Technology by specifying a single thread per CPU core\. You might do this for certain workloads, such as high\-performance computing \(HPC\) workloads\.

You can control the number of CPU cores and threads for each core separately\. You can set one or both in a request\. After a setting is associated with a DB instance, the setting persists until you change it\.

The processor settings for a DB instance are associated with snapshots of the DB instance\. When a snapshot is restored, its restored DB instance uses the processor feature settings used when the snapshot was taken\.

If you modify the DB instance class for a DB instance with nondefault processor settings, either specify default processor settings or explicitly specify processor settings at modification\. This requirement ensures that you are aware of the third\-party licensing costs that might be incurred when you modify the DB instance\.

There is no additional or reduced charge for specifying processor features on an Amazon RDS DB instance\. You're charged the same as for DB instances that are launched with default CPU configurations\.

### DB instance classes that support processor configuration<a name="USER_ConfigureProcessor.CPUOptionsDBInstanceClass"></a>

You can configure the number of CPU cores and threads per core only when the following conditions are met:
+ You're configuring an Oracle DB instance\. For information about the DB instance classes supported by different Oracle database editions, see [RDS for Oracle instance classes](CHAP_Oracle.md#Oracle.Concepts.InstanceClasses)\.
+ Your instance is using the Bring Your Own License \(BYOL\) licensing option\. For more information about Oracle licensing options, see [Oracle licensing options](CHAP_Oracle.md#Oracle.Concepts.Licensing)\.
+ Your instance isn't one of the db\.r5 instance classes that have predefined processor configurations\. These instance classes have names of the form db\.r5\.*instance\_size*\.tpc*threads\_per\_core*\.mem*ratio*\. For example, db\.r5\.xlarge\.tpc2\.mem4x is preconfigured with 2 threads per core \(tpc2\) and 4x as much memory as the standard db\.r5\.xlarge instance class\. You can't configure the processor features of these optimized instance classes\. For more information, see [Supported Oracle DB instance classes](CHAP_Oracle.md#Oracle.Concepts.InstanceClasses.Supported)\.

In the following table, you can find the DB instance classes that support setting a number of CPU cores and CPU threads per core\. You can also find the default value and the valid values for the number of CPU cores and CPU threads per core for each DB instance class\.


****  

| DB instance class | Default vCPUs | Default CPU cores | Default threads per core | Valid number of CPU cores | Valid number of threads per core | 
| --- | --- | --- | --- | --- | --- | 
|  db\.m5\.large  |  2  |  1  |  2  |  1  |  1, 2  | 
|  db\.m5\.xlarge  |  4  |  2  |  2  |  2  |  1, 2  | 
|  db\.m5\.2xlarge  |  8  |  4  |  2  |  2, 4  |  1, 2  | 
|  db\.m5\.4xlarge  |  16  |  8  |  2  |  2, 4, 6, 8  |  1, 2  | 
|  db\.m5\.8xlarge  |  32  |  16  |  2  |  2, 4, 6, 8, 10, 12, 14, 16  |  1, 2  | 
|  db\.m5\.12xlarge  |  48  |  24  |  2  |  2, 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24  |  1, 2  | 
|  db\.m5\.16xlarge  |  64  |  32  |  2  |  2, 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24, 26, 28, 30, 32  |  1, 2  | 
|  db\.m5\.24xlarge  |  96  |  48  |  2  |  4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24, 26, 28, 30, 32, 34, 36, 38, 40, 42, 44, 46, 48  |  1, 2  | 
|  db\.m5d\.large  |  2  |  1  |  2  |  1  |  1, 2  | 
|  db\.m5d\.xlarge  |  4  |  2  |  2  |  2  |  1, 2  | 
|  db\.m5d\.2xlarge  |  8  |  4  |  2  |  2, 4  |  1, 2  | 
|  db\.m5d\.4xlarge  |  16  |  8  |  2  |  2, 4, 6, 8  |  1, 2  | 
|  db\.m5d\.8xlarge  |  32  |  16  |  2  |  2, 4, 6, 8, 10, 12, 14, 16  |  1, 2  | 
|  db\.m5d\.12xlarge  |  48  |  24  |  2  |  2, 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24  |  1, 2  | 
|  db\.m5d\.16xlarge  |  64  |  32  |  2  |  2, 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24, 26, 28, 30, 32  |  1, 2  | 
|  db\.m5d\.24xlarge  |  96  |  48  |  2  |  4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24, 26, 28, 30, 32, 34, 36, 38, 40, 42, 44, 46, 48  |  1, 2  | 
|  db\.m4\.10xlarge  |  40  |  20  |  2  |  2, 4, 6, 8, 10, 12, 14, 16, 18, 20  |  1, 2  | 
|  db\.m4\.16xlarge  |  64  |  32  |  2  |  2, 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24, 26, 28, 30, 32  |  1, 2  | 
|  db\.r3\.large  |  2  |  1  |  2  |  1  |  1, 2  | 
|  db\.r3\.xlarge  |  4  |  2  |  2  |  1, 2  |  1, 2  | 
|  db\.r3\.2xlarge  |  8  |  4  |  2  |  1, 2, 3, 4  |  1, 2  | 
|  db\.r3\.4xlarge  |  16  |  8  |  2  |  1, 2, 3, 4, 5, 6, 7, 8  |  1, 2  | 
|  db\.r3\.8xlarge  |  32  |  16  |  2  |  2, 4, 6, 8, 10, 12, 14, 16  |  1, 2  | 
|  db\.r5\.large  |  2  |  1  |  2  |  1  |  1, 2  | 
|  db\.r5\.xlarge  |  4  |  2  |  2  |  2  |  1, 2  | 
|  db\.r5\.2xlarge  |  8  |  4  |  2  |  2, 4  |  1, 2  | 
|  db\.r5\.4xlarge  |  16  |  8  |  2  |  2, 4, 6, 8  |  1, 2  | 
|  db\.r5\.8xlarge  |  32  |  16  |  2  |  2, 4, 6, 8, 10, 12, 14, 16  |  1, 2  | 
|  db\.r5\.12xlarge  |  48  |  24  |  2  |  2, 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24  |  1, 2  | 
|  db\.r5\.16xlarge  |  64  |  32  |  2  |  2, 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24, 26, 28, 30, 32  |  1, 2  | 
|  db\.r5\.24xlarge  |  96  |  48  |  2  |  4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24, 26, 28, 30, 32, 34, 36, 38, 40, 42, 44, 46, 48  |  1, 2  | 
|  db\.r5b\.large  |  2  |  1  |  2  |  1  |  1, 2  | 
|  db\.r5b\.xlarge  |  4  |  2  |  2  |  2  |  1, 2  | 
|  db\.r5b\.2xlarge  |  8  |  4  |  2  |  2, 4  |  1, 2  | 
|  db\.r5b\.4xlarge  |  16  |  8  |  2  |  2, 4, 6, 8  |  1, 2  | 
|  db\.r5b\.8xlarge  |  32  |  16  |  2  |  2, 4, 6, 8, 10, 12, 14, 16  |  1, 2  | 
|  db\.r5b\.12xlarge  |  48  |  24  |  2  |  2, 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24  |  1, 2  | 
|  db\.r5b\.16xlarge  |  64  |  32  |  2  |  2, 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24, 26, 28, 30, 32  |  1, 2  | 
|  db\.r5b\.24xlarge  |  96  |  48  |  2  |  4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24, 26, 28, 30, 32, 34, 36, 38, 40, 42, 44, 46, 48  |  1, 2  | 
|  db\.r5d\.large  |  2  |  1  |  2  |  1  |  1, 2  | 
|  db\.r5d\.xlarge  |  4  |  2  |  2  |  2  |  1, 2  | 
|  db\.r5d\.2xlarge  |  8  |  4  |  2  |  2, 4  |  1, 2  | 
|  db\.r5d\.4xlarge  |  16  |  8  |  2  |  2, 4, 6, 8  |  1, 2  | 
|  db\.r5d\.8xlarge  |  32  |  16  |  2  |  2, 4, 6, 8, 10, 12, 14, 16  |  1, 2  | 
|  db\.r5d\.12xlarge  |  48  |  24  |  2  |  2, 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24  |  1, 2  | 
|  db\.r5d\.16xlarge  |  64  |  32  |  2  |  2, 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24, 26, 28, 30, 32  |  1, 2  | 
|  db\.r5d\.24xlarge  |  96  |  48  |  2  |  4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24, 26, 28, 30, 32, 34, 36, 38, 40, 42, 44, 46, 48  |  1, 2  | 
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
|  db\.z1d\.large  |  2  |  1  |  2  |  1  |  1, 2  | 
|  db\.z1d\.xlarge  |  4  |  2  |  2  |  2  |  1, 2  | 
|  db\.z1d\.2xlarge  |  8  |  4  |  2  |  2, 4  |  1, 2  | 
|  db\.z1d\.3xlarge  |  12  |  6  |  2  |  2, 4, 6  |  1, 2  | 
|  db\.z1d\.6xlarge  |  24  |  12  |  2  |  2, 4, 6, 8, 10, 12  |  1, 2  | 
|  db\.z1d\.12xlarge  |  48  |  24  |  2  |  4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24  |  1, 2  | 

**Note**  
You can use AWS CloudTrail to monitor and audit changes to the process configuration of Amazon RDS for Oracle DB instances\. For more information about using CloudTrail, see [Working with AWS CloudTrail and Amazon RDS](logging-using-cloudtrail.md)\.

### Setting the CPU cores and threads per CPU core for a DB instance class<a name="USER_ConfigureProcessor.SettingCPUOptions"></a>

You can configure the number of CPU cores and threads per core for the DB instance class when you perform the following operations:
+ [Creating an Amazon RDS DB instance](USER_CreateDBInstance.md)
+ [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)
+ [Restoring from a DB snapshot](USER_RestoreFromSnapshot.md)
+ [Restoring a DB instance to a specified time](USER_PIT.md)

**Note**  
When you modify a DB instance to configure the number of CPU cores or threads per core, there is a brief DB instance outage\.

You can set the CPU cores and the threads per CPU core for a DB instance class using the AWS Management Console, the AWS CLI, or the RDS API\.

#### Console<a name="USER_ConfigureProcessor.Console"></a>

When you are creating, modifying, or restoring a DB instance, you set the DB instance class in the AWS Management Console\. The **Instance specifications** section shows options for the processor\. The following image shows the processor features options\.

![\[Configure processor options\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/vcpu-config.png)

Set the following options to the appropriate values for your DB instance class under **Processor features**:
+ **Core count – **Set the number of CPU cores using this option\. The value must be equal to or less than the maximum number of CPU cores for the DB instance class\.
+ **Threads per core** – Specify **2** to enable multiple threads per core, or specify **1** to disable multiple threads per core\.

When you modify or restore a DB instance, you can also set the CPU cores and the threads per CPU core to the defaults for the instance class\.

When you view the details for a DB instance in the console, you can view the processor information for its DB instance class on the **Configuration** tab\. The following image shows a DB instance class with one CPU core and multiple threads per core enabled\.

![\[View processor options\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/vcpu-view.png)

For Oracle DB instances, the processor information only appears for Bring Your Own License \(BYOL\) DB instances\.

#### AWS CLI<a name="USER_ConfigureProcessor.CLI"></a>

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

The following are examples that configure the processor:

**Topics**
+ [Setting the number of CPU cores for a DB instance](#USER_ConfigureProcessor.CLI.Example1)
+ [Setting the number of CPU cores and disabling multiple threads for a DB instance](#USER_ConfigureProcessor.CLI.Example2)
+ [Viewing the valid processor values for a DB instance class](#USER_ConfigureProcessor.CLI.Example3)
+ [Returning to default processor settings for a DB instance](#USER_ConfigureProcessor.CLI.Example4)
+ [Returning to the default number of CPU cores for a DB instance](#USER_ConfigureProcessor.CLI.Example5)
+ [Returning to the default number of threads per core for a DB instance](#USER_ConfigureProcessor.CLI.Example6)

##### Setting the number of CPU cores for a DB instance<a name="USER_ConfigureProcessor.CLI.Example1"></a>

**Example**  
The following example modifies `mydbinstance` by setting the number of CPU cores to 4\. The changes are applied immediately by using `--apply-immediately`\. If you want to apply the changes during the next scheduled maintenance window, omit the `--apply-immediately` option\.   
For Linux, macOS, or Unix:  

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

##### Setting the number of CPU cores and disabling multiple threads for a DB instance<a name="USER_ConfigureProcessor.CLI.Example2"></a>

**Example**  
The following example modifies `mydbinstance` by setting the number of CPU cores to `4` and disabling multiple threads per core\. The changes are applied immediately by using `--apply-immediately`\. If you want to apply the changes during the next scheduled maintenance window, omit the `--apply-immediately` option\.  
For Linux, macOS, or Unix:  

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

##### Viewing the valid processor values for a DB instance class<a name="USER_ConfigureProcessor.CLI.Example3"></a>

**Example**  
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
In the output of the preceding commands, the values for the processor features are not null only if the following conditions are met:  
+ You are using an Oracle DB instance\.
+ Your Oracle DB instance supports changing processor values\.
+ The current CPU core and thread settings are set to nondefault values\.
If the preceding conditions aren't met, you can get the instance type using [describe\-db\-instances](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instances.html)\. You can get the processor information for this instance type by running the EC2 operation [describe\-instance\-types](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instance-types.html)\.

##### Returning to default processor settings for a DB instance<a name="USER_ConfigureProcessor.CLI.Example4"></a>

**Example**  
The following example modifies `mydbinstance` by returning its DB instance class to the default processor values for it\. The changes are applied immediately by using `--apply-immediately`\. If you want to apply the changes during the next scheduled maintenance window, omit the `--apply-immediately` option\.   
For Linux, macOS, or Unix:  

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

##### Returning to the default number of CPU cores for a DB instance<a name="USER_ConfigureProcessor.CLI.Example5"></a>

**Example**  
The following example modifies `mydbinstance` by returning its DB instance class to the default number of CPU cores for it\. The threads per core setting isn't changed\. The changes are applied immediately by using `--apply-immediately`\. If you want to apply the changes during the next scheduled maintenance window, omit the `--apply-immediately` option\.   
For Linux, macOS, or Unix:  

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

##### Returning to the default number of threads per core for a DB instance<a name="USER_ConfigureProcessor.CLI.Example6"></a>

**Example**  
The following example modifies `mydbinstance` by returning its DB instance class to the default number of threads per core for it\. The number of CPU cores setting isn't changed\. The changes are applied immediately by using `--apply-immediately`\. If you want to apply the changes during the next scheduled maintenance window, omit the `--apply-immediately` option\.  
For Linux, macOS, or Unix:  

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

#### RDS API<a name="USER_ConfigureProcessor.API"></a>

You can set the processor features for a DB instance when you call one of the following Amazon RDS API operations:
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

You can view the valid processor values for a particular instance class by running the [DescribeOrderableDBInstanceOptions](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeOrderableDBInstanceOptions.html) operation and specifying the instance class for the `DBInstanceClass` parameter\. You can also use the following operations:
+ [DescribeDBInstances](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBInstances.html) – Shows the processor information for the specified DB instance\.
+ [DescribeDBSnapshots](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBSnapshots.html) – Shows the processor information for the specified DB snapshot\.
+ [DescribeValidDBInstanceModifications](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeValidDBInstanceModifications.html) – Shows the valid modifications to the processor for the specified DB instance\.

In the output of the preceding operations, the values for the processor features are not null only if the following conditions are met:
+ You are using an Oracle DB instance\.
+ Your Oracle DB instance supports changing processor values\.
+ The current CPU core and thread settings are set to nondefault values\.

If the preceding conditions aren't met, you can get the instance type using [DescribeDBInstances](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBInstances.html)\. You can get the processor information for this instance type by running the EC2 operation [DescribeInstanceTypes](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeInstanceTypes.html)\.

## Hardware specifications for DB instance classes<a name="Concepts.DBInstanceClass.Summary"></a>

The following terminology is used to describe hardware specifications for DB instance classes:

**vCPU**  
The number of virtual central processing units \(CPUs\)\. A *virtual CPU *is a unit of capacity that you can use to compare DB instance classes\. Instead of purchasing or leasing a particular processor to use for several months or years, you are renting capacity by the hour\. Our goal is to make a consistent and specific amount of CPU capacity available, within the limits of the actual underlying hardware\.

**ECU**  
The relative measure of the integer processing power of an Amazon EC2 instance\. To make it easy for developers to compare CPU capacity between different instance classes, we have defined an Amazon EC2 Compute Unit\. The amount of CPU that is allocated to a particular instance is expressed in terms of these EC2 Compute Units\. One ECU currently provides CPU capacity equivalent to a 1\.0–1\.2 GHz 2007 Opteron or 2007 Xeon processor\.

**Memory \(GiB\)**  
The RAM, in gibibytes, allocated to the DB instance\. There is often a consistent ratio between memory and vCPU\. As an example, take the db\.r4 instance class, which has a memory to vCPU ratio similar to the db\.r5 instance class\. However, for most use cases the db\.r5 instance class provides better, more consistent performance than the db\.r4 instance class\. 

**VPC Only**  
The instance class is supported only for DB instances that are in a VPC based on the Amazon VPC service\. In some cases, you might want to use an instance class that requires a VPC but your current DB instance isn't in a VPC\. In these cases, start by moving your DB instance into a VPC\. For more information, see [Moving a DB instance not in a VPC into a VPC](USER_VPC.md#USER_VPC.Non-VPC2VPC)\. 

**EBS\-Optimized**  
The DB instance uses an optimized configuration stack and provides additional, dedicated capacity for I/O\. This optimization provides the best performance by minimizing contention between I/O and other traffic from your instance\. For more information about Amazon EBS–optimized instances, see [Amazon EBS–Optimized instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSOptimized.html) in the *Amazon EC2 User Guide for Linux Instances\.* 

**Max\. Bandwidth \(Mbps\)**  
The maximum bandwidth in megabits per second\. Divide by 8 to get the expected throughput in megabytes per second\.   
General Purpose SSD \(gp2\) volumes for Amazon RDS DB instances have a throughput limit of 250 MiB/s in most cases\. However, the throughput limit can vary depending on volume size\. For more information, see [Amazon EBS volume types](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSVolumeTypes.html) in the *Amazon EC2 User Guide for Linux Instances\.* For information on estimating bandwidth for gp2 storage, see [General Purpose SSD storage](CHAP_Storage.md#Concepts.Storage.GeneralSSD)\.

**Network Performance**  
The network speed relative to other DB instance classes\.

In the following table, you can find hardware details about the Amazon RDS DB instance classes\. 

For information about Amazon RDS DB engine support for each DB instance class, see [Supported DB engines for DB instance classes](#Concepts.DBInstanceClass.Support)\. 


****  

| Instance class | vCPU | ECU | Memory \(GiB\) | VPC only | EBS optimized | Max\. bandwidth \(mbps\) | Network performance | 
| --- | --- | --- | --- | --- | --- | --- | --- | 
| db\.m6g – Standard instance classes powered by AWS Graviton2 processors | 
| db\.m6g\.16xlarge | 64 | – | 256 | Yes | Yes | 19,000 | 25 Gbps | 
| db\.m6g\.12xlarge | 48 | – | 192 | Yes | Yes | 13,500 | 20 Gbps | 
| db\.m6g\.8xlarge | 32 | – | 128 | Yes | Yes | 9,500 | 12 Gbps | 
| db\.m6g\.4xlarge | 16 | – | 64 | Yes | Yes | 6,800 | Up to 10 Gbps | 
| db\.m6g\.2xlarge\* | 8 | – | 32 | Yes | Yes | Up to 4,750 | Up to 10 Gbps | 
| db\.m6g\.xlarge\* | 4 | – | 16 | Yes | Yes | Up to 4,750 | Up to 10 Gbps | 
| db\.m6g\.large\* | 2 | – | 8 | Yes | Yes | Up to 4,750 | Up to 10 Gbps | 
| db\.m5d – Latest generation standard instance classes | 
| db\.m5d\.24xlarge | 96 | 345 | 384 | Yes | Yes | 19,000 | 25 Gbps | 
| db\.m5d\.16xlarge | 64 | 262 | 256 | Yes | Yes | 13,600 | 20 Gbps | 
| db\.m5d\.12xlarge | 48 | 173 | 192 | Yes | Yes | 9,500 | 10 Gbps | 
| db\.m5d\.8xlarge | 32 | 131 | 128 | Yes | Yes | 6,800 | 10 Gbps | 
| db\.m5d\.4xlarge | 16 | 61 | 64 | Yes | Yes | 4,750 | Up to 10 Gbps | 
| db\.m5d\.2xlarge\* | 8 | 31 | 32 | Yes | Yes | Up to 4,750 | Up to 10 Gbps | 
| db\.m5d\.xlarge\* | 4 | 15 | 16 | Yes | Yes | Up to 4,750 | Up to 10 Gbps | 
| db\.m5d\.large\* | 2 | 10 | 8 | Yes | Yes | Up to 4,750 | Up to 10 Gbps | 
| db\.m5 – Latest generation standard instance classes | 
| db\.m5\.24xlarge | 96 | 345 | 384 | Yes | Yes | 19,000 | 25 Gbps | 
| db\.m5\.16xlarge | 64 | 262 | 256 | Yes | Yes | 13,600 | 20 Gbps | 
| db\.m5\.12xlarge | 48 | 173 | 192 | Yes | Yes | 9,500 | 10 Gbps | 
| db\.m5\.8xlarge | 32 | 131 | 128 | Yes | Yes | 6,800 | 10 Gbps | 
| db\.m5\.4xlarge | 16 | 61 | 64 | Yes | Yes | 4,750 | Up to 10 Gbps | 
| db\.m5\.2xlarge\* | 8 | 31 | 32 | Yes | Yes | Up to 4,750 | Up to 10 Gbps | 
| db\.m5\.xlarge\* | 4 | 15 | 16 | Yes | Yes | Up to 4,750 | Up to 10 Gbps | 
| db\.m5\.large\* | 2 | 10 | 8 | Yes | Yes | Up to 4,750 | Up to 10 Gbps | 
| db\.m4 – Standard instance classes | 
| db\.m4\.16xlarge | 64 | 188 | 256 | Yes | Yes | 10,000 | 25 Gbps | 
| db\.m4\.10xlarge | 40 | 124\.5 | 160 | Yes | Yes | 4,000 | 10 Gbps | 
| db\.m4\.4xlarge | 16 | 53\.5 | 64 | Yes | Yes | 2,000 | High | 
| db\.m4\.2xlarge | 8 | 25\.5 | 32 | Yes | Yes | 1,000 | High | 
| db\.m4\.xlarge | 4 | 13 | 16 | Yes | Yes | 750 | High | 
| db\.m4\.large | 2 | 6\.5 | 8 | Yes | Yes | 450 | Moderate | 
| db\.m3 – Standard instance classes | 
| db\.m3\.2xlarge | 8 | 26 | 30 | No | Yes | 1,000 | High | 
| db\.m3\.xlarge | 4 | 13 | 15 | No | Yes | 500 | High | 
| db\.m3\.large | 2 | 6\.5 | 7\.5 | No | No | — | Moderate | 
| db\.m3\.medium | 1 | 3 | 3\.75 | No | No | — | Moderate | 
| db\.m1 – Standard instance classes | 
| db\.m1\.xlarge | 4 | 4 | 15 | No | Yes | 450 | High | 
| db\.m1\.large | 2 | 2 | 7\.5 | No | Yes | 450 | Moderate | 
| db\.m1\.medium | 1 | 1 | 3\.75 | No | No | — | Moderate | 
| db\.m1\.small | 1 | 1 | 1\.7 | No | No | — | Very Low | 
| db\.z1d – Memory\-optimized instance classes | 
| db\.z1d\.12xlarge | 48 | 271 | 384 | Yes | Yes | 14,000 | 25 Gbps | 
| db\.z1d\.6xlarge | 24 | 134 | 192 | Yes | Yes | 7,000 | 10 Gbps | 
| db\.z1d\.3xlarge | 12 | 75 | 96 | Yes | Yes | 3,500 | Up to 10 Gbps | 
| db\.z1d\.2xlarge | 8 | 53 | 64 | Yes | Yes | 2,333 | Up to 10 Gbps | 
| db\.z1d\.xlarge\* | 4 | 28 | 32 | Yes | Yes | Up to 2,333 | Up to 10 Gbps | 
| db\.z1d\.large\* | 2 | 15 | 16 | Yes | Yes | Up to 2,333 | Up to 10 Gbps | 
| db\.x1e – Memory\-optimized instance classes | 
| db\.x1e\.32xlarge | 128 | 340 | 3,904 | Yes | Yes | 14,000 | 25 Gbps | 
| db\.x1e\.16xlarge | 64 | 179 | 1,952 | Yes | Yes | 7,000 | 10 Gbps | 
| db\.x1e\.8xlarge | 32 | 91 | 976 | Yes | Yes | 3,500 | Up to 10 Gbps | 
| db\.x1e\.4xlarge | 16 | 47 | 488 | Yes | Yes | 1,750 | Up to 10 Gbps | 
| db\.x1e\.2xlarge | 8 | 23 | 244 | Yes | Yes | 1,000 | Up to 10 Gbps | 
| db\.x1e\.xlarge | 4 | 12 | 122 | Yes | Yes | 500 | Up to 10 Gbps | 
| db\.x1 – Memory\-optimized instance classes | 
| db\.x1\.32xlarge | 128 | 349 | 1,952 | Yes | Yes | 14,000 | 25 Gbps | 
| db\.x1\.16xlarge | 64 | 174\.5 | 976 | Yes | Yes | 7,000 | 10 Gbps | 
| db\.r6g – Memory\-optimized instance classes powered by AWS Graviton2 processors | 
| db\.r6g\.16xlarge | 64 | – | 512 | Yes | Yes | 19,000 | 25 Gbps | 
| db\.r6g\.12xlarge | 48 | – | 384 | Yes | Yes | 13,500 | 20 Gbps | 
| db\.r6g\.8xlarge | 32 | – | 256 | Yes | Yes | 9,000 | 12 Gbps | 
| db\.r6g\.4xlarge | 16 | – | 128 | Yes | Yes | 4,750 | Up to 10 Gbps  | 
| db\.r6g\.2xlarge\* | 8 | – | 64 | Yes | Yes | Up to 4,750 | Up to 10 Gbps  | 
| db\.r6g\.xlarge\* | 4 | – | 32 | Yes | Yes | Up to 4,750 | Up to 10 Gbps  | 
| db\.r6g\.large\* | 2 | – | 16 | Yes | Yes | Up to 4,750 | Up to 10 Gbps  | 
| db\.r5d – Latest generation memory optimized instance classes | 
| db\.r5d\.24xlarge | 96 | 347 | 768 | Yes | Yes | 19,000 | 25 Gbps | 
| db\.r5d\.16xlarge | 64 | 264 | 512 | Yes | Yes | 13,600 | 20 Gbps | 
| db\.r5d\.12xlarge | 48 | 173 | 384 | Yes | Yes | 9,500 | 10 Gbps | 
| db\.r5d\.8xlarge | 32 | 132 | 256 | Yes | Yes | 6,800 | 10 Gbps | 
| db\.r5d\.4xlarge | 16 | 71 | 128 | Yes | Yes | 4,750 | Up to 10 Gbps | 
| db\.r5d\.2xlarge\* | 8 | 38 | 64 | Yes | Yes | Up to 4,750 | Up to 10 Gbps | 
| db\.r5d\.xlarge\* | 4 | 19 | 32 | Yes | Yes | Up to 4,750 | Up to 10 Gbps | 
| db\.r5d\.large\* | 2 | 10 | 16 | Yes | Yes | Up to 4,750 | Up to 10 Gbps | 
| db\.r5b – Memory\-optimized instance classes | 
| db\.r5b\.24xlarge | 96 | 347 | 768 | Yes | Yes | 60,000 | 25 Gbps | 
| db\.r5b\.16xlarge | 64 | 264 | 512 | Yes | Yes | 40,000 | 20 Gbps | 
| db\.r5b\.12xlarge | 48 | 173 | 384 | Yes | Yes | 30,000 | 10 Gbps | 
| db\.r5b\.8xlarge | 32 | 132 | 256 | Yes | Yes | 20,000 | 10 Gbps | 
| db\.r5b\.4xlarge | 16 | 71 | 128 | Yes | Yes | 10,000 | Up to 10 Gbps | 
| db\.r5b\.2xlarge\* | 8 | 38 | 64 | Yes | Yes | Up to 10,000 | Up to 10 Gbps | 
| db\.r5b\.xlarge\* | 4 | 19 | 32 | Yes | Yes | Up to 10,000 | Up to 10 Gbps | 
| db\.r5b\.large\* | 2 | 10 | 16 | Yes | Yes | Up to 10,000 | Up to 10 Gbps | 
| db\.r5 – Latest generation memory\-optimized instance classes | 
| db\.r5\.24xlarge | 96 | 347 | 768 | Yes | Yes | 19,000 | 25 Gbps | 
| db\.r5\.16xlarge | 64 | 264 | 512 | Yes | Yes | 13,600 | 20 Gbps | 
| db\.r5\.12xlarge | 48 | 173 | 384 | Yes | Yes | 9,500 | 10 Gbps | 
| db\.r5\.8xlarge | 32 | 132 | 256 | Yes | Yes | 6,800 | 10 Gbps | 
| db\.r5\.4xlarge | 16 | 71 | 128 | Yes | Yes | 4,750 | Up to 10 Gbps | 
| db\.r5\.2xlarge\* | 8 | 38 | 64 | Yes | Yes | Up to 4,750 | Up to 10 Gbps | 
| db\.r5\.xlarge\* | 4 | 19 | 32 | Yes | Yes | Up to 4,750 | Up to 10 Gbps | 
| db\.r5\.large\* | 2 | 10 | 16 | Yes | Yes | Up to 4,750 | Up to 10 Gbps | 
| db\.r4 – Memory\-optimized instance classes | 
| db\.r4\.16xlarge | 64 | 195 | 488 | Yes | Yes | 14,000 | 25 Gbps | 
| db\.r4\.8xlarge | 32 | 99 | 244 | Yes | Yes | 7,000 | 10 Gbps | 
| db\.r4\.4xlarge | 16 | 53 | 122 | Yes | Yes | 3,500 | Up to 10 Gbps | 
| db\.r4\.2xlarge | 8 | 27 | 61 | Yes | Yes | 1,700 | Up to 10 Gbps | 
| db\.r4\.xlarge | 4 | 13\.5 | 30\.5 | Yes | Yes | 850 | Up to 10 Gbps | 
| db\.r4\.large | 2 | 7 | 15\.25 | Yes | Yes | 425 | Up to 10 Gbps | 
| db\.r3 – Memory\-optimized instance classes \(deprecated\) | 
| db\.r3\.8xlarge | 32 | 104 | 244 | No | No | — | 10 Gbps | 
| db\.r3\.4xlarge | 16 | 52 | 122 | No | Yes | 2,000 | High | 
| db\.r3\.2xlarge | 8 | 26 | 61 | No | Yes | 1,000 | High | 
| db\.r3\.xlarge | 4 | 13 | 30\.5 | No | Yes | 500 | Moderate | 
| db\.r3\.large | 2 | 6\.5 | 15\.25 | No | No | — | Moderate | 
| db\.m2 – Memory\-optimized instance classes | 
| db\.m2\.4xlarge | 8 | 26 | 68\.4 | No | Yes | 1,000 | High | 
| db\.m2\.2xlarge | 4 | 13 | 34\.2 | No | Yes | 500 | Moderate | 
| db\.m2\.xlarge | 2 | 6\.5 | 17\.1 | No | No | — | Moderate | 
| db\.t3 – Next generation burstable performance instance classes | 
| db\.t3\.2xlarge\* | 8 | Variable | 32 | Yes | Yes | Up to 2,048 | Up to 5 Gbps | 
| db\.t3\.xlarge\* | 4 | Variable | 16 | Yes | Yes | Up to 2,048 | Up to 5 Gbps | 
| db\.t3\.large\* | 2 | Variable | 8 | Yes | Yes | Up to 2,048 | Up to 5 Gbps | 
| db\.t3\.medium\* | 2 | Variable | 4 | Yes | Yes | Up to 1,536 | Up to 5 Gbps | 
| db\.t3\.small\* | 2 | Variable | 2 | Yes | Yes | Up to 1,536 | Up to 5 Gbps | 
| db\.t3\.micro\* | 2 | Variable | 1 | Yes | Yes | Up to 1,536 | Up to 5 Gbps | 
| db\.t2 – Burstable performance instance classes | 
| db\.t2\.2xlarge | 8 | Variable | 32 | Yes | No | — | Moderate | 
| db\.t2\.xlarge | 4 | Variable | 16 | Yes | No | — | Moderate | 
| db\.t2\.large | 2 | Variable | 8 | Yes | No | — | Moderate | 
| db\.t2\.medium | 2 | Variable | 4 | Yes | No | — | Moderate | 
| db\.t2\.small | 1 | Variable | 2 | Yes | No | — | Low | 
| db\.t2\.micro | 1 | Variable | 1 | Yes | No | — | Low | 

\* These DB instance classes can support maximum performance for 30 minutes at least once every 24 hours\. For more information on baseline performance of these instance types, see [Amazon EBS\-optimized instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSOptimized.html) in the *Amazon EC2 User Guide for Linux Instances\.*

\*\* The r3\.8xlarge instance doesn't have dedicated EBS bandwidth and therefore doesn't offer EBS optimization\. On this instance, network traffic and Amazon EBS traffic share the same 10\-gigabit network interface\.
