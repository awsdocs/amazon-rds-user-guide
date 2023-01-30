# Upgrading the MariaDB DB engine<a name="USER_UpgradeDBInstance.MariaDB"></a>

When Amazon RDS supports a new version of a database engine, you can upgrade your DB instances to the new version\. There are two kinds of upgrades for MariaDB DB instances: major version upgrades and minor version upgrades\. 

*Major version upgrades* can contain database changes that are not backward\-compatible with existing applications\. As a result, you must manually perform major version upgrades of your DB instances\. You can initiate a major version upgrade by modifying your DB instance\. However, before you perform a major version upgrade, we recommend that you follow the instructions in [Major version upgrades for MariaDB](#USER_UpgradeDBInstance.MariaDB.Major)\. 

In contrast, *minor version upgrades* include only changes that are backward\-compatible with existing applications\. You can initiate a minor version upgrade manually by modifying your DB instance\. Or you can enable the **Auto minor version upgrade** option when creating or modifying a DB instance\. Doing so means that your DB instance is automatically upgraded after Amazon RDS tests and approves the new version\. For information about performing an upgrade, see [Upgrading a DB instance engine version](USER_UpgradeDBInstance.Upgrading.md)\.

If your MariaDB DB instance is using read replicas, you must upgrade all of the read replicas before upgrading the source instance\. If your DB instance is in a Multi\-AZ deployment, both the writer and standby replicas are upgraded\. Your DB instance might not be available until the upgrade is complete\. 

For more information about MariaDB supported versions and version management, see [MariaDB on Amazon RDS versions](MariaDB.Concepts.VersionMgmt.md)\. 

Database engine upgrades require downtime\. The duration of the downtime varies based on the size of your DB instance\.

**Tip**  
You can minimize the downtime required for DB instance upgrade by using a blue/green deployment\. For more information, see [Using Amazon RDS Blue/Green Deployments for database updates](blue-green-deployments.md)\.

**Topics**
+ [Overview of upgrading](#USER_UpgradeDBInstance.MariaDB.Overview)
+ [Major version upgrades for MariaDB](#USER_UpgradeDBInstance.MariaDB.Major)
+ [Upgrading a MariaDB DB instance](#USER_UpgradeDBInstance.MariaDB.Upgrading)
+ [Automatic minor version upgrades for MariaDB](#USER_UpgradeDBInstance.MariaDB.Minor)
+ [Using a read replica to reduce downtime when upgrading a MariaDB database](#USER_UpgradeDBInstance.MariaDB.ReducedDowntime)

## Overview of upgrading<a name="USER_UpgradeDBInstance.MariaDB.Overview"></a>

When you use the AWS Management Console to upgrade a DB instance, it shows the valid upgrade targets for the DB instance\. You can also use the following AWS CLI command to identify the valid upgrade targets for a DB instance:

For Linux, macOS, or Unix:

```
aws rds describe-db-engine-versions \
  --engine mariadb \
  --engine-version version-number \
  --query "DBEngineVersions[*].ValidUpgradeTarget[*].{EngineVersion:EngineVersion}" --output text
```

For Windows:

```
aws rds describe-db-engine-versions ^
  --engine mariadb ^
  --engine-version version-number ^
  --query "DBEngineVersions[*].ValidUpgradeTarget[*].{EngineVersion:EngineVersion}" --output text
```

For example, to identify the valid upgrade targets for a MariaDB version 10\.5\.15 DB instance, run the following AWS CLI command:

For Linux, macOS, or Unix:

```
aws rds describe-db-engine-versions \
  --engine mariadb \
  --engine-version 10.5.15 \
  --query "DBEngineVersions[*].ValidUpgradeTarget[*].{EngineVersion:EngineVersion}" --output text
```

For Windows:

```
aws rds describe-db-engine-versions ^
  --engine mariadb ^
  --engine-version 10.5.15 ^
  --query "DBEngineVersions[*].ValidUpgradeTarget[*].{EngineVersion:EngineVersion}" --output text
```

Amazon RDS takes two DB snapshots during the upgrade process\. The first DB snapshot is of the DB instance before any upgrade changes have been made\. If the upgrade doesn't work for your databases, you can restore this snapshot to create a DB instance running the old version\. The second DB snapshot is taken when the upgrade completes\. RDS takes these snapshots regardless of whether AWS Backup manages the backups for the DB instance\. 

**Note**  
Amazon RDS only takes DB snapshots if you have set the backup retention period for your DB instance to a number greater than 0\. To change your backup retention period, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\. 

After the upgrade is complete, you can't revert to the previous version of the database engine\. If you want to return to the previous version, restore the first DB snapshot taken to create a new DB instance\. 

You control when to upgrade your DB instance to a new version supported by Amazon RDS\. This level of control helps you maintain compatibility with specific database versions and test new versions with your application before deploying in production\. When you are ready, you can perform version upgrades at the times that best fit your schedule\. 

If your DB instance is using read replication, you must upgrade all of the Read Replicas before upgrading the source instance\. 

If your DB instance is in a Multi\-AZ deployment, both the primary and standby DB instances are upgraded\. The primary and standby DB instances are upgraded at the same time and you will experience an outage until the upgrade is complete\. The time for the outage varies based on your database engine, engine version, and the size of your DB instance\. 

## Major version upgrades for MariaDB<a name="USER_UpgradeDBInstance.MariaDB.Major"></a>

Major version upgrades can contain database changes that are not backward\-compatible with existing applications\. As a result, Amazon RDS doesn't apply major version upgrades automatically\. You must manually modify your DB instance\. We recommend that you thoroughly test any upgrade before applying it to your production instances\. 

Amazon RDS supports the following in\-place upgrades for major versions of the MariaDB database engine:
+ Any MariaDB version to MariaDB 10\.6
+ MariaDB 10\.4 to MariaDB 10\.5
+ MariaDB 10\.3 to MariaDB 10\.4

To perform a major version upgrade to MariaDB version 10\.6, you can upgrade directly from any MariaDB version to version 10\.6\.

To perform a major version upgrade to a MariaDB version lower than 10\.6, upgrade to each major version in order\. For example, to upgrade from version 10\.3 to version 10\.5, upgrade in the following order: 10\.3 to 10\.4 and then 10\.4 to 10\.5\.

If you are using a custom parameter group, and you perform a major version upgrade, you must specify either a default parameter group for the new DB engine version or create your own custom parameter group for the new DB engine version\. Associating the new parameter group with the DB instance requires a customer\-initiated database reboot after the upgrade completes\. The instance's parameter group status will show `pending-reboot` if the instance needs to be rebooted to apply the parameter group changes\. An instance's parameter group status can be viewed in the AWS Management Console or by using a "describe" call such as `describe-db-instances`\.

## Upgrading a MariaDB DB instance<a name="USER_UpgradeDBInstance.MariaDB.Upgrading"></a>

For information about manually or automatically upgrading a MariaDB DB instance, see [Upgrading a DB instance engine version](USER_UpgradeDBInstance.Upgrading.md)\.

## Automatic minor version upgrades for MariaDB<a name="USER_UpgradeDBInstance.MariaDB.Minor"></a>

If you specify the following settings when creating or modifying a DB instance, you can have your DB instance automatically upgraded\.
+ The **Auto minor version upgrade** setting is enabled\.
+ The **Backup retention period** setting is greater than 0\.

In the AWS Management Console, these settings are under **Additional configuration**\. The following image shows the **Auto minor version upgrade** setting\.

![\[Auto minor version upgrade setting\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/amvu.png)

For more information about these settings, see [Settings for DB instances](Overview.DBInstance.Modifying.md#USER_ModifyInstance.Settings)\.

For some RDS for MariaDB major versions in some AWS Regions, one minor version is designated by RDS as the automatic upgrade version\. After a minor version has been tested and approved by Amazon RDS, the minor version upgrade occurs automatically during your maintenance window\. RDS doesn't automatically set newer released minor versions as the automatic upgrade version\. Before RDS designates a newer automatic upgrade version, several criteria are considered, such as the following:
+ Known security issues
+ Bugs in the MariaDB community version
+ Overall fleet stability since the minor version was released

You can use the following AWS CLI command to determine the current automatic minor upgrade target version for a specified MariaDB minor version in a specific AWS Region\. 

For Linux, macOS, or Unix:

```
aws rds describe-db-engine-versions \
--engine mariadb \
--engine-version minor-version \
--region region \
--query "DBEngineVersions[*].ValidUpgradeTarget[*].{AutoUpgrade:AutoUpgrade,EngineVersion:EngineVersion}" \
--output text
```

For Windows:

```
aws rds describe-db-engine-versions ^
--engine mariadb ^
--engine-version minor-version ^
--region region ^
--query "DBEngineVersions[*].ValidUpgradeTarget[*].{AutoUpgrade:AutoUpgrade,EngineVersion:EngineVersion}" ^
--output text
```

For example, the following AWS CLI command determines the automatic minor upgrade target for MariaDB minor version 10\.5\.12 in the US East \(Ohio\) AWS Region \(us\-east\-2\)\.

For Linux, macOS, or Unix:

```
aws rds describe-db-engine-versions \
--engine mariadb \
--engine-version 10.5.12 \
--region us-east-2 \
--query "DBEngineVersions[*].ValidUpgradeTarget[*].{AutoUpgrade:AutoUpgrade,EngineVersion:EngineVersion}" \
--output table
```

For Windows:

```
aws rds describe-db-engine-versions ^
--engine mariadb ^
--engine-version 10.5.12 ^
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
|  False       |  10.5.13        |
|  False       |  10.5.15        |
|  False       |  10.5.16        |
|  True        |  10.5.17        |
|  False       |  10.5.18        |
|  False       |  10.6.5         |
|  False       |  10.6.7         |
|  False       |  10.6.8         |
|  False       |  10.6.10        |
|  False       |  10.6.11        |
+--------------+-----------------+
```

In this example, the `AutoUpgrade` value is `True` for MariaDB version 10\.5\.17\. So, the automatic minor upgrade target is MariaDB version 10\.5\.17, which is highlighted in the output\.

A MariaDB DB instance is automatically upgraded during your maintenance window if the following criteria are met:
+ The **Auto minor version upgrade** setting is enabled\.
+ The **Backup retention period** setting is greater than 0\.
+ The DB instance is running a minor DB engine version that is less than the current automatic upgrade minor version\.

For more information, see [Automatically upgrading the minor engine version](USER_UpgradeDBInstance.Upgrading.md#USER_UpgradeDBInstance.Upgrading.AutoMinorVersionUpgrades)\. 

## Using a read replica to reduce downtime when upgrading a MariaDB database<a name="USER_UpgradeDBInstance.MariaDB.ReducedDowntime"></a>

In most cases, a blue/green deployment is the best option to reduce downtime when upgrading a MariaDB DB instance\. For more information, see [Using Amazon RDS Blue/Green Deployments for database updates](blue-green-deployments.md)\. 

If you can't use a blue/green deployment and your MariaDB DB instance is currently in use with a production application, you can use the following procedure to upgrade the database version for your DB instance\. This procedure can reduce the amount of downtime for your application\. 

By using a read replica, you can perform most of the maintenance steps ahead of time and minimize the necessary changes during the actual outage\. With this technique, you can test and prepare the new DB instance without making any changes to your existing DB instance\.

The following procedure shows an example of upgrading from MariaDB version 10\.5 to MariaDB version 10\.6\. You can use the same general steps for upgrades to other major versions\. 

**To upgrade a MariaDB database while a DB instance is in use**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Create a read replica of your MariaDB 10\.5 DB instance\. This process creates an upgradable copy of your database\. Other read replicas of the DB instance might also exist\.

   1. In the console, choose **Databases**, and then choose the DB instance that you want to upgrade\.

   1. For **Actions**, choose **Create read replica**\.

   1. Provide a value for **DB instance identifier** for your read replica and ensure that the **DB instance class** and other settings match your MariaDB 10\.5 DB instance\.

   1. Choose **Create read replica**\.

1. \(Optional\) When the read replica has been created and **Status** shows **Available**, convert the read replica into a Multi\-AZ deployment and enable backups\.

   By default, a read replica is created as a Single\-AZ deployment with backups disabled\. Because the read replica ultimately becomes the production DB instance, it is a best practice to configure a Multi\-AZ deployment and enable backups now\.

   1. In the console, choose **Databases**, and then choose the read replica that you just created\.

   1. Choose **Modify**\.

   1. For **Multi\-AZ deployment**, choose **Create a standby instance**\.

   1. For **Backup Retention Period**, choose a positive nonzero value, such as 3 days, and then choose **Continue**\.

   1. For **Scheduling of modifications**, choose **Apply immediately**\.

   1. Choose **Modify DB instance**\.

1. When the read replica **Status** shows **Available**, upgrade the read replica to MariaDB 10\.6\.

   1. In the console, choose **Databases**, and then choose the read replica that you just created\.

   1. Choose **Modify**\.

   1. For **DB engine version**, choose the MariaDB 10\.6 version to upgrade to, and then choose **Continue**\.

   1. For **Scheduling of modifications**, choose **Apply immediately**\.

   1. Choose **Modify DB instance** to start the upgrade\. 

1. When the upgrade is complete and **Status** shows **Available**, verify that the upgraded read replica is up\-to\-date with the source MariaDB 10\.5 DB instance\. To verify, connect to the read replica and run the `SHOW REPLICA STATUS` command\. If the `Seconds_Behind_Master` field is `0`, then replication is up\-to\-date\.
**Note**  
Previous versions of MariaDB used `SHOW SLAVE STATUS` instead of `SHOW REPLICA STATUS`\. If you are using a MariaDB version before 10\.6, then use `SHOW SLAVE STATUS`\. 

1. \(Optional\) Create a read replica of your read replica\.

   If you want the DB instance to have a read replica after it is promoted to a standalone DB instance, you can create the read replica now\.

   1. In the console, choose **Databases**, and then choose the read replica that you just upgraded\.

   1. For **Actions**, choose **Create read replica**\.

   1. Provide a value for **DB instance identifier** for your read replica and ensure that the **DB instance class** and other settings match your MariaDB 10\.5 DB instance\.

   1. Choose **Create read replica**\.

1. \(Optional\) Configure a custom DB parameter group for the read replica\.

   If you want the DB instance to use a custom parameter group after it is promoted to a standalone DB instance, you can create the DB parameter group now and associate it with the read replica\.

   1. Create a custom DB parameter group for MariaDB 10\.6\. For instructions, see [Creating a DB parameter group](USER_WorkingWithDBInstanceParamGroups.md#USER_WorkingWithParamGroups.Creating)\.

   1. Modify the parameters that you want to change in the DB parameter group you just created\. For instructions, see [Modifying parameters in a DB parameter group](USER_WorkingWithDBInstanceParamGroups.md#USER_WorkingWithParamGroups.Modifying)\.

   1. In the console, choose **Databases**, and then choose the read replica\.

   1. Choose **Modify**\.

   1. For **DB parameter group**, choose the MariaDB 10\.6 DB parameter group you just created, and then choose **Continue**\.

   1. For **Scheduling of modifications**, choose **Apply immediately**\.

   1. Choose **Modify DB instance** to start the upgrade\. 

1. Make your MariaDB 10\.6 read replica a standalone DB instance\. 
**Important**  
When you promote your MariaDB 10\.6 read replica to a standalone DB instance, it is no longer a replica of your MariaDB 10\.5 DB instance\. We recommend that you promote your MariaDB 10\.6 read replica during a maintenance window when your source MariaDB 10\.5 DB instance is in read\-only mode and all write operations are suspended\. When the promotion is completed, you can direct your write operations to the upgraded MariaDB 10\.6 DB instance to ensure that no write operations are lost\.  
In addition, we recommend that, before promoting your MariaDB 10\.6 read replica, you perform all necessary data definition language \(DDL\) operations on your MariaDB 10\.6 read replica\. An example is creating indexes\. This approach avoids negative effects on the performance of the MariaDB 10\.6 read replica after it has been promoted\. To promote a read replica, use the following procedure\.

   1. In the console, choose **Databases**, and then choose the read replica that you just upgraded\.

   1. For **Actions**, choose **Promote**\.

   1. Choose **Yes** to enable automated backups for the read replica instance\. For more information, see [Working with backups](USER_WorkingWithAutomatedBackups.md)\.

   1. Choose **Continue**\.

   1. Choose **Promote Read Replica**\.

1. You now have an upgraded version of your MariaDB database\. At this point, you can direct your applications to the new MariaDB 10\.6 DB instance\.