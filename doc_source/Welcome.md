# What is Amazon Relational Database Service \(Amazon RDS\)?<a name="Welcome"></a>

Amazon Relational Database Service \(Amazon RDS\) is a web service that makes it easier to set up, operate, and scale a relational database in the AWS Cloud\. It provides cost\-efficient, resizable capacity for an industry\-standard relational database and manages common database administration tasks\.

**Note**  
This guide covers Amazon RDS database engines other than Amazon Aurora\. For information about using Amazon Aurora, see the [https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/CHAP_AuroraOverview.html](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/CHAP_AuroraOverview.html)\.

If you are new to AWS products and services, begin learning more with the following resources:
+ For an overview of all AWS products, see [What is cloud computing?](http://aws.amazon.com/what-is-aws/)
+ Amazon Web Services provides a number of database services\. For guidance on which service is best for your environment, see [Running databases on AWS](http://aws.amazon.com/running_databases/)\. 

## Overview of Amazon RDS<a name="Welcome.Concepts"></a>

Why do you want to run a relational database in the AWS Cloud? Because AWS takes over many of the difficult and tedious management tasks of a relational database\.

**Topics**
+ [Amazon EC2 and on\-premises databases](#Welcome.Concepts.on-prem)
+ [Amazon RDS and Amazon EC2](#Welcome.Concepts.RDS)
+ [Amazon RDS Custom for Oracle and Microsoft SQL Server](#Welcome.Concepts.Custom)
+ [Amazon RDS on AWS Outposts](#Welcome.Concepts.Outposts)

### Amazon EC2 and on\-premises databases<a name="Welcome.Concepts.on-prem"></a>

Amazon Elastic Compute Cloud \(Amazon EC2\) provides scalable computing capacity in the AWS Cloud\. Amazon EC2 eliminates your need to invest in hardware up front, so you can develop and deploy applications faster\.

When you buy an on\-premises server, you get CPU, memory, storage, and IOPS, all bundled together\. With Amazon EC2, these are split apart so that you can scale them independently\. If you need more CPU, less IOPS, or more storage, you can easily allocate them\.

For a relational database in an on\-premises server, you assume full responsibility for the server, operating system, and software\. For a database on an Amazon EC2 instance, AWS manages the layers below the operating system\. In this way, Amazon EC2 eliminates some of the burden of managing an on\-premises database server\. 

In the following table, you can find a comparison of the management models for on\-premises databases and Amazon EC2\.


|  Feature  |  On\-premises management  |  Amazon EC2 management  | 
| --- | --- | --- | 
|  Application optimization  |  Customer  |  Customer  | 
|  Scaling  |  Customer  |  Customer  | 
|  High availability  |  Customer  |  Customer  | 
|  Database backups  |  Customer  |  Customer  | 
|  Database software patching  |  Customer  |  Customer  | 
|  Database software install  |  Customer  |  Customer  | 
|  Operating system \(OS\) patching  |  Customer  |  Customer  | 
|  OS installation  |  Customer  |  Customer  | 
|  Server maintenance  |  Customer  |  AWS  | 
|  Hardware lifecycle  |  Customer  |  AWS  | 
|  Power, network, and cooling  |  Customer  |  AWS  | 

Amazon EC2 isn't a fully managed service\. Thus, when you run a database on Amazon EC2, you're more prone to user errors\. For example, when you update the operating system or database software manually, you might accidentally cause application downtime\. You might spend hours checking every change to identify and fix an issue\.

### Amazon RDS and Amazon EC2<a name="Welcome.Concepts.RDS"></a>

Amazon RDS is a managed database service\. It's responsible for most management tasks\. By eliminating tedious manual tasks, Amazon RDS frees you to focus on your application and your users\. We recommend Amazon RDS over Amazon EC2 as your default choice for most database deployments\.

In the following table, you can find a comparison of the management models in Amazon EC2 and Amazon RDS\.


|  Feature  |  Amazon EC2 management  |  Amazon RDS management  | 
| --- | --- | --- | 
|  Application optimization  |  Customer  |  Customer  | 
|  Scaling  |  Customer  |  AWS  | 
|  High availability  |  Customer  |  AWS  | 
|  Database backups  |  Customer  |  AWS  | 
|  Database software patching  |  Customer  |  AWS  | 
|  Database software install  |  Customer  |  AWS  | 
|  OS patching  |  Customer  |  AWS  | 
|  OS installation  |  Customer  |  AWS  | 
|  Server maintenance  |  AWS  |  AWS  | 
|  Hardware lifecycle  |  AWS  |  AWS  | 
|  Power, network, and cooling  |  AWS  |  AWS  | 

Amazon RDS provides the following specific advantages over database deployments that aren't fully managed:
+ You can use the database products you are already familiar with: MariaDB, Microsoft SQL Server, MySQL, Oracle, and PostgreSQL\.
+ Amazon RDS manages backups, software patching, automatic failure detection, and recovery\.
+ You can turn on automated backups, or manually create your own backup snapshots\. You can use these backups to restore a database\. The Amazon RDS restore process works reliably and efficiently\.
+ You can get high availability with a primary instance and a synchronous secondary instance that you can fail over to when problems occur\. You can also use read replicas to increase read scaling\.
+ In addition to the security in your database package, you can help control who can access your RDS databases\. To do so, you can use AWS Identity and Access Management \(IAM\) to define users and permissions\. You can also help protect your databases by putting them in a virtual private cloud \(VPC\)\.

### Amazon RDS Custom for Oracle and Microsoft SQL Server<a name="Welcome.Concepts.Custom"></a>

Amazon RDS Custom is an RDS management type that gives you full access to your database and operating system\.

You can use the control capabilities of RDS Custom to access and customize the database environment and operating system for legacy and packaged business applications\. Meanwhile, Amazon RDS automates database administration tasks and operations\.

In this deployment model, you can install applications and change configuration settings to suit your applications\. At the same time, you can offload database administration tasks such as provisioning, scaling, upgrading, and backup to AWS\. You can take advantage of the database management benefits of Amazon RDS, with more control and flexibility\.

For Oracle Database and Microsoft SQL Server, RDS Custom combines the automation of Amazon RDS with the flexibility of Amazon EC2\. For more information on RDS Custom, see [Working with Amazon RDS Custom](rds-custom.md)\.

With the shared responsibility model of RDS Custom, you get more control than in Amazon RDS, but also more responsibility\. For more information, see [Shared responsibility model](rds-custom.md#custom-intro.solution.shared)\.

### Amazon RDS on AWS Outposts<a name="Welcome.Concepts.Outposts"></a>

Amazon RDS on AWS Outposts extends RDS for SQL Server, RDS for MySQL, and RDS for PostgreSQL databases to AWS Outposts environments\. AWS Outposts uses the same hardware as in public AWS Regions to bring AWS services, infrastructure, and operation models on\-premises\. With RDS on Outposts, you can provision managed DB instances close to the business applications that must run on\-premises\. For more information, see [Working with Amazon RDS on AWS Outposts](rds-on-outposts.md)\.

## DB instances<a name="Welcome.Concepts.DBInstance"></a>

A *DB instance* is an isolated database environment in the AWS Cloud\. The basic building block of Amazon RDS is the DB instance\. 

Your DB instance can contain one or more user\-created databases\. You can access your DB instance by using the same tools and applications that you use with a standalone database instance\. You can create and modify a DB instance by using the AWS Command Line Interface \(AWS CLI\), the Amazon RDS API, or the AWS Management Console\.

### DB engines<a name="Welcome.Concepts.DBInstance.engine"></a>

A *DB engine* is the specific relational database software that runs on your DB instance\. Amazon RDS currently supports the following engines:
+ MariaDB
+ Microsoft SQL Server
+ MySQL
+ Oracle
+ PostgreSQL

Each DB engine has its own supported features, and each version of a DB engine can include specific features\. Support for Amazon RDS features varies across AWS Regions and specific versions of each DB engine\. To check feature support in different engine versions and Regions, see [Supported features in Amazon RDS by AWS Region and DB engine](Concepts.RDSFeaturesRegionsDBEngines.grids.md)\.

Additionally, each DB engine has a set of parameters in a DB parameter group that control the behavior of the databases that it manages\.

### DB instance classes<a name="Welcome.Concepts.DBInstance.instance-class"></a>

A *DB instance class* determines the computation and memory capacity of a DB instance\. A DB instance class consists of both the DB instance type and the size\. Each instance type offers different compute, memory, and storage capabilities\. For example, db\.m6g is a general\-purpose DB instance type powered by AWS Graviton2 processors\. Within the db\.m6g instance type, db\.m6g\.2xlarge is a DB instance class\.

You can select the DB instance that best meets your needs\. If your needs change over time, you can change DB instances\. For information, see [DB instance classes](Concepts.DBInstanceClass.md)\.

**Note**  
For pricing information on DB instance classes, see the Pricing section of the [Amazon RDS](http://aws.amazon.com/rds/) product page\.

### DB instance storage<a name="Welcome.Concepts.DBInstance.storage"></a>

Amazon EBS provides durable, block\-level storage volumes that you can attach to a running instance\. DB instance storage comes in the following types:
+ General Purpose \(SSD\)
+ Provisioned IOPS \(PIOPS\)
+ Magnetic

The storage types differ in performance characteristics and price\. You can tailor your storage performance and cost to the needs of your database\. 

Each DB instance has minimum and maximum storage requirements depending on the storage type and the database engine it supports\. It's important to have sufficient storage so that your databases have room to grow\. Also, sufficient storage makes sure that features for the DB engine have room to write content or log entries\. For more information, see [Amazon RDS DB instance storage](CHAP_Storage.md)\. 

### Amazon Virtual Private Cloud \(Amazon VPC\)<a name="Welcome.Concepts.DBInstance.VPC"></a>

You can run a DB instance on a virtual private cloud \(VPC\) using the Amazon Virtual Private Cloud \(Amazon VPC\) service\. When you use a VPC, you have control over your virtual networking environment\. You can choose your own IP address range, create subnets, and configure routing and access control lists\. The basic functionality of Amazon RDS is the same whether it's running in a VPC or not\. Amazon RDS manages backups, software patching, automatic failure detection, and recovery\. There's no additional cost to run your DB instance in a VPC\. For more information on using Amazon VPC with RDS, see [Amazon VPC VPCs and Amazon RDS](USER_VPC.md)\. 

Amazon RDS uses Network Time Protocol \(NTP\) to synchronize the time on DB Instances\. 

## AWS Regions and Availability Zones<a name="Welcome.Concepts.Regions"></a>

Amazon cloud computing resources are housed in highly available data center facilities in different areas of the world \(for example, North America, Europe, or Asia\)\. Each data center location is called an AWS Region\. 

Each AWS Region contains multiple distinct locations called Availability Zones, or AZs\. Each Availability Zone is engineered to be isolated from failures in other Availability Zones\. Each is engineered to provide inexpensive, low\-latency network connectivity to other Availability Zones in the same AWS Region\. By launching instances in separate Availability Zones, you can protect your applications from the failure of a single location\. For more information, see [Regions, Availability Zones, and Local Zones](Concepts.RegionsAndAvailabilityZones.md)\. 

You can run your DB instance in several Availability Zones, an option called a Multi\-AZ deployment\. When you choose this option, Amazon automatically provisions and maintains one or more secondary standby DB instances in a different Availability Zone\. Your primary DB instance is replicated across Availability Zones to each secondary DB instance\. This approach helps provide data redundancy and failover support, eliminate I/O freezes, and minimize latency spikes during system backups\. In a Multi\-AZ DB clusters deployment, the secondary DB instances can also serve read traffic\. For more information, see [Multi\-AZ deployments for high availability](Concepts.MultiAZ.md)\. 

## Security<a name="Welcome.Concepts.SecurityGroups"></a>

A *security group *controls the access to a DB instance\. It does so by allowing access to IP address ranges or Amazon EC2 instances that you specify\. 

For more information about security groups, see [Security in Amazon RDS](UsingWithRDS.md)\.

## Monitoring an Amazon RDS DB instance<a name="Welcome.Monitoring"></a>

There are several ways that you can track the performance and health of a DB instance\. You can use the Amazon CloudWatch service to monitor the performance and health of a DB instance\. CloudWatch performance charts are shown in the Amazon RDS console\. You can also subscribe to Amazon RDS events to be notified about changes to a DB instance, DB snapshot, or DB parameter group\. For more information, see [Monitoring metrics in an Amazon RDS instance](CHAP_Monitoring.md)\. 

## How to work with Amazon RDS<a name="Welcome.Interfaces"></a>

There are several ways that you can interact with Amazon RDS\.

### AWS Management Console<a name="Welcome.Interfaces.Console"></a>

The AWS Management Console is a simple web\-based user interface\. You can manage your DB instances from the console with no programming required\. To access the Amazon RDS console, sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\. 

### Command line interface<a name="Welcome.Interfaces.CLI"></a>

You can use the AWS Command Line Interface \(AWS CLI\) to access the Amazon RDS API interactively\. To install the AWS CLI, see [Installing the AWS Command Line Interface](https://docs.aws.amazon.com/cli/latest/userguide/installing.html)\. To begin using the AWS CLI for RDS, see [AWS Command Line Interface reference for Amazon RDS](https://docs.aws.amazon.com/cli/latest/reference/rds/index.html)\. 

### Programming with Amazon RDS<a name="Welcome.Interfaces.API"></a>

If you are a developer, you can access the Amazon RDS programmatically\. For more information, see [Amazon RDS API reference](ProgrammingGuide.md)\. 

For application development, we recommend that you use one of the AWS Software Development Kits \(SDKs\)\. The AWS SDKs handle low\-level details such as authentication, retry logic, and error handling, so that you can focus on your application logic\. AWS SDKs are available for a wide variety of languages\. For more information, see [Tools for Amazon web services ](https://aws.amazon.com/tools/)\. 

AWS also provides libraries, sample code, tutorials, and other resources to help you get started more easily\. For more information, see [Sample code & libraries](https://aws.amazon.com/code)\. 

## How you are charged for Amazon RDS<a name="Welcome.Costs"></a>

When you use Amazon RDS, you can choose to use on\-demand DB instances or reserved DB instances\. For more information, see [DB instance billing for Amazon RDS](User_DBInstanceBilling.md)\. 

For Amazon RDS pricing information, see the [Amazon RDS product page](https://aws.amazon.com/rds/pricing)\.

## What's next?<a name="Welcome.WhatsNext"></a>

The preceding section introduced you to the basic infrastructure components that RDS offers\. What should you do next? 

### Getting started<a name="Welcome.WhatsNext.GettingStarted"></a>

Create a DB instance using instructions in [Getting started with Amazon RDS](CHAP_GettingStarted.md)\. 

### Topics specific to database engines<a name="Welcome.WhatsNext.DBTopics"></a>

You can review information specific to a particular DB engine in the following sections: 
+ [Amazon RDS for MariaDB](CHAP_MariaDB.md)
+ [Amazon RDS for Microsoft SQL Server](CHAP_SQLServer.md)
+ [Amazon RDS for MySQL](CHAP_MySQL.md)
+ [Amazon RDS for Oracle](CHAP_Oracle.md)
+ [Amazon RDS for PostgreSQL](CHAP_PostgreSQL.md)