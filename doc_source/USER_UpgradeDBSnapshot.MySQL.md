# Upgrading a MySQL DB Snapshot<a name="USER_UpgradeDBSnapshot.MySQL"></a>

With Amazon RDS, you can create a storage volume DB snapshot of your MySQL DB instance\. When you create a DB snapshot, the snapshot is based on the engine version used by your Amazon RDS instance\. In addition to upgrading the DB engine version of your DB instance, you can also upgrade the engine version for your DB snapshots\. For example, you can upgrade DB snapshots created from the MySQL 5\.1 engine to DB snapshots for the MySQL 5\.5 engine\. After restoring a DB snapshot upgraded to a new engine version, you should test that the upgrade was successful\. To learn how to test a major version upgrade, see [Testing an Upgrade](USER_UpgradeDBInstance.MySQL.md#USER_UpgradeDBInstance.MySQL.UpgradeTesting)\. To learn how to restore a DB snapshot, see [Restoring from a DB Snapshot](USER_RestoreFromSnapshot.md)\.

Amazon RDS supports upgrading a MySQL DB snapshot from MySQL 5\.1 to MySQL 5\.5\.

## Upgrading a MySQL DB Snapshot<a name="USER_UpgradeDBSnapshot.MySQL.Upgrading"></a>

You can upgrade manual DB snapshots, which can be encrypted or not encrypted, from MySQL 5\.1 to MySQL 5\.5 within the same AWS Region\. You can't upgrade automated DB snapshots that are created during the automated backup process\.

### Console<a name="USER_UpgradeDBSnapshot.MySQL.Console"></a>

**To upgrade a DB snapshot**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Snapshots**\.

1. For **Actions**, choose **Upgrade snapshot**\. The **Upgrade snapshot** page appears\. 

1. Choose the **New engine version** to upgrade to\.

1. Choose **Save changes** to upgrade the snapshot\.

   During the upgrade process, all snapshot actions are disabled for this DB snapshot\. Also, the DB snapshot status changes from **available** to **upgrading**, and then changes to **active** upon completion\. If the DB snapshot can't be upgraded because of snapshot corruption issues, the status changes to **unavailable**\. You can't recover the snapshot from this state\. 
**Note**  
If the DB snapshot upgrade fails, the snapshot is rolled back to the original state with the original version\.

## AWS CLI<a name="USER_UpgradeDBSnapshot.MySQL.CLI"></a>

To upgrade a DB snapshot to a new database engine version, use the AWS CLI [modify\-db\-snapshot](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-snapshot.html) command\. 

**Parameters**
+ `--db-snapshot-identifier` – The identifier of the DB snapshot to upgrade\. The identifier must be a unique Amazon Resource Name \(ARN\)\. For more information, see [Working with Amazon Resource Names \(ARNs\) in Amazon RDS](USER_Tagging.ARN.md)\.
+ `--engine-version` – The engine version to upgrade the DB snapshot to\.

**Example**  
For Linux, macOS, or Unix:  

```
1. aws rds modify-db-snapshot \
2.     --db-snapshot-identifier <mydbsnapshot> \
3.     --engine-version <new_version>
```
For Windows:  

```
1. aws rds modify-db-snapshot ^
2.     --db-snapshot-identifier <mydbsnapshot> ^
3.     --engine-version <new_version>
```

## RDS API<a name="USER_UpgradeDBSnapshot.MySQL.API"></a>

To upgrade a DB snapshot to a new database engine version, call the Amazon RDS API [ ModifyDBSnapshot](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBSnapshot.html) operation\. 
+ `DBSnapshotIdentifier` – The identifier of the DB snapshot to upgrade\. The identifier must be a unique Amazon Resource Name \(ARN\)\. For more information, see [Working with Amazon Resource Names \(ARNs\) in Amazon RDS](USER_Tagging.ARN.md)\. 
+ `EngineVersion` – The engine version to upgrade the DB snapshot to\. 

**Example**  

```
 1. https://rds.us-west-2.amazonaws.com/
 2.    ?Action=ModifyDBSnapshot
 3.    &DBSnapshotIdentifier=mydbsnapshot
 4.    &EngineVersion=newversion
 5.    &SignatureVersion=4
 6.    &Version=2014-10-31
 7.    &X-Amz-Algorithm=AWS4-HMAC-SHA256
 8.    &X-Amz-Credential=AKIADQKE4SARGYLE/20161222/us-west-1/rds/aws4_request
 9.    &X-Amz-Date=20161222T233051Z
10.    &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
11.    &X-Amz-Signature=8052a76dfb18469393c5f0182cdab0ebc224a9c7c5c949155376c1c250fc7ec3
```