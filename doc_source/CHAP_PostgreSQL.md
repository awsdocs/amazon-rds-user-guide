# PostgreSQL on Amazon RDS<a name="CHAP_PostgreSQL"></a>

Amazon RDS supports DB instances running several versions of PostgreSQL\. You can create DB instances and DB snapshots, point\-in\-time restores and backups\. DB instances running PostgreSQL support Multi\-AZ deployments, read replicas, Provisioned IOPS, and can be created inside a VPC\. You can also use Secure Socket Layer \(SSL\) to connect to a DB instance running PostgreSQL\.

Before creating a DB instance, you should complete the steps in the [Setting Up for Amazon RDS](CHAP_SettingUp.md) section of this guide\. 

You can use any standard SQL client application to run commands for the instance from your client computer\. Such applications include *pgAdmin*, a popular Open Source administration and development tool for PostgreSQL, or *psql*, a command line utility that is part of a PostgreSQL installation\. To deliver a managed service experience, Amazon RDS doesn't provide host access to DB instances, and it restricts access to certain system procedures and tables that require advanced privileges\. Amazon RDS supports access to databases on a DB instance using any standard SQL client application\. Amazon RDS doesn't allow direct host access to a DB instance by using Telnet or Secure Shell \(SSH\)\. 

Amazon RDS for PostgreSQL is compliant with many industry standards\. For example, you can use Amazon RDS for PostgreSQL databases to build HIPAA\-compliant applications and to store healthcare\-related information, including protected health information \(PHI\) under a completed Business Associate Agreement \(BAA\) with AWS\. Amazon RDS for PostgreSQL also meets Federal Risk and Authorization Management Program \(FedRAMP\) security requirements\. Amazon RDS for PostgreSQL has received a FedRAMP Joint Authorization Board \(JAB\) Provisional Authority to Operate \(P\-ATO\) at the FedRAMP HIGH Baseline within the AWS GovCloud \(US\-West\) Region\. For more information on supported compliance standards, see [AWS Cloud Compliance](https://aws.amazon.com/compliance/)\.

To import PostgreSQL data into a DB instance, follow the information in the [Importing Data into PostgreSQL on Amazon RDS](PostgreSQL.Procedural.Importing.md) section\. 

**Topics**
+ [Common Management Tasks for PostgreSQL on Amazon RDS](#CHAP_PostgreSQL.CommonTasks)
+ [Connecting to a DB Instance Running the PostgreSQL Database Engine](USER_ConnectToPostgreSQLInstance.md)
+ [Updating Applications to Connect to PostgreSQL DB Instances Using New SSL/TLS Certificates](ssl-certificate-rotation-postgresql.md)
+ [Upgrading the PostgreSQL DB Engine for Amazon RDS](USER_UpgradeDBInstance.PostgreSQL.md)
+ [Upgrading a PostgreSQL DB Snapshot](USER_UpgradeDBSnapshot.PostgreSQL.md)
+ [Working with PostgreSQL Read Replicas in Amazon RDS](USER_PostgreSQL.Replication.ReadReplicas.md)
+ [Importing Data into PostgreSQL on Amazon RDS](PostgreSQL.Procedural.Importing.md)
+ [Common DBA Tasks for PostgreSQL](Appendix.PostgreSQL.CommonDBATasks.md)
+ [Using Kerberos Authentication with Amazon RDS for PostgreSQL](postgresql-kerberos.md)
+ [Working with the Database Preview Environment](#working-with-the-database-preview-environment)
+ [Amazon RDS for PostgreSQL Versions and Extensions](#PostgreSQL.Concepts)

## Common Management Tasks for PostgreSQL on Amazon RDS<a name="CHAP_PostgreSQL.CommonTasks"></a>

The following are the common management tasks you perform with an Amazon RDS for PostgreSQL DB instance, with links to relevant documentation for each task\. 


| Task Area | Relevant Documentation | 
| --- | --- | 
|  **Setting up Amazon RDS for first\-time use** There are prerequisites you must complete before you create your DB instance\. For example, DB instances are created by default with a firewall that prevents access to it\. You therefore must create a security group with the correct IP addresses and network configuration to access the DB instance\.   |  [Setting Up for Amazon RDS](CHAP_SettingUp.md)  | 
|  **Understanding Amazon RDS DB instances** If you are creating a DB instance for production purposes, you should understand how instance classes, storage types, and Provisioned IOPS work in Amazon RDS\.   |  [DB Instance Classes](Concepts.DBInstanceClass.md) [Amazon RDS Storage Types](CHAP_Storage.md#Concepts.Storage) [Provisioned IOPS SSD Storage](CHAP_Storage.md#USER_PIOPS)  | 
|  **Finding supported PostgreSQL versions** Amazon RDS supports several versions of PostgreSQL\.   |  [Supported PostgreSQL Database Versions](#PostgreSQL.Concepts.General.DBVersions)  | 
|  **Setting up high availability and failover support** A production DB instance should use Multi\-AZ deployments\. Multi\-AZ deployments provide increased availability, data durability, and fault tolerance for DB instances\.   |  [High Availability \(Multi\-AZ\) for Amazon RDS](Concepts.MultiAZ.md)  | 
|  **Understanding the Amazon Virtual Private Cloud \(VPC\) network** If your AWS account has a default VPC, then your DB instance is automatically created inside the default VPC\. In some cases, your account might not have a default VPC, and you might want the DB instance in a VPC\. In these cases, create the VPC and subnet groups before you create the DB instance\.   |  [Determining Whether You Are Using the EC2\-VPC or EC2\-Classic Platform](USER_VPC.FindDefaultVPC.md) [Working with a DB Instance in a VPC](USER_VPC.WorkingWithRDSInstanceinaVPC.md)  | 
|  **Importing data into Amazon RDS PostgreSQL** You can use several different tools to import data into your PostgreSQL DB instance on Amazon RDS\.   |  [Importing Data into PostgreSQL on Amazon RDS](PostgreSQL.Procedural.Importing.md)  | 
|  **Setting up read\-only read replicas \(primary and standbys\)** PostgreSQL on Amazon RDS supports read replicas in both the same AWS Region and in a different AWS Region from the primary instance\.  |  [Working with Read Replicas](USER_ReadRepl.md) [Working with PostgreSQL Read Replicas in Amazon RDS](USER_PostgreSQL.Replication.ReadReplicas.md) [Creating a Read Replica in a Different AWS Region](USER_ReadRepl.md#USER_ReadRepl.XRgn)  | 
|  **Understanding security groups** By default, DB instances are created with a firewall that prevents access to them\. You therefore must create a security group with the correct IP addresses and network configuration to access the DB instance\.  In general, if your DB instance is on the EC2\-Classic platform, you need to create a DB security group\. If your DB instance is on the EC2\-VPC platform, you need to create a VPC security group\.   |  [Determining Whether You Are Using the EC2\-VPC or EC2\-Classic Platform](USER_VPC.FindDefaultVPC.md) [Controlling Access with Security Groups](Overview.RDSSecurityGroups.md)  | 
|  **Setting up parameter groups and features** If your DB instance is going to require specific database parameters, you should create a parameter group before you create the DB instance\.   |  [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)  | 
|  **Performing common DBA tasks for PostgreSQL** Some of the more common tasks for PostgreSQL DBAs include:  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html)  |  [Common DBA Tasks for PostgreSQL](Appendix.PostgreSQL.CommonDBATasks.md)  | 
|  **Connecting to your PostgreSQL DB instance** After creating a security group and associating it to a DB instance, you can connect to the DB instance using any standard SQL client application such as pgadmin III\.   |  [Connecting to a DB Instance Running the PostgreSQL Database Engine](USER_ConnectToPostgreSQLInstance.md) [Using SSL with a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.SSL)  | 
|  **Backing up and restoring your DB instance** You can configure your DB instance to take automated backups, or take manual snapshots, and then restore instances from the backups or snapshots\.   |  [Backing Up and Restoring an Amazon RDS DB Instance](CHAP_CommonTasks.BackupRestore.md)  | 
|  **Monitoring the activity and performance of your DB instance** You can monitor a PostgreSQL DB instance by using CloudWatch Amazon RDS metrics, events, and enhanced monitoring\.   |  [Viewing DB Instance Metrics](MonitoringOverview.md#USER_Monitoring) [Viewing Amazon RDS Events](USER_ListEvents.md)  | 
|  **Upgrading the PostgreSQL database version** You can do both major and minor version upgrades for your PostgreSQL DB instance\.   |  [Upgrading a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.Patching) [ Choosing a Major Version Upgrade for PostgreSQL ](USER_UpgradeDBInstance.PostgreSQL.md#USER_UpgradeDBInstance.PostgreSQL.MajorVersion)  | 
|  **Working with log files** You can access the log files for your PostgreSQL DB instance\.   |  [PostgreSQL Database Log Files](USER_LogAccess.Concepts.PostgreSQL.md)  | 
|  **Understanding the best practices for PostgreSQL DB instances** Find some of the best practices for working with PostgreSQL on Amazon RDS\.   |  [Best Practices for Working with PostgreSQL](CHAP_BestPractices.md#CHAP_BestPractices.PostgreSQL)  | 

## Working with the Database Preview Environment<a name="working-with-the-database-preview-environment"></a>

When you create a DB instance in Amazon RDS, you know that the PostgreSQL version it's based on has been tested and is fully supported by Amazon\. The PostgreSQL community releases new versions and new extensions continuously\. You can try out new PostgreSQL versions and extensions before they are fully supported\. To do that, you can create a new DB instance in the Database Preview Environment\. 

DB instances in the Database Preview Environment are similar to DB instances in a production environment\. However, keep in mind several important factors:
+ All DB instances are deleted 60 days after you create them, along with any backups and snapshots\.
+ You can only create a DB instance in a virtual private cloud \(VPC\) based on the Amazon VPC service\.
+ You can only create M5, T3, and R5 instance types\. For more information about RDS instance classes, see [DB Instance Classes](Concepts.DBInstanceClass.md)\. 
+ You can only use General Purpose SSD and Provisioned IOPS SSD storage\. 
+ You can't get help from AWS Support with DB instances\. You can post your questions in the [RDS Database Preview Environment Forum](https://forums.aws.amazon.com/forum.jspa?forumID=301)\.
+ You can't copy a snapshot of a DB instance to a production environment\.
+ You can use both single\-AZ and multi\-AZ deployments\.
+ You can use standard PostgreSQL dump and load functions to export databases from or import databases to the Database Preview Environment\.

**Topics**
+ [Features Not Supported in the Preview Environment](#preview-environment-exclusions)
+ [PostgreSQL Extensions Supported in the Preview Environment](#preview-environment-extensions)
+ [Creating a New DB Instance in the Preview Environment](#create-db-instance-in-preview-environment)

### Features Not Supported in the Preview Environment<a name="preview-environment-exclusions"></a>

The following features are not available in the preview environment:
+ Cross\-region snapshot copy
+ Cross\-region read replicas
+ Extensions not in the following table of supported extensions

### PostgreSQL Extensions Supported in the Preview Environment<a name="preview-environment-extensions"></a>

The PostgreSQL extensions supported in the Database Preview Environment are listed in the following table\.


| Extension | Version | 
| --- | --- | 
|  amcheck  |  1\.2  | 
| aws\_commons | 1\.0 | 
| aws\_s3 | 1\.0 | 
|  bloom  |  1\.0  | 
|  btree\_gin  |  1\.3  | 
|  btree\_gist  |  1\.5  | 
|  citext  |  1\.6  | 
|  cube  |  1\.4  | 
|  dblink  |  1\.2  | 
|  dict\_int  |  1\.0  | 
|  dict\_xsyn  |  1\.0  | 
|  earthdistance  |  1\.1  | 
|  fuzzystrmatch  |  1\.1  | 
|  hstore  |  1\.7  | 
|  hstore\_plper  |  1\.0  | 
|  intagg  |  1\.1  | 
|  intarray  |  1\.3  | 
| ip4r | 2\.4 | 
|  isn  |  1\.2  | 
| jsonb\_plperl | 1\.0 | 
|  ltree  |  1\.2  | 
| pageinspect | 1\.8 | 
|  pg\_buffercache  |  1\.3  | 
|  pg\_freespacemap  |  1\.2  | 
|  pg\_prewarm  |  1\.2  | 
| pg\_similarity | 1\.0  | 
|  pg\_stat\_statements  |  1\.8  | 
| pg\_transport | 1\.0 | 
|  pg\_trgm  |  1\.5  | 
|  pg\_visibility  |  1\.2  | 
|  pgcrypto  |  1\.3  | 
| pgrouting | 3\.0\.0 | 
|  pgrowlocks  |  1\.2  | 
|  pgstattuple  |  1\.5  | 
| pgtap | 1\.1\.0 | 
|  plperl  |  1\.0  | 
|  plpgsql  |  1\.0  | 
| plprofiler | 4\.1 | 
|  pltcl  |  1\.0  | 
|  postgres\_fdw  |  1\.0  | 
| prefix | 1\.2\.0 | 
|  sslinfo  |  1\.2  | 
|  tablefunc  |  1\.0  | 
|  test\_parser  |  1\.0  | 
|  tsm\_system\_rows  |  1\.0  | 
|  tsm\_system\_time  |  1\.0  | 
|  unaccent  |  1\.1  | 
|  uuid\_ossp  |  1\.1  | 

### Creating a New DB Instance in the Preview Environment<a name="create-db-instance-in-preview-environment"></a>

Use the following procedure to create a DB instance in the preview environment\.

**To create a DB instance in the preview environment**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1.  Choose **Dashboard** from the navigation pane\. 

1. Choose **Switch to database preview environment**\.   
![\[Dialog box to select the preview environment\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/instance_preview.png)

   You also can navigate directly to the [Database Preview Environment](https://us-east-2.console.aws.amazon.com/rds-preview/home?region=us-east-2#)\.
**Note**  
If you want to create an instance in the Database Preview Environment with the API or CLI, the endpoint is `rds-preview.us-east-2.amazonaws.com`\.

1. Continue with the procedure as described in [Console](USER_CreateDBInstance.md#USER_CreateDBInstance.CON)\.

## Amazon RDS for PostgreSQL Versions and Extensions<a name="PostgreSQL.Concepts"></a>

Amazon RDS supports DB instances running several editions of PostgreSQL\. Use this section to see how to work with PostgreSQL on Amazon RDS\. You should also be aware of the limits for PostgreSQL DB instances\.

You can specify any currently supported PostgreSQL version when creating a new DB instance\. You can specify the major version \(such as PostgreSQL 10\), and any supported minor version for the specified major version\. If no version is specified, Amazon RDS defaults to a supported version, typically the most recent version\. If a major version is specified but a minor version is not, Amazon RDS defaults to a recent release of the major version you have specified\. To see a list of supported versions, as well as defaults for newly created DB instances, use the [ `describe-db-engine-versions`](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-engine-versions.html) AWS CLI command\.

For information about importing PostgreSQL data into a DB instance, see [Importing Data into PostgreSQL on Amazon RDS](PostgreSQL.Procedural.Importing.md)\.

**Topics**
+ [Supported PostgreSQL Database Versions](#PostgreSQL.Concepts.General.DBVersions)
+ [Supported PostgreSQL Features and Extensions](#PostgreSQL.Concepts.General.FeaturesExtensions)

### Supported PostgreSQL Database Versions<a name="PostgreSQL.Concepts.General.DBVersions"></a>

Amazon RDS supports the following PostgreSQL versions\.

**Topics**
+ [PostgreSQL 13 Versions](#PostgreSQL.Concepts.General.version13)
+ [PostgreSQL 12 Versions](#PostgreSQL.Concepts.General.version12)
+ [PostgreSQL 11 Versions](#PostgreSQL.Concepts.General.version11)
+ [PostgreSQL 10 Versions](#PostgreSQL.Concepts.General.version10)
+ [PostgreSQL 9\.6 Versions](#PostgreSQL.Concepts.General.version96)
+ [PostgreSQL 9\.5 Versions](#PostgreSQL.Concepts.General.version95)

#### PostgreSQL 13 Versions<a name="PostgreSQL.Concepts.General.version13"></a>

**Topics**
+ [PostgreSQL Version 13 Beta 3 on Amazon RDS in the Database Preview Environment](#PostgreSQL.Concepts.General.version13beta3)
+ [PostgreSQL Version 13 Beta 1 on Amazon RDS in the Database Preview Environment](#PostgreSQL.Concepts.General.version13beta1)

##### PostgreSQL Version 13 Beta 3 on Amazon RDS in the Database Preview Environment<a name="PostgreSQL.Concepts.General.version13beta3"></a>

PostgreSQL version 13 Beta 3 contains several improvements that are described in [PostgreSQL 12\.4, 11\.9, 10\.14, 9\.6\.19, 9\.5\.23, and 13 Beta 3 Released\!](https://www.postgresql.org/about/news/2060/) 

For information on the Database Preview Environment, see [Working with the Database Preview Environment](#working-with-the-database-preview-environment)\. To access the Preview Environment from the console, select [https://console\.aws\.amazon\.com/rds\-preview/](https://console.aws.amazon.com/rds-preview/)\. 

This version also adds the following extensions:
+ address\_standardizer
+ address\_standardizer\_data\_us
+ log\_fdw
+ pgaudit
+ plcoffee
+ plls
+ plv8
+ PostGIS
+ postgis\_raster
+ postgis\_tiger\_geocoder
+ postgis\_topology
+ postgresql\-hll
+ rdkit

For information on extensions and modules, see [PostgreSQL Version 13 Extensions and Modules Supported on Amazon RDS](#PostgreSQL.Concepts.General.FeatureSupport.Extensions.13x)\.

##### PostgreSQL Version 13 Beta 1 on Amazon RDS in the Database Preview Environment<a name="PostgreSQL.Concepts.General.version13beta1"></a>

PostgreSQL version 13 Beta 1 contains several improvements that are described in [PostgreSQL 13 Beta 1 Released](https://www.postgresql.org/about/news/2040/)\. 

For information on the Database Preview Environment, see [Working with the Database Preview Environment](#working-with-the-database-preview-environment)\. To access the Preview Environment from the console, select [https://console\.aws\.amazon\.com/rds\-preview/](https://console.aws.amazon.com/rds-preview/)\. 

#### PostgreSQL 12 Versions<a name="PostgreSQL.Concepts.General.version12"></a>

**Topics**
+ [PostgreSQL Version 12\.3 on Amazon RDS](#PostgreSQL.Concepts.General.version123)
+ [PostgreSQL Version 12\.2 on Amazon RDS](#PostgreSQL.Concepts.General.version122)

##### PostgreSQL Version 12\.3 on Amazon RDS<a name="PostgreSQL.Concepts.General.version123"></a>

PostgreSQL version 12\.3 is now available on Amazon RDS\. PostgreSQL version 12\.3 contains several improvements that were announced for PostgreSQL release [12\.3](https://www.postgresql.org/docs/12/release-12-3.html)\. 

This version also includes the following changes:

1. Upgraded the `pg_hint_plan` extension to version 1\.3\.5\.

1. Upgraded the `pglogical` extension to version 2\.3\.1\.

For information on extensions and modules, see [PostgreSQL Version 12 Extensions and Modules Supported on Amazon RDS](#PostgreSQL.Concepts.General.FeatureSupport.Extensions.12x)\.

##### PostgreSQL Version 12\.2 on Amazon RDS<a name="PostgreSQL.Concepts.General.version122"></a>

PostgreSQL version 12\.2 is now available on Amazon RDS\. PostgreSQL version 12\.2 contains several improvements that were announced for PostgreSQL releases [12\.0](https://www.postgresql.org/docs/12/release-12.html), [12\.1](https://www.postgresql.org/docs/12/release-12-1.html), and [12\.2](https://www.postgresql.org/docs/12/release-12-2.html)\. 

For information on extensions and modules, see [PostgreSQL Version 12 Extensions and Modules Supported on Amazon RDS](#PostgreSQL.Concepts.General.FeatureSupport.Extensions.12x)\.

#### PostgreSQL 11 Versions<a name="PostgreSQL.Concepts.General.version11"></a>

**Topics**
+ [PostgreSQL Version 11\.8 on Amazon RDS](#PostgreSQL.Concepts.General.version118)
+ [PostgreSQL Version 11\.7 on Amazon RDS](#PostgreSQL.Concepts.General.version117)
+ [PostgreSQL Version 11\.6 on Amazon RDS](#PostgreSQL.Concepts.General.version116)
+ [PostgreSQL Version 11\.5 on Amazon RDS](#PostgreSQL.Concepts.General.version115)
+ [PostgreSQL Version 11\.4 on Amazon RDS](#PostgreSQL.Concepts.General.version114)
+ [PostgreSQL Version 11\.2 on Amazon RDS](#PostgreSQL.Concepts.General.version112)
+ [PostgreSQL Version 11\.1 on Amazon RDS](#PostgreSQL.Concepts.General.version111)
+ [PostgreSQL Version 11 on Amazon RDS in the Database Preview Environment](#PostgreSQL.Concepts.General.version110)

##### PostgreSQL Version 11\.8 on Amazon RDS<a name="PostgreSQL.Concepts.General.version118"></a>

PostgreSQL version 11\.8 contains several bug fixes for issues in release 11\.7\. For more information on the fixes in PostgreSQL 11\.8, see the [PostgreSQL 11\.8 documentation](https://www.postgresql.org/docs/11/release-11-8.html)\. 

This version also includes the following change:

1. Upgraded the `pg_hint_plan` extension to version 1\.3\.5\.

For information on extensions and modules, see [PostgreSQL Version 11\.x Extensions and Modules Supported on Amazon RDS](#PostgreSQL.Concepts.General.FeatureSupport.Extensions.11x)\.

##### PostgreSQL Version 11\.7 on Amazon RDS<a name="PostgreSQL.Concepts.General.version117"></a>

PostgreSQL version 11\.7 contains several bug fixes for issues in release 11\.6\. For more information on the fixes in PostgreSQL 11\.7, see the [PostgreSQL 11\.7 documentation](https://www.postgresql.org/docs/11/release-11-7.html)\. 

##### PostgreSQL Version 11\.6 on Amazon RDS<a name="PostgreSQL.Concepts.General.version116"></a>

PostgreSQL version 11\.6 contains several bug fixes for issues in release 11\.5\. For more information on the fixes in PostgreSQL 11\.6, see the [PostgreSQL documentation](https://www.postgresql.org/docs/11/release-11-6.html)\. 

This version also includes the following changes:

1. Upgraded the `pgTAP` extension to version 1\.1\.0\.

1. Added the `plprofiler` extension\.

1. Added to `shared_preload_libraries` support for `pg_prewarm` to start automatically\.

##### PostgreSQL Version 11\.5 on Amazon RDS<a name="PostgreSQL.Concepts.General.version115"></a>

PostgreSQL version 11\.5 contains several bug fixes for issues in release 11\.4\. For more information on the fixes in PostgreSQL 11\.5, see the [PostgreSQL documentation](https://www.postgresql.org/docs/11/release-11-5.html)\. 

This version also includes the following changes:
+ A new extension `pg_transport` is added\.
+ The extension `aws_s3` has been updated to support virtual\-hosted style requests\. For more information, see [Amazon S3 Path Deprecation Plan – The Rest of the Story](https://aws.amazon.com/blogs/aws/amazon-s3-path-deprecation-plan-the-rest-of-the-story/)\. 
+ The `PostGIS` extension is updated to version 2\.5\.2\.

##### PostgreSQL Version 11\.4 on Amazon RDS<a name="PostgreSQL.Concepts.General.version114"></a>

This release contains an important security fix and also bug fixes and improvements done by the PostgreSQL community\. For more information on the security fix, see the [PostgreSQL community announcement](https://www.postgresql.org/about/news/1949/) and the security fix CVE\-2019\-10164\. 

With this release, the `pg_hint_plan` extension has been updated to version 1\.3\.4\.

For more information on the fixes in PostgreSQL 11\.4, see the [PostgreSQL documentation](https://www.postgresql.org/docs/11/release-11-4.html)\.

##### PostgreSQL Version 11\.2 on Amazon RDS<a name="PostgreSQL.Concepts.General.version112"></a>

PostgreSQL version 11\.2 contains several bug fixes for issues in release 11\.1\. For more information on the fixes in PostgreSQL 11\.2, see the [PostgreSQL documentation](https://www.postgresql.org/docs/11/release-11-2.html)\.

This version also includes the following changes:
+ A new [pgTAP](https://pgtap.org/) extension version 1\.0\.
+ Support for Amazon S3 import\. For more information, see [Importing Amazon S3 Data into an RDS for PostgreSQL DB Instance](PostgreSQL.Procedural.Importing.md#USER_PostgreSQL.S3Import)\.
+ Multiple major version upgrade is available to PostgreSQL 11\.2 from certain previous PostgreSQL versions\. For more information, see [ Choosing a Major Version Upgrade for PostgreSQL ](USER_UpgradeDBInstance.PostgreSQL.md#USER_UpgradeDBInstance.PostgreSQL.MajorVersion)\.

For information on upgrading the engine version for your PostgreSQL DB instance, see [Upgrading a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.Patching)\. 

For the complete list of extensions supported by Amazon RDS for PostgreSQL, see [Supported PostgreSQL Features and Extensions](#PostgreSQL.Concepts.General.FeaturesExtensions)\.

##### PostgreSQL Version 11\.1 on Amazon RDS<a name="PostgreSQL.Concepts.General.version111"></a>

PostgreSQL version 11\.1 contains several improvements that were announced in [PostgreSQL 11\.1 Released\!](https://www.postgresql.org/about/news/1905/) This version includes SQL stored procedures that enable embedded transactions within a procedure\. This version also includes major improvements to partitioning and parallelism and many useful performance improvements\. For example, by using a non\-null constant for a column default, you can now use an ALTER TABLE command to add a column without causing a table rewrite\.

PostgreSQL version 11\.1 contains several bug fixes for issues in release 11\. For complete details, see the [PostgreSQL Release 11\.1 documentation](https://www.postgresql.org/docs/11/release-11-1.html)\. Some changes in this version include the following:
+ Partitioning – Partitioning improvements include support for hash partitioning, enabling creation of a default partition, and dynamic row movement to another partition based on the key column update\.
+ Performance – Performance improvements include parallelism while creating indexes, materialized views, hash joins, and sequential scans to make the operations perform better\.
+ Stored procedures – SQL stored procedures now added support embedded transactions\.
+ Support for Just\-In\-Time \(JIT\) capability – RDS PostgreSQL 11 instances are created with JIT capability, speeding evaluation of expressions\. To enable JIT capability, set the `jit` parameter to 1 in the PostgreSQL parameter group for the database\. 
+ Segment size – The write\-ahead logging \(WAL\) segment size has been changed from 16 MB to 64 MB\.
+ Autovacuum improvements – To provide valuable logging, the parameter `rds.force_autovacuum_logging` is ON by default in conjunction with the `log_autovacuum_min_duration` parameter set to 10 seconds\. To increase autovacuum effectiveness, the values for the `autovacuum_max_workers` and `autovacuum_vacuum_cost_limit` parameters are computed based on host memory capacity to provide larger default values\.
+ Improved transaction timeout – The parameter `idle_in_transaction_session_timeout` is set to 12 hours\. Any session that has been idle more than 12 hours is terminated\.
+ Performance metrics – The `pg_stat_statements` module is included in `shared_preload_libraries` by default\. This avoids having to reboot the instance immediately after creation\. However, this functionality still requires you to run the statement `CREATE EXTENSION pg_stat_statements;`\. Also, `track_io_timing` is enabled by default to add more granular data to `pg_stat_statements`\.
+ The tsearch2 module is no longer supported – If your application uses `tsearch2` functions, update it to use the equivalent functions provided by the core PostgreSQL engine\. For more information about the tsearch2 module, see [PostgreSQL tsearch2](https://www.postgresql.org/docs/9.6/static/tsearch2.html)\.
+ The chkpass module is no longer supported – For more information about the `chkpass` module, see [PostgreSQL chkpass](https://www.postgresql.org/docs/10/chkpass.html)\.
+ Extension updates for RDS PostgreSQL 11\.1 include the following: 
  + `pgaudit` is updated to 1\.3\.0\.
  + `pg_hint_plan` is updated to 1\.3\.2\.
  + `pglogical` is updated to 2\.2\.1\.
  + `plcoffee` is updated to 2\.3\.8\.
  + `plv8` is updated to 2\.3\.8\.
  + `PostGIS` is updated to 2\.5\.1\.
  + `prefix` is updated to 1\.2\.8\.
  + `wal2json` is updated to hash 9e962bad\.

For the complete list of extensions supported by Amazon RDS for PostgreSQL, see [Supported PostgreSQL Features and Extensions](#PostgreSQL.Concepts.General.FeaturesExtensions)\.

##### PostgreSQL Version 11 on Amazon RDS in the Database Preview Environment<a name="PostgreSQL.Concepts.General.version110"></a>

**Note**  
PostgreSQL Version 11 on Amazon RDS has been released in the production environment\. It is no longer supported in the Database Preview Environment\.

PostgreSQL version 11 contains several improvements that are described in [PostgreSQL 11 Released\!](https://www.postgresql.org/about/news/1894/) 

For information on the Database Preview Environment, see [Working with the Database Preview Environment](#working-with-the-database-preview-environment)\. To access the Preview Environment from the console, select [https://console\.aws\.amazon\.com/rds\-preview/](https://console.aws.amazon.com/rds-preview/)\. 

#### PostgreSQL 10 Versions<a name="PostgreSQL.Concepts.General.version10"></a>

**Topics**
+ [PostgreSQL Version 10\.13 on Amazon RDS](#PostgreSQL.Concepts.General.version1013)
+ [PostgreSQL Version 10\.12 on Amazon RDS](#PostgreSQL.Concepts.General.version1012)
+ [PostgreSQL Version 10\.11 on Amazon RDS](#PostgreSQL.Concepts.General.version1011)
+ [PostgreSQL Version 10\.10 on Amazon RDS](#PostgreSQL.Concepts.General.version1010)
+ [PostgreSQL Version 10\.9 on Amazon RDS](#PostgreSQL.Concepts.General.version109)
+ [PostgreSQL Version 10\.7 on Amazon RDS](#PostgreSQL.Concepts.General.version107)
+ [PostgreSQL Version 10\.6 on Amazon RDS](#PostgreSQL.Concepts.General.version106)
+ [PostgreSQL Version 10\.5 on Amazon RDS](#PostgreSQL.Concepts.General.version105)
+ [PostgreSQL Version 10\.4 on Amazon RDS](#PostgreSQL.Concepts.General.version104)
+ [PostgreSQL Version 10\.3 on Amazon RDS](#PostgreSQL.Concepts.General.version103)
+ [PostgreSQL Version 10\.1 on Amazon RDS](#PostgreSQL.Concepts.General.version101)

##### PostgreSQL Version 10\.13 on Amazon RDS<a name="PostgreSQL.Concepts.General.version1013"></a>

PostgreSQL version 10\.13 contains several bug fixes for issues in release 10\.12\. For more information on the fixes in PostgreSQL 10\.13, see the [PostgreSQL 10\.13 documentation](https://www.postgresql.org/docs/10/release-10-13.html)\. 

This version also includes the following change:

1. Upgraded the `pg_hint_plan` extension to version 1\.3\.5\.

For information on extensions and modules, see [PostgreSQL Version 10\.x Extensions and Modules Supported on Amazon RDS](#PostgreSQL.Concepts.General.FeatureSupport.Extensions.101x)\.

##### PostgreSQL Version 10\.12 on Amazon RDS<a name="PostgreSQL.Concepts.General.version1012"></a>

PostgreSQL version 10\.12 contains several bug fixes for issues in release 10\.11\. For more information on the fixes in PostgreSQL 10\.12, see the [PostgreSQL 10\.12 documentation](https://www.postgresql.org/docs/10/release-10-12.html)\. 

##### PostgreSQL Version 10\.11 on Amazon RDS<a name="PostgreSQL.Concepts.General.version1011"></a>

PostgreSQL version 10\.11 contains several bug fixes for issues in release 10\.10\. For more information on the fixes in PostgreSQL 10\.11, see the [PostgreSQL documentation](https://www.postgresql.org/docs/10/release-10-11.html)\. Changes in this version include the following:

1. Added the `plprofiler` extension\.

##### PostgreSQL Version 10\.10 on Amazon RDS<a name="PostgreSQL.Concepts.General.version1010"></a>

PostgreSQL version 10\.10 contains several bug fixes for issues in release 10\.9\. For more information on the fixes in PostgreSQL 10\.10, see the [PostgreSQL documentation](https://www.postgresql.org/docs/10/release-10-10.html)\. Changes in this version include the following:

1. The `aws_s3` extension is updated to support virtual\-hosted style requests\. For more information, see [Amazon S3 Path Deprecation Plan – The Rest of the Story](https://aws.amazon.com/blogs/aws/amazon-s3-path-deprecation-plan-the-rest-of-the-story/)\. 

1. The `PostGIS` extension is updated to version 2\.5\.2\.

##### PostgreSQL Version 10\.9 on Amazon RDS<a name="PostgreSQL.Concepts.General.version109"></a>

This release contains an important security fix and also bug fixes and improvements done by the PostgreSQL community\. For more information on the security fix, see the [PostgreSQL community announcement](https://www.postgresql.org/about/news/1949/) and [security fix CVE\-2019\-10164](https://cve.mitre.org/cgi-bin/cvename.cgi?name=2019-10164)\. 

With this release, the `pg_hint_plan` extension has been updated to version 1\.3\.3\.

For more information on the fixes in PostgreSQL 10\.9, see the [PostgreSQL documentation](https://www.postgresql.org/docs/10/release-10-9.html)\.

##### PostgreSQL Version 10\.7 on Amazon RDS<a name="PostgreSQL.Concepts.General.version107"></a>

PostgreSQL version 10\.7 contains several bug fixes for issues in release 10\.6\. For more information on the fixes in 10\.7, see the [PostgreSQL documentation](https://www.postgresql.org/docs/10/release-10-7.html)\.

This version also includes the following changes:
+ Support for Amazon S3 import\. For more information, see [Importing Amazon S3 Data into an RDS for PostgreSQL DB Instance](PostgreSQL.Procedural.Importing.md#USER_PostgreSQL.S3Import)\.
+ Multiple major version upgrade is available to PostgreSQL 10\.7 from certain previous PostgreSQL versions\. For more information, see [ Choosing a Major Version Upgrade for PostgreSQL ](USER_UpgradeDBInstance.PostgreSQL.md#USER_UpgradeDBInstance.PostgreSQL.MajorVersion)\.

For information on upgrading the engine version for your PostgreSQL DB instance, see [Upgrading a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.Patching)\. 

##### PostgreSQL Version 10\.6 on Amazon RDS<a name="PostgreSQL.Concepts.General.version106"></a>

PostgreSQL version 10\.6 contains several bug fixes for issues in release 10\.5\. For more information on the fixes in PostgreSQL 10\.6, see the [PostgreSQL documentation](http://www.postgresql.org/docs/10/static/release-10-6.html)\.

This version also includes the following changes:
+ A new `rds.restrict_password_commands` parameter and a new `rds_password` role have been introduced\. When the `rds.restrict_password_commands` parameter is enabled, only users who have the `rds_password` role can make user password and password expiration changes\. By restricting password\-related operations to a limited set of roles, you can implement policies such as password complexity requirements from the client side\. The `rds.restrict_password_commands` parameter is static, so it requires a database restart to change it\. For more information, see [Restricting Password Management](Appendix.PostgreSQL.CommonDBATasks.md#Appendix.PostgreSQL.CommonDBATasks.RestrictPasswordMgmt)\. 
+ The logical decoding plugin `wal2json` has been updated to commit `9e962ba`\. 

For information on upgrading the engine version for your PostgreSQL DB instance, see [Upgrading a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.Patching)\. 

For the complete list of extensions supported by Amazon RDS for PostgreSQL, see [Supported PostgreSQL Features and Extensions](#PostgreSQL.Concepts.General.FeaturesExtensions)\.

**Note**  
Amazon RDS for PostgreSQL has announced the removal of the `tsearch2` extension in the next major release\. We encourage customers still using pre\-8\.3 text search to migrate to the equivalent built\-in features\. For more information about migrating, see the [PostgreSQL documentation](https://www.postgresql.org/docs/9.6/static/textsearch-migration.html)\. 

##### PostgreSQL Version 10\.5 on Amazon RDS<a name="PostgreSQL.Concepts.General.version105"></a>

PostgreSQL version 10\.5 contains several bug fixes for issues in release 10\.4\. For more information on the fixes in 10\.5, see the [PostgreSQL documentation](http://www.postgresql.org/docs/10/static/release-10-5.html)\.

This version also includes the following changes:
+ Support for the `pglogical` extension version 2\.2\.0\. Prerequisites for using this extension are the same as the prerequisites for using logical replication for PostgreSQL as described in [Logical Replication for PostgreSQL on Amazon RDS](#PostgreSQL.Concepts.General.FeatureSupport.LogicalReplication)\.
+ Support for the `pg_similarity` extension version 1\.0\.
+ Support for the `pageinspect` extension version 1\.6\.
+ Support for the `libprotobuf` extension version 1\.3\.0 for the PostGIS component\.
+ An update for the `pg_hint_plan` extension to version 1\.3\.1\.
+ An update for the `wal2json` extension to version 01c5c1e\.

For information on upgrading the engine version for your PostgreSQL DB instance, see [Upgrading a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.Patching)\. 

For the complete list of extensions supported by Amazon RDS for PostgreSQL, see [Supported PostgreSQL Features and Extensions](#PostgreSQL.Concepts.General.FeaturesExtensions)\.

##### PostgreSQL Version 10\.4 on Amazon RDS<a name="PostgreSQL.Concepts.General.version104"></a>

PostgreSQL version 10\.4 contains several bug fixes for issues in release 10\.3\. For more information on the fixes in 10\.4, see the [PostgreSQL documentation](http://www.postgresql.org/docs/10/static/release-10-4.html)\.

This version also includes the following changes:
+ Support for PostgreSQL 10 Logical Replication using the native publication and subscription framework\. RDS PostgreSQL databases can function as both publishers and subscribers\. You can specify replication to other PostgreSQL databases at the database\-level or at the table\-level\. With logical replication, the publisher and subscriber databases need not be physically identical \(block\-to\-block\) to each other\. This allows for use cases such as data consolidation, data distribution, and data replication across different database versions for 10\.4 and above\. For more details, refer to [Logical Replication for PostgreSQL on Amazon RDS](#PostgreSQL.Concepts.General.FeatureSupport.LogicalReplication)\.
+ The temporary file size limitation is user\-configurable\. You require the **rds\_superuser** role to modify the `temp_file_limit` parameter\.
+ Update of the `GDAL` library, which is used by the PostGIS extension\. See [Working with PostGIS](Appendix.PostgreSQL.CommonDBATasks.md#Appendix.PostgreSQL.CommonDBATasks.PostGIS)\.
+ Update of the `ip4r` extension to version 2\.1\.1\.
+ Update of the `pg_repack` extension to version 1\.4\.3\. See [Working with the pg\_repack Extension](Appendix.PostgreSQL.CommonDBATasks.md#Appendix.PostgreSQL.CommonDBATasks.pg_repack)\.
+ Update of the `plv8` extension to version 2\.1\.2\.

For information on upgrading the engine version for your PostgreSQL DB instance, see [Upgrading a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.Patching)\. 

For the complete list of extensions supported by Amazon RDS for PostgreSQL, see [Supported PostgreSQL Features and Extensions](#PostgreSQL.Concepts.General.FeaturesExtensions)\.

**Note**  
The `tsearch2` extension is to be removed in the next major release\. We encourage customers still using pre\-8\.3 text search to migrate to the equivalent built\-in features\. For more information about migrating, see the [PostgreSQL documentation](https://www.postgresql.org/docs/9.6/static/textsearch-migration.html)\.

##### PostgreSQL Version 10\.3 on Amazon RDS<a name="PostgreSQL.Concepts.General.version103"></a>

PostgreSQL version 10\.3 contains several bug fixes for issues in release 10\. For more information on the fixes in 10\.3, see the [PostgreSQL documentation](http://www.postgresql.org/docs/10/static/release-10-3.html)\.

Version 2\.1\.0 of PL/v8 is now available\. If you use PL/v8 and upgrade PostgreSQL to a new PL/v8 version, you immediately take advantage of the new extension but the catalog metadata doesn't reflect this fact\. For the steps to synchronize your catalog metadata with the new version of PL/v8, see [Upgrading PL/v8](#PostgreSQL.Concepts.General.UpgradingPLv8)\.

For information on upgrading the engine version for your PostgreSQL DB instance, see [Upgrading a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.Patching)\. 

For the complete list of extensions supported by Amazon RDS for PostgreSQL, see [Supported PostgreSQL Features and Extensions](#PostgreSQL.Concepts.General.FeaturesExtensions)\.

##### PostgreSQL Version 10\.1 on Amazon RDS<a name="PostgreSQL.Concepts.General.version101"></a>

PostgreSQL version 10\.1 contains several bug fixes for issues in release 10\. For more information on the fixes in 10\.1, see the [PostgreSQL documentation](http://www.postgresql.org/docs/10/static/release-10-1.html) and the [PostgreSQL 10 community announcement](https://www.postgresql.org/about/news/1786/)\.

For information on upgrading the engine version for your PostgreSQL DB instance, see [Upgrading a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.Patching)\. 

PostgreSQL version 10\.1 includes the following changes: 
+ **Declarative table partitioning** – PostgreSQL 10 adds table partitioning to SQL syntax and native tuple routing\. 
+ **Parallel queries** – When you create a new PostgreSQL 10\.1 instance, parallel queries are enabled for the `default.postgres10` parameter group\. The parameter [max\_parallel\_workers\_per\_gather](https://www.postgresql.org/docs/10/static/runtime-config-resource.html#GUC-MAX-PARALLEL-WORKERS-PER-GATHER) is set to 2 by default, but you can modify it to support your specific workload requirements\.
+ **Support for the International Components for Unicode \(ICU\)** – You can use the ICU library to provide explicitly versioned collations\. Amazon RDS for PostgreSQL 10\.1 is compiled with ICU version 60\.2\. For more information about ICU implementation in PostgreSQL, see [Collation Support](https://www.postgresql.org/docs/10/static/collation.html)\.
+ **Huge pages** – Huge pages is a feature of the Linux kernel that uses multiple page size capabilities of modern hardware architectures\. Amazon RDS for PostgreSQL supports huge pages with a global configuration parameter\. When you create a new PostgreSQL 10\.1 instance with RDS, the `huge_pages` parameter is set to `"on"` for the` default.postgres10` parameter group\. You can modify this setting to support your specific workload requirements\. 
+ **PL/v8 update** – PL/v8 is a procedural language that you can use to write functions in JavaScript that you can then call from SQL\. This release of PostgreSQL supports version 2\.1\.0 of PL/v8\.
+ **Renaming of xlog and location** – In PostgreSQL version 10 the abbreviation "xlog" has changed to "wal", and the term "location" has changed to "lsn"\. For more information, see [ https://www\.postgresql\.org/docs/10/static/release\-10\.html\#id\-1\.11\.6\.8\.4](https://www.postgresql.org/docs/10/static/release-10.html#id-1.11.6.8.4)\. 
+ **tsearch2 module** – Amazon RDS continues to provide the `tsearch2` module in PostgreSQL version 10, but is to remove it in the next major version release\. If your application uses tsearch2 functions update it to use the equivalent functions the core engine provides\. For more information about using `tsearch2`, see [tsearch2 module](https://www.postgresql.org/docs/9.6/static/tsearch2.html)\.

For the complete list of extensions supported by Amazon RDS for PostgreSQL, see [Supported PostgreSQL Features and Extensions](#PostgreSQL.Concepts.General.FeaturesExtensions)\.

#### PostgreSQL 9\.6 Versions<a name="PostgreSQL.Concepts.General.version96"></a>

**Topics**
+ [PostgreSQL Version 9\.6\.18 on Amazon RDS](#PostgreSQL.Concepts.General.version9618)
+ [PostgreSQL Version 9\.6\.17 on Amazon RDS](#PostgreSQL.Concepts.General.version9617)
+ [PostgreSQL Version 9\.6\.16 on Amazon RDS](#PostgreSQL.Concepts.General.version9616)
+ [PostgreSQL Version 9\.6\.15 on Amazon RDS](#PostgreSQL.Concepts.General.version9615)
+ [PostgreSQL Version 9\.6\.14 on Amazon RDS](#PostgreSQL.Concepts.General.version9614)
+ [PostgreSQL Version 9\.6\.12 on Amazon RDS](#PostgreSQL.Concepts.General.version9612)
+ [PostgreSQL Version 9\.6\.11 on Amazon RDS](#PostgreSQL.Concepts.General.version9611)
+ [PostgreSQL Version 9\.6\.10 on Amazon RDS](#PostgreSQL.Concepts.General.version9610)
+ [PostgreSQL Version 9\.6\.9 on Amazon RDS](#PostgreSQL.Concepts.General.version969)
+ [PostgreSQL Version 9\.6\.8 on Amazon RDS](#PostgreSQL.Concepts.General.version968)
+ [PostgreSQL Version 9\.6\.6 on Amazon RDS](#PostgreSQL.Concepts.General.version966)
+ [PostgreSQL Version 9\.6\.5 on Amazon RDS](#PostgreSQL.Concepts.General.version965)
+ [PostgreSQL Version 9\.6\.3 on Amazon RDS](#PostgreSQL.Concepts.General.version963)
+ [PostgreSQL Version 9\.6\.2 on Amazon RDS](#PostgreSQL.Concepts.General.version962)
+ [PostgreSQL Version 9\.6\.1 on Amazon RDS](#PostgreSQL.Concepts.General.version961)

##### PostgreSQL Version 9\.6\.18 on Amazon RDS<a name="PostgreSQL.Concepts.General.version9618"></a>

PostgreSQL version 9\.6\.18 contains several bug fixes for issues in release 9\.6\.17\. For more information on the fixes in PostgreSQL 9\.6\.18, see the [PostgreSQL 9\.6\.18 documentation](https://www.postgresql.org/docs/9.6/release-9-6-18.html)\. 

This version also includes the following change:

1. Upgraded the `pg_hint_plan` extension to version 1\.2\.6\.

For information on extensions and modules, see [PostgreSQL Version 9\.6\.x Extensions and Modules Supported on Amazon RDS](#PostgreSQL.Concepts.General.FeatureSupport.Extensions.96x)\.

##### PostgreSQL Version 9\.6\.17 on Amazon RDS<a name="PostgreSQL.Concepts.General.version9617"></a>

PostgreSQL version 9\.6\.17 contains several bug fixes for issues in release 9\.6\.16\. For more information on the fixes in PostgreSQL 9\.6\.17, see the [PostgreSQL 9\.6\.17 documentation](https://www.postgresql.org/docs/9.6/release-9-6-17.html)\. 

##### PostgreSQL Version 9\.6\.16 on Amazon RDS<a name="PostgreSQL.Concepts.General.version9616"></a>

PostgreSQL version 9\.6\.16 contains several bug fixes for issues in release 9\.6\.15\. For more information on the fixes in PostgreSQL 9\.6\.16, see the [PostgreSQL documentation](https://www.postgresql.org/docs/9.6/release-9-6-16.html)\. 

##### PostgreSQL Version 9\.6\.15 on Amazon RDS<a name="PostgreSQL.Concepts.General.version9615"></a>

PostgreSQL version 9\.6\.15 contains several bug fixes for issues in release 9\.6\.14\. For more information on the fixes in PostgreSQL 9\.6\.15, see the [PostgreSQL documentation](https://www.postgresql.org/docs/9.6/release-9-6-15.html)\. 

The `PostGIS` extension is updated to version 2\.5\.2\.

##### PostgreSQL Version 9\.6\.14 on Amazon RDS<a name="PostgreSQL.Concepts.General.version9614"></a>

This release contains bug fixes and improvements done by the PostgreSQL community\. 

With this release, the `pg_hint_plan` extension has been updated to version 1\.2\.5\.

For more information on the fixes in PostgreSQL 9\.6\.14, see the [PostgreSQL documentation](https://www.postgresql.org/docs/9.6/release-9-6-14.html)\.

##### PostgreSQL Version 9\.6\.12 on Amazon RDS<a name="PostgreSQL.Concepts.General.version9612"></a>

PostgreSQL version 9\.6\.12 contains several bug fixes for issues in release 9\.6\.11\. For more information on the fixes in 9\.6\.12, see the [PostgreSQL documentation](http://www.postgresql.org/docs/9.6/release-9-6-12.html)\. 

For information on upgrading the engine version for your PostgreSQL DB instance, see [Upgrading a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.Patching)\. 

##### PostgreSQL Version 9\.6\.11 on Amazon RDS<a name="PostgreSQL.Concepts.General.version9611"></a>

PostgreSQL version 9\.6\.11 contains several bug fixes for issues in release 9\.6\.10\. For more information on the fixes in PostgreSQL 9\.6\.11, see the [PostgreSQL documentation](http://www.postgresql.org/docs/9.6/static/release-9-6-11.html)\. For information on upgrading the engine version for your PostgreSQL DB instance, see [Upgrading a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.Patching)\. 

With this version, the logical decoding plugin `wal2json` has been updated to commit `9e962ba`\. 

For the complete list of extensions supported by Amazon RDS for PostgreSQL, see [Supported PostgreSQL Features and Extensions](#PostgreSQL.Concepts.General.FeaturesExtensions)\.

##### PostgreSQL Version 9\.6\.10 on Amazon RDS<a name="PostgreSQL.Concepts.General.version9610"></a>

PostgreSQL version 9\.6\.10 contains several bug fixes for issues in release 9\.6\.9\. For more information on the fixes in 9\.6\.10, see the [PostgreSQL documentation](http://www.postgresql.org/docs/current/static/release-9-6-10.html)\. 

This version includes the following changes: 
+ Support for the `pglogical` extension version 2\.2\.0\. Prerequisites for using this extension are the same as the prerequisites for using logical replication for PostgreSQL as described in [Logical Replication for PostgreSQL on Amazon RDS](#PostgreSQL.Concepts.General.FeatureSupport.LogicalReplication)\. 
+ Support for the `pg_similarity` extension version 2\.2\.0\.
+ An update for the `wal2json` extension to version 01c5c1e\.
+ An update for the `pg_hint_plan` extension to version 1\.2\.3\.

For information on upgrading the engine version for your PostgreSQL DB instance, see [Upgrading a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.Patching)\. 

For the complete list of extensions supported by Amazon RDS for PostgreSQL, see [Supported PostgreSQL Features and Extensions](#PostgreSQL.Concepts.General.FeaturesExtensions)\.

##### PostgreSQL Version 9\.6\.9 on Amazon RDS<a name="PostgreSQL.Concepts.General.version969"></a>

PostgreSQL version 9\.6\.9 contains several bug fixes for issues in release 9\.6\.8\. For more information on the fixes in 9\.6\.9, see the [PostgreSQL documentation](http://www.postgresql.org/docs/9.6/static/release-9-6-9.html)\. For information on upgrading the engine version for your PostgreSQL DB instance, see [Upgrading a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.Patching)\. 

This version includes the following changes: 
+ The temporary file size limitation is user\-configurable\. You require the **rds\_superuser** role to modify the `temp_file_limit` parameter\.
+ Update of the `GDAL` library, which is used by the PostGIS extension\. See [Working with PostGIS](Appendix.PostgreSQL.CommonDBATasks.md#Appendix.PostgreSQL.CommonDBATasks.PostGIS)\.
+ Update of the `ip4r` extension to version 2\.1\.1\.
+ Update of the `pgaudit` extension to version 1\.1\.1\. See [Working with the pgaudit Extension](Appendix.PostgreSQL.CommonDBATasks.md#Appendix.PostgreSQL.CommonDBATasks.pgaudit)\.

  Update of the `pg_repack` extension to version 1\.4\.3\. See [Working with the pg\_repack Extension](Appendix.PostgreSQL.CommonDBATasks.md#Appendix.PostgreSQL.CommonDBATasks.pg_repack)\.
+ Update of the `plv8` extension to version 2\.1\.2\.

For the complete list of extensions supported by Amazon RDS for PostgreSQL, see [Supported PostgreSQL Features and Extensions](#PostgreSQL.Concepts.General.FeaturesExtensions)\.

##### PostgreSQL Version 9\.6\.8 on Amazon RDS<a name="PostgreSQL.Concepts.General.version968"></a>

PostgreSQL version 9\.6\.8 contains several bug fixes for issues in release 9\.6\.6\. For more information on the fixes in 9\.6\.8, see the [PostgreSQL documentation](http://www.postgresql.org/docs/9.6/static/release-9-6-8.html)\. For information on upgrading the engine version for your PostgreSQL DB instance, see [Upgrading a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.Patching)\. 

For the complete list of extensions supported by Amazon RDS for PostgreSQL, see [Supported PostgreSQL Features and Extensions](#PostgreSQL.Concepts.General.FeaturesExtensions)\.

##### PostgreSQL Version 9\.6\.6 on Amazon RDS<a name="PostgreSQL.Concepts.General.version966"></a>

PostgreSQL version 9\.6\.6 contains several bug fixes for issues in release 9\.6\.5\. For more information on the fixes in 9\.6\.6, see the [PostgreSQL documentation](http://www.postgresql.org/docs/9.6/static/release-9-6-6.html)\. For information on upgrading the engine version for your PostgreSQL DB instance, see [Upgrading a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.Patching)\. 

This version includes the following features: 
+ Supports the `orafce` extension, version 3\.6\.1\. This extension contains functions that are native to commercial databases, and can be helpful if you are porting a commercial database to PostgreSQL\. For more information about using `orafce` with Amazon RDS, see [Working with the orafce Extension](Appendix.PostgreSQL.CommonDBATasks.md#Appendix.PostgreSQL.CommonDBATasks.orafce)\.
+ Supports the `prefix` extension, version 1\.2\.6\. This extension provides an operator for text prefix searches\. For more information about `prefix`, see the [prefix project on GitHub](https://github.com/dimitri/prefix)\.
+ Supports version 2\.3\.4 of PostGIS, version 2\.4\.2 of pgrouting, and an updated version of wal2json\.

For the complete list of extensions supported by Amazon RDS for PostgreSQL, see [Supported PostgreSQL Features and Extensions](#PostgreSQL.Concepts.General.FeaturesExtensions)\.

##### PostgreSQL Version 9\.6\.5 on Amazon RDS<a name="PostgreSQL.Concepts.General.version965"></a>

PostgreSQL version 9\.6\.5 contains several bug fixes for issues in release 9\.6\.4\. For more information on the fixes in 9\.6\.5, see the [PostgreSQL documentation](http://www.postgresql.org/docs/9.6/static/release-9-6-5.html)\. For information on upgrading the engine version for your PostgreSQL DB instance, see [Upgrading a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.Patching)\. 

This version also includes support for the [pgrouting](http://pgrouting.org/) and [postgresql\-hll](https://github.com/citusdata/postgresql-hll/releases/tag/v2.10.2) extensions, and the [decoder\_raw](https://github.com/michaelpq/pg_plugins/tree/master/decoder_raw) optional module\.

For the complete list of extensions supported by Amazon RDS for PostgreSQL, see [Supported PostgreSQL Features and Extensions](#PostgreSQL.Concepts.General.FeaturesExtensions)\.

##### PostgreSQL Version 9\.6\.3 on Amazon RDS<a name="PostgreSQL.Concepts.General.version963"></a>

PostgreSQL version 9\.6\.3 contains several new features and bug fixes\. This version includes the following features: 
+ Supports the extension `pg_repack` version 1\.4\.0\. You can use this extension to remove bloat from tables and indexes\. For more information on using `pg_repack` with Amazon RDS, see [Working with the pg\_repack Extension](Appendix.PostgreSQL.CommonDBATasks.md#Appendix.PostgreSQL.CommonDBATasks.pg_repack)\.
+ Supports the extension `pgaudit` version 1\.1\.0\. This extension provides detailed session and object audit logging\. For more information on using pgaudit with Amazon RDS, see [Working with the pgaudit Extension](Appendix.PostgreSQL.CommonDBATasks.md#Appendix.PostgreSQL.CommonDBATasks.pgaudit)\.
+ Supports `wal2json`, an output plugin for logical decoding\.
+ Supports the `auto_explain` module\. You can use this module to log execution plans of slow statements automatically\. The following example shows how to use `auto_explain` from within an Amazon RDS PostgreSQL session:

  ```
  LOAD '$libdir/plugins/auto_explain';
  ```

  For more information on using `auto_explain`, see the [ PostgreSQL documentation](https://www.postgresql.org/docs/current/static/auto-explain.html)\. 

##### PostgreSQL Version 9\.6\.2 on Amazon RDS<a name="PostgreSQL.Concepts.General.version962"></a>

PostgreSQL version 9\.6\.2 contains several new features and bug fixes\. The new version also includes the following extension versions: 
+ PostGIS version 2\.3\.2
+ [ pg\_freespacemap](https://www.postgresql.org/docs/current/static/pgfreespacemap.html) version 1\.1–Provides a way to examine the free space map \(FSM\)\. This extension provides an overloaded function called pg\_freespace\. The functions show the value recorded in the free space map for a given page, or for all pages in the relation\.
+ [pg\_hint\_plan](http://pghintplan.osdn.jp/pg_hint_plan.html) version 1\.1\.3– Provides control of execution plans by using hinting phrases at the beginning of SQL statements\.
+ log\_fdw version 1\.0–Using this extension from Amazon RDS, you can load and query your database engine log from within the database\. For more information, see [Using the log\_fdw Extension](#CHAP_PostgreSQL.Extensions.log_fdw)\.
+ With this version release, you can now edit the `max_worker_processes` parameter in a DB parameter group\. 

PostgreSQL version 9\.6\.2 on Amazon RDS also supports altering enum values\. For more information, see [ALTER ENUM for PostgreSQL](#PostgreSQL.Concepts.General.FeatureSupport.AlterEnum)\.

 For more information on the fixes in 9\.6\.2, see the [PostgreSQL documentation](http://www.postgresql.org/docs/9.6/static/release-9-6-2.html)\. For information on upgrading the engine version for your PostgreSQL DB instance, see [Upgrading a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.Patching)\. 

##### PostgreSQL Version 9\.6\.1 on Amazon RDS<a name="PostgreSQL.Concepts.General.version961"></a>

PostgreSQL version 9\.6\.1 contains several new features and improvements\. For more information about the fixes and improvements in PostgreSQL 9\.6\.1, see the [PostgreSQL documentation](https://www.postgresql.org/docs/9.6/static/release-9-6-1.html)\. For information on upgrading the engine version for your PostgreSQL DB instance, see [Upgrading a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.Patching)\. For information about performing parallel queries and phrase searching using Amazon RDS for PostgreSQL 9\.6\.1, see the [AWS Database Blog](https://aws.amazon.com/blogs/database/performing-parallel-queries-and-phrase-searching-with-amazon-rds-for-postgresql-9-6-1/)\.

PostgreSQL version 9\.6\.1 includes the following changes:
+ **Parallel query processing**: Supports parallel processing of large read\-only queries, allowing sequential scans, hash joins, nested loops, and aggregates to be run in parallel\. By default, parallel query processing is not enabled\. To enable parallel query processing, set the parameter `max_parallel_workers_per_gather` to a value larger than zero\.
+ **Updated postgres\_fdw extension**: Supports remote JOINs, SORTs, UPDATEs, and DELETE operations\.
+ **PL/v8 update**: Provides version 1\.5\.3 of the PL/v8 language\.
+ **PostGIS version update**: Supports POSTGIS="2\.3\.0 r15146" GEOS="3\.5\.0\-CAPI\-1\.9\.0 r4084" PROJ="Rel\. 4\.9\.2, 08 September 2015" GDAL="GDAL 2\.1\.1, released 2016/07/07" LIBXML="2\.9\.1" LIBJSON="0\.12" RASTER
+ **Vacuum improvement**: Avoids scanning pages unnecessarily during vacuum freeze operations\.
+ **Full\-text search support for phrases**: Supports the ability to specify a phrase\-search query in tsquery input using the new operators <\-> and <N>\.
+ **Two new extensions are supported**:
  + `bloom`, an index access method based on [Bloom filters](http://en.wikipedia.org/wiki/Bloom_filter)
  + `pg_visibility`, which provides a means for examining the visibility map and page\-level visibility information of a table\.
+ With the release of version 9\.6\.2, you can now edit the `max_worker_processes` parameter in a PostgreSQL version 9\.6\.1 DB parameter group\.

You can create a new PostgreSQL 9\.6\.1 database instance using the AWS Management Console, AWS CLI, or RDS API\. You can also upgrade an existing PostgreSQL 9\.5 instance to version 9\.6\.1 using major version upgrade\. If you want to upgrade a DB instance from version 9\.4 to 9\.6, you must perform a point\-and\-click upgrade to the next major version first\. Each upgrade operation involves a short period of unavailability for your DB instance\.

#### PostgreSQL 9\.5 Versions<a name="PostgreSQL.Concepts.General.version95"></a>

**Topics**
+ [PostgreSQL Version 9\.5\.22 on Amazon RDS](#PostgreSQL.Concepts.General.version9522)
+ [PostgreSQL Version 9\.5\.21 on Amazon RDS](#PostgreSQL.Concepts.General.version9521)
+ [PostgreSQL Version 9\.5\.20 on Amazon RDS](#PostgreSQL.Concepts.General.version9520)
+ [PostgreSQL Version 9\.5\.19 on Amazon RDS](#PostgreSQL.Concepts.General.version9519)
+ [PostgreSQL Version 9\.5\.18 on Amazon RDS](#PostgreSQL.Concepts.General.version9518)
+ [PostgreSQL Version 9\.5\.16 on Amazon RDS](#PostgreSQL.Concepts.General.version9516)
+ [PostgreSQL Version 9\.5\.15 on Amazon RDS](#PostgreSQL.Concepts.General.version9515)
+ [PostgreSQL Version 9\.5\.14 on Amazon RDS](#PostgreSQL.Concepts.General.version9514)
+ [PostgreSQL Version 9\.5\.13 on Amazon RDS](#PostgreSQL.Concepts.General.version9513)
+ [PostgreSQL Version 9\.5\.12 on Amazon RDS](#PostgreSQL.Concepts.General.version9512)
+ [PostgreSQL Version 9\.5\.10 on Amazon RDS](#PostgreSQL.Concepts.General.version9510)
+ [PostgreSQL Version 9\.5\.9 on Amazon RDS](#PostgreSQL.Concepts.General.version959)
+ [PostgreSQL Version 9\.5\.7 on Amazon RDS](#PostgreSQL.Concepts.General.version957)
+ [PostgreSQL Version 9\.5\.6 on Amazon RDS](#PostgreSQL.Concepts.General.version956)
+ [PostgreSQL Version 9\.5\.4 on Amazon RDS](#PostgreSQL.Concepts.General.version954)
+ [PostgreSQL Version 9\.5\.2 on Amazon RDS](#PostgreSQL.Concepts.General.version952)

##### PostgreSQL Version 9\.5\.22 on Amazon RDS<a name="PostgreSQL.Concepts.General.version9522"></a>

PostgreSQL version 9\.5\.22 contains several bug fixes for issues in release 9\.5\.21\. For more information on the fixes in PostgreSQL 9\.5\.22, see the [PostgreSQL 9\.5\.22 documentation](https://www.postgresql.org/docs/9.5/release-9-5-22.html)\. 

This version also includes the following change:

1. Upgraded the `pg_hint_plan` extension to version 1\.1\.9\.

For information on extensions and modules, see [PostgreSQL Version 9\.5\.x Extensions Supported on Amazon RDS](#PostgreSQL.Concepts.General.FeatureSupport.Extensions.95x)\.

##### PostgreSQL Version 9\.5\.21 on Amazon RDS<a name="PostgreSQL.Concepts.General.version9521"></a>

PostgreSQL version 9\.5\.21 contains several bug fixes for issues in release 9\.5\.20\. For more information on the fixes in PostgreSQL 9\.5\.21, see the [PostgreSQL 9\.5\.21 documentation](https://www.postgresql.org/docs/9.5/release-9-5-21.html)\. 

##### PostgreSQL Version 9\.5\.20 on Amazon RDS<a name="PostgreSQL.Concepts.General.version9520"></a>

PostgreSQL version 9\.5\.20 contains several bug fixes for issues in release 9\.6\.19\. For more information on the fixes in PostgreSQL 9\.5\.20, see the [PostgreSQL documentation](https://www.postgresql.org/docs/9.5/release-9-5-20.html)\. 

##### PostgreSQL Version 9\.5\.19 on Amazon RDS<a name="PostgreSQL.Concepts.General.version9519"></a>

PostgreSQL version 9\.5\.19 contains several bug fixes for issues in release 9\.6\.18\. For more information on the fixes in PostgreSQL 9\.5\.19, see the [PostgreSQL documentation](https://www.postgresql.org/docs/9.5/release-9-5-19.html)\. 

The `PostGIS` extension is updated to version 2\.5\.2\.

##### PostgreSQL Version 9\.5\.18 on Amazon RDS<a name="PostgreSQL.Concepts.General.version9518"></a>

This release contains bug fixes and improvements done by the PostgreSQL community\. 

With this release, the `pg_hint_plan` extension has been updated to version 1\.1\.8\.

For more information on the fixes in PostgreSQL 9\.5\.18, see the [PostgreSQL documentation](https://www.postgresql.org/docs/9.5/release-9-5-18.html)\.

##### PostgreSQL Version 9\.5\.16 on Amazon RDS<a name="PostgreSQL.Concepts.General.version9516"></a>

PostgreSQL version 9\.5\.16 contains several bug fixes for issues in release 9\.5\.15\. For more information on the fixes in 9\.5\.16, see the [PostgreSQL documentation](http://www.postgresql.org/docs/current/release-9-5-16.html)\. For information on upgrading the engine version for your PostgreSQL DB instance, see [Upgrading a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.Patching)\. 

For the complete list of extensions supported by Amazon RDS for PostgreSQL, see [Supported PostgreSQL Features and Extensions](#PostgreSQL.Concepts.General.FeaturesExtensions)\.

##### PostgreSQL Version 9\.5\.15 on Amazon RDS<a name="PostgreSQL.Concepts.General.version9515"></a>

PostgreSQL version 9\.5\.15 contains several bug fixes for issues in release 9\.5\.14\. For more information on the fixes in 9\.5\.15, see the [PostgreSQL documentation](http://www.postgresql.org/docs/current/static/release-9-5-15.html)\. For information on upgrading the engine version for your PostgreSQL DB instance, see [Upgrading a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.Patching)\. 

For the complete list of extensions supported by Amazon RDS for PostgreSQL, see [Supported PostgreSQL Features and Extensions](#PostgreSQL.Concepts.General.FeaturesExtensions)\.

##### PostgreSQL Version 9\.5\.14 on Amazon RDS<a name="PostgreSQL.Concepts.General.version9514"></a>

PostgreSQL version 9\.5\.14 contains several bug fixes for issues in release 9\.5\.13\. For more information on the fixes in 9\.5\.14, see the [PostgreSQL documentation](http://www.postgresql.org/docs/current/static/release-9-5-14.html)\. 

For information on upgrading the engine version for your PostgreSQL DB instance, see [Upgrading a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.Patching)\. 

For the complete list of extensions supported by Amazon RDS for PostgreSQL, see [Supported PostgreSQL Features and Extensions](#PostgreSQL.Concepts.General.FeaturesExtensions)\.

##### PostgreSQL Version 9\.5\.13 on Amazon RDS<a name="PostgreSQL.Concepts.General.version9513"></a>

PostgreSQL version 9\.5\.13 contains several bug fixes for issues in release 9\.5\.12\. For more information on the fixes in 9\.5\.13, see the [PostgreSQL documentation](http://www.postgresql.org/docs/9.5/static/release-9-5-13.html)\. For information on upgrading the engine version for your PostgreSQL DB instance, see [Upgrading a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.Patching)\. 

This version includes the following extension updates: 
+ Update of the `pgaudit` extension to version 1\.0\.6\. See [Working with the pgaudit Extension](Appendix.PostgreSQL.CommonDBATasks.md#Appendix.PostgreSQL.CommonDBATasks.pgaudit)\.
+ Update of the `pg_hint_plan` extension to version 1\.1\.5\. 
+ Update of the `plv8` extension to version 2\.1\.2\.

For the complete list of extensions supported by Amazon RDS for PostgreSQL, see [Supported PostgreSQL Features and Extensions](#PostgreSQL.Concepts.General.FeaturesExtensions)\.

##### PostgreSQL Version 9\.5\.12 on Amazon RDS<a name="PostgreSQL.Concepts.General.version9512"></a>

PostgreSQL version 9\.5\.12 contains several bug fixes for issues in release 9\.5\.10 For more information on the fixes in 9\.5\.12, see the [PostgreSQL documentation](http://www.postgresql.org/docs/9.5/static/release-9-5-12.html)\. For information on upgrading the engine version for your PostgreSQL DB instance, see [Upgrading a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.Patching)\. 

For the complete list of extensions supported by Amazon RDS for PostgreSQL, see [Supported PostgreSQL Features and Extensions](#PostgreSQL.Concepts.General.FeaturesExtensions)\.

##### PostgreSQL Version 9\.5\.10 on Amazon RDS<a name="PostgreSQL.Concepts.General.version9510"></a>

PostgreSQL version 9\.5\.10 contains several bug fixes for issues in version 9\.5\.9\. For more information on the fixes in 9\.5\.10, see the [PostgreSQL documentation](http://www.postgresql.org/docs/9.5/static/release-9-5-10.html)\. For information on upgrading the engine version for your PostgreSQL DB instance, see [Upgrading a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.Patching)\. 

##### PostgreSQL Version 9\.5\.9 on Amazon RDS<a name="PostgreSQL.Concepts.General.version959"></a>

PostgreSQL version 9\.5\.9 contains several bug fixes for issues in version 9\.5\.8\. For more information on the fixes in 9\.5\.9, see the [PostgreSQL documentation](http://www.postgresql.org/docs/9.5/static/release-9-5-9.html)\. For information on upgrading the engine version for your PostgreSQL DB instance, see [Upgrading a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.Patching)\. 

##### PostgreSQL Version 9\.5\.7 on Amazon RDS<a name="PostgreSQL.Concepts.General.version957"></a>

PostgreSQL version 9\.5\.7 contains several new features and bug fixes\. This version includes the following features: 
+ Supports the extension `pgaudit` version 1\.0\.5\. This extension provides detailed session and object audit logging\. For more information on using `pgaudit` with Amazon RDS, see [Working with the pgaudit Extension](Appendix.PostgreSQL.CommonDBATasks.md#Appendix.PostgreSQL.CommonDBATasks.pgaudit)\.
+ Supports `wal2json`, an output plugin for logical decoding\.
+ Supports the `auto_explain` module\. You can use this module to log execution plans of slow statements automatically\. The following example shows how to use `auto_explain` from within an Amazon RDS PostgreSQL session\.

  ```
  LOAD '$libdir/plugins/auto_explain';
  ```

  For more information on using `auto_explain`, see the [ PostgreSQL documentation](https://www.postgresql.org/docs/current/static/auto-explain.html)\. 

##### PostgreSQL Version 9\.5\.6 on Amazon RDS<a name="PostgreSQL.Concepts.General.version956"></a>

PostgreSQL version 9\.5\.6 contains several new features and bug fixes\. The new version also includes the following extension versions: 
+ PostGIS version 2\.2\.5
+ [ pg\_freespacemap](https://www.postgresql.org/docs/current/static/pgfreespacemap.html) version 1\.1–Provides a way to examine the free space map \(FSM\)\. This extension provides an overloaded function called pg\_freespace\. This function shows the value recorded in the free space map for a given page, or for all pages in the relation\.
+ [pg\_hint\_plan](http://pghintplan.osdn.jp/pg_hint_plan.html) version 1\.1\.3– Provides control of execution plans by using hinting phrases at the beginning of SQL statements\.

PostgreSQL version 9\.5\.6 on Amazon RDS also supports altering enum values\. For more information, see [ALTER ENUM for PostgreSQL](#PostgreSQL.Concepts.General.FeatureSupport.AlterEnum)\.

 For more information on the fixes in 9\.5\.6, see the [PostgreSQL documentation](http://www.postgresql.org/docs/9.5/static/release-9-5-6.html)\. For information on upgrading the engine version for your PostgreSQL DB instance, see [Upgrading a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.Patching)\. 

##### PostgreSQL Version 9\.5\.4 on Amazon RDS<a name="PostgreSQL.Concepts.General.version954"></a>

PostgreSQL version 9\.5\.4 contains several fixes to issue found in previous versions\. For more information on the fixes in 9\.5\.4, see the [PostgreSQL documentation](http://www.postgresql.org/docs/9.5/static/release-9-5-4.html)\. For information on upgrading the engine version for your PostgreSQL DB instance, see [Upgrading a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.Patching)\. 

PostgreSQL supports the streaming of WAL changes using logical replication decoding\. Amazon RDS supports logical replication for PostgreSQL version 9\.5\.4 and higher\. For more information about PostgreSQL logical replication on Amazon RDS, see [Logical Replication for PostgreSQL on Amazon RDS](#PostgreSQL.Concepts.General.FeatureSupport.LogicalReplication)\. 

Beginning with PostgreSQL version 9\.5\.4 for Amazon RDS, the command ALTER USER WITH BYPASSRLS is supported\. 

PostgreSQL versions 9\.5\.4 and later support event triggers, and Amazon RDS supports event triggers for these versions\. You can use the master user account can be used to create, modify, rename, and delete event triggers\. Event triggers are at the DB instance level, so they can apply to all databases on an instance\. For more information about PostgreSQL event triggers on Amazon RDS, see [Event Triggers for PostgreSQL on Amazon RDS](#PostgreSQL.Concepts.General.FeatureSupport.EventTriggers)\.

##### PostgreSQL Version 9\.5\.2 on Amazon RDS<a name="PostgreSQL.Concepts.General.version952"></a>

PostgreSQL version 9\.5\.2 contains several fixes to issues found in previous versions\. For more information on the features in 9\.5\.2, see the [PostgreSQL documentation](http://www.postgresql.org/docs/9.5/static/release-9-5.html)\. For information on upgrading the engine version for your PostgreSQL DB instance, see [Upgrading a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.Patching)\.

PostgreSQL version 9\.5\.2 doesn't support the db\.m1 or db\.m2 DB instance classes\. If you need to upgrade a DB instance running PostgreSQL version 9\.4 to version 9\.5\.2 to one of these instance classes, you need to scale compute\. To do that, you need a comparable db\.t2 or db\.m3 DB instance class before you can upgrade a DB instance running PostgreSQL version 9\.4 to version 9\.5\.2\. For more information on DB instance classes, see [DB Instance Classes](Concepts.DBInstanceClass.md)\.

Native PostgreSQL version 9\.5\.2 introduced the command ALTER USER WITH BYPASSRLS\. 

This release includes updates from previous versions, including the following:
+ **CVE\-2016\-2193:** Fixes an issue where a query plan might be reused for more than one ROLE in the same session\. Reusing a query plan can cause the query to use the wrong set of Row Level Security \(RLS\) policies\.
+ **CVE\-2016\-3065:** Fixes a server crash bug triggered by using `pageinspect` with BRIN index pages\. Because an attacker might be able to expose a few bytes of server memory, this crash is being treated as a security issue\.

Major enhancements in RDS PostgreSQL 9\.5 include the following:
+ UPSERT: Allow INSERTs that would generate constraint conflicts to be turned into UPDATEs or ignored
+ Add the GROUP BY analysis features GROUPING SETS, CUBE, and ROLLUP
+ Add row\-level security control
+ Create mechanisms for tracking the progress of replication, including methods for identifying the origin of individual changes during logical replication
+ Add Block Range Indexes \(BRIN\)
+ Add substantial performance improvements for sorting
+ Add substantial performance improvements for multi\-CPU machines
+ PostGIS 2\.2\.2 \- To use this latest version of PostGIS, use the ALTER EXTENSION UPDATE statement to update after you upgrade to version 9\.5\.2\. Example: 

  ALTER EXTENSION POSTGIS UPDATE TO '2\.2\.2' 
+ Improved visibility of autovacuum sessions by allowing the rds\_superuser account to view autovacuum sessions in pg\_stat\_activity\. For example, you can identify and terminate an autovacuum session that is blocking a command from running, or running slower than a manually issued vacuum command\.

RDS PostgreSQL version 9\.5\.2 includes the following new extensions:
+ **address\_standardizer** – A single\-line address parser that takes an input address and normalizes it based on a set of rules stored in a table, helper lex, and gaz tables\.
+ [hstore\_plperl](http://www.postgresql.org/docs/current/static/hstore.html) – Provides transforms for the `hstore` type for PL/Perl\. 
+ [tsm\_system\_rows](http://www.postgresql.org/docs/current/static/tsm-system-rows.html) – Provides the table sampling method `SYSTEM_ROWS`, which can be used in the `TABLESAMPLE` clause of a `SELECT` command\.
+ [tsm\_system\_time](http://www.postgresql.org/docs/current/static/tsm-system-time.html) – Provides the table sampling method `SYSTEM_TIME`, which can be used in the `TABLESAMPLE` clause of a `SELECT` command\. 

### Supported PostgreSQL Features and Extensions<a name="PostgreSQL.Concepts.General.FeaturesExtensions"></a>

Amazon RDS supports many of the most common PostgreSQL extensions and features\.

**Topics**
+ [PostgreSQL Extensions and Modules Supported on Amazon RDS](#PostgreSQL.Concepts.General.FeatureSupport.Extensions)
+ [Upgrading PL/v8](#PostgreSQL.Concepts.General.UpgradingPLv8)
+ [Supported PostgreSQL Features](#PostgreSQL.Concepts.General.FeatureSupport)
+ [Limits for PostgreSQL DB Instances](#PostgreSQL.Concepts.General.Limits)
+ [Upgrading a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.Patching)
+ [Using SSL with a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.SSL)

#### PostgreSQL Extensions and Modules Supported on Amazon RDS<a name="PostgreSQL.Concepts.General.FeatureSupport.Extensions"></a>

PostgreSQL supports many PostgreSQL extensions and modules\. Extensions and modules expand on the functionality provided by the PostgreSQL engine\. The following sections show the extensions and modules supported by Amazon RDS for the major PostgreSQL versions\.

**Topics**
+ [PostgreSQL Version 13 Extensions and Modules Supported on Amazon RDS](#PostgreSQL.Concepts.General.FeatureSupport.Extensions.13x)
+ [PostgreSQL Version 12 Extensions and Modules Supported on Amazon RDS](#PostgreSQL.Concepts.General.FeatureSupport.Extensions.12x)
+ [PostgreSQL Version 11\.x Extensions and Modules Supported on Amazon RDS](#PostgreSQL.Concepts.General.FeatureSupport.Extensions.11x)
+ [PostgreSQL Version 10\.x Extensions and Modules Supported on Amazon RDS](#PostgreSQL.Concepts.General.FeatureSupport.Extensions.101x)
+ [PostgreSQL Version 9\.6\.x Extensions and Modules Supported on Amazon RDS](#PostgreSQL.Concepts.General.FeatureSupport.Extensions.96x)
+ [PostgreSQL Version 9\.5\.x Extensions Supported on Amazon RDS](#PostgreSQL.Concepts.General.FeatureSupport.Extensions.95x)
+ [PostgreSQL Extension Support for PostGIS on Amazon RDS](#CHAP_PostgreSQL.Extensions.SupportedPerVersion)
+ [Using the log\_fdw Extension](#CHAP_PostgreSQL.Extensions.log_fdw)

You can find a list of extensions supported by Amazon RDS in the default DB parameter group for that PostgreSQL version\. You can also see the current extensions list using `psql` by showing the `rds.extensions` parameter as in the following example\.

```
SHOW rds.extensions; 
```

**Note**  
Parameters added in a minor version release might display inaccurately when using the `rds.extensions` parameter in `psql`\. 

##### PostgreSQL Version 13 Extensions and Modules Supported on Amazon RDS<a name="PostgreSQL.Concepts.General.FeatureSupport.Extensions.13x"></a>

The following table shows PostgreSQL extensions and modules for PostgreSQL version 13 that are currently supported on Amazon RDS\. For more information on PostgreSQL extensions, see [Packaging Related Objects into an Extension](https://www.postgresql.org/docs/13/extend-extensions.html)\. 


| Extensions and Modules | Version 13 Beta 3 | 
| --- | --- | 
| [ address\_standardizer](http://postgis.net/docs/Address_Standardizer.html) | 3\.0\.2 | 
| [ address\_standardizer\_data\_us](http://postgis.net/docs/Address_Standardizer.html) | 3\.0\.2 | 
| [amcheck](https://www.postgresql.org/docs/current/amcheck.html) module | 1\.2 | 
| aws\_commons —see [ Importing Data into PostgreSQL on Amazon RDS ](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/PostgreSQL.Procedural.Importing.html#USER_PostgreSQL.S3Import) | 1\.0 | 
| aws\_s3 —see [ Importing Data into PostgreSQL on Amazon RDS ](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/PostgreSQL.Procedural.Importing.html#USER_PostgreSQL.S3Import)  | 1\.0 | 
| [ bloom](https://www.postgresql.org/docs/current/bloom.html) | 1\.0 | 
| [btree\_gin](http://www.postgresql.org/docs/current/btree-gin.html) | 1\.3 | 
| [btree\_gist](http://www.postgresql.org/docs/current/btree-gist.html) | 1\.5 | 
| [citext ](http://www.postgresql.org/docs/current/citext.html) | 1\.6 | 
| [cube ](http://www.postgresql.org/docs/current/cube.html) | 1\.4 | 
| [ dblink](http://www.postgresql.org/docs/current/dblink.html) | 1\.2 | 
| [ dict\_int ](http://www.postgresql.org/docs/current/dict-int.html) | 1\.0 | 
| [ dict\_xsyn](https://www.postgresql.org/docs/current/dict-xsyn.html) | 1\.0 | 
| [earthdistance](http://www.postgresql.org/docs/current/earthdistance.html) | 1\.1 | 
| [fuzzystrmatch](http://www.postgresql.org/docs/current/fuzzystrmatch.html) | 1\.1 | 
| [hstore](http://www.postgresql.org/docs/current/hstore.html) | 1\.7 | 
| [ hstore\_plperl](https://www.postgresql.org/docs/current/hstore.html) | 1\.0 | 
| [ intagg](http://www.postgresql.org/docs/current/intagg.html) | 1\.1 | 
| [ intarray](http://www.postgresql.org/docs/current/intarray.html) | 1\.3 | 
| [ip4r](https://github.com/RhodiumToad/ip4r) | 2\.4 | 
| [isn ](http://www.postgresql.org/docs/current/isn.html) | 1\.2 | 
| jsonb\_plperl | 1\.0 | 
| log\_fdw—see [Using the log\_fdw Extension](#CHAP_PostgreSQL.Extensions.log_fdw) | 1\.2 | 
| [ltree ](http://www.postgresql.org/docs/current/ltree.html) | 1\.2 | 
| [pageinspect](https://www.postgresql.org/docs/current/pageinspect.html) | 1\.8 | 
| [ pg\_buffercache](http://www.postgresql.org/docs/current/pgbuffercache.html) | 1\.3 | 
| [pg\_freespacemap](https://www.postgresql.org/docs/current/pgfreespacemap.html) | 1\.2 | 
| [ pg\_prewarm](https://www.postgresql.org/docs/current/pgprewarm.html) | 1\.2 | 
| [pg\_similarity](https://github.com/eulerto/pg_similarity) | 1\.0 | 
| [pg\_stat\_statements](http://www.postgresql.org/docs/current/pgstatstatements.html) | 1\.8 | 
| pg\_transport — see [Transporting PostgreSQL Databases Between DB Instances ](PostgreSQL.Procedural.Importing.md#PostgreSQL.TransportableDB)  | 1\.0 | 
| [pg\_trgm](http://www.postgresql.org/docs/current/pgtrgm.html) | 1\.5 | 
| [pg\_visibility](https://www.postgresql.org/docs/current/pgvisibility.html) | 1\.2 | 
| [pgaudit](https://github.com/pgaudit/pgaudit/blob/master/README.md) | 1\.5 | 
| [pgcrypto](http://www.postgresql.org/docs/current/pgcrypto.html) | 1\.3 | 
| [pgrouting](http://docs.pgrouting.org/latest/en/index.html) | 3\.0\.0 | 
| [pgrowlocks](http://www.postgresql.org/docs/current/pgrowlocks.html) | 1\.2 | 
| [pgstattuple](http://www.postgresql.org/docs/current/pgstattuple.html) | 1\.5 | 
| [pgTAP](https://pgtap.org/)  | 1\.1\.0 | 
| plcoffee | 2\.3\.15 | 
| plls | 2\.3\.15 | 
| [plperl](https://www.postgresql.org/docs/current/plperl.html) | 1\.0 | 
| [plpgsql](https://www.postgresql.org/docs/current/plpgsql.html) | 1\.0 | 
| plprofiler | 4\.1 | 
| [pltcl](https://www.postgresql.org/docs/current/pltcl-overview.html) | 1\.0 | 
| [plv8](https://github.com/plv8) | 2\.3\.15 | 
| [PostGIS](http://www.postgis.net/) | 3\.0\.2 | 
| postgis\_raster | 3\.0\.2 | 
| [postgis\_tiger\_geocoder](http://postgis.net/docs/Geocode.html) | 3\.0\.2 | 
| [postgis\_topology](http://postgis.net/docs/manual-dev/Topology.html) | 3\.0\.2 | 
| [postgres\_fdw](http://www.postgresql.org/docs/current/postgres-fdw.html) | 1\.0 | 
| [prefix](https://github.com/dimitri/prefix) | 1\.2\.0 | 
| rdkit | 3\.8 | 
| [sslinfo](http://www.postgresql.org/docs/current/sslinfo.html) | 1\.2 | 
| [tablefunc](http://www.postgresql.org/docs/current/tablefunc.html) | 1\.0 | 
| [test\_parser](https://www.postgresql.org/docs/9.4/test-parser.html) | 1\.0 | 
| [tsm\_system\_rows](https://www.postgresql.org/docs/current/tsm-system-rows.html) | 1\.0 | 
| [tsm\_system\_time](https://www.postgresql.org/docs/current/tsm-system-time.html) | 1\.0 | 
| [unaccent](http://www.postgresql.org/docs/current/unaccent.html) | 1\.1 | 
| [uuid\-ossp](http://www.postgresql.org/docs/current/uuid-ossp.html) | 1\.1 | 

##### PostgreSQL Version 12 Extensions and Modules Supported on Amazon RDS<a name="PostgreSQL.Concepts.General.FeatureSupport.Extensions.12x"></a>

The following table shows PostgreSQL extensions and modules for PostgreSQL version 12 that are currently supported on Amazon RDS\. For more information on PostgreSQL extensions, see [Packaging Related Objects into an Extension](https://www.postgresql.org/docs/12/extend-extensions.html)\. 


| Extensions and Modules | Version 12\.2 | Version 12\.3 | 
| --- | --- | --- | 
| [ address\_standardizer](http://postgis.net/docs/Address_Standardizer.html) | 3\.0\.0 | 3\.0\.0 | 
| [ address\_standardizer\_data\_us](http://postgis.net/docs/Address_Standardizer.html) | 3\.0\.0 | 3\.0\.0 | 
| [amcheck](https://www.postgresql.org/docs/current/amcheck.html) module | 1\.2 | 1\.2 | 
| aws\_commons —see [ Importing Data into PostgreSQL on Amazon RDS ](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/PostgreSQL.Procedural.Importing.html#USER_PostgreSQL.S3Import) | 1\.0 | 1\.0 | 
| aws\_s3 —see [ Importing Data into PostgreSQL on Amazon RDS ](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/PostgreSQL.Procedural.Importing.html#USER_PostgreSQL.S3Import)  | 1\.0 | 1\.0 | 
| [ bloom](https://www.postgresql.org/docs/12/bloom.html) | 1\.0 | 1\.0 | 
| [btree\_gin](http://www.postgresql.org/docs/12/btree-gin.html) | 1\.3 | 1\.3 | 
| [btree\_gist](http://www.postgresql.org/docs/12/btree-gist.html) | 1\.5 | 1\.5 | 
| [citext ](http://www.postgresql.org/docs/12/citext.html) | 1\.6 | 1\.6 | 
| [cube ](http://www.postgresql.org/docs/12/cube.html) | 1\.4 | 1\.4 | 
| [ dblink](http://www.postgresql.org/docs/12/dblink.html) | 1\.2 | 1\.2 | 
| [ dict\_int ](http://www.postgresql.org/docs/12/dict-int.html) | 1\.0 | 1\.0 | 
| [ dict\_xsyn](https://www.postgresql.org/docs/12/dict-xsyn.html) | 1\.0 | 1\.0 | 
| [earthdistance](http://www.postgresql.org/docs/12/earthdistance.html) | 1\.1 | 1\.1 | 
| [fuzzystrmatch](http://www.postgresql.org/docs/12/fuzzystrmatch.html) | 1\.1 | 1\.1 | 
| [hll](https://github.com/citusdata/postgresql-hll) | 2\.14 | 2\.14 | 
| [hstore](http://www.postgresql.org/docs/12/hstore.html) | 1\.6 | 1\.6 | 
| [ hstore\_plperl](https://www.postgresql.org/docs/12/hstore.html) | 1\.0 | 1\.0 | 
| [ intagg](http://www.postgresql.org/docs/12/intagg.html) | 1\.1 | 1\.1 | 
| [ intarray](http://www.postgresql.org/docs/12/intarray.html) | 1\.2 | 1\.2 | 
| [ip4r](https://github.com/RhodiumToad/ip4r) | 2\.4 | 2\.4 | 
| [isn ](http://www.postgresql.org/docs/12/isn.html) | 1\.2 | 1\.2 | 
| jsonb\_plperl | 1\.0 | 1\.0 | 
| log\_fdw—see [Using the log\_fdw Extension](#CHAP_PostgreSQL.Extensions.log_fdw) | 1\.1 | 1\.1 | 
| [ltree ](http://www.postgresql.org/docs/12/ltree.html) | 1\.1 | 1\.1 | 
| [orafce](https://github.com/orafce/orafce) | 3\.8 | 3\.8 | 
| [pageinspect](https://www.postgresql.org/docs/current/pageinspect.html) | 1\.7 | 1\.7 | 
| [ pg\_buffercache](http://www.postgresql.org/docs/12/pgbuffercache.html) | 1\.3 | 1\.3 | 
| [pg\_freespacemap](https://www.postgresql.org/docs/12/pgfreespacemap.html) | 1\.2 | 1\.2 | 
| [pg\_hint\_plan](http://pghintplan.osdn.jp/pg_hint_plan.html) | 1\.3\.4 | 1\.3\.5 | 
| [ pg\_prewarm](https://www.postgresql.org/docs/12/pgprewarm.html) | 1\.2 | 1\.2 | 
| [ pg\_repack ](http://reorg.github.io/pg_repack/) | 1\.4\.5 | 1\.4\.5 | 
| [pg\_similarity](https://github.com/eulerto/pg_similarity) | 1\.0 | 1\.0 | 
| [pg\_stat\_statements](http://www.postgresql.org/docs/12/pgstatstatements.html) | 1\.7 | 1\.7 | 
| pg\_transport — see [Transporting PostgreSQL Databases Between DB Instances ](PostgreSQL.Procedural.Importing.md#PostgreSQL.TransportableDB)  | 1\.0 | 1\.0 | 
| [pg\_trgm](http://www.postgresql.org/docs/12/pgtrgm.html) | 1\.4 | 1\.4 | 
| [pg\_visibility](https://www.postgresql.org/docs/12/pgvisibility.html) | 1\.2 | 1\.2 | 
| [pgaudit](https://github.com/pgaudit/pgaudit/blob/master/README.md) | 1\.4 | 1\.4 | 
| [pgcrypto](http://www.postgresql.org/docs/12/pgcrypto.html) | 1\.3 | 1\.3 | 
| [pglogical](https://github.com/2ndQuadrant/pglogical) | 2\.3\.0 | 2\.3\.1 | 
| [pgrouting](http://docs.pgrouting.org/2.3/en/doc/index.html) | 3\.0\.0 | 3\.0\.0 | 
| [pgrowlocks](http://www.postgresql.org/docs/12/pgrowlocks.html) | 1\.2 | 1\.2 | 
| [pgstattuple](http://www.postgresql.org/docs/12/pgstattuple.html) | 1\.5 | 1\.5 | 
| [pgTAP](https://pgtap.org/)  | 1\.1\.0 | 1\.1\.0 | 
| plcoffee | 2\.3\.14 | 2\.3\.14 | 
| plls | 2\.3\.14 | 2\.3\.14 | 
| [plperl](https://www.postgresql.org/docs/12/plperl.html) | 1\.0 | 1\.0 | 
| [plpgsql](https://www.postgresql.org/docs/12/plpgsql.html) | 1\.0 | 1\.0 | 
| plprofiler | 4\.1 | 4\.1 | 
| [pltcl](https://www.postgresql.org/docs/12/pltcl-overview.html) | 1\.0 | 1\.0 | 
| [plv8](https://github.com/plv8) | 2\.3\.14 | 2\.3\.14 | 
| [PostGIS](http://www.postgis.net/) | 3\.0\.0 | 3\.0\.0 | 
| postgis\_raster | 3\.0\.0 | 3\.0\.0 | 
| [postgis\_tiger\_geocoder](http://postgis.net/docs/Geocode.html) | 3\.0\.0 | 3\.0\.0 | 
| [postgis\_topology](http://postgis.net/docs/manual-dev/Topology.html) | 3\.0\.0 | 3\.0\.0 | 
| [postgres\_fdw](http://www.postgresql.org/docs/12/postgres-fdw.html) | 1\.0 | 1\.0 | 
| [prefix](https://github.com/dimitri/prefix) | 1\.2\.0 | 1\.2\.0 | 
| [sslinfo](http://www.postgresql.org/docs/12/sslinfo.html) | 1\.2 | 1\.2 | 
| [tablefunc](http://www.postgresql.org/docs/12/tablefunc.html) | 1\.0 | 1\.0 | 
| [test\_parser](https://www.postgresql.org/docs/9.4/static/test-parser.html) | 1\.0 | 1\.0 | 
| [tsm\_system\_rows](https://www.postgresql.org/docs/12/tsm-system-rows.html) | 1\.0 | 1\.0 | 
| [tsm\_system\_time](https://www.postgresql.org/docs/12/tsm-system-time.html) | 1\.0 | 1\.0 | 
| [unaccent](http://www.postgresql.org/docs/12/unaccent.html) | 1\.1 | 1\.1 | 
| [uuid\-ossp](http://www.postgresql.org/docs/12/uuid-ossp.html) | 1\.1 | 1\.1 | 
| wal2json module | 2\.1 | 2\.1 | 

##### PostgreSQL Version 11\.x Extensions and Modules Supported on Amazon RDS<a name="PostgreSQL.Concepts.General.FeatureSupport.Extensions.11x"></a>

The following tables show PostgreSQL extensions and modules for PostgreSQL version 11\.x that are currently supported by PostgreSQL on Amazon RDS\. "N/A" indicates that the extension or module is not available for that PostgreSQL version\. For more information on PostgreSQL extensions, see [Packaging Related Objects into an Extension](https://www.postgresql.org/docs/11/extend-extensions.html)\. 


| Extension | Version 11\.1 | Version 11\.2 | Version 11\.4 | Version 11\.5 | Version 11\.6 | Version 11\.7 | Version 11\.8 | 
| --- | --- | --- | --- | --- | --- | --- | --- | 
| [ address\_standardizer](http://postgis.net/docs/Address_Standardizer.html) | 2\.5\.1 | 2\.5\.1 | 2\.5\.1 | 2\.5\.1 | 2\.5\.1 | 2\.5\.1 | 2\.5\.1 | 
| [ address\_standardizer\_data\_us](http://postgis.net/docs/Address_Standardizer.html) | 2\.5\.1 | 2\.5\.1 | 2\.5\.1 | 2\.5\.1 | 2\.5\.1 | 2\.5\.1 | 2\.5\.1 | 
| [ bloom](https://www.postgresql.org/docs/11/static/bloom.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [btree\_gin](http://www.postgresql.org/docs/11/static/btree-gin.html) | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 
| [btree\_gist](http://www.postgresql.org/docs/11/static/btree-gist.html) | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 
| [citext ](http://www.postgresql.org/docs/11/static/citext.html) | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 
| [cube ](http://www.postgresql.org/docs/11/static/cube.html) | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 
| [ dblink](http://www.postgresql.org/docs/11/static/dblink.html) | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 
| [ dict\_int ](http://www.postgresql.org/docs/11/static/dict-int.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ dict\_xsyn](https://www.postgresql.org/docs/11/static/dict-xsyn.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [earthdistance](http://www.postgresql.org/docs/11/static/earthdistance.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [fuzzystrmatch](http://www.postgresql.org/docs/11/static/fuzzystrmatch.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [hstore](http://www.postgresql.org/docs/11/static/hstore.html) | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 
| [ hstore\_plperl](https://www.postgresql.org/docs/11/static/hstore.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ intagg](http://www.postgresql.org/docs/11/static/intagg.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [ intarray](http://www.postgresql.org/docs/11/static/intarray.html) | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 
| [ip4r](https://github.com/RhodiumToad/ip4r) | 2\.3 | 2\.3 | 2\.3 | 2\.3 | 2\.3 | 2\.3 | 2\.3 | 
| [isn ](http://www.postgresql.org/docs/11/static/isn.html) | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 
| log\_fdw—see [Using the log\_fdw Extension](#CHAP_PostgreSQL.Extensions.log_fdw) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| libprotobuf | 1\.3\.0 | 1\.3\.0 | 1\.3\.0 | 1\.3\.0 | 1\.3\.0 | 1\.3\.0 | 1\.3\.0 | 
| [ltree ](http://www.postgresql.org/docs/11/static/ltree.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [orafce](https://github.com/orafce/orafce) | 3\.7 | 3\.7 | 3\.7 | 3\.7 | 3\.7 | 3\.8 | 3\.8 | 
| pageinspect | 1\.6 | 1\.6 | 1\.6 | 1\.6 | 1\.6 | 1\.6 | 1\.6 | 
| [ pg\_buffercache](http://www.postgresql.org/docs/11/static/pgbuffercache.html) | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 
| [pg\_freespacemap](https://www.postgresql.org/docs/11/static/pgfreespacemap.html) | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 
| [pg\_hint\_plan](http://pghintplan.osdn.jp/pg_hint_plan.html) | 1\.3\.2 | 1\.3\.2 | 1\.3\.4 | 1\.3\.4 | 1\.3\.4 | 1\.3\.4 | 1\.3\.5 | 
| [ pg\_prewarm](https://www.postgresql.org/docs/11/static/pgprewarm.html) | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 
| [ pg\_repack ](http://reorg.github.io/pg_repack/) | 1\.4\.4 | 1\.4\.4 | 1\.4\.4 | 1\.4\.4 | 1\.4\.4 | 1\.4\.4 | 1\.4\.4 | 
| pg\_similarity | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [pg\_stat\_statements](http://www.postgresql.org/docs/11/static/pgstatstatements.html) | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 
| pg\_transport | N/A | N/A | N/A | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [pg\_trgm](http://www.postgresql.org/docs/11/static/pgtrgm.html) | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 
| [ pg\_visibility](https://www.postgresql.org/docs/11/static/pgvisibility.html) | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 
| [pgaudit](https://github.com/pgaudit/pgaudit/blob/master/README.md) | 1\.3\.0 | 1\.3\.0 | 1\.3\.0 | 1\.3\.0 | 1\.3\.0 | 1\.3\.0 | 1\.3\.0 | 
| [pgcrypto](http://www.postgresql.org/docs/11/static/pgcrypto.html) | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 
| [pglogical](https://github.com/2ndQuadrant/pglogical) | 2\.2\.1 | 2\.2\.1 | 2\.2\.1 | 2\.2\.1 | 2\.2\.1 | 2\.2\.1 | 2\.2\.1 | 
| [pgrowlocks](http://www.postgresql.org/docs/11/static/pgrowlocks.html) | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 
| [pgrouting](http://docs.pgrouting.org/2.3/en/doc/index.html) | 2\.6\.1 | 2\.6\.1 | 2\.6\.1 | 2\.6\.1 | 2\.6\.1 | 2\.6\.1 | 2\.6\.1 | 
| [pgstattuple](http://www.postgresql.org/docs/11/static/pgstattuple.html) | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 
|  [pgTAP](https://pgtap.org/)  | N/A | 1\.0 | 1\.0 | 1\.0 | 1\.1\.0 | 1\.1\.0 | 1\.1\.0 | 
| plcoffee | 2\.3\.8 | 2\.3\.8 | 2\.3\.8 | 2\.3\.8 | 2\.3\.8 | 2\.3\.8 | 2\.3\.8 | 
| plls | 2\.3\.8 | 2\.3\.8 | 2\.3\.8 | 2\.3\.8 | 2\.3\.8 | 2\.3\.8 | 2\.3\.8 | 
| [plperl](https://www.postgresql.org/docs/11/static/plperl.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [plpgsql](https://www.postgresql.org/docs/11/static/plpgsql.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [plprofiler](https://github.com/bigsql/plprofiler) | N/A | N/A | N/A | N/A | 4\.1 | 4\.1 | 4\.1 | 
| [pltcl](https://www.postgresql.org/docs/11/static/pltcl-overview.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [plv8](https://github.com/plv8) | 2\.3\.8 | 2\.3\.8 | 2\.3\.8 | 2\.3\.8 | 2\.3\.8 | 2\.3\.8 | 2\.3\.8 | 
| [PostGIS](http://www.postgis.net/) | 2\.5\.1 | 2\.5\.1 | 2\.5\.1 | 2\.5\.2 | 2\.5\.2 | 2\.5\.2 | 2\.5\.2 | 
| [ postgis\_tiger\_geocoder](http://postgis.net/docs/Geocode.html) | 2\.5\.1 | 2\.5\.1 | 2\.5\.1 | 2\.5\.1 | 2\.5\.1 | 2\.5\.1 | 2\.5\.1 | 
| [ postgis\_topology](http://postgis.net/docs/manual-dev/Topology.html) | 2\.5\.1 | 2\.5\.1 | 2\.5\.1 | 2\.5\.1 | 2\.5\.1 | 2\.5\.1 | 2\.5\.1 | 
| [postgres\_fdw](http://www.postgresql.org/docs/11/static/postgres-fdw.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ postgresql\-hll](https://github.com/citusdata/postgresql-hll/releases/tag/v2.10.2) | 2\.11 | 2\.11 | 2\.11 | 2\.11 | 2\.11 | 2\.11 | 2\.11 | 
|  [ prefix](https://github.com/dimitri/prefix) | 1\.2\.8 | 1\.2\.8 | 1\.2\.8 | 1\.2\.8 | 1\.2\.8 | 1\.2\.8 | 1\.2\.8 | 
| [sslinfo](http://www.postgresql.org/docs/11/static/sslinfo.html) | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 
| [tablefunc](http://www.postgresql.org/docs/11/static/tablefunc.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [test\_parser](https://www.postgresql.org/docs/9.4/static/test-parser.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [tsm\_system\_rows](https://www.postgresql.org/docs/11/static/tsm-system-rows.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [tsm\_system\_time](https://www.postgresql.org/docs/11/static/tsm-system-time.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
|  [unaccent](http://www.postgresql.org/docs/11/static/unaccent.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [uuid\-ossp](http://www.postgresql.org/docs/11/static/uuid-ossp.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 

The following modules are supported as shown for PostgreSQL version 11\.x\.


| Module | Version 11\.1 | Version 11\.2 | Version 11\.4 | Version 11\.5 | Version 11\.6 | Version 11\.7 | Version 11\.8 | 
| --- | --- | --- | --- | --- | --- | --- | --- | 
| amcheck | Supported | Supported | Supported | Supported | Supported | Supported | Supported | 
| auto\_explain | Supported | Supported | Supported | Supported | Supported | Supported | Supported | 
| decoder\_raw | Supported | Supported | Supported | Supported | Supported | Supported | Supported | 
| ICU | Version 60\.2 supported | Version 60\.2 supported | Version 60\.2 supported | Version 60\.2 supported | Version 60\.2 supported | Version 60\.2 supported | Version 60\.2 supported | 
| test\_decoding | Supported | Supported | Supported | Supported | Supported | Supported | Supported | 
| wal2json | Commit hash 9e962bad | Commit hash 9e962bad | Commit hash 9e962bad | Commit hash 9e962bad | Commit hash 9e962bad | version 2\.1 | version 2\.1 | 

##### PostgreSQL Version 10\.x Extensions and Modules Supported on Amazon RDS<a name="PostgreSQL.Concepts.General.FeatureSupport.Extensions.101x"></a>

The following tables show PostgreSQL extensions and modules for PostgreSQL version 10 that are currently supported by PostgreSQL on Amazon RDS\. "N/A" indicates that the extension or module is not available for that PostgreSQL version\. For more information on PostgreSQL extensions, see [Packaging Related Objects into an Extension](https://www.postgresql.org/docs/10/extend-extensions.html)\. 


| Extension | 10\.1 | 10\.3 | 10\.4 | 10\.5 | 10\.6 | 10\.7 | 10\.9 | 10\.10 | 10\.11 | 10\.12 | 10\.13 | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| [ address\_standardizer](http://postgis.net/docs/Address_Standardizer.html) | 2\.4\.2 | 2\.4\.2 | 2\.4\.2 | 2\.4\.2 | 2\.4\.2 | 2\.4\.2 | 2\.4\.2 | 2\.4\.2 | 2\.4\.2 | 2\.4\.2 | 2\.4\.2 | 
| [ address\_standardizer\_data\_us](http://postgis.net/docs/Address_Standardizer.html) | 2\.4\.2 | 2\.4\.2 | 2\.4\.2 | 2\.4\.2 | 2\.4\.2 | 2\.4\.2 | 2\.4\.2 | 2\.4\.2 | 2\.4\.2 | 2\.4\.2 | 2\.4\.2 | 
| [ bloom](https://www.postgresql.org/docs/10/static/bloom.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [btree\_gin](http://www.postgresql.org/docs/10/static/btree-gin.html) | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 
| [btree\_gist](http://www.postgresql.org/docs/10/static/btree-gist.html) | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 
| [chkpass](http://www.postgresql.org/docs/10/static/chkpass.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [citext](http://www.postgresql.org/docs/10/static/citext.html) | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 
| [cube](http://www.postgresql.org/docs/10/static/cube.html) | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 
| [dblink](http://www.postgresql.org/docs/10/static/dblink.html) | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 
| [dict\_int](http://www.postgresql.org/docs/10/static/dict-int.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [dict\_xsyn](https://www.postgresql.org/docs/10/static/dict-xsyn.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [earthdistance](http://www.postgresql.org/docs/10/static/earthdistance.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [fuzzystrmatch](http://www.postgresql.org/docs/10/static/fuzzystrmatch.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [hstore](http://www.postgresql.org/docs/10/static/hstore.html) | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 
| [hstore\_plperl](https://www.postgresql.org/docs/10/static/hstore.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ intagg](http://www.postgresql.org/docs/10/static/intagg.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [ intarray](http://www.postgresql.org/docs/10/static/intarray.html) | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 
| [ip4r](https://github.com/RhodiumToad/ip4r) | 2\.0 | 2\.0 | 2\.1\.1 | 2\.1\.1 | 2\.1\.1 | 2\.1\.1 | 2\.1\.1 | 2\.1\.1 | 2\.1\.1 | 2\.1\.1 | 2\.1\.1 | 
| [isn](http://www.postgresql.org/docs/10/static/isn.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| log\_fdw—see [Using the log\_fdw Extension](#CHAP_PostgreSQL.Extensions.log_fdw) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| libprotobuf | N/A | N/A | N/A | 1\.3\.0 | 1\.3\.0 | 1\.3\.0 | 1\.3\.0 | 1\.3\.0 | 1\.3\.0 | 1\.3\.0 | 1\.3\.0 | 
| [ltree](http://www.postgresql.org/docs/10/static/ltree.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [orafce](https://github.com/orafce/orafce) | 3\.6\.1 | 3\.6\.1 | 3\.6\.1 | 3\.6\.1 | 3\.6\.1 | 3\.6\.1 | 3\.6\.1 | 3\.6\.1 | 3\.6\.1 | 3\.8 | 3\.8 | 
| [pgaudit](https://github.com/pgaudit/pgaudit/blob/master/README.md) | 1\.2\.0 | 1\.2\.0 | 1\.2\.0 | 1\.2\.0 | 1\.2\.0 | 1\.2\.0 | 1\.2\.0 | 1\.2\.0 | 1\.2\.0 | 1\.2\.0 | 1\.2\.0 | 
| [pg\_buffercache](http://www.postgresql.org/docs/10/static/pgbuffercache.html) | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 
| [pg\_freespacemap](https://www.postgresql.org/docs/10/static/pgfreespacemap.html) | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 
| [pg\_hint\_plan](http://pghintplan.osdn.jp/pg_hint_plan.html) | 1\.3\.0 | 1\.3\.0 | 1\.3\.0 | 1\.3\.1 | 1\.3\.1 | 1\.3\.1 | 1\.3\.3 | 1\.3\.3 | 1\.3\.3 | 1\.3\.3 | 1\.3\.5 | 
| [pg\_prewarm](https://www.postgresql.org/docs/10/static/pgprewarm.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [pg\_repack ](http://reorg.github.io/pg_repack/) | 1\.4\.2 | 1\.4\.2 | 1\.4\.3 | 1\.4\.3 | 1\.4\.3 | 1\.4\.3 | 1\.4\.3 | 1\.4\.3 | 1\.4\.3 | 1\.4\.3 | 1\.4\.3 | 
| pg\_similarity | N/A | N/A | N/A | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [pg\_stat\_statements](http://www.postgresql.org/docs/10/static/pgstatstatements.html) | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 
| [pg\_trgm](http://www.postgresql.org/docs/10/static/pgtrgm.html) | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 
| [pg\_visibility](https://www.postgresql.org/docs/10/static/pgvisibility.html) | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 
| [pgcrypto](http://www.postgresql.org/docs/10/static/pgcrypto.html) | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 
| pageinspect | N/A | N/A | N/A | 1\.6 | 1\.6 | 1\.6 | 1\.6 | 1\.6 | 1\.6 | 1\.6 | 1\.6 | 
| [pglogical](https://github.com/2ndQuadrant/pglogical) | N/A | N/A | N/A | 2\.2\.0 | 2\.2\.0 | 2\.2\.0 | 2\.2\.0 | 2\.2\.0 | 2\.2\.0 | 2\.2\.0 | 2\.2\.0 | 
| [pgrowlocks](http://www.postgresql.org/docs/10/static/pgrowlocks.html) | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 
| [pgrouting](http://docs.pgrouting.org/2.3/en/doc/index.html) | 2\.5\.2 | 2\.5\.2 | 2\.5\.2 | 2\.5\.2 | 2\.5\.2 | 2\.5\.2 | 2\.5\.2 | 2\.5\.2 | 2\.5\.2 | 2\.5\.2 | 2\.5\.2 | 
| [pgstattuple](http://www.postgresql.org/docs/10/static/pgstattuple.html) | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 1\.5 | 
| plcoffee | 2\.1\.0 | 2\.1\.0 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 
| plls | 2\.1\.0 | 2\.1\.0 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 
| [plperl](https://www.postgresql.org/docs/10/static/plperl.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [plpgsql](https://www.postgresql.org/docs/10/static/plpgsql.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [plprofiler](https://github.com/bigsql/plprofiler) | N/A | N/A | N/A | N/A | N/A | N/A | N/A | N/A | 4\.1 | 4\.1 | 4\.1 | 
| [ pltcl](https://www.postgresql.org/docs/10/static/pltcl-overview.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [plv8](https://github.com/plv8) | 2\.1\.0 | 2\.1\.0 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 
| [PostGIS](http://www.postgis.net/) | 2\.4\.2 | 2\.4\.2 | 2\.4\.4 | 2\.4\.4 | 2\.4\.4 | 2\.4\.4 | 2\.4\.4 | 2\.5\.2 | 2\.5\.2 | 2\.5\.2 | 2\.5\.2 | 
| [ postgis\_tiger\_geocoder](http://postgis.net/docs/Geocode.html) | 2\.4\.2 | 2\.4\.2 | 2\.4\.2 | 2\.4\.2 | 2\.4\.2 | 2\.4\.2 | 2\.4\.2 | 2\.4\.2 | 2\.4\.2 | 2\.4\.2 | 2\.4\.2 | 
| [ postgis\_topology](http://postgis.net/docs/manual-dev/Topology.html) | 2\.4\.2 | 2\.4\.2 | 2\.4\.2 | 2\.4\.2 | 2\.4\.2 | 2\.4\.2 | 2\.4\.2 | 2\.4\.2 | 2\.4\.2 | 2\.4\.2 | 2\.4\.2 | 
| [postgres\_fdw](http://www.postgresql.org/docs/10/static/postgres-fdw.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ postgresql\-hll](https://github.com/citusdata/postgresql-hll/releases/tag/v2.10.2) | 2\.10\.2 | 2\.10\.2 | 2\.10\.2 | 2\.10\.2 | 2\.10\.2 | 2\.10\.2 | 2\.10\.2 | 2\.10\.2 | 2\.10\.2 | 2\.10\.2 | 2\.10\.2 | 
|  [ prefix](https://github.com/dimitri/prefix) | 1\.2\.0 | 1\.2\.0 | 1\.2\.0 | 1\.2\.0 | 1\.2\.0 | 1\.2\.0 | 1\.2\.0 | 1\.2\.0 | 1\.2\.0 | 1\.2\.0 | 1\.2\.0 | 
| [sslinfo](http://www.postgresql.org/docs/10/static/sslinfo.html) | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 
| [tablefunc](http://www.postgresql.org/docs/10/static/tablefunc.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ test\_parser](https://www.postgresql.org/docs/9.4/static/test-parser.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [tsearch2](http://www.postgresql.org/docs/9.6/static/tsearch2.html) \(deprecated in version 10\) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ tsm\_system\_rows](https://www.postgresql.org/docs/10/static/tsm-system-rows.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ tsm\_system\_time](https://www.postgresql.org/docs/10/static/tsm-system-time.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
|  [unaccent ](http://www.postgresql.org/docs/10/static/unaccent.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [uuid\-ossp](http://www.postgresql.org/docs/10/static/uuid-ossp.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 

The `tsearch2` extension is deprecated in version 10\. The PostgreSQL team plans to remove `tsearch2` from the next major release of PostgreSQL\.

The following modules are supported as shown for versions of PostgreSQL 10\.


| Module | Version 10\.1 | 10\.3 | 10\.4 | 10\.5 | 10\.6 | 10\.7 | 10\.9 | 10\.10 | 10\.11 | 10\.12 | 10\.13 | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| amcheck | Not supported | Supported | Supported | Supported | Supported | Supported | Supported | Supported | Supported | Supported | Supported | 
| auto\_explain | Supported | Supported | Supported | Supported | Supported | Supported | Supported | Supported | Supported | Supported | Supported | 
| decoder\_raw | Supported | Supported | Supported | Supported | Supported | Supported | Supported | Supported | Supported | Supported | Supported | 
| ICU | Version 60\.2 supported | Version 60\.2 supported | Version 60\.2 supported | Version 60\.2 supported | Version 60\.2 supported | Version 60\.2 supported | Version 60\.2 supported | Version 60\.2 supported | Version 60\.2 supported | Version 60\.2 supported | Version 60\.2 supported | 
| test\_decoding | Supported | Supported | Supported | Supported | Supported | Supported | Supported | Supported | Supported | Supported | Supported | 
| wal2json | Commit hash 5352cc4 | Commit hash 5352cc4 | Commit hash 5352cc4 | Commit hash 01c5c1e | Commit hash 9e962ba | Commit hash 9e962ba | Commit hash 9e962ba | Commit hash 9e962ba | Commit hash 9e962ba | version 2\.1 | version 2\.1 | 

##### PostgreSQL Version 9\.6\.x Extensions and Modules Supported on Amazon RDS<a name="PostgreSQL.Concepts.General.FeatureSupport.Extensions.96x"></a>

The following tables show PostgreSQL extensions and modules for PostgreSQL version 9\.6\.x that are currently supported by PostgreSQL on Amazon RDS\. "N/A" indicates that the extension or module is not available for that PostgreSQL version\. For more information on PostgreSQL extensions, see [Packaging Related Objects into an Extension](https://www.postgresql.org/docs/9.6/static/extend-extensions.html)\. 


| Extension | 9\.6\.1 | 9\.6\.2 | 9\.6\.3 | 9\.6\.5 | 9\.6\.6 | 9\.6\.8 | 9\.6\.9 | 9\.6\.10 | 9\.6\.11 | 9\.6\.12 | 9\.6\.14 | 9\.6\.15 | 9\.6\.16 | 9\.6\.17 | 9\.6\.18 | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| [ address\_standardizer](http://postgis.net/docs/Address_Standardizer.html) | 2\.3\.0 | 2\.3\.2 | 2\.3\.2 | 2\.3\.2 | 2\.3\.4 | 2\.3\.4 | 2\.3\.4 | 2\.3\.4 | 2\.3\.4 | 2\.3\.4 | 2\.3\.4 | 2\.3\.4 | 2\.3\.4 | 2\.3\.4 | 2\.3\.4 | 
| [ address\_standardizer\_data\_us](http://postgis.net/docs/Address_Standardizer.html) | 2\.3\.0 | 2\.3\.2 | 2\.3\.2 | 2\.3\.2 | 2\.3\.4 | 2\.3\.4 | 2\.3\.4 | 2\.3\.4 | 2\.3\.4 | 2\.3\.4 | 2\.3\.4 | 2\.3\.4 | 2\.3\.4 | 2\.3\.4 | 2\.3\.4 | 
| [ bloom](https://www.postgresql.org/docs/9.6/static/bloom.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [btree\_gin](http://www.postgresql.org/docs/9.6/static/btree-gin.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [btree\_gist](http://www.postgresql.org/docs/9.6/static/btree-gist.html) | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 
| [chkpass](http://www.postgresql.org/docs/9.6/static/chkpass.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [citext ](http://www.postgresql.org/docs/9.6/static/citext.html) | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 
| [cube ](http://www.postgresql.org/docs/9.6/static/cube.html) | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 
| [ dblink](http://www.postgresql.org/docs/9.6/static/dblink.html) | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 
| [ dict\_int ](http://www.postgresql.org/docs/9.6/static/dict-int.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ dict\_xsyn](https://www.postgresql.org/docs/9.6/static/dict-xsyn.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [earthdistance](http://www.postgresql.org/docs/9.6/static/earthdistance.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [fuzzystrmatch](http://www.postgresql.org/docs/9.6/static/fuzzystrmatch.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [hstore](http://www.postgresql.org/docs/9.6/static/hstore.html) | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 
| [ hstore\_plperl](https://www.postgresql.org/docs/9.6/static/hstore.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ intagg](http://www.postgresql.org/docs/9.6/static/intagg.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [ intarray](http://www.postgresql.org/docs/9.6/static/intarray.html) | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 
| [ip4r](https://github.com/RhodiumToad/ip4r) | 2\.0 | 2\.0 | 2\.0 | 2\.0 | 2\.0 | 2\.0 | 2\.1\.1 | 2\.1\.1 | 2\.1\.1 | 2\.1\.1 | 2\.1\.1 | 2\.1\.1 | 2\.1\.1 | 2\.1\.1 | 2\.1\.1 | 
| [isn ](http://www.postgresql.org/docs/9.6/static/isn.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| log\_fdw—see [Using the log\_fdw Extension](#CHAP_PostgreSQL.Extensions.log_fdw) |  N/A | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ltree ](http://www.postgresql.org/docs/9.6/static/ltree.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [orafce](https://github.com/orafce/orafce) |  N/A | N/A | N/A | N/A | 3\.6\.1 | 3\.6\.1 | 3\.6\.1 | 3\.6\.1 | 3\.6\.1 | 3\.6\.1 | 3\.6\.1 | 3\.6\.1 | 3\.6\.1 | 3\.8 | 3\.8 | 
| [pgaudit](https://github.com/pgaudit/pgaudit/blob/master/README.md) | N/A | N/A | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1\.1 | 1\.1\.1 | 1\.1\.1 | 1\.1\.1 | 1\.1\.1 | 1\.1\.1 | 1\.1\.1 | 1\.1\.1 | 1\.1\.1 | 
| [ pg\_buffercache](http://www.postgresql.org/docs/9.6/static/pgbuffercache.html) | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 
| [pg\_freespacemap](https://www.postgresql.org/docs/current/static/pgfreespacemap.html) |  N/A | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [pg\_hint\_plan](http://pghintplan.osdn.jp/pg_hint_plan.html) |  N/A | 1\.1\.3 | 1\.1\.3 | 1\.1\.3 | 1\.1\.3 | 1\.2\.2 | 1\.2\.2 | 1\.2\.3 | 1\.2\.3 | 1\.2\.3 | 1\.2\.5 | 1\.2\.5 | 1\.2\.5 | 1\.2\.5 | 1\.2\.6 | 
| [ pg\_prewarm](https://www.postgresql.org/docs/9.6/static/pgprewarm.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [ pg\_repack ](http://reorg.github.io/pg_repack/) | N/A | N/A | 1\.4\.0 | 1\.4\.1 | 1\.4\.2 | 1\.4\.2 | 1\.4\.3 | 1\.4\.3 | 1\.4\.3 | 1\.4\.3 | 1\.4\.3 | 1\.4\.3 | 1\.4\.3 | 1\.4\.3 | 1\.4\.3 | 
| pg\_similarity | N/A | N/A | N/A | N/A | N/A | N/A | N/A | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [pg\_stat\_statements](http://www.postgresql.org/docs/9.6/static/pgstatstatements.html) | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 
| [pg\_trgm](http://www.postgresql.org/docs/9.6/static/pgtrgm.html) | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 
| [ pg\_visibility](https://www.postgresql.org/docs/9.6/static/pgvisibility.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [pgcrypto](http://www.postgresql.org/docs/9.6/static/pgcrypto.html) | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 
| [pglogical](https://github.com/2ndQuadrant/pglogical) | N/A | N/A | N/A | N/A | N/A | N/A | N/A | 2\.2\.0 | 2\.2\.0 | 2\.2\.0 | 2\.2\.0 | 2\.2\.0 | 2\.2\.0 | 2\.2\.0 | 2\.2\.0 | 
| [pgrowlocks](http://www.postgresql.org/docs/9.6/static/pgrowlocks.html) | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 
| [pgrouting](http://docs.pgrouting.org/2.3/en/doc/index.html) | N/A | N/A | N/A | 2\.3\.2 | 2\.4\.2 | 2\.4\.2 | 2\.4\.2 | 2\.4\.2 | 2\.4\.2 | 2\.4\.2 | 2\.4\.2 | 2\.4\.2 | 2\.4\.2 | 2\.4\.2 | 2\.4\.2 | 
| [pgstattuple](http://www.postgresql.org/docs/9.6/static/pgstattuple.html) | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 
| plcoffee | 1\.5\.3 | 1\.5\.3 | 1\.5\.3 | 1\.5\.3 | 1\.5\.3 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 
| plls | 1\.5\.3 | 1\.5\.3 | 1\.5\.3 | 1\.5\.3 | 1\.5\.3 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 
| [ plperl](https://www.postgresql.org/docs/current/static/plperl.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ plpgsql](https://www.postgresql.org/docs/current/static/plpgsql.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ pltcl](https://www.postgresql.org/docs/current/static/pltcl-overview.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [plv8](https://github.com/plv8) | 1\.5\.3 | 1\.5\.3 | 1\.5\.3 | 1\.5\.3 | 1\.5\.3 | 2\.1\.0 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 
| [PostGIS](http://www.postgis.net/) | 2\.3\.0 | 2\.3\.2 | 2\.3\.2 | 2\.3\.2 | 2\.3\.4 | 2\.3\.4 | 2\.3\.7 | 2\.3\.7 | 2\.3\.7 | 2\.3\.7 | 2\.3\.7 | 2\.5\.2 | 2\.5\.2 | 2\.5\.2 | 2\.5\.2 | 
| [ postgis\_tiger\_geocoder](http://postgis.net/docs/Geocode.html) | 2\.3\.0 | 2\.3\.2 | 2\.3\.2 | 2\.3\.2 | 2\.3\.4 | 2\.3\.4 | 2\.3\.4 | 2\.3\.4 | 2\.3\.4 | 2\.3\.4 | 2\.3\.4 | 2\.3\.4 | 2\.3\.4 | 2\.3\.4 | 2\.3\.4 | 
| [ postgis\_topology](http://postgis.net/docs/manual-dev/Topology.html) | 2\.3\.0 | 2\.3\.2 | 2\.3\.2 | 2\.3\.2 | 2\.3\.4 | 2\.3\.4 | 2\.3\.4 | 2\.3\.4 | 2\.3\.4 | 2\.3\.4 | 2\.3\.4 | 2\.3\.4 | 2\.3\.4 | 2\.3\.4 | 2\.3\.4 | 
| [postgres\_fdw](http://www.postgresql.org/docs/9.6/static/postgres-fdw.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ postgresql\-hll](https://github.com/citusdata/postgresql-hll/releases/tag/v2.10.2) | N/A | N/A | N/A | 2\.10\.2 | 2\.10\.2 | 2\.10\.2 | 2\.10\.2 | 2\.10\.2 | 2\.10\.2 | 2\.10\.2 | 2\.10\.2 | 2\.10\.2 | 2\.10\.2 | 2\.10\.2 | 2\.10\.2 | 
|  [ prefix](https://github.com/dimitri/prefix) | N/A | N/A | N/A | N/A | 1\.2\.6 | 1\.2\.6 | 1\.2\.6 | 1\.2\.6 | 1\.2\.6 | 1\.2\.6 | 1\.2\.6 | 1\.2\.6 | 1\.2\.6 | 1\.2\.6 | 1\.2\.6 | 
| [sslinfo](http://www.postgresql.org/docs/9.6/static/sslinfo.html) | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 
| [tablefunc](http://www.postgresql.org/docs/9.6/static/tablefunc.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ test\_parser](https://www.postgresql.org/docs/9.4/static/test-parser.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [tsearch2](http://www.postgresql.org/docs/9.6/static/tsearch2.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ tsm\_system\_rows](https://www.postgresql.org/docs/current/static/tsm-system-rows.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ tsm\_system\_time](https://www.postgresql.org/docs/current/static/tsm-system-time.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
|  [unaccent ](http://www.postgresql.org/docs/9.6/static/unaccent.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [uuid\-ossp](http://www.postgresql.org/docs/9.6/static/uuid-ossp.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 

The following modules are supported as shown for versions of PostgreSQL 9\.6\.


| Module | 9\.6\.1 | 9\.6\.2 | 9\.6\.3 | 9\.6\.5 | 9\.6\.8 | 9\.6\.9 | 9\.6\.10 | 9\.6\.11 | 9\.6\.12 | 9\.6\.14 | 9\.6\.15 | 9\.6\.16 | 9\.6\.17 | 9\.6\.18 | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| auto\_explain | N/A | N/A | Supported | Supported | Supported | Supported | Supported | Supported | Supported | Supported | Supported | Supported | Supported | Supported | 
| decoder\_raw | N/A | N/A | N/A | Supported | Supported | Supported | Supported | Supported | Supported | Supported | Supported | Supported | Supported | Supported | 
| test\_decoding | Supported | Supported | Supported | Supported | Supported | Supported | Supported | Supported | Supported | Supported | Supported | Supported | Supported | Supported | 
| wal2json | N/A | N/a | Commit hash 2828409 | Commit hash 645ab69 | Commit hash 5352cc4 | Commit hash 5352cc4 | Commit hash 01c5c1e | Commit hash 9e962ba | Commit hash 9e962ba | Commit hash 9e962ba | Commit hash 9e962ba | Commit hash 9e962ba | version 2\.1 | version 2\.1 | 

##### PostgreSQL Version 9\.5\.x Extensions Supported on Amazon RDS<a name="PostgreSQL.Concepts.General.FeatureSupport.Extensions.95x"></a>

The following tables show PostgreSQL extensions and modules for PostgreSQL version 9\.5\.x that are currently supported by PostgreSQL on Amazon RDS\. "N/A" indicates that the extension or module is not available for that PostgreSQL version\. For more information on PostgreSQL extensions, see [Packaging Related Objects into an Extension](https://www.postgresql.org/docs/9.6/static/extend-extensions.html)\.


| Extension | 9\.5\.2 | 9\.5\.4 | 9\.5\.6 | 9\.5\.7 | 9\.5\.9 | 9\.5\.10 | 9\.5\.12 | 9\.5\.13 | 9\.5\.14 | 9\.5\.15 | 9\.5\.16 | 9\.5\.18 | 9\.5\.19 | 9\.5\.20 | 9\.5\.21 | 9\.5\.22 | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| [ address\_standardizer](http://postgis.net/docs/Address_Standardizer.html) | 2\.2\.2 | 2\.2\.2 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 
| [ address\_standardizer\_data\_us](http://postgis.net/docs/Address_Standardizer.html) | 2\.2\.2 | 2\.2\.2 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 
| [ bloom](https://www.postgresql.org/docs/9.6/static/bloom.html) | N/A | N/A | N/A | N/A | N/A | N/A | N/A | N/A | N/A | N/A | N/A | N/A | N/A | N/A | N/A | N/A | 
| [btree\_gin](http://www.postgresql.org/docs/9.6/static/btree-gin.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [btree\_gist](http://www.postgresql.org/docs/9.6/static/btree-gist.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [chkpass](http://www.postgresql.org/docs/9.6/static/chkpass.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [citext ](http://www.postgresql.org/docs/9.6/static/citext.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [cube ](http://www.postgresql.org/docs/9.6/static/cube.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ dblink](http://www.postgresql.org/docs/9.6/static/dblink.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [ dict\_int ](http://www.postgresql.org/docs/9.6/static/dict-int.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ dict\_xsyn](https://www.postgresql.org/docs/9.6/static/dict-xsyn.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [earthdistance](http://www.postgresql.org/docs/9.6/static/earthdistance.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [fuzzystrmatch](http://www.postgresql.org/docs/9.6/static/fuzzystrmatch.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [hstore](http://www.postgresql.org/docs/9.6/static/hstore.html) | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 
| [ hstore\_plperl](https://www.postgresql.org/docs/9.6/static/hstore.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ intagg](http://www.postgresql.org/docs/9.6/static/intagg.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ intarray](http://www.postgresql.org/docs/9.6/static/intarray.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ip4r](https://github.com/RhodiumToad/ip4r) | 2\.0 | 2\.0 | 2\.0 | 2\.0 | 2\.0 | 2\.0 | 2\.0 | 2\.0 | 2\.0 | 2\.0 | 2\.0 | 2\.0 | 2\.0 | 2\.0 | 2\.0 | 2\.0 | 
| [isn ](http://www.postgresql.org/docs/9.6/static/isn.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| log\_fdw—see [Using the log\_fdw Extension](#CHAP_PostgreSQL.Extensions.log_fdw) | N/A | N/A | N/A | N/A | N/A | N/A | N/A | N/A | N/A | N/A | N/A | N/A | N/A | N/A | N/A | N/A | 
| [ltree ](http://www.postgresql.org/docs/9.6/static/ltree.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ pgaudit](http://www.postgresql.org/docs/9.6/static/pgbuffercache.html) | N/A | N/A | N/A | 1\.0\.5 | 1\.0\.5 | 1\.0\.5 | 1\.0\.5 | 1\.0\.6 | 1\.0\.6 | 1\.0\.6 | 1\.0\.6 | 1\.0\.6 | 1\.0\.6 | 1\.0\.6 | 1\.0\.6 | 1\.0\.6 | 
| [ pg\_buffercache](http://www.postgresql.org/docs/9.6/static/pgbuffercache.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [pg\_freespacemap](https://www.postgresql.org/docs/current/static/pgfreespacemap.html) |  N/A |  N/A | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [pg\_hint\_plan](http://pghintplan.osdn.jp/pg_hint_plan.html) |  N/A |  N/A | 1\.1\.3 | 1\.1\.3 | 1\.1\.3 | 1\.1\.3 | 1\.1\.3 | 1\.1\.5 | 1\.1\.5 | 1\.1\.5 | 1\.1\.5 | 1\.1\.8 | 1\.1\.8 | 1\.1\.8 | 1\.1\.8 | 1\.1\.9 | 
| [ pg\_prewarm](https://www.postgresql.org/docs/9.6/static/pgprewarm.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [pg\_stat\_statements](http://www.postgresql.org/docs/9.6/static/pgstatstatements.html) | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 
| [pg\_trgm](http://www.postgresql.org/docs/9.6/static/pgtrgm.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [ pg\_visibility](https://www.postgresql.org/docs/9.6/static/pgvisibility.html) |  N/A |  N/A |  N/A |  N/A |  N/A |  N/A | N/A | N/A | N/A | N/A | N/A | N/A | N/A | N/A | N/A | N/A | 
| [pgcrypto](http://www.postgresql.org/docs/9.6/static/pgcrypto.html) | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 
| [pgrowlocks](http://www.postgresql.org/docs/9.6/static/pgrowlocks.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [pgstattuple](http://www.postgresql.org/docs/9.6/static/pgstattuple.html) | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 
| plcoffee | 1\.4\.4 | 1\.4\.4 | 1\.4\.4 | 1\.4\.4 | 1\.4\.4 | 1\.4\.4 | 2\.1\.0 | 2\.1\.0 | 2\.1\.0 | 2\.1\.0 | 2\.1\.0 | 2\.1\.0 | 2\.1\.0 | 2\.1\.0 | 2\.1\.0 | 2\.1\.0 | 
| plls | 1\.4\.4 | 1\.4\.4 | 1\.4\.4 | 1\.4\.4 | 1\.4\.4 | 1\.4\.4 | 2\.1\.0 | 2\.1\.0 | 2\.1\.0 | 2\.1\.0 | 2\.1\.0 | 2\.1\.0 | 2\.1\.0 | 2\.1\.0 | 2\.1\.0 | 2\.1\.0 | 
| [ plperl](https://www.postgresql.org/docs/current/static/plperl.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ plpgsql](https://www.postgresql.org/docs/current/static/plpgsql.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ pltcl](https://www.postgresql.org/docs/current/static/pltcl-overview.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [plv8](https://github.com/plv8) | 1\.4\.4 | 1\.4\.4 | 1\.4\.4 | 1\.4\.4 | 1\.4\.4 | 1\.4\.4 | 2\.1\.0 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 2\.1\.2 | 
| [PostGIS](http://www.postgis.net/) | 2\.2\.2 | 2\.2\.2 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.5\.2 | 2\.5\.2 | 2\.5\.2 | 2\.5\.2 | 
| [ postgis\_tiger\_geocoder](http://postgis.net/docs/Geocode.html) | 2\.2\.2 | 2\.2\.2 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 
| [ postgis\_topology](http://postgis.net/docs/manual-dev/Topology.html) | 2\.2\.2 | 2\.2\.2 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 
| [postgres\_fdw](http://www.postgresql.org/docs/9.6/static/postgres-fdw.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [sslinfo](http://www.postgresql.org/docs/9.6/static/sslinfo.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [tablefunc](http://www.postgresql.org/docs/9.6/static/tablefunc.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ test\_parser](https://www.postgresql.org/docs/9.4/static/test-parser.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [tsearch2](http://www.postgresql.org/docs/9.6/static/tsearch2.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ tsm\_system\_rows](https://www.postgresql.org/docs/current/static/tsm-system-rows.html) |  N/A |  N/A | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ tsm\_system\_time](https://www.postgresql.org/docs/current/static/tsm-system-time.html) |  N/A |  N/A | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
|  [unaccent ](http://www.postgresql.org/docs/9.6/static/unaccent.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [uuid\-ossp](http://www.postgresql.org/docs/9.6/static/uuid-ossp.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 

The following modules are supported as shown for versions of PostgreSQL 9\.5\.


| Module | 9\.5\.2 | 9\.5\.4 | 9\.5\.6 | 9\.5\.7 | 9\.5\.9 | 9\.5\.12 | 9\.5\.13 | 9\.5\.14 | 9\.5\.15 | 9\.5\.16 | 9\.5\.18 | 9\.5\.19 | 9\.5\.20 | 9\.5\.21 | 9\.5\.22 | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| auto\_explain | N/A | N/A | N/A | Supported | Supported | Supported | Supported | Supported | Supported | Supported | Supported | Supported | Supported | Supported | Supported | 
| test\_decoding | N/A | N/A | Supported | Supported | Supported | Supported | Supported | Supported | Supported | Supported | Supported | Supported | Supported | Supported | Supported | 
| wal2json | N/A | N/a | N/A | Commit hash 2828409 | Commit hash 2828409 | Commit hash 2828409 | Commit hash 2828409 | Commit hash 2828409 | Commit hash 2828409 | Commit hash 2828409 | Commit hash 2828409 | Commit hash 2828409 | Commit hash 2828409 | version 2\.1 | version 2\.1 | 

##### PostgreSQL Extension Support for PostGIS on Amazon RDS<a name="CHAP_PostgreSQL.Extensions.SupportedPerVersion"></a>

Before you can use the PostGIS extension, you must create it by running the following command\.

```
 CREATE EXTENSION POSTGIS;
```

The following table shows the PostGIS component versions that ship with the Amazon RDS for PostgreSQL versions\.


| PostgreSQL | PostGIS | GEOS | GDAL | PROJ | 
| --- | --- | --- | --- | --- | 
| 9\.5\.2 |  2\.2\.2 r14797  |  3\.5\.0\-CAPI\-1\.9\.0 r4084  |  2\.0\.2, released 2016/01/26  | Rel\. 4\.9\.2, September 8th, 2015 | 
| 9\.5\.4 |  2\.2\.2 r14797  |  3\.5\.0\-CAPI\-1\.9\.0 r4084  |  2\.0\.3, released 2016/07/01  | Rel\. 4\.9\.2, September 8th, 2015 | 
| 9\.5\.6 |  2\.2\.5 r15298  |  3\.5\.1\-CAPI\-1\.9\.1 r4246  |  2\.0\.3, released 2016/07/01  |  Rel\. 4\.9\.3, September 15th, 2016  | 
| 9\.5\.7 |  2\.2\.5 r15298  |  3\.5\.1\-CAPI\-1\.9\.1 r4246  |  2\.0\.3, released 2016/07/01  |  Rel\. 4\.9\.3, September 15th, 2016  | 
| 9\.6\.1 |  2\.3\.0 r15146  |  3\.5\.0\-CAPI\-1\.9\.0 r4084  |  2\.1\.1, released 2016/07/07  | Rel\. 4\.9\.2, September 8th, 2016 | 
| 9\.6\.2 |  2\.3\.2 r15302  |  3\.5\.1\-CAPI\-1\.9\.1 r4246  |  2\.1\.3, released 2017/20/01  |  Rel\. 4\.9\.3, September 15th, 2016  | 
| 9\.6\.3 |  2\.3\.2 r15302  |  3\.5\.1\-CAPI\-1\.9\.1 r4246  |  2\.1\.3, released 2017/20/01  |  Rel\. 4\.9\.3, September 15th, 2016  | 
| 9\.6\.6 | 2\.3\.4 r16009 | 3\.6\.2\-CAPI\-1\.10\.2 4d2925d6 | 2\.1\.3, released 2017/20/01 | Rel\. 4\.9\.3, September 15th, 2016 | 
| 9\.6\.8 | 2\.3\.4 r16009 | 3\.6\.2\-CAPI\-1\.10\.2 4d2925d6 | 2\.1\.3, released 2017/20/01 | Rel\. 4\.9\.3, September 15th, 2016 | 
| 9\.6\.9 | 2\.3\.7 r16523 | 3\.6\.2\-CAPI\-1\.10\.2 4d2925d6 | 2\.1\.4, released 2017/06/23 |  Rel\. 4\.9\.3, September 15th, 2016  | 
| 9\.6\.10 | 2\.3\.7 r16523 | 3\.6\.2\-CAPI\-1\.10\.2 4d2925d6 | 2\.1\.4, released 2017/06/23 |  Rel\. 4\.9\.3, September 15th, 2016  | 
| 9\.6\.11 | 2\.3\.7 r16523 | 3\.6\.2\-CAPI\-1\.10\.2 4d2925d6 | 2\.1\.4, released 2017/06/23 |  Rel\. 4\.9\.3, September 15th, 2016  | 
| 10\.1 | 2\.4\.2 | 3\.6\.2\-CAPI\-1\.10\.2 4d2925d6 | 2\.1\.3, released 2017/20/01  | Rel\. 4\.9\.3, September 15th, 2016 | 
| 10\.3 | 2\.4\.2 | 3\.6\.2\-CAPI\-1\.10\.2 4d2925d6 | 2\.1\.3, released 2017/20/01  | Rel\. 4\.9\.3, September 15th, 2016 | 
| 10\.4 | 2\.4\.4 r16526 | 3\.6\.2\-CAPI\-1\.10\.2 4d2925d6 | 2\.1\.4, released 2017/06/23 |  Rel\. 4\.9\.3, September 15th, 2016  | 
| 10\.5  | 2\.4\.4 r16526 | 3\.6\.2\-CAPI\-1\.10\.2 4d2925d6 | 2\.1\.4, released 2017/06/23 | Rel\. 4\.9\.3, September 15th, 2016  | 
| 10\.6 | 2\.4\.4 r16526 | 3\.6\.2\-CAPI\-1\.10\.2 4d2925d6 | 2\.1\.4, released 2017/06/23 | Rel\. 4\.9\.3, September 15th, 2016  | 
| 11\.1 | 2\.5\.1 r17027 | 3\.7\.0\-CAPI\-1\.11\.0 673b9939 | 2\.3\.1, released 2018/06/22 | Rel\. 5\.2\.0, September 15th, 2018 | 

**Note**  
PostgreSQL 10\.5 added support for the `libprotobuf` extension version 1\.3\.0 to the PostGIS component\. 

##### Using the log\_fdw Extension<a name="CHAP_PostgreSQL.Extensions.log_fdw"></a>

The `log_fdw` extension is new for Amazon RDS for PostgreSQL version 9\.6\.2 and later\. Using this extension, you can access your database engine log using a SQL interface\. In addition to viewing the *stderr* log files that are generated by default on RDS, you can view CSV logs \(set the `log_destination` parameter to `csvlog`\) and build foreign tables with the data neatly split into several columns\.

This extension introduces two new functions that make it easy to create foreign tables for database logs:
+ `list_postgres_log_files()` – Lists the files in the database log directory and the file size in bytes\.
+ `create_foreign_table_for_log_file(table_name text, server_name text, log_file_name text)` – Builds a foreign table for the specified file in the current database\.

All functions created by `log_fdw` are owned by `rds_superuser`\. Members of the `rds_superuser` role can grant access to these functions to other database users\.

The following example shows how to use the `log_fdw` extension\.

**To use the log\_fdw extension**

1. Get the `log_fdw` extension\.

   ```
   postgres=> CREATE EXTENSION log_fdw;
   CREATE EXTENSION
   ```

1. Create the log server as a foreign data wrapper\.

   ```
   postgres=> CREATE SERVER log_server FOREIGN DATA WRAPPER log_fdw;
   CREATE SERVER
   ```

1. Select all from a list of log files\.

   ```
   postgres=> SELECT * from list_postgres_log_files() order by 1;
   ```

   A sample response is as follows\.

   ```
               file_name             | file_size_bytes 
   ----------------------------------+-----------------
    postgresql.log.2016-08-09-22.csv |            1111
    postgresql.log.2016-08-09-23.csv |            1172
    postgresql.log.2016-08-10-00.csv |            1744
    postgresql.log.2016-08-10-01.csv |            1102
   (4 rows)
   ```

1. Create a table with a single 'log\_entry' column for non\-CSV files\.

   ```
   postgres=> SELECT create_foreign_table_for_log_file('my_postgres_error_log', 
        'log_server', 'postgresql.log.2016-08-09-22.csv');
   ```

   A sample response is as follows\.

   ```
   -----------------------------------
   
   (1 row)
   ```

1. Select a sample of the log file\. The following code retrieves the log time and error message description\.

   ```
   postgres=> SELECT log_time, message from my_postgres_error_log order by 1;
   ```

   A sample response is as follows\.

   ```
                log_time             |                                  message                                 
   ----------------------------------+---------------------------------------------------------------------------
   Tue Aug 09 15:45:18.172 2016 PDT | ending log output to stderr
   Tue Aug 09 15:45:18.175 2016 PDT | database system was interrupted; last known up at 2016-08-09 22:43:34 UTC
   Tue Aug 09 15:45:18.223 2016 PDT | checkpoint record is at 0/90002E0
   Tue Aug 09 15:45:18.223 2016 PDT | redo record is at 0/90002A8; shutdown FALSE
   Tue Aug 09 15:45:18.223 2016 PDT | next transaction ID: 0/1879; next OID: 24578
   Tue Aug 09 15:45:18.223 2016 PDT | next MultiXactId: 1; next MultiXactOffset: 0
   Tue Aug 09 15:45:18.223 2016 PDT | oldest unfrozen transaction ID: 1822, in database 1
   (7 rows)
   ```

#### Upgrading PL/v8<a name="PostgreSQL.Concepts.General.UpgradingPLv8"></a>

If you use PL/v8 and upgrade PostgreSQL to a new PL/v8 version, you immediately take advantage of the new extension\. Take the following steps to synchronize your catalog metadata with the new version of PL/v8\. These steps are optional, but we highly recommended that you complete them to avoid metadata mismatch warnings\.

**To synchronize your catalog metadata with a new version of PL/v8**

1. Verify that you need to update\. To do this, run the following command while connected to your instance\.

   `select * from pg_available_extensions where name in ('plv8','plls','plcoffee');`

   If your results contain values for an installed version that is a lower number than the default version, continue with this procedure to update your extensions\. 

   For example, the following result set indicates that you should update\.

   ```
   name    | default_version | installed_version |                     comment
   --------+-----------------+-------------------+--------------------------------------------------
   plls    | 2.1.0           | 1.5.3             | PL/LiveScript (v8) trusted procedural language
   plcoffee| 2.1.0           | 1.5.3             | PL/CoffeeScript (v8) trusted procedural language
   plv8    | 2.1.0           | 1.5.3             | PL/JavaScript (v8) trusted procedural language
   (3 rows)
   ```

1. Take a snapshot of your instance as a precaution, because the upgrade drops all your PL/v8 functions\. You can continue with the following steps while the snapshot is being created\.

   For steps to create a snapshot see, [Creating a DB Snapshot](USER_CreateSnapshot.md)

1. Get a count of the number of PL/v8 functions in your DB instance so you can validate that they are all in place after the upgrade\. 

   The following code returns the number of functions written in PL/v8, plcoffee, or plls\.

   ```
   select proname, nspname, lanname 
   from pg_proc p, pg_language l, pg_namespace n
   where p.prolang = l.oid
   and n.oid = p.pronamespace
   and lanname in ('plv8','plcoffee','plls');
   ```

1. Use pg\_dump to create a schema\-only dump file\.

   The following code creates a file on your client machine in the /tmp directory\. 

   `./pg_dump -Fc --schema-only -U master postgres > /tmp/test.dmp`

   This example uses the following options:
   + \-FC "format custom"
   + \-\-schema\-only "will only dump commands necessary to create schema \(functions in our case\)"
   + \-U "rds master username"
   + database "the database name in our instance"

   For more information on pg\_dump, see the [pg\_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html ) page in the PostgreSQL documentation\.

1. Extract the "CREATE FUNCTION" DDL statement that is present in the dump file\.

   The following code extracts the DDL statement needed to create the functions\. You use this in subsequent steps to recreate the functions\. The code uses the `grep` command to extract the statements to a file\.

   `./pg_restore -l /tmp/test.dmp | grep FUNCTION > /tmp/function_list/`

   For more information on pg\_restore see, [pg\_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html)\. 

1. Drop the functions and extensions\.

   The following code drops any PL/v8 based objects\. The cascade option ensures that any dependent are dropped\.

   `drop extension plv8 cascade;`

   If your PostgreSQL instance contains objects based on plcoffee or plls, repeat this step for those extensions\.

1. Create the extensions\.

   The following code creates the PL/v8, plcoffee, and plls extensions\.

   `create extension plv8;`

   `create extension plcoffee;`

   `create extension plls;`

1. Create the functions using the dump file and "driver" file\.

   The following code recreates the functions that you extracted previously\.

   `./pg_restore -U master -d postgres -Fc -L /tmp/function_list /tmp/test.dmp`

1. Verify your functions count\.

   Validate that your functions have all been recreated by running the following code statement\.

   `select * from pg_available_extensions where name in ('plv8','plls','plcoffee'); `
**Note**  
PL/v8 version 2 adds the following extra row to your result set:  

   ```
       proname    |  nspname   | lanname
   ---------------+------------+----------
    plv8_version  | pg_catalog | plv8
   ```

#### Supported PostgreSQL Features<a name="PostgreSQL.Concepts.General.FeatureSupport"></a>

Amazon RDS supports many of the most common PostgreSQL features\. These include:

**Topics**
+ [Logical Replication for PostgreSQL on Amazon RDS](#PostgreSQL.Concepts.General.FeatureSupport.LogicalReplication)
+ [Event Triggers for PostgreSQL on Amazon RDS](#PostgreSQL.Concepts.General.FeatureSupport.EventTriggers)
+ [Huge Pages for Amazon RDS for PostgreSQL](#PostgreSQL.Concepts.General.FeatureSupport.HugePages)
+ [Tablespaces for PostgreSQL on Amazon RDS](#PostgreSQL.Concepts.General.FeatureSupport.Tablespaces)
+ [Autovacuum for PostgreSQL on Amazon RDS](#PostgreSQL.Concepts.General.FeatureSupport.Autovacuum)
+ [RAM Disk for the stats\_temp\_directory](#PostgreSQL.Concepts.General.FeatureSupport.RamDisk)
+ [ALTER ENUM for PostgreSQL](#PostgreSQL.Concepts.General.FeatureSupport.AlterEnum)

##### Logical Replication for PostgreSQL on Amazon RDS<a name="PostgreSQL.Concepts.General.FeatureSupport.LogicalReplication"></a>

Beginning with PostgreSQL version 10\.4, RDS supports the publication and subscription SQL Syntax for PostgreSQL 10 Logical Replication\.

**To enable logical replication for an Amazon RDS for PostgreSQL DB instance**

1. The AWS user account requires the **rds\_superuser** role to perform logical replication for the PostgreSQL database on Amazon RDS\.

1. Set the `rds.logical_replication` static parameter to 1\. 

1. Modify the inbound rules of the security group for the publisher instance \(production\) to allow the subscriber instance \(replica\) to connect\. This is usually done by including the IP address of the subscriber in the security group\.

1. Restart the DB instance for the changes to the static parameter `rds.logical_replication` to take effect\. 

For more information on PostgreSQL logical replication, see the [PostgreSQL documentation](https://www.postgresql.org/docs/10/static/logical-replication.html)\. 

##### Logical Decoding and Logical Replication<a name="PostgreSQL.Concepts.General.FeatureSupport.LogicalDecoding"></a>

RDS for PostgreSQL supports the streaming of WAL changes using logical replication slots\. Amazon RDS supports logical decoding for a PostgreSQL DB instance version 9\.5\.4 and higher\. You can set up logical replication slots on your instance and stream database changes through these slots to a client such as `pg_recvlogical`\. Logical replication slots are created at the database level and support replication connections to a single database\. 

The most common clients for PostgreSQL logical replication are the AWS Database Migration Service or a custom\-managed host on an AWS EC2 instance\. The logical replication slot knows nothing about the receiver of the stream, and there is no requirement that the target be a replica database\. If you set up a logical replication slot and don't read from the slot, data can be written and quickly fill up your DB instance's storage\.

PostgreSQL logical replication and logical decoding on Amazon RDS are enabled with a parameter, a replication connection type, and a security role\. The client for logical decoding can be any client that is capable of establishing a replication connection to a database on a PostgreSQL DB instance\. 

**To enable logical decoding for an Amazon RDS for PostgreSQL DB instance**

1. The user account requires the **rds\_superuser** role to enable logical replication\. The user account also requires the **rds\_replication** role to grant permissions to manage logical slots and to stream data using logical slots\.

1. Set the `rds.logical_replication` static parameter to 1\. As part of applying this parameter, we also set the parameters `wal_level`, `max_wal_senders`, `max_replication_slots`, and `max_connections`\. These parameter changes can increase WAL generation, so you should only set the `rds.logical_replication` parameter when you are using logical slots\.

1. Reboot the DB instance for the static `rds.logical_replication` parameter to take effect\.

1. Create a logical replication slot as explained in the next section\. This process requires that you specify a decoding plugin\. Currently we support the `test_decoding` and `wal2json` output plugins that ship with PostgreSQL\.

For more information on PostgreSQL logical decoding, see the [ PostgreSQL documentation](https://www.postgresql.org/docs/current/static/logicaldecoding-explanation.html)\.

##### Working with Logical Replication Slots<a name="PostgreSQL.Concepts.General.FeatureSupport.LogicalReplicationSlots"></a>

You can use SQL commands to work with logical slots\. For example, the following command creates a logical slot named test\_slot using the default PostgreSQL output plugin test\_decoding\.

```
SELECT * FROM pg_create_logical_replication_slot('test_slot', 'test_decoding');
```

The output should be similar to the following\.

```
slot_name    | xlog_position
-----------------+---------------
regression_slot | 0/16B1970
(1 row)
```

To list logical slots, use the following command\.

```
SELECT * FROM pg_replication_slots;
```

To drop a logical slot, use the following command\.

```
SELECT pg_drop_replication_slot('test_slot');
```

The output should be similar to the following\.

```
pg_drop_replication_slot
-----------------------

(1 row)
```

For more examples on working with logical replication slots, see [ Logical Decoding Examples](https://www.postgresql.org/docs/9.5/static/logicaldecoding-example.html) in the PostgreSQL documentation\.

Once you create the logical replication slot, you can start streaming\. The following example shows how logical decoding is controlled over the streaming replication protocol, using the program pg\_recvlogical included in the PostgreSQL distribution\. This requires that client authentication is set up to allow replication connections\.

```
pg_recvlogical -d postgres --slot test_slot -U master 
    --host sg-postgresql1.c6c8mresaghgv0.us-west-2.rds.amazonaws.com 
    -f -  --start
```

##### Event Triggers for PostgreSQL on Amazon RDS<a name="PostgreSQL.Concepts.General.FeatureSupport.EventTriggers"></a>

PostgreSQL versions 9\.5\.4 and later support event triggers, and Amazon RDS supports event triggers for these versions\. The master user account can be used to create, modify, rename, and delete event triggers\. Event triggers are at the DB instance level, so they can apply to all databases on an instance\.

For example, the following code creates an event trigger that prints the current user at the end of every DDL command\.

```
CREATE OR REPLACE FUNCTION raise_notice_func()
                RETURNS event_trigger
                LANGUAGE plpgsql AS
$$
BEGIN
                RAISE NOTICE 'In trigger function: %', current_user;
END;
$$;

CREATE EVENT TRIGGER event_trigger_1 
                ON ddl_command_end
EXECUTE PROCEDURE raise_notice_func();
```

For more information about PostgreSQL event triggers, see [Event Triggers](https://www.postgresql.org/docs/current/static/event-triggers.html) in the PostgreSQL documentation\.

There are several limitations to using PostgreSQL event triggers on Amazon RDS\. These include:
+ You cannot create event triggers on read replicas\. You can, however, create event triggers on a read replica source\. The event triggers are then copied to the read replica\. The event triggers on the read replica don't fire on the read replica when changes are pushed from the source\. However, if the read replica is promoted, the existing event triggers fire when database operations occur\.
+ To perform a major version upgrade to a PostgreSQL DB instance that uses event triggers, you must delete the event triggers before you upgrade the instance\.

##### Huge Pages for Amazon RDS for PostgreSQL<a name="PostgreSQL.Concepts.General.FeatureSupport.HugePages"></a>

Amazon RDS for PostgreSQL supports multiple page sizes for PostgreSQL versions 9\.5\.6 and later, and 9\.6\.2 and later\. This support includes 4 K and 2 MB page sizes\. 

Huge pages reduce overhead when using large contiguous chunks of memory\. You allocate huge pages for your application by using calls to *mmap* or *SYSV* shared memory\. You enable huge pages on an Amazon RDS for PostgreSQL database by using the `huge_pages` parameter\. Set this parameter to "on" to enable huge pages\. 

For PostgreSQL versions 10 and above, huge pages are enabled for all instance classes\. For PostgreSQL versions below 10, huge pages are enabled by default for db\.r4\.\*, db\.m4\.16xlarge, and db\.m5\.\* instance classes\. For other instance classes, huge pages are disabled by default\. 

When you set the `huge_pages` parameter to "on," Amazon RDS uses huge pages based on the available shared memory\. If the DB instance is unable to use huge pages due to shared memory constraints, Amazon RDS prevents the instance from starting and sets the status of the DB instance to an incompatible parameters state\. In this case, you can set the `huge_pages` parameter to "off" to allow Amazon RDS to start the DB instance\.

The `shared_buffers` parameter is key to setting the shared memory pool that is required for using huge pages\. The default value for the `shared_buffers` parameter is set to a percentage of the total 8K pages available for that instance's memory\. When you use huge pages, those pages are allocated in the huge pages collocated together\. Amazon RDS puts a DB instance into an incompatible parameters state if the shared memory parameters are set to require more than 90 percent of the DB instance memory\. For more information about setting shared memory for PostgreSQL, see the [PostgreSQL documentation](https://www.postgresql.org/docs/9.6/static/runtime-config-resource.html)\.

**Note**  
Huge pages are not supported for the db\.m1, db\.m2, and db\.m3 DB instance classes\. 

##### Tablespaces for PostgreSQL on Amazon RDS<a name="PostgreSQL.Concepts.General.FeatureSupport.Tablespaces"></a>

Tablespaces are supported in PostgreSQL on Amazon RDS for compatibility; since all storage is on a single logical volume, tablespaces cannot be used for IO splitting or isolation\. We have benchmarks and practical experience that shows that a single logical volume is the best setup for most use cases\.

##### Autovacuum for PostgreSQL on Amazon RDS<a name="PostgreSQL.Concepts.General.FeatureSupport.Autovacuum"></a>

The PostgreSQL autovacuum feature is turned on by default for new PostgreSQL DB instances\. Autovacuum is optional, but we highly recommend that you do not turn autovacuum off\. For more information on using autovacuum with Amazon RDS for PostgreSQL, see [Working with PostgreSQL Autovacuum on Amazon RDS](Appendix.PostgreSQL.CommonDBATasks.md#Appendix.PostgreSQL.CommonDBATasks.Autovacuum)\.

##### RAM Disk for the stats\_temp\_directory<a name="PostgreSQL.Concepts.General.FeatureSupport.RamDisk"></a>

The Amazon RDS for PostgreSQL parameter, `rds.pg_stat_ramdisk_size`, can be used to specify the system memory allocated to a RAM disk for storing the PostgreSQL `stats_temp_directory`\. The RAM disk parameter is available for all PostgreSQL versions on Amazon RDS\. 

Under certain workloads, setting this parameter can improve performance and decrease IO requirements\. For more information about the `stats_temp_directory`, see [ the PostgreSQL documentation\.](https://www.postgresql.org/docs/current/static/runtime-config-statistics.html#GUC-STATS-TEMP-DIRECTORY)\.

To enable a RAM disk for your `stats_temp_directory`, set the `rds.pg_stat_ramdisk_size` parameter to a non\-zero value in the parameter group used by your DB instance\. The parameter value is in MB\. You must reboot the DB instance before the change takes effect\.

For example, the following AWS CLI command sets the RAM disk parameter to 256 MB\.

```
aws rds modify-db-parameter-group \
    --db-parameter-group-name pg-95-ramdisk-testing \
    --parameters "ParameterName=rds.pg_stat_ramdisk_size, ParameterValue=256, ApplyMethod=pending-reboot"
```

After you reboot, run the following command to see the status of the `stats_temp_directory`:

```
postgres=>show stats_temp_directory;
```

 The command should return the following: 

```
stats_temp_directory
---------------------------
/rdsdbramdisk/pg_stat_tmp
(1 row)
```

##### ALTER ENUM for PostgreSQL<a name="PostgreSQL.Concepts.General.FeatureSupport.AlterEnum"></a>

Amazon RDS for PostgreSQL versions 9\.6\.2 and 9\.5\.6 and later support the ability to alter enumerations\. This feature is not available in other versions on Amazon RDS\.

The following code shows an example of altering an enum value\.

```
postgres=> CREATE TYPE rainbow AS ENUM ('red', 'orange', 'yellow', 'green', 'blue', 'purple');
CREATE TYPE
postgres=> CREATE TABLE t1 (colors rainbow);
CREATE TABLE
postgres=> INSERT INTO t1 VALUES ('red'), ( 'orange');
INSERT 0 2
postgres=> SELECT * from t1;
colors
--------
red
orange
(2 rows)
postgres=> ALTER TYPE rainbow RENAME VALUE 'red' TO 'crimson';
ALTER TYPE
postgres=> SELECT * from t1;
colors
---------
crimson
orange
(2 rows)
```

#### Limits for PostgreSQL DB Instances<a name="PostgreSQL.Concepts.General.Limits"></a>

The following is a list of limitations for PostgreSQL on Amazon RDS:
+ You can have up to 40 PostgreSQL DB instances\.
+ For storage limits, see [Amazon RDS DB Instance Storage](CHAP_Storage.md)\.
+ Amazon RDS reserves up to 3 connections for system maintenance\. If you specify a value for the user connections parameter, you need to add 3 to the number of connections that you expect to use\. 

#### Upgrading a PostgreSQL DB Instance<a name="PostgreSQL.Concepts.General.Patching"></a>

There are two types of upgrades you can manage for your PostgreSQL DB instance:
+ OS Updates – Occasionally, Amazon RDS might need to update the underlying operating system of your DB instance to apply security fixes or OS changes\. You can decide when Amazon RDS applies OS updates by using the RDS console, AWS Command Line Interface \(AWS CLI\), or RDS API\.

  For more information about OS updates, see [Applying Updates for a DB Instance](USER_UpgradeDBInstance.Maintenance.md#USER_UpgradeDBInstance.OSUpgrades)\.
+  Database Engine Upgrades – When Amazon RDS supports a new version of a database engine, you can upgrade your DB instances to the new version\. There are two kinds of upgrades: major version upgrades and minor version upgrades\. Amazon RDS supports both major and minor version upgrades for PostgreSQL DB instances\. 

  For more information about PostgreSQL DB engine upgrades, see [Upgrading the PostgreSQL DB Engine for Amazon RDS](USER_UpgradeDBInstance.PostgreSQL.md)\.

#### Using SSL with a PostgreSQL DB Instance<a name="PostgreSQL.Concepts.General.SSL"></a>

Amazon RDS supports Secure Socket Layer \(SSL\) encryption for PostgreSQL DB instances\. Using SSL, you can encrypt a PostgreSQL connection between your applications and your PostgreSQL DB instances\. You can also force all connections to your PostgreSQL DB instance to use SSL\. 

For general information about SSL support and PostgreSQL databases, see [SSL Support](https://www.postgresql.org/docs/11/libpq-ssl.html) in the PostgreSQL documentation\. For information about using an SSL connection over JDBC, see [Configuring the Client](https://jdbc.postgresql.org/documentation/head/ssl-client.html) in the PostgreSQL documentation\.

**Topics**
+ [Requiring an SSL Connection to a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.SSL.Requiring)
+ [Determining the SSL Connection Status](#PostgreSQL.Concepts.General.SSL.Status)

SSL support is available in all AWS regions for PostgreSQL\. Amazon RDS creates an SSL certificate for your PostgreSQL DB instance when the instance is created\. If you enable SSL certificate verification, then the SSL certificate includes the DB instance endpoint as the Common Name \(CN\) for the SSL certificate to guard against spoofing attacks\. 

**To connect to a PostgreSQL DB instance over SSL**

1. Download the certificate\.

   For information about downloading certificates, see [Using SSL/TLS to Encrypt a Connection to a DB Instance](UsingWithRDS.SSL.md)\.

1. Import the certificate into your operating system\.

1. Connect to your PostgreSQL DB instance over SSL\.

   When you connect using SSL, your client can choose whether to verify the certificate chain\. If your connection parameters specify `sslmode=verify-ca` or `sslmode=verify-full`, then your client requires the RDS CA certificates to be in their trust store or referenced in the connection URL\. This requirement is to verify the certificate chain that signs your database certificate\.

   When a client, such as psql or JDBC, is configured with SSL support, the client first tries to connect to the database with SSL by default\. If the client can't connect with SSL, it reverts to connecting without SSL\. The default `sslmode` mode used is different between libpq\-based clients \(such as psql\) and JDBC\. The libpq\-based clients default to `prefer`, and JDBC clients default to `verify-full`\.

   Use the `sslrootcert` parameter to reference the certificate, for example `sslrootcert=rds-ssl-ca-cert.pem`\.

The following is an example of using psql to connect to a PostgreSQL DB instance\.

```
$ psql -h testpg.cdhmuqifdpib.us-east-1.rds.amazonaws.com -p 5432 \
    "dbname=testpg user=testuser sslrootcert=rds-ca-2019-root.pem sslmode=verify-full"
```

##### Requiring an SSL Connection to a PostgreSQL DB Instance<a name="PostgreSQL.Concepts.General.SSL.Requiring"></a>

You can require that connections to your PostgreSQL DB instance use SSL by using the `rds.force_ssl` parameter\. By default, the `rds.force_ssl` parameter is set to 0 \(off\)\. You can set the `rds.force_ssl` parameter to 1 \(on\) to require SSL for connections to your DB instance\. Updating the `rds.force_ssl` parameter also sets the PostgreSQL `ssl` parameter to 1 \(on\) and modifies your DB instance's `pg_hba.conf` file to support the new SSL configuration\.

You can set the `rds.force_ssl` parameter value by updating the parameter group for your DB instance\. If the parameter group for your DB instance isn't the default one, and the `ssl` parameter is already set to 1 when you set `rds.force_ssl` to 1, you don't need to reboot your DB instance\. Otherwise, you must reboot your DB instance for the change to take effect\. For more information on parameter groups, see [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)\.

When the `rds.force_ssl` parameter is set to 1 for a DB instance, you see output similar to the following when you connect, indicating that SSL is now required:

```
$ psql postgres -h SOMEHOST.amazonaws.com -p 8192 -U someuser
. . .
SSL connection (cipher: DHE-RSA-AES256-SHA, bits: 256)
Type "help" for help.

postgres=>
```

##### Determining the SSL Connection Status<a name="PostgreSQL.Concepts.General.SSL.Status"></a>

The encrypted status of your connection is shown in the logon banner when you connect to the DB instance:

```
Password for user master: 
psql (10.3) 
SSL connection (cipher: DHE-RSA-AES256-SHA, bits: 256) 
Type "help" for help.   

postgres=>
```

You can also load the `sslinfo` extension and then call the `ssl_is_used()` function to determine if SSL is being used\. The function returns `t` if the connection is using SSL, otherwise it returns `f`\.

```
postgres=> create extension sslinfo;
CREATE EXTENSION

postgres=> select ssl_is_used();
 ssl_is_used
---------
t
(1 row)
```

You can use the `select ssl_cipher()` command to determine the SSL cipher:

```
postgres=> select ssl_cipher();
ssl_cipher
--------------------
DHE-RSA-AES256-SHA
(1 row)
```

 If you enable `set rds.force_ssl` and restart your instance, non\-SSL connections are refused with the following message:

```
$ export PGSSLMODE=disable
$ psql postgres -h SOMEHOST.amazonaws.com -p 8192 -U someuser
psql: FATAL: no pg_hba.conf entry for host "host.ip", user "someuser", database "postgres", SSL off
$
```

For information about the `sslmode` option, see [Database Connection Control Functions](https://www.postgresql.org/docs/11/libpq-connect.html#LIBPQ-CONNECT-SSLMODE) in the PostgreSQL documentation\.