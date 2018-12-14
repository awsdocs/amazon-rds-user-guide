# Microsoft SQL Server on Amazon RDS<a name="CHAP_SQLServer"></a>

Amazon RDS supports DB instances running several versions and editions of Microsoft SQL Server\. The most recent supported version of each major version is shown following\. For the full list of supported versions, editions, and RDS engine versions, see [Version and Feature Support on Amazon RDS](#SQLServer.Concepts.General.FeatureSupport)\. 
+ SQL Server 2017 RTM \(CU\) 14\.00\.3035\.2\.v1, released per [KB4293805](https://support.microsoft.com/en-us/help/4293805) on 14 August 2018 \. 
+ SQL Server 2016 SP2 \(CU2 \+ Security Update\) 13\.00\.5201\.2\.v1, released per [KB4458621](https://support.microsoft.com/en-us/help/3177312/sql-server-2016-build-versions) on 21 August 2018 \. 
+ SQL Server 2014 SP2 CU10 12\.00\.5571\.0, released per [KB4052725](https://support.microsoft.com/en-us/help/2936603/sql-server-2014-build-versions) on 16 January 2018\. 
+ SQL Server 2012 SP4 GDR 11\.00\.7462\.6, released per [KB4057116](https://support.microsoft.com/en-us/help/4057116/security-update-for-vulnerabilities-in-sql-server) on 12 January 2017\. 
+ SQL Server 2008 R2 SP3 GDR 10\.50\.6560\.0, released per [KB4057113](https://support.microsoft.com/en-us/help/4057113/security-update-for-vulnerabilities-in-sql-server) on 6 January 2018\. Not available in US East \(Ohio\), Canada \(Central\), and EU \(London\) 

For information about licensing for SQL Server, see [Licensing Microsoft SQL Server on Amazon RDS](SQLServer.Concepts.General.Licensing.md)\. For information about SQL Server builds, see this Microsoft support article about [the latest SQL Server builds](https://support.microsoft.com/en-us/help/957826)\.

With Amazon RDS, you can create DB instances and DB snapshots, point\-in\-time restores, and automated or manual backups\. DB instances running SQL Server can be used inside a VPC\. You can also use SSL to connect to a DB instance running SQL Server, and you can use TDE to encrypt data at rest\. Amazon RDS currently supports Multi\-AZ deployments for SQL Server using SQL Server Mirroring or Always On as a high\-availability, failover solution\. 

In order to deliver a managed service experience, Amazon RDS does not provide shell access to DB instances, and it restricts access to certain system procedures and tables that require advanced privileges\. Amazon RDS supports access to databases on a DB instance using any standard SQL client application such as Microsoft SQL Server Management Studio\. Amazon RDS does not allow direct host access to a DB instance via Telnet, Secure Shell \(SSH\), or Windows Remote Desktop Connection\. When you create a DB instance, you are assigned to the *db\_owner* role for all databases on that instance, and you have all database\-level permissions except for those that are used for backups\. Amazon RDS manages backups for you\. 

Before creating your first DB instance, you should complete the steps in the setting up section of this guide\. For more information, see [Setting Up for Amazon RDS](CHAP_SettingUp.md)\. 

## Common Management Tasks for Microsoft SQL Server on Amazon RDS<a name="SQLServer.Concepts.General"></a>

The following are the common management tasks you perform with an Amazon RDS SQL Server DB instance, with links to relevant documentation for each task\. 


****  

| Task Area | Relevant Documentation | 
| --- | --- | 
|  **Instance Classes, Storage, and PIOPS** If you are creating a DB instance for production purposes, you should understand how instance classes, storage types, and Provisioned IOPS work in Amazon RDS\.   |  [DB Instance Class Support for Microsoft SQL Server](#SQLServer.Concepts.General.InstanceClasses) [Amazon RDS Storage Types](CHAP_Storage.md#Concepts.Storage)   | 
|  **Multi\-AZ Deployments** A production DB instance should use Multi\-AZ deployments\. Multi\-AZ deployments provide increased availability, data durability, and fault tolerance for DB instances\. Multi\-AZ deployments for SQL Server are implemented using SQL Server’s native Mirroring or Always On technology\.   |  [High Availability \(Multi\-AZ\) for Amazon RDS](Concepts.MultiAZ.md) [Multi\-AZ Deployments Using Microsoft SQL Server Mirroring or Always On](#SQLServer.Concepts.General.Mirroring)  | 
|  **Amazon Virtual Private Cloud \(VPC\)** If your AWS account has a default VPC, then your DB instance is automatically created inside the default VPC\. If your account does not have a default VPC, and you want the DB instance in a VPC, you must create the VPC and subnet groups before you create the DB instance\.   |  [Determining Whether You Are Using the EC2\-VPC or EC2\-Classic Platform](USER_VPC.FindDefaultVPC.md) [Working with an Amazon RDS DB Instance in a VPC](USER_VPC.WorkingWithRDSInstanceinaVPC.md)  | 
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

The Amazon RDS implementation of Microsoft SQL Server on a DB instance have some limitations you should be aware of: 
+ You can create up to 30 databases on each of your DB instances running Microsoft SQL Server\. The Microsoft system databases, such as `master` and `model`, don't count toward this limit\. 
+ Some ports are reserved for Amazon RDS use and you can't use them when you create a DB instance\. 
+ Amazon RDS for SQL Server does not support importing data into the msdb database\. 
+ You can't rename databases on a DB instance in a SQL Server Multi\-AZ deployment\. 
+ The maximum storage size for SQL Server DB instances is the following: 
  + General Purpose \(SSD\) storage: 16 TiB for all editions 
  + Provisioned IOPS storage: 16 TiB for all editions 
  + Magnetic storage: 1 TiB for all editions 

  If you have a scenario that requires a larger amount of storage, you can use sharding across multiple DB instances to get around the limit\. This approach requires data\-dependent routing logic in applications that connect to the sharded system\. You can use an existing sharding framework, or you can write custom code to enable sharding\. If you use an existing framework, the framework can't install any components on the same server as the DB instance\. 
+ The minimum storage size for SQL Server DB instances is the following: 
  + General Purpose \(SSD\) storage: 200 GiB for Enterprise and Standard editions, 20 GiB for Web and Express editions 
  + Provisioned IOPS storage: 200 GiB for Enterprise and Standard editions, 100 GiB for Web and Express editions 
  + Magnetic storage: 200 GiB for Enterprise and Standard editions, 20 GiB for Web and Express editions 
+ Amazon RDS doesn't support running SQL Server Analysis Services, SQL Server Integration Services, SQL Server Reporting Services, Data Quality Services, or Master Data Services on the same server as your Amazon RDS DB instance\. To use these features, we recommend that you install SQL Server on an Amazon EC2 instance, or use an on\-premise SQL Server instance, to act as the Reporting, Analysis, Integration, or Master Data Services server for your SQL Server DB instance on Amazon RDS\. You can install SQL Server on an Amazon EC2 instance with Amazon EBS storage, pursuant to Microsoft licensing policies\. 
+ Because of limitations in Microsoft SQL Server, restoring to a point in time before successful execution of a DROP DATABASE might not reflect the state of that database at that point in time\. For example, the dropped database is typically restored to its state up to 5 minutes before the DROP DATABASE command was issued, which means that you can't restore the transactions made during those few minutes on your dropped database\. To work around this, you can reissue the DROP DATABASE command after the restore operation is completed\. Dropping a database removes the transaction logs for that database\. 

## DB Instance Class Support for Microsoft SQL Server<a name="SQLServer.Concepts.General.InstanceClasses"></a>

The computation and memory capacity of a DB instance is determined by its DB instance class\. The DB instance class you need depends on your processing power and memory requirements\. For more information, see [DB Instance Class](Concepts.DBInstanceClass.md)\. 

The following are the DB instance classes supported for Microsoft SQL Server\. 


****  

| SQL Server Edition | 2017 and 2016 Support | 2014, 2012, and 2008 R2 Support | 
| --- | --- | --- | 
|  Enterprise Edition |  db\.m4\.xlarge–16xlarge db\.r4\.xlarge–16xlarge —  |  db\.m4\.xlarge–10xlarge db\.r4\.xlarge–8xlarge —  | 
|  Standard Edition |  db\.m4\.large–16xlarge, except db\.m4\.10xlarge db\.r4\.large–16xlarge —  |  db\.m4\.large–4xlarge db\.r4\.large–8xlarge —  | 
|  Web Edition  |  db\.m4\.large–4xlarge db\.r4\.large–2xlarge db\.t2\.small–medium  |  db\.m4\.large–4xlarge db\.r4\.large–2xlarge db\.t2\.small–medium  | 
|  Express Edition  |  — — db\.t2\.micro–medium  |  db\.m1\.small–small — db\.t2\.micro–medium  | 

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
+ ADMINISTER BULK OPERATIONS
+ ALTER ANY CREDENTIAL
+ ALTER ANY EVENT NOTIFICATION
+ ALTER ANY EVENT SESSION
+ ALTER ANY SERVER AUDIT
+ ALTER RESOURCES
+ ALTER SETTINGS \(You can use the DB Parameter Group APIs to modify parameters\. For more information, see [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)\. 
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
+ SQL Server 2017, 2016, 2014, and 2012: Enterprise, Standard, and Web Editions
+ SQL Server 2008 R2: Enterprise Edition

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

### Microsoft SQL Server 2017 Support on Amazon RDS<a name="SQLServer.Concepts.General.FeatureSupport.2017"></a>

Amazon RDS supports the following versions of SQL Server 2017: 
+ SQL Server 2017 RTM CU3 14\.00\.3015\.40, released per [KB4052987](https://support.microsoft.com/en-us/help/4052987/cumulative-update-3-for-sql-server-2017) on 4 January 2018\. 

  RDS API `EngineVersion` and CLI `engine-version`: `14.00.3015.40.v1`
+ Version 14\.00\.1000\.169, RTM, for all editions, and all AWS Regions

  RDS API `EngineVersion` and CLI `engine-version`: `14.00.1000.169.v1`

SQL Server 2017 includes many new features, such as the following: 
+ Adaptive query processing
+ Automatic plan correction
+ GraphDB
+ Resumable index rebuilds

For the full list of SQL Server 2017 features, see [What's New in SQL Server 2017](https://docs.microsoft.com/en-us/sql/sql-server/what-s-new-in-sql-server-2017)  in the Microsoft documentation\. 

For a list of unsupported features, see [Features Not Supported](#SQLServer.Concepts.General.FeatureNonSupport)\. 

### Microsoft SQL Server 2016 Support on Amazon RDS<a name="SQLServer.Concepts.General.FeatureSupport.2016"></a>

Amazon RDS supports the following versions of SQL Server 2016: 
+ SQL Server 2016 SP1 CU7 13\.00\.4466\.4, released per [KB4057119](https://support.microsoft.com/en-us/help/4057119/cumulative-update-7-for-sql-server-2016-sp1) on 4 January 2018\. 

  RDS API `EngineVersion` and CLI `engine-version`: `13.00.4466.4.v1`
+ Version 13\.00\.4451\.0, SP1 CU5, for all editions, and all AWS Regions

  RDS API `EngineVersion` and CLI `engine-version`: `13.00.4451.0.v1`
+ Version 13\.00\.4422\.0, SP1 CU2, for all editions, and all AWS Regions

  RDS API `EngineVersion` and CLI `engine-version`: `13.00.4422.0.v1`
+ Version 13\.00\.2164\.0, RTM CU2, for all editions, and all AWS Regions

  RDS API `EngineVersion` and CLI `engine-version`: `13.00.2164.0.v1`

### Microsoft SQL Server 2014 Support on Amazon RDS<a name="SQLServer.Concepts.General.FeatureSupport.2014"></a>

Amazon RDS supports the following versions of SQL Server 2014: 
+ SQL Server 2014 SP2 CU10 12\.00\.5571\.0, released per [KB4052725](https://support.microsoft.com/en-us/help/2936603/sql-server-2014-build-versions) on 16 January 2018\. 

  RDS API `EngineVersion` and CLI `engine-version`: `12.00.5571.0.v1`
+ Version 12\.00\.5546\.0, SP2 CU5, for all editions and all AWS Regions 

  RDS API `EngineVersion` and CLI `engine-version`: `12.00.5546.0.v1`
+ Version 12\.00\.5000\.0, SP2, for all editions and all AWS Regions 

  RDS API `EngineVersion` and CLI `engine-version`: `12.00.5000.0.v1`
+ Version 12\.00\.4422\.0, SP1 CU2, for all editions except Enterprise Edition, and all AWS Regions except Canada \(Central\), and EU \(London\) 

  RDS API `EngineVersion` and CLI `engine-version`: `12.00.4422.0.v1`

In addition to supported features of SQL Server 2012, Amazon RDS supports the new query optimizer available in SQL Server 2014, and also the delayed durability feature\. 

For a list of unsupported features, see [Features Not Supported](#SQLServer.Concepts.General.FeatureNonSupport)\. 

SQL Server 2014 supports all the parameters from SQL Server 2012 and uses the same default values\. SQL Server 2014 includes one new parameter, backup checksum default\. For more information, see [How to enable the CHECKSUM option if backup utilities do not expose the option](https://support.microsoft.com/en-us/kb/2656988) in the Microsoft documentation\. 

### Microsoft SQL Server 2012 Support on Amazon RDS<a name="SQLServer.Concepts.General.FeatureSupport.2012"></a>

Amazon RDS supports the following versions of SQL Server 2012: 
+ SQL Server 2012 SP4 GDR 11\.00\.7462\.6, released per [KB4057116](https://support.microsoft.com/en-us/help/4057116/security-update-for-vulnerabilities-in-sql-server) on 12 January 2017\. 

  RDS API `EngineVersion` and CLI `engine-version`: `11.00.7462.6.v1`
+ Version 11\.00\.6594\.0, SP3 CU8, for all editions and all AWS Regions

  RDS API `EngineVersion` and CLI `engine-version`: `11.00.6594.0.v1`
+ Version 11\.00\.6020\.0, SP3, for all editions and all AWS Regions 

  RDS API `EngineVersion` and CLI `engine-version`: `11.00.6020.0.v1`
+ Version 11\.00\.5058\.0, SP2, for all editions, and all AWS Regions except US East \(Ohio\), Canada \(Central\), and EU \(London\) 

  RDS API `EngineVersion` and CLI `engine-version`: `11.00.5058.0.v1`
+ Version 11\.00\.2100\.60, RTM, for all editions, and all AWS Regions except US East \(Ohio\), Canada \(Central\), and EU \(London\) 

  RDS API `EngineVersion` and CLI `engine-version`: `11.00.2100.60.v1`

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

For a list of unsupported features, see [Features Not Supported](#SQLServer.Concepts.General.FeatureNonSupport)\. 

Some SQL Server parameters have changed in SQL Server 2012\. 
+ The following parameters have been removed from SQL Server 2012: `awe enabled`, `precompute rank`, and `sql mail xps`\. These parameters were not modifiable in SQL Server DB Instances and their removal should have no impact on your SQL Server use\. 
+ A new `contained database authentication` parameter in SQL Server 2012 supports partially contained databases\. When you enable this parameter and then create a partially contained database, an authorized user's user name and password is stored within the partially contained database instead of in the master database\. For more information about partially contained databases, see [Contained Databases](http://msdn.microsoft.com/en-us/library/ff929071.aspx) in the Microsoft documentation\. 

### Microsoft SQL Server 2008 R2 Support on Amazon RDS<a name="SQLServer.Concepts.General.FeatureSupport.2008"></a>

Amazon RDS supports the following versions of SQL Server 2008 R2: 
+ SQL Server 2008 R2 SP3 GDR 10\.50\.6560\.0, released per [KB4057113](https://support.microsoft.com/en-us/help/4057113/security-update-for-vulnerabilities-in-sql-server) on 6 January 2018\. Not available in US East \(Ohio\), Canada \(Central\), and EU \(London\) 

  RDS API `EngineVersion` and CLI `engine-version`: `10.50.6560.0.v1`
+ Version 10\.50\.6529\.0, SP3 QFE, for all editions, and all AWS Regions except US East \(Ohio\), Canada \(Central\), and EU \(London\) 

  RDS API `EngineVersion` and CLI `engine-version`: `10.50.6529.0.v1`
+ Version 10\.50\.6000\.34, SP3, for all editions, and all AWS Regions except US East \(Ohio\), Canada \(Central\), and EU \(London\) 

  RDS API `EngineVersion` and CLI `engine-version`: `10.50.6000.34.v1`
+ Version 10\.50\.2789\.0, SP1, for all editions, and all AWS Regions except US East \(Ohio\), Canada \(Central\), and EU \(London\) 

  RDS API `EngineVersion` and CLI `engine-version`: `10.50.2789.0.v1`

For more information about SQL Server 2008 R2, see [Features Supported by the Editions of SQL Server 2008 R2](https://msdn.microsoft.com/en-us/library/cc645993%28v=sql.105%29.aspx) in the Microsoft documentation\. 

Amazon RDS supports the following SQL Server 2008 R2 features:
+ Core database engine features
+ SQL Server development tools:
  + Visual Studio integration
  + IntelliSense
+ SQL Server management tools:
  + SQL Server Management Studio \(SMS\)
  + sqlcmd
  + SQL Server Profiler \(client side traces; workaround available for server side\)
  + SQL Server Migration Assistant \(SSMA\)
  + Database Engine Tuning Advisor
  + SQL Server Agent
+ Safe CLR
+ Full\-text search \(except semantic search\)
+ SSL
+ Transparent Data Encryption \(Enterprise Edition only\)
+ Spatial and location features
+ Service Broker is supported, Service Broker endpoints are not supported
+ Change Tracking
+ Database Mirroring or Always On
+ The ability to use an Amazon RDS SQL DB instance as a data source for Reporting, Analysis, and Integration Services that are running on a separate server\.

For a list of unsupported features, see [Features Not Supported](#SQLServer.Concepts.General.FeatureNonSupport)\. 

## Microsoft SQL Server Engine Version Management<a name="SQLServer.Concepts.General.Patching"></a>

With Amazon RDS, you control when to upgrade your SQL Server DB instance to new versions supported by Amazon RDS\. You can maintain compatibility with specific SQL Server versions, test new versions with your application before deploying in production, and perform version upgrades on your own terms and timelines\. 

Currently, you perform all SQL Server database upgrades manually\. For more information about upgrading a SQL Server DB instance, see [Upgrading the Microsoft SQL Server DB Engine](USER_UpgradeDBInstance.SQLServer.md)\. 

## Change Data Capture Support for Microsoft SQL Server DB Instances<a name="SQLServer.Concepts.General.CDC"></a>

Amazon RDS supports change data capture \(CDC\) for your DB instances running Microsoft SQL Server\. CDC captures changes that are made to the data in your tables, and stores metadata about each change that you can access later\. For more information, see [Change Data Capture](https://docs.microsoft.com/en-us/sql/relational-databases/track-changes/track-data-changes-sql-server#Capture) in the Microsoft documentation\. 

Amazon RDS supports CDC for the following SQL Server editions and versions:
+ Microsoft SQL Server Enterprise Edition \(2016, 2014, 2012, 2008 R2\) 
+ Microsoft SQL Server Standard Edition \(2016 version 13\.00\.4422\.0 SP1 CU2 and later\) 

To use CDC with your Amazon RDS DB instances, first enable or disable CDC at the database level by using RDS\-provided stored procedures\. After that, any user that has the `db_owner` role for that database can use the native Microsoft stored procedures to control CDC on that database\. For more information, see [Using Change Data Capture](Appendix.SQLServer.CommonDBATasks.CDC.md)\. 

You can use CDC and AWS Database Migration Service to enable ongoing replication from SQL Server DB instances\.  

## Features Not Supported<a name="SQLServer.Concepts.General.FeatureNonSupport"></a>

The following Microsoft SQL Server features are not supported on Amazon RDS: 
+ Stretch database
+ Backing up to Microsoft Azure Blob Storage
+ Buffer pool extension
+ BULK INSERT and OPENROWSET\(BULK\.\.\.\) features
+ Data Quality Services
+ Database Log Shipping
+ Database Mail
+ Distributed Queries \(i\.e\., Linked Servers\)
+ Distribution Transaction Coordinator \(MSDTC\)
+ File tables
+ FILESTREAM support
+ Maintenance Plans
+ Performance Data Collector
+ Policy\-Based Management
+ PolyBase
+ R
+ Replication
+ Resource Governor
+ SQL Server Audit
+ Server\-level triggers
+ Service Broker endpoints
+ T\-SQL endpoints \(all operations using CREATE ENDPOINT are unavailable\)
+ WCF Data Services

## Multi\-AZ Deployments Using Microsoft SQL Server Mirroring or Always On<a name="SQLServer.Concepts.General.Mirroring"></a>

Amazon RDS supports Multi\-AZ deployments for DB instances running Microsoft SQL Server by using SQL Server Database Mirroring or Always On\. Multi\-AZ deployments provide increased availability, data durability, and fault tolerance for DB instances\. In the event of planned database maintenance or unplanned service disruption, Amazon RDS automatically fails over to the up\-to\-date secondary replica so database operations can resume quickly without manual intervention\. The primary and secondary instances use the same endpoint, whose physical network address transitions to the passive secondary replica as part of the failover process\. You don't have to reconfigure your application when a failover occurs\. 

Amazon RDS manages failover by actively monitoring your Multi\-AZ deployment and initiating a failover when a problem with your primary occurs\. Failover doesn't occur unless the standby and primary are fully in sync\. Amazon RDS actively maintains your Multi\-AZ deployment by automatically repairing unhealthy DB instances and re\-establishing synchronous replication\. You don't have to manage anything\. Amazon RDS handles the primary, the witness, and the standby instance for you\. When you set up SQL Server Multi\-AZ, RDS configures passive secondary instances for all of the databases on the instance\. 

For more information, see [Multi\-AZ Deployments for Microsoft SQL Server](USER_SQLServerMultiAZ.md)\. 

## Using Transparent Data Encryption to Encrypt Data at Rest<a name="SQLServer.Concepts.General.Options"></a>

Amazon RDS supports Microsoft SQL Server Transparent Data Encryption \(TDE\), which transparently encrypts stored data\. Amazon RDS uses option groups to enable and configure these features\. For more information about the TDE option, see [Microsoft SQL Server Transparent Data Encryption Support](Appendix.SQLServer.Options.TDE.md)\. 

## Local Time Zone for Microsoft SQL Server DB Instances<a name="SQLServer.Concepts.General.TimeZone"></a>

The time zone of an Amazon RDS DB instance running Microsoft SQL Server is set by default\. The current default is Universal Coordinated Time \(UTC\)\. You can set the time zone of your DB instance to a local time zone instead, to match the time zone of your applications\. 

You set the time zone when you first create your DB instance\. You can create your DB instance by using the [AWS Management Console](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_CreateMicrosoftSQLServerInstance.html), the Amazon RDS API [CreateDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html.html) action, or the AWS CLI [create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) command\. 

If your DB instance is part of a Multi\-AZ deployment \(using SQL Server Mirroring or Always On\), then when you fail over, your time zone remains the local time zone that you set\. For more information, see [Multi\-AZ Deployments Using Microsoft SQL Server Mirroring or Always On](#SQLServer.Concepts.General.Mirroring)\. 

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