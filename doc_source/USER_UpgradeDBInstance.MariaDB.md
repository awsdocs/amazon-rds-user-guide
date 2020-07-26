# Upgrading the MariaDB DB Engine<a name="USER_UpgradeDBInstance.MariaDB"></a>

When Amazon RDS supports a new version of a database engine, you can upgrade your DB instances to the new version\. There are two kinds of upgrades for MariaDB DB instances: major version upgrades and minor version upgrades\. 

*Major version upgrades* can contain database changes that are not backward\-compatible with existing applications\. As a result, you must manually perform major version upgrades of your DB instances\. You can initiate a major version upgrade by modifying your DB instance\. However, before you perform a major version upgrade, we recommend that you follow the instructions in [Overview of UpgradingOverview](#USER_UpgradeDBInstance.MariaDB.Overview)\. 

In contrast, *minor version upgrades* include only changes that are backward\-compatible with existing applications\. You can initiate a minor version upgrade manually by modifying your DB instance\. Or you can enable the **Auto minor version upgrade** option when creating or modifying a DB instance\. Doing so means that your DB instance is automatically upgraded after Amazon RDS tests and approves the new version\. For information about performing an upgrade, see [Upgrading a DB Instance Engine Version](USER_UpgradeDBInstance.Upgrading.md)\.

If your MariaDB DB instance is using read replicas, you must upgrade all of the read replicas before upgrading the source instance\. If your DB instance is in a Multi\-AZ deployment, both the writer and standby replicas are upgraded\. Your DB instance might not be available until the upgrade is complete\. 

For more information about MariaDB supported versions and version management, see [MariaDB on Amazon RDS Versions](CHAP_MariaDB.md#MariaDB.Concepts.VersionMgmt)\. 

**Topics**
+ [Overview of Upgrading](#USER_UpgradeDBInstance.MariaDB.Overview)
+ [Upgrading a MariaDB DB Instance](#USER_UpgradeDBInstance.MariaDB.Upgrading)
+ [Automatic Minor Version Upgrades for MariaDB](#USER_UpgradeDBInstance.MariaDB.Minor)

## Overview of Upgrading<a name="USER_UpgradeDBInstance.MariaDB.Overview"></a>

Major version upgrades can contain database changes that are not backward\-compatible with existing applications\. As a result, Amazon RDS doesn't apply major version upgrades automatically; you must manually modify your DB instance\. You should thoroughly test any upgrade before applying it to your production instances\. 

Unless you specify otherwise, your DB instance will automatically be upgraded to new MariaDB minor versions as they are supported by Amazon RDS\. This patching occurs during your scheduled maintenance window\. You can modify a DB instance to turn off automatic minor version upgrades\. 

Amazon RDS takes two DB snapshots during the upgrade process\. The first DB snapshot is of the DB instance before any upgrade changes have been made\. If the upgrade doesn't work for your databases, you can restore this snapshot to create a DB instance running the old version\. The second DB snapshot is taken when the upgrade completes\. 

**Note**  
Amazon RDS only takes DB snapshots if you have set the backup retention period for your DB instance to a number greater than 0\. To change your backup retention period, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\. 

After the upgrade is complete, you can't revert to the previous version of the database engine\. If you want to return to the previous version, restore the first DB snapshot taken to create a new DB instance\. 

You control when to upgrade your DB instance to a new version supported by Amazon RDS\. This level of control helps you maintain compatibility with specific database versions and test new versions with your application before deploying in production\. When you are ready, you can perform version upgrades at the times that best fit your schedule\. 

If your DB instance is using read replication, you must upgrade all of the Read Replicas before upgrading the source instance\. 

If your DB instance is in a Multi\-AZ deployment, both the primary and standby DB instances are upgraded\. The primary and standby DB instances are upgraded at the same time and you will experience an outage until the upgrade is complete\. The time for the outage varies based on your database engine, engine version, and the size of your DB instance\. 

If you are using a custom parameter group, and you perform a major version upgrade, you must specify either a default parameter group for the new DB engine version or create your own custom parameter group for the new DB engine version\. Associating the new parameter group with the DB instance requires a customer\-initiated database reboot after the upgrade completes\. The instance's parameter group status will show `pending-reboot` if the instance needs to be rebooted to apply the parameter group changes\. An instance's parameter group status can be viewed in the AWS console or by using a "describe" call such as `describe-db-instances`\.

## Upgrading a MariaDB DB Instance<a name="USER_UpgradeDBInstance.MariaDB.Upgrading"></a>

For information about manually or automatically upgrading a MariaDB DB instance, see [Upgrading a DB Instance Engine Version](USER_UpgradeDBInstance.Upgrading.md)\.

## Automatic Minor Version Upgrades for MariaDB<a name="USER_UpgradeDBInstance.MariaDB.Minor"></a>

If you enable the **Auto minor version upgrade** option when creating or modifying a DB instance, you can have your DB instance automatically upgraded\.

For some RDS for MariaDB major versions in some AWS Regions, one minor version is designated by RDS as the automatic upgrade version\. After a minor version has been tested and approved by Amazon RDS, the minor version upgrade occurs automatically during your maintenance window\. RDS doesn't automatically set newer released minor versions as the automatic upgrade version\. Before RDS designates a newer automatic upgrade version, several criteria are considered, such as the following:
+ Known security issues
+ Bugs in the MySQL community version
+ Overall fleet stability since the minor version was released

You can use the following AWS CLI command and script to determine the current automatic minor upgrade target version for a specified MariaDB minor version in a specific AWS Region\. 

```
aws rds describe-db-engine-versions --output=table --engine mariadb --engine-version minor-version --region region
```

For example, the following AWS CLI command determines the automatic minor upgrade target for MariaDB minor version 5\.7\.19 in the US East \(Ohio\) AWS Region \(us\-east\-2\)\.

```
aws rds describe-db-engine-versions --output=table --engine mysql --engine-version 10.2.21 --region us-east-2
```

Your output is similar to the following\.

```
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
|                                                                                  DescribeDBEngineVersions                                                                                   |
+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
||                                                                                     DBEngineVersions                                                                                      ||
|+---------------------------+-----------------------------+-------------------------+----------+----------------+------------+--------------------------------------+-----------------------+|
||    DBEngineDescription    | DBEngineVersionDescription  | DBParameterGroupFamily  | Engine   | EngineVersion  |  Status    | SupportsLogExportsToCloudwatchLogs   |  SupportsReadReplica  ||
|+---------------------------+-----------------------------+-------------------------+----------+----------------+------------+--------------------------------------+-----------------------+|
||  MariaDb Community Edition|  MariaDB 10.2.11            |  mariadb10.2            |  mariadb |  10.2.11       |  available |  True                                |  True                 ||
|+---------------------------+-----------------------------+-------------------------+----------+----------------+------------+--------------------------------------+-----------------------+|
|||                                                                                   ExportableLogTypes                                                                                    |||
||+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+||
|||  audit                                                                                                                                                                                  |||
|||  error                                                                                                                                                                                  |||
|||  general                                                                                                                                                                                |||
|||  slowquery                                                                                                                                                                              |||
||+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+||
|||                                                                                   ValidUpgradeTarget                                                                                    |||
||+-----------------------------+----------------------------------------+-----------------------+------------------------------------+-----------------------------------------------------+||
|||         AutoUpgrade         |              Description               |        Engine         |           EngineVersion            |                IsMajorVersionUpgrade                |||
||+-----------------------------+----------------------------------------+-----------------------+------------------------------------+-----------------------------------------------------+||
|||  False                      |  MariaDB 10.2.12                       |  mariadb              |  10.2.12                           |  False                                              |||
|||  False                      |  MariaDB 10.2.15                       |  mariadb              |  10.2.15                           |  False                                              |||
|||  True                       |  MariaDB 10.2.21                       |  mariadb              |  10.2.21                           |  False                                              |||
|||  False                      |  MariaDB 10.3.8                        |  mariadb              |  10.3.8                            |  True                                               |||
|||  False                      |  MariaDB 10.3.13                       |  mariadb              |  10.3.13                           |  True                                               |||
|||  False                      |  MariaDB 10.3.20                       |  mariadb              |  10.3.20                           |  True                                               |||
|||  False                      |  MariaDB 10.3.23                       |  mariadb              |  10.3.23                           |  True                                               |||
||+-----------------------------+----------------------------------------+-----------------------+------------------------------------+-----------------------------------------------------+||
```

In this example, the `AutoUpgrade` value is `True` for MariaDB version 10\.2\.21\. So, the automatic minor upgrade target is MariaDB version 10\.2\.21, which is highlighted in the output\.

A MariaDB DB instance is automatically upgraded during your maintenance window if the following criteria are met:
+ The DB instance has the **Auto minor version upgrade** option enabled\.
+ The DB instance is running a minor DB engine version that is less than the current automatic upgrade minor version\.

For more information, see [Automatically Upgrading the Minor Engine Version](USER_UpgradeDBInstance.Upgrading.md#USER_UpgradeDBInstance.Upgrading.AutoMinorVersionUpgrades)\. 