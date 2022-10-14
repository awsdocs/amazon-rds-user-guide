# Amazon RDS for Microsoft SQL Server<a name="CHAP_SQLServer"></a>

Amazon RDS supports several versions and editions of Microsoft SQL Server\. The most recent supported version of each major version is listed here\. For the full list of supported versions, editions, and RDS engine versions, see [Microsoft SQL Server versions on Amazon RDS](#SQLServer.Concepts.General.VersionSupport)\.


+ SQL Server 2019 CU16 15\.0\.4236\.7, released per [KB5014353](https://support.microsoft.com/en-us/topic/kb5014353-description-of-the-security-update-for-sql-server-2019-cu16-june-14-2022-f0afe659-bd19-4c87-a417-a4c67a47e644) on June 14, 2022\.
+ SQL Server 2017 CU30 14\.0\.3451\.2, released per [KB5013756](https://support.microsoft.com/en-us/topic/kb5013756-cumulative-update-30-for-sql-server-2017-274943fa-8dde-4844-90ed-d3b587fa0c7c) on July 13, 2022\.
+ SQL Server 2016 SP3 GDR 13\.0\.6419\.1, released per [KB5014355](https://support.microsoft.com/en-us/topic/kb5014355-description-of-the-security-update-for-sql-server-2016-sp3-gdr-june-14-2022-bb5097a0-f8f1-4d2c-bfe1-af069ca3cc59) on June 14, 2022\.
+ SQL Server 2014 SP3 CU4 12\.0\.6439\.10, released per [KB5014164](https://support.microsoft.com/en-us/topic/kb5014164-description-of-the-security-update-for-sql-server-2014-sp3-cu4-june-14-2022-f3400be9-b2d3-4873-ae58-15e6e0cc686e) on June 14, 2022\.
+ SQL Server 2012: It's no longer possible to provision new instances in any Region\. Amazon RDS is actively migrating existing instances off this version\.
+ SQL Server 2008: It's no longer possible to provision new instances in any Region\. Amazon RDS is actively migrating existing instances off this version\.

For information about licensing for SQL Server, see [Licensing Microsoft SQL Server on Amazon RDS](SQLServer.Concepts.General.Licensing.md)\. For information about SQL Server builds, see this Microsoft support article about [the latest SQL Server builds](https://support.microsoft.com/en-us/help/957826)\.

With Amazon RDS, you can create DB instances and DB snapshots, point\-in\-time restores, and automated or manual backups\. DB instances running SQL Server can be used inside a VPC\. You can also use Secure Sockets Layer \(SSL\) to connect to a DB instance running SQL Server, and you can use transparent data encryption \(TDE\) to encrypt data at rest\. Amazon RDS currently supports Multi\-AZ deployments for SQL Server using SQL Server Database Mirroring \(DBM\) or Always On Availability Groups \(AGs\) as a high\-availability, failover solution\. 

To deliver a managed service experience, Amazon RDS does not provide shell access to DB instances, and it restricts access to certain system procedures and tables that require advanced privileges\. Amazon RDS supports access to databases on a DB instance using any standard SQL client application such as Microsoft SQL Server Management Studio\. Amazon RDS does not allow direct host access to a DB instance via Telnet, Secure Shell \(SSH\), or Windows Remote Desktop Connection\. When you create a DB instance, the master user is assigned to the *db\_owner* role for all user databases on that instance, and has all database\-level permissions except for those that are used for backups\. Amazon RDS manages backups for you\. 

Before creating your first DB instance, you should complete the steps in the setting up section of this guide\. For more information, see [Setting up for Amazon RDS](CHAP_SettingUp.md)\.

**Topics**
+ [Common management tasks for Microsoft SQL Server on Amazon RDS](#SQLServer.Concepts.General)
+ [Limitations for Microsoft SQL Server DB instances](#SQLServer.Concepts.General.FeatureSupport.Limits)
+ [DB instance class support for Microsoft SQL Server](#SQLServer.Concepts.General.InstanceClasses)
+ [Microsoft SQL Server security](#SQLServer.Concepts.General.FeatureSupport.UnsupportedRoles)
+ [Compliance program support for Microsoft SQL Server DB instances](#SQLServer.Concepts.General.Compliance)
+ [SSL support for Microsoft SQL Server DB instances](#SQLServer.Concepts.General.SSL)
+ [Microsoft SQL Server versions on Amazon RDS](#SQLServer.Concepts.General.VersionSupport)
+ [Version management in Amazon RDS](#SQLServer.Concepts.General.Version-Management)
+ [Microsoft SQL Server features on Amazon RDS](#SQLServer.Concepts.General.FeatureSupport)
+ [Change data capture support for Microsoft SQL Server DB instances](#SQLServer.Concepts.General.CDC)
+ [Features not supported and features with limited support](#SQLServer.Concepts.General.FeatureNonSupport)
+ [Multi\-AZ deployments using Microsoft SQL Server Database Mirroring or Always On availability groups](#SQLServer.Concepts.General.Mirroring)
+ [Using Transparent Data Encryption to encrypt data at rest](#SQLServer.Concepts.General.Options)
+ [Functions and stored procedures for Amazon RDS for Microsoft SQL Server](#SQLServer.Concepts.General.StoredProcedures)
+ [Local time zone for Microsoft SQL Server DB instances](#SQLServer.Concepts.General.TimeZone)
+ [Licensing Microsoft SQL Server on Amazon RDS](SQLServer.Concepts.General.Licensing.md)
+ [Connecting to a DB instance running the Microsoft SQL Server database engine](USER_ConnectToMicrosoftSQLServerInstance.md)
+ [Updating applications to connect to Microsoft SQL Server DB instances using new SSL/TLS certificates](ssl-certificate-rotation-sqlserver.md)
+ [Upgrading the Microsoft SQL Server DB engine](USER_UpgradeDBInstance.SQLServer.md)
+ [Importing and exporting SQL Server databases using native backup and restore](SQLServer.Procedural.Importing.md)
+ [Working with read replicas for Microsoft SQL Server in Amazon RDS](SQLServer.ReadReplicas.md)
+ [Multi\-AZ deployments for Amazon RDS for Microsoft SQL Server](USER_SQLServerMultiAZ.md)
+ [Additional features for Microsoft SQL Server on Amazon RDS](User.SQLServer.AdditionalFeatures.md)
+ [Options for the Microsoft SQL Server database engine](Appendix.SQLServer.Options.md)
+ [Common DBA tasks for Microsoft SQL Server](Appendix.SQLServer.CommonDBATasks.md)

## Common management tasks for Microsoft SQL Server on Amazon RDS<a name="SQLServer.Concepts.General"></a>

The following are the common management tasks you perform with an Amazon RDS for SQL Server DB instance, with links to relevant documentation for each task\. 


****  

| Task area | Relevant documentation | 
| --- | --- | 
|  **Instance classes, storage, and PIOPS** If you are creating a DB instance for production purposes, you should understand how instance classes, storage types, and Provisioned IOPS work in Amazon RDS\.   |  [DB instance class support for Microsoft SQL Server](#SQLServer.Concepts.General.InstanceClasses) [Amazon RDS storage types](CHAP_Storage.md#Concepts.Storage)   | 
|  **Multi\-AZ deployments** A production DB instance should use Multi\-AZ deployments\. Multi\-AZ deployments provide increased availability, data durability, and fault tolerance for DB instances\. Multi\-AZ deployments for SQL Server are implemented using SQL Server's native DBM or AGs technology\.   |  [Multi\-AZ deployments for high availability](Concepts.MultiAZ.md) [Multi\-AZ deployments using Microsoft SQL Server Database Mirroring or Always On availability groups](#SQLServer.Concepts.General.Mirroring)  | 
|  **Amazon Virtual Private Cloud \(VPC\)** If your AWS account has a default VPC, then your DB instance is automatically created inside the default VPC\. If your account does not have a default VPC, and you want the DB instance in a VPC, you must create the VPC and subnet groups before you create the DB instance\.   | [Working with a DB instance in a VPC](USER_VPC.WorkingWithRDSInstanceinaVPC.md)  | 
|  **Security groups** By default, DB instances are created with a firewall that prevents access to them\. You therefore must create a security group with the correct IP addresses and network configuration to access the DB instance\. | [Controlling access with security groups](Overview.RDSSecurityGroups.md)   | 
|  **Parameter groups** If your DB instance is going to require specific database parameters, you should create a parameter group before you create the DB instance\.   |  [Working with parameter groups](USER_WorkingWithParamGroups.md)  | 
|  **Option groups** If your DB instance is going to require specific database options, you should create an option group before you create the DB instance\.   |  [Options for the Microsoft SQL Server database engine](Appendix.SQLServer.Options.md)  | 
|  **Connecting to your DB instance** After creating a security group and associating it to a DB instance, you can connect to the DB instance using any standard SQL client application such as Microsoft SQL Server Management Studio\.   |  [Connecting to a DB instance running the Microsoft SQL Server database engine](USER_ConnectToMicrosoftSQLServerInstance.md)  | 
|  **Backup and restore** When you create your DB instance, you can configure it to take automated backups\. You can also back up and restore your databases manually by using full backup files \(\.bak files\)\.   |  [Working with backups](USER_WorkingWithAutomatedBackups.md) [Importing and exporting SQL Server databases using native backup and restore](SQLServer.Procedural.Importing.md)  | 
|  **Monitoring** You can monitor your SQL Server DB instance by using CloudWatch Amazon RDS metrics, events, and enhanced monitoring\.   |  [Viewing metrics in the Amazon RDS console](USER_Monitoring.md) [Viewing Amazon RDS events](USER_ListEvents.md)  | 
|  **Log files** You can access the log files for your SQL Server DB instance\.   |  [Monitoring Amazon RDS log files](USER_LogAccess.md) [Microsoft SQL Server database log files](USER_LogAccess.Concepts.SQLServer.md)  | 

There are also advanced administrative tasks for working with SQL Server DB instances\. For more information, see the following documentation: 
+ [Common DBA tasks for Microsoft SQL Server](Appendix.SQLServer.CommonDBATasks.md)\.
+ [Using Windows Authentication with a SQL Server DB instance](USER_SQLServerWinAuth.md)
+ [Accessing the tempdb database](SQLServer.TempDB.md)

## Limitations for Microsoft SQL Server DB instances<a name="SQLServer.Concepts.General.FeatureSupport.Limits"></a>

The Amazon RDS implementation of Microsoft SQL Server on a DB instance has some limitations that you should be aware of:
+ The maximum number of databases supported on a DB instance depends on the instance class type and the availability mode—Single\-AZ, Multi\-AZ Database Mirroring \(DBM\), or Multi\-AZ Availability Groups \(AGs\)\. The Microsoft SQL Server system databases don't count toward this limit\. 

  The following table shows the maximum number of supported databases for each instance class type and availability mode\. Use this table to help you decide if you can move from one instance class type to another, or from one availability mode to another\. If your source DB instance has more databases than the target instance class type or availability mode can support, modifying the DB instance fails\. You can see the status of your request in the **Events** pane\.     
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_SQLServer.html)

  \* Represents the different instance class types\. 

  For example, let's say that your DB instance runs on a db\.\*\.16xlarge with Single\-AZ and that it has 76 databases\. You modify the DB instance to upgrade to using Multi\-AZ Always On AGs\. This upgrade fails, because your DB instance contains more databases than your target configuration can support\. If you upgrade your instance class type to db\.\*\.24xlarge instead, the modification succeeds\.

  If the upgrade fails, you see events and messages similar to the following:
  +  Unable to modify database instance class\. The instance has 76 databases, but after conversion it would only support 75\. 
  +  Unable to convert the DB instance to Multi\-AZ: The instance has 76 databases, but after conversion it would only support 75\. 

   If the point\-in\-time restore or snapshot restore fails, you see events and messages similar to the following:
  +  Database instance put into incompatible\-restore\. The instance has 76 databases, but after conversion it would only support 75\. 
+ Some ports are reserved for Amazon RDS, and you can't use them when you create a DB instance\.
+ Client connections from IP addresses within the range 169\.254\.0\.0/16 are not permitted\. This is the Automatic Private IP Addressing Range \(APIPA\), which is used for local\-link addressing\.
+ SQL Server Standard Edition uses only a subset of the available processors if the DB instance has more processors than the software limits \(24 cores, 4 sockets, and 128GB RAM\)\. Examples of this are the db\.m5\.24xlarge and db\.r5\.24xlarge instance classes\.

  For more information, see the table of scale limits under [Editions and supported features of SQL Server 2019 \(15\.x\)](https://docs.microsoft.com/en-us/sql/sql-server/editions-and-components-of-sql-server-version-15) in the Microsoft documentation\.
+ Amazon RDS for SQL Server doesn't support importing data into the msdb database\. 
+ You can't rename databases on a DB instance in a SQL Server Multi\-AZ deployment\.
+ Make sure that you use these guidelines when setting the following DB parameters on RDS for SQL Server:
  + `max server memory (mb)` >= 256 MB
  + `max worker threads` >= \(number of logical CPUs \* 7\)

  For more information on setting DB parameters, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\.
+ The maximum storage size for SQL Server DB instances is the following: 
  + General Purpose \(SSD\) storage – 16 TiB for all editions 
  + Provisioned IOPS storage – 16 TiB for all editions 
  + Magnetic storage – 1 TiB for all editions 

  If you have a scenario that requires a larger amount of storage, you can use sharding across multiple DB instances to get around the limit\. This approach requires data\-dependent routing logic in applications that connect to the sharded system\. You can use an existing sharding framework, or you can write custom code to enable sharding\. If you use an existing framework, the framework can't install any components on the same server as the DB instance\. 
+ The minimum storage size for SQL Server DB instances is the following:
  + General Purpose \(SSD\) storage – 20 GiB for Enterprise, Standard, Web, and Express Editions
  + Provisioned IOPS storage – 20 GiB for Enterprise, Standard, Web, and Express Editions
  + Magnetic storage – 20 GiB for Enterprise, Standard, Web, and Express Editions
+ Amazon RDS doesn't support running these services on the same server as your RDS DB instance:
  + Data Quality Services
  + Master Data Services

  To use these features, we recommend that you install SQL Server on an Amazon EC2 instance, or use an on\-premises SQL Server instance\. In these cases, the EC2 or SQL Server instance acts as the Master Data Services server for your SQL Server DB instance on Amazon RDS\. You can install SQL Server on an Amazon EC2 instance with Amazon EBS storage, pursuant to Microsoft licensing policies\.
+ Because of limitations in Microsoft SQL Server, restoring to a point in time before successfully running `DROP DATABASE` might not reflect the state of that database at that point in time\. For example, the dropped database is typically restored to its state up to 5 minutes before the `DROP DATABASE` command was issued\. This type of restore means that you can't restore the transactions made during those few minutes on your dropped database\. To work around this, you can reissue the `DROP DATABASE` command after the restore operation is completed\. Dropping a database removes the transaction logs for that database\.
+ For SQL Server, you create your databases after you create your DB instance\. Database names follow the usual SQL Server naming rules with the following differences:
  + Database names can't start with `rdsadmin`\.
  + They can't start or end with a space or a tab\.
  + They can't contain any of the characters that create a new line\.
  + They can't contain a single quote \(`'`\)\.

## DB instance class support for Microsoft SQL Server<a name="SQLServer.Concepts.General.InstanceClasses"></a>

The computation and memory capacity of a DB instance is determined by its DB instance class\. The DB instance class you need depends on your processing power and memory requirements\. For more information, see [DB instance classes](Concepts.DBInstanceClass.md)\. 

The following list of DB instance classes supported for Microsoft SQL Server is provided here for your convenience\. For the most current list, see the RDS console: [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

Not all DB instance classes are available on all supported SQL Server minor versions\. For example, some newer DB instance classes such as db\.r6i aren't available on older minor versions\. You can use the [describe\-orderable\-db\-instance\-options](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/rds/describe-orderable-db-instance-options.html) AWS CLI command to find out which DB instance classes are available for your SQL Server edition and version\.


****  

| SQL Server edition | 2019 support range | 2017 and 2016 support range | 2014 support range | 
| --- | --- | --- | --- | 
|  Enterprise Edition  |  `db.t3.xlarge`–`db.t3.2xlarge` `db.r5.xlarge`–`db.r5.24xlarge` `db.r5b.xlarge`–`db.r5b.24xlarge` `db.r5d.xlarge`–`db.r5d.24xlarge` `db.r6i.xlarge`–`db.r6i.32xlarge` `db.m5.xlarge`–`db.m5.24xlarge` `db.m5d.xlarge`–`db.m5d.24xlarge` `db.m6i.xlarge`–`db.m6i.32xlarge` `db.x1.16xlarge`–`db.x1.32xlarge` `db.x1e.xlarge`–`db.x1e.32xlarge` `db.z1d.xlarge`–`db.z1d.12xlarge`  |  `db.t3.xlarge`–`db.t3.2xlarge` `db.r3.xlarge`–`db.r3.8xlarge` `db.r4.xlarge`–`db.r4.16xlarge` `db.r5.xlarge`–`db.r5.24xlarge` `db.r5b.xlarge`–`db.r5b.24xlarge` `db.r5d.xlarge`–`db.r5d.24xlarge` `db.r6i.xlarge`–`db.r6i.32xlarge` `db.m4.xlarge`–`db.m4.16xlarge` `db.m5.xlarge`–`db.m5.24xlarge` `db.m5d.xlarge`–`db.m5d.24xlarge` `db.m6i.xlarge`–`db.m6i.32xlarge` `db.x1.16xlarge`–`db.x1.32xlarge` `db.x1e.xlarge`–`db.x1e.32xlarge` `db.z1d.xlarge`–`db.z1d.12xlarge`  |  `db.t3.xlarge`–`db.t3.2xlarge` `db.r3.xlarge`–`db.r3.8xlarge` `db.r4.xlarge`–`db.r4.8xlarge` `db.r5.xlarge`–`db.r5.24xlarge` `db.r5b.xlarge`–`db.r5b.24xlarge` `db.r5d.xlarge`–`db.r5d.24xlarge` `db.r6i.xlarge`–`db.r6i.32xlarge` `db.m4.xlarge`–`db.m4.10xlarge` `db.m5.xlarge`–`db.m5.24xlarge` `db.m5d.xlarge`–`db.m5d.24xlarge` `db.m6i.xlarge`–`db.m6i.32xlarge` `db.x1.16xlarge`–`db.x1.32xlarge`  | 
|  Standard Edition  |  `db.t3.xlarge`–`db.t3.2xlarge` `db.r5.large`–`db.r5.24xlarge` `db.r5b.large`–`db.r5b.24xlarge` `db.r5d.large`–`db.r5d.24xlarge` `db.r6i.large`–`db.r6i.8xlarge` `db.m5.large`–`db.m5.24xlarge` `db.m5d.large`–`db.m5d.24xlarge`  `db.m6i.large`–`db.m6i.8xlarge` `db.x1.16xlarge`–`db.x1.32xlarge` `db.x1e.xlarge`–`db.x1e.32xlarge` `db.z1d.large`–`db.z1d.12xlarge`  |  `db.t3.xlarge`–`db.t3.2xlarge` `db.r4.large`–`db.r4.16xlarge` `db.r5.large`–`db.r5.24xlarge` `db.r5b.large`–`db.r5b.24xlarge` `db.r5d.large`–`db.r5d.24xlarge` `db.r6i.large`–`db.r6i.8xlarge` `db.m4.large`–`db.m4.16xlarge` `db.m5.large`–`db.m5.24xlarge` `db.m5d.large`–`db.m5d.24xlarge` `db.m6i.large`–`db.m6i.8xlarge` `db.x1.16xlarge`–`db.x1.32xlarge` `db.x1e.xlarge`–`db.x1e.32xlarge` `db.z1d.large`–`db.z1d.12xlarge`  |  `db.t3.xlarge`–`db.t3.2xlarge` `db.r3.large`–`db.r3.8xlarge` `db.r4.large`–`db.r4.8xlarge` `db.r5.large`–`db.r5.24xlarge` `db.r5b.large`–`db.r5b.24xlarge` `db.r5d.large`–`db.r5d.24xlarge` `db.r6i.large`–`db.r6i.8xlarge` `db.m3.medium`–`db.m3.2xlarge` `db.m4.large`–`db.m4.10xlarge` `db.m5.large`–`db.m5.24xlarge` `db.m5d.large`–`db.m5d.24xlarge` `db.m6i.large`–`db.m6i.8xlarge` `db.x1.16xlarge`–`db.x1.32xlarge`  | 
|  Web Edition  |  `db.t3.small`–`db.t3.2xlarge` `db.r5.large`–`db.r5.4xlarge` `db.r5b.large`–`db.r5b.4xlarge` `db.r5d.large`–`db.r5d.4xlarge` `db.r6i.large`–`db.r6i.4xlarge` `db.m5.large`–`db.m5.4xlarge` `db.m5d.large`–`db.m5d.4xlarge` `db.m6i.large`–`db.m6i.4xlarge` `db.z1d.large`–`db.z1d.3xlarge`  |  `db.t2.small`–`db.t2.medium` `db.t3.small`–`db.t3.2xlarge` `db.r4.large`–`db.r4.2xlarge` `db.r5.large`–`db.r5.4xlarge` `db.r5b.large`–`db.r5b.4xlarge` `db.r5d.large`–`db.r5d.4xlarge` `db.r6i.large`–`db.r6i.4xlarge` `db.m4.large`–`db.m4.4xlarge` `db.m5.large`–`db.m5.4xlarge` `db.m5d.large`–`db.m5d.4xlarge` `db.m6i.large`–`db.m6i.4xlarge` `db.z1d.large`–`db.z1d.3xlarge`  |  `db.t2.small`–`db.t2.medium` `db.t3.small`–`db.t3.2xlarge` `db.r3.large`–`db.r3.2xlarge` `db.r4.large`–`db.r4.2xlarge` `db.r5.large`–`db.r5.4xlarge` `db.r5b.large`–`db.r5b.4xlarge` `db.r5d.large`–`db.r5d.4xlarge` `db.r6i.large`–`db.r6i.4xlarge` `db.m3.medium`–`db.m3.2xlarge` `db.m4.large`–`db.m4.4xlarge` `db.m5.large`–`db.m5.4xlarge` `db.m5d.large`–`db.m5d.4xlarge` `db.m6i.large`–`db.m6i.4xlarge`  | 
|  Express Edition  |  `db.t3.small`–`db.t3.xlarge`  |  `db.t2.micro`–`db.t2.medium` `db.t3.small`–`db.t3.xlarge`  |  `db.t2.micro`–`db.t2.medium` `db.t3.small`–`db.t3.xlarge`  | 

## Microsoft SQL Server security<a name="SQLServer.Concepts.General.FeatureSupport.UnsupportedRoles"></a>

The Microsoft SQL Server database engine uses role\-based security\. The master user name that you specify when you create a DB instance is a SQL Server Authentication login that is a member of the `processadmin`, `public`, and `setupadmin` fixed server roles\.

Any user who creates a database is assigned to the db\_owner role for that database and has all database\-level permissions except for those that are used for backups\. Amazon RDS manages backups for you\.

The following server\-level roles aren't available in Amazon RDS for SQL Server:
+ bulkadmin
+ dbcreator
+ diskadmin
+ securityadmin
+ serveradmin
+ sysadmin

The following server\-level permissions aren't available on RDS for SQL Server DB instances:
+ ALTER ANY DATABASE
+ ALTER ANY EVENT NOTIFICATION
+ ALTER RESOURCES
+ ALTER SETTINGS \(you can use the DB parameter group API operations to modify parameters; for more information, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\) 
+ AUTHENTICATE SERVER
+ CONTROL\_SERVER
+ CREATE DDL EVENT NOTIFICATION
+ CREATE ENDPOINT
+ CREATE SERVER ROLE
+ CREATE TRACE EVENT NOTIFICATION
+ DROP ANY DATABASE
+ EXTERNAL ACCESS ASSEMBLY
+ SHUTDOWN \(You can use the RDS reboot option instead\)
+ UNSAFE ASSEMBLY
+ ALTER ANY AVAILABILITY GROUP
+ CREATE ANY AVAILABILITY GROUP

## Compliance program support for Microsoft SQL Server DB instances<a name="SQLServer.Concepts.General.Compliance"></a>

AWS Services in scope have been fully assessed by a third\-party auditor and result in a certification, attestation of compliance, or Authority to Operate \(ATO\)\. For more information, see [AWS services in scope by compliance program](http://aws.amazon.com/compliance/services-in-scope/)\.

### HIPAA support for Microsoft SQL Server DB instances<a name="SQLServer.Concepts.General.HIPAA"></a>

You can use Amazon RDS for Microsoft SQL Server databases to build HIPAA\-compliant applications\. You can store healthcare\-related information, including protected health information \(PHI\), under a Business Associate Agreement \(BAA\) with AWS\. For more information, see [HIPAA compliance](http://aws.amazon.com/compliance/hipaa-compliance/)\.

Amazon RDS for SQL Server supports HIPAA for the following versions and editions:
+ SQL Server 2019 Enterprise, Standard, and Web Editions
+ SQL Server 2017 Enterprise, Standard, and Web Editions
+ SQL Server 2016 Enterprise, Standard, and Web Editions
+ SQL Server 2014 Enterprise, Standard, and Web Editions

To enable HIPAA support on your DB instance, set up the following three components\.


****  

| Component | Details | 
| --- | --- | 
|  Auditing  |  To set up auditing, set the parameter `rds.sqlserver_audit` to the value `fedramp_hipaa`\. If your DB instance is not already using a custom DB parameter group, you must create a custom parameter group and attach it to your DB instance before you can modify the `rds.sqlserver_audit` parameter\. For more information, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\.  | 
|  Transport encryption  |  To set up transport encryption, force all connections to your DB instance to use Secure Sockets Layer \(SSL\)\. For more information, see [Forcing connections to your DB instance to use SSL](SQLServer.Concepts.General.SSL.Using.md#SQLServer.Concepts.General.SSL.Forcing)\.  | 
|  Encryption at rest  |  To set up encryption at rest, you have two options: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_SQLServer.html)  | 

## SSL support for Microsoft SQL Server DB instances<a name="SQLServer.Concepts.General.SSL"></a>

You can use SSL to encrypt connections between your applications and your Amazon RDS DB instances running Microsoft SQL Server\. You can also force all connections to your DB instance to use SSL\. If you force connections to use SSL, it happens transparently to the client, and the client doesn't have to do any work to use SSL\. 

SSL is supported in all AWS Regions and for all supported SQL Server editions\. For more information, see [Using SSL with a Microsoft SQL Server DB instance](SQLServer.Concepts.General.SSL.Using.md)\. 

## Microsoft SQL Server versions on Amazon RDS<a name="SQLServer.Concepts.General.VersionSupport"></a>

You can specify any currently supported Microsoft SQL Server version when creating a new DB instance\. You can specify the Microsoft SQL Server major version \(such as Microsoft SQL Server 14\.00\), and any supported minor version for the specified major version\. If no version is specified, Amazon RDS defaults to a supported version, typically the most recent version\. If a major version is specified but a minor version is not, Amazon RDS defaults to a recent release of the major version you have specified\.

The following table shows the supported versions for all editions and all AWS Regions, except where noted\. You can also use the [https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-engine-versions.html](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-engine-versions.html) AWS CLI command to see a list of supported versions, as well as defaults for newly created DB instances\.


**SQL Server versions supported in RDS**  

| Major version | Minor version | RDS API `EngineVersion` and CLI `engine-version` | 
| --- | --- | --- | 
| SQL Server 2019 |  15\.00\.4236\.7 \(CU16\) 15\.00\.4198\.2 \(CU15\) 15\.00\.4153\.1 \(CU12\) 15\.00\.4073\.23 \(CU8\) 15\.00\.4043\.16 \(CU5\)  |  `15.00.4236.7.v1` `15.00.4198.2.v1` `15.00.4153.1.v1` `15.00.4073.23.v1` `15.00.4043.16.v1`  | 
| SQL Server 2017 |  14\.00\.3451\.2 \(CU30\) 14\.00\.3421\.10 \(CU27\) 14\.00\.3401\.7 \(CU25\) 14\.00\.3381\.3 \(CU23\) 14\.00\.3356\.20 \(CU22\) 14\.00\.3294\.2 \(CU20\) 14\.00\.3281\.6 \(CU19\)  |  `14.00.3451.2.v1` `14.00.3421.10.v1` `14.00.3401.7.v1` `14.00.3381.3.v1` `14.00.3356.20.v1` `14.00.3294.2.v1` `14.00.3281.6.v1`  | 
| SQL Server 2016 |  13\.00\.6419\.1 \(SP3 \+ Hotfix\) 13\.00\.6300\.2 \(SP3\)   |  `13.00.6419.1.v1` `13.00.6300.2.v1`   | 
| SQL Server 2014 |  12\.00\.6439\.10 \(SP3 CU4 SU\) 12\.00\.6433\.1 \(SP3 CU4 SU\) 12\.00\.6329\.1 \(SP3 CU4\) 12\.00\.6293\.0 \(SP3 CU3\)   |  `12.00.6439.10.v1` `12.00.6433.1.v1` `12.00.6329.1.v1` `12.00.6293.0.v1`   | 

## Version management in Amazon RDS<a name="SQLServer.Concepts.General.Version-Management"></a>

Amazon RDS includes flexible version management that enables you to control when and how your DB instance is patched or upgraded\. This enables you to do the following for your DB engine:
+ Maintain compatibility with database engine patch versions\.
+ Test new patch versions to verify that they work with your application before you deploy them in production\.
+ Plan and perform version upgrades to meet your service level agreements and timing requirements\.

### Microsoft SQL Server engine patching in Amazon RDS<a name="SQLServer.Concepts.General.Patching"></a>

Amazon RDS periodically aggregates official Microsoft SQL Server database patches into a DB instance engine version that's specific to Amazon RDS\. For more information about the Microsoft SQL Server patches in each engine version, see [Version and feature support on Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_SQLServer.html#SQLServer.Concepts.General.FeatureSupport)\.

Currently, you manually perform all engine upgrades on your DB instance\. For more information, see [Upgrading the Microsoft SQL Server DB engine](USER_UpgradeDBInstance.SQLServer.md)\. 

### Deprecation schedule for major engine versions of Microsoft SQL Server on Amazon RDS<a name="SQLServer.Concepts.General.Deprecated-Versions"></a>

The following table displays the planned schedule of deprecations for major engine versions of Microsoft SQL Server\.


| Date | Information | 
| --- | --- | 
| July 12, 2022 |  Microsoft will stop critical patch updates for SQL Server 2012\. For more information, see [Microsoft SQL Server 2012](https://docs.microsoft.com/en-us/lifecycle/products/microsoft-sql-server-2012) in the Microsoft documentation\.  | 
| June 1, 2022 |  Amazon RDS plans to end support of Microsoft SQL Server 2012 on RDS for SQL Server\. At that time, any remaining instances will be scheduled to migrate to SQL Server 2014 \(latest minor version available\)\. For more information, see [Announcement: Amazon RDS for SQL Server ending support for SQL Server 2012 major versions](https://repost.aws/questions/QUFNiETqrMQ_WT_AXSxOYNOA)\. To avoid an automatic upgrade from Microsoft SQL Server 2012, you can upgrade at a time that is convenient to you\. For more information, see [Upgrading a DB instance engine version](USER_UpgradeDBInstance.Upgrading.md)\.  | 
| September 1, 2021 | Amazon RDS is starting to disable the creation of new RDS for SQL Server DB instances using Microsoft SQL Server 2012\. For more information, see [Announcement: Amazon RDS for SQL Server ending support for SQL Server 2012 major versions](https://repost.aws/questions/QUFNiETqrMQ_WT_AXSxOYNOA)\. | 
| July 12, 2019 |  The Amazon RDS team deprecated support for Microsoft SQL Server 2008 R2 in June 2019\. Remaining instances of Microsoft SQL Server 2008 R2 are migrating to SQL Server 2012 \(latest minor version available\)\.  To avoid an automatic upgrade from Microsoft SQL Server 2008 R2, you can upgrade at a time that is convenient to you\. For more information, see [Upgrading a DB instance engine version](USER_UpgradeDBInstance.Upgrading.md)\.  | 
| April 25, 2019 | Before the end of April 2019, you will no longer be able to create new Amazon RDS for SQL Server database instances using Microsoft SQL Server 2008R2\. | 

## Microsoft SQL Server features on Amazon RDS<a name="SQLServer.Concepts.General.FeatureSupport"></a>

The supported SQL Server versions on Amazon RDS include the following features\. In general, a version also includes features from the previous versions, unless otherwise noted in the Microsoft documentation\.

**Topics**
+ [Microsoft SQL Server 2019 features](#SQLServer.Concepts.General.FeatureSupport.2019)
+ [Microsoft SQL Server 2017 features](#SQLServer.Concepts.General.FeatureSupport.2017)
+ [Microsoft SQL Server 2016 features](#SQLServer.Concepts.General.FeatureSupport.2016)
+ [Microsoft SQL Server 2014 features](#SQLServer.Concepts.General.FeatureSupport.2014)
+ [Microsoft SQL Server 2012 end of support on Amazon RDS](#SQLServer.Concepts.General.FeatureSupport.2012)
+ [Microsoft SQL Server 2008 R2 end of support on Amazon RDS](#SQLServer.Concepts.General.FeatureSupport.2008)

### Microsoft SQL Server 2019 features<a name="SQLServer.Concepts.General.FeatureSupport.2019"></a>

SQL Server 2019 includes many new features, such as the following: 
+ Accelerated database recovery \(ADR\) – Reduces crash recovery time after a restart or a long\-running transaction rollback\.
+ Intelligent Query Processing \(IQP\):
  + Row mode memory grant feedback – Corrects excessive grants automatically, that would otherwise result in wasted memory and reduced concurrency\.
  + Batch mode on rowstore – Enables batch mode execution for analytic workloads without requiring columnstore indexes\.
  + Table variable deferred compilation – Improves plan quality and overall performance for queries that reference table variables\.
+ Intelligent performance:
  + `OPTIMIZE_FOR_SEQUENTIAL_KEY` index option – Improves throughput for high\-concurrency inserts into indexes\.
  + Improved indirect checkpoint scalability – Helps databases with heavy DML workloads\.
  + Concurrent Page Free Space \(PFS\) updates – Enables handling as a shared latch rather than an exclusive latch\.
+ Monitoring improvements:
  + `WAIT_ON_SYNC_STATISTICS_REFRESH` wait type – Shows accumulated instance\-level time spent on synchronous statistics refresh operations\.
  + Database\-scoped configurations – Include `LIGHTWEIGHT_QUERY_PROFILING` and `LAST_QUERY_PLAN_STATS`\.
  + Dynamic management functions \(DMFs\) – Include `sys.dm_exec_query_plan_stats` and `sys.dm_db_page_info`\.
+ Verbose truncation warnings – The data truncation error message defaults to include table and column names and the truncated value\.
+ Resumable online index creation – In SQL Server 2017, only resumable online index rebuild is supported\.

For the full list of SQL Server 2019 features, see [What's new in SQL Server 2019 \(15\.x\)](https://docs.microsoft.com/en-us/sql/sql-server/what-s-new-in-sql-server-ver15) in the Microsoft documentation\.

For a list of unsupported features, see [Features not supported and features with limited support](#SQLServer.Concepts.General.FeatureNonSupport)\. 

### Microsoft SQL Server 2017 features<a name="SQLServer.Concepts.General.FeatureSupport.2017"></a>

SQL Server 2017 includes many new features, such as the following: 
+ Adaptive query processing
+ Automatic plan correction \(an automatic tuning feature\)
+ GraphDB
+ Resumable index rebuilds

For the full list of SQL Server 2017 features, see [What's new in SQL Server 2017](https://docs.microsoft.com/en-us/sql/sql-server/what-s-new-in-sql-server-2017) in the Microsoft documentation\.

For a list of unsupported features, see [Features not supported and features with limited support](#SQLServer.Concepts.General.FeatureNonSupport)\. 

### Microsoft SQL Server 2016 features<a name="SQLServer.Concepts.General.FeatureSupport.2016"></a>

Amazon RDS supports the following features of SQL Server 2016:
+ Always Encrypted
+ JSON Support
+ Operational Analytics
+ Query Store
+ Temporal Tables

For the full list of SQL Server 2016 features, see [What's new in SQL Server 2016](https://docs.microsoft.com/en-us/sql/sql-server/what-s-new-in-sql-server-2016) in the Microsoft documentation\.

### Microsoft SQL Server 2014 features<a name="SQLServer.Concepts.General.FeatureSupport.2014"></a>

In addition to supported features of SQL Server 2012, Amazon RDS supports the new query optimizer available in SQL Server 2014, and also the delayed durability feature\.

For a list of unsupported features, see [Features not supported and features with limited support](#SQLServer.Concepts.General.FeatureNonSupport)\. 

SQL Server 2014 supports all the parameters from SQL Server 2012 and uses the same default values\. SQL Server 2014 includes one new parameter, backup checksum default\. For more information, see [How to enable the CHECKSUM option if backup utilities do not expose the option](https://support.microsoft.com/en-us/kb/2656988) in the Microsoft documentation\. 

### Microsoft SQL Server 2012 end of support on Amazon RDS<a name="SQLServer.Concepts.General.FeatureSupport.2012"></a>

SQL Server 2012 has reached its end of support on Amazon RDS\.

RDS is upgrading all existing DB instances that are still using SQL Server 2012 to the latest minor version of SQL Server 2014\. For more information, see [Version management in Amazon RDS](#SQLServer.Concepts.General.Version-Management)\.

### Microsoft SQL Server 2008 R2 end of support on Amazon RDS<a name="SQLServer.Concepts.General.FeatureSupport.2008"></a>

SQL Server 2008 R2 has reached its end of support on Amazon RDS\.

RDS is upgrading all existing DB instances that are still using SQL Server 2008 R2 to the latest minor version of SQL Server 2012\. For more information, see [Version management in Amazon RDS](#SQLServer.Concepts.General.Version-Management)\.

## Change data capture support for Microsoft SQL Server DB instances<a name="SQLServer.Concepts.General.CDC"></a>

Amazon RDS supports change data capture \(CDC\) for your DB instances running Microsoft SQL Server\. CDC captures changes that are made to the data in your tables, and stores metadata about each change that you can access later\. For more information, see [Change data capture](https://docs.microsoft.com/en-us/sql/relational-databases/track-changes/track-data-changes-sql-server#Capture) in the Microsoft documentation\. 

Amazon RDS supports CDC for the following SQL Server editions and versions:
+ Microsoft SQL Server Enterprise Edition \(All versions\) 
+ Microsoft SQL Server Standard Edition: 
  + 2019
  + ea 
  + 2017
  + 2016 version 13\.00\.4422\.0 SP1 CU2 and later

To use CDC with your Amazon RDS DB instances, first enable or disable CDC at the database level by using RDS\-provided stored procedures\. After that, any user that has the `db_owner` role for that database can use the native Microsoft stored procedures to control CDC on that database\. For more information, see [Using change data capture](Appendix.SQLServer.CommonDBATasks.CDC.md)\. 

You can use CDC and AWS Database Migration Service to enable ongoing replication from SQL Server DB instances\.  

## Features not supported and features with limited support<a name="SQLServer.Concepts.General.FeatureNonSupport"></a>

The following Microsoft SQL Server features aren't supported on Amazon RDS: 
+ Backing up to Microsoft Azure Blob Storage
+ Buffer pool extension
+ Custom password policies
+ Data Quality Services
+ Database Log Shipping
+ Database snapshots \(Amazon RDS supports only DB instance snapshots\)
+ Extended stored procedures, including xp\_cmdshell
+ FILESTREAM support
+ File tables
+ Machine Learning and R Services \(requires OS access to install it\)
+ Maintenance plans
+ Performance Data Collector
+ Policy\-Based Management
+ PolyBase
+ Replication
+ Resource Governor
+ Server\-level triggers
+ Service Broker endpoints
+ Stretch database
+ TRUSTWORTHY database property \(requires sysadmin role\)
+ T\-SQL endpoints \(all operations using CREATE ENDPOINT are unavailable\)
+ WCF Data Services

The following Microsoft SQL Server features have limited support on Amazon RDS:
+ Distributed queries/linked servers\. For more information, see [Implement linked servers with Amazon RDS for Microsoft SQL Server](http://aws.amazon.com/blogs/database/implement-linked-servers-with-amazon-rds-for-microsoft-sql-server/)\.
+ Common Runtime Language \(CLR\)\. On RDS for SQL Server 2016 and lower versions, CLR is supported in `SAFE` mode and using assembly bits only\. CLR isn't supported on RDS for SQL Server 2017 and higher versions\. For more information, see [Common Runtime Language Integration](https://docs.microsoft.com/en-us/sql/relational-databases/clr-integration/common-language-runtime-integration-overview) in the Microsoft documentation\.

## Multi\-AZ deployments using Microsoft SQL Server Database Mirroring or Always On availability groups<a name="SQLServer.Concepts.General.Mirroring"></a>

Amazon RDS supports Multi\-AZ deployments for DB instances running Microsoft SQL Server by using SQL Server Database Mirroring \(DBM\) or Always On Availability Groups \(AGs\)\. Multi\-AZ deployments provide increased availability, data durability, and fault tolerance for DB instances\. In the event of planned database maintenance or unplanned service disruption, Amazon RDS automatically fails over to the up\-to\-date secondary replica so database operations can resume quickly without manual intervention\. The primary and secondary instances use the same endpoint, whose physical network address transitions to the passive secondary replica as part of the failover process\. You don't have to reconfigure your application when a failover occurs\. 

Amazon RDS manages failover by actively monitoring your Multi\-AZ deployment and initiating a failover when a problem with your primary occurs\. Failover doesn't occur unless the standby and primary are fully in sync\. Amazon RDS actively maintains your Multi\-AZ deployment by automatically repairing unhealthy DB instances and re\-establishing synchronous replication\. You don't have to manage anything\. Amazon RDS handles the primary, the witness, and the standby instance for you\. When you set up SQL Server Multi\-AZ, RDS configures passive secondary instances for all of the databases on the instance\. 

For more information, see [Multi\-AZ deployments for Amazon RDS for Microsoft SQL Server](USER_SQLServerMultiAZ.md)\. 

## Using Transparent Data Encryption to encrypt data at rest<a name="SQLServer.Concepts.General.Options"></a>

Amazon RDS supports Microsoft SQL Server Transparent Data Encryption \(TDE\), which transparently encrypts stored data\. Amazon RDS uses option groups to enable and configure these features\. For more information about the TDE option, see [Support for Transparent Data Encryption in SQL Server](Appendix.SQLServer.Options.TDE.md)\. 

## Functions and stored procedures for Amazon RDS for Microsoft SQL Server<a name="SQLServer.Concepts.General.StoredProcedures"></a>

Following, you can find a list of the Amazon RDS functions and stored procedures that help automate SQL Server tasks\. 

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_SQLServer.html)

## Local time zone for Microsoft SQL Server DB instances<a name="SQLServer.Concepts.General.TimeZone"></a>

The time zone of an Amazon RDS DB instance running Microsoft SQL Server is set by default\. The current default is Coordinated Universal Time \(UTC\)\. You can set the time zone of your DB instance to a local time zone instead, to match the time zone of your applications\.

You set the time zone when you first create your DB instance\. You can create your DB instance by using the [AWS Management Console](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_CreateDBInstance.html), the Amazon RDS API [CreateDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html.html) action, or the AWS CLI [create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) command\.

If your DB instance is part of a Multi\-AZ deployment \(using SQL Server DBM or AGs\), then when you fail over, your time zone remains the local time zone that you set\. For more information, see [Multi\-AZ deployments using Microsoft SQL Server Database Mirroring or Always On availability groups](#SQLServer.Concepts.General.Mirroring)\.

When you request a point\-in\-time restore, you specify the time to restore to\. The time is shown in your local time zone\. For more information, see [Restoring a DB instance to a specified time](USER_PIT.md)\. 

The following are limitations to setting the local time zone on your DB instance:
+ You can't modify the time zone of an existing SQL Server DB instance\. 
+ You can't restore a snapshot from a DB instance in one time zone to a DB instance in a different time zone\. 
+ We strongly recommend that you don't restore a backup file from one time zone to a different time zone\. If you restore a backup file from one time zone to a different time zone, you must audit your queries and applications for the effects of the time zone change\. For more information, see [Importing and exporting SQL Server databases using native backup and restore](SQLServer.Procedural.Importing.md)\. 

### Supported time zones<a name="SQLServer.Concepts.General.TimeZone.Zones"></a>

You can set your local time zone to one of the values listed in the following table\.


**Time zones supported for Amazon RDS on SQL Server**  

| Time zone | Standard time offset | Description | Notes | 
| --- | --- | --- | --- | 
| Afghanistan Standard Time | \(UTC\+04:30\) | Kabul | This time zone doesn't observe daylight saving time\. | 
| Alaskan Standard Time | \(UTC–09:00\) | Alaska |  | 
| Aleutian Standard Time | \(UTC–10:00\) | Aleutian Islands |  | 
| Altai Standard Time | \(UTC\+07:00\) | Barnaul, Gorno\-Altaysk |  | 
| Arab Standard Time | \(UTC\+03:00\) | Kuwait, Riyadh | This time zone doesn't observe daylight saving time\. | 
| Arabian Standard Time | \(UTC\+04:00\) | Abu Dhabi, Muscat |  | 
| Arabic Standard Time | \(UTC\+03:00\) | Baghdad | This time zone doesn't observe daylight saving time\. | 
| Argentina Standard Time | \(UTC–03:00\) | City of Buenos Aires | This time zone doesn't observe daylight saving time\. | 
| Astrakhan Standard Time | \(UTC\+04:00\) | Astrakhan, Ulyanovsk |  | 
| Atlantic Standard Time | \(UTC–04:00\) | Atlantic Time \(Canada\) |  | 
| AUS Central Standard Time | \(UTC\+09:30\) | Darwin | This time zone doesn't observe daylight saving time\. | 
| Aus Central W\. Standard Time | \(UTC\+08:45\) | Eucla |  | 
| AUS Eastern Standard Time | \(UTC\+10:00\) | Canberra, Melbourne, Sydney |  | 
| Azerbaijan Standard Time | \(UTC\+04:00\) | Baku |  | 
| Azores Standard Time | \(UTC–01:00\) | Azores |  | 
| Bahia Standard Time | \(UTC–03:00\) | Salvador |  | 
| Bangladesh Standard Time | \(UTC\+06:00\) | Dhaka | This time zone doesn't observe daylight saving time\. | 
| Belarus Standard Time | \(UTC\+03:00\) | Minsk | This time zone doesn't observe daylight saving time\. | 
| Bougainville Standard Time | \(UTC\+11:00\) | Bougainville Island |  | 
| Canada Central Standard Time | \(UTC–06:00\) | Saskatchewan | This time zone doesn't observe daylight saving time\. | 
| Cape Verde Standard Time | \(UTC–01:00\) | Cabo Verde Is\. | This time zone doesn't observe daylight saving time\. | 
| Caucasus Standard Time | \(UTC\+04:00\) | Yerevan |  | 
| Cen\. Australia Standard Time | \(UTC\+09:30\) | Adelaide |  | 
| Central America Standard Time | \(UTC–06:00\) | Central America | This time zone doesn't observe daylight saving time\. | 
| Central Asia Standard Time | \(UTC\+06:00\) | Astana | This time zone doesn't observe daylight saving time\. | 
| Central Brazilian Standard Time | \(UTC–04:00\) | Cuiaba |  | 
| Central Europe Standard Time | \(UTC\+01:00\) | Belgrade, Bratislava, Budapest, Ljubljana, Prague |  | 
| Central European Standard Time | \(UTC\+01:00\) | Sarajevo, Skopje, Warsaw, Zagreb |  | 
| Central Pacific Standard Time | \(UTC\+11:00\) | Solomon Islands, New Caledonia | This time zone doesn't observe daylight saving time\. | 
| Central Standard Time | \(UTC–06:00\) | Central Time \(US and Canada\) |  | 
| Central Standard Time \(Mexico\) | \(UTC–06:00\) | Guadalajara, Mexico City, Monterrey |  | 
| Chatham Islands Standard Time | \(UTC\+12:45\) | Chatham Islands |  | 
| China Standard Time | \(UTC\+08:00\) | Beijing, Chongqing, Hong Kong, Urumqi | This time zone doesn't observe daylight saving time\. | 
| Cuba Standard Time | \(UTC–05:00\) | Havana |  | 
| Dateline Standard Time | \(UTC–12:00\) | International Date Line West | This time zone doesn't observe daylight saving time\. | 
| E\. Africa Standard Time | \(UTC\+03:00\) | Nairobi | This time zone doesn't observe daylight saving time\. | 
| E\. Australia Standard Time | \(UTC\+10:00\) | Brisbane | This time zone doesn't observe daylight saving time\. | 
| E\. Europe Standard Time | \(UTC\+02:00\) | Chisinau |  | 
| E\. South America Standard Time | \(UTC–03:00\) | Brasilia |  | 
| Easter Island Standard Time | \(UTC–06:00\) | Easter Island |  | 
| Eastern Standard Time | \(UTC–05:00\) | Eastern Time \(US and Canada\) |  | 
| Eastern Standard Time \(Mexico\) | \(UTC–05:00\) | Chetumal |  | 
| Egypt Standard Time | \(UTC\+02:00\) | Cairo |  | 
| Ekaterinburg Standard Time | \(UTC\+05:00\) | Ekaterinburg |  | 
| Fiji Standard Time | \(UTC\+12:00\) | Fiji |  | 
| FLE Standard Time | \(UTC\+02:00\) | Helsinki, Kyiv, Riga, Sofia, Tallinn, Vilnius |  | 
| Georgian Standard Time | \(UTC\+04:00\) | Tbilisi | This time zone doesn't observe daylight saving time\. | 
| GMT Standard Time | \(UTC\) | Dublin, Edinburgh, Lisbon, London |  This time zone isn't the same as Greenwich Mean Time\. This time zone does observe daylight saving time\. | 
| Greenland Standard Time | \(UTC–03:00\) | Greenland |  | 
| Greenwich Standard Time | \(UTC\) | Monrovia, Reykjavik | This time zone doesn't observe daylight saving time\. | 
| GTB Standard Time | \(UTC\+02:00\) | Athens, Bucharest |  | 
| Haiti Standard Time | \(UTC–05:00\) | Haiti |  | 
| Hawaiian Standard Time | \(UTC–10:00\) | Hawaii |  | 
| India Standard Time | \(UTC\+05:30\) | Chennai, Kolkata, Mumbai, New Delhi | This time zone doesn't observe daylight saving time\. | 
| Iran Standard Time | \(UTC\+03:30\) | Tehran |  | 
| Israel Standard Time | \(UTC\+02:00\) | Jerusalem |  | 
| Jordan Standard Time | \(UTC\+02:00\) | Amman |  | 
| Kaliningrad Standard Time | \(UTC\+02:00\) | Kaliningrad |  | 
| Kamchatka Standard Time | \(UTC\+12:00\) | Petropavlovsk\-Kamchatsky – Old |  | 
| Korea Standard Time | \(UTC\+09:00\) | Seoul | This time zone doesn't observe daylight saving time\. | 
| Libya Standard Time | \(UTC\+02:00\) | Tripoli |  | 
| Line Islands Standard Time | \(UTC\+14:00\) | Kiritimati Island |  | 
| Lord Howe Standard Time | \(UTC\+10:30\) | Lord Howe Island |  | 
| Magadan Standard Time | \(UTC\+11:00\) | Magadan | This time zone doesn't observe daylight saving time\. | 
| Magallanes Standard Time | \(UTC–03:00\) | Punta Arenas |  | 
| Marquesas Standard Time | \(UTC–09:30\) | Marquesas Islands |  | 
| Mauritius Standard Time | \(UTC\+04:00\) | Port Louis | This time zone doesn't observe daylight saving time\. | 
| Middle East Standard Time | \(UTC\+02:00\) | Beirut |  | 
| Montevideo Standard Time | \(UTC–03:00\) | Montevideo |  | 
| Morocco Standard Time | \(UTC\+01:00\) | Casablanca |  | 
| Mountain Standard Time | \(UTC–07:00\) | Mountain Time \(US and Canada\) |  | 
| Mountain Standard Time \(Mexico\) | \(UTC–07:00\) | Chihuahua, La Paz, Mazatlan |  | 
| Myanmar Standard Time | \(UTC\+06:30\) | Yangon \(Rangoon\) | This time zone doesn't observe daylight saving time\. | 
| N\. Central Asia Standard Time | \(UTC\+07:00\) | Novosibirsk |  | 
| Namibia Standard Time | \(UTC\+02:00\) | Windhoek |  | 
| Nepal Standard Time | \(UTC\+05:45\) | Kathmandu | This time zone doesn't observe daylight saving time\. | 
| New Zealand Standard Time | \(UTC\+12:00\) | Auckland, Wellington |  | 
| Newfoundland Standard Time | \(UTC–03:30\) | Newfoundland |  | 
| Norfolk Standard Time | \(UTC\+11:00\) | Norfolk Island |  | 
| North Asia East Standard Time | \(UTC\+08:00\) | Irkutsk |  | 
| North Asia Standard Time | \(UTC\+07:00\) | Krasnoyarsk |  | 
| North Korea Standard Time | \(UTC\+09:00\) | Pyongyang |  | 
| Omsk Standard Time | \(UTC\+06:00\) | Omsk |  | 
| Pacific SA Standard Time | \(UTC–03:00\) | Santiago |  | 
| Pacific Standard Time | \(UTC–08:00\) | Pacific Time \(US and Canada\) |  | 
| Pacific Standard Time \(Mexico\) | \(UTC–08:00\) | Baja California |  | 
| Pakistan Standard Time | \(UTC\+05:00\) | Islamabad, Karachi | This time zone doesn't observe daylight saving time\. | 
| Paraguay Standard Time | \(UTC–04:00\) | Asuncion |  | 
| Romance Standard Time | \(UTC\+01:00\) | Brussels, Copenhagen, Madrid, Paris |  | 
| Russia Time Zone 10 | \(UTC\+11:00\) | Chokurdakh |  | 
| Russia Time Zone 11 | \(UTC\+12:00\) | Anadyr, Petropavlovsk\-Kamchatsky |  | 
| Russia Time Zone 3 | \(UTC\+04:00\) | Izhevsk, Samara |  | 
| Russian Standard Time | \(UTC\+03:00\) | Moscow, St\. Petersburg, Volgograd | This time zone doesn't observe daylight saving time\. | 
| SA Eastern Standard Time | \(UTC–03:00\) | Cayenne, Fortaleza | This time zone doesn't observe daylight saving time\. | 
| SA Pacific Standard Time | \(UTC–05:00\) | Bogota, Lima, Quito, Rio Branco |  This time zone doesn't observe daylight saving time\. | 
| SA Western Standard Time | \(UTC–04:00\) | Georgetown, La Paz, Manaus, San Juan | This time zone doesn't observe daylight saving time\. | 
| Saint Pierre Standard Time | \(UTC–03:00\) | Saint Pierre and Miquelon |  | 
| Sakhalin Standard Time | \(UTC\+11:00\) | Sakhalin |  | 
| Samoa Standard Time | \(UTC\+13:00\) | Samoa |  | 
| Sao Tome Standard Time | \(UTC\+01:00\) | Sao Tome |  | 
| Saratov Standard Time | \(UTC\+04:00\) | Saratov |  | 
| SE Asia Standard Time | \(UTC\+07:00\) | Bangkok, Hanoi, Jakarta | This time zone doesn't observe daylight saving time\. | 
| Singapore Standard Time | \(UTC\+08:00\) | Kuala Lumpur, Singapore | This time zone doesn't observe daylight saving time\. | 
| South Africa Standard Time | \(UTC\+02:00\) | Harare, Pretoria | This time zone doesn't observe daylight saving time\. | 
| Sri Lanka Standard Time | \(UTC\+05:30\) | Sri Jayawardenepura | This time zone doesn't observe daylight saving time\. | 
| Sudan Standard Time | \(UTC\+02:00\) | Khartoum |  | 
| Syria Standard Time | \(UTC\+02:00\) | Damascus |  | 
| Taipei Standard Time | \(UTC\+08:00\) | Taipei | This time zone doesn't observe daylight saving time\. | 
| Tasmania Standard Time | \(UTC\+10:00\) | Hobart |  | 
| Tocantins Standard Time | \(UTC–03:00\) | Araguaina |  | 
| Tokyo Standard Time | \(UTC\+09:00\) | Osaka, Sapporo, Tokyo | This time zone doesn't observe daylight saving time\. | 
| Tomsk Standard Time | \(UTC\+07:00\) | Tomsk |  | 
| Tonga Standard Time | \(UTC\+13:00\) | Nuku'alofa | This time zone doesn't observe daylight saving time\. | 
| Transbaikal Standard Time | \(UTC\+09:00\) | Chita |  | 
| Turkey Standard Time | \(UTC\+03:00\) | Istanbul |  | 
| Turks And Caicos Standard Time | \(UTC–05:00\) | Turks and Caicos |  | 
| Ulaanbaatar Standard Time | \(UTC\+08:00\) | Ulaanbaatar | This time zone doesn't observe daylight saving time\. | 
| US Eastern Standard Time | \(UTC–05:00\) | Indiana \(East\) |  | 
| US Mountain Standard Time | \(UTC–07:00\) | Arizona | This time zone doesn't observe daylight saving time\. | 
| UTC | UTC | Coordinated Universal Time | This time zone doesn't observe daylight saving time\. | 
| UTC–02 | \(UTC–02:00\) | Coordinated Universal Time–02 | This time zone doesn't observe daylight saving time\. | 
| UTC–08 | \(UTC–08:00\) | Coordinated Universal Time–08 |  | 
| UTC–09 | \(UTC–09:00\) | Coordinated Universal Time–09 |  | 
| UTC–11 | \(UTC–11:00\) | Coordinated Universal Time–11 | This time zone doesn't observe daylight saving time\. | 
| UTC\+12 | \(UTC\+12:00\) | Coordinated Universal Time\+12 | This time zone doesn't observe daylight saving time\. | 
| UTC\+13 | \(UTC\+13:00\) | Coordinated Universal Time\+13 |  | 
| Venezuela Standard Time | \(UTC–04:00\) | Caracas | This time zone doesn't observe daylight saving time\. | 
| Vladivostok Standard Time | \(UTC\+10:00\) | Vladivostok |  | 
| Volgograd Standard Time | \(UTC\+04:00\) | Volgograd |  | 
| W\. Australia Standard Time | \(UTC\+08:00\) | Perth | This time zone doesn't observe daylight saving time\. | 
| W\. Central Africa Standard Time | \(UTC\+01:00\) | West Central Africa | This time zone doesn't observe daylight saving time\. | 
| W\. Europe Standard Time | \(UTC\+01:00\) | Amsterdam, Berlin, Bern, Rome, Stockholm, Vienna |  | 
| W\. Mongolia Standard Time | \(UTC\+07:00\) | Hovd |  | 
| West Asia Standard Time | \(UTC\+05:00\) | Ashgabat, Tashkent | This time zone doesn't observe daylight saving time\. | 
| West Bank Standard Time | \(UTC\+02:00\) | Gaza, Hebron |  | 
| West Pacific Standard Time | \(UTC\+10:00\) | Guam, Port Moresby | This time zone doesn't observe daylight saving time\. | 
| Yakutsk Standard Time | \(UTC\+09:00\) | Yakutsk |  | 