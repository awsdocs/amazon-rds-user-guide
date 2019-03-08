# Upgrading the MySQL DB Engine<a name="USER_UpgradeDBInstance.MySQL"></a>

When Amazon RDS supports a new version of a database engine, you can upgrade your DB instances to the new version\. There are two kinds of upgrades: major version upgrades and minor version upgrades\. In general, a major engine version upgrade can introduce changes that are not compatible with existing applications\. In contrast, a minor version upgrade includes only changes that are backward\-compatible with existing applications\. 

You must modify the DB instance manually to perform a major version upgrade\. Minor version upgrades occur automatically if you enable auto minor version upgrades on your DB instance\. In all other cases, you must modify the DB instance manually to perform a minor version upgrade\.

**Topics**
+ [Overview of Upgrading](#USER_UpgradeDBInstance.MySQL.Overview)
+ [Major Version Upgrades for MySQL](#USER_UpgradeDBInstance.MySQL.Major)
+ [Testing an Upgrade](#USER_UpgradeDBInstance.MySQL.UpgradeTesting)
+ [Upgrading a MySQL DB Instance](#USER_UpgradeDBInstance.MySQL.Upgrading)
+ [Upgrading a MySQL Database with Reduced Downtime](#USER_UpgradeDBInstance.MySQL.ReducedDowntime)

## Overview of Upgrading<a name="USER_UpgradeDBInstance.MySQL.Overview"></a>

Amazon RDS takes two DB snapshots during the upgrade process\. The first DB snapshot is of the DB instance before any upgrade changes have been made\. If the upgrade doesn't work for your databases, you can restore this snapshot to create a DB instance running the old version\. The second DB snapshot is taken when the upgrade completes\. 

**Note**  
Amazon RDS only takes DB snapshots if you have set the backup retention period for your DB instance to a number greater than 0\. To change your backup retention period, see [Modifying a DB Instance Running the MySQL Database Engine](USER_ModifyInstance.MySQL.md)\. 

After the upgrade is complete, you can't revert to the previous version of the database engine\. If you want to return to the previous version, restore the first DB snapshot taken to create a new DB instance\. 

You control when to upgrade your DB instance to a new version supported by Amazon RDS\. This level of control helps you maintain compatibility with specific database versions and test new versions with your application before deploying in production\. When you are ready, you can perform version upgrades at the times that best fit your schedule\. 

If your DB instance is using read replication, you must upgrade all of the Read Replicas before upgrading the source instance\. 

If your DB instance is in a Multi\-AZ deployment, both the primary and standby DB instances are upgraded\. The primary and standby DB instances are upgraded at the same time and you will experience an outage until the upgrade is complete\. The time for the outage varies based on the size of your DB instance\. 

## Major Version Upgrades for MySQL<a name="USER_UpgradeDBInstance.MySQL.Major"></a>

Amazon RDS supports the following in\-place upgrades for major versions of the MySQL database engine:
+ MySQL 5\.5 to MySQL 5\.6
+ MySQL 5\.6 to MySQL 5\.7
+ MySQL 5\.7 to MySQL 8\.0

**Note**  
You can only create MySQL version 5\.7 and 8\.0 DB instances with latest\-generation and current\-generation DB instance classes, in addition to the db\.m3 previous\-generation DB instance class\. If you want to upgrade a MySQL version 5\.6 DB instance running on a previous\-generation DB instance class \(other than db\.m3\) to a MySQL version 5\.7 DB instance, you must first modify the DB instance to use a latest\-generation or current\-generation DB instance class\. After the DB instance has been modified to use a latest\-generation or current\-generation DB instance class, you can then modify the DB instance to use the MySQL version 5\.7 database engine\. For information on Amazon RDS DB instance classes, see [DB Instance Class](Concepts.DBInstanceClass.md)\. 

Major version upgrades can contain database changes that are not backward\-compatible with existing applications\. As a result, Amazon RDS doesn't apply major version upgrades automatically; you must manually modify your DB instance\. You should thoroughly test any upgrade before applying it to your production instances\. 

To perform a major version upgrade for a MySQL version 5\.5 DB instance on Amazon RDS to MySQL version 5\.6 or later, you should first perform any available OS updates\. After OS updates are complete, you must upgrade to each major version: 5\.5 to 5\.6, then 5\.6 to 5\.7, and then 5\.7 to 8\.0\. MySQL DB instances created before April 24, 2014, show an available OS update until the update has been applied\. For more information on OS updates, see [Applying Updates for a DB Instance](USER_UpgradeDBInstance.Maintenance.md#USER_UpgradeDBInstance.OSUpgrades)\. 

During a major version upgrade of MySQL, Amazon RDS runs the MySQL binary `mysql_upgrade` to upgrade tables, if required\. Also, Amazon RDS empties the `slow_log` and `general_log` tables during a major version upgrade\. To preserve log information, save the log contents before the major version upgrade\. 

MySQL major version upgrades typically complete in about 10 minutes\. Some upgrades might take longer because of the DB instance class size or because the instance doesn't follow certain operational guidelines in [Best Practices for Amazon RDS](CHAP_BestPractices.md)\. If you upgrade a DB instance from the Amazon RDS console, the status of the DB instance indicates when the upgrade is complete\. If you upgrade using the AWS Command Line Interface \(AWS CLI\), use the [describe\-db\-instances](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instances.html) command and check the `Status` value\. 

If you are using a custom parameter group, you must specify either a default parameter group for the new DB engine version or create your own custom parameter group for the new DB engine version\. Associating the new parameter group with the DB instance requires a customer\-initiated database reboot after the upgrade completes\. The DB instance's parameter group status shows `pending-reboot` if the DB instance needs to be rebooted to apply the parameter group changes\. A DB instance's parameter group status can be viewed in the AWS console or by using a "describe" call such as `describe-db-instances`\.

### Upgrades to MySQL Version 5\.7 Might Be Slow<a name="USER_UpgradeDBInstance.MySQL.DateTime57"></a>

MySQL version 5\.6\.4 introduced a new date and time format for the `datetime`, `time`, and `timestamp` columns that allows fractional components in date and time values\. When upgrading a DB instance to MySQL version 5\.7, MySQL will force the conversion of all date and time column types to the new format\. Because this conversion rebuilds your tables, it might take a considerable amount of time to complete the DB instance upgrade\. The forced conversion will occur for any DB instances that are running a version prior to MySQL version 5\.6\.4, and also any DB instances that were upgraded from a version prior to MySQL version 5\.6\.4 to a version other than 5\.7\.

If your DB instance is running a version prior to MySQL version 5\.6\.4, or was upgraded from a version prior to MySQL version 5\.6\.4, then we recommend that you convert the `datetime`, `time`, and `timestamp` columns in your database before upgrading your DB instance to MySQL version 5\.7\. This conversion can significantly reduce the amount of time required to upgrade the DB instance to MySQL version 5\.7\. To upgrade your date and time columns to the new format, issue the `ALTER TABLE <table_name> FORCE;` command for each table that contains date or time columns\. Because altering a table locks the table as read\-only, we recommend that you perform this update during a maintenance window\.

You can use the following query to find all tables in your database that have columns of type datetime, time, or timestamp and to create an `ALTER TABLE <table_name> FORCE;` command for each table:

```
SELECT DISTINCT CONCAT('ALTER TABLE `',
      REPLACE(is_tables.TABLE_SCHEMA, '`', '``'), '`.`',
      REPLACE(is_tables.TABLE_NAME, '`', '``'), '` FORCE;')
    FROM information_schema.TABLES is_tables
      INNER JOIN information_schema.COLUMNS col ON col.TABLE_SCHEMA = is_tables.TABLE_SCHEMA
        AND col.TABLE_NAME = is_tables.TABLE_NAME
      LEFT OUTER JOIN information_schema.INNODB_SYS_TABLES systables ON
        SUBSTRING_INDEX(systables.NAME, '#', 1) = CONCAT(is_tables.TABLE_SCHEMA,'/',is_tables.TABLE_NAME)
      LEFT OUTER JOIN information_schema.INNODB_SYS_COLUMNS syscolumns ON
        syscolumns.TABLE_ID = systables.TABLE_ID AND syscolumns.NAME = col.COLUMN_NAME
    WHERE col.COLUMN_TYPE IN ('time','timestamp','datetime')
      AND is_tables.TABLE_TYPE = 'BASE TABLE'
      AND is_tables.TABLE_SCHEMA NOT IN ('mysql','information_schema','performance_schema')
      AND (is_tables.ENGINE = 'InnoDB' AND syscolumns.MTYPE = 6);
```

## Testing an Upgrade<a name="USER_UpgradeDBInstance.MySQL.UpgradeTesting"></a>

Before you perform a major version upgrade on your DB instance, you should thoroughly test your database, and all applications that access the database, for compatibility with the new version\. We recommend that you use the following procedure\. 

**To test a major version upgrade**

1. Review the upgrade documentation for the new version of the database engine to see if there are compatibility issues that might affect your database or applications: 
   +  [Changes in MySQL 5\.6](http://dev.mysql.com/doc/refman/5.6/en/upgrading-from-previous-series.html) 
   +  [Changes in MySQL 5\.7](http://dev.mysql.com/doc/refman/5.7/en/upgrading-from-previous-series.html) 
   +  [Changes in MySQL 8\.0](http://dev.mysql.com/doc/refman/8.0/en/upgrading-from-previous-series.html) 

1. If your DB instance is a member of a custom DB parameter group, you need to create a new DB parameter group with your existing settings that is compatible with the new major version\. Specify the new DB parameter group when you upgrade your test instance, so that your upgrade testing ensures that it works correctly\. For more information about creating a DB parameter group, see [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)\. 

1. Create a DB snapshot of the DB instance to be upgraded\. For more information, see [Creating a DB Snapshot](USER_CreateSnapshot.md)\. 

1. Restore the DB snapshot to create a new test DB instance\. For more information, see [Restoring from a DB Snapshot](USER_RestoreFromSnapshot.md)\. 

1. Modify this new test DB instance to upgrade it to the new version, using one of the methods detailed following\. If you created a new parameter group in step 2, specify that parameter group\. 

1. Evaluate the storage used by the upgraded instance to determine if the upgrade requires additional storage\. 

1. Run as many of your quality assurance tests against the upgraded DB instance as needed to ensure that your database and application work correctly with the new version\. Implement any new tests needed to evaluate the impact of any compatibility issues you identified in step 1\. Test all stored procedures and functions\. Direct test versions of your applications to the upgraded DB instance\. 

1. If all tests pass, then perform the upgrade on your production DB instance\. We recommend that you do not allow write operations to the DB instance until you confirm that everything is working correctly\. 

## Upgrading a MySQL DB Instance<a name="USER_UpgradeDBInstance.MySQL.Upgrading"></a>

For information about manually or automatically upgrading a MySQL DB instance, see [Upgrading a DB Instance Engine Version](USER_UpgradeDBInstance.Upgrading.md)\.

## Upgrading a MySQL Database with Reduced Downtime<a name="USER_UpgradeDBInstance.MySQL.ReducedDowntime"></a>

If your MySQL DB instance is currently in use with a production application, you can use the following procedure to upgrade the database version for your DB instance and reduce the amount of downtime for your application\. This procedure shows an example of upgrading from MySQL version 5\.5 to MySQL version 5\.6\. You can use the same general steps for upgrades to other major versions\. 

**To upgrade an MySQL database while a DB instance is in use**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Create a Read Replica of your MySQL 5\.5 DB instance\. This process creates an upgradable copy of your database\.

   1. On the console, choose **Databases**, and then choose the DB instance that you want to upgrade\.

   1. For **Actions**, choose **Create read replica**\.

   1. Provide a value for **DB instance identifier** for your Read Replica and ensure that the **DB instance class** and other settings match your MySQL 5\.5 DB instance\.

   1. Choose **Create read replica**\.

1. When the Read Replica has been created and **Status** shows **available**, upgrade the Read Replica to MySQL 5\.6:

   1. On the console, choose **Databases**, and then choose the Read Replica that you just created\.

   1. Choose **Modify**\.

   1. For **DB engine version**, choose the MySQL 5\.6 version to upgrade to, and then choose **Continue**\.

   1. For **Scheduling of Modifications**, choose **Apply immediately**\.

   1. Choose **Modify DB instance** to start the upgrade\. 

1. When the upgrade is complete and **Status** shows `available`, verify that the upgraded Read Replica is up to date with the master MySQL 5\.5 DB instance\. You can do this by connecting to the Read Replica and issuing the `SHOW SLAVE STATUS` command\. If the `Seconds_Behind_Master` field is `0`, then replication is up to date\. 

1. Make your MySQL 5\.6 Read Replica a master DB instance\. 
**Important**  
When you promote your MySQL 5\.6 Read Replica to a standalone, single\-AZ DB instance, it no longer is a replication replica to your MySQL 5\.5 DB instance\. We recommend that you promote your MySQL 5\.6 Read Replica during a maintenance window when your source MySQL 5\.5 DB instance is in read\-only mode and all write operations are suspended\. When the promotion is completed, you can direct your write operations to the upgraded MySQL 5\.6 DB instance to ensure that no write operations are lost\.  
In addition, we recommend that before promoting your MySQL 5\.6 Read Replica you perform all necessary data definition language \(DDL\) operations, such as creating indexes, on the MySQL 5\.6 Read Replica\. This approach avoids negative effects on the performance of the MySQL 5\.6 Read Replica after it has been promoted\. To promote a Read Replica, use the following procedure\.

   1. On the console, choose **Databases**, and then choose the Read Replica that you just upgraded\.

   1. For **Actions**, choose **Promote read replica**\.

   1. Choose **Yes** to enable automated backups for the Read Replica instance\. For more information, see [Working With Backups](USER_WorkingWithAutomatedBackups.md)\.

      Choose **Continue**\.

   1. Choose **Promote Read Replica**\.

1. You now have an upgraded version of your MySQL database\. At this point, you can direct your applications to the new MySQL 5\.6 DB instance, add Read Replicas, set up Multi\-AZ support, and so on\.