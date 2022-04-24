# PostgreSQL on Amazon RDS<a name="CHAP_PostgreSQL"></a>

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
+ [Limitations for PostgreSQL DB instances](#PostgreSQL.Concepts.General.Limits)
+ [Available PostgreSQL database versions](#PostgreSQL.Concepts.General.DBVersions)
+ [Deprecated versions for Amazon RDS for PostgreSQL](#PostgreSQL.Concepts.General.DeprecatedVersions)
+ [Supported PostgreSQL extension versions](#PostgreSQL.Concepts.General.FeatureSupport.Extensions)
+ [Connecting to a DB instance running the PostgreSQL database engine](USER_ConnectToPostgreSQLInstance.md)
+ [Securing connections to RDS for PostgreSQL with SSL/TLS](PostgreSQL.Concepts.General.Security.md)
+ [Using Kerberos authentication with Amazon RDS for PostgreSQL](postgresql-kerberos.md)
+ [Using a custom DNS server for outbound network access](Appendix.PostgreSQL.CommonDBATasks.CustomDNS.md)
+ [Upgrading the PostgreSQL DB engine for Amazon RDS](USER_UpgradeDBInstance.PostgreSQL.md)
+ [Upgrading a PostgreSQL DB snapshot engine version](USER_UpgradeDBSnapshot.PostgreSQL.md)
+ [Working with PostgreSQL read replicas in Amazon RDS](USER_PostgreSQL.Replication.ReadReplicas.md)
+ [Importing data into PostgreSQL on Amazon RDS](PostgreSQL.Procedural.Importing.md)
+ [Exporting data from an RDS for PostgreSQL DB instance to Amazon S3](postgresql-s3-export.md)
+ [Invoking an AWS Lambda function from an RDS for PostgreSQL DB instance](PostgreSQL-Lambda.md)
+ [Working with PostgreSQL features supported by Amazon RDS for PostgreSQL](#PostgreSQL.Concepts.General.FeatureSupport)
+ [Common DBA tasks for Amazon RDS for PostgreSQL](Appendix.PostgreSQL.CommonDBATasks.md)
+ [Using PostgreSQL extensions with Amazon RDS for PostgreSQL](Appendix.PostgreSQL.CommonDBATasks.Extensions.md)
+ [Working with the supported foreign data wrappers for Amazon RDS for PostgreSQL](Appendix.PostgreSQL.CommonDBATasks.Extensions.foreign-data-wrappers.md)

## Common management tasks for Amazon RDS for PostgreSQL<a name="CHAP_PostgreSQL.CommonTasks"></a>

The following are the common management tasks you perform with an Amazon RDS for PostgreSQL DB instance, with links to relevant documentation for each task\.


| Task area | Relevant documentation | 
| --- | --- | 
|  **Setting up Amazon RDS for first\-time use** Before you can create your DB instance, make sure to complete a few prerequisites\. For example, DB instances are created by default with a firewall that prevents access to it\. So you need to create a security group with the correct IP addresses and network configuration to access the DB instance\.   |  [Setting up for Amazon RDS](CHAP_SettingUp.md)  | 
|  **Understanding Amazon RDS DB instances** If you are creating a DB instance for production purposes, you should understand how instance classes, storage types, and Provisioned IOPS work in Amazon RDS\.   |  [DB instance classes](Concepts.DBInstanceClass.md) [Amazon RDS storage types](CHAP_Storage.md#Concepts.Storage) [Provisioned IOPS SSD storage](CHAP_Storage.md#USER_PIOPS)  | 
|  **Finding available PostgreSQL versions** Amazon RDS supports several versions of PostgreSQL\.   |  [Available PostgreSQL database versions](#PostgreSQL.Concepts.General.DBVersions)  | 
|  **Setting up high availability and failover support** A production DB instance should use Multi\-AZ deployments\. Multi\-AZ deployments provide increased availability, data durability, and fault tolerance for DB instances\.   |  [Multi\-AZ deployments for high availability](Concepts.MultiAZ.md)  | 
|  **Understanding the Amazon Virtual Private Cloud \(VPC\) network** If your AWS account has a default VPC, then your DB instance is automatically created inside the default VPC\. In some cases, your account might not have a default VPC, and you might want the DB instance in a VPC\. In these cases, create the VPC and subnet groups before you create the DB instance\.    |  [Determining whether you are using the EC2\-VPC or EC2\-Classic platform](USER_VPC.FindDefaultVPC.md) [Working with a DB instance in a VPC](USER_VPC.WorkingWithRDSInstanceinaVPC.md)  | 
|  **Importing data into Amazon RDS PostgreSQL** You can use several different tools to import data into your PostgreSQL DB instance on Amazon RDS\.   |  [Importing data into PostgreSQL on Amazon RDS](PostgreSQL.Procedural.Importing.md)  | 
|  **Setting up read\-only read replicas \(primary and standbys\)** RDS for PostgreSQL supports read replicas in both the same AWS Region and in a different AWS Region from the primary instance\.  |  [Working with read replicas](USER_ReadRepl.md) [Working with PostgreSQL read replicas in Amazon RDS](USER_PostgreSQL.Replication.ReadReplicas.md) [Creating a read replica in a different AWS Region](USER_ReadRepl.XRgn.md)  | 
|  **Understanding security groups** By default, DB instances are created with a firewall that prevents access to them\. To provide access through that firewall, you edit the inbound rules for the security group associated with the VPC hosting the DB instance\.  In general, if your DB instance is on the EC2\-Classic platform, you need to create a DB security group\. If your DB instance is on the EC2\-VPC platform, you need to create a VPC security group\.   |  [Determining whether you are using the EC2\-VPC or EC2\-Classic platform](USER_VPC.FindDefaultVPC.md) [Controlling access with security groups](Overview.RDSSecurityGroups.md)  | 
|  **Setting up parameter groups and features** To change the default parameters for your DB instance, create a custom DB parameter group and change settings to that\. If you do this before creating your DB instance, you can choose your custom DB parameter group when you create the instance\.   |  [Working with parameter groups](USER_WorkingWithParamGroups.md)  | 
|  **Connecting to your PostgreSQL DB instance** After creating a security group and associating it to a DB instance, you can connect to the DB instance using any standard SQL client application such as `psql` or `pgAdmin`\.  |  [Connecting to a DB instance running the PostgreSQL database engine](USER_ConnectToPostgreSQLInstance.md) [Using SSL with a PostgreSQL DB instance](PostgreSQL.Concepts.General.SSL.md)  | 
|  **Backing up and restoring your DB instance** You can configure your DB instance to take automated backups, or take manual snapshots, and then restore instances from the backups or snapshots\.   |  [Backing up and restoring an Amazon RDS DB instance](CHAP_CommonTasks.BackupRestore.md)  | 
|  **Monitoring the activity and performance of your DB instance** You can monitor a PostgreSQL DB instance by using CloudWatch Amazon RDS metrics, events, and enhanced monitoring\.   |  [Viewing metrics in the Amazon RDS console](USER_Monitoring.md) [Viewing Amazon RDS events](USER_ListEvents.md)  | 
|  **Upgrading the PostgreSQL database version** You can do both major and minor version upgrades for your PostgreSQL DB instance\.   |  [Upgrading the PostgreSQL DB engine for Amazon RDS](USER_UpgradeDBInstance.PostgreSQL.md) [ Choosing a major version upgrade for PostgreSQL ](USER_UpgradeDBInstance.PostgreSQL.md#USER_UpgradeDBInstance.PostgreSQL.MajorVersion)  | 
|  **Working with log files** You can access the log files for your PostgreSQL DB instance\.   |  [PostgreSQL database log files](USER_LogAccess.Concepts.PostgreSQL.md)  | 
|  **Understanding the best practices for PostgreSQL DB instances** Find some of the best practices for working with PostgreSQL on Amazon RDS\.   |  [Best practices for working with PostgreSQL](CHAP_BestPractices.md#CHAP_BestPractices.PostgreSQL)  | 

Following is a list of other sections in this guide that can help you understand and use important features of RDS for PostgreSQL: 
+  [Understanding the rds\_superuser role](Appendix.PostgreSQL.CommonDBATasks.md#Appendix.PostgreSQL.CommonDBATasks.Roles) 
+  [Controlling user access to the PostgreSQL databaseControlling user access to PostgreSQL](Appendix.PostgreSQL.CommonDBATasks.md#Appendix.PostgreSQL.CommonDBATasks.Access) 
+  [Working with parameters on your RDS for PostgreSQL DB instance](Appendix.PostgreSQL.CommonDBATasks.Parameters.md) 
+  [Understanding logging mechanisms supported by RDS for PostgreSQL](Appendix.PostgreSQL.CommonDBATasks.md#Appendix.PostgreSQL.CommonDBATasks.Auditing) 
+  [Working with the PostgreSQL autovacuum on Amazon RDS for PostgreSQL](Appendix.PostgreSQL.CommonDBATasks.Autovacuum.md) 
+  [Using a custom DNS server for outbound network access](Appendix.PostgreSQL.CommonDBATasks.CustomDNS.md) 

## Working with the database preview environment<a name="working-with-the-database-preview-environment"></a>

When you create a DB instance in Amazon RDS, you know that the PostgreSQL version it's based on has been tested and is fully supported by Amazon\. The PostgreSQL community releases new versions and new extensions continuously\. You can try out new PostgreSQL versions and extensions before they are fully supported\. To do that, you can create a new DB instance in the Database Preview Environment\. 

DB instances in the Database Preview Environment are similar to DB instances in a production environment\. However, keep in mind several important factors:
+ All DB instances are deleted 60 days after you create them, along with any backups and snapshots\.
+ You can only create a DB instance in a virtual private cloud \(VPC\) based on the Amazon VPC service\.
+ You can only create M6g, M5, T3, R6g, and R5 instance types\. For more information about RDS instance classes, see [DB instance classes](Concepts.DBInstanceClass.md)\. 
+ You can only use General Purpose SSD and Provisioned IOPS SSD storage\. 
+ You can't get help from AWS Support with DB instances\. Instead, you can post your questions to the AWS‐managed Q&A community, [AWS re:Post](https://repost.aws/tags/TAsibBK6ZeQYihN9as4S_psg/amazon-relational-database-service)\.
+ You can't copy a snapshot of a DB instance to a production environment\.
+ You can use both single\-AZ and multi\-AZ deployments\.
+ You can use standard PostgreSQL dump and load functions to export databases from or import databases to the Database Preview Environment\.

**Topics**
+ [Features not supported in the preview environment](#preview-environment-exclusions)
+ [Creating a new DB instance in the preview environment](#create-db-instance-in-preview-environment)

### Features not supported in the preview environment<a name="preview-environment-exclusions"></a>

The following features are not available in the preview environment:
+ Cross\-Region snapshot copy
+ Cross\-Region read replicas

### Creating a new DB instance in the preview environment<a name="create-db-instance-in-preview-environment"></a>

Use the following procedure to create a DB instance in the preview environment\.

**To create a DB instance in the preview environment**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1.  Choose **Dashboard** from the navigation pane\. 

1. Choose **Switch to database preview environment**\.   
![\[Dialog box to select the preview environment\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/instance_preview.png)

   You also can navigate directly to the [Database preview environment](https://us-east-2.console.aws.amazon.com/rds-preview/home?region=us-east-2#)\.
**Note**  
If you want to create an instance in the Database Preview Environment with the API or CLI, the endpoint is `rds-preview.us-east-2.amazonaws.com`\.

1. Continue with the procedure as described in [Console](USER_CreateDBInstance.md#USER_CreateDBInstance.CON)\.

## Limitations for PostgreSQL DB instances<a name="PostgreSQL.Concepts.General.Limits"></a>

The following is a list of limitations for RDS for PostgreSQL:
+ You can have up to 40 PostgreSQL DB instances\.
+ For storage limits, see [Amazon RDS DB instance storage](CHAP_Storage.md)\.
+ Amazon RDS reserves up to 3 connections for system maintenance\. If you specify a value for the user connections parameter, add 3 to the number of connections that you expect to use\. 

## Available PostgreSQL database versions<a name="PostgreSQL.Concepts.General.DBVersions"></a>

Amazon RDS supports DB instances running several editions of PostgreSQL\. You can specify any currently available PostgreSQL version when creating a new DB instance\. You can specify the major version \(such as PostgreSQL 10\), and any available minor version for the specified major version\. If no version is specified, Amazon RDS defaults to an available version, typically the most recent version\. If a major version is specified but a minor version is not, Amazon RDS defaults to a recent release of the major version you have specified\. 

To see a list of available versions, as well as defaults for newly created DB instances, use the [https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-engine-versions.html](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-engine-versions.html) AWS CLI command\. For example, to display the default PostgreSQL engine version, use the following command:

```
aws rds describe-db-engine-versions --default-only --engine postgres
```

For details about the PostgreSQL versions that are supported on Amazon RDS, see the [https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/Welcome.html](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/Welcome.html)\.

### Deprecation of PostgreSQL version 9\.6<a name="PostgreSQL.Concepts.General.DBVersions.Deprecation96"></a>

On March 31, 2022, Amazon RDS plans to deprecate PostgreSQL 9\.6 using the following schedule\. This extends the previously announced date of January 18, 2022 to April 26, 2022\. You should upgrade all your PostgreSQL 9\.6 DB instances to PostgreSQL 12 or higher as soon as possible\. We recommend that you first upgrade to minor version 9\.6\.20 or higher and then upgrade directly to PostgreSQL 12 rather than upgrading to an intermediate major version\. For more information, see [Upgrading the PostgreSQL DB engine for Amazon RDS](USER_UpgradeDBInstance.PostgreSQL.md)\. 


| Action or recommendation | Dates | 
| --- | --- | 
|  The PostgreSQL community discontinued support for PostgreSQL 9\.6, and will no longer provide bug fixes or security patches for this version\.   |  November 11, 2021  | 
|  Start upgrading RDS for PostgreSQL 9\.6 DB instances to PostgreSQL 12 or higher as soon as possible\. Although you can continue to restore PostgreSQL 9\.6 snapshots and create read replicas with version 9\.6, be aware of the other critical dates in this deprecation schedule and their impact\.   |  Now – March 31, 2022  | 
|  After this date, you can't create new Amazon RDS instances with PostgreSQL major version 9\.6 from either the AWS Management Console or the AWS CLI\.   |  March 31, 2022  | 
|  Amazon RDS automatically upgrades PostgreSQL 9\.6 instances to version 12\. If you restore a PostgreSQL 9\.6 database snapshot, Amazon RDS automatically upgrades the restored database to PostgreSQL 12\.   |  April 26, 2022  | 

## Deprecated versions for Amazon RDS for PostgreSQL<a name="PostgreSQL.Concepts.General.DeprecatedVersions"></a>

RDS for PostgreSQL 9\.5 is deprecated as of March, 2021\. For more information about RDS for PostgreSQL 9\.5 deprecation, see [ Upgrading from Amazon RDS for PostgreSQL version 9\.5](http://aws.amazon.com/blogs/database/upgrading-from-amazon-rds-for-postgresql-version-9-5/)\.

To learn more about deprecation policy for RDS for PostgreSQL, see [Amazon RDS FAQs](http://aws.amazon.com/rds/faqs/)\. For more information about PostgreSQL versions, see [Versioning Policy](https://www.postgresql.org/support/versioning/) in the PostgreSQL documentation\.

## Supported PostgreSQL extension versions<a name="PostgreSQL.Concepts.General.FeatureSupport.Extensions"></a>

RDS for PostgreSQL supports many PostgreSQL extensions\. The PostgreSQL community sometimes refers to these as modules\. Extensions expand on the functionality provided by the PostgreSQL engine\. You can find a list of extensions supported by Amazon RDS in the default DB parameter group for that PostgreSQL version\. You can also see the current extensions list using `psql` by showing the `rds.extensions` parameter as in the following example\.

```
SHOW rds.extensions; 
```

**Note**  
Parameters added in a minor version release might display inaccurately when using the `rds.extensions` parameter in `psql`\. 

For details about the PostgreSQL extensions that are supported on Amazon RDS, see [PostgreSQL extensions supported on Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-extensions.html) in *Amazon RDS for PostgreSQL Release Notes*\.

**Contents**
+ [Restricting installation of PostgreSQL extensions](#PostgreSQL.Concepts.General.FeatureSupport.Extensions.Restriction)
+ [PostgreSQL trusted extensions](#PostgreSQL.Concepts.General.Extensions.Trusted)

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

## Working with PostgreSQL features supported by Amazon RDS for PostgreSQL<a name="PostgreSQL.Concepts.General.FeatureSupport"></a>

Amazon RDS for PostgreSQL supports many of the most common PostgreSQL features and much common functionality\. For example, PostgreSQL has an autovacuum feature that performs routine maintenance on the database\. This feature is active by default\. Although you can turn off this feature, we highly recommend that you keep it on\. Understanding this feature and what you can do to make sure it works as it should is a basic task of any DBA\. For more information about the autovacuum, see [Working with the PostgreSQL autovacuum on Amazon RDS for PostgreSQL](Appendix.PostgreSQL.CommonDBATasks.Autovacuum.md)\. To learn more about other common DBA tasks, [Common DBA tasks for Amazon RDS for PostgreSQL](Appendix.PostgreSQL.CommonDBATasks.md)\. 

RDS for PostgreSQL also supports extensions that add important functionality to the DB instance\. For example, you can use the PostGIS extension to work with spatial data, or use the pg\_cron extension to schedule maintenance from within the instance\. For more information about PostgreSQL extensions, see [Using PostgreSQL extensions with Amazon RDS for PostgreSQL](Appendix.PostgreSQL.CommonDBATasks.Extensions.md)\. 

Foreign data wrappers are a specific type of extension designed to let your RDS for PostgreSQL DB instance work with other commercial databases or data types\. For more information about foreign data wrappers supported by RDS for PostgreSQL, see [Working with the supported foreign data wrappers for Amazon RDS for PostgreSQL](Appendix.PostgreSQL.CommonDBATasks.Extensions.foreign-data-wrappers.md)\. 

Following, you can find information about some PostgreSQL features supported by RDS for PostgreSQL\. 

**Topics**
+ [Custom data types and enumerations with RDS for PostgreSQL](#PostgreSQL.Concepts.General.FeatureSupport.AlterEnum)
+ [Event triggers for RDS for PostgreSQL](#PostgreSQL.Concepts.General.FeatureSupport.EventTriggers)
+ [Huge pages for RDS for PostgreSQL](#PostgreSQL.Concepts.General.FeatureSupport.HugePages)
+ [Performing logical replication for Amazon RDS for PostgreSQL](#PostgreSQL.Concepts.General.FeatureSupport.LogicalReplication)
+ [RAM disk for the stats\_temp\_directory](#PostgreSQL.Concepts.General.FeatureSupport.RamDisk)
+ [Tablespaces for RDS for PostgreSQL](#PostgreSQL.Concepts.General.FeatureSupport.Tablespaces)

### Custom data types and enumerations with RDS for PostgreSQL<a name="PostgreSQL.Concepts.General.FeatureSupport.AlterEnum"></a>

PostgreSQL supports creating custom data types and working with enumerations\. For more information about creating and working with enumerations and other data types, see [Enumerated types](https://www.postgresql.org/docs/14/datatype-enum.html) in the PostgreSQL documentation\. 

The following is an example of creating a type as an enumeration and then inserting values into a table\. 

```
CREATE TYPE rainbow AS ENUM ('red', 'orange', 'yellow', 'green', 'blue', 'purple');
CREATE TYPE
CREATE TABLE t1 (colors rainbow);
CREATE TABLE
INSERT INTO t1 VALUES ('red'), ( 'orange');
INSERT 0 2
SELECT * from t1;
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

### Event triggers for RDS for PostgreSQL<a name="PostgreSQL.Concepts.General.FeatureSupport.EventTriggers"></a>

All current PostgreSQL versions support event triggers, and so do all available versions of RDS for PostgreSQL\. You can use the main user account \(default, `postgres`\) to create, modify, rename, and delete event triggers\. Event triggers are at the DB instance level, so they can apply to all databases on an instance\.

For example, the following code creates an event trigger that prints the current user at the end of every data definition language \(DDL\) command\.

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

For more information about PostgreSQL event triggers, see [Event triggers](https://www.postgresql.org/docs/current/static/event-triggers.html) in the PostgreSQL documentation\.

There are several limitations to using PostgreSQL event triggers on Amazon RDS\. These include the following:
+ You can't create event triggers on read replicas\. You can, however, create event triggers on a read replica source\. The event triggers are then copied to the read replica\. The event triggers on the read replica don't fire on the read replica when changes are pushed from the source\. However, if the read replica is promoted, the existing event triggers fire when database operations occur\.
+ To perform a major version upgrade to a PostgreSQL DB instance that uses event triggers, make sure to delete the event triggers before you upgrade the instance\.

### Huge pages for RDS for PostgreSQL<a name="PostgreSQL.Concepts.General.FeatureSupport.HugePages"></a>

*Huge pages* are a memory management feature that reduces overhead when a DB instance is working with large contiguous chunks of memory, such as that used by shared buffers\. This PostgreSQL feature is supported by all currently available RDS for PostgreSQL versions\. You allocate huge pages for your application by using calls to `mmap` or `SYSV` shared memory\. RDS for PostgreSQL supports both 4\-KB and 2\-MB page sizes\. 

You can turn huge pages on or off by changing the value of the `huge_pages` parameter\. The feature is turned on by default for all DB instance classes other than micro, small, and medium DB instance classes\.

**Note**  
Huge pages aren't supported for db\.m1, db\.m2, and db\.m3 DB instance classes\.

RDS for PostgreSQL uses huge pages based on the available shared memory\. If the DB instance can't use huge pages due to shared memory constraints, Amazon RDS prevents the DB instance from starting\. In this case, Amazon RDS sets the status of the DB instance to an incompatible parameters state\. If this occurs, you can set the `huge_pages` parameter to `off` to allow Amazon RDS to start the DB instance\.

The `shared_buffers` parameter is key to setting the shared memory pool that is required for using huge pages\. The default value for the `shared_buffers` parameter uses a database parameters macro\. This macro sets a percentage of the total 8 KB pages available for the DB instance's memory\. When you use huge pages, those pages are located with the huge pages\. Amazon RDS puts a DB instance into an incompatible parameters state if the shared memory parameters are set to require more than 90 percent of the DB instance memory\.

To learn more about PostgreSQL memory management, see [Resource Consumption](https://www.postgresql.org/docs/current/static/runtime-config-resource.html) in the PostgreSQL documentation\.

### Performing logical replication for Amazon RDS for PostgreSQL<a name="PostgreSQL.Concepts.General.FeatureSupport.LogicalReplication"></a>

Starting with version 10\.4, Amazon RDS for PostgreSQL supports the publication and subscription SQL syntax that was first introduced in PostgreSQL 10\. To learn more, see [Logical replication](https://www.postgresql.org/docs/current/logical-replication.html) in the PostgreSQL documentation\. 

Following, you can find information about setting up logical replication for an RDS for PostgreSQL DB instance\. 

**Topics**
+ [Understanding logical replication and logical decoding](#PostgreSQL.Concepts.General.FeatureSupport.LogicalDecoding)
+ [Working with logical replication slots](#PostgreSQL.Concepts.General.FeatureSupport.LogicalReplicationSlots)

#### Understanding logical replication and logical decoding<a name="PostgreSQL.Concepts.General.FeatureSupport.LogicalDecoding"></a>

RDS for PostgreSQL supports the streaming of write\-ahead log \(WAL\) changes using PostgreSQL's logical replication slots\. It also supports using logical decoding\. You can set up logical replication slots on your instance and stream database changes through these slots to a client such as `pg_recvlogical`\. You create logical replication slots at the database level, and they support replication connections to a single database\. 

The most common clients for PostgreSQL logical replication are AWS Database Migration Service or a custom\-managed host on an Amazon EC2 instance\. The logical replication slot has no information about the receiver of the stream\. Also, there's no requirement that the target be a replica database\. If you set up a logical replication slot and don't read from the slot, data can be written and quickly fill up your DB instance's storage\.

You turn on PostgreSQL logical replication and logical decoding for Amazon RDS with a parameter, a replication connection type, and a security role\. The client for logical decoding can be any client that can establish a replication connection to a database on a PostgreSQL DB instance\. 

**To turn on logical decoding for an RDS for PostgreSQL DB instance**

1. Make sure that the user account that you're using has these roles:
   + The `rds_superuser` role so you can turn on logical replication 
   + The `rds_replication` role to grant permissions to manage logical slots and to stream data using logical slots

1. Set the `rds.logical_replication` static parameter to 1\. As part of applying this parameter, also set the parameters `wal_level`, `max_wal_senders`, `max_replication_slots`, and `max_connections`\. These parameter changes can increase WAL generation, so set the `rds.logical_replication` parameter only when you are using logical slots\.

1. Reboot the DB instance for the static `rds.logical_replication` parameter to take effect\.

1. Create a logical replication slot as explained in the next section\. This process requires that you specify a decoding plugin\. Currently, RDS for PostgreSQL supports the test\_decoding and wal2json output plugins that ship with PostgreSQL\.

For more information on PostgreSQL logical decoding, see the [ PostgreSQL documentation](https://www.postgresql.org/docs/current/static/logicaldecoding-explanation.html)\.

#### Working with logical replication slots<a name="PostgreSQL.Concepts.General.FeatureSupport.LogicalReplicationSlots"></a>

You can use SQL commands to work with logical slots\. For example, the following command creates a logical slot named `test_slot` using the default PostgreSQL output plugin `test_decoding`\.

```
SELECT * FROM pg_create_logical_replication_slot('test_slot', 'test_decoding');
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
pg_drop_replication_slot
-----------------------
(1 row)
```

For more examples on working with logical replication slots, see [ Logical decoding examples](https://www.postgresql.org/docs/9.5/static/logicaldecoding-example.html) in the PostgreSQL documentation\.

After you create the logical replication slot, you can start streaming\. The following example shows how logical decoding is controlled over the streaming replication protocol\. This example uses the program pg\_recvlogical, which is included in the PostgreSQL distribution\. Doing this requires that client authentication is set up to allow replication connections\.

```
pg_recvlogical -d postgres --slot test_slot -U postgres
    --host -instance-name.111122223333.aws-region.rds.amazonaws.com 
    -f -  --start
```

To see the contents of the `pg_replication_origin_status` view, query the `pg_show_replication_origin_status` function\.

```
SELECT * FROM pg_show_replication_origin_status();
local_id | external_id | remote_lsn | local_lsn
----------+-------------+------------+-----------
(0 rows)
```

### RAM disk for the stats\_temp\_directory<a name="PostgreSQL.Concepts.General.FeatureSupport.RamDisk"></a>

You can use the RDS for PostgreSQL parameter `rds.pg_stat_ramdisk_size` to specify the system memory allocated to a RAM disk for storing the PostgreSQL `stats_temp_directory`\. The RAM disk parameter is available for all PostgreSQL versions on Amazon RDS\. 

Under certain workloads, setting this parameter can improve performance and decrease I/O requirements\. For more information about the `stats_temp_directory`, see [ the PostgreSQL documentation\.](https://www.postgresql.org/docs/current/static/runtime-config-statistics.html#GUC-STATS-TEMP-DIRECTORY)\.

To set up a RAM disk for your `stats_temp_directory`, set the `rds.pg_stat_ramdisk_size` parameter to a nonzero value in the parameter group used by your DB instance\. The parameter value is in MB\. You must reboot the DB instance before the change takes effect\. For information about setting parameters, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\.

For example, the following AWS CLI command sets the RAM disk parameter to 256 MB\.

```
aws rds modify-db-parameter-group \
    --db-parameter-group-name pg-95-ramdisk-testing \
    --parameters "ParameterName=rds.pg_stat_ramdisk_size, ParameterValue=256, ApplyMethod=pending-reboot"
```

After you reboot, run the following command to see the status of the `stats_temp_directory`\.

```
postgres=> SHOW stats_temp_directory;
```

 The command should return the following\.

```
stats_temp_directory
---------------------------
/rdsdbramdisk/pg_stat_tmp
(1 row)
```

### Tablespaces for RDS for PostgreSQL<a name="PostgreSQL.Concepts.General.FeatureSupport.Tablespaces"></a>

RDS for PostgreSQL supports tablespaces for compatibility\. Because all storage is on a single logical volume, you can't use tablespaces for I/O splitting or isolation\. Our benchmarks and experience indicate that a single logical volume is the best setup for most use cases\. 

To create and use tablespaces with your RDS for PostgreSQL DB instance requires the `rds_superuser` role\. Your RDS for PostgreSQL DB instance's main user account \(default name, `postgres`\) is a member of this role\. For more information, see [Understanding the rds\_superuser role](Appendix.PostgreSQL.CommonDBATasks.md#Appendix.PostgreSQL.CommonDBATasks.Roles)\. 

If you specify a file name when you create a tablespace, the path prefix is `/rdsdbdata/db/base/tablespace`\. The following example places tablespace files in `/rdsdbdata/db/base/tablespace/data`\. This example assumes that a `dbadmin` user \(role\) exists and that it's been granted the `rds_superuser` role needed to work with tablespaces\.

```
postgres=> CREATE TABLESPACE act_data
  OWNER dbadmin
  LOCATION '/data';
CREATE TABLESPACE
```

To learn more about PostgreSQL tablespaces, see [Tablespaces](https://www.postgresql.org/docs/current/manage-ag-tablespaces.html) in the PostgreSQL documentation\.