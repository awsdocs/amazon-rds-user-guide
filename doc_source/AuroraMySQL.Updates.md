# Amazon Aurora MySQL Database Engine Updates<a name="AuroraMySQL.Updates"></a>

Amazon Aurora releases updates regularly\. Updates are applied to Aurora DB clusters during system maintenance windows\. The timing when updates are applied depends on the region and maintenance window setting for the DB cluster, as well as the type of update\. Updates are applied to all instances in a DB cluster simultaneously\. An update requires a database restart on all instances in a DB cluster, so you will experience 20 to 30 seconds of downtime, after which you can resume using your DB cluster or clusters\. You can view or change your maintenance window settings from the [AWS Management Console](https://console.aws.amazon.com/)\. 

## Amazon Aurora Versions<a name="AuroraMySQL.Updates.Versions"></a>

Although Amazon Aurora is compatible with the MySQL and PostgreSQL database engines, Aurora includes features that are specific to Amazon Aurora and only available to Aurora DB clusters\. Aurora versions use the format <major version>\.<minor version>\.<patch version>\. You can get the version of your Aurora instance by querying for the `AURORA_VERSION` system variable\. To get the Amazon Aurora version, use one of the following queries\.


| Database Engine | Queries | 
| --- | --- | 
|   MySQL   |  <pre>select AURORA_VERSION();</pre>  | 
|   MySQL   |  <pre>select @@aurora_version;</pre>  | 
|   PostgreSQL   |  <pre>SELECT AURORA_VERSION();</pre>  | 

## Amazon Aurora Database Upgrades \(Patching\)<a name="AuroraMySQL.Updates.Patching"></a>

When a new minor version of the Amazon Aurora MySQL database engine is released, Amazon RDS schedules an automatic upgrade of the database engine for all Aurora DB clusters\. We announce automatic upgrades in the [Amazon RDS Community Forum](https://forums.aws.amazon.com/forum.jspa?forumID=60)\.

When a new patch version of the Aurora MySQL database engine is released, no automatic upgrade is required\. You can choose to upgrade and apply the patch, otherwise the patch will be applied during the next automatic upgrade for a minor version release\. 

Before automatic upgrade, new database engine releases show as an **available** maintenance upgrade for your DB cluster\. You can manually upgrade the database version for your DB cluster by applying the available maintenance action\. We encourage you to apply the update on a non\-production instance prior to the automatic upgrade, so that you can see how changes in the new version will affect your instances and applications\.

**To apply pending maintenance actions**
+ **By using the Amazon RDS Console** – Log on to the Amazon RDS console and choose **Clusters**\. Choose the DB cluster that shows an **available** maintenance upgrade\. Choose **Cluster Actions**\. Choose **Upgrade Now** to immediately update the database version for your DB cluster, or **Upgrade at Next Window** to update the database version for your DB cluster during the next cluster maintenance window\. 
+ **By using the AWS CLI** – Call the [apply\-pending\-maintenance\-action](http://docs.aws.amazon.com/cli/latest/reference/rds/apply-pending-maintenance-action.html) AWS CLI command and specify the Amazon Resource Name \(ARN\) for your DB cluster for the `--resource-id` option and `system-update` for the `--apply-action` option\. Set the `--opt-in-type` option to `immediate` to immediately update the database version for your DB cluster, or `next-maintenance` to update the database version for your DB cluster during the next cluster maintenance window\. 
+ **By using the Amazon RDS API** – Call the [ApplyPendingMaintenanceAction](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ApplyPendingMaintenanceAction.html) API action and specify the ARN for your DB cluster for the `ResourceId` parameter and `system-update` for the `ApplyAction` parameter\. Set the `OptInType` parameter to `immediate` to immediately update the database version for your DB cluster, or `next-maintenance` to update the database version for your instance during the next cluster maintenance window\. 

For more information on how Amazon RDS manages database and operating system updates, see [Amazon RDS Maintenance](USER_UpgradeDBInstance.Maintenance.md)\. 

**Note**  
If your current Aurora MySQL version is 1\.14\.x, but it is lower than 1\.14\.4, you can only upgrade to 1\.14\.4 \(which supports db\.r4 instance classes\)\. Also, to upgrade from 1\.14\.x to a higher major Aurora MySQL version, such as 1\.17, the 1\.14\.x version must be 1\.14\.4\.

## Aurora Lab Mode<a name="AuroraMySQL.Updates.LabMode"></a>

Aurora lab mode is used to enable Aurora features that are available in the current Aurora database version, but are not enabled by default\. While Aurora lab mode features are not recommended for use in production DB clusters, you can use Aurora lab mode to enable these features for DB clusters in your development and test environments\. For more information about Aurora features available when Aurora lab mode is enabled, see [Aurora Lab Mode Features](AuroraMySQL.Updates.LabModeFeatures.md)\.

The `aurora_lab_mode` parameter is an instance\-level parameter that is in the default parameter group\. The parameter is set to `0` \(disabled\) in the default parameter group\. To enable Aurora lab mode, create a custom parameter group, set the `aurora_lab_mode` parameter to `1` \(enabled\) in the custom parameter group, and modify your primary instance or Aurora Replica to use the custom parameter group\. For information on modifying a DB parameter group, see [Modifying Parameters in a DB Parameter Group](USER_WorkingWithParamGroups.md#USER_WorkingWithParamGroups.Modifying)\. For information on parameter groups and Amazon Aurora, see [Amazon Aurora MySQL Parameters](AuroraMySQL.Reference.md#AuroraMySQL.Reference.ParameterGroups)\.

## Related Topics<a name="AuroraMySQL.Updates.Related"></a>
+ [Aurora Lab Mode Features](AuroraMySQL.Updates.LabModeFeatures.md)
+ [Amazon Aurora MySQL 2\.0 Database Engine Updates ](AuroraMySQL.Updates.20Updates.md)
+ [Amazon Aurora MySQL 1\.1 Database Engine Updates ](AuroraMySQL.Updates.11Updates.md)
+ [MySQL Bugs Fixed by Amazon Aurora MySQL Database Engine Updates](AuroraMySQL.Updates.MySQLBugs.md)