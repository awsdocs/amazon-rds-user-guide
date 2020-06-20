# What Is Amazon Relational Database Service \(Amazon RDS\)?<a name="Welcome"></a>

Amazon Relational Database Service \(Amazon RDS\) is a web service that makes it easier to set up, operate, and scale a relational database in the AWS Cloud\. It provides cost\-efficient, resizable capacity for an industry\-standard relational database and manages common database administration tasks\.

**Note**  
This guide covers Amazon RDS database engines other than Amazon Aurora\. For information about using Amazon Aurora, see the [https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/CHAP_AuroraOverview.html](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/CHAP_AuroraOverview.html)\.  
This guide covers using Amazon RDS in the AWS Cloud\. For information about using Amazon RDS in on\-premises VMware environments, see the [https://docs.aws.amazon.com/AmazonRDS/latest/RDSonVMwareUserGuide/rds-on-vmware.html](https://docs.aws.amazon.com/AmazonRDS/latest/RDSonVMwareUserGuide/rds-on-vmware.html)\.

## Overview of Amazon RDS<a name="Welcome.Concepts"></a>

Why do you want a managed relational database service? Because Amazon RDS takes over many of the difficult or tedious management tasks of a relational database: 
+ When you buy a server, you get CPU, memory, storage, and IOPS, all bundled together\. With Amazon RDS, these are split apart so that you can scale them independently\. If you need more CPU, less IOPS, or more storage, you can easily allocate them\. 
+ Amazon RDS manages backups, software patching, automatic failure detection, and recovery\. 
+ To deliver a managed service experience, Amazon RDS doesn't provide shell access to DB instances\. It also restricts access to certain system procedures and tables that require advanced privileges\. 
+ You can have automated backups performed when you need them, or manually create your own backup snapshot\. You can use these backups to restore a database\. The Amazon RDS restore process works reliably and efficiently\. 
+ You can get high availability with a primary instance and a synchronous secondary instance that you can fail over to when problems occur\. You can also use MySQL, MariaDB, or PostgreSQL read replicas to increase read scaling\. 
+ You can use the database products you are already familiar with: MySQL, MariaDB, PostgreSQL, Oracle, Microsoft SQL Server\. 
+ In addition to the security in your database package, you can help control who can access your RDS databases by using AWS Identity and Access Management \(IAM\) to define users and permissions\. You can also help protect your databases by putting them in a virtual private cloud\. 

If you are new to AWS products and services, begin learning more with the following resources: 
+ For an overview of all AWS products, see [What is Cloud Computing?](http://aws.amazon.com/what-is-aws/)
+ Amazon Web Services provides a number of database services\. For guidance on which service is best for your environment, see [Running Databases on AWS](http://aws.amazon.com/running_databases/)\. 

## DB Instances<a name="Welcome.Concepts.DBInstance"></a>

The basic building block of Amazon RDS is the DB instance\. A *DB instance* is an isolated database environment in the AWS Cloud\. Your DB instance can contain multiple user\-created databases\. You can access your DB instance by using the same tools and applications that you use with a standalone database instance\. You can create and modify a DB instance by using the AWS Command Line Interface, the Amazon RDS API, or the AWS Management Console\. 

 Each DB instance runs a *DB engine*\. Amazon RDS currently supports the MySQL, MariaDB, PostgreSQL, Oracle, and Microsoft SQL Server DB engines\. Each DB engine has its own supported features, and each version of a DB engine may include specific features\. Additionally, each DB engine has a set of parameters in a DB parameter group that control the behavior of the databases that it manages\. 

 The computation and memory capacity of a DB instance is determined by its *DB instance class*\. You can select the DB instance that best meets your needs\. If your needs change over time, you can change DB instances\. For information, see [DB Instance Classes](Concepts.DBInstanceClass.md)\. 

**Note**  
For pricing information on DB instance classes, see the Pricing section of the [Amazon RDS](http://aws.amazon.com/rds/) product page\. 

DB instance storage comes in three types: Magnetic, General Purpose \(SSD\), and Provisioned IOPS \(PIOPS\)\. They differ in performance characteristics and price, allowing you to tailor your storage performance and cost to the needs of your database\. Each DB instance has minimum and maximum storage requirements depending on the storage type and the database engine it supports\. It's important to have sufficient storage so that your databases have room to grow\. Also, sufficient storage makes sure that features for the DB engine have room to write content or log entries\. For more information, see [Amazon RDS DB Instance Storage](CHAP_Storage.md)\. 

You can run a DB instance on a virtual private cloud \(VPC\) using the Amazon Virtual Private Cloud \(Amazon VPC\) service\. When you use a VPC, you have control over your virtual networking environment\. You can choose your own IP address range, create subnets, and configure routing and access control lists\. The basic functionality of Amazon RDS is the same whether it's running in a VPC or not\. Amazon RDS manages backups, software patching, automatic failure detection, and recovery\. There's no additional cost to run your DB instance in a VPC\. For more information on using Amazon VPC with RDS, see [Amazon Virtual Private Cloud VPCs and Amazon RDS](USER_VPC.md)\. 

Amazon RDS uses Network Time Protocol \(NTP\) to synchronize the time on DB Instances\. 

## AWS Regions and Availability Zones<a name="Welcome.Concepts.Regions"></a>

Amazon cloud computing resources are housed in highly available data center facilities in different areas of the world \(for example, North America, Europe, or Asia\)\. Each data center location is called an AWS Region\. 

Each AWS Region contains multiple distinct locations called Availability Zones, or AZs\. Each Availability Zone is engineered to be isolated from failures in other Availability Zones\. Each is engineered to provide inexpensive, low\-latency network connectivity to other Availability Zones in the same AWS Region\. By launching instances in separate Availability Zones, you can protect your applications from the failure of a single location\. For more information, see [ Regions, Availability Zones, and Local Zones ](Concepts.RegionsAndAvailabilityZones.md)\. 

You can run your DB instance in several Availability Zones, an option called a Multi\-AZ deployment\. When you choose this option, Amazon automatically provisions and maintains a secondary standby DB instance in a different Availability Zone\. Your primary DB instance is synchronously replicated across Availability Zones to the secondary instance\. This approach helps provide data redundancy and failover support, eliminate I/O freezes, and minimize latency spikes during system backups\. For more information, see [High Availability \(Multi\-AZ\) for Amazon RDS](Concepts.MultiAZ.md)\. 

## Security<a name="Welcome.Concepts.SecurityGroups"></a>

A *security group *controls the access to a DB instance\. It does so by allowing access to IP address ranges or Amazon EC2 instances that you specify\. 

For more information about security groups, see [Security in Amazon RDS](UsingWithRDS.md)\.

## Monitoring an Amazon RDS DB Instance<a name="Welcome.Monitoring"></a>

 There are several ways that you can track the performance and health of a DB instance\. You can use the Amazon CloudWatch service to monitor the performance and health of a DB instance\. CloudWatch performance charts are shown in the Amazon RDS console\. You can also subscribe to Amazon RDS events to be notified about changes to a DB instance, DB snapshot, DB parameter group, or DB security group\. For more information, see [Monitoring an Amazon RDS DB Instance](CHAP_Monitoring.md)\. 

## How to Work with Amazon RDS<a name="Welcome.Interfaces"></a>

There are several ways that you can interact with Amazon RDS\.

### AWS Management Console<a name="Welcome.Interfaces.Console"></a>

The AWS Management Console is a simple web\-based user interface\. You can manage your DB instances from the console with no programming required\. To access the Amazon RDS console, sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\. 

### Command Line Interface<a name="Welcome.Interfaces.CLI"></a>

You can use the AWS Command Line Interface \(AWS CLI\) to access the Amazon RDS API interactively\. To install the AWS CLI, see [Installing the AWS Command Line Interface](https://docs.aws.amazon.com/cli/latest/userguide/installing.html)\. To begin using the AWS CLI for RDS, see [AWS Command Line Interface Reference for Amazon RDS](https://docs.aws.amazon.com/cli/latest/reference/rds/index.html)\. 

### Programming with Amazon RDS<a name="Welcome.Interfaces.API"></a>

If you are a developer, you can access the Amazon RDS programmatically\. For more information, see [Amazon RDS Application Programming Interface \(API\) Reference](ProgrammingGuide.md)\. 

For application development, we recommend that you use one of the AWS Software Development Kits \(SDKs\)\. The AWS SDKs handle low\-level details such as authentication, retry logic, and error handling, so that you can focus on your application logic\. AWS SDKs are available for a wide variety of languages\. For more information, see [Tools for Amazon Web Services ](https://aws.amazon.com/tools/)\. 

AWS also provides libraries, sample code, tutorials, and other resources to help you get started more easily\. For more information, see [Sample Code & Libraries](https://aws.amazon.com/code)\. 

## How You Are Charged for Amazon RDS<a name="Welcome.Costs"></a>

When you use Amazon RDS, you can choose to use on\-demand DB instances or reserved DB instances\. For more information, see [ DB Instance Billing for Amazon RDS ](User_DBInstanceBilling.md)\. 

For Amazon RDS pricing information, see the [Amazon RDS product page](https://aws.amazon.com/rds/pricing)\.

## What's Next?<a name="Welcome.WhatsNext"></a>

The preceding section introduced you to the basic infrastructure components that RDS offers\. What should you do next? 

### Getting Started<a name="Welcome.WhatsNext.GettingStarted"></a>

Create a DB instance using instructions in [Getting Started with Amazon RDS](CHAP_GettingStarted.md)\. 

### Database Engine–Specific Topics<a name="Welcome.WhatsNext.DBTopics"></a>

You can review information specific to a particular DB engine in the following sections: 
+ [MariaDB on Amazon RDS](CHAP_MariaDB.md)
+ [Microsoft SQL Server on Amazon RDS](CHAP_SQLServer.md)
+ [MySQL on Amazon RDS](CHAP_MySQL.md)
+ [Oracle on Amazon RDS](CHAP_Oracle.md)
+ [PostgreSQL on Amazon RDS](CHAP_PostgreSQL.md)