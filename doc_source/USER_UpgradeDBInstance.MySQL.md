# Upgrading the MySQL DB engine<a name="USER_UpgradeDBInstance.MySQL"></a>

When Amazon RDS supports a new version of a database engine, you can upgrade your DB instances to the new version\. There are two kinds of upgrades for MySQL DB instances: major version upgrades and minor version upgrades\. 

*Major version upgrades* can contain database changes that are not backward\-compatible with existing applications\. As a result, you must manually perform major version upgrades of your DB instances\. You can initiate a major version upgrade by modifying your DB instance\. However, before you perform a major version upgrade, we recommend that you follow the instructions in [Major version upgrades for MySQL](#USER_UpgradeDBInstance.MySQL.Major)\. 

In contrast, *minor version upgrades* include only changes that are backward\-compatible with existing applications\. You can initiate a minor version upgrade manually by modifying your DB instance\. Or you can enable the **Auto minor version upgrade** option when creating or modifying a DB instance\. Doing so means that your DB instance is automatically upgraded after Amazon RDS tests and approves the new version\. For information about performing an upgrade, see [Upgrading a DB instance engine version](USER_UpgradeDBInstance.Upgrading.md)\.

If your MySQL DB instance is using read replicas, you must upgrade all of the read replicas before upgrading the source instance\. If your DB instance is in a Multi\-AZ deployment, both the primary and standby replicas are upgraded\. Your DB instance will not be available until the upgrade is complete\. 

Database engine upgrades require downtime\. The duration of the downtime varies based on the size of your DB instance\.

**Tip**  
You can minimize the downtime required for DB instance upgrade by using a blue/green deployment\. For more information, see [Using Amazon RDS Blue/Green Deployments for database updates](blue-green-deployments.md)\.

**Topics**
+ [Overview of upgrading](#USER_UpgradeDBInstance.MySQL.Overview)
+ [Major version upgrades for MySQL](#USER_UpgradeDBInstance.MySQL.Major)
+ [Testing an upgrade](#USER_UpgradeDBInstance.MySQL.UpgradeTesting)
+ [Upgrading a MySQL DB instance](#USER_UpgradeDBInstance.MySQL.Upgrading)
+ [Automatic minor version upgrades for MySQL](#USER_UpgradeDBInstance.MySQL.Minor)
+ [Using a read replica to reduce downtime when upgrading a MySQL database](#USER_UpgradeDBInstance.MySQL.ReducedDowntime)

## Overview of upgrading<a name="USER_UpgradeDBInstance.MySQL.Overview"></a>

When you use the AWS Management Console to upgrade a DB instance, it shows the valid upgrade targets for the DB instance\. You can also use the following AWS CLI command to identify the valid upgrade targets for a DB instance:

For Linux, macOS, or Unix:

```
aws rds describe-db-engine-versions \
  --engine mysql \
  --engine-version version-number \
  --query "DBEngineVersions[*].ValidUpgradeTarget[*].{EngineVersion:EngineVersion}" --output text
```

For Windows:

```
aws rds describe-db-engine-versions ^
  --engine mysql ^
  --engine-version version-number ^
  --query "DBEngineVersions[*].ValidUpgradeTarget[*].{EngineVersion:EngineVersion}" --output text
```

For example, to identify the valid upgrade targets for a MySQL version 8\.0\.23 DB instance, run the following AWS CLI command:

For Linux, macOS, or Unix:

```
aws rds describe-db-engine-versions \
  --engine mysql \
  --engine-version 8.0.23 \
  --query "DBEngineVersions[*].ValidUpgradeTarget[*].{EngineVersion:EngineVersion}" --output text
```

For Windows:

```
aws rds describe-db-engine-versions ^
  --engine mysql ^
  --engine-version 8.0.23 ^
  --query "DBEngineVersions[*].ValidUpgradeTarget[*].{EngineVersion:EngineVersion}" --output text
```

Amazon RDS takes two DB snapshots during the upgrade process\. The first DB snapshot is of the DB instance before any upgrade changes have been made\. If the upgrade doesn't work for your databases, you can restore this snapshot to create a DB instance running the old version\. The second DB snapshot is taken when the upgrade completes\. RDS takes these snapshots regardless of whether AWS Backup manages the backups for the DB instance\. 

**Note**  
Amazon RDS only takes DB snapshots if you have set the backup retention period for your DB instance to a number greater than 0\. To change your backup retention period, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\. 

After the upgrade is complete, you can't revert to the previous version of the database engine\. If you want to return to the previous version, restore the first DB snapshot taken to create a new DB instance\. 

You control when to upgrade your DB instance to a new version supported by Amazon RDS\. This level of control helps you maintain compatibility with specific database versions and test new versions with your application before deploying in production\. When you are ready, you can perform version upgrades at the times that best fit your schedule\. 

If your DB instance is using read replication, you must upgrade all of the Read Replicas before upgrading the source instance\. 

If your DB instance is in a Multi\-AZ deployment, both the primary and standby DB instances are upgraded\. The primary and standby DB instances are upgraded at the same time and you will experience an outage until the upgrade is complete\. The time for the outage varies based on your database engine, engine version, and the size of your DB instance\. 

## Major version upgrades for MySQL<a name="USER_UpgradeDBInstance.MySQL.Major"></a>

Amazon RDS supports the following in\-place upgrades for major versions of the MySQL database engine:
+ MySQL 5\.6 to MySQL 5\.7
+ MySQL 5\.7 to MySQL 8\.0

**Note**  
You can only create MySQL version 5\.7 and 8\.0 DB instances with latest\-generation and current\-generation DB instance classes, in addition to the db\.m3 previous\-generation DB instance class\.   
In some cases, you want to upgrade a MySQL version 5\.6 DB instance running on a previous\-generation DB instance class \(other than db\.m3\) to a MySQL version 5\.7 DB instance\. In these cases, first modify the DB instance to use a latest\-generation or current\-generation DB instance class\. After you do this, you can then modify the DB instance to use the MySQL version 5\.7 database engine\. For information on Amazon RDS DB instance classes, see [DB instance classes](Concepts.DBInstanceClass.md)\.

**Topics**
+ [Overview of MySQL major version upgrades](#USER_UpgradeDBInstance.MySQL.Major.Overview)
+ [Upgrades to MySQL version 5\.7 might be slow](#USER_UpgradeDBInstance.MySQL.DateTime57)
+ [Prechecks for upgrades from MySQL 5\.7 to 8\.0](#USER_UpgradeDBInstance.MySQL.57to80Prechecks)
+ [Rollback after failure to upgrade from MySQL 5\.7 to 8\.0](#USER_UpgradeDBInstance.MySQL.Major.RollbackAfterFailure)

### Overview of MySQL major version upgrades<a name="USER_UpgradeDBInstance.MySQL.Major.Overview"></a>

Major version upgrades can contain database changes that are not backward\-compatible with existing applications\. As a result, Amazon RDS doesn't apply major version upgrades automatically; you must manually modify your DB instance\. We recommend that you thoroughly test any upgrade before applying it to your production instances\. 

To perform a major version upgrade for a MySQL version 5\.6 DB instance on Amazon RDS to MySQL version 5\.7 or later, first perform any available OS updates\. After OS updates are complete, upgrade to each major version: 5\.6 to 5\.7 and then 5\.7 to 8\.0\. MySQL DB instances created before April 24, 2014, show an available OS update until the update has been applied\. For more information on OS updates, see [Applying updates for a DB instance](USER_UpgradeDBInstance.Maintenance.md#USER_UpgradeDBInstance.OSUpgrades)\. 

During a major version upgrade of MySQL, Amazon RDS runs the MySQL binary `mysql_upgrade` to upgrade tables, if necessary\. Also, Amazon RDS empties the `slow_log` and `general_log` tables during a major version upgrade\. To preserve log information, save the log contents before the major version upgrade\. 

MySQL major version upgrades typically complete in about 10 minutes\. Some upgrades might take longer because of the DB instance class size or because the instance doesn't follow certain operational guidelines in [Best practices for Amazon RDS](CHAP_BestPractices.md)\. If you upgrade a DB instance from the Amazon RDS console, the status of the DB instance indicates when the upgrade is complete\. If you upgrade using the AWS Command Line Interface \(AWS CLI\), use the [describe\-db\-instances](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instances.html) command and check the `Status` value\. 

### Upgrades to MySQL version 5\.7 might be slow<a name="USER_UpgradeDBInstance.MySQL.DateTime57"></a>

MySQL version 5\.6\.4 introduced a new date and time format for the `datetime`, `time`, and `timestamp` columns that allows fractional components in date and time values\. When upgrading a DB instance to MySQL version 5\.7, MySQL forces the conversion of all date and time column types to the new format\. 

Because this conversion rebuilds your tables, it might take a considerable amount of time to complete the DB instance upgrade\. The forced conversion occurs for any DB instances that are running a version before MySQL version 5\.6\.4\. It also occurs for any DB instances that were upgraded from a version before MySQL version 5\.6\.4 to a version other than 5\.7\.

If your DB instance runs a version before MySQL version 5\.6\.4, or was upgraded from a version before 5\.6\.4, we recommend an extra step\. In these cases, we recommend that you convert the `datetime`, `time`, and `timestamp` columns in your database before upgrading your DB instance to MySQL version 5\.7\. This conversion can significantly reduce the amount of time required to upgrade the DB instance to MySQL version 5\.7\. To upgrade your date and time columns to the new format, issue the `ALTER TABLE <table_name> FORCE;` command for each table that contains date or time columns\. Because altering a table locks the table as read\-only, we recommend that you perform this update during a maintenance window\.

To find all tables in your database that have `datetime`, `time`, or `timestamp` columns and create an `ALTER TABLE <table_name> FORCE;` command for each table, use the following query\.

```
SET show_old_temporals = ON;
   SELECT table_schema, table_name,column_name, column_type
   FROM information_schema.columns
   WHERE column_type LIKE '%/* 5.5 binary format */';
   SET show_old_temporals = OFF;
```

### Prechecks for upgrades from MySQL 5\.7 to 8\.0<a name="USER_UpgradeDBInstance.MySQL.57to80Prechecks"></a>

MySQL 8\.0 includes a number of incompatibilities with MySQL 5\.7\. These incompatibilities can cause problems during an upgrade from MySQL 5\.7 to MySQL 8\.0\. So, some preparation might be required on your database for the upgrade to be successful\. The following is a general list of these incompatibilities:
+ There must be no tables that use obsolete data types or functions\.
+ There must be no orphan \*\.frm files\.
+ Triggers must not have a missing or empty definer or an invalid creation context\.
+ There must be no partitioned table that uses a storage engine that does not have native partitioning support\.
+ There must be no keyword or reserved word violations\. Some keywords might be reserved in MySQL 8\.0 that were not reserved previously\.

  For more information, see [Keywords and reserved words](https://dev.mysql.com/doc/refman/8.0/en/keywords.html) in the MySQL documentation\.
+ There must be no tables in the MySQL 5\.7 `mysql` system database that have the same name as a table used by the MySQL 8\.0 data dictionary\.
+ There must be no obsolete SQL modes defined in your `sql_mode` system variable setting\.
+ There must be no tables or stored procedures with individual `ENUM` or `SET` column elements that exceed 255 characters or 1020 bytes in length\.
+ Before upgrading to MySQL 8\.0\.13 or higher, there must be no table partitions that reside in shared InnoDB tablespaces\.
+ There must be no queries and stored program definitions from MySQL 8\.0\.12 or lower that use `ASC` or `DESC` qualifiers for `GROUP BY` clauses\.
+ Your MySQL 5\.7 installation must not use features that are not supported in MySQL 8\.0\.

  For more information, see [ Features removed in MySQL 8\.0](https://dev.mysql.com/doc/refman/8.0/en/mysql-nutshell.html#mysql-nutshell-removals) in the MySQL documentation\.
+ There must be no foreign key constraint names longer than 64 characters\.
+ For improved Unicode support, consider converting objects that use the `utf8mb3` charset to use the `utf8mb4` charset\. The `utf8mb3` character set is deprecated\. Also, consider using `utf8mb4` for character set references instead of `utf8`, because currently `utf8` is an alias for the `utf8mb3` charset\.

  For more information, see [ The utf8mb3 character set \(3\-byte UTF\-8 unicode encoding\)](https://dev.mysql.com/doc/refman/8.0/en/charset-unicode-utf8mb3.html) in the MySQL documentation\.

When you start an upgrade from MySQL 5\.7 to 8\.0, Amazon RDS runs prechecks automatically to detect these incompatibilities\. For information about upgrading to MySQL 8\.0, see [Upgrading MySQL](https://dev.mysql.com/doc/refman/8.0/en/upgrading.html) in the MySQL documentation\.

These prechecks are mandatory\. You can't choose to skip them\. The prechecks provide the following benefits:
+ They enable you to avoid unplanned downtime during the upgrade\.
+ If there are incompatibilities, Amazon RDS prevents the upgrade and provides a log for you to learn about them\. You can then use the log to prepare your database for the upgrade to MySQL 8\.0 by reducing the incompatibilities\. For detailed information about removing incompatibilities, see [ Preparing your installation for upgrade](https://dev.mysql.com/doc/refman/8.0/en/upgrade-prerequisites.html) in the MySQL documentation and [ Upgrading to MySQL 8\.0? here is what you need to know\.\.\.](https://mysqlserverteam.com/upgrading-to-mysql-8-0-here-is-what-you-need-to-know/) on the MySQL Server Blog\.

The prechecks include some that are included with MySQL and some that were created specifically by the Amazon RDS team\. For information about the prechecks provided by MySQL, see [Upgrade checker utility](https://dev.mysql.com/doc/mysql-shell/8.0/en/mysql-shell-utilities-upgrade.html)\.

The prechecks run before the DB instance is stopped for the upgrade, meaning that they don't cause any downtime when they run\. If the prechecks find an incompatibility, Amazon RDS automatically cancels the upgrade before the DB instance is stopped\. Amazon RDS also generates an event for the incompatibility\. For more information about Amazon RDS events, see [Working with Amazon RDS event notification](USER_Events.md)\.

Amazon RDS records detailed information about each incompatibility in the log file `PrePatchCompatibility.log`\. In most cases, the log entry includes a link to the MySQL documentation for correcting the incompatibility\. For more information about viewing log files, see [Viewing and listing database log files](USER_LogAccess.Procedural.Viewing.md)\.

Due to the nature of the prechecks, they analyze the objects in your database\. This analysis results in resource consumption and increases the time for the upgrade to complete\.

**Note**  
Amazon RDS runs all of these prechecks only for an upgrade from MySQL 5\.7 to MySQL 8\.0\. For an upgrade from MySQL 5\.6 to MySQL 5\.7, prechecks are limited to confirming that there are no orphan tables and that there is enough storage space to rebuild tables\. Prechecks aren't run for upgrades to releases lower than MySQL 5\.7\. 

### Rollback after failure to upgrade from MySQL 5\.7 to 8\.0<a name="USER_UpgradeDBInstance.MySQL.Major.RollbackAfterFailure"></a>

When you upgrade a DB instance from MySQL version 5\.7 to MySQL version 8\.0, the upgrade can fail\. In particular, it can fail if the data dictionary contains incompatibilities that weren't captured by the prechecks\. In this case, the database fails to start up successfully in the new MySQL 8\.0 version\. At this point, Amazon RDS rolls back the changes performed for the upgrade\. After the rollback, the MySQL DB instance is running MySQL version 5\.7\. When an upgrade fails and is rolled back, Amazon RDS generates an event with the event ID RDS\-EVENT\-0188\.

Typically, an upgrade fails because there are incompatibilities in the metadata between the databases in your DB instance and the target MySQL version\. When an upgrade fails, you can view the details about these incompatibilities in the `upgradeFailure.log` file\. Resolve the incompatibilities before attempting to upgrade again\.

During an unsuccessful upgrade attempt and rollback, your DB instance is restarted\. Any pending parameter changes are applied during the restart and persist after the rollback\.

For more information about upgrading to MySQL 8\.0, see the following topics in the MySQL documentation:
+ [ Preparing Your Installation for Upgrade](https://dev.mysql.com/doc/refman/8.0/en/upgrade-prerequisites.html)
+ [ Upgrading to MySQL 8\.0? Here is what you need to know…](https://mysqlserverteam.com/upgrading-to-mysql-8-0-here-is-what-you-need-to-know/)

**Note**  
Currently, automatic rollback after upgrade failure is supported only for MySQL 5\.7 to 8\.0 major version upgrades\.

## Testing an upgrade<a name="USER_UpgradeDBInstance.MySQL.UpgradeTesting"></a>

Before you perform a major version upgrade on your DB instance, thoroughly test your database for compatibility with the new version\. In addition, thoroughly test all applications that access the database for compatibility with the new version\. We recommend that you use the following procedure\. 

**To test a major version upgrade**

1. Review the upgrade documentation for the new version of the database engine to see if there are compatibility issues that might affect your database or applications: 
   +  [Changes in MySQL 5\.6](http://dev.mysql.com/doc/refman/5.6/en/upgrading-from-previous-series.html) 
   +  [Changes in MySQL 5\.7](http://dev.mysql.com/doc/refman/5.7/en/upgrading-from-previous-series.html) 
   +  [Changes in MySQL 8\.0](http://dev.mysql.com/doc/refman/8.0/en/upgrading-from-previous-series.html) 

1. If your DB instance is a member of a custom DB parameter group, create a new DB parameter group with your existing settings that is compatible with the new major version\. Specify the new DB parameter group when you upgrade your test instance, so your upgrade testing ensures that it works correctly\. For more information about creating a DB parameter group, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\. 

1. Create a DB snapshot of the DB instance to be upgraded\. For more information, see [Creating a DB snapshot](USER_CreateSnapshot.md)\. 

1. Restore the DB snapshot to create a new test DB instance\. For more information, see [Restoring from a DB snapshot](USER_RestoreFromSnapshot.md)\. 

1. Modify this new test DB instance to upgrade it to the new version, using one of the methods detailed following\. If you created a new parameter group in step 2, specify that parameter group\. 

1. Evaluate the storage used by the upgraded instance to determine if the upgrade requires additional storage\. 

1. Run as many of your quality assurance tests against the upgraded DB instance as needed to ensure that your database and application work correctly with the new version\. Implement any new tests needed to evaluate the impact of any compatibility issues that you identified in step 1\. Test all stored procedures and functions\. Direct test versions of your applications to the upgraded DB instance\. 

1. If all tests pass, then perform the upgrade on your production DB instance\. We recommend that you don't allow write operations to the DB instance until you confirm that everything is working correctly\. 

## Upgrading a MySQL DB instance<a name="USER_UpgradeDBInstance.MySQL.Upgrading"></a>

For information about manually or automatically upgrading a MySQL DB instance, see [Upgrading a DB instance engine version](USER_UpgradeDBInstance.Upgrading.md)\.

## Automatic minor version upgrades for MySQL<a name="USER_UpgradeDBInstance.MySQL.Minor"></a>

If you specify the following settings when creating or modifying a DB instance, you can have your DB instance automatically upgraded\.
+ The **Auto minor version upgrade** setting is enabled\.
+ The **Backup retention period** setting is greater than 0\.

In the AWS Management Console, these settings are under **Additional configuration**\. The following image shows the **Auto minor version upgrade** setting\.

![\[Auto minor version upgrade setting\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/amvu.png)

For more information about these settings, see [Settings for DB instances](Overview.DBInstance.Modifying.md#USER_ModifyInstance.Settings)\.

For some RDS for MySQL major versions in some AWS Regions, one minor version is designated by RDS as the automatic upgrade version\. After a minor version has been tested and approved by Amazon RDS, the minor version upgrade occurs automatically during your maintenance window\. RDS doesn't automatically set newer released minor versions as the automatic upgrade version\. Before RDS designates a newer automatic upgrade version, several criteria are considered, such as the following:
+ Known security issues
+ Bugs in the MySQL community version
+ Overall fleet stability since the minor version was released

You can use the following AWS CLI command to determine the current automatic minor upgrade target version for a specified MySQL minor version in a specific AWS Region\. 

For Linux, macOS, or Unix:

```
aws rds describe-db-engine-versions \
--engine mysql \
--engine-version minor-version \
--region region \
--query "DBEngineVersions[*].ValidUpgradeTarget[*].{AutoUpgrade:AutoUpgrade,EngineVersion:EngineVersion}" \
--output text
```

For Windows:

```
aws rds describe-db-engine-versions ^
--engine mysql ^
--engine-version minor-version ^
--region region ^
--query "DBEngineVersions[*].ValidUpgradeTarget[*].{AutoUpgrade:AutoUpgrade,EngineVersion:EngineVersion}" ^
--output text
```

For example, the following AWS CLI command determines the automatic minor upgrade target for MySQL minor version 8\.0\.11 in the US East \(Ohio\) AWS Region \(us\-east\-2\)\.

For Linux, macOS, or Unix:

```
aws rds describe-db-engine-versions \
--engine mysql \
--engine-version 8.0.11 \
--region us-east-2 \
--query "DBEngineVersions[*].ValidUpgradeTarget[*].{AutoUpgrade:AutoUpgrade,EngineVersion:EngineVersion}" \
--output table
```

For Windows:

```
aws rds describe-db-engine-versions ^
--engine mysql ^
--engine-version 8.0.11 ^
--region us-east-2 ^
--query "DBEngineVersions[*].ValidUpgradeTarget[*].{AutoUpgrade:AutoUpgrade,EngineVersion:EngineVersion}" ^
--output table
```

Your output is similar to the following\.

```
----------------------------------
|    DescribeDBEngineVersions    |
+--------------+-----------------+
|  AutoUpgrade |  EngineVersion  |
+--------------+-----------------+
|  False       |  8.0.15         |
|  False       |  8.0.16         |
|  False       |  8.0.17         |
|  False       |  8.0.19         |
|  False       |  8.0.20         |
|  False       |  8.0.21         |
|  True        |  8.0.23         |
|  False       |  8.0.25         |
+--------------+-----------------+
```

In this example, the `AutoUpgrade` value is `True` for MySQL version 8\.0\.23\. So, the automatic minor upgrade target is MySQL version 8\.0\.23, which is highlighted in the output\.

A MySQL DB instance is automatically upgraded during your maintenance window if the following criteria are met:
+ The **Auto minor version upgrade** setting is enabled\.
+ The **Backup retention period** setting is greater than 0\.
+ The DB instance is running a minor DB engine version that is less than the current automatic upgrade minor version\.

For more information, see [Automatically upgrading the minor engine version](USER_UpgradeDBInstance.Upgrading.md#USER_UpgradeDBInstance.Upgrading.AutoMinorVersionUpgrades)\. 

## Using a read replica to reduce downtime when upgrading a MySQL database<a name="USER_UpgradeDBInstance.MySQL.ReducedDowntime"></a>

In most cases, a blue/green deployment is the best option to reduce downtime when upgrading a MySQL DB instance\. For more information, see [Using Amazon RDS Blue/Green Deployments for database updates](blue-green-deployments.md)\. 

If you can't use a blue/green deployment and your MySQL DB instance is currently in use with a production application, you can use the following procedure to upgrade the database version for your DB instance\. This procedure can reduce the amount of downtime for your application\. 

By using a read replica, you can perform most of the maintenance steps ahead of time and minimize the necessary changes during the actual outage\. With this technique, you can test and prepare the new DB instance without making any changes to your existing DB instance\.

The following procedure shows an example of upgrading from MySQL version 5\.7 to MySQL version 8\.0\. You can use the same general steps for upgrades to other major versions\. 

**Note**  
When you are upgrading from MySQL version 5\.7 to MySQL version 8\.0, complete the prechecks before performing the upgrade\. For more information, see [Prechecks for upgrades from MySQL 5\.7 to 8\.0](#USER_UpgradeDBInstance.MySQL.57to80Prechecks)\.

**To upgrade a MySQL database while a DB instance is in use**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Create a read replica of your MySQL 5\.7 DB instance\. This process creates an upgradable copy of your database\. Other read replicas of the DB instance might also exist\.

   1. In the console, choose **Databases**, and then choose the DB instance that you want to upgrade\.

   1. For **Actions**, choose **Create read replica**\.

   1. Provide a value for **DB instance identifier** for your read replica and ensure that the **DB instance class** and other settings match your MySQL 5\.7 DB instance\.

   1. Choose **Create read replica**\.

1. \(Optional\) When the read replica has been created and **Status** shows **Available**, convert the read replica into a Multi\-AZ deployment and enable backups\.

   By default, a read replica is created as a Single\-AZ deployment with backups disabled\. Because the read replica ultimately becomes the production DB instance, it is a best practice to configure a Multi\-AZ deployment and enable backups now\.

   1. In the console, choose **Databases**, and then choose the read replica that you just created\.

   1. Choose **Modify**\.

   1. For **Multi\-AZ deployment**, choose **Create a standby instance**\.

   1. For **Backup Retention Period**, choose a positive nonzero value, such as 3 days, and then choose **Continue**\.

   1. For **Scheduling of modifications**, choose **Apply immediately**\.

   1. Choose **Modify DB instance**\.

1. When the read replica **Status** shows **Available**, upgrade the read replica to MySQL 8\.0:

   1. In the console, choose **Databases**, and then choose the read replica that you just created\.

   1. Choose **Modify**\.

   1. For **DB engine version**, choose the MySQL 8\.0 version to upgrade to, and then choose **Continue**\.

   1. For **Scheduling of modifications**, choose **Apply immediately**\.

   1. Choose **Modify DB instance** to start the upgrade\. 

1. When the upgrade is complete and **Status** shows **Available**, verify that the upgraded read replica is up\-to\-date with the source MySQL 5\.7 DB instance\. To verify, connect to the read replica and run the `SHOW REPLICA STATUS` command\. If the `Seconds_Behind_Master` field is `0`, then replication is up\-to\-date\.
**Note**  
Previous versions of MySQL used `SHOW SLAVE STATUS` instead of `SHOW REPLICA STATUS`\. If you are using a MySQL version before 8\.0\.23, then use `SHOW SLAVE STATUS`\. 

1. \(Optional\) Create a read replica of your read replica\.

   If you want the DB instance to have a read replica after it is promoted to a standalone DB instance, you can create the read replica now\.

   1. In the console, choose **Databases**, and then choose the read replica that you just upgraded\.

   1. For **Actions**, choose **Create read replica**\.

   1. Provide a value for **DB instance identifier** for your read replica and ensure that the **DB instance class** and other settings match your MySQL 5\.7 DB instance\.

   1. Choose **Create read replica**\.

1. \(Optional\) Configure a custom DB parameter group for the read replica\.

   If you want the DB instance to use a custom parameter group after it is promoted to a standalone DB instance, you can create the DB parameter group now and associate it with the read replica\.

   1. Create a custom DB parameter group for MySQL 8\.0\. For instructions, see [Creating a DB parameter group](USER_WorkingWithDBInstanceParamGroups.md#USER_WorkingWithParamGroups.Creating)\.

   1. Modify the parameters that you want to change in the DB parameter group you just created\. For instructions, see [Modifying parameters in a DB parameter group](USER_WorkingWithDBInstanceParamGroups.md#USER_WorkingWithParamGroups.Modifying)\.

   1. In the console, choose **Databases**, and then choose the read replica\.

   1. Choose **Modify**\.

   1. For **DB parameter group**, choose the MySQL 8\.0 DB parameter group you just created, and then choose **Continue**\.

   1. For **Scheduling of modifications**, choose **Apply immediately**\.

   1. Choose **Modify DB instance** to start the upgrade\. 

1. Make your MySQL 8\.0 read replica a standalone DB instance\. 
**Important**  
When you promote your MySQL 8\.0 read replica to a standalone DB instance, it is no longer a replica of your MySQL 5\.7 DB instance\. We recommend that you promote your MySQL 8\.0 read replica during a maintenance window when your source MySQL 5\.7 DB instance is in read\-only mode and all write operations are suspended\. When the promotion is completed, you can direct your write operations to the upgraded MySQL 8\.0 DB instance to ensure that no write operations are lost\.  
In addition, we recommend that, before promoting your MySQL 8\.0 read replica, you perform all necessary data definition language \(DDL\) operations on your MySQL 8\.0 read replica\. An example is creating indexes\. This approach avoids negative effects on the performance of the MySQL 8\.0 read replica after it has been promoted\. To promote a read replica, use the following procedure\.

   1. In the console, choose **Databases**, and then choose the read replica that you just upgraded\.

   1. For **Actions**, choose **Promote**\.

   1. Choose **Yes** to enable automated backups for the read replica instance\. For more information, see [Working with backups](USER_WorkingWithAutomatedBackups.md)\.

   1. Choose **Continue**\.

   1. Choose **Promote Read Replica**\.

1. You now have an upgraded version of your MySQL database\. At this point, you can direct your applications to the new MySQL 8\.0 DB instance\.