# Amazon RDS DB Instances<a name="Overview.DBInstance"></a>

A *DB instance* is an isolated database environment running in the cloud\. It is the basic building block of Amazon RDS\. A DB instance can contain multiple user\-created databases, and can be accessed using the same client tools and applications you might use to access a standalone database instance\. DB instances are simple to create and modify with the Amazon AWS command line tools, Amazon RDS API operations, or the AWS Management Console\. 

**Note**  
Amazon RDS supports access to databases using any standard SQL client application\. Amazon RDS does not allow direct host access\. 

You can have up to 40 Amazon RDS DB instances, with the following limitations:
+ 10 for each SQL Server edition \(Enterprise, Standard, Web, and Express\) under the "license\-included" model
+ 10 for Oracle under the "license\-included" model
+ 40 for MySQL, MariaDB, or PostgreSQL
+ 40 for Oracle under the "bring\-your\-own\-license" \(BYOL\) licensing model

**Note**  
If your application requires more DB instances, you can request additional DB instances by using [this form](https://console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase&limitType=service-code-rds-instances)\.

Each DB instance has a DB instance identifier\. This customer\-supplied name uniquely identifies the DB instance when interacting with the Amazon RDS API and AWS CLI commands\. The DB instance identifier must be unique for that customer in an AWS Region\.

The identifier is used as part of the DNS hostname allocated to your instance by RDS\. For example, if you specify `db1` as the DB instance identifier, then RDS will automatically allocate a DNS endpoint for your instance, such as `db1.123456789012.us-east-1.rds.amazonaws.com`, where `123456789012` is the fixed identifier for a specific region for your account\.

Each DB instance supports a database engine\. Amazon RDS currently supports MySQL, MariaDB, PostgreSQL, Oracle, Microsoft SQL Server, and Amazon Aurora database engines\. 

When creating a DB instance, some database engines require that a database name be specified\. A DB instance can host multiple databases, or a single Oracle database with multiple schemas\. The database name value depends on the database engine: 
+ For the MySQL and MariaDB database engines, the database name is the name of a database hosted in your DB instance\. Databases hosted by the same DB instance must have a unique name within that instance\. 
+ For the Oracle database engine, database name is used to set the value of ORACLE\_SID, which must be supplied when connecting to the Oracle RDS instance\. 
+ For the Microsoft SQL Server database engine, database name is not a supported parameter\.
+ For the PostgreSQL database engine, the database name is the name of a database hosted in your DB instance\. A database name is not required when creating a DB instance\. Databases hosted by the same DB instance must have a unique name within that instance\.

Amazon RDS creates a master user account for your DB instance as part of the creation process\. This master user has permissions to create databases and to perform create, delete, select, update, and insert operations on tables the master user creates\. You must set the master user password when you create a DB instance, but you can change it at any time using the Amazon AWS command line tools, Amazon RDS API operations, or the AWS Management Console\. You can also change the master user password and manage users using standard SQL commands\. 

**Note**  
This guide covers non\-Aurora Amazon RDS database engines\. For information about using Amazon Aurora, see the [https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/CHAP_AuroraOverview.html](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/CHAP_AuroraOverview.html)\.