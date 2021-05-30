# Upgrading a PostgreSQL DB snapshot engine version<a name="USER_UpgradeDBSnapshot.PostgreSQL"></a>

With Amazon RDS, you can create a storage volume DB snapshot of your PostgreSQL DB instance\. When you create a DB snapshot, the snapshot is based on the engine version used by your Amazon RDS instance\. In addition to upgrading the DB engine version of your DB instance, you can also upgrade the engine version for your DB snapshots\. 

After restoring a DB snapshot upgraded to a new engine version, make sure to test that the upgrade was successful\. For more information about a major version upgrade, see [Upgrading the PostgreSQL DB engine for Amazon RDS](USER_UpgradeDBInstance.PostgreSQL.md)\. To learn how to restore a DB snapshot, see [Restoring from a DB snapshot](USER_RestoreFromSnapshot.md)\.

You can upgrade manual DB snapshots that are either encrypted or not encrypted\. 

For the list of engine versions that are available for upgrading a DB snapshot, see [ Upgrading the PostgreSQL DB engine for Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_UpgradeDBInstance.PostgreSQL.html#USER_UpgradeDBInstance.PostgreSQL.MajorVersion)\. 

**Note**  
You can't upgrade automated DB snapshots that are created during the automated backup process\.

## Console<a name="USER_UpgradeDBSnapshot.PostgreSQL.Console"></a>

**To upgrade a DB snapshot**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Snapshots**\.

1. Choose the snapshot that you want to upgrade\. 

1. For **Actions**, choose **Upgrade snapshot**\. The **Upgrade snapshot** page appears\. 

1. Choose the **New engine version** to upgrade to\.

1. Choose **Save changes** to upgrade the snapshot\.

   During the upgrade process, all snapshot actions are disabled for this DB snapshot\. Also, the DB snapshot status changes from **available** to **upgrading**, and then changes to **active** upon completion\. If the DB snapshot can't be upgraded because of snapshot corruption issues, the status changes to **unavailable**\. You can't recover the snapshot from this state\. 
**Note**  
If the DB snapshot upgrade fails, the snapshot is rolled back to the original state with the original version\.

## AWS CLI<a name="USER_UpgradeDBSnapshot.PostgreSQL.CLI"></a>

To upgrade a DB snapshot to a new database engine version, use the AWS CLI [modify\-db\-snapshot](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-snapshot.html) command\. 

**Parameters**
+ `--db-snapshot-identifier` – The identifier of the DB snapshot to upgrade\. The identifier must be a unique Amazon Resource Name \(ARN\)\. For more information, see [Working with Amazon Resource Names \(ARNs\) in Amazon RDS](USER_Tagging.ARN.md)\.
+ `--engine-version` – The engine version to upgrade the DB snapshot to\.

**Example**  
For Linux, macOS, or Unix:  

```
1. aws rds modify-db-snapshot \
2.     --db-snapshot-identifier my_db_snapshot \
3.     --engine-version new_version
```
For Windows:  

```
1. aws rds modify-db-snapshot ^
2.     --db-snapshot-identifier my_db_snapshot ^
3.     --engine-version new_version
```

## RDS API<a name="USER_UpgradeDBSnapshot.PostgreSQL.API"></a>

To upgrade a DB snapshot to a new database engine version, call the Amazon RDS API [ ModifyDBSnapshot](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBSnapshot.html) operation\. 
+ `DBSnapshotIdentifier` – The identifier of the DB snapshot to upgrade\. The identifier must be a unique Amazon Resource Name \(ARN\)\. For more information, see [Working with Amazon Resource Names \(ARNs\) in Amazon RDS](USER_Tagging.ARN.md)\. 
+ `EngineVersion` – The engine version to upgrade the DB snapshot to\.