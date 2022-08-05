# Upgrading an Oracle DB snapshot<a name="USER_UpgradeDBSnapshot.Oracle"></a>

If you have existing manual DB snapshots, you can upgrade them to a later version of the Oracle database engine\. 

When Oracle stops providing patches for a version, and Amazon RDS deprecates the version, you can upgrade your snapshots that correspond to the deprecated version\. For more information, see [Oracle engine version management](USER_UpgradeDBInstance.Oracle.Overview.md#Oracle.Concepts.Patching)\.

Amazon RDS supports upgrading snapshots in all AWS Regions\.

## Console<a name="USER_UpgradeDBSnapshot.Oracle.Console"></a>

**To upgrade an Oracle DB snapshot**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Snapshots**, and then select the DB snapshot that you want to upgrade\.

1. For **Actions**, choose **Upgrade snapshot**\. The **Upgrade snapshot** page appears\.

1. Choose the **New engine version** to upgrade the snapshot to\.

1. \(Optional\) For **Option group**, choose the option group for the upgraded DB snapshot\. The same option group considerations apply when upgrading a DB snapshot as when upgrading a DB instance\. For more information, see [Option group considerations](USER_UpgradeDBInstance.Oracle.OGPG.md#USER_UpgradeDBInstance.Oracle.OGPG.OG)\. 

1. Choose **Save changes** to save your changes\.

   During the upgrade process, all snapshot actions are disabled for this DB snapshot\. Also, the DB snapshot status changes from **available** to **upgrading**, and then changes to **active** upon completion\. If the DB snapshot can't be upgraded because of snapshot corruption issues, the status changes to **unavailable**\. You can't recover the snapshot from this state\. 
**Note**  
If the DB snapshot upgrade fails, the snapshot is rolled back to the original state with the original version\.

## AWS CLI<a name="USER_UpgradeDBSnapshot.Oracle.CLI"></a>

To upgrade an Oracle DB snapshot by using the AWS CLI, call the [modify\-db\-snapshot](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-snapshot.html) command with the following parameters: 
+ `--db-snapshot-identifier` – The name of the DB snapshot\. 
+ `--engine-version` – The version to upgrade the snapshot to\. 

You might also need to include the following parameter\. The same option group considerations apply when upgrading a DB snapshot as when upgrading a DB instance\. For more information, see [Option group considerations](USER_UpgradeDBInstance.Oracle.OGPG.md#USER_UpgradeDBInstance.Oracle.OGPG.OG)\. 
+ `--option-group-name` – The option group for the upgraded DB snapshot\. 

**Example**  
The following example upgrades a DB snapshot\.   
For Linux, macOS, or Unix:  

```
aws rds modify-db-snapshot \
    --db-snapshot-identifier mydbsnapshot \
    --engine-version 19.0.0.0.ru-2020-10.rur-2020-10.r1 \
    --option-group-name default:oracle-se2-19
```
For Windows:  

```
aws rds modify-db-snapshot ^
    --db-snapshot-identifier mydbsnapshot ^
    --engine-version 19.0.0.0.ru-2020-10.rur-2020-10.r1 ^
    --option-group-name default:oracle-se2-19
```

## RDS API<a name="USER_UpgradeDBSnapshot.Oracle.API"></a>

To upgrade an Oracle DB snapshot by using the Amazon RDS API, call the [ModifyDBSnapshot](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBSnapshot.html) operation with the following parameters: 
+ `DBSnapshotIdentifier` – The name of the DB snapshot\. 
+ `EngineVersion` – The version to upgrade the snapshot to\. 

You might also need to include the `OptionGroupName` parameter\. The same option group considerations apply when upgrading a DB snapshot as when upgrading a DB instance\. For more information, see [Option group considerations](USER_UpgradeDBInstance.Oracle.OGPG.md#USER_UpgradeDBInstance.Oracle.OGPG.OG)\. 