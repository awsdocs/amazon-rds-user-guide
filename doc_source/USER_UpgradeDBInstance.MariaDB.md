# Upgrading the MariaDB DB Engine<a name="USER_UpgradeDBInstance.MariaDB"></a>

When Amazon RDS supports a new version of a database engine, you can upgrade your DB instances to the new version\. There are two kinds of upgrades: major version upgrades and minor version upgrades\. You must modify the DB instance manually to perform a major version upgrade\. Minor version upgrades occur automatically if you enable auto minor version upgrades on your DB instance\. In all other cases, you must modify the DB instance manually to perform a minor version upgrade\. 

For more information about MariaDB supported versions and version management, see [MariaDB on Amazon RDS Versions](CHAP_MariaDB.md#MariaDB.Concepts.VersionMgmt)\. 

**Topics**
+ [Overview of Upgrading](#USER_UpgradeDBInstance.MariaDB.Overview)
+ [Upgrading a MariaDB DB Instance](#USER_UpgradeDBInstance.MariaDB.Upgrading)

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