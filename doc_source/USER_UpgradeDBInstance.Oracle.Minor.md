# Oracle minor version upgrades<a name="USER_UpgradeDBInstance.Oracle.Minor"></a>

A minor version upgrade applies an Oracle Database Patch Set Update \(PSU\) or Release Update \(RU\) to a major engine version\. For example, if your DB instance runs major version Oracle Database 21c and minor version 21\.0\.0\.0\.ru\-2022\-07\.rur\-2022\-07\.r1, you can upgrade your upgrade to minor version 21\.0\.0\.0\.ru\-2022\-10\.rur\-2022\-10\.r1\. Typically, a new minor version is available every quarter\.

**Note**  
RDS for Oracle doesn't support minor version downgrades\.

You can upgrade your DB engine to a minor version manually or automatically\. To learn how to upgrade manually, see [Manually upgrading the engine version](USER_UpgradeDBInstance.Upgrading.md#USER_UpgradeDBInstance.Upgrading.Manual)\. To learn how to configure automatic upgrades, see [Automatically upgrading the minor engine version](USER_UpgradeDBInstance.Upgrading.md#USER_UpgradeDBInstance.Upgrading.AutoMinorVersionUpgrades)\.

Whether you upgrade manually or automatically, a minor version upgrade entails downtime\. Keep this in mind when planning your upgrades\.

## Turning on automatic minor version upgrades<a name="oracle-minor-version-upgrade-tuning-on"></a>

In an automatic minor version upgrade, RDS applies the latest available minor version to your Oracle database without manual intervention\. An Amazon RDS for Oracle DB instance schedules your upgrade during the next maintenance window in the following circumstances:
+ Your DB instance has the **Auto minor version upgrade** option turned on\.
+ Your DB instance isn't already running the latest minor DB engine version\.
+ Your DB instance doesn't already have a pending upgrade scheduled\.

To learn how to turn on automatic upgrades, see [Automatically upgrading the minor engine version](USER_UpgradeDBInstance.Upgrading.md#USER_UpgradeDBInstance.Upgrading.AutoMinorVersionUpgrades)\.

## Before an automatic minor version upgrade is scheduled<a name="oracle-minor-version-upgrade-advance"></a>

RDS publishes an advance notice before it begins scheduling automatic upgrades\. You can find the notification in the **Maintenance & backups** tab of the database details page\. The message has the following format:

```
An automatic minor version upgrade to engine version will become available on availability-date and should be applied during a subsequent maintenance window.
```

The *availability\-date* in the preceding message is the date when RDS starts scheduling upgrades for DB instances in your AWS Region\. It is not the date on which the upgrade of your DB instance is scheduled to occur\.

You can also get the upgrade availability date by using the `describe-pending-maintenance-actions` command in the AWS CLI, as shown in the following example:

```
aws rds describe-pending-maintenance-actions 

{
    "PendingMaintenanceActions": [
        {
            "ResourceIdentifier": "arn:aws:rds:us-east-1:123456789012:db:orclinst1",
            "PendingMaintenanceActionDetails": [
                {
                    "Action": "db-upgrade",
                    "Description": "Automatic minor version upgrade to 21.0.0.0.ru-2022-10.rur-2022-10.r1",
                    "CurrentApplyDate": "2022-12-02T08:10:00Z",
                    "OptInStatus": "next-maintenance"
                }
            ]
        }, ...
```

For more information about [describe\-pending\-maintenance\-actions](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-pending-maintenance-actions.html), see the *AWS CLI Command Reference*\.

## When RDS schedules automatic minor version upgrades<a name="oracle-minor-version-upgrade-scheduled"></a>

When the availability date for automatic upgrades arrives, RDS begins scheduling upgrades\. For most AWS Regions, RDS schedules your upgrade to the latest quarterly RU approximately four to six weeks after the availability date\. The scheduled date varies depending on the AWS Region and other factors\. For more information about RUs and RURs, see [https://docs.aws.amazon.com/AmazonRDS/latest/OracleReleaseNotes/Welcome.html](https://docs.aws.amazon.com/AmazonRDS/latest/OracleReleaseNotes/Welcome.html)\.

When RDS schedules the upgrade, the following notification appears in the **Maintenance & backups** tab of the database details page:

```
Automatic minor version upgrade to engine-version
```

The preceding message indicates that RDS has scheduled your DB engine to be upgraded in the next maintenance window\.

## Managing an automatic minor version upgrade<a name="oracle-minor-version-upgrade-managing"></a>

When a new minor version becomes available, you can upgrade your DB instance to this version manually\. The following example upgrades the DB instance named `orclinst1` immediately:

```
aws rds apply-pending-maintenance-action \
    --resource-identifier arn:aws:rds:us-east-1:123456789012:db:orclinst1 \
    --apply-action db-upgrade \
    --opt-in-type immediate
```

To opt out of an automatic minor version upgrade that hasn't been scheduled yet, set `opt-in-type` to `undo-opt-in`, as in the following example:

```
aws rds apply-pending-maintenance-action \
    --resource-identifier arn:aws:rds:us-east-1:123456789012:db:orclinst1 \
    --apply-action db-upgrade \
    --opt-in-type undo-opt-in
```

If RDS has already scheduled an upgrade for your DB instance, you can't use `apply-pending-maintenance-action` to cancel it\. But you can modify your DB instance and turn off the automatic minor upgrade feature, which then unschedules the upgrade\.

To learn how to turn off automatic minor version upgrades, see [Automatically upgrading the minor engine version](USER_UpgradeDBInstance.Upgrading.md#USER_UpgradeDBInstance.Upgrading.AutoMinorVersionUpgrades)\. For more information about [apply\-pending\-maintenance\-action](https://docs.aws.amazon.com/cli/latest/reference/rds/apply-pending-maintenance-action.html), see the *AWS CLI Command Reference*\.