# Amazon RDS DB Instances<a name="Overview.DBInstance"></a>

A *DB instance* is an isolated database environment running in the cloud\. It is the basic building block of Amazon RDS\. A DB instance can contain multiple user\-created databases, and can be accessed using the same client tools and applications you might use to access a standalone database instance\. DB instances are simple to create and modify with the Amazon AWS command line tools, Amazon RDS API actions, or the AWS Management Console\. 

**Note**  
Amazon RDS supports access to databases using any standard SQL client application\. Amazon RDS does not allow direct host access\. 

You can have up to 40 Amazon RDS DB instances\. Of these 40, up to 10 can be Oracle or SQL Server DB instances under the "License Included" model\. All 40 DB instances can be used for MySQL, MariaDB, or PostgreSQL\. You can also have 40 DB instances for SQL Server or Oracle under the "BYOL" licensing model\. If your application requires more DB instances, you can request additional DB instances using the form at https://console\.aws\.amazon\.com/support/home\#/case/create?issueType=service\-limit\-increase&limitType=service\-code\-rds\-instances\. 

Each DB instance has a DB instance identifier\. This customer\-supplied name uniquely identifies the DB instance when interacting with the Amazon RDS API and AWS CLI commands\. The DB instance identifier must be unique for that customer in an AWS Region\. 

Each DB instance supports a database engine\. Amazon RDS currently supports MySQL, MariaDB, PostgreSQL, Oracle, Microsoft SQL Server, and Amazon Aurora database engines\. 

When creating a DB instance, some database engines require that a database name be specified\. A DB instance can host multiple databases, or a single Oracle database with multiple schemas\. The database name value depends on the database engine: 
+ For the MySQL and MariaDB database engines, the database name is the name of a database hosted in your DB instance\. Databases hosted by the same DB instance must have a unique name within that instance\. 
+ For the Oracle database engine, database name is used to set the value of ORACLE\_SID, which must be supplied when connecting to the Oracle RDS instance\. 
+ For the Microsoft SQL Server database engine, database name is not a supported parameter\.
+ For the PostgreSQL database engine, the database name is the name of a database hosted in your DB instance\. A database name is not required when creating a DB instance\. Databases hosted by the same DB instance must have a unique name within that instance\.

Amazon RDS creates a master user account for your DB instance as part of the creation process\. This master user has permissions to create databases and to perform create, delete, select, update, and insert operations on tables the master user creates\. You must set the master user password when you create a DB instance, but you can change it at any time using the Amazon AWS command line tools, Amazon RDS API actions, or the AWS Management Console\. You can also change the master user password and manage users using standard SQL commands\. 

**Note**  
This guide covers non\-Aurora Amazon RDS database engines\. For information about using Amazon Aurora, see the [https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/CHAP_AuroraOverview.html](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/CHAP_AuroraOverview.html)\.

**Topics**
+ [DB Instance Class](Concepts.DBInstanceClass.md)
+ [DB Instance Status](Overview.DBInstance.Status.md)
+ [Regions and Availability Zones](Concepts.RegionsAndAvailabilityZones.md)
+ [DB instance storage](CHAP_Storage.md)
+ [High Availability \(Multi\-AZ\) for Amazon RDS](Concepts.MultiAZ.md)
+ [Amazon RDS DB Instance Lifecycle](CHAP_CommonTasks.md)
+ [Tagging Amazon RDS Resources](USER_Tagging.md)
+ [Working with Read Replicas of MariaDB, MySQL, and PostgreSQL DB Instances](USER_ReadRepl.md)
+ [Working with Option Groups](USER_WorkingWithOptionGroups.md)
+ [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)
+ [Working with Amazon Resource Names \(ARNs\) in Amazon RDS](USER_Tagging.ARN.md)
+ [Working with Storage](USER_PIOPS.StorageTypes.md)
+ [DB Instance Billing for Amazon RDS](User_DBInstanceBilling.md)