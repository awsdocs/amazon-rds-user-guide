# Upgrading a DB instance for Amazon RDS Custom for Oracle<a name="custom-upgrading"></a>

You can upgrade an Amazon RDS Custom DB instance by modifying it to use a new custom engine version \(CEV\)\. For general information about upgrades, see [Upgrading a DB instance engine version](USER_UpgradeDBInstance.Upgrading.md)\.

**Topics**
+ [Requirements and considerations for RDS Custom for Oracle upgrades](#custom-upgrading-considerations)
+ [Viewing valid upgrade targets for RDS Custom for Oracle DB instances](#custom-upgrading-target)
+ [Upgrading an RDS Custom DB instance](#custom-upgrading-modify)
+ [Viewing pending upgrades for RDS Custom DB instances](#custom-upgrading-pending)
+ [Troubleshooting an upgrade failure for an RDS Custom for Oracle DB instance](#custom-upgrading-failure)

## Requirements and considerations for RDS Custom for Oracle upgrades<a name="custom-upgrading-considerations"></a>

Before upgrading your DB instance, note the following requirements:
+ You can upgrade your DB instance to a new CEV only if it already exists\.
+ You can upgrade your DB instance to a new CEV only if it uses the same installation parameter settings in the manifest\. For example, you can't upgrade a database that uses the default Oracle home to a CEV that uses a nondefault Oracle home\.
+ You can upgrade your DB instance to a new minor version only\. For example, you can't upgrade a DB instance using an Oracle Database 12c CEV to an Oracle Database 19c CEV\.

Consider the following:
+ We strongly recommend that you upgrade your RDS Custom for Oracle DB instance using CEVs\. RDS Custom for Oracle automation synchronizes the patch metadata with the database binary on your DB instance\.

  In special circumstances, RDS Custom supports applying a "one\-off" patch directly to the underlying Amazon EC2 instance directly using OPATCH\. A valid use case might be a patch that you want to apply immediately, but the RDS Custom team is upgrading the CEV feature, causing a delay\. To apply a patch manually, perform the following steps:

  1. Pause RDS Custom automation\.

  1. Apply your patch to the database binaries on the Amazon EC2 instance\.

  1. Resume RDS Custom automation\.

  A disadvantage of the preceding technique is that you must apply the patch manually to every instance that you want to upgrade\. In contrast, when you create a new CEV, you can create or upgrade multiple DB instances using the same CEV\.
+ When you upgrade your primary DB instance, RDS Custom for Oracle upgrades your read replicas automatically\. You don't have to upgrade read replicas manually\.
+ When you upgrade a CEV, RDS Custom deletes the data in the `bin` volume of your DB instance\.
+ When you upgrade a container database \(CDB\), RDS Custom for Oracle checks that all PDBs are open or could be opened\. If these conditions aren't met, RDS Custom stops the check and returns the database to its original state without attempting the upgrade\. If the conditions are met, RDS Custom patches the CDB root first, and then patches all other PDBs \(including `PDB$SEED`\) in parallel\.

  After the patching process completes, RDS Custom attempts to open all PDBs\. If any PDBs fail to open, you receive the following event: `The following PDBs failed to open: list-of-PDBs`\. If RDS Custom fails to patch the CDB root or any PDBs, the instance is put into the `PATCH_DB_FAILED` state\.
+ You might want to perform a major version upgrade and a conversion of non\-CDB to CDB at the same time\. In this case, we recommend that you complete this goal in the following process:

  1. Create a new RDS Custom DB instance that uses the Oracle Multitenant architecture\.

  1. Plug in a non\-CDB into your CDB root, creating it as a PDB\. Make sure that the non\-CDB is the same major version as your CDB\.

  1. Convert your PDB by running the `noncdb_to_pdb.sql` Oracle script\.

  1. Validate your CDB instance\.

  1. Upgrade your CDB instance\.

## Viewing valid upgrade targets for RDS Custom for Oracle DB instances<a name="custom-upgrading-target"></a>

You can see existing CEVs on the **Custom engine versions** page in the AWS Management Console\.

You can also use the [describe\-db\-engine\-versions](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-engine-versions.html) AWS CLI command to find valid upgrades for your DB instances, as shown in the following example\. This example assumes that a DB instance was created using the version 19\.my\_cev1, and that the upgrade versions 19\.my\_cev2 and 19\.my\_cev exist\.

```
aws rds describe-db-engine-versions --engine custom-oracle-ee --engine-version 19.my_cev1
```

The output resembles the following\.

```
{
    "DBEngineVersions": [
        {
            "Engine": "custom-oracle-ee",
            "EngineVersion": "19.my_cev1",
            ...
            "ValidUpgradeTarget": [
                {
                    "Engine": "custom-oracle-ee",
                    "EngineVersion": "19.my_cev2",
                    "Description": "19.my_cev2 description",
                    "AutoUpgrade": false,
                    "IsMajorVersionUpgrade": false
                },
                {
                    "Engine": "custom-oracle-ee",
                    "EngineVersion": "19.my_cev3",
                    "Description": "19.my_cev3 description",
                    "AutoUpgrade": false,
                    "IsMajorVersionUpgrade": false
                }
            ]
            ...
```

## Upgrading an RDS Custom DB instance<a name="custom-upgrading-modify"></a>

To upgrade your RDS Custom DB instance, you modify it to use a new CEV\.

Read replicas managed by RDS Custom are automatically upgraded after the primary DB instance is upgraded\.

### Console<a name="custom-upgrading-modify.CON"></a>

**To upgrade an RDS Custom DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then choose the RDS Custom DB instance that you want to modify\.

1. Choose **Modify**\. The **Modify DB instance** page appears\.

1. For **DB engine version**, choose the CEV to upgrade to, such as `19.my_cev3`\.

1. Choose **Continue** to check the summary of modifications\.

   Choose **Apply immediately** to apply the changes immediately\.

1. If your changes are correct, choose **Modify DB instance**\. Or choose **Back** to edit your changes or **Cancel** to cancel your changes\.

### AWS CLI<a name="custom-upgrading-modify.CLI"></a>

To upgrade an RDS Custom DB instance, use the [modify\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) AWS CLI command with the following parameters:
+ `--db-instance-identifier` – The DB instance to be upgraded
+ `--engine-version` – The new CEV
+ `--no-apply-immediately` \| `--apply-immediately` – Whether to perform the upgrade immediately or wait until the scheduled maintenance window

The following example upgrades `my-custom-instance` to version `19.my_cev3`\.

**Example**  
For Linux, macOS, or Unix:  

```
aws rds modify-db-instance \
    --db-instance-identifier my-custom-instance \
    --engine-version 19.my_cev3 \
    --apply-immediately
```
For Windows:  

```
aws rds modify-db-instance ^
    --db-instance-identifier my-custom-instance ^
    --engine-version 19.my_cev3 ^
    --apply-immediately
```

## Viewing pending upgrades for RDS Custom DB instances<a name="custom-upgrading-pending"></a>

You can see pending upgrades for your Amazon RDS Custom DB instances by using the [describe\-db\-instances](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instances.html) or [describe\-pending\-maintenance\-actions](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-pending-maintenance-actions.html) AWS CLI command\.

However, this approach doesn't work if you used the `--apply-immediately` option or if the upgrade is in progress\.

The following `describe-db-instances` command shows pending upgrades for `my-custom-instance`\.

```
aws rds describe-db-instances --db-instance-identifier my-custom-instance
```

The output resembles the following\.

```
{
    "DBInstances": [
        {
           "DBInstanceIdentifier": "my-custom-instance",
            "EngineVersion": "19.my_cev1",
            ...
            "PendingModifiedValues": {
                "EngineVersion": "19.my_cev3"
            ...
            }
        }
    ]
}
```

The following shows use of the `describe-pending-maintenance-actions` command\.

```
aws rds describe-pending-maintenance-actions
```

The output resembles the following\.

```
{
    "PendingMaintenanceActions": [
        {
            "ResourceIdentifier": "arn:aws:rds:us-west-2:123456789012:instance:my-custom-instance",
            "PendingMaintenanceActionDetails": [
                {
                    "Action": "db-upgrade",
                    "Description": "Upgrade to 19.my_cev3"
                }
            ]
        }
    ]
}
```

## Troubleshooting an upgrade failure for an RDS Custom for Oracle DB instance<a name="custom-upgrading-failure"></a>

If an RDS Custom DB instance upgrade fails, an RDS event is generated and the DB instance status becomes `upgrade-failed`\.

You can see this status by using the [describe\-db\-instances](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instances.html) AWS CLI command, as shown in the following example\.

```
aws rds describe-db-instances --db-instance-identifier my-custom-instance
```

The output resembles the following\.

```
{
    "DBInstances": [
        {
           "DBInstanceIdentifier": "my-custom-instance",
            "EngineVersion": "19.my_cev1",
            ...
            "PendingModifiedValues": {
                "EngineVersion": "19.my_cev3"
            ...
            }
            "DBInstanceStatus": "upgrade-failed"
        }
    ]
}
```

After an upgrade failure, all database actions are blocked except for modifying the DB instance to perform the following tasks:
+ Retrying the same upgrade
+ Pausing and resuming RDS Custom automation
+ Point\-in\-time recovery \(PITR\)
+ Deleting the DB instance

**Note**  
If automation has been paused for the RDS Custom DB instance, you can't retry the upgrade until you resume automation\.  
The same actions apply to an upgrade failure for an RDS\-managed read replica as for the primary\.

For more information, see [Troubleshooting upgrades for RDS Custom for Oracle](custom-troubleshooting.md#custom-troubleshooting-upgrade)\.