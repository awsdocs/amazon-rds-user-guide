# Amazon Aurora PostgreSQL Database Engine Updates<a name="AuroraPostgreSQL.Updates"></a>

In the following topic, you can find version and update information specific to Amazon Aurora PostgreSQL\. For more information about updates that apply generally to Aurora, see [Amazon Aurora Updates](AuroraMySQL.Updates.md)\.

## Amazon Aurora PostgreSQL Versions<a name="AuroraPostgreSQL.Updates.Versions"></a>

Amazon Aurora includes certain features that are general to Aurora and available to all Aurora DB clusters\. Aurora includes other features that are specific to a particular database engine that Aurora supports\. These features are available only to those Aurora DB clusters that use that database engine, such as Aurora PostgreSQL\.

An Aurora DB instance provides two version numbers, the Aurora version number and the Aurora database engine version number\. For more information about the Aurora version number, see [Amazon Aurora Versions](Aurora.Updates.md#Aurora.Updates.Versions)\.

You can get the Aurora database engine version number for an Aurora PostgreSQL DB instance by querying for the `SERVER_VERSION` run\-time parameter\. To get the Aurora database engine version number, use the following query\.

```
SHOW SERVER_VERSION;
```

## Amazon Aurora PostgreSQL Database Upgrades \(Patching\)<a name="AuroraPostgreSQL.Updates.Patching"></a>

When a new minor version of the Amazon Aurora PostgreSQL database engine is released, Amazon RDS schedules an automatic upgrade of the database engine for all Aurora DB clusters\. We announce automatic upgrades in the [Amazon RDS Community Forum](https://forums.aws.amazon.com/forum.jspa?forumID=60)\.

When a new patch version of the Aurora PostgreSQL database engine is released, no automatic upgrade is required\. You can choose to upgrade and apply the patch\. If you don't, the patch is applied during the next automatic upgrade for a minor version release\. 

Before automatic upgrade, new database engine releases show as an **available** maintenance upgrade for your DB cluster\. You can manually upgrade the database version for your DB cluster by applying the available maintenance action\. We encourage you to apply the update on a nonproduction instance before the automatic upgrade\. That way, you can see how changes in the new version affect your instances and applications\.

**To apply pending maintenance actions**

+ **By using the Amazon RDS Management Console** – Log on to the Amazon RDS console and choose **Clusters**\. Choose the DB cluster that shows an **available** maintenance upgrade\. Choose **Cluster Actions**\. Choose **Upgrade Now** to immediately update the database version for your DB cluster\. Or choose **Upgrade at Next Window** to update the database version for your DB cluster during the next cluster maintenance window\. 

+ **By using the AWS CLI** – Call the [apply\-pending\-maintenance\-action](http://docs.aws.amazon.com/cli/latest/reference/rds/apply-pending-maintenance-action.html) AWS CLI command and specify the Amazon Resource Name \(ARN\) for your DB cluster for the `--resource-id` option and `system-update` for the `--apply-action` option\. Set the `--opt-in-type` option to `immediate` to immediately update the database version for your DB cluster\. Or set it to `next-maintenance` to update the database version for your DB cluster during the next cluster maintenance window\. 

+ **By using the Amazon RDS API** – Call the [ApplyPendingMaintenanceAction](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ApplyPendingMaintenanceAction.html) API action and specify the Amazon Resource Name \(ARN\) for your DB cluster for the `ResourceId` parameter and `system-update` for the `ApplyAction` parameter\. Set the `OptInType` parameter to `immediate` to immediately update the database version for your DB cluster, or `next-maintenance` to update the database version for your instance during the next cluster maintenance window\. 

For more information on how Amazon RDS manages database and operating system updates, see [Amazon RDS Maintenance](USER_UpgradeDBInstance.Maintenance.md)\. 