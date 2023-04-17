# Amazon RDS for PostgreSQL<a name="CHAP_PostgreSQL"></a>

Amazon RDS supports DB instances running several versions of PostgreSQL\. For a list of available versions, see [Available PostgreSQL database versions](#PostgreSQL.Concepts.General.DBVersions)\.

**Note**  
Deprecation of PostgreSQL 9\.6 is scheduled for April 26, 2022\. For more information, see [Deprecation of PostgreSQL version 9\.6](#PostgreSQL.Concepts.General.DBVersions.Deprecation96)\. 

You can create DB instances and DB snapshots, point\-in\-time restores and backups\. DB instances running PostgreSQL support Multi\-AZ deployments, read replicas, Provisioned IOPS, and can be created inside a virtual private cloud \(VPC\)\. You can also use Secure Socket Layer \(SSL\) to connect to a DB instance running PostgreSQL\.

Before creating a DB instance, make sure to complete the steps in [Setting up for Amazon RDS](CHAP_SettingUp.md)\.

You can use any standard SQL client application to run commands for the instance from your client computer\. Such applications include pgAdmin, a popular Open Source administration and development tool for PostgreSQL, or psql, a command line utility that is part of a PostgreSQL installation\. To deliver a managed service experience, Amazon RDS doesn't provide host access to DB instances\. Also, it restricts access to certain system procedures and tables that require advanced privileges\. Amazon RDS supports access to databases on a DB instance using any standard SQL client application\. Amazon RDS doesn't allow direct host access to a DB instance by using Telnet or Secure Shell \(SSH\)\.

Amazon RDS for PostgreSQL is compliant with many industry standards\. For example, you can use Amazon RDS for PostgreSQL databases to build HIPAA\-compliant applications and to store healthcare\-related information\. This includes storage for protected health information \(PHI\) under a completed Business Associate Agreement \(BAA\) with AWS\. Amazon RDS for PostgreSQL also meets Federal Risk and Authorization Management Program \(FedRAMP\) security requirements\. Amazon RDS for PostgreSQL has received a FedRAMP Joint Authorization Board \(JAB\) Provisional Authority to Operate \(P\-ATO\) at the FedRAMP HIGH Baseline within the AWS GovCloud \(US\) Regions\. For more information on supported compliance standards, see [AWS cloud compliance](http://aws.amazon.com/compliance/)\.

To import PostgreSQL data into a DB instance, follow the information in the [Importing data into PostgreSQL on Amazon RDS](PostgreSQL.Procedural.Importing.md) section\.

**Topics**
+ [Common management tasks for Amazon RDS for PostgreSQL](#CHAP_PostgreSQL.CommonTasks)
+ [Working with the database preview environment](#working-with-the-database-preview-environment)
+ [PostgreSQL version 15 in the database preview environment](#PostgreSQL.Concepts.General.version15)
+ [Available PostgreSQL database versions](#PostgreSQL.Concepts.General.DBVersions)
+ [Supported PostgreSQL extension versions](#PostgreSQL.Concepts.General.FeatureSupport.Extensions)
+ [Working with PostgreSQL features supported by Amazon RDS for PostgreSQL](PostgreSQL.Concepts.General.FeatureSupport.md)
+ [Connecting to a DB instance running the PostgreSQL database engine](USER_ConnectToPostgreSQLInstance.md)
+ [Securing connections to RDS for PostgreSQL with SSL/TLS](PostgreSQL.Concepts.General.Security.md)
+ [Using Kerberos authentication with Amazon RDS for PostgreSQL](postgresql-kerberos.md)
+ [Using a custom DNS server for outbound network access](Appendix.PostgreSQL.CommonDBATasks.CustomDNS.md)
+ [Upgrading the PostgreSQL DB engine for Amazon RDS](USER_UpgradeDBInstance.PostgreSQL.md)
+ [Upgrading a PostgreSQL DB snapshot engine version](USER_UpgradeDBSnapshot.PostgreSQL.md)
+ [Working with read replicas for Amazon RDS for PostgreSQL](USER_PostgreSQL.Replication.ReadReplicas.md)
+ [Improving query performance for RDS for PostgreSQL with Amazon RDS Optimized Reads](USER_PostgreSQL.optimizedreads.md)
+ [Importing data into PostgreSQL on Amazon RDS](PostgreSQL.Procedural.Importing.md)
+ [Exporting data from an RDS for PostgreSQL DB instance to Amazon S3](postgresql-s3-export.md)
+ [Invoking an AWS Lambda function from an RDS for PostgreSQL DB instance](PostgreSQL-Lambda.md)
+ [Common DBA tasks for Amazon RDS for PostgreSQL](Appendix.PostgreSQL.CommonDBATasks.md)
+ [Tuning with wait events for RDS for PostgreSQL](PostgreSQL.Tuning.md)
+ [Tuning RDS for PostgreSQL with Amazon DevOps Guru proactive insights](PostgreSQL.Tuning_proactive_insights.md)
+ [Using PostgreSQL extensions with Amazon RDS for PostgreSQL](Appendix.PostgreSQL.CommonDBATasks.Extensions.md)
+ [Working with the supported foreign data wrappers for Amazon RDS for PostgreSQL](Appendix.PostgreSQL.CommonDBATasks.Extensions.foreign-data-wrappers.md)
+ [Working with Trusted Language Extensions for PostgreSQL](PostgreSQL_trusted_language_extension.md)

## Common management tasks for Amazon RDS for PostgreSQL<a name="CHAP_PostgreSQL.CommonTasks"></a>

The following are the common management tasks you perform with an Amazon RDS for PostgreSQL DB instance, with links to relevant documentation for each task\.


| Task area | Relevant documentation | 
| --- | --- | 
|  **Setting up Amazon RDS for first\-time use** Before you can create your DB instance, make sure to complete a few prerequisites\. For example, DB instances are created by default with a firewall that prevents access to it\. So you need to create a security group with the correct IP addresses and network configuration to access the DB instance\.   |  [Setting up for Amazon RDS](CHAP_SettingUp.md)  | 
|  **Understanding Amazon RDS DB instances** If you are creating a DB instance for production purposes, you should understand how instance classes, storage types, and Provisioned IOPS work in Amazon RDS\.   |  [DB instance classes](Concepts.DBInstanceClass.md) [Amazon RDS storage types](CHAP_Storage.md#Concepts.Storage) [Provisioned IOPS SSD storage](CHAP_Storage.md#USER_PIOPS)  | 
|  **Finding available PostgreSQL versions** Amazon RDS supports several versions of PostgreSQL\.   |  [Available PostgreSQL database versions](#PostgreSQL.Concepts.General.DBVersions)  | 
|  **Setting up high availability and failover support** A production DB instance should use Multi\-AZ deployments\. Multi\-AZ deployments provide increased availability, data durability, and fault tolerance for DB instances\.   |  [Configuring and managing a Multi\-AZ deployment](Concepts.MultiAZ.md)  | 
|  **Understanding the Amazon Virtual Private Cloud \(VPC\) network** If your AWS account has a default VPC, then your DB instance is automatically created inside the default VPC\. In some cases, your account might not have a default VPC, and you might want the DB instance in a VPC\. In these cases, create the VPC and subnet groups before you create the DB instance\.    |  [Working with a DB instance in a VPC](USER_VPC.WorkingWithRDSInstanceinaVPC.md)  | 
|  **Importing data into Amazon RDS PostgreSQL** You can use several different tools to import data into your PostgreSQL DB instance on Amazon RDS\.   |  [Importing data into PostgreSQL on Amazon RDS](PostgreSQL.Procedural.Importing.md)  | 
|  **Setting up read\-only read replicas \(primary and standbys\)** RDS for PostgreSQL supports read replicas in both the same AWS Region and in a different AWS Region from the primary instance\.  |  [Working with DB instance read replicas](USER_ReadRepl.md) [Working with read replicas for Amazon RDS for PostgreSQL](USER_PostgreSQL.Replication.ReadReplicas.md) [Creating a read replica in a different AWS Region](USER_ReadRepl.md#USER_ReadRepl.XRgn)  | 
|  **Understanding security groups** By default, DB instances are created with a firewall that prevents access to them\. To provide access through that firewall, you edit the inbound rules for the VPC security group associated with the VPC hosting the DB instance\.   |  [Controlling access with security groups](Overview.RDSSecurityGroups.md)  | 
|  **Setting up parameter groups and features** To change the default parameters for your DB instance, create a custom DB parameter group and change settings to that\. If you do this before creating your DB instance, you can choose your custom DB parameter group when you create the instance\.   |  [Working with parameter groups](USER_WorkingWithParamGroups.md)  | 
|  **Connecting to your PostgreSQL DB instance** After creating a security group and associating it to a DB instance, you can connect to the DB instance using any standard SQL client application such as `psql` or `pgAdmin`\.  |  [Connecting to a DB instance running the PostgreSQL database engine](USER_ConnectToPostgreSQLInstance.md) [Using SSL with a PostgreSQL DB instance](PostgreSQL.Concepts.General.SSL.md)  | 
|  **Backing up and restoring your DB instance** You can configure your DB instance to take automated backups, or take manual snapshots, and then restore instances from the backups or snapshots\.   |  [Backing up and restoring](CHAP_CommonTasks.BackupRestore.md)  | 
|  **Monitoring the activity and performance of your DB instance** You can monitor a PostgreSQL DB instance by using CloudWatch Amazon RDS metrics, events, and enhanced monitoring\.   |  [Viewing metrics in the Amazon RDS console](USER_Monitoring.md) [Viewing Amazon RDS events](USER_ListEvents.md)  | 
|  **Upgrading the PostgreSQL database version** You can do both major and minor version upgrades for your PostgreSQL DB instance\.   |  [Upgrading the PostgreSQL DB engine for Amazon RDS](USER_UpgradeDBInstance.PostgreSQL.md) [ Choosing a major version upgrade for PostgreSQL](USER_UpgradeDBInstance.PostgreSQL.md#USER_UpgradeDBInstance.PostgreSQL.MajorVersion)  | 
|  **Working with log files** You can access the log files for your PostgreSQL DB instance\.   |  [RDS for PostgreSQL database log files](USER_LogAccess.Concepts.PostgreSQL.md)  | 
|  **Understanding the best practices for PostgreSQL DB instances** Find some of the best practices for working with PostgreSQL on Amazon RDS\.   |  [Best practices for working with PostgreSQL](CHAP_BestPractices.md#CHAP_BestPractices.PostgreSQL)  | 

Following is a list of other sections in this guide that can help you understand and use important features of RDS for PostgreSQL: 
+  [Understanding PostgreSQL roles and permissions](Appendix.PostgreSQL.CommonDBATasks.Roles.md) 
+  [Controlling user access to the PostgreSQL databaseControlling user access to PostgreSQL](Appendix.PostgreSQL.CommonDBATasks.Roles.md#Appendix.PostgreSQL.CommonDBATasks.Access) 
+  [Working with parameters on your RDS for PostgreSQL DB instance](Appendix.PostgreSQL.CommonDBATasks.Parameters.md) 
+  [Understanding logging mechanisms supported by RDS for PostgreSQL](Appendix.PostgreSQL.CommonDBATasks.md#Appendix.PostgreSQL.CommonDBATasks.Auditing) 
+  [Working with the PostgreSQL autovacuum on Amazon RDS for PostgreSQL](Appendix.PostgreSQL.CommonDBATasks.Autovacuum.md) 
+  [Using a custom DNS server for outbound network access](Appendix.PostgreSQL.CommonDBATasks.CustomDNS.md) 

## Working with the database preview environment<a name="working-with-the-database-preview-environment"></a>

 The PostgreSQL community continuously releases new PostgreSQL version and extensions, including beta versions\. This gives PostgreSQL users the opportunity to try out a new PostgreSQL version early\. To learn more about the PostgreSQL community beta release process, see [Beta Information](https://www.postgresql.org/developer/beta/) in the PostgreSQL documentation\. Similarly, Amazon RDS makes certain PostgreSQL beta versions available as Preview releases\. This allows you to create DB instances using the Preview version and test out its features in the Database Preview Environment\. 

RDS for PostgreSQL DB instances in the Database Preview Environment are functionally similar to other RDS for PostgreSQL instances\. However, you can't use a Preview version for production\.

Keep in mind the following important limitations:
+ All DB instances are deleted 60 days after you create them, along with any backups and snapshots\.
+ You can only create a DB instance in a virtual private cloud \(VPC\) based on the Amazon VPC service\.
+ You can only use General Purpose SSD and Provisioned IOPS SSD storage\. 
+ You can't get help from AWS Support with DB instances\. Instead, you can post your questions to the AWS‐managed Q&A community, [AWS re:Post](https://repost.aws/tags/TAsibBK6ZeQYihN9as4S_psg/amazon-relational-database-service)\.
+ You can't copy a snapshot of a DB instance to a production environment\.

The following options are supported by the Preview\.
+ You can create DB instances using M6i, R6i, M6g, M5, T3, R6g, and R5 instance types only\. For more information about RDS instance classes, see [DB instance classes](Concepts.DBInstanceClass.md)\. 
+ You can use both single\-AZ and multi\-AZ deployments\.
+ You can use standard PostgreSQL dump and load functions to export databases from or import databases to the Database Preview Environment\.

### Features not supported in the preview environment<a name="preview-environment-exclusions"></a>

The following features aren't available in the preview environment:
+ Cross\-Region snapshot copy
+ Cross\-Region read replicas

### Creating a new DB instance in the preview environment<a name="create-db-instance-in-preview-environment"></a>

Use the following procedure to create a DB instance in the preview environment\.

**To create a DB instance in the preview environment**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Choose **Dashboard** from the navigation pane\.

1. In the Dashboard page, locate the **Database Preview Environment** section on the Dashboard page, as shown in the following image\.  
![\[Preview environment section with link displayed in RDS Console, Dashboard\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/rpg-preview-environment-dashboard.png)

   You can navigate directly to the [Database preview environment](https://us-east-2.console.aws.amazon.com/rds-preview/home?region=us-east-2#)\. Before you can proceed, you must acknowledge and accept the limitations\.   
![\[Preview environment limitations dialog\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/rpg-preview-environment-console.png)

1. To create the RDS for PostgreSQL DB instance, follow the same process as that for creating any Amazon RDS DB instance\. For more information, see the [Console](USER_CreateDBInstance.md#USER_CreateDBInstance.CON) procedure in [Creating a DB instance](USER_CreateDBInstance.md#USER_CreateDBInstance.Creating)\.

To create an instance in the Database Preview Environment using the RDS API or the AWS CLI, use the following endpoint\.

```
rds-preview.us-east-2.amazonaws.com
```

## PostgreSQL version 15 in the database preview environment<a name="PostgreSQL.Concepts.General.version15"></a>

PostgreSQL version 15 is now available in the Amazon RDS Database Preview Environment\. PostgreSQL version 15 contains several improvements that are described in the following PostgreSQL documentation:
+ [ PostgreSQL 15](https://www.postgresql.org/docs/15/release-15.html)
+ [ PostgreSQL 15 Beta 3 Released\!](https://www.postgresql.org/about/news/2496/)

For information on the Database Preview Environment, see [Working with the database preview environment](#working-with-the-database-preview-environment)\. To access the Preview Environment from the console, select [https://console\.aws\.amazon\.com/rds\-preview/](https://console.aws.amazon.com/rds-preview/)\.

## Available PostgreSQL database versions<a name="PostgreSQL.Concepts.General.DBVersions"></a>

Amazon RDS supports DB instances running several editions of PostgreSQL\. You can specify any currently available PostgreSQL version when creating a new DB instance\. You can specify the major version \(such as PostgreSQL 14\), and any available minor version for the specified major version\. If no version is specified, Amazon RDS defaults to an available version, typically the most recent version\. If a major version is specified but a minor version is not, Amazon RDS defaults to a recent release of the major version you have specified\. 

To see a list of available versions, as well as defaults for newly created DB instances, use the [https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-engine-versions.html](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-engine-versions.html) AWS CLI command\. For example, to display the default PostgreSQL engine version, use the following command:

```
aws rds describe-db-engine-versions --default-only --engine postgres
```

For details about the PostgreSQL versions that are supported on Amazon RDS, see the [https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/Welcome.html](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/Welcome.html)\.

### Deprecation of PostgreSQL version 10<a name="PostgreSQL.Concepts.General.DBVersions.Deprecation10"></a>

On April 17, 2023, Amazon RDS plans to deprecate PostgreSQL 10 using the following schedule\. We recommend that you take action and upgrade your PostgreSQL databases running on major version 10 to a later version, such as PostgreSQL version 14\. To upgrade your RDS for PostgreSQL major version 10 DB instance from a PostgreSQL version older than 10\.19, we recommend that you first upgrade to version 10\.19 and then upgrade to version 14\. For more information, see [Upgrading the PostgreSQL DB engine for Amazon RDS](USER_UpgradeDBInstance.PostgreSQL.md)\. 


| Action or recommendation | Dates | 
| --- | --- | 
|  The PostgreSQL community plans to deprecate PostgreSQL 10 and won't provide any security patches after this date\.   |  November 10, 2022  | 
|  Start upgrading RDS for PostgreSQL 10 DB instances to a later major version, such as PostgreSQL 14\. Although you can continue to restore PostgreSQL 10 snapshots and create read replicas with version 10, be aware of the other critical dates in this deprecation schedule and their impact\.   |  Now – February 14, 2023  | 
|  After this date, you can't create new Amazon RDS instances with PostgreSQL major version 10 from either the AWS Management Console or the AWS CLI\.   |  February 14, 2023  | 
|  After this date, Amazon RDS automatically upgrades PostgreSQL 10 instances to version 14\. If you restore a PostgreSQL 10 database snapshot, Amazon RDS automatically upgrades the restored database to PostgreSQL 14\.   |  April 17, 2023  | 

For more information about RDS for PostgreSQL version 10 deprecation, see [\[Announcement\]: RDS for PostgreSQL 10 deprecation](https://repost.aws/questions/QUph1IFLkkRiyc0pCdTH493Q/announcement-amazon-rds-for-postgre-sql-10-deprecation) in AWS re:Post\. 

### Deprecation of PostgreSQL version 9\.6<a name="PostgreSQL.Concepts.General.DBVersions.Deprecation96"></a>

On March 31, 2022, Amazon RDS plans to deprecate PostgreSQL 9\.6 using the following schedule\. This extends the previously announced date of January 18, 2022 to April 26, 2022\. You should upgrade all your PostgreSQL 9\.6 DB instances to PostgreSQL 12 or higher as soon as possible\. We recommend that you first upgrade to minor version 9\.6\.20 or higher and then upgrade directly to PostgreSQL 12 rather than upgrading to an intermediate major version\. For more information, see [Upgrading the PostgreSQL DB engine for Amazon RDS](USER_UpgradeDBInstance.PostgreSQL.md)\. 


| Action or recommendation | Dates | 
| --- | --- | 
|  The PostgreSQL community discontinued support for PostgreSQL 9\.6, and will no longer provide bug fixes or security patches for this version\.   |  November 11, 2021  | 
|  Start upgrading RDS for PostgreSQL 9\.6 DB instances to PostgreSQL 12 or higher as soon as possible\. Although you can continue to restore PostgreSQL 9\.6 snapshots and create read replicas with version 9\.6, be aware of the other critical dates in this deprecation schedule and their impact\.   |  Now – March 31, 2022  | 
|  After this date, you can't create new Amazon RDS instances with PostgreSQL major version 9\.6 from either the AWS Management Console or the AWS CLI\.   |  March 31, 2022  | 
|  After this date, Amazon RDS automatically upgrades PostgreSQL 9\.6 instances to version 12\. If you restore a PostgreSQL 9\.6 database snapshot, Amazon RDS automatically upgrades the restored database to PostgreSQL 12\.   |  April 26, 2022  | 

### Deprecated versions for Amazon RDS for PostgreSQL<a name="PostgreSQL.Concepts.General.DeprecatedVersions"></a>

RDS for PostgreSQL 9\.5 is deprecated as of March, 2021\. For more information about RDS for PostgreSQL 9\.5 deprecation, see [ Upgrading from Amazon RDS for PostgreSQL version 9\.5](http://aws.amazon.com/blogs/database/upgrading-from-amazon-rds-for-postgresql-version-9-5/)\.

To learn more about deprecation policy for RDS for PostgreSQL, see [Amazon RDS FAQs](http://aws.amazon.com/rds/faqs/)\. For more information about PostgreSQL versions, see [Versioning Policy](https://www.postgresql.org/support/versioning/) in the PostgreSQL documentation\.

## Supported PostgreSQL extension versions<a name="PostgreSQL.Concepts.General.FeatureSupport.Extensions"></a>

RDS for PostgreSQL supports many PostgreSQL extensions\. The PostgreSQL community sometimes refers to these as modules\. Extensions expand on the functionality provided by the PostgreSQL engine\. You can find a list of extensions supported by Amazon RDS in the default DB parameter group for that PostgreSQL version\. You can also see the current extensions list using `psql` by showing the `rds.extensions` parameter as in the following example\.

```
SHOW rds.extensions; 
```

**Note**  
Parameters added in a minor version release might display inaccurately when using the `rds.extensions` parameter in `psql`\. 

As of RDS for PostgreSQL 13, certain extensions can be installed by database users other than the `rds_superuser`\. These are known as *trusted extensions*\. To learn more, see [PostgreSQL trusted extensions](#PostgreSQL.Concepts.General.Extensions.Trusted)\. 

Certain versions of RDS for PostgreSQL support the `rds.allowed_extensions` parameter\. This parameter lets an `rds_superuser` limit the extensions that can be installed in the RDS for PostgreSQL DB instance\. For more information, see [Restricting installation of PostgreSQL extensions](#PostgreSQL.Concepts.General.FeatureSupport.Extensions.Restriction)\. 

For lists of PostgreSQL extensions and versions that are supported by each available RDS for PostgreSQL version, see [PostgreSQL extensions supported on Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-extensions.html) in *Amazon RDS for PostgreSQL Release Notes*\. 

### Restricting installation of PostgreSQL extensions<a name="PostgreSQL.Concepts.General.FeatureSupport.Extensions.Restriction"></a>

You can restrict which extensions can be installed on a PostgreSQL DB instance\. To do so, set the `rds.allowed_extensions` parameter to a string of comma\-separated extension names\. Only these extensions can then be installed in the PostgreSQL DB instance\.

The default string for the `rds.allowed_extensions` parameter is '\*', which means that any extension available for the engine version can be installed\. Changing the `rds.allowed_extensions` parameter does not require a database restart because it's a dynamic parameter\.

The PostgreSQL DB instance engine must be one of the following versions for you to use the `rds.allowed_extensions` parameter:
+ PostgreSQL 14\.1 or a higher minor version
+ PostgreSQL 13\.2 or a higher minor version
+ PostgreSQL 12\.6 or a higher minor version

 To see which extension installations are allowed, use the following psql command\.

```
postgres=> SHOW rds.allowed_extensions;
 rds.allowed_extensions
------------------------
 *
```

If an extension was installed prior to it being left out of the list in the `rds.allowed_extensions` parameter, the extension can still be used normally, and commands such as `ALTER EXTENSION` and `DROP EXTENSION` will continue to work\. However, after an extension is restricted, `CREATE EXTENSION` commands for the restricted extension will fail\.

Installation of extension dependencies with `CREATE EXTENSION CASCADE` are also restricted\. The extension and its dependencies must be specified in `rds.allowed_extensions`\. If an extension dependency installation fails, the entire `CREATE EXTENSION CASCADE` statement will fail\. 

If an extension is not included with the `rds.allowed_extensions` parameter, you will see an error such as the following if you try to install it\.

```
ERROR: permission denied to create extension "extension-name" 
HINT: This extension is not specified in "rds.allowed_extensions".
```

### PostgreSQL trusted extensions<a name="PostgreSQL.Concepts.General.Extensions.Trusted"></a>

To install most PostgreSQL extensions requires `rds_superuser` privileges\. PostgreSQL 13 introduced trusted extensions, which reduce the need to grant `rds_superuser` privileges to regular users\. With this feature, users can install many extensions if they have the `CREATE` privilege on the current database instead of requiring the `rds_superuser` role\. For more information, see the SQL [CREATE EXTENSION](https://www.postgresql.org/docs/current/sql-createextension.html) command in the PostgreSQL documentation\. 

The following lists the extensions that can be installed by a user who has the `CREATE` privilege on the current database and do not require the `rds_superuser` role:
+ bool\_plperl
+ [btree\_gin](http://www.postgresql.org/docs/current/btree-gin.html)
+ [btree\_gist](http://www.postgresql.org/docs/current/btree-gist.html)
+ [citext ](http://www.postgresql.org/docs/current/citext.html)
+ [cube ](http://www.postgresql.org/docs/current/cube.html)
+ [ dict\_int ](http://www.postgresql.org/docs/current/dict-int.html)
+ [fuzzystrmatch](http://www.postgresql.org/docs/current/fuzzystrmatch.html)
+  [hstore](http://www.postgresql.org/docs/current/hstore.html)
+ [ intarray](http://www.postgresql.org/docs/current/intarray.html)
+ [isn](http://www.postgresql.org/docs/current/isn.html)
+ jsonb\_plperl
+ [ltree ](http://www.postgresql.org/docs/current/ltree.html)
+ [pg\_trgm](http://www.postgresql.org/docs/current/pgtrgm.html)
+ [pgcrypto](http://www.postgresql.org/docs/current/pgcrypto.html)
+ [plperl](https://www.postgresql.org/docs/current/plperl.html)
+ [plpgsql](https://www.postgresql.org/docs/current/plpgsql.html)
+ [pltcl](https://www.postgresql.org/docs/current/pltcl-overview.html)
+ [tablefunc](http://www.postgresql.org/docs/current/tablefunc.html) 
+ [tsm\_system\_rows](https://www.postgresql.org/docs/current/tsm-system-rows.html)
+ [tsm\_system\_time](https://www.postgresql.org/docs/current/tsm-system-time.html)
+ [unaccent](http://www.postgresql.org/docs/current/unaccent.html)
+ [uuid\-ossp](http://www.postgresql.org/docs/current/uuid-ossp.html)

For lists of PostgreSQL extensions and versions that are supported by each available RDS for PostgreSQL version, see [PostgreSQL extensions supported on Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-extensions.html) in *Amazon RDS for PostgreSQL Release Notes*\. 