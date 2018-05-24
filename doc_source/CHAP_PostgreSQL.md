# PostgreSQL on Amazon RDS<a name="CHAP_PostgreSQL"></a>

Amazon RDS supports DB instances running several versions of PostgreSQL\. You can create DB instances and DB snapshots, point\-in\-time restores and backups\. DB instances running PostgreSQL support Multi\-AZ deployments, Read Replicas \(version 9\.3\.5 and later\), Provisioned IOPS, and can be created inside a VPC\. You can also use Secure Socket Layer \(SSL\) to connect to a DB instance running PostgreSQL\.

Before creating a DB instance, you should complete the steps in the [Setting Up for Amazon RDS](CHAP_SettingUp.md) section of this guide\. 

You can use any standard SQL client application to run commands for the instance from your client computer\. Such applications include *pgAdmin*, a popular Open Source administration and development tool for PostgreSQL, or *psql*, a command line utility that is part of a PostgreSQL installation\. In order to deliver a managed service experience, Amazon RDS does not provide host access to DB instances, and it restricts access to certain system procedures and tables that require advanced privileges\. Amazon RDS supports access to databases on a DB instance using any standard SQL client application\. Amazon RDS does not allow direct host access to a DB instance via Telnet or Secure Shell \(SSH\)\. 

Amazon RDS for PostgreSQL is compliant with many industry standards\. For example, you can use Amazon RDS for PostgreSQL databases to build HIPAA\-compliant applications and to store healthcare\-related information, including protected health information \(PHI\) under an executed Business Associate Agreement \(BAA\) with AWS\. Amazon RDS for PostgreSQL also meets Federal Risk and Authorization Management Program \(FedRAMP\) security requirements\. Amazon RDS for PostgreSQL has received a FedRAMP Joint Authorization Board \(JAB\) Provisional Authority to Operate \(P\-ATO\) at the FedRAMP HIGH Baseline within the AWS GovCloud \(US\) Region\. For more information on supported compliance standards, see [AWS Cloud Compliance](https://aws.amazon.com/compliance/)\.

To import PostgreSQL data into a DB instance, follow the information in the [Importing Data into PostgreSQL on Amazon RDS](PostgreSQL.Procedural.Importing.md) section\. 

**Topics**
+ [Common Management Tasks for PostgreSQL on Amazon RDS](#CHAP_PostgreSQL.CommonTasks)
+ [Creating a DB Instance Running the PostgreSQL Database Engine](USER_CreatePostgreSQLInstance.md)
+ [Connecting to a DB Instance Running the PostgreSQL Database Engine](USER_ConnectToPostgreSQLInstance.md)
+ [Modifying a DB Instance Running the PostgreSQL Database Engine](USER_ModifyPostgreSQLInstance.md)
+ [Upgrading the PostgreSQL DB Engine](USER_UpgradeDBInstance.PostgreSQL.md)
+ [Importing Data into PostgreSQL on Amazon RDS](PostgreSQL.Procedural.Importing.md)
+ [Common DBA Tasks for PostgreSQL](Appendix.PostgreSQL.CommonDBATasks.md)
+ [Working with the Database Preview Environment](#working-with-the-database-preview-environment)
+ [Amazon RDS PostgreSQL Versions and Extensions](#PostgreSQL.Concepts)

## Common Management Tasks for PostgreSQL on Amazon RDS<a name="CHAP_PostgreSQL.CommonTasks"></a>

The following are the common management tasks you perform with an Amazon RDS PostgreSQL DB instance, with links to relevant documentation for each task\. 


| Task Area | Relevant Documentation | 
| --- | --- | 
|  **Setting up Amazon RDS for first\-time use** There are prerequisites you must complete before you create your DB instance\. For example, DB instances are created by default with a firewall that prevents access to it\. You therefore must create a security group with the correct IP addresses and network configuration to access the DB instance\.   |  [Setting Up for Amazon RDS](CHAP_SettingUp.md)  | 
|  **Understanding Amazon RDS DB instances** If you are creating a DB instance for production purposes, you should understand how instance classes, storage types, and Provisioned IOPS work in Amazon RDS\.   |  [DB Instance Class](Concepts.DBInstanceClass.md) [Amazon RDS Storage Types](CHAP_Storage.md#Concepts.Storage) [Provisioned IOPS SSD Storage](CHAP_Storage.md#USER_PIOPS)  | 
|  **Finding supported PostgreSQL versions** Amazon RDS supports several versions of PostgreSQL\.   |  [Supported PostgreSQL Database Versions](#PostgreSQL.Concepts.General.DBVersions)  | 
|  **Setting up high availability and failover support** A production DB instance should use Multi\-AZ deployments\. Multi\-AZ deployments provide increased availability, data durability, and fault tolerance for DB instances\.   |  [High Availability \(Multi\-AZ\)](Concepts.MultiAZ.md)  | 
|  **Understanding the Amazon Virtual Private Cloud \(VPC\) network** If your AWS account has a default VPC, then your DB instance is automatically created inside the default VPC\. If your account does not have a default VPC, and you want the DB instance in a VPC, you must create the VPC and subnet groups before you create the DB instance\.   |  [Determining Whether You Are Using the EC2\-VPC or EC2\-Classic Platform](USER_VPC.FindDefaultVPC.md) [Working with an Amazon RDS DB Instance in a VPC](USER_VPC.WorkingWithRDSInstanceinaVPC.md)  | 
|  **Importing data into Amazon RDS PostgreSQL** You can use several different tools to import data into your PostgreSQL DB instance on Amazon RDS\.   |  [Importing Data into PostgreSQL on Amazon RDS](PostgreSQL.Procedural.Importing.md)  | 
|  **Setting up read only Read Replicas \(master/standby\)** PostgreSQL on Amazon RDS supports Read Replicas in both the same AWS Region and in a different AWS Region from the master instance\.  |  [Working with Read Replicas of MariaDB, MySQL, and PostgreSQL DB Instances](USER_ReadRepl.md) [PostgreSQL Read Replicas \(Version 9\.3\.5 and Later\)PostgreSQL Read Replicas](USER_ReadRepl.md#USER_ReadRepl.PostgreSQL) [Creating a Read Replica in a Different AWS Region](USER_ReadRepl.md#USER_ReadRepl.XRgn)  | 
|  **Understanding security groups** By default, DB instances are created with a firewall that prevents access to them\. You therefore must create a security group with the correct IP addresses and network configuration to access the DB instance\.  In general, if your DB instance is on the *EC2\-Classic* platform, you need to create a DB security group\. If your DB instance is on the *EC2\-VPC* platform, you need to create a VPC security group\.   |  [Determining Whether You Are Using the EC2\-VPC or EC2\-Classic Platform](USER_VPC.FindDefaultVPC.md) [Amazon RDS Security Groups](Overview.RDSSecurityGroups.md)  | 
|  **Setting up parameter groups and features** If your DB instance is going to require specific database parameters, you should create a parameter group before you create the DB instance\.   |  [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)  | 
|  **Performing common DBA tasks for PostgreSQL** Some of the more common tasks for PostgreSQL DBAs include:  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html)  |  [Common DBA Tasks for PostgreSQL](Appendix.PostgreSQL.CommonDBATasks.md)  | 
|  **Connecting to your PostgreSQL DB instance** After creating a security group and associating it to a DB instance, you can connect to the DB instance using any standard SQL client application such as pgadmin III\.   |  [Connecting to a DB Instance Running the PostgreSQL Database Engine](USER_ConnectToPostgreSQLInstance.md) [Using SSL with a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.SSL)  | 
|  **Backing up and restoring your DB instance** You can configure your DB instance to take automated backups, or take manual snapshots, and then restore instances from the backups or snapshots\.   |  [Backing Up and Restoring Amazon RDS DB Instances](CHAP_CommonTasks.BackupRestore.md)  | 
|  **Monitoring the activity and performance of your DB instance** You can monitor a PostgreSQL DB instance by using CloudWatch Amazon RDS metrics, events, and enhanced monitoring\.   |  [Viewing DB Instance Metrics](CHAP_Monitoring.md#USER_Monitoring) [Viewing Amazon RDS Events](USER_ListEvents.md)  | 
|  **Upgrading the PostgreSQL database version** You can do both major and minor version upgrades for your PostgreSQL DB instance\.   |  [Upgrading a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.Patching) [Major Version Upgrades](USER_UpgradeDBInstance.PostgreSQL.md#USER_UpgradeDBInstance.PostgreSQL.MajorVersion)  | 
|  **Working with log files** You can access the log files for your PostgreSQL DB instance\.   |  [PostgreSQL Database Log Files](USER_LogAccess.Concepts.PostgreSQL.md)  | 
|  **Understanding the best practices for PostgreSQL DB instances** Find some of the best practices for working with PostgreSQL on Amazon RDS\.   |  [Best Practices for Working with PostgreSQL](CHAP_BestPractices.md#CHAP_BestPractices.PostgreSQL)  | 

## Working with the Database Preview Environment<a name="working-with-the-database-preview-environment"></a>

When you create a DB instance in Amazon RDS, you know that the PostgreSQL version it's based on has been tested and is fully supported by Amazon\. The PostgreSQL community releases new versions and new extensions continuously\. You can try out new PostgreSQL versions and extensions before they are fully supported\. To do that, you can create a new DB instance in the Database Preview Environment\. 

DB instances in the Database Preview Environment are similar to DB instances in a production environment\. However, keep in mind several important factors:
+ All DB instances are deleted 60 days after you create them, along with any backups and snapshots\.
+ You can only create a DB instance in a virtual private cloud \(VPC\) based on the Amazon VPC service\.
+ You can only create M4, T2, and R4 instance types\. For more information about RDS instance classes, see [DB Instance Class](Concepts.DBInstanceClass.md)\. 
+ You can't get help from AWS Support with DB instances\. You can post your questions in the [RDS Database Preview Environment Forum](https://forums.aws.amazon.com/forum.jspa?forumID=301)\.
+ You can only use General Purpose SSD and Provisioned IOPS SSD storage\. 
+ You can't copy a snapshot of a DB instance to a production environment\.
+ Some Amazon RDS features aren't available in the preview environment, as described following\.

### Features Not Supported in the Preview Environment<a name="preview-environment-exclusions"></a>

The following features are not available in the preview environment:
+ Cross\-region snapshot copy
+ Cross\-region Read Replicas
+ Extensions not in the following table of supported extensions

The PostgreSQL extensions supported in the Database Preview Environment are listed following\.


| Extension | Version | 
| --- | --- | 
|  amcheck  |  1\.1  | 
|  bloom  |  1\.0  | 
|  btree\_gin  |  1\.3  | 
|  btree\_gist  |  1\.5  | 
|  citext  |  1\.5  | 
|  cube  |  1\.4  | 
|  dblink  |  1\.2  | 
|  dict\_int  |  1\.0  | 
|  dict\_xsyn  |  1\.0  | 
|  earthdistance  |  1\.1  | 
|  fuzzystrmatch  |  1\.1  | 
|  hstore  |  1\.5  | 
|  hstore\_plper  |  1\.0  | 
|  intagg  |  1\.1  | 
|  antarray  |  1\.2  | 
|  isn  |  1\.2  | 
|  log\_fdw  |  1\.0  | 
|  ltree  |  1\.1  | 
|  pg\_buffercache  |  1\.3  | 
|  pg\_freespacemap  |  1\.2  | 
|  pg\_prewarm  |  1\.2  | 
|  pg\_stat\_statements  |  1\.5  | 
|  pg\_trgm  |  1\.4  | 
|  pg\_visibility  |  1\.2  | 
|  pgcrypto  |  1\.3  | 
|  pgrowlocks  |  1\.2  | 
|  pgstattuple  |  1\.5  | 
|  plperl  |  1\.0  | 
|  plpgsql  |  1\.0  | 
|  pltcl  |  1\.0  | 
|  postgres\_fdw  |  1\.0  | 
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
If you want to create an instance in the Database Preview Environment with the API or CLI the endpoint is `rds-preview.us-east-2.amazonaws.com`\.

1. Continue with the procedure as described in [Create a PostgreSQL DB Instance](USER_CreatePostgreSQLInstance.md#USER_CreatePostgreSQLInstance.CON)\.

## Amazon RDS PostgreSQL Versions and Extensions<a name="PostgreSQL.Concepts"></a>

Amazon RDS supports DB instances running several editions of PostgreSQL\. Use this section to see how to work with PostgreSQL on Amazon RDS\. You should also be aware of the limits for PostgreSQL DB instances\.

For information about importing PostgreSQL data into a DB instance, see [Importing Data into PostgreSQL on Amazon RDS](PostgreSQL.Procedural.Importing.md)\.

**Topics**
+ [Supported PostgreSQL Database Versions](#PostgreSQL.Concepts.General.DBVersions)
+ [Supported PostgreSQL Features and Extensions](#PostgreSQL.Concepts.General.FeaturesExtensions)

### Supported PostgreSQL Database Versions<a name="PostgreSQL.Concepts.General.DBVersions"></a>

Amazon RDS supports the following PostgreSQL versions\.

**Topics**
+ [PostgreSQL Version 10\.3 on Amazon RDS](#PostgreSQL.Concepts.General.version103)
+ [PostgreSQL Version 10\.1 on Amazon RDS](#PostgreSQL.Concepts.General.version101)
+ [PostgreSQL Version 9\.6\.8 on Amazon RDS](#PostgreSQL.Concepts.General.version968)
+ [PostgreSQL Version 9\.6\.6 on Amazon RDS](#PostgreSQL.Concepts.General.version966)
+ [PostgreSQL Version 9\.6\.5 on Amazon RDS](#PostgreSQL.Concepts.General.version965)
+ [PostgreSQL Version 9\.6\.3 on Amazon RDS](#PostgreSQL.Concepts.General.version963)
+ [PostgreSQL Version 9\.6\.2 on Amazon RDS](#PostgreSQL.Concepts.General.version962)
+ [PostgreSQL Version 9\.6\.1 on Amazon RDS](#PostgreSQL.Concepts.General.version961)
+ [PostgreSQL Version 9\.5\.12 on Amazon RDS](#PostgreSQL.Concepts.General.version9512)
+ [PostgreSQL Version 9\.5\.10 on Amazon RDS](#PostgreSQL.Concepts.General.version9510)
+ [PostgreSQL Version 9\.5\.9 on Amazon RDS](#PostgreSQL.Concepts.General.version959)
+ [PostgreSQL Version 9\.5\.7 on Amazon RDS](#PostgreSQL.Concepts.General.version957)
+ [PostgreSQL Version 9\.5\.6 on Amazon RDS](#PostgreSQL.Concepts.General.version956)
+ [PostgreSQL Version 9\.5\.4 on Amazon RDS](#PostgreSQL.Concepts.General.version954)
+ [PostgreSQL Version 9\.5\.2 on Amazon RDS](#PostgreSQL.Concepts.General.version952)
+ [PostgreSQL Version 9\.4\.17 on Amazon RDS](#PostgreSQL.Concepts.General.version9417)
+ [PostgreSQL Version 9\.4\.15 on Amazon RDS](#PostgreSQL.Concepts.General.version9415)
+ [PostgreSQL Version 9\.4\.14 on Amazon RDS](#PostgreSQL.Concepts.General.version9414)
+ [PostgreSQL Version 9\.4\.12 on Amazon RDS](#PostgreSQL.Concepts.General.version9412)
+ [PostgreSQL Version 9\.4\.11 on Amazon RDS](#PostgreSQL.Concepts.General.version9411)
+ [PostgreSQL Version 9\.4\.9 on Amazon RDS](#PostgreSQL.Concepts.General.version949)
+ [PostgreSQL Version 9\.4\.7 on Amazon RDS](#PostgreSQL.Concepts.General.version947)
+ [PostgreSQL Version 9\.3\.22 on Amazon RDS](#PostgreSQL.Concepts.General.version9322)
+ [PostgreSQL Version 9\.3\.20 on Amazon RDS](#PostgreSQL.Concepts.General.version9320)
+ [PostgreSQL Version 9\.3\.19 on Amazon RDS](#PostgreSQL.Concepts.General.version9319)
+ [PostgreSQL Version 9\.3\.17 on Amazon RDS](#PostgreSQL.Concepts.General.version9317)
+ [PostgreSQL Version 9\.3\.16 on Amazon RDS](#PostgreSQL.Concepts.General.version9316)
+ [PostgreSQL Version 9\.3\.14 on Amazon RDS](#PostgreSQL.Concepts.General.version9314)
+ [PostgreSQL Version 9\.3\.12 on Amazon RDS](#PostgreSQL.Concepts.General.version9312)

#### PostgreSQL Version 10\.3 on Amazon RDS<a name="PostgreSQL.Concepts.General.version103"></a>

PostgreSQL version 10\.3 contains several bug fixes for issues in release 10\. For more information on the fixes in 10\.3, see the [PostgreSQL documentation](http://www.postgresql.org/docs/10/static/release-10-3.html)\.

Version 2\.1\.0 of PL/v8 is now available\. If you use PL/v8 and upgrade PostgreSQL to a new PL/v8 version, you will immediately take advantage of the new extension but the catalog metadata will not reflect this fact\. See [Upgrade PL/v8](#PostgreSQL.Concepts.General.UpgradingPLv8) for the optional steps to synchronize your catalog metadata with the new version of PL/v8\.

For information on upgrading the engine version for your PostgreSQL DB instance, see [Upgrading a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.Patching)\. 

For the complete list of extensions supported by Amazon RDS PostgreSQL, see [Supported PostgreSQL Features and Extensions](#PostgreSQL.Concepts.General.FeaturesExtensions)\.

#### PostgreSQL Version 10\.1 on Amazon RDS<a name="PostgreSQL.Concepts.General.version101"></a>

PostgreSQL version 10\.1 contains several bug fixes for issues in release 10\. For more information on the fixes in 10\.1, see the [PostgreSQL documentation](http://www.postgresql.org/docs/10/static/release-10-1.html) and the [PostgreSQL 10 commmunity announcement](https://www.postgresql.org/about/news/1786/)\.

For information on upgrading the engine version for your PostgreSQL DB instance, see [Upgrading a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.Patching)\. 

PostgreSQL version 10\.1 includes the following changes: 
+ **Declarative table partitioning** – PostgreSQL 10 adds table partitioning to SQL syntax and native tuple routing\. 
+ **Parallel queries** – When you create a new PostgreSQL 10\.1 instance, parallel queries are enabled for the `default.postgres10` parameter group\. The parameter [max\_parallel\_workers\_per\_gather](https://www.postgresql.org/docs/10/static/runtime-config-resource.html#GUC-MAX-PARALLEL-WORKERS-PER-GATHER) is set to 2 by default, but you can modify it to support your specific workload requirements\.
+ **Support for the International Components for Unicode \(ICU\)** – You can use the ICU library to provide explicitly versioned collations\. Amazon RDS for PostgreSQL 10\.1 is compiled with ICU version 60\.2\. For more information about ICU implementation in PostgreSQL, see [Collation Support](https://www.postgresql.org/docs/10/static/collation.html)\.
+ **Huge pages** – Huge pages is a feature of the Linux kernel that uses multiple page size capabilities of modern hardware architectures\. Amazon RDS for PostgreSQL supports huge pages with a global configuration parameter\. When you create a new PostgreSQL 10\.1 instance with RDS, the `huge_pages` parameter is set to `"on"` for the` default.postgres10` parameter group\. You can modify this setting to support your specific workload requirements\. 
+ **PL/v8 update** – PL/v8 is a procedural language that allows you to write functions in JavaScript that you can then call from SQL\. This release of PostgreSQL supports version 2\.1\.0 of PL/v8\.
+ **Renaming of xlog and location** – In PostgreSQL version 10 the abbreviation "xlog" has changed to "wal", and the term "location" has changed to "lsn"\. For more information, see [ https://www\.postgresql\.org/docs/10/static/release\-10\.html\#id\-1\.11\.6\.8\.4](https://www.postgresql.org/docs/10/static/release-10.html#id-1.11.6.8.4)\. 
+ **tsearch2 module** – Amazon RDS will continue to provide the `tsearch2` module in PostgreSQL version 10, but will remove it in the next major version release\. If your application uses tsearch2 functions update it to use the equivalent functions the core engine provides\. For more information about using `tsearch2`, see [tsearch2 module](https://www.postgresql.org/docs/9.6/static/tsearch2.html)\.

For the complete list of extensions supported by Amazon RDS PostgreSQL, see [Supported PostgreSQL Features and Extensions](#PostgreSQL.Concepts.General.FeaturesExtensions)\.

#### PostgreSQL Version 9\.6\.8 on Amazon RDS<a name="PostgreSQL.Concepts.General.version968"></a>

PostgreSQL version 9\.6\.8 contains several bug fixes for issues in release 9\.6\.6\. For more information on the fixes in 9\.6\.8, see the [PostgreSQL documentation](http://www.postgresql.org/docs/9.6/static/release-9-6-8.html)\. For information on upgrading the engine version for your PostgreSQL DB instance, see [Upgrading a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.Patching)\. 

For the complete list of extensions supported by Amazon RDS PostgreSQL, see [Supported PostgreSQL Features and Extensions](#PostgreSQL.Concepts.General.FeaturesExtensions)\.

#### PostgreSQL Version 9\.6\.6 on Amazon RDS<a name="PostgreSQL.Concepts.General.version966"></a>

PostgreSQL version 9\.6\.6 contains several bug fixes for issues in release 9\.6\.5\. For more information on the fixes in 9\.6\.6, see the [PostgreSQL documentation](http://www.postgresql.org/docs/9.6/static/release-9-6-6.html)\. For information on upgrading the engine version for your PostgreSQL DB instance, see [Upgrading a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.Patching)\. 

This version includes the following features: 
+ Supports the `orafce` extension, version 3\.6\.1\. This extension contains functions that are native to commercial databases, and can be helpful if you are porting a commercial database to PostgreSQL\.For more information about using `orafce` with Amazon RDS, see [Working with the orafce Extension](Appendix.PostgreSQL.CommonDBATasks.md#Appendix.PostgreSQL.CommonDBATasks.orafce)\.
+ Supports the `prefix` extension, version 1\.2\.6\. This extension provides an operator for text prefix searches\. For more information about `prefix`, see the [prefix project on GitHub](https://github.com/dimitri/prefix)\.
+ Supports version 2\.3\.4 of PostGIS, version 2\.4\.2 of pgrouting, and an updated version of wal2json\.

For the complete list of extensions supported by Amazon RDS PostgreSQL, see [Supported PostgreSQL Features and Extensions](#PostgreSQL.Concepts.General.FeaturesExtensions)\.

#### PostgreSQL Version 9\.6\.5 on Amazon RDS<a name="PostgreSQL.Concepts.General.version965"></a>

PostgreSQL version 9\.6\.5 contains several bug fixes for issues in release 9\.6\.4\. For more information on the fixes in 9\.6\.5, see the [PostgreSQL documentation](http://www.postgresql.org/docs/9.6/static/release-9-6-5.html)\. For information on upgrading the engine version for your PostgreSQL DB instance, see [Upgrading a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.Patching)\. 

This version also includes support for the [pgrouting](http://pgrouting.org/) and [postgresql\-hll](https://github.com/citusdata/postgresql-hll/releases/tag/v2.10.2) extensions, and the [decoder\_raw](https://github.com/michaelpq/pg_plugins/tree/master/decoder_raw) optional module\.

For the complete list of extensions supported by Amazon RDS PostgreSQL, see [Supported PostgreSQL Features and Extensions](#PostgreSQL.Concepts.General.FeaturesExtensions)\.

#### PostgreSQL Version 9\.6\.3 on Amazon RDS<a name="PostgreSQL.Concepts.General.version963"></a>

PostgreSQL version 9\.6\.3 contains several new features and bug fixes\. This version includes the following features: 
+ Supports the extension `pg_repack` version 1\.4\.0\. You can use this extension to remove bloat from tables and indexes\. For more information on using `pg_repack` with Amazon RDS, see [Working with the pg\_repack Extension](Appendix.PostgreSQL.CommonDBATasks.md#Appendix.PostgreSQL.CommonDBATasks.pg_repack)\.
+ Supports the extension `pgaudit` version 1\.1\.0\. This extension provides detailed session and object audit logging\. For more information on using pgaudit with Amazon RDS, see [Working with the pgaudit Extension](Appendix.PostgreSQL.CommonDBATasks.md#Appendix.PostgreSQL.CommonDBATasks.pgaudit)\.
+ Supports `wal2json`, an output plugin for logical decoding\.
+ Supports the `auto_explain` module\. You can use this module to log execution plans of slow statements automatically\. The following example shows how to use `auto_explain` from within an Amazon RDS PostgreSQL session:

  ```
  LOAD '$libdir/plugins/auto_explain';
  ```

  For more information on using `auto_explain`, see the [ PostgreSQL documentation](https://www.postgresql.org/docs/current/static/auto-explain.html)\. 

#### PostgreSQL Version 9\.6\.2 on Amazon RDS<a name="PostgreSQL.Concepts.General.version962"></a>

PostgreSQL version 9\.6\.2 contains several new features and bug fixes\. The new version also includes the following extension versions: 
+ PostGIS version 2\.3\.2
+ [ pg\_freespacemap](https://www.postgresql.org/docs/current/static/pgfreespacemap.html) version 1\.1–Provides a way to examine the free space map \(FSM\)\. This extension provides an overloaded function called pg\_freespace\. The functions show the value recorded in the free space map for a given page, or for all pages in the relation\.
+ [pg\_hint\_plan](http://pghintplan.osdn.jp/pg_hint_plan.html) version 1\.1\.3– Provides control of execution plans by using hinting phrases at the beginning of SQL statements\.
+ log\_fdw version 1\.0–Using this extension from Amazon RDS, you can load and query your database engine log from within the database\. For more information, see [Using the log\_fdw Extension](#CHAP_PostgreSQL.Extensions.log_fdw)\.
+ With this version release, you can now edit the `max_worker_processes` parameter in a DB parameter group\. 

PostgreSQL version 9\.6\.2 on Amazon RDS also supports altering enum values\. For more information, see [ALTER ENUM for PostgreSQL](#PostgreSQL.Concepts.General.FeatureSupport.AlterEnum)\.

 For more information on the fixes in 9\.6\.2, see the [PostgreSQL documentation](http://www.postgresql.org/docs/9.6/static/release-9-6-2.html)\. For information on upgrading the engine version for your PostgreSQL DB instance, see [Upgrading a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.Patching)\. 

#### PostgreSQL Version 9\.6\.1 on Amazon RDS<a name="PostgreSQL.Concepts.General.version961"></a>

PostgreSQL version 9\.6\.1 contains several new features and improvements\. For more information about the fixes and improvements in PostgreSQL 9\.6\.1, see the [PostgreSQL documentation](https://www.postgresql.org/docs/9.6/static/release-9-6-1.html)\. For information on upgrading the engine version for your PostgreSQL DB instance, see [Upgrading a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.Patching)\. For information about performing parallel queries and phrase searching using Amazon RDS for PostgreSQL 9\.6\.1, see the [AWS Database Blog](https://aws.amazon.com/blogs/database/performing-parallel-queries-and-phrase-searching-with-amazon-rds-for-postgresql-9-6-1/)\.

PostgreSQL version 9\.6\.1 includes the following changes:
+ **Parallel query execution**: Supports parallel execution of large read\-only queries, allowing sequential scans, hash joins, nested loops, and aggregates to be run in parallel\. By default, parallel query execution is not enabled\. To enable parallel query execution, set the parameter `max_parallel_workers_per_gather` to a value larger than zero\.
+ **Updated postgres\_fdw extension**: Supports remote JOINs, SORTs, UPDATEs, and DELETE operations\.
+ **PL/v8 update**: Provides version 1\.5\.3 of the PL/v8 language\.
+ **PostGIS version update**: Supports POSTGIS="2\.3\.0 r15146" GEOS="3\.5\.0\-CAPI\-1\.9\.0 r4084" PROJ="Rel\. 4\.9\.2, 08 September 2015" GDAL="GDAL 2\.1\.1, released 2016/07/07" LIBXML="2\.9\.1" LIBJSON="0\.12" RASTER
+ **Vacuum improvement**: Avoids scanning pages unnecessarily during vacuum freeze operations\.
+ **Full\-text search support for phrases**: Supports the ability to specify a phrase\-search query in tsquery input using the new operators <\-> and <N>\.
+ **Two new extensions are supported**:
  + `bloom`, an index access method based on [Bloom filters](http://en.wikipedia.org/wiki/Bloom_filter)
  + `pg_visibility`, which provides a means for examining the visibility map and page\-level visibility information of a table\.
+ With the release of version 9\.6\.2, you can now edit the `max_worker_processes` parameter in a PostgreSQL version 9\.6\.1 DB parameter group\.

You can create a new PostgreSQL 9\.6\.1 database instance using the AWS Management Console, AWS CLI, or RDS API\. You can also upgrade an existing PostgreSQL 9\.5 instance to version 9\.6\.1 using major version upgrade\. If you want to upgrade a DB instance from version 9\.3 or 9\.4 to 9\.6, you must perform a point\-and\-click upgrade to the next major version first\. Each upgrade operation involves a short period of unavailability for your DB instance\.

#### PostgreSQL Version 9\.5\.12 on Amazon RDS<a name="PostgreSQL.Concepts.General.version9512"></a>

PostgreSQL version 9\.5\.12 contains several bug fixes for issues in release 9\.5\.10 For more information on the fixes in 9\.5\.12, see the [PostgreSQL documentation](http://www.postgresql.org/docs/9.5/static/release-9-5-12.html)\. For information on upgrading the engine version for your PostgreSQL DB instance, see [Upgrading a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.Patching)\. 

For the complete list of extensions supported by Amazon RDS PostgreSQL, see [Supported PostgreSQL Features and Extensions](#PostgreSQL.Concepts.General.FeaturesExtensions)\.

#### PostgreSQL Version 9\.5\.10 on Amazon RDS<a name="PostgreSQL.Concepts.General.version9510"></a>

PostgreSQL version 9\.5\.10 contains several bug fixes for issues in version 9\.5\.9\. For more information on the fixes in 9\.5\.10, see the [PostgreSQL documentation](http://www.postgresql.org/docs/9.5/static/release-9-5-10.html)\. For information on upgrading the engine version for your PostgreSQL DB instance, see [Upgrading a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.Patching)\. 

#### PostgreSQL Version 9\.5\.9 on Amazon RDS<a name="PostgreSQL.Concepts.General.version959"></a>

PostgreSQL version 9\.5\.9 contains several bug fixes for issues in version 9\.5\.8\. For more information on the fixes in 9\.5\.9, see the [PostgreSQL documentation](http://www.postgresql.org/docs/9.5/static/release-9-5-9.html)\. For information on upgrading the engine version for your PostgreSQL DB instance, see [Upgrading a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.Patching)\. 

#### PostgreSQL Version 9\.5\.7 on Amazon RDS<a name="PostgreSQL.Concepts.General.version957"></a>

PostgreSQL version 9\.5\.7 contains several new features and bug fixes\. This version includes the following features: 
+ Supports the extension `pgaudit` version 1\.0\.5\. This extension provides detailed session and object audit logging\. For more information on using `pgaudit` with Amazon RDS, see [Working with the pgaudit Extension](Appendix.PostgreSQL.CommonDBATasks.md#Appendix.PostgreSQL.CommonDBATasks.pgaudit)\.
+ Supports `wal2json`, an output plugin for logical decoding\.
+ Supports the `auto_explain` module\. You can use this module to log execution plans of slow statements automatically\. The following example shows how to use `auto_explain` from within an Amazon RDS PostgreSQL session\.

  ```
  LOAD '$libdir/plugins/auto_explain';
  ```

  For more information on using `auto_explain`, see the [ PostgreSQL documentation](https://www.postgresql.org/docs/current/static/auto-explain.html)\. 

#### PostgreSQL Version 9\.5\.6 on Amazon RDS<a name="PostgreSQL.Concepts.General.version956"></a>

PostgreSQL version 9\.5\.6 contains several new features and bug fixes\. The new version also includes the following extension versions: 
+ PostGIS version 2\.2\.5
+ [ pg\_freespacemap](https://www.postgresql.org/docs/current/static/pgfreespacemap.html) version 1\.1–Provides a way to examine the free space map \(FSM\)\. This extension provides an overloaded function called pg\_freespace\. This function shows the value recorded in the free space map for a given page, or for all pages in the relation\.
+ [pg\_hint\_plan](http://pghintplan.osdn.jp/pg_hint_plan.html) version 1\.1\.3– Provides control of execution plans by using hinting phrases at the beginning of SQL statements\.

PostgreSQL version 9\.5\.6 on Amazon RDS also supports altering enum values\. For more information, see [ALTER ENUM for PostgreSQL](#PostgreSQL.Concepts.General.FeatureSupport.AlterEnum)\.

 For more information on the fixes in 9\.5\.6, see the [PostgreSQL documentation](http://www.postgresql.org/docs/9.5/static/release-9-5-6.html)\. For information on upgrading the engine version for your PostgreSQL DB instance, see [Upgrading a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.Patching)\. 

#### PostgreSQL Version 9\.5\.4 on Amazon RDS<a name="PostgreSQL.Concepts.General.version954"></a>

PostgreSQL version 9\.5\.4 contains several fixes to issue found in previous versions\. For more information on the fixes in 9\.5\.4, see the [PostgreSQL documentation](http://www.postgresql.org/docs/9.5/static/release-9-5-4.html)\. For information on upgrading the engine version for your PostgreSQL DB instance, see [Upgrading a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.Patching)\. 

Beginning with PostgreSQL version 9\.4, PostgreSQL supports the streaming of WAL changes using logical replication decoding\. Amazon RDS supports logical replication for PostgreSQL version 9\.4\.9 and higher and 9\.5\.4 and higher\. For more information about PostgreSQL logical replication on Amazon RDS, see [Logical Replication for PostgreSQL on Amazon RDS](#PostgreSQL.Concepts.General.FeatureSupport.LogicalReplication)\. 

Beginning with PostgreSQL version 9\.5\.4 for Amazon RDS, the command ALTER USER WITH BYPASSRLS is supported\. 

PostgreSQL versions 9\.4\.9 and later and version 9\.5\.4 and later support event triggers, and Amazon RDS supports event triggers for these versions\. You can use the master user account can be used to create, modify, rename, and delete event triggers\. Event triggers are at the DB instance level, so they can apply to all databases on an instance\. For more information about PostgreSQL event triggers on Amazon RDS, see [Event Triggers for PostgreSQL on Amazon RDS](#PostgreSQL.Concepts.General.FeatureSupport.EventTriggers)\.

#### PostgreSQL Version 9\.5\.2 on Amazon RDS<a name="PostgreSQL.Concepts.General.version952"></a>

PostgreSQL version 9\.5\.2 contains several fixes to issues found in previous versions\. For more information on the features in 9\.5\.2, see the [PostgreSQL documentation](http://www.postgresql.org/docs/9.5/static/release-9-5.html)\. For information on upgrading the engine version for your PostgreSQL DB instance, see [Upgrading a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.Patching)\.

PostgreSQL version 9\.5\.2 doesn't support the db\.m1 or db\.m2 DB instance classes\. If you need to upgrade a DB instance running PostgreSQL version 9\.4 to version 9\.5\.2 to one of these instance classes, you need to scale compute\. To do that, you need a comparable db\.t2 or db\.m3 DB instance class before you can upgrade a DB instance running PostgreSQL version 9\.4 to version 9\.5\.2\. For more information on DB instance classes, see [DB Instance Class](Concepts.DBInstanceClass.md)\.

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
+ Improved visibility of autovacuum sessions by allowing the rds\_superuser account to view autovacuum sessions in pg\_stat\_activity\. For example, you can identify and terminate an autovacuum session that is blocking a command from running, or executing slower than a manually issued vacuum command\.

RDS PostgreSQL version 9\.5\.2 includes the following new extensions:
+ **address\_standardizer** – A single\-line address parser that takes an input address and normalizes it based on a set of rules stored in a table, helper lex, and gaz tables\.
+ [hstore\_plperl](http://www.postgresql.org/docs/current/static/hstore.html) – Provides transforms for the `hstore` type for PL/Perl\. 
+ [tsm\_system\_rows](http://www.postgresql.org/docs/current/static/tsm-system-rows.html) – Provides the table sampling method `SYSTEM_ROWS`, which can be used in the `TABLESAMPLE` clause of a `SELECT` command\.
+ [tsm\_system\_time](http://www.postgresql.org/docs/current/static/tsm-system-time.html) – Provides the table sampling method `SYSTEM_TIME`, which can be used in the `TABLESAMPLE` clause of a `SELECT` command\. 

#### PostgreSQL Version 9\.4\.17 on Amazon RDS<a name="PostgreSQL.Concepts.General.version9417"></a>

PostgreSQL version 9\.4\.17 contains several bug fixes for issues in release 9\.4\.15\. For more information on the fixes in 9\.4\.17, see the [PostgreSQL documentation](http://www.postgresql.org/docs/9.4/static/release-9-4-17.html)\. For information on upgrading the engine version for your PostgreSQL DB instance, see [Upgrading a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.Patching)\. 

For the complete list of extensions supported by Amazon RDS PostgreSQL, see [Supported PostgreSQL Features and Extensions](#PostgreSQL.Concepts.General.FeaturesExtensions)\.

#### PostgreSQL Version 9\.4\.15 on Amazon RDS<a name="PostgreSQL.Concepts.General.version9415"></a>

PostgreSQL version 9\.4\.15 contains several bug fixes for issues in release 9\.4\.14\. For more information on the fixes in 9\.4\.15, see the [PostgreSQL documentation](http://www.postgresql.org/docs/9.5/static/release-9-4-15.html)\. For information on upgrading the engine version for your PostgreSQL DB instance, see [Upgrading a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.Patching)\. 

#### PostgreSQL Version 9\.4\.14 on Amazon RDS<a name="PostgreSQL.Concepts.General.version9414"></a>

PostgreSQL version 9\.4\.14 contains several bug fixes for issues in release 9\.4\.12\. For more information on the fixes in 9\.4\.14, see the [PostgreSQL documentation](http://www.postgresql.org/docs/9.5/static/release-9-4-14.html)\. For information on upgrading the engine version for your PostgreSQL DB instance, see [Upgrading a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.Patching)\. 

#### PostgreSQL Version 9\.4\.12 on Amazon RDS<a name="PostgreSQL.Concepts.General.version9412"></a>

PostgreSQL version 9\.4\.12 contains several fixes to issue found in previous versions\.

 For more information on the fixes in 9\.4\.12, see the [PostgreSQL documentation](http://www.postgresql.org/docs/9.4/static/release-9-4-12.html)\. For information on upgrading the engine version for your PostgreSQL DB instance, see [Upgrading a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.Patching)\.

#### PostgreSQL Version 9\.4\.11 on Amazon RDS<a name="PostgreSQL.Concepts.General.version9411"></a>

PostgreSQL version 9\.4\.11 contains several fixes to issue found in previous versions\.

 For more information on the fixes in 9\.4\.11, see the [PostgreSQL documentation](http://www.postgresql.org/docs/9.4/static/release-9-4-11.html)\. For information on upgrading the engine version for your PostgreSQL DB instance, see [Upgrading a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.Patching)\.

Beginning with PostgreSQL version 9\.4, PostgreSQL supports the streaming of WAL changes using logical replication decoding\. Amazon RDS supports logical replication for PostgreSQL version 9\.4\.9 and higher and 9\.5\.4 and higher\. For more information about PostgreSQL logical replication on Amazon RDS, see [Logical Replication for PostgreSQL on Amazon RDS](#PostgreSQL.Concepts.General.FeatureSupport.LogicalReplication)\. 

PostgreSQL versions 9\.4\.9 and later and version 9\.5\.4 and later support event triggers, and Amazon RDS supports event triggers for these versions\. The master user account can be used to create, modify, rename, and delete event triggers\. Event triggers are at the DB instance level, so they can apply to all databases on an instance\. For more information about PostgreSQL event triggers on Amazon RDS, see [Event Triggers for PostgreSQL on Amazon RDS](#PostgreSQL.Concepts.General.FeatureSupport.EventTriggers)\.

#### PostgreSQL Version 9\.4\.9 on Amazon RDS<a name="PostgreSQL.Concepts.General.version949"></a>

PostgreSQL version 9\.4\.9 contains several fixes to issue found in previous versions\. For more information on the fixes in 9\.4\.9, see the [PostgreSQL documentation](http://www.postgresql.org/docs/9.4/static/release-9-4-9.html)\. For information on upgrading the engine version for your PostgreSQL DB instance, see [Upgrading a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.Patching)\.

Beginning with PostgreSQL version 9\.4, PostgreSQL supports the streaming of WAL changes using logical replication decoding\. Amazon RDS supports logical replication for PostgreSQL version 9\.4\.9 and higher and 9\.5\.4 and higher\. For more information about PostgreSQL logical replication on Amazon RDS, see [Logical Replication for PostgreSQL on Amazon RDS](#PostgreSQL.Concepts.General.FeatureSupport.LogicalReplication)\. 

PostgreSQL versions 9\.4\.9 and later and version 9\.5\.4 and later support event triggers, and Amazon RDS supports event triggers for these versions\. The master user account can be used to create, modify, rename, and delete event triggers\. Event triggers are at the DB instance level, so they can apply to all databases on an instance\. For more information about PostgreSQL event triggers on Amazon RDS, see [Event Triggers for PostgreSQL on Amazon RDS](#PostgreSQL.Concepts.General.FeatureSupport.EventTriggers)\.

#### PostgreSQL Version 9\.4\.7 on Amazon RDS<a name="PostgreSQL.Concepts.General.version947"></a>

PostgreSQL version 9\.4\.7 contains several fixes to issue found in previous versions\. For more information on the fixes in 9\.4\.7, see the [PostgreSQL documentation](http://www.postgresql.org/docs/9.4/static/release-9-4-7.html)\. For information on upgrading the engine version for your PostgreSQL DB instance, see [Upgrading a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.Patching)\.

PostgreSQL version 9\.4\.7 includes improved visibility of autovacuum sessions by allowing the rds\_superuser account to view autovacuum sessions in pg\_stat\_activity\. For example, you can identify and terminate an autovacuum session that is blocking a command from running, or executing slower than a manually issued vacuum command\.

#### PostgreSQL Version 9\.3\.22 on Amazon RDS<a name="PostgreSQL.Concepts.General.version9322"></a>

PostgreSQL version 9\.3\.22 contains several bug fixes for issues in release 9\.3\.20\. For more information on the fixes in 9\.3\.22, see the [PostgreSQL documentation](http://www.postgresql.org/docs/9.3/static/release-9-3-22.html)\. For information on upgrading the engine version for your PostgreSQL DB instance, see [Upgrading a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.Patching)\. 

For the complete list of extensions supported by Amazon RDS PostgreSQL, see [Supported PostgreSQL Features and Extensions](#PostgreSQL.Concepts.General.FeaturesExtensions)\.

#### PostgreSQL Version 9\.3\.20 on Amazon RDS<a name="PostgreSQL.Concepts.General.version9320"></a>

PostgreSQL version 9\.3\.20 contains several bug fixes for issues in version 9\.3\.19\. For more information on the fixes in 9\.3\.20, see the [PostgreSQL documentation](http://www.postgresql.org/docs/9.3/static/release-9-3-20.html)\. For information on upgrading the engine version for your PostgreSQL DB instance, see [Upgrading a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.Patching)\. 

#### PostgreSQL Version 9\.3\.19 on Amazon RDS<a name="PostgreSQL.Concepts.General.version9319"></a>

PostgreSQL version 9\.3\.19 contains several bug fixes for issues in version 9\.3\.18\. For more information on the fixes in 9\.3\.19, see the [PostgreSQL documentation](http://www.postgresql.org/docs/9.3/static/release-9-3-19.html)\. For information on upgrading the engine version for your PostgreSQL DB instance, see [Upgrading a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.Patching)\. 

#### PostgreSQL Version 9\.3\.17 on Amazon RDS<a name="PostgreSQL.Concepts.General.version9317"></a>

PostgreSQL version 9\.3\.17 contains several fixes for bugs found in previous versions\. This version contains the same extension components as version 9\.3\.16\. For a list of fixes in version 9\.3\.17, see the [PostgreSQL documentation](http://www.postgresql.org/docs/9.3/static/release-9-3-17.html)\. For information on upgrading the engine version for your PostgreSQL DB instance, see [Upgrading a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.Patching)\.

#### PostgreSQL Version 9\.3\.16 on Amazon RDS<a name="PostgreSQL.Concepts.General.version9316"></a>

PostgreSQL version 9\.3\.16 contains several fixes for bugs found in previous versions\. This version contains the same extension components as version 9\.3\.14\. For a list of fixes in version 9\.3\.16, see the [PostgreSQL documentation](http://www.postgresql.org/docs/9.3/static/release-9-3-16.html)\. For information on upgrading the engine version for your PostgreSQL DB instance, see [Upgrading a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.Patching)\.

#### PostgreSQL Version 9\.3\.14 on Amazon RDS<a name="PostgreSQL.Concepts.General.version9314"></a>

PostgreSQL version 9\.3\.14 contains several fixes for bugs found in previous versions\. For a list of fixes in version 9\.3\.14, see the [PostgreSQL documentation](http://www.postgresql.org/docs/9.3/static/release-9-3-14.html)\. For information on upgrading the engine version for your PostgreSQL DB instance, see [Upgrading a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.Patching)\.

#### PostgreSQL Version 9\.3\.12 on Amazon RDS<a name="PostgreSQL.Concepts.General.version9312"></a>

PostgreSQL version 9\.3\.12 contains several fixes for bugs found in previous versions\. For a list of fixes in version 9\.3\.12, see the [PostgreSQL documentation](http://www.postgresql.org/docs/9.4/static/release-9-3-12.html)\. For information on upgrading the engine version for your PostgreSQL DB instance, see [Upgrading a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.Patching)\.

PostgreSQL version 9\.3\.12 includes improved visibility of autovacuum sessions by allowing the rds\_superuser account to view autovacuum sessions in pg\_stat\_activity\. For example, you can identify and terminate an autovacuum session that is blocking a command from running, or executing slower than a manually issued vacuum command\.

### Supported PostgreSQL Features and Extensions<a name="PostgreSQL.Concepts.General.FeaturesExtensions"></a>

Amazon RDS supports many of the most common PostgreSQL extensions and features\.

**Topics**
+ [PostgreSQL Extensions and Modules Supported on Amazon RDS](#PostgreSQL.Concepts.General.FeatureSupport.Extensions)
+ [Upgrade PL/v8](#PostgreSQL.Concepts.General.UpgradingPLv8)
+ [Supported PostgreSQL Features](#PostgreSQL.Concepts.General.FeatureSupport)
+ [Limits for PostgreSQL DB Instances](#PostgreSQL.Concepts.General.Limits)
+ [Upgrading a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.Patching)
+ [Using SSL with a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.SSL)

#### PostgreSQL Extensions and Modules Supported on Amazon RDS<a name="PostgreSQL.Concepts.General.FeatureSupport.Extensions"></a>

PostgreSQL supports many PostgreSQL extensions and modules\. Extensions and modules expand on the functionality provided by the PostgreSQL engine\. The following sections show the extensions and modules supported by Amazon RDS for the major PostgreSQL versions\.

**Topics**
+ [PostgreSQL Version 10\.x Extensions and Modules Supported on Amazon RDS](#PostgreSQL.Concepts.General.FeatureSupport.Extensions.101x)
+ [PostgreSQL Version 9\.6\.x Extensions and Modules Supported on Amazon RDS](#PostgreSQL.Concepts.General.FeatureSupport.Extensions.96x)
+ [PostgreSQL Version 9\.5\.x Extensions Supported on Amazon RDS](#PostgreSQL.Concepts.General.FeatureSupport.Extensions.95x)
+ [PostgreSQL Version 9\.4\.x Extensions and Modules Supported on Amazon RDS](#PostgreSQL.Concepts.General.FeatureSupport.Extensions.94x)
+ [PostgreSQL Version 9\.3\.x Extensions Supported on Amazon RDS](#PostgreSQL.Concepts.General.FeatureSupport.Extensions.93x)
+ [PostgreSQL Extension Support for PostGIS on Amazon RDS](#CHAP_PostgreSQL.Extensions.SupportedPerVersion)
+ [Using the log\_fdw Extension](#CHAP_PostgreSQL.Extensions.log_fdw)

You can find a list of extensions supported by Amazon RDS in the default DB parameter group for that PostgreSQL version\. You can also see the current extensions list using `psql` by showing the `rds.extensions` parameter as in the following example\.

```
SHOW rds.extensions; 
```

**Note**  
Parameters added in a minor version release might display inaccurately when using the `rds.extensions` parameter in `psql`\. 

##### PostgreSQL Version 10\.x Extensions and Modules Supported on Amazon RDS<a name="PostgreSQL.Concepts.General.FeatureSupport.Extensions.101x"></a>

The following tables show PostgreSQL extensions and modules for PostgreSQL version 10 that are currently supported by PostgreSQL on Amazon RDS\. "N/A" indicates that the extension or module is not available for that PostgreSQL version\. For more information on PostgreSQL extensions, see [Packaging Related Objects into an Extension](https://www.postgresql.org/docs/9.6/static/extend-extensions.html)\. 


| Extension | 10\.1 | 10\.3 | 
| --- | --- | --- | 
| [ address\_standardizer](http://postgis.net/docs/Address_Standardizer.html) | 2\.4\.2 | 2\.4\.2 | 
| [ address\_standardizer\_data\_us](http://postgis.net/docs/Address_Standardizer.html) | 2\.4\.2 | 2\.4\.2 | 
| [ bloom](https://www.postgresql.org/docs/10/static/bloom.html) | 1\.0 | 1\.0 | 
| [btree\_gin](http://www.postgresql.org/docs/10/static/btree-gin.html) | 1\.2 | 1\.2 | 
| [btree\_gist](http://www.postgresql.org/docs/10/static/btree-gist.html) | 1\.5 | 1\.5 | 
| [chkpass](http://www.postgresql.org/docs/10/static/chkpass.html) | 1\.0 | 1\.0 | 
| [citext ](http://www.postgresql.org/docs/10/static/citext.html) | 1\.4 | 1\.4 | 
| [cube ](http://www.postgresql.org/docs/10/static/cube.html) | 1\.2 | 1\.2 | 
| [ dblink](http://www.postgresql.org/docs/10/static/dblink.html) | 1\.2 | 1\.2 | 
| [ dict\_int ](http://www.postgresql.org/docs/10/static/dict-int.html) | 1\.0 | 1\.0 | 
| [ dict\_xsyn](https://www.postgresql.org/docs/10/static/dict-xsyn.html) | 1\.0 | 1\.0 | 
| [earthdistance](http://www.postgresql.org/docs/10/static/earthdistance.html) | 1\.1 | 1\.1 | 
| [fuzzystrmatch](http://www.postgresql.org/docs/10/static/fuzzystrmatch.html) | 1\.1 | 1\.1 | 
| [hstore](http://www.postgresql.org/docs/10/static/hstore.html) | 1\.4 | 1\.4 | 
| [ hstore\_plperl](https://www.postgresql.org/docs/10/static/hstore.html) | 1\.0 | 1\.0 | 
| [ intagg](http://www.postgresql.org/docs/10/static/intagg.html) | 1\.1 | 1\.1 | 
| [ intarray](http://www.postgresql.org/docs/10/static/intarray.html) | 1\.2 | 1\.2 | 
| [ip4r](http://www.postgresql.org/ftp/projects/pgFoundry/ip4r/) | 2\.0 | 2\.0 | 
| [isn ](http://www.postgresql.org/docs/10/static/isn.html) | 1\.1 | 1\.1 | 
| log\_fdw—see [Using the log\_fdw Extension](#CHAP_PostgreSQL.Extensions.log_fdw) | 1\.0 | 1\.0 | 
| [ltree ](http://www.postgresql.org/docs/10/static/ltree.html) | 1\.1 | 1\.1 | 
| [orafce](https://github.com/orafce/orafce) | 3\.6\.1 | 3\.6\.1 | 
| [pgaudit](https://github.com/pgaudit/pgaudit/blob/master/README.md) | 1\.2\.0 | 1\.2\.0 | 
| [ pg\_buffercache](http://www.postgresql.org/docs/10/static/pgbuffercache.html) | 1\.3 | 1\.3 | 
| [pg\_freespacemap](https://www.postgresql.org/docs/10/static/pgfreespacemap.html) | 1\.2 | 1\.2 | 
| [pg\_hint\_plan](http://pghintplan.osdn.jp/pg_hint_plan.html) | 1\.3\.0 | 1\.3\.0 | 
| [ pg\_prewarm](https://www.postgresql.org/docs/10/static/pgprewarm.html) | 1\.1 | 1\.1 | 
| [ pg\_repack ](http://reorg.github.io/pg_repack/) | 1\.4\.2 | 1\.4\.2 | 
| [pg\_stat\_statements](http://www.postgresql.org/docs/10/static/pgstatstatements.html) | 1\.5 | 1\.5 | 
| [pg\_trgm](http://www.postgresql.org/docs/10/static/pgtrgm.html) | 1\.3 | 1\.3 | 
| [ pg\_visibility](https://www.postgresql.org/docs/10/static/pgvisibility.html) | 1\.2 | 1\.2 | 
| [pgcrypto](http://www.postgresql.org/docs/10/static/pgcrypto.html) | 1\.3 | 1\.3 | 
| [pgrowlocks](http://www.postgresql.org/docs/10/static/pgrowlocks.html) | 1\.2 | 1\.2 | 
| [pgrouting](http://docs.pgrouting.org/2.3/en/doc/index.html) | 2\.5\.2 | 2\.5\.2 | 
| [pgstattuple](http://www.postgresql.org/docs/10/static/pgstattuple.html) | 1\.5 | 1\.5 | 
| plcoffee | 2\.1\.0 | 2\.1\.0 | 
| plls | 2\.1\.0 | 2\.1\.0 | 
| [ plperl](https://www.postgresql.org/docs/10/static/plperl.html) | 1\.0 | 1\.0 | 
| [ plpgsql](https://www.postgresql.org/docs/10/static/plpgsql.html) | 1\.0 | 1\.0 | 
| [ pltcl](https://www.postgresql.org/docs/10/static/pltcl-overview.html) | 1\.0 | 1\.0 | 
| [plv8](https://github.com/plv8) | 2\.1\.0 | 2\.1\.0 | 
| [PostGIS](http://www.postgis.net/) | 2\.4\.2 | 2\.4\.2 | 
| [ postgis\_tiger\_geocoder](http://postgis.net/docs/Geocode.html) | 2\.4\.2 | 2\.4\.2 | 
| [ postgis\_topology](http://postgis.net/docs/manual-dev/Topology.html) | 2\.4\.2 | 2\.4\.2 | 
| [postgres\_fdw](http://www.postgresql.org/docs/10/static/postgres-fdw.html) | 1\.0 | 1\.0 | 
| [ postgresql\-hll](https://github.com/citusdata/postgresql-hll/releases/tag/v2.10.2) | 2\.10\.2 | 2\.10\.2 | 
|  [ prefix](http://pgfoundry.org/projects/prefix) | 1\.2\.0 | 1\.2\.0 | 
| [sslinfo](http://www.postgresql.org/docs/10/static/sslinfo.html) | 1\.2 | 1\.2 | 
| [tablefunc](http://www.postgresql.org/docs/10/static/tablefunc.html) | 1\.0 | 1\.0 | 
| [ test\_parser](https://www.postgresql.org/docs/9.4/static/test-parser.html) | 1\.0 | 1\.0 | 
| [tsearch2](http://www.postgresql.org/docs/9.6/static/tsearch2.html) \(deprecated in version 10\) | 1\.0 | 1\.0 | 
| [ tsm\_system\_rows](https://www.postgresql.org/docs/10/static/tsm-system-rows.html) | 1\.0 | 1\.0 | 
| [ tsm\_system\_time](https://www.postgresql.org/docs/10/static/tsm-system-time.html) | 1\.0 | 1\.0 | 
|  [unaccent ](http://www.postgresql.org/docs/10/static/unaccent.html) | 1\.1 | 1\.1 | 
| [uuid\-ossp](http://www.postgresql.org/docs/10/static/uuid-ossp.html) | 1\.1 | 1\.1 | 

The `tsearch2` extension is deprecated in version 10\. The PostgreSQL team plans to remove `tsearch2` from the next major release of PostgreSQL\.

The following modules are supported as shown for versions of PostgreSQL 10\.


| Module | Version 10\.1 | 10\.3 | 
| --- | --- | --- | 
| amcheck | Not Supported | Supported | 
| auto\_explain | Supported | Supported | 
| decoder\_raw | Supported | Supported | 
| ICU | Version 60\.2 supported | Version 60\.2 supported | 
| test\_decoder | Supported | Supported | 
| wal2json | Supported | Supported | 

##### PostgreSQL Version 9\.6\.x Extensions and Modules Supported on Amazon RDS<a name="PostgreSQL.Concepts.General.FeatureSupport.Extensions.96x"></a>

The following tables show PostgreSQL extensions and modules for PostgreSQL version 9\.6\.x that are currently supported by PostgreSQL on Amazon RDS\. "N/A" indicates that the extension or module is not available for that PostgreSQL version\. For more information on PostgreSQL extensions, see [Packaging Related Objects into an Extension](https://www.postgresql.org/docs/9.6/static/extend-extensions.html)\. 


| Extension | 9\.6\.1 | 9\.6\.2 | 9\.6\.3 | 9\.6\.5 | 9\.6\.6 | 9\.6\.8 | 
| --- | --- | --- | --- | --- | --- | --- | 
| [ address\_standardizer](http://postgis.net/docs/Address_Standardizer.html) | 2\.3\.0 | 2\.3\.2 | 2\.3\.2 | 2\.3\.2 | 2\.3\.4 | 2\.3\.4 | 
| [ address\_standardizer\_data\_us](http://postgis.net/docs/Address_Standardizer.html) | 2\.3\.0 | 2\.3\.2 | 2\.3\.2 | 2\.3\.2 | 2\.3\.4 | 2\.3\.4 | 
| [ bloom](https://www.postgresql.org/docs/9.6/static/bloom.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [btree\_gin](http://www.postgresql.org/docs/9.6/static/btree-gin.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [btree\_gist](http://www.postgresql.org/docs/9.6/static/btree-gist.html) | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 
| [chkpass](http://www.postgresql.org/docs/9.6/static/chkpass.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [citext ](http://www.postgresql.org/docs/9.6/static/citext.html) | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 
| [cube ](http://www.postgresql.org/docs/9.6/static/cube.html) | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 
| [ dblink](http://www.postgresql.org/docs/9.6/static/dblink.html) | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 
| [ dict\_int ](http://www.postgresql.org/docs/9.6/static/dict-int.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ dict\_xsyn](https://www.postgresql.org/docs/9.6/static/dict-xsyn.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [earthdistance](http://www.postgresql.org/docs/9.6/static/earthdistance.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [fuzzystrmatch](http://www.postgresql.org/docs/9.6/static/fuzzystrmatch.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [hstore](http://www.postgresql.org/docs/9.6/static/hstore.html) | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 
| [ hstore\_plperl](https://www.postgresql.org/docs/9.6/static/hstore.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ intagg](http://www.postgresql.org/docs/9.6/static/intagg.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [ intarray](http://www.postgresql.org/docs/9.6/static/intarray.html) | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 
| [ip4r](http://www.postgresql.org/ftp/projects/pgFoundry/ip4r/) | 2\.0 | 2\.0 | 2\.0 | 2\.0 | 2\.0 | 2\.0 | 
| [isn ](http://www.postgresql.org/docs/9.6/static/isn.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| log\_fdw—see [Using the log\_fdw Extension](#CHAP_PostgreSQL.Extensions.log_fdw) |  N/A | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ltree ](http://www.postgresql.org/docs/9.6/static/ltree.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [orafce](https://github.com/orafce/orafce) |  N/A | N/A | N/A | N/A | 3\.6\.1 | 3\.6\.1 | 
| [pgaudit](https://github.com/pgaudit/pgaudit/blob/master/README.md) | N/A | N/A | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [ pg\_buffercache](http://www.postgresql.org/docs/9.6/static/pgbuffercache.html) | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 
| [pg\_freespacemap](https://www.postgresql.org/docs/current/static/pgfreespacemap.html) |  N/A | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [pg\_hint\_plan](http://pghintplan.osdn.jp/pg_hint_plan.html) |  N/A | 1\.1\.3 | 1\.1\.3 | 1\.1\.3 | 1\.1\.3 | 1\.2\.2 | 
| [ pg\_prewarm](https://www.postgresql.org/docs/9.6/static/pgprewarm.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [ pg\_repack ](http://reorg.github.io/pg_repack/) | N/A | N/A | 1\.4\.0 | 1\.4\.1 | 1\.4\.2 | 1\.4\.2 | 
| [pg\_stat\_statements](http://www.postgresql.org/docs/9.6/static/pgstatstatements.html) | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 
| [pg\_trgm](http://www.postgresql.org/docs/9.6/static/pgtrgm.html) | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 
| [ pg\_visibility](https://www.postgresql.org/docs/9.6/static/pgvisibility.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [pgcrypto](http://www.postgresql.org/docs/9.6/static/pgcrypto.html) | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 
| [pgrowlocks](http://www.postgresql.org/docs/9.6/static/pgrowlocks.html) | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 
| [pgrouting](http://docs.pgrouting.org/2.3/en/doc/index.html) | N/A | N/A | N/A | 2\.3\.2 | 2\.4\.2 | 2\.4\.2 | 
| [pgstattuple](http://www.postgresql.org/docs/9.6/static/pgstattuple.html) | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 1\.4 | 
| plcoffee | 1\.5\.3 | 1\.5\.3 | 1\.5\.3 | 1\.5\.3 | 1\.5\.3 | 1\.5\.3 | 
| plls | 1\.5\.3 | 1\.5\.3 | 1\.5\.3 | 1\.5\.3 | 1\.5\.3 | 1\.5\.3 | 
| [ plperl](https://www.postgresql.org/docs/current/static/plperl.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ plpgsql](https://www.postgresql.org/docs/current/static/plpgsql.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ pltcl](https://www.postgresql.org/docs/current/static/pltcl-overview.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [plv8](https://github.com/plv8) | 1\.5\.3 | 1\.5\.3 | 1\.5\.3 | 1\.5\.3 | 1\.5\.3 | 2\.1\.0 | 
| [PostGIS](http://www.postgis.net/) | 2\.3\.0 | 2\.3\.2 | 2\.3\.2 | 2\.3\.2 | 2\.3\.4 | 2\.3\.4 | 
| [ postgis\_tiger\_geocoder](http://postgis.net/docs/Geocode.html) | 2\.3\.0 | 2\.3\.2 | 2\.3\.2 | 2\.3\.2 | 2\.3\.4 | 2\.3\.4 | 
| [ postgis\_topology](http://postgis.net/docs/manual-dev/Topology.html) | 2\.3\.0 | 2\.3\.2 | 2\.3\.2 | 2\.3\.2 | 2\.3\.4 | 2\.3\.4 | 
| [postgres\_fdw](http://www.postgresql.org/docs/9.6/static/postgres-fdw.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ postgresql\-hll](https://github.com/citusdata/postgresql-hll/releases/tag/v2.10.2) | N/A | N/A | N/A | 2\.10\.2 | 2\.10\.2 | 2\.10\.2 | 
|  [ prefix](http://pgfoundry.org/projects/prefix) | N/A | N/A | N/A | N/A | 1\.2\.6 | 1\.2\.6 | 
| [sslinfo](http://www.postgresql.org/docs/9.6/static/sslinfo.html) | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 
| [tablefunc](http://www.postgresql.org/docs/9.6/static/tablefunc.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ test\_parser](https://www.postgresql.org/docs/9.4/static/test-parser.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [tsearch2](http://www.postgresql.org/docs/9.6/static/tsearch2.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ tsm\_system\_rows](https://www.postgresql.org/docs/current/static/tsm-system-rows.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ tsm\_system\_time](https://www.postgresql.org/docs/current/static/tsm-system-time.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
|  [unaccent ](http://www.postgresql.org/docs/9.6/static/unaccent.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [uuid\-ossp](http://www.postgresql.org/docs/9.6/static/uuid-ossp.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 

The following modules are supported as shown for versions of PostgreSQL 9\.6\.


| Module | 9\.6\.1 | 9\.6\.2 | 9\.6\.3 | 9\.6\.5 | 9\.6\.8 | 
| --- | --- | --- | --- | --- | --- | 
| auto\_explain | N/A | N/A | Supported | Supported | Supported | 
| decoder\_raw | N/A | N/A | N/A | Supported | Supported | 
| test\_decoder | Supported | Supported | Supported | Supported | Supported | 
| wal2json | N/A | N/a | Supported | Supported | Supported | 

##### PostgreSQL Version 9\.5\.x Extensions Supported on Amazon RDS<a name="PostgreSQL.Concepts.General.FeatureSupport.Extensions.95x"></a>

The following tables show PostgreSQL extensions and modules for PostgreSQL version 9\.5\.x that are currently supported by PostgreSQL on Amazon RDS\. "N/A" indicates that the extension or module is not available for that PostgreSQL version\. For more information on PostgreSQL extensions, see [Packaging Related Objects into an Extension](https://www.postgresql.org/docs/9.6/static/extend-extensions.html)\.


| Extension | 9\.5\.2 | 9\.5\.4 | 9\.5\.6 | 9\.5\.7 | 9\.5\.9 | 9\.5\.10 | 9\.5\.12 | 
| --- | --- | --- | --- | --- | --- | --- | --- | 
| [ address\_standardizer](http://postgis.net/docs/Address_Standardizer.html) | 2\.2\.2 | 2\.2\.2 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 
| [ address\_standardizer\_data\_us](http://postgis.net/docs/Address_Standardizer.html) | 2\.2\.2 | 2\.2\.2 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 
| [ bloom](https://www.postgresql.org/docs/9.6/static/bloom.html) | N/A | N/A | N/A | N/A | N/A | N/A | N/A | 
| [btree\_gin](http://www.postgresql.org/docs/9.6/static/btree-gin.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [btree\_gist](http://www.postgresql.org/docs/9.6/static/btree-gist.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [chkpass](http://www.postgresql.org/docs/9.6/static/chkpass.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [citext ](http://www.postgresql.org/docs/9.6/static/citext.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [cube ](http://www.postgresql.org/docs/9.6/static/cube.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ dblink](http://www.postgresql.org/docs/9.6/static/dblink.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [ dict\_int ](http://www.postgresql.org/docs/9.6/static/dict-int.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ dict\_xsyn](https://www.postgresql.org/docs/9.6/static/dict-xsyn.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [earthdistance](http://www.postgresql.org/docs/9.6/static/earthdistance.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [fuzzystrmatch](http://www.postgresql.org/docs/9.6/static/fuzzystrmatch.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [hstore](http://www.postgresql.org/docs/9.6/static/hstore.html) | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 
| [ hstore\_plperl](https://www.postgresql.org/docs/9.6/static/hstore.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ intagg](http://www.postgresql.org/docs/9.6/static/intagg.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ intarray](http://www.postgresql.org/docs/9.6/static/intarray.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ip4r](http://www.postgresql.org/ftp/projects/pgFoundry/ip4r/) | 2\.0 | 2\.0 | 2\.0 | 2\.0 | 2\.0 | 2\.0 | 2\.0 | 
| [isn ](http://www.postgresql.org/docs/9.6/static/isn.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| log\_fdw—see [Using the log\_fdw Extension](#CHAP_PostgreSQL.Extensions.log_fdw) | N/A | N/A | N/A | N/A | N/A | N/A | N/A | 
| [ltree ](http://www.postgresql.org/docs/9.6/static/ltree.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ pgaudit](http://www.postgresql.org/docs/9.6/static/pgbuffercache.html) | N/A | N/A | N/A | 1\.0\.5 | 1\.0\.5 | 1\.0\.5 | 1\.0 | 
| [ pg\_buffercache](http://www.postgresql.org/docs/9.6/static/pgbuffercache.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [pg\_freespacemap](https://www.postgresql.org/docs/current/static/pgfreespacemap.html) |  N/A |  N/A | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [pg\_hint\_plan](http://pghintplan.osdn.jp/pg_hint_plan.html) |  N/A |  N/A | 1\.1\.3 | 1\.1\.3 | 1\.1\.3 | 1\.1\.3 | 1\.1\.3 | 
| [ pg\_prewarm](https://www.postgresql.org/docs/9.6/static/pgprewarm.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [pg\_stat\_statements](http://www.postgresql.org/docs/9.6/static/pgstatstatements.html) | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 
| [pg\_trgm](http://www.postgresql.org/docs/9.6/static/pgtrgm.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [ pg\_visibility](https://www.postgresql.org/docs/9.6/static/pgvisibility.html) |  N/A |  N/A |  N/A |  N/A |  N/A |  N/A | N/A | 
| [pgcrypto](http://www.postgresql.org/docs/9.6/static/pgcrypto.html) | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 
| [pgrowlocks](http://www.postgresql.org/docs/9.6/static/pgrowlocks.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [pgstattuple](http://www.postgresql.org/docs/9.6/static/pgstattuple.html) | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 
| plcoffee | 1\.4\.4 | 1\.4\.4 | 1\.4\.4 | 1\.4\.4 | 1\.4\.4 | 1\.4\.4 | 2\.1\.0 | 
| plls | 1\.4\.4 | 1\.4\.4 | 1\.4\.4 | 1\.4\.4 | 1\.4\.4 | 1\.4\.4 | 2\.1\.0 | 
| [ plperl](https://www.postgresql.org/docs/current/static/plperl.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ plpgsql](https://www.postgresql.org/docs/current/static/plpgsql.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ pltcl](https://www.postgresql.org/docs/current/static/pltcl-overview.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [plv8](https://github.com/plv8) | 1\.4\.4 | 1\.4\.4 | 1\.4\.4 | 1\.4\.4 | 1\.4\.4 | 1\.4\.4 | 2\.1\.0 | 
| [PostGIS](http://www.postgis.net/) | 2\.2\.2 | 2\.2\.2 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 
| [ postgis\_tiger\_geocoder](http://postgis.net/docs/Geocode.html) | 2\.2\.2 | 2\.2\.2 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 
| [ postgis\_topology](http://postgis.net/docs/manual-dev/Topology.html) | 2\.2\.2 | 2\.2\.2 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 2\.2\.5 | 
| [postgres\_fdw](http://www.postgresql.org/docs/9.6/static/postgres-fdw.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [sslinfo](http://www.postgresql.org/docs/9.6/static/sslinfo.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [tablefunc](http://www.postgresql.org/docs/9.6/static/tablefunc.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ test\_parser](https://www.postgresql.org/docs/9.4/static/test-parser.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [tsearch2](http://www.postgresql.org/docs/9.6/static/tsearch2.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ tsm\_system\_rows](https://www.postgresql.org/docs/current/static/tsm-system-rows.html) |  N/A |  N/A | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ tsm\_system\_time](https://www.postgresql.org/docs/current/static/tsm-system-time.html) |  N/A |  N/A | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
|  [unaccent ](http://www.postgresql.org/docs/9.6/static/unaccent.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [uuid\-ossp](http://www.postgresql.org/docs/9.6/static/uuid-ossp.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 

The following modules are supported as shown for versions of PostgreSQL 9\.5\.


| Module | 9\.5\.2 | 9\.5\.4 | 9\.5\.6 | 9\.5\.7 | 9\.5\.9 | 9\.5\.12 | 
| --- | --- | --- | --- | --- | --- | --- | 
| auto\_explain | N/A | N/A | N/A | Supported | Supported | Supported | 
| test\_decoder | N/A | N/A | Supported | Supported | Supported | Supported | 
| wal2json | N/A | N/a | N/A | Supported | Supported | Supported | 

##### PostgreSQL Version 9\.4\.x Extensions and Modules Supported on Amazon RDS<a name="PostgreSQL.Concepts.General.FeatureSupport.Extensions.94x"></a>

The following tables show the PostgreSQL extensions and modules for PostgreSQL version 9\.4\.x that are currently supported by PostgreSQL on Amazon RDS\. "N/A" indicates that the extension or module is not available for that PostgreSQL version\. For more information on PostgreSQL extensions, see [Packaging Related Objects into an Extension](https://www.postgresql.org/docs/9.4/static/extend-extensions.html)\. 


| Extension | 9\.4\.7 | 9\.4\.9 | 9\.4\.11 | 9\.4\.12 | 9\.4\.14 | 9\.4\.15 | 9\.4\.17 | 
| --- | --- | --- | --- | --- | --- | --- | --- | 
| [ address\_standardizer](http://postgis.net/docs/Address_Standardizer.html) |  N/A |  N/A |  N/A |  N/A |  N/A |  N/A |  N/A | 
| [ address\_standardizer\_data\_us](http://postgis.net/docs/Address_Standardizer.html) |  N/A |  N/A |  N/A |  N/A |  N/A |  N/A |  N/A | 
| [ bloom](https://www.postgresql.org/docs/9.6/static/bloom.html) |  N/A |  N/A |  N/A |  N/A |  N/A |  N/A |  N/A | 
| [btree\_gin](http://www.postgresql.org/docs/9.6/static/btree-gin.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [btree\_gist](http://www.postgresql.org/docs/9.6/static/btree-gist.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [chkpass](http://www.postgresql.org/docs/9.6/static/chkpass.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [citext ](http://www.postgresql.org/docs/9.6/static/citext.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [cube ](http://www.postgresql.org/docs/9.6/static/cube.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ dblink](http://www.postgresql.org/docs/9.6/static/dblink.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [ dict\_int ](http://www.postgresql.org/docs/9.6/static/dict-int.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ dict\_xsyn](https://www.postgresql.org/docs/9.6/static/dict-xsyn.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [earthdistance](http://www.postgresql.org/docs/9.6/static/earthdistance.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [fuzzystrmatch](http://www.postgresql.org/docs/9.6/static/fuzzystrmatch.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [hstore](http://www.postgresql.org/docs/9.6/static/hstore.html) | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 1\.3 | 
| [ hstore\_plperl](https://www.postgresql.org/docs/9.6/static/hstore.html) |  N/A |  N/A |  N/A |  N/A |  N/A |  N/A |  N/A | 
| [ intagg](http://www.postgresql.org/docs/9.6/static/intagg.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ intarray](http://www.postgresql.org/docs/9.6/static/intarray.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ip4r](http://www.postgresql.org/ftp/projects/pgFoundry/ip4r/) | 2\.0 | 2\.0 | 2\.0 | 2\.0 | 2\.0 | 2\.0 | 2\.0 | 
| [isn ](http://www.postgresql.org/docs/9.6/static/isn.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| log\_fdw—see [Using the log\_fdw Extension](#CHAP_PostgreSQL.Extensions.log_fdw) |  N/A |  N/A |  N/A |  N/A |  N/A |  N/A |  N/A | 
| [ltree ](http://www.postgresql.org/docs/9.6/static/ltree.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ pg\_buffercache](http://www.postgresql.org/docs/9.6/static/pgbuffercache.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [pg\_freespacemap](https://www.postgresql.org/docs/current/static/pgfreespacemap.html) |  N/A |  N/A |  N/A |  N/A |  N/A |  N/A |  N/A | 
| [pg\_hint\_plan](http://pghintplan.osdn.jp/pg_hint_plan.html) |  N/A |  N/A |  N/A |  N/A |  N/A |  N/A |  N/A | 
| [ pg\_prewarm](https://www.postgresql.org/docs/9.6/static/pgprewarm.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [pg\_stat\_statements](http://www.postgresql.org/docs/9.6/static/pgstatstatements.html) | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 
| [pg\_trgm](http://www.postgresql.org/docs/9.6/static/pgtrgm.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [ pg\_visibility](https://www.postgresql.org/docs/9.6/static/pgvisibility.html) |  N/A |  N/A |  N/A |  N/A |  N/A |  N/A |  N/A | 
| [pgcrypto](http://www.postgresql.org/docs/9.6/static/pgcrypto.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [pgrowlocks](http://www.postgresql.org/docs/9.6/static/pgrowlocks.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [pgstattuple](http://www.postgresql.org/docs/9.6/static/pgstattuple.html) | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 
| plcoffee | 1\.4\.4 | 1\.4\.4 | 1\.4\.4 | 1\.4\.4 | 1\.4\.4 | 1\.4\.4 | 1\.4\.4 | 
| plls | 1\.4\.4 | 1\.4\.4 | 1\.4\.4 | 1\.4\.4 | 1\.4\.4 | 1\.4\.4 | 1\.4\.4 | 
| [ plperl](https://www.postgresql.org/docs/current/static/plperl.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ plpgsql](https://www.postgresql.org/docs/current/static/plpgsql.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ pltcl](https://www.postgresql.org/docs/current/static/pltcl-overview.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [plv8](https://github.com/plv8) | 1\.4\.4 | 1\.4\.4 | 1\.4\.4 | 1\.4\.4 | 1\.4\.4 | 1\.4\.4 | 2\.1\.0 | 
| [PostGIS](http://www.postgis.net/) | 2\.1\.8 | 2\.1\.8 | 2\.1\.8 | 2\.1\.8 | 2\.1\.8 | 2\.1\.8 | 2\.1\.8 | 
| [ postgis\_tiger\_geocoder](http://postgis.net/docs/Geocode.html) | 2\.1\.8 | 2\.1\.8 | 2\.1\.8 | 2\.1\.8 | 2\.1\.8 | 2\.1\.8 | 2\.1\.8 | 
| [ postgis\_topology](http://postgis.net/docs/manual-dev/Topology.html) | 2\.1\.8 | 2\.1\.8 | 2\.1\.8 | 2\.1\.8 | 2\.1\.8 | 2\.1\.8 | 2\.1\.8 | 
| [postgres\_fdw](http://www.postgresql.org/docs/9.6/static/postgres-fdw.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [sslinfo](http://www.postgresql.org/docs/9.6/static/sslinfo.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [tablefunc](http://www.postgresql.org/docs/9.6/static/tablefunc.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ test\_parser](https://www.postgresql.org/docs/9.4/static/test-parser.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [tsearch2](http://www.postgresql.org/docs/9.6/static/tsearch2.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ tsm\_system\_rows](https://www.postgresql.org/docs/current/static/tsm-system-rows.html) |  N/A |  N/A |  N/A |  N/A |  N/A |  N/A |  N/A | 
| [ tsm\_system\_time](https://www.postgresql.org/docs/current/static/tsm-system-time.html) |  N/A |  N/A |  N/A |  N/A |  N/A |  N/A |  N/A | 
|  [unaccent ](http://www.postgresql.org/docs/9.6/static/unaccent.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [uuid\-ossp](http://www.postgresql.org/docs/9.6/static/uuid-ossp.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 

The following modules are supported as shown for versions of PostgreSQL 9\.4\.


| Module | 9\.4\.7 | 9\.4\.9 | 9\.4\.11 | 9\.4\.12 | 9\.4\.14 | 9\.4\.17 | 
| --- | --- | --- | --- | --- | --- | --- | 
| test\_decoder | N/A | N/A | N/A | Supported | Supported | Supported | 

##### PostgreSQL Version 9\.3\.x Extensions Supported on Amazon RDS<a name="PostgreSQL.Concepts.General.FeatureSupport.Extensions.93x"></a>

The following table shows PostgreSQL extensions for PostgreSQL version 9\.3\.x that are currently supported by PostgreSQL on Amazon RDS\. "N/A" indicates that the extension is not available for that PostgreSQL version\. For more information on PostgreSQL extensions, see [Packaging Related Objects into an Extension](https://www.postgresql.org/docs/9.6/static/extend-extensions.html)\. 


| Extension | 9\.3\.12 | 9\.3\.14 | 9\.3\.16 | 9\.3\.17 | 9\.3\.19 | 9\.3\.20 | 9\.3\.22 | 
| --- | --- | --- | --- | --- | --- | --- | --- | 
| [ address\_standardizer](http://postgis.net/docs/Address_Standardizer.html) |  N/A |  N/A |  N/A |  N/A |  N/A |  N/A |  N/A | 
| [ address\_standardizer\_data\_us](http://postgis.net/docs/Address_Standardizer.html) |  N/A |  N/A |  N/A |  N/A |  N/A |  N/A |  N/A | 
| [ bloom](https://www.postgresql.org/docs/9.6/static/bloom.html) |  N/A |  N/A |  N/A |  N/A |  N/A |  N/A |  N/A | 
| [btree\_gin](http://www.postgresql.org/docs/9.6/static/btree-gin.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [btree\_gist](http://www.postgresql.org/docs/9.6/static/btree-gist.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [chkpass](http://www.postgresql.org/docs/9.6/static/chkpass.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [citext ](http://www.postgresql.org/docs/9.6/static/citext.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [cube ](http://www.postgresql.org/docs/9.6/static/cube.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ dblink](http://www.postgresql.org/docs/9.6/static/dblink.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [ dict\_int ](http://www.postgresql.org/docs/9.6/static/dict-int.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ dict\_xsyn](https://www.postgresql.org/docs/9.6/static/dict-xsyn.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [earthdistance](http://www.postgresql.org/docs/9.6/static/earthdistance.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [fuzzystrmatch](http://www.postgresql.org/docs/9.6/static/fuzzystrmatch.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [hstore](http://www.postgresql.org/docs/9.6/static/hstore.html) | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 1\.2 | 
| [ hstore\_plperl](https://www.postgresql.org/docs/9.6/static/hstore.html) |  N/A |  N/A |  N/A |  N/A |  N/A |  N/A |  N/A | 
| [ intagg](http://www.postgresql.org/docs/9.6/static/intagg.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ intarray](http://www.postgresql.org/docs/9.6/static/intarray.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ip4r](http://www.postgresql.org/ftp/projects/pgFoundry/ip4r/) |  N/A |  N/A |  N/A |  N/A |  N/A |  N/A |  N/A | 
| [isn ](http://www.postgresql.org/docs/9.6/static/isn.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| log\_fdw—see [Using the log\_fdw Extension](#CHAP_PostgreSQL.Extensions.log_fdw) |  N/A |  N/A |  N/A |  N/A |  N/A |  N/A |  N/A | 
| [ltree ](http://www.postgresql.org/docs/9.6/static/ltree.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ pg\_buffercache](http://www.postgresql.org/docs/9.6/static/pgbuffercache.html) |  N/A |  N/A |  N/A |  N/A |  N/A |  N/A |  N/A | 
| [pg\_freespacemap](https://www.postgresql.org/docs/current/static/pgfreespacemap.html) |  N/A |  N/A |  N/A |  N/A |  N/A |  N/A |  N/A | 
| [pg\_hint\_plan](http://pghintplan.osdn.jp/pg_hint_plan.html) |  N/A |  N/A |  N/A |  N/A |  N/A |  N/A |  N/A | 
| [ pg\_prewarm](https://www.postgresql.org/docs/9.6/static/pgprewarm.html) |  N/A |  N/A |  N/A |  N/A |  N/A |  N/A |  N/A | 
| [pg\_stat\_statements](http://www.postgresql.org/docs/9.6/static/pgstatstatements.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [pg\_trgm](http://www.postgresql.org/docs/9.6/static/pgtrgm.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [ pg\_visibility](https://www.postgresql.org/docs/9.6/static/pgvisibility.html) |  N/A |  N/A |  N/A |  N/A |  N/A |  N/A |  N/A | 
| [pgcrypto](http://www.postgresql.org/docs/9.6/static/pgcrypto.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [pgrowlocks](http://www.postgresql.org/docs/9.6/static/pgrowlocks.html) | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 1\.1 | 
| [pgstattuple](http://www.postgresql.org/docs/9.6/static/pgstattuple.html) |  N/A |  N/A |  N/A |  N/A |  N/A |  N/A |  N/A | 
| plcoffee | 1\.4\.4 | 1\.4\.4 | 1\.4\.4 | 1\.4\.4 | 1\.4\.4 | 1\.4\.4 | 1\.4\.4 | 
| plls | 1\.4\.4 | 1\.4\.4 | 1\.4\.4 | 1\.4\.4 | 1\.4\.4 | 1\.4\.4 | 1\.4\.4 | 
| [ plperl](https://www.postgresql.org/docs/current/static/plperl.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ plpgsql](https://www.postgresql.org/docs/current/static/plpgsql.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ pltcl](https://www.postgresql.org/docs/current/static/pltcl-overview.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [plv8](https://github.com/plv8) | 1\.4\.4 | 1\.4\.4 | 1\.4\.4 | 1\.4\.4 | 1\.4\.4 | 1\.4\.4 | 2\.1\.0 | 
| [PostGIS](http://www.postgis.net/) | 2\.1\.8 | 2\.1\.8 | 2\.1\.8 | 2\.1\.8 | 2\.1\.8 | 2\.1\.8 | 2\.1\.8 | 
| [ postgis\_tiger\_geocoder](http://postgis.net/docs/Geocode.html) | 2\.1\.8 | 2\.1\.8 | 2\.1\.8 | 2\.1\.8 | 2\.1\.8 | 2\.1\.8 | 2\.1\.8 | 
| [ postgis\_topology](http://postgis.net/docs/manual-dev/Topology.html) | 2\.1\.8 | 2\.1\.8 | 2\.1\.8 | 2\.1\.8 | 2\.1\.8 | 2\.1\.8 | 2\.1\.8 | 
| [postgres\_fdw](http://www.postgresql.org/docs/9.6/static/postgres-fdw.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [sslinfo](http://www.postgresql.org/docs/9.6/static/sslinfo.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [tablefunc](http://www.postgresql.org/docs/9.6/static/tablefunc.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ test\_parser](https://www.postgresql.org/docs/9.4/static/test-parser.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [tsearch2](http://www.postgresql.org/docs/9.6/static/tsearch2.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [ tsm\_system\_rows](https://www.postgresql.org/docs/current/static/tsm-system-rows.html) |  N/A |  N/A |  N/A |  N/A |  N/A |  N/A |  N/A | 
| [ tsm\_system\_time](https://www.postgresql.org/docs/current/static/tsm-system-time.html) |  N/A |  N/A |  N/A |  N/A |  N/A |  N/A |  N/A | 
|  [unaccent ](http://www.postgresql.org/docs/9.6/static/unaccent.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 
| [uuid\-ossp](http://www.postgresql.org/docs/9.6/static/uuid-ossp.html) | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 1\.0 | 

##### PostgreSQL Extension Support for PostGIS on Amazon RDS<a name="CHAP_PostgreSQL.Extensions.SupportedPerVersion"></a>

The following table shows the PostGIS component versions that ship with the Amazon RDS PostgreSQL versions\.


| Version | PostGIS | GEOS | GDAL | PROJ | 
| --- | --- | --- | --- | --- | 
| 9\.3\.12 | 2\.1\.8 r13780 | 3\.5\.0\-CAPI\-1\.9\.0 r4084 |  GDAL 1\.11\.4, released 2016/01/25  |  Rel\. 4\.9\.2, 08 September 2015  | 
| 9\.3\.14 | 2\.1\.8 r13780 | 3\.5\.0\-CAPI\-1\.9\.0 r4084 |  GDAL 1\.11\.5, released 2016/07/01  | Rel\. 4\.9\.2, 08 September 2015 | 
| 9\.3\.16 | 2\.1\.8 r13780 | 3\.5\.0\-CAPI\-1\.9\.0 r4084 |  GDAL 1\.11\.5, released 2016/07/01  | Rel\. 4\.9\.2, 08 September 2015 | 
| 9\.3\.17 | 2\.1\.8 r13780 | 3\.5\.0\-CAPI\-1\.9\.0 r4084 |  GDAL 1\.11\.5, released 2016/07/01  | Rel\. 4\.9\.2, 08 September 2015 | 
| 9\.4\.7 |  2\.1\.8 r13780  |  3\.5\.0\-CAPI\-1\.9\.0 r4084  |  GDAL 1\.11\.4, released 2016/01/25  | Rel\. 4\.9\.2, 08 September 2015 | 
| 9\.4\.9 |  2\.1\.8 r13780  |  3\.5\.0\-CAPI\-1\.9\.0 r4084  |  GDAL 1\.11\.5, released 2016/07/01  | Rel\. 4\.9\.2, 08 September 2015 | 
| 9\.4\.11 |  2\.1\.8 r13780  |  3\.5\.0\-CAPI\-1\.9\.0 r4084  |  GDAL 1\.11\.5, released 2016/07/01  | Rel\. 4\.9\.2, 08 September 2015 | 
| 9\.4\.12 |  2\.1\.8 r13780  |  3\.5\.0\-CAPI\-1\.9\.0 r4084  |  GDAL 1\.11\.5, released 2016/07/01  | Rel\. 4\.9\.2, 08 September 2015 | 
| 9\.5\.2 |  2\.2\.2 r14797  |  3\.5\.0\-CAPI\-1\.9\.0 r4084  |  GDAL 2\.0\.2, released 2016/01/26  | Rel\. 4\.9\.2, 08 September 2015 | 
| 9\.5\.4 |  2\.2\.2 r14797  |  3\.5\.0\-CAPI\-1\.9\.0 r4084  |  GDAL 2\.0\.3, released 2016/07/01  | Rel\. 4\.9\.2, 08 September 2015 | 
| 9\.5\.6 |  2\.2\.5 r15298  |  3\.5\.1\-CAPI\-1\.9\.1 r4246  |  GDAL 2\.0\.3, released 2016/07/01  |  Rel\. 4\.9\.3, 15 August 2016  | 
| 9\.5\.7 |  2\.2\.5 r15298  |  3\.5\.1\-CAPI\-1\.9\.1 r4246  |  GDAL 2\.0\.3, released 2016/07/01  |  Rel\. 4\.9\.3, 15 August 2016  | 
| 9\.6\.1 |  2\.3\.0 r15146  |  3\.5\.0\-CAPI\-1\.9\.0 r4084  |  GDAL 2\.1\.1, released 2016/07/07  | Rel\. 4\.9\.2, 08 September 2015 | 
| 9\.6\.2 |  2\.3\.2 r15302  |  3\.5\.1\-CAPI\-1\.9\.1 r4246  |  GDAL 2\.1\.3, released 2017/20/01  |  Rel\. 4\.9\.3, 15 August 2016  | 
| 9\.6\.3 |  2\.3\.2 r15302  |  3\.5\.1\-CAPI\-1\.9\.1 r4246  |  GDAL 2\.1\.3, released 2017/20/01  |  Rel\. 4\.9\.3, 15 August 2016  | 
| 9\.6\.6 | 2\.3\.4 r16009 | 3\.6\.2\-CAPI\-1\.10\.2 4d2925d6 | GDAL 2\.1\.3, released 2017/20/01 | Rel\. 4\.9\.3, 15 August 2016 | 
| 10\.1 | 2\.4\.2 | 3\.6\.2\-CAPI\-1\.10\.2 4d2925d6 | GDAL 2\.1\.3, released 2017/20/01  | Rel\. 4\.9\.3, 15 August 2016 | 

Before you can use the PostGIS extension, you must create it by running the following command\.

```
 CREATE EXTENSION POSTGIS;
```

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

#### Upgrade PL/v8<a name="PostgreSQL.Concepts.General.UpgradingPLv8"></a>

If you use PL/v8 and upgrade PostgreSQL to a new PL/v8 version, you will immediately take advantage of the new extension but the catalog metadata will not reflect this fact\. The following optional steps will synchronize your catalog metadata with the new version of PL/v8\. 

1. Verify that you need to update\.

   Run the following command while connected to your instance\.

   `select * from pg_available_extensions where name in ('plv8','plls','plcoffee');`

   If your results contain values for an installed version that is a lower number than the default version, you should continue with this procedure to update your extensions\. 

   For example, the following result set indicates you should update:

   ```
   name    | default_version | installed_version |                     comment
   --------+-----------------+-------------------+--------------------------------------------------
   plls    | 2.1.0           | 1.5.3             | PL/LiveScript (v8) trusted procedural language
   plcoffee| 2.1.0           | 1.5.3             | PL/CoffeeScript (v8) trusted procedural language
   plv8    | 2.1.0           | 1.5.3             | PL/JavaScript (v8) trusted procedural language
   (3 rows)
   ```

1. Take a snapshot of your instance\.

   The upgrade will drop all your PL/v8 functions\. Take a snapshot of your instance as a precaution\. You can continue with the following steps while the snapshot is being created\.

   For steps to create a snapshot see, [Creating a DB Snapshot](USER_CreateSnapshot.md)

1. Get a count of the functions you need to drop and recreate\.

   Obtain the count of the number of PL/v8 functions in your instance so you can validate that they are all in place after the upgrade\. 

   The following code returns the number of functions written in PL/v8, plcoffee, or plls:

   ```
   select proname, nspname, lanname 
   from pg_proc p, pg_language l, pg_namespace n
   where p.prolang = l.oid
   and n.oid = p.pronamespace
   and lanname in ('plv8','plcoffee','plls');
   ```

1. Use pg\_dump to create a schema\-only dump file\.

   The following code will create a file on your client machine in the /tmp directory\. 

   `./pg_dump -Fc --schema-only -U master postgres > /tmp/test.dmp`

   This example uses the following flags:
   + \-FC "format custom"
   + \-\-schema\-only "will only dump commands necessary to create schema \(functions in our case\)"
   + \-U "rds master username"
   + database "the database name in our instance"

   For more information on pg\_dump see, [pg\_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html )\.

1. Extract the "CREATE FUNCTION" ddl that is present in the dump file\.

   The following code extracts the ddl needed to create the functions\. You will use this in subsequent steps to re\-create the functions\. The code uses the `grep` command to extract the statements to a file\.

   `./pg_restore -l /tmp/test.dmp | grep FUNCTION > /tmp/function_list/`

   For more information on pg\_restore see, [pg\_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html)\. 

1. Drop the functions and extensions\.

   The following code drops any plv8 based objects\. The cascade option ensures that any dependent are dropped\.

   `drop extension plv8 cascade;`

   If your PostgreSQL instance contains objects based on plcoffee or plls, repeat this step for those extensions\.

1. Create the extensions\.

   The following code creates the PL/v8, plcoffee, and plls extensions:

   `create extension plv8;`

   `create extension plcoffee;`

   `create extension plls;`

1. Create the functions using the dump file and "driver" file\.

   The following code re\-creates the functions that you extracted previously\.

   `./pg_restore -U master -d postgres -Fc -L /tmp/function_list /tmp/test.dmp`

1. Verify your functions count\.

   Validate that your functions have all been re\-creating by re\-running the following code:

   `select * from pg_available_extensions where name in ('plv8','plls','plcoffee'); `
**Note**  
PL/v8 version 2 will add the following extra row to your result set:  

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

Beginning with PostgreSQL version 9\.4, PostgreSQL supports the streaming of WAL changes using logical replication slots\. Amazon RDS supports logical replication for a PostgreSQL DB instance version 9\.4\.9 and higher and 9\.5\.4 and higher\. Using logical replication, you can set up logical replication slots on your instance and stream database changes through these slots to a client like pg\_recvlogical\. Logical slots are created at the database level and support replication connections to a single database\. 

PostgreSQL logical replication on Amazon RDS is enabled by a new parameter, a new replication connection type, and a new security role\. The client for the replication can be any client that is capable of establishing a replication connection to a database on a PostgreSQL DB instance\. 

The most common clients for PostgreSQL logical replication are AWS Database Migration Service or a custom\-managed host on an AWS EC2 instance\. The logical replication slot knows nothing about the receiver of the stream; there is no requirement that the target be a replica database\. If you set up a logical replication slot and don't read from the slot, data can be written to your DB instance's storage and you can quickly fill up the storage on your instance\.

For more information on using logical replication with PostgreSQL, see the [ PostgreSQL documentation](https://www.postgresql.org/docs/current/static/logicaldecoding-example.html)\.

To enable logical replication for an Amazon RDS for PostgreSQL DB instance, you must do the following:
+ The AWS user account initiating the logical replication for the PostgreSQL database on Amazon RDS must have the rds\_superuser role and the rds\_replication role\. The rds\_replication role grants permissions to manage logical slots and to stream data using logical slots\.
+ Set the `rds.logical_replication` parameter to 1\. It is a static parameter that requires a reboot to take effect\. As part of applying this parameter, we set the `wal_level`, `max_wal_senders`, `max_replication_slots`, and `max_connections` parameters\. These parameter changes can increase WAL generation, so you should only set the `rds.logical_replication` parameter when you are using logical slots\.
+ Create a logical replication slot as explained following\. This process requires a decoding plugin to be specified; currently we support the ‘test\_decoding’ output plugin that ships with PostgreSQL\.

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

PostgreSQL versions 9\.4\.9 and later and version 9\.5\.4 and later support event triggers, and Amazon RDS supports event triggers for these versions\. The master user account can be used to create, modify, rename, and delete event triggers\. Event triggers are at the DB instance level, so they can apply to all databases on an instance\.

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
+ You cannot create event triggers on read replicas\. You can, however, create event triggers on a read replica master\. The event triggers are then copied to the read replica\. The event triggers on the read replica don't fire on the read replica when changes are pushed from the master\. However, if the read replica is promoted, the existing event triggers fire when database operations occur\.
+ To perform a major version upgrade to a PostgreSQL DB instance that uses event triggers, you must delete the event triggers before you upgrade the instance\.

##### Huge Pages for Amazon RDS for PostgreSQL<a name="PostgreSQL.Concepts.General.FeatureSupport.HugePages"></a>

Amazon RDS for PostgreSQL supports multiple page sizes for PostgreSQL versions 9\.4\.11 and later, 9\.5\.6 and later, and 9\.6\.2 and later\. This support includes 4 K and 2 MB page sizes\. 

Huge pages reduce overhead when using large contiguous chunks of memory\. You allocate huge pages for your application by using calls to *mmap* or *SYSV* shared memory\. You enable huge pages on an Amazon RDS for PostgreSQL database by using the `huge_pages` parameter\. Set this parameter to "on" to use huge pages; the default value is "off\."

When you set the `huge_pages` parameter to "on," Amazon RDS uses huge pages based on the available shared memory\. If the DB instance is unable to use huge pages due to shared memory constraints, Amazon RDS prevents the instance from starting and sets the status of the DB instance to an incompatible parameters state\. In this case, you can set the `huge_pages` parameter to "off" to allow Amazon RDS to start the DB instance\.

The `shared_buffers` parameter is key to setting the shared memory pool that is required for using huge pages\. The default value for the `shared_buffers` parameter is set to a percentage of the total 8K pages available for that instance's memory\. When you use huge pages, those pages are allocated in the huge pages collocated together\. Amazon RDS puts a DB instance into an incompatible parameters state if the shared memory parameters are set to require more than 90 percent of the DB instance memory\. For more information about setting shared memory for PostgreSQL, see the [PostgreSQL documentation](https://www.postgresql.org/docs/9.6/static/runtime-config-resource.html)\.

**Note**  
Huge pages are not supported for the db\.m1, db\.m2, and db\.m3 DB instance classes\. 

##### Tablespaces for PostgreSQL on Amazon RDS<a name="PostgreSQL.Concepts.General.FeatureSupport.Tablespaces"></a>

Tablespaces are supported in PostgreSQL on Amazon RDS for compatibility; since all storage is on a single logical volume, tablespaces cannot be used for IO splitting or isolation\. We have benchmarks and practical experience that shows that a single logical volume is the best setup for most use cases\.

##### Autovacuum for PostgreSQL on Amazon RDS<a name="PostgreSQL.Concepts.General.FeatureSupport.Autovacuum"></a>

The PostgreSQL auto\-vacuum is an optional, but highly recommended, parameter that by default is turned on for new PostgreSQL DB instances\. Do not turn this parameter off\. For more information on using auto\-vacuum with Amazon RDS PostgreSQL, see [Working with PostgreSQL Autovacuum on Amazon RDS](Appendix.PostgreSQL.CommonDBATasks.md#Appendix.PostgreSQL.CommonDBATasks.Autovacuum)\.

##### RAM Disk for the stats\_temp\_directory<a name="PostgreSQL.Concepts.General.FeatureSupport.RamDisk"></a>

The Amazon RDS for PostgreSQL parameter, `rds.pg_stat_ramdisk_size`, can be used to specify the system memory allocated to a RAM disk for storing the PostgreSQL `stats_temp_directory`\. The RAM disk parameter is available for all PostgreSQL versions on Amazon RDS\. 

Under certain workloads, setting this parameter can improve performance and decrease IO requirements\. For more information about the `stats_temp_directory`, see [ the PostgreSQL documentation\.](https://www.postgresql.org/docs/current/static/runtime-config-statistics.html#GUC-STATS-TEMP-DIRECTORY)

To enable a RAM disk for your `stats_temp_directory`, set the `rds.pg_stat_ramdisk_size` parameter to a non\-zero value in the parameter group used by your DB instance\. The parameter value is in MB\. You must reboot the DB instance before the change takes effect\.

For example, the following AWS CLI command sets the RAM disk parameter to 256 MB\.

```
postgres=>aws rds modify-db-parameter-group \
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

Amazon RDS PostgreSQL versions 9\.6\.2 and 9\.5\.6 and later support the ability to alter enumerations\. This feature is not available in other versions on Amazon RDS\.

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

You can have up to 40 PostgreSQL DB instances\. The following is a list of limitations for PostgreSQL on Amazon RDS:
+ The maximum storage size for PostgreSQL DB instances is the following: 
  + General Purpose \(SSD\) storage: 16 TiB 
  + Provisioned IOPS storage: 16 TiB 
  + Magnetic storage: 3 TiB 
+ The minimum storage size for PostgreSQL DB instances is the following: 
  + General Purpose \(SSD\) storage: 5 GiB 
  + Provisioned IOPS storage: 100 GiB 
  + Magnetic storage: 5 GiB 
+ Amazon RDS reserves up to 3 connections for system maintenance\. If you specify a value for the user connections parameter, you need to add 3 to the number of connections that you expect to use\. 

#### Upgrading a PostgreSQL DB Instance<a name="PostgreSQL.Concepts.General.Patching"></a>

There are two types of upgrades you can manage for your PostgreSQL DB instance:
+ OS Updates – Occasionally, Amazon RDS might need to update the underlying operating system of your DB instance to apply security fixes or OS changes\. You can decide when Amazon RDS applies OS updates by using the RDS console, AWS Command Line Interface \(AWS CLI\), or RDS API\.

  For more information about OS updates, see [Applying Updates for a DB Instance or DB Cluster](USER_UpgradeDBInstance.Maintenance.md#USER_UpgradeDBInstance.OSUpgrades)\.
+  Database Engine Upgrades – When Amazon RDS supports a new version of a database engine, you can upgrade your DB instances to the new version\. There are two kinds of upgrades: major version upgrades and minor version upgrades\. Amazon RDS supports both major and minor version upgrades for PostgreSQL DB instances\. 

  For more information about PostgreSQL DB engine upgrades, see [Upgrading the PostgreSQL DB Engine](USER_UpgradeDBInstance.PostgreSQL.md)\.

#### Using SSL with a PostgreSQL DB Instance<a name="PostgreSQL.Concepts.General.SSL"></a>

Amazon RDS supports Secure Socket Layer \(SSL\) encryption for PostgreSQL DB instances\. Using SSL, you can encrypt a PostgreSQL connection between your applications and your PostgreSQL DB instances\. You can also force all connections to your PostgreSQL DB instance to use SSL\. 

**Topics**
+ [Requiring an SSL Connection to a PostgreSQL DB Instance](#PostgreSQL.Concepts.General.SSL.Requiring)
+ [Determining the SSL Connection Status](#PostgreSQL.Concepts.General.SSL.Status)

SSL support is available in all AWS regions for PostgreSQL\. Amazon RDS creates an SSL certificate for your PostgreSQL DB instance when the instance is created\. If you enable SSL certificate verification, then the SSL certificate includes the DB instance endpoint as the Common Name \(CN\) for the SSL certificate to guard against spoofing attacks\. 

**To connect to a PostgreSQL DB instance over SSL**

1. Download the certificate stored at [https://s3\.amazonaws\.com/rds\-downloads/rds\-combined\-ca\-bundle\.pem](https://s3.amazonaws.com/rds-downloads/rds-combined-ca-bundle.pem)\. 

1. Import the certificate into your operating system\.

1. Connect to your PostgreSQL DB instance over SSL by appending `sslmode=verify-full` to your connection string\. When you use `sslmode=verify-full`, the SSL connection verifies the DB instance endpoint against the endpoint in the SSL certificate\.

   Use the `sslrootcert` parameter to reference the certificate, for example, `sslrootcert=rds-ssl-ca-cert.pem`\.

The following is an example of using the `psql` program to connect to a PostgreSQL DB instance :

```
$ psql -h testpg.cdhmuqifdpib.us-east-1.rds.amazonaws.com -p 5432 \
    "dbname=testpg user=testuser sslrootcert=rds-ca-2015-root.pem sslmode=verify-full"
```

##### Requiring an SSL Connection to a PostgreSQL DB Instance<a name="PostgreSQL.Concepts.General.SSL.Requiring"></a>

You can require that connections to your PostgreSQL DB instance use SSL by using the `rds.force_ssl` parameter\. By default, the `rds.force_ssl` parameter is set to 0 \(off\)\. You can set the `rds.force_ssl` parameter to 1 \(on\) to require SSL for connections to your DB instance\. Updating the `rds.force_ssl` parameter also sets the PostgreSQL `ssl` parameter to 1 \(on\) and modifies your DB instance’s `pg_hba.conf` file to support the new SSL configuration\.

You can set the `rds.force_ssl` parameter value by updating the parameter group for your DB instance\. If the parameter group for your DB instance isn't the default one, and the `ssl` parameter is already set to 1 when you set `rds.force_ssl` to 1, you don't need to reboot your DB instance\. Otherwise, you must reboot your DB instance for the change to take effect\. For more information on parameter groups, see [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)\.

When the `rds.force_ssl` parameter is set to 1 for a DB instance, you see output similar to the following when you connect, indicating that SSL is now required:

```
$ psql postgres -h SOMEHOST.amazonaws.com -p 8192 -U someuser
psql (9.3.12, server 9.4.4)
WARNING: psql major version 9.3, server major version 9.4.
Some psql features might not work.
SSL connection (cipher: DHE-RSA-AES256-SHA, bits: 256)
Type "help" for help.

postgres=>
```

##### Determining the SSL Connection Status<a name="PostgreSQL.Concepts.General.SSL.Status"></a>

The encrypted status of your connection is shown in the logon banner when you connect to the DB instance:

```
Password for user master: 
psql (9.3.12) 
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