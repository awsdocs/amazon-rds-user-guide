# Working with RDS for Oracle replica backups<a name="oracle-read-replicas.backups"></a>

You can create and restore backups of an RDS for Oracle replica\. Both automatic backups and manual snapshots are supported\. For more information, see [Backing up and restoring an Amazon RDS DB instance](CHAP_CommonTasks.BackupRestore.md)\. The following sections describe the key differences between managing backups of a primary and an RDS for Oracle replica\.

## Turning on RDS for Oracle replica backups<a name="oracle-read-replicas.backups.turning-on"></a>

An Oracle replica doesn't have automated backups turned on by default\. You turn on automated backups by setting the backup retention period to a positive nonzero value\.

### Console<a name="USER_WorkingWithAutomatedBackups.Enabling.CON"></a>

**To enable automated backups immediately**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then choose the DB instance that you want to modify\.

1. Choose **Modify**\. The **Modify DB instance** page appears\.

1. For **Backup retention period**, choose a positive nonzero value, for example 3 days\.

1. Choose **Continue**\.

1. Choose **Apply immediately**\.

1. On the confirmation page, choose **Modify DB instance** to save your changes and enable automated backups\.

### AWS CLI<a name="USER_WorkingWithAutomatedBackups.Enabling.CLI"></a>

To enable automated backups, use the AWS CLI [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) command\.

Include the following parameters:
+ `--db-instance-identifier`
+ `--backup-retention-period`
+ `--apply-immediately` or `--no-apply-immediately`

In the following example, we enable automated backups by setting the backup retention period to three days\. The changes are applied immediately\.

**Example**  
For Linux, macOS, or Unix:  

```
aws rds modify-db-instance \
    --db-instance-identifier mydbinstance  \
    --backup-retention-period 3 \
    --apply-immediately
```
For Windows:  

```
aws rds modify-db-instance ^
    --db-instance-identifier mydbinstance  ^
    --backup-retention-period 3 ^
    --apply-immediately
```

### RDS API<a name="USER_WorkingWithAutomatedBackups.Enabling.API"></a>

To enable automated backups, use the RDS API [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) operation with the following required parameters:
+ `DBInstanceIdentifier`
+ `BackupRetentionPeriod`

## Restoring an RDS for Oracle replica backup<a name="oracle-read-replicas.backups.restoring"></a>

You can restore an Oracle replica backup just as you can restore a backup of the primary instance\. For more information, see the following:
+ [Restoring from a DB snapshot](USER_RestoreFromSnapshot.md)
+ [Restoring a DB instance to a specified time](USER_PIT.md)

The main consideration when you restore a replica backup is determining the point in time to which you are restoring\. The database time refers to the latest applied transaction time of the data in the backup\. When you restore a replica backup, you restore to the database time, not the time when the backup completed\. The difference is significant because an RDS for Oracle replica can lag behind the primary by minutes or hours\. Thus, the database time of a replica backup, and thus the point in time to which you restore it, might be much earlier than the backup creation time\.

To find the difference between database time and creation time, use the `describe-db-snapshots` command\. Compare the `SnapshotDatabaseTime`, which is the database time of the replica backup, and the `OriginalSnapshotCreateTime` field, which is the latest applied transaction on the primary database\. The following example shows the difference between the two times:

```
aws rds describe-db-snapshots \
    --db-instance-identifier my-oracle-replica
    --db-snapshot-identifier my-replica-snapshot

{
    "DBSnapshots": [
        {
            "DBSnapshotIdentifier": "my-replica-snapshot",
            "DBInstanceIdentifier": "my-oracle-replica", 
            "SnapshotDatabaseTime": "2022-07-26T17:49:44Z",
            ...
            "OriginalSnapshotCreateTime": "2021-07-26T19:49:44Z"
        }
    ]
}
```