# Upgrading an Oracle DB Snapshot<a name="USER_UpgradeDBSnapshot.Oracle"></a>

If you have existing manual DB snapshots, you can upgrade them to a later version of the Oracle database engine\. 

When Oracle stops providing patches for a version, and Amazon RDS deprecates the version, you can upgrade your snapshots that correspond to the deprecated version\. For more information, see [Oracle Engine Version Management](CHAP_Oracle.md#Oracle.Concepts.Patching)\. To learn how to upgrade snapshots in preparation for the automatic upgrade of 11\.2, see [Migrating from SE2 to EE Using Snapshots](USER_UpgradeDBInstance.Oracle.md#USER_UpgradeDBInstance.Oracle.auto-upgrade-of-11g.migrating-editions)\.

The following snapshot upgrades are currently supported\. 


****  

| Current Snapshot Version | Supported Snapshot Upgrade | 
| --- | --- | 
|  12\.1\.0\.1  |  12\.1\.0\.2\.v8  | 
|  11\.2\.0\.4  |  12\.1\.0\.2, 12\.2\.0\.1, 18c, and 19c when the following conditions are met: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_UpgradeDBSnapshot.Oracle.html)  | 
| 11\.2\.0\.3 | 11\.2\.0\.4\.v11 | 
| 11\.2\.0\.2 | 11\.2\.0\.4\.v12 | 

Amazon RDS supports upgrading snapshots in all AWS Regions\.

## Console<a name="USER_UpgradeDBSnapshot.Oracle.Console"></a>

**To upgrade an Oracle DB snapshot**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Snapshots**, and then select the DB snapshot that you want to upgrade\.

1. For **Actions**, choose **Upgrade snapshot**\. The **Upgrade snapshot** page appears\.

1. Choose the **New engine version** to upgrade the snapshot to\.

1. \(Optional\) For **Option group**, choose the option group for the upgraded DB snapshot\. The same option group considerations apply when upgrading a DB snapshot as when upgrading a DB instance\. For more information, see [Option Group Considerations](USER_UpgradeDBInstance.Oracle.md#USER_UpgradeDBInstance.Oracle.OGPG.OG)\. 

1. Choose **Save changes** to save your changes\.

   During the upgrade process, all snapshot actions are disabled for this DB snapshot\. Also, the DB snapshot status changes from **available** to **upgrading**, and then changes to **active** upon completion\. If the DB snapshot can't be upgraded because of snapshot corruption issues, the status changes to **unavailable**\. You can't recover the snapshot from this state\. 
**Note**  
If the DB snapshot upgrade fails, the snapshot is rolled back to the original state with the original version\.

## AWS CLI<a name="USER_UpgradeDBSnapshot.Oracle.CLI"></a>

To upgrade an Oracle DB snapshot by using the AWS CLI, call the [modify\-db\-snapshot](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-snapshot.html) command with the following parameters: 
+ `--db-snapshot-identifier` – The name of the DB snapshot\. 
+ `--engine-version` – The version to upgrade the snapshot to\. 

You might also need to include the following parameter\. The same option group considerations apply when upgrading a DB snapshot as when upgrading a DB instance\. For more information, see [Option Group Considerations](USER_UpgradeDBInstance.Oracle.md#USER_UpgradeDBInstance.Oracle.OGPG.OG)\. 
+ `--option-group-name` – The option group for the upgraded DB snapshot\. 

**Example**  
The following example upgrades a DB snapshot\.   
For Linux, macOS, or Unix:  

```
aws rds modify-db-snapshot \
    --db-snapshot-identifier <mydbsnapshot> \
    --engine-version <11.2.0.4.v12> \
    --option-group-name <default:oracle-se1-11-2>
```
For Windows:  

```
aws rds modify-db-snapshot ^
    --db-snapshot-identifier <mydbsnapshot> ^
    --engine-version <11.2.0.4.v12> ^
    --option-group-name <default:oracle-se1-11-2>
```

## RDS API<a name="USER_UpgradeDBSnapshot.Oracle.API"></a>

To upgrade an Oracle DB snapshot by using the Amazon RDS API, call the [ModifyDBSnapshot](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBSnapshot.html) operation with the following parameters: 
+ `DBSnapshotIdentifier` – The name of the DB snapshot\. 
+ `EngineVersion` – The version to upgrade the snapshot to\. 

You might also need to include the following parameter\. The same option group considerations apply when upgrading a DB snapshot as when upgrading a DB instance\. For more information, see [Option Group Considerations](USER_UpgradeDBInstance.Oracle.md#USER_UpgradeDBInstance.Oracle.OGPG.OG)\. 
+ `OptionGroupName` – The option group for the upgraded DB snapshot\. 

**Example**  
The following example upgrades a DB snapshot\.   

```
 1. https://rds.amazonaws.com/
 2.     ?Action=ModifyDBSnapshot
 3.     &DBSnapshotIdentifier=mydbsnapshot
 4.     &EngineVersion=11.2.0.4.v12
 5.     &OptionGroupName=default:oracle-se1-11-2
 6.     &SignatureMethod=HmacSHA256
 7.     &SignatureVersion=4
 8.     &Version=2014-10-31
 9.     &X-Amz-Algorithm=AWS4-HMAC-SHA256
10.     &X-Amz-Credential=AKIADQKE4SARGYLE/20131016/us-west-1/rds/aws4_request
11.     &X-Amz-Date=20131016T233051Z
12.     &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
13.     &X-Amz-Signature=087a8eb41cb1ab5f99e81575f23e73757ffc6a1e42d7d2b30b9cc0be988cff97
```