# Microsoft SQL Server on Amazon RDS<a name="CHAP_SQLServer"></a>

Amazon RDS supports DB instances running several versions and editions of Microsoft SQL Server\. The most recent supported version of each major version is shown following\. For the full list of supported versions, editions, and RDS engine versions, see [Version and Feature Support on Amazon RDS](#SQLServer.Concepts.General.FeatureSupport)\. 
+ SQL Server 2017 RTM CU13 14\.00\.3049\.1, released per [KB4466404](https://support.microsoft.com/en-us/help/4483666/on-demand-hotfix-update-package-for-sql-server-2017-cu13) on January 01, 2019\.
+ SQL Server 2016 SP2 CU8 13\.00\.5426\.0, released per [KB4505830](https://support.microsoft.com/en-us/help/4505830/cumulative-update-8-for-sql-server-2016-sp2) on July 31, 2019\.
+ SQL Server 2014 SP2 CU10 12\.00\.5571\.0, released per [KB4052725](https://support.microsoft.com/en-us/help/2936603/sql-server-2014-build-versions) on January 16, 2018\.
+ SQL Server 2012 SP4 GDR 11\.00\.7462\.6, released per [KB4057116](https://support.microsoft.com/en-us/help/4057116/security-update-for-vulnerabilities-in-sql-server) on January 12, 2017\.
+ SQL Server 2008: It is no longer possible to provision new instances in any region\. Amazon RDS is actively migrating existing instances off this version\. 

For information about licensing for SQL Server, see [Licensing Microsoft SQL Server on Amazon RDS](SQLServer.Concepts.General.Licensing.md)\. For information about SQL Server builds, see this Microsoft support article about [the latest SQL Server builds](https://support.microsoft.com/en-us/help/957826)\.

With Amazon RDS, you can create DB instances and DB snapshots, point\-in\-time restores, and automated or manual backups\. DB instances running SQL Server can be used inside a VPC\. You can also use SSL to connect to a DB instance running SQL Server, and you can use TDE to encrypt data at rest\. Amazon RDS currently supports Multi\-AZ deployments for SQL Server using SQL Server Database Mirroring \(DBM\) or Always On Availability Groups \(AGs\) as a high\-availability, failover solution\. 

In order to deliver a managed service experience, Amazon RDS does not provide shell access to DB instances, and it restricts access to certain system procedures and tables that require advanced privileges\. Amazon RDS supports access to databases on a DB instance using any standard SQL client application such as Microsoft SQL Server Management Studio\. Amazon RDS does not allow direct host access to a DB instance via Telnet, Secure Shell \(SSH\), or Windows Remote Desktop Connection\. When you create a DB instance, the master user is assigned to the *db\_owner* role for all user databases on that instance, and has all database\-level permissions except for those that are used for backups\. Amazon RDS manages backups for you\. 

Before creating your first DB instance, you should complete the steps in the setting up section of this guide\. For more information, see [Setting Up for Amazon RDS](CHAP_SettingUp.md)\. 

## Common Management Tasks for Microsoft SQL Server on Amazon RDS<a name="SQLServer.Concepts.General"></a>

The following are the common management tasks you perform with an Amazon RDS SQL Server DB instance, with links to relevant documentation for each task\. 


****  

| Task Area | Relevant Documentation | 
| --- | --- | 
|  **Instance Classes, Storage, and PIOPS** If you are creating a DB instance for production purposes, you should understand how instance classes, storage types, and Provisioned IOPS work in Amazon RDS\.   |  [DB Instance Class Support for Microsoft SQL Server](#SQLServer.Concepts.General.InstanceClasses) [Amazon RDS Storage Types](CHAP_Storage.md#Concepts.Storage)   | 
|  **Multi\-AZ Deployments** A production DB instance should use Multi\-AZ deployments\. Multi\-AZ deployments provide increased availability, data durability, and fault tolerance for DB instances\. Multi\-AZ deployments for SQL Server are implemented using SQL Server’s native DBM or AGs technology\.   |  [High Availability \(Multi\-AZ\) for Amazon RDS](Concepts.MultiAZ.md) [Multi\-AZ Deployments Using Microsoft SQL Server Database Mirroring or Always On Availability Groups ](#SQLServer.Concepts.General.Mirroring)  | 
|  **Amazon Virtual Private Cloud \(VPC\)** If your AWS account has a default VPC, then your DB instance is automatically created inside the default VPC\. If your account does not have a default VPC, and you want the DB instance in a VPC, you must create the VPC and subnet groups before you create the DB instance\.   |  [Determining Whether You Are Using the EC2\-VPC or EC2\-Classic Platform](USER_VPC.FindDefaultVPC.md) [Working with a DB Instance in a VPC](USER_VPC.WorkingWithRDSInstanceinaVPC.md)  | 
|  **Security Groups** By default, DB instances are created with a firewall that prevents access to them\. You therefore must create a security group with the correct IP addresses and network configuration to access the DB instance\. The security group you create depends on what Amazon EC2 platform your DB instance is on, and whether you will access your DB instance from an Amazon EC2 instance\.   In general, if your DB instance is on the *EC2\-Classic* platform, you will need to create a DB security group; if your DB instance is on the *EC2\-VPC* platform, you will need to create a VPC security group\.   |  [Determining Whether You Are Using the EC2\-VPC or EC2\-Classic Platform](USER_VPC.FindDefaultVPC.md) [Controlling Access with Security Groups](Overview.RDSSecurityGroups.md)   | 
|  **Parameter Groups** If your DB instance is going to require specific database parameters, you should create a parameter group before you create the DB instance\.   |  [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)  | 
|  **Option Groups** If your DB instance is going to require specific database options, you should create an option group before you create the DB instance\.   |  [Options for the Microsoft SQL Server Database Engine](Appendix.SQLServer.Options.md)  | 
|  **Connecting to Your DB Instance** After creating a security group and associating it to a DB instance, you can connect to the DB instance using any standard SQL client application such as Microsoft SQL Server Management Studio\.   |  [Connecting to a DB Instance Running the Microsoft SQL Server Database Engine](USER_ConnectToMicrosoftSQLServerInstance.md)  | 
|  **Backup and Restore** When you create your DB instance, you can configure it to take automated backups\. You can also back up and restore your databases manually by using full backup files \(\.bak files\)\.   |  [Working With Backups](USER_WorkingWithAutomatedBackups.md) [Importing and Exporting SQL Server Databases](SQLServer.Procedural.Importing.md)  | 
|  **Monitoring** You can monitor your SQL Server DB instance by using CloudWatch Amazon RDS metrics, events, and enhanced monitoring\.   |  [Viewing DB Instance Metrics](MonitoringOverview.md#USER_Monitoring) [Viewing Amazon RDS Events](USER_ListEvents.md)  | 
|  **Log Files** You can access the log files for your SQL Server DB instance\.   |  [Amazon RDS Database Log Files](USER_LogAccess.md) [Microsoft SQL Server Database Log Files](USER_LogAccess.Concepts.SQLServer.md)  | 

There are also advanced administrative tasks for working with SQL Server DB instances\. For more information, see the following documentation: 
+ [Common DBA Tasks for Microsoft SQL Server](Appendix.SQLServer.CommonDBATasks.md)\.
+ [Using Windows Authentication with a SQL Server DB Instance](USER_SQLServerWinAuth.md)
+ [Accessing the tempdb Database](SQLServer.TempDB.md)

## Limits for Microsoft SQL Server DB Instances<a name="SQLServer.Concepts.General.FeatureSupport.Limits"></a>

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
+ SQL Server Standard Edition will use only a subset of the available processors if the DB instance has more processors than the software limits \(24 cores, 4 sockets, and 128GB RAM\)\. Examples of this are the db\.m5\.24xlarge and db\.r5\.24xlarge instance classes\.
+ Amazon RDS for SQL Server doesn't support importing data into the msdb database\. 
+ You can't rename databases on a DB instance in a SQL Server Multi\-AZ deployment\. 
+ The maximum storage size for SQL Server DB instances is the following: 
  + General Purpose \(SSD\) storage – 16 TiB for all editions 
  + Provisioned IOPS storage – 16 TiB for all editions 
  + Magnetic storage – 1 TiB for all editions 

  If you have a scenario that requires a larger amount of storage, you can use sharding across multiple DB instances to get around the limit\. This approach requires data\-dependent routing logic in applications that connect to the sharded system\. You can use an existing sharding framework, or you can write custom code to enable sharding\. If you use an existing framework, the framework can't install any components on the same server as the DB instance\. 
+ The minimum storage size for SQL Server DB instances is the following: 
  + General Purpose \(SSD\) storage – 20 GiB for Enterprise, Standard, Web, and Express editions 
  + Provisioned IOPS storage – 20 GiB for Enterprise and Standard editions, 100 GiB for Web and Express editions 
  + Magnetic storage – 200 GiB for Enterprise and Standard editions, 20 GiB for Web and Express editions 
+ Amazon RDS doesn't support running these services on the same server as your Amazon RDS DB instance:
  + SQL Server Analysis Services
  + SQL Server Integration Services
  + SQL Server Reporting Services
  + Data Quality Services
  + Master Data Services

  To use these features, we recommend that you install SQL Server on an Amazon EC2 instance, or use an on\-premises SQL Server instance\. In these cases, the EC2 or SQL Server instance acts as the Reporting, Analysis, Integration, or Master Data Services server for your SQL Server DB instance on Amazon RDS\. You can install SQL Server on an Amazon EC2 instance with Amazon EBS storage, pursuant to Microsoft licensing policies\. 
+ Because of limitations in Microsoft SQL Server, restoring to a point in time before successful execution of DROP DATABASE might not reflect the state of that database at that point in time\. For example, the dropped database is typically restored to its state up to 5 minutes before the DROP DATABASE command was issued\. This type of restore means that you can't restore the transactions made during those few minutes on your dropped database\. To work around this, you can reissue the DROP DATABASE command after the restore operation is completed\. Dropping a database removes the transaction logs for that database\. 

## DB Instance Class Support for Microsoft SQL Server<a name="SQLServer.Concepts.General.InstanceClasses"></a>

The computation and memory capacity of a DB instance is determined by its DB instance class\. The DB instance class you need depends on your processing power and memory requirements\. For more information, see [Choosing the DB Instance Class](Concepts.DBInstanceClass.md)\. 

The following list of DB instance classes supported for Microsoft SQL Server is provided here for your convenience\. For the most current list, see the RDS console: [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\. 


****  

| SQL Server Edition | 2017 and 2016 Support Range | 2014 and 2012 Support Range | 
| --- | --- | --- | 
|  Enterprise Edition  |  `db.t3.xlarge`–`db.t3.2xlarge` `db.r3.xlarge`–`db.r3.8xlarge` `db.r4.xlarge`–`db.r4.16xlarge` `db.r5.xlarge`–`db.r5.24xlarge` `db.m4.xlarge`–`db.m4.16xlarge` `db.m5.xlarge`–`db.m5.24xlarge` `db.x1.16xlarge`–`db.x1.32xlarge` `db.x1e.xlarge`–`db.x1e.32xlarge`  |  `db.t3.xlarge`–`db.t3.2xlarge` `db.r3.xlarge`–`db.r3.8xlarge` `db.r4.xlarge`–`db.r4.8xlarge` `db.r5.xlarge`–`db.r5.24xlarge` `db.m4.xlarge`–`db.m4.10xlarge` `db.m5.xlarge`–`db.m5.24xlarge` `db.x1.16xlarge`–`db.x1.32xlarge` `db.x1e.xlarge`–`db.x1e.32xlarge`  | 
|  Standard Edition  |  `db.t3.xlarge`–`db.t3.2xlarge` `db.r4.large`–`db.r4.16xlarge` `db.r5.large`–`db.r5.24xlarge` `db.m4.large`–`db.m4.16xlarge` `db.m5.large`–`db.m5.24xlarge` `db.x1.16xlarge`–`db.x1.32xlarge` `db.x1e.xlarge`–`db.x1e.32xlarge`  |  `db.t3.xlarge`–`db.t3.2xlarge` `db.r3.large`–`db.r3.8xlarge` `db.r4.large`–`db.r4.8xlarge` `db.r5.large`–`db.r5.24xlarge` `db.m3.medium`–`db.m3.2xlarge` `db.m4.large`–`db.m4.10xlarge` `db.m5.large`–`db.m5.24xlarge` `db.x1.16xlarge`–`db.x1.32xlarge` `db.x1e.xlarge`–`db.x1e.32xlarge`  | 
|  Web Edition  |  `db.t2.small`–`db.t2.medium` `db.t3.small`–`db.t3.2xlarge` `db.r4.large`–`db.r4.2xlarge` `db.r5.large`–`db.r5.4xlarge` `db.m4.large`–`db.m4.4xlarge` `db.m5.large`–`db.m5.4xlarge`  |  `db.t2.small`–`db.t2.medium` `db.t3.small`–`db.t3.2xlarge` `db.r3.large`–`db.r3.2xlarge` `db.r4.large`–`db.r4.2xlarge` `db.r5.large`–`db.r5.4xlarge` `db.m3.medium`–`db.m3.2xlarge` `db.m4.large`–`db.m4.4xlarge` `db.m5.large`–`db.m5.4xlarge`  | 
|  Express Edition  |  `db.t2.micro`–`db.t2.medium` `db.t3.small`–`db.t3.xlarge`  |  `db.t2.micro`–`db.t2.medium` `db.t3.small`–`db.t3.xlarge`  | 

## Microsoft SQL Server Security<a name="SQLServer.Concepts.General.FeatureSupport.UnsupportedRoles"></a>

The Microsoft SQL Server database engine uses role\-based security\. The master user name you use when you create a DB instance is a SQL Server Authentication login that is a member of the `processadmin`, `public`, and `setupadmin` fixed server roles\. 

Any user who creates a database is assigned to the db\_owner role for that database and has all database\-level permissions except for those that are used for backups\. Amazon RDS manages backups for you\. 

The following server\-level roles are not currently available in Amazon RDS: 
+ bulkadmin
+ dbcreator
+ diskadmin
+ securityadmin
+ serveradmin
+ sysadmin

The following server\-level permissions are not available on SQL Server DB instances: 
+ ALTER ANY CREDENTIAL
+ ALTER ANY EVENT NOTIFICATION
+ ALTER ANY EVENT SESSION
+ ALTER RESOURCES
+ ALTER SETTINGS \(you can use the DB parameter group API operations to modify parameters; for more information, see [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)\) 
+ AUTHENTICATE SERVER
+ CONTROL\_SERVER
+ CREATE DDL EVENT NOTIFICATION
+ CREATE ENDPOINT
+ CREATE TRACE EVENT NOTIFICATION
+ EXTERNAL ACCESS ASSEMBLY
+ SHUTDOWN \(You can use the RDS reboot option instead\)
+ UNSAFE ASSEMBLY
+ ALTER ANY AVAILABILITY GROUP \(SQL Server 2012 only\)
+ CREATE ANY AVAILABILITY GROUP \(SQL Server 2012 only\)

## Compliance Program Support for Microsoft SQL Server DB Instances<a name="SQLServer.Concepts.General.Compliance"></a>

AWS Services in Scope have been fully assessed by a third\-party auditor and result in a certification, attestation of compliance, or Authority to Operate \(ATO\)\. For more information, see [AWS Services in Scope by Compliance Program](https://aws.amazon.com/compliance/services-in-scope/)\. 

### HIPAA Support for Microsoft SQL Server DB Instances<a name="SQLServer.Concepts.General.HIPAA"></a>

You can use Amazon RDS for Microsoft SQL Server databases to build HIPAA\-compliant applications\. You can store healthcare\-related information, including protected health information \(PHI\), under an executed Business Associate Agreement \(BAA\) with AWS\. For more information, see [HIPAA Compliance](https://aws.amazon.com/compliance/hipaa-compliance/)\. 

Amazon RDS for SQL Server supports HIPAA for the following versions and editions: 
+ SQL Server 2017 Enterprise, Standard, and Web Editions
+ SQL Server 2016 Enterprise, Standard, and Web Editions
+ SQL Server 2014 Enterprise, Standard, and Web Editions
+ SQL Server 2012 Enterprise, Standard, and Web Editions

To enable HIPAA support on your DB instance, set up the following three components\. 


****  

| Component | Details | 
| --- | --- | 
|  Auditing  |  To set up auditing, set the parameter `rds.sqlserver_audit` to the value `fedramp_hipaa`\. If your DB instance is not already using a custom DB parameter group, you must create a custom parameter group and attach it to your DB instance before you can modify the `rds.sqlserver_audit` parameter\. For more information, see [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)\.   | 
|  Transport Encryption  |  To set up transport encryption, force all connections to your DB instance to use Secure Sockets Layer \(SSL\)\. For more information, see [Forcing Connections to Your DB Instance to Use SSL](SQLServer.Concepts.General.SSL.Using.md#SQLServer.Concepts.General.SSL.Forcing)\.   | 
|  Encryption at Rest  |  To set up encryption at rest, you have two options:  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_SQLServer.html)  | 

## SSL Support for Microsoft SQL Server DB Instances<a name="SQLServer.Concepts.General.SSL"></a>

You can use SSL to encrypt connections between your applications and your Amazon RDS DB instances running Microsoft SQL Server\. You can also force all connections to your DB instance to use SSL\. If you force connections to use SSL, it happens transparently to the client, and the client doesn't have to do any work to use SSL\. 

SSL is supported in all AWS Regions and for all supported SQL Server editions\. For more information, see [Using SSL with a Microsoft SQL Server DB Instance](SQLServer.Concepts.General.SSL.Using.md)\. 

## Version and Feature Support on Amazon RDS<a name="SQLServer.Concepts.General.FeatureSupport"></a>

You can specify any currently supported Microsoft SQL Server version when creating a new DB instance\. You can specify the Microsoft SQL Server major version \(such as Microsoft SQL Server 14\.00\), and any supported minor version for the specified major version\. If no version is specified, Amazon RDS defaults to a supported version, typically the most recent version\. If a major version is specified but a minor version is not, Amazon RDS defaults to a recent release of the major version you have specified\. To see a list of supported versions, as well as defaults for newly created DB instances, use the [ `describe-db-engine-versions`](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-engine-versions.html) AWS CLI command\.

### Microsoft SQL Server 2017 Support on Amazon RDS<a name="SQLServer.Concepts.General.FeatureSupport.2017"></a>

Amazon RDS supports the following versions of SQL Server 2017: 
+ SQL Server 2017 RTM \(CU13\) 14\.00\.3049\.1, for all editions and all AWS Regions\.

  RDS API `EngineVersion` and CLI `engine-version`: `14.00.3049.1.v1`
+ SQL Server 2017 RTM CU13 14\.00\.3015\.40, for all editions and all AWS Regions\. 

  RDS API `EngineVersion` and CLI `engine-version`: `14.00.3015.40.v1`
+ Version 14\.00\.1000\.169, RTM, for all editions, and all AWS Regions\.

  RDS API `EngineVersion` and CLI `engine-version`: `14.00.1000.169.v1`

SQL Server 2017 includes many new features, such as the following: 
+ Adaptive query processing
+ Automatic plan correction
+ GraphDB
+ Resumable index rebuilds

For the full list of SQL Server 2017 features, see [What's New in SQL Server 2017](https://docs.microsoft.com/en-us/sql/sql-server/what-s-new-in-sql-server-2017) in the Microsoft documentation\.

For a list of unsupported features, see [Features Not Supported and Features with Limited Support](#SQLServer.Concepts.General.FeatureNonSupport)\. 

### Microsoft SQL Server 2016 Support on Amazon RDS<a name="SQLServer.Concepts.General.FeatureSupport.2016"></a>

Amazon RDS supports the following versions of SQL Server 2016: 
+ SQL Server 2016, SP2 CU8 13\.00\.5426\.0, for all editions and all AWS Regions

  RDS API `EngineVersion` and CLI `engine-version`: `13.00.5426.0.v1`
+ SQL Server 2016, SP1 CU7 13\.00\.4466\.4, for all editions and all AWS Regions

  RDS API `EngineVersion` and CLI `engine-version`: `13.00.4466.4.v1`
+ Version 13\.00\.4451\.0, SP1 CU5, for all editions, and all AWS Regions

  RDS API `EngineVersion` and CLI `engine-version`: `13.00.4451.0.v1`
+ Version 13\.00\.4422\.0, SP1 CU2, for all editions, and all AWS Regions

  RDS API `EngineVersion` and CLI `engine-version`: `13.00.4422.0.v1`
+ Version 13\.00\.2164\.0, RTM CU2, for all editions, and all AWS Regions

  RDS API `EngineVersion` and CLI `engine-version`: `13.00.2164.0.v1`

Amazon RDS supports the following features of SQL Server 2016:
+ Always Encrypted
+ JSON Support
+ Operational Analytics
+ Query Store
+ Temporal Tables

For the full list of SQL Server 2016 features, see [What's New in SQL Server 2016](https://docs.microsoft.com/en-us/sql/sql-server/what-s-new-in-sql-server-2016) in the Microsoft documentation\.

### Microsoft SQL Server 2014 Support on Amazon RDS<a name="SQLServer.Concepts.General.FeatureSupport.2014"></a>

Amazon RDS supports the following versions of SQL Server 2014: 
+ SQL Server 2014 SP2 CU10 12\.00\.5571\.0, for all editions, and all AWS Regions\.

  RDS API `EngineVersion` and CLI `engine-version`: `12.00.5571.0.v1`
+ Version 12\.00\.5546\.0, SP2 CU5, for all editions and all AWS Regions 

  RDS API `EngineVersion` and CLI `engine-version`: `12.00.5546.0.v1`
+ Version 12\.00\.5000\.0, SP2, for all editions and all AWS Regions 

  RDS API `EngineVersion` and CLI `engine-version`: `12.00.5000.0.v1`
+ Version 12\.00\.4422\.0, SP1 CU2, for all editions except Enterprise Edition, and all AWS Regions except Canada \(Central\), and Europe \(London\) 

  RDS API `EngineVersion` and CLI `engine-version`: `12.00.4422.0.v1`

In addition to supported features of SQL Server 2012, Amazon RDS supports the new query optimizer available in SQL Server 2014, and also the delayed durability feature\. 

For a list of unsupported features, see [Features Not Supported and Features with Limited Support](#SQLServer.Concepts.General.FeatureNonSupport)\. 

SQL Server 2014 supports all the parameters from SQL Server 2012 and uses the same default values\. SQL Server 2014 includes one new parameter, backup checksum default\. For more information, see [How to enable the CHECKSUM option if backup utilities do not expose the option](https://support.microsoft.com/en-us/kb/2656988) in the Microsoft documentation\. 

### Microsoft SQL Server 2012 Support on Amazon RDS<a name="SQLServer.Concepts.General.FeatureSupport.2012"></a>

Amazon RDS supports the following versions of SQL Server 2012: 
+ SQL Server 2012 SP4 GDR 11\.00\.7462\.6, for all editions, for all AWS Regions\.

  RDS API `EngineVersion` and CLI `engine-version`: `11.00.7462.6.v1`
+ Version 11\.00\.6594\.0, SP3 CU8, for all editions and all AWS Regions

  RDS API `EngineVersion` and CLI `engine-version`: `11.00.6594.0.v1`
+ Version 11\.00\.6020\.0, SP3, for all editions and all AWS Regions 

  RDS API `EngineVersion` and CLI `engine-version`: `11.00.6020.0.v1`
+ Version 11\.00\.5058\.0, SP2, for all editions, and all AWS Regions except US East \(Ohio\), Canada \(Central\), and Europe \(London\) 

  RDS API `EngineVersion` and CLI `engine-version`: `11.00.5058.0.v1`

For more information about SQL Server 2012, see [Features Supported by the Editions of SQL Server 2012](https://msdn.microsoft.com/en-us/library/cc645993%28v=sql.110%29.aspx) in the Microsoft documentation\. 

In addition to supported features of SQL Server 2008 R2, Amazon RDS supports the following SQL Server 2012 features: 
+ Columnstore indexes \(Enterprise Edition\)
+ Online Index Create, Rebuild and Drop for XML, varchar\(max\), nvarchar\(max\), and varbinary\(max\) data types \(Enterprise Edition\)
+ Flexible Server Roles
+ Service Broker is supported, Service Broker endpoints are not supported
+ Partially Contained Databases
+ Sequences
+ Transparent Data Encryption \(Enterprise Edition only\)
+ THROW statement
+ New and enhanced spatial types
+ UTF\-16 Support
+ ALTER ANY SERVER ROLE server\-level permission

For a list of unsupported features, see [Features Not Supported and Features with Limited Support](#SQLServer.Concepts.General.FeatureNonSupport)\. 

Some SQL Server parameters have changed in SQL Server 2012\. 
+ The following parameters have been removed from SQL Server 2012: `awe enabled`, `precompute rank`, and `sql mail xps`\. These parameters were not modifiable in SQL Server DB Instances and their removal should have no impact on your SQL Server use\. 
+ A new `contained database authentication` parameter in SQL Server 2012 supports partially contained databases\. When you enable this parameter and then create a partially contained database, an authorized user's user name and password is stored within the partially contained database instead of in the master database\. For more information about partially contained databases, see [Contained Databases](http://msdn.microsoft.com/en-us/library/ff929071.aspx) in the Microsoft documentation\. 

### Microsoft SQL Server 2008 R2 Deprecated on Amazon RDS<a name="SQLServer.Concepts.General.FeatureSupport.2008"></a>

We are upgrading all existing instances that are still using SQL Server 2008 R2 to the latest minor version of SQL Server 2012\. For more information, see [Microsoft SQL Server Engine Version Management in Amazon RDS](#SQLServer.Concepts.General.Version-Management)\. 

For more information about SQL Server 2008 R2, see [Features Supported by the Editions of SQL Server 2008 R2](https://msdn.microsoft.com/en-us/library/cc645993%28v=sql.105%29.aspx) in the Microsoft documentation\. 

## Microsoft SQL Server Engine Version Management in Amazon RDS<a name="SQLServer.Concepts.General.Version-Management"></a>

Amazon RDS includes flexible version management that enables you to control when and how your DB instance is patched or upgraded\. This enables you to do the following for your DB engine:
+ Maintain compatibility with database engine patch versions
+ Test new patch versions to verify that they work with your application before you deploy them in production
+ Plan and perform version upgrades to meet your service level agreements and timing requirements 

### Microsoft SQL Server Engine Patching in Amazon RDS<a name="SQLServer.Concepts.General.Patching"></a>

Amazon RDS periodically aggregates official Microsoft SQL Server database patches into a DB instance engine version that's specific to Amazon RDS\. For more information about the Microsoft SQL Server patches in each engine version, see [Version and Feature Support on Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_SQLServer.html#SQLServer.Concepts.General.FeatureSupport)\.

Currently, you manually execute all engine upgrades on your DB instance\. For more information, see [Upgrading the Microsoft SQL Server DB Engine](USER_UpgradeDBInstance.SQLServer.md)\. 

### Deprecation Schedule for Major Engine Versions of Microsoft SQL Server on Amazon RDS<a name="SQLServer.Concepts.General.Deprecated-Versions"></a>

The table following displays the planned schedule of deprecations for major engine versions of Microsoft SQL Server\. 


| Date | Information | 
| --- | --- | 
| July 12, 2019 |  The Amazon RDS team deprecated support for Microsoft SQL Server 2008 R2 in June 2019\. Remaining instances of Microsoft SQL Server 2008 R2 are migrating to SQL Server 2012 \(latest minor version available\)\.  To avoid an automatic upgrade from Microsoft SQL Server 2008 R2, you can upgrade at a time that is convenient to you\. For more information, see [Upgrading a DB Instance Engine Version](USER_UpgradeDBInstance.Upgrading.md)\.  | 
| April 25, 2019 | Before the end of April 2019, you will no longer be able to create new Amazon RDS for SQL Server database instances using Microsoft SQL Server 2008R2\. | 

## Change Data Capture Support for Microsoft SQL Server DB Instances<a name="SQLServer.Concepts.General.CDC"></a>

Amazon RDS supports change data capture \(CDC\) for your DB instances running Microsoft SQL Server\. CDC captures changes that are made to the data in your tables, and stores metadata about each change that you can access later\. For more information, see [Change Data Capture](https://docs.microsoft.com/en-us/sql/relational-databases/track-changes/track-data-changes-sql-server#Capture) in the Microsoft documentation\. 

Amazon RDS supports CDC for the following SQL Server editions and versions:
+ Microsoft SQL Server Enterprise Edition \(All versions\) 
+ Microsoft SQL Server Standard Edition: 
  + 2017
  + 2016 version 13\.00\.4422\.0 SP1 CU2 and later

To use CDC with your Amazon RDS DB instances, first enable or disable CDC at the database level by using RDS\-provided stored procedures\. After that, any user that has the `db_owner` role for that database can use the native Microsoft stored procedures to control CDC on that database\. For more information, see [Using Change Data Capture](Appendix.SQLServer.CommonDBATasks.CDC.md)\. 

You can use CDC and AWS Database Migration Service to enable ongoing replication from SQL Server DB instances\.  

## Features Not Supported and Features with Limited Support<a name="SQLServer.Concepts.General.FeatureNonSupport"></a>

The following Microsoft SQL Server features are not supported on Amazon RDS: 
+ Stretch database
+ Backing up to Microsoft Azure Blob Storage
+ Buffer pool extension
+ Data Quality Services
+ Database Log Shipping
+ Database Mail
+ Distribution Transaction Coordinator \(MSDTC\)
+ File tables
+ FILESTREAM support
+ Maintenance Plans
+ Performance Data Collector
+ Policy\-Based Management
+ PolyBase
+ Machine Learning and R Services \(requires OS access to install it\)
+ Replication
+ Resource Governor
+ Server\-level triggers
+ Service Broker endpoints
+ T\-SQL endpoints \(all operations using CREATE ENDPOINT are unavailable\)
+ WCF Data Services

The following Microsoft SQL Server features have limited support on Amazon RDS: 
+ Distributed Queries / Linked Servers\. For more information, see: [Implementing Linked Servers with Amazon RDS for Microsoft SQL Server](https://aws.amazon.com/blogs/database/implement-linked-servers-with-amazon-rds-for-microsoft-sql-server/)\.

## Multi\-AZ Deployments Using Microsoft SQL Server Database Mirroring or Always On Availability Groups<a name="SQLServer.Concepts.General.Mirroring"></a>

Amazon RDS supports Multi\-AZ deployments for DB instances running Microsoft SQL Server by using SQL Server Database Mirroring \(DBM\) or Always On Availability Groups \(AGs\)\. Multi\-AZ deployments provide increased availability, data durability, and fault tolerance for DB instances\. In the event of planned database maintenance or unplanned service disruption, Amazon RDS automatically fails over to the up\-to\-date secondary replica so database operations can resume quickly without manual intervention\. The primary and secondary instances use the same endpoint, whose physical network address transitions to the passive secondary replica as part of the failover process\. You don't have to reconfigure your application when a failover occurs\. 

Amazon RDS manages failover by actively monitoring your Multi\-AZ deployment and initiating a failover when a problem with your primary occurs\. Failover doesn't occur unless the standby and primary are fully in sync\. Amazon RDS actively maintains your Multi\-AZ deployment by automatically repairing unhealthy DB instances and re\-establishing synchronous replication\. You don't have to manage anything\. Amazon RDS handles the primary, the witness, and the standby instance for you\. When you set up SQL Server Multi\-AZ, RDS configures passive secondary instances for all of the databases on the instance\. 

For more information, see [Multi\-AZ Deployments for Microsoft SQL Server](USER_SQLServerMultiAZ.md)\. 

## Using Transparent Data Encryption to Encrypt Data at Rest<a name="SQLServer.Concepts.General.Options"></a>

Amazon RDS supports Microsoft SQL Server Transparent Data Encryption \(TDE\), which transparently encrypts stored data\. Amazon RDS uses option groups to enable and configure these features\. For more information about the TDE option, see [Support for Transparent Data Encryption in SQL Server](Appendix.SQLServer.Options.TDE.md)\. 

## Local Time Zone for Microsoft SQL Server DB Instances<a name="SQLServer.Concepts.General.TimeZone"></a>

The time zone of an Amazon RDS DB instance running Microsoft SQL Server is set by default\. The current default is Universal Coordinated Time \(UTC\)\. You can set the time zone of your DB instance to a local time zone instead, to match the time zone of your applications\. 

You set the time zone when you first create your DB instance\. You can create your DB instance by using the [AWS Management Console](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_CreateMicrosoftSQLServerInstance.html), the Amazon RDS API [CreateDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html.html) action, or the AWS CLI [create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) command\. 

If your DB instance is part of a Multi\-AZ deployment \(using SQL Server DBM or AGs\), then when you fail over, your time zone remains the local time zone that you set\. For more information, see [Multi\-AZ Deployments Using Microsoft SQL Server Database Mirroring or Always On Availability Groups ](#SQLServer.Concepts.General.Mirroring)\. 

When you request a point\-in\-time restore, you specify the time to restore to in UTC\. During the restore process, the time is translated to the time zone of the DB instance\. For more information, see [Restoring a DB Instance to a Specified Time](USER_PIT.md)\. 

The following are limitations to setting the local time zone on your DB instance:
+ You can't modify the time zone of an existing SQL Server DB instance\. 
+ You can't restore a snapshot from a DB instance in one time zone to a DB instance in a different time zone\. 
+ We strongly recommend that you don't restore a backup file from one time zone to a different time zone\. If you restore a backup file from one time zone to a different time zone, you must audit your queries and applications for the effects of the time zone change\. For more information, see [Importing and Exporting SQL Server Databases](SQLServer.Procedural.Importing.md)\. 

### Supported Time Zones<a name="SQLServer.Concepts.General.TimeZone.Zones"></a>

You can set your local time zone to one of the values listed in the following table\. 


****  

| Time Zone | Standard Time Offset | Description | Notes | 
| --- | --- | --- | --- | 
| Afghanistan Standard Time | \(UTC\+04:30\) | Kabul |  | 
| Alaskan Standard Time | \(UTC–09:00\) | Alaska |  | 
| Arabian Standard Time | \(UTC\+04:00\) | Abu Dhabi, Muscat |  | 
| Atlantic Standard Time | \(UTC–04:00\) | Atlantic Time \(Canada\) |  | 
| AUS Central Standard Time | \(UTC\+09:30\) | Darwin |  | 
| AUS Eastern Standard Time | \(UTC\+10:00\) | Canberra, Melbourne, Sydney |  | 
| Belarus Standard Time | \(UTC\+03:00\) | Minsk |  This time zone does not observe daylight savings time\.   | 
| Canada Central Standard Time | \(UTC–06:00\) | Saskatchewan |  | 
| Cape Verde Standard Time | \(UTC–01:00\) | Cabo Verde Is\. |  | 
| Cen\. Australia Standard Time | \(UTC\+09:30\) | Adelaide |  | 
| Central America Standard Time | \(UTC–06:00\) | Central America |  | 
| Central Asia Standard Time | \(UTC\+06:00\) | Astana |  | 
| Central Brazilian Standard Time | \(UTC–04:00\) | Cuiaba |  | 
| Central Europe Standard Time | \(UTC\+01:00\) | Belgrade, Bratislava, Budapest, Ljubljana, Prague |  | 
| Central European Standard Time | \(UTC\+01:00\) | Sarajevo, Skopje, Warsaw, Zagreb |  | 
| Central Pacific Standard Time | \(UTC\+11:00\) | Solomon Islands, New Caledonia |  | 
| Central Standard Time | \(UTC–06:00\) | Central Time \(US and Canada\) |  | 
| Central Standard Time \(Mexico\) | \(UTC–06:00\) | Guadalajara, Mexico City, Monterrey |  | 
| China Standard Time | \(UTC\+08:00\) | Beijing, Chongqing, Hong Kong, Urumqi |  | 
| E\. Africa Standard Time | \(UTC\+03:00\) | Nairobi |  This time zone does not observe daylight savings time\.   | 
| E\. Australia Standard Time | \(UTC\+10:00\) | Brisbane |  | 
| E\. Europe Standard Time | \(UTC\+02:00\) | Chisinau |  | 
| E\. South America Standard Time | \(UTC–03:00\) | Brasilia |  | 
| Eastern Standard Time | \(UTC–05:00\) | Eastern Time \(US and Canada\) |  | 
| Georgian Standard Time | \(UTC\+04:00\) | Tbilisi |  | 
| GMT Standard Time | \(UTC\) | Dublin, Edinburgh, Lisbon, London |  This time zone is not the same as Greenwich Mean Time\. This time zone does observe daylight savings time\.   | 
| Greenland Standard Time | \(UTC–03:00\) | Greenland |  | 
| Greenwich Standard Time | \(UTC\) | Monrovia, Reykjavik |  This time zone does not observe daylight savings time\.   | 
| GTB Standard Time | \(UTC\+02:00\) | Athens, Bucharest |  | 
| Hawaiian Standard Time | \(UTC–10:00\) | Hawaii |  | 
| India Standard Time | \(UTC\+05:30\) | Chennai, Kolkata, Mumbai, New Delhi |  | 
| Jordan Standard Time | \(UTC\+02:00\) | Amman |  | 
| Korea Standard Time | \(UTC\+09:00\) | Seoul |  | 
| Middle East Standard Time | \(UTC\+02:00\) | Beirut |  | 
| Mountain Standard Time | \(UTC–07:00\) | Mountain Time \(US and Canada\) |  | 
| Mountain Standard Time \(Mexico\) | \(UTC–07:00\) | Chihuahua, La Paz, Mazatlan |  | 
| US Mountain Standard Time | \(UTC–07:00\) | Arizona | This time zone does not observe daylight savings time\. | 
| New Zealand Standard Time | \(UTC\+12:00\) | Auckland, Wellington |  | 
| Newfoundland Standard Time | \(UTC–03:30\) | Newfoundland |  | 
| Pacific SA Standard Time | \(UTC–03:00\) | Santiago |  | 
| Pacific Standard Time | \(UTC–08:00\) | Pacific Time \(US and Canada\) |  | 
| Pacific Standard Time \(Mexico\) | \(UTC–08:00\) | Baja California |  | 
| Russian Standard Time | \(UTC\+03:00\) | Moscow, St\. Petersburg, Volgograd |  This time zone does not observe daylight savings time\.   | 
| SA Pacific Standard Time | \(UTC–05:00\) | Bogota, Lima, Quito, Rio Branco |  This time zone does not observe daylight savings time\.   | 
| SE Asia Standard Time | \(UTC\+07:00\) | Bangkok, Hanoi, Jakarta |  | 
| Singapore Standard Time | \(UTC\+08:00\) | Kuala Lumpur, Singapore |  | 
| Tokyo Standard Time | \(UTC\+09:00\) | Osaka, Sapporo, Tokyo |  | 
| US Eastern Standard Time | \(UTC–05:00\) | Indiana \(East\) |  | 
| UTC | UTC | Coordinated Universal Time |  This time zone does not observe daylight savings time\.   | 
| UTC–02 | \(UTC–02:00\) | Coordinated Universal Time–02 |  | 
| UTC–08 | \(UTC–08:00\) | Coordinated Universal Time–08 |  | 
| UTC–09 | \(UTC–09:00\) | Coordinated Universal Time–09 |  | 
| UTC–11 | \(UTC–11:00\) | Coordinated Universal Time–11 |  | 
| UTC\+12 | \(UTC\+12:00\) | Coordinated Universal Time\+12 |  | 
| W\. Australia Standard Time | \(UTC\+08:00\) | Perth |  | 
| W\. Central Africa Standard Time | \(UTC\+01:00\) | West Central Africa |  | 
| W\. Europe Standard Time | \(UTC\+01:00\) | Amsterdam, Berlin, Bern, Rome, Stockholm, Vienna |  | 