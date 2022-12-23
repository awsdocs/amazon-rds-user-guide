# Backing up and restoring an Amazon RDS Custom for Oracle DB instance<a name="custom-backup"></a>

Like Amazon RDS, RDS Custom creates and saves automated backups of your RDS Custom for Oracle DB instance during the backup window of your DB instance\. You can also back up your DB instance manually\. 

The procedure is identical to taking a snapshot of an Amazon RDS DB instance\. The first snapshot of an RDS Custom DB instance contains the data for the full DB instance\. Subsequent snapshots are incremental\.

Restore DB snapshots using either the AWS Management Console or the AWS CLI\.

**Topics**
+ [Creating an RDS Custom for Oracle snapshot](#custom-backup.creating)
+ [Restoring from an RDS Custom for Oracle DB snapshot](#custom-backup.restoring)
+ [Restoring an RDS Custom for Oracle instance to a point in time](#custom-backup.pitr)
+ [Deleting an RDS Custom for Oracle snapshot](#custom-backup.deleting)
+ [Deleting RDS Custom for Oracle automated backups](#custom-backup.deleting-backups)

## Creating an RDS Custom for Oracle snapshot<a name="custom-backup.creating"></a>

RDS Custom for Oracle creates a storage volume snapshot of your DB instance, backing up the entire DB instance and not just individual databases\. When your DB instance contains a container database \(CDB\), the snapshot of the instance includes the root CDB and all PDBs\.

When you create an RDS Custom for Oracle snapshot, specify which RDS Custom DB instance to back up\. Give your snapshot a name so you can restore from it later\.

When you create a snapshot, RDS Custom for Oracle creates an Amazon EBS snapshot for every volume attached to the DB instance\. RDS Custom for Oracle uses the EBS snapshot of the root volume to register a new Amazon Machine Image \(AMI\)\. To make snapshots easy to associate with a specific DB instance, they're tagged with `DBSnapshotIdentifier`, `DbiResourceId`, and `VolumeType`\.

Creating a DB snapshot results in a brief I/O suspension\. This suspension can last from a few seconds to a few minutes, depending on the size and class of your DB instance\. The snapshot creation time varies with the size of your database\. Because the snapshot includes the entire storage volume, the size of files, such as temporary files, also affects snapshot creation time\. To learn more about creating snapshots, see [Creating a DB snapshot](USER_CreateSnapshot.md)\.

Create an RDS Custom for Oracle snapshot using the console or the AWS CLI\.

### Console<a name="USER_CreateSnapshot.CON"></a>

**To create an RDS Custom snapshot**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. In the list of RDS Custom DB instances, choose the instance for which you want to take a snapshot\.

1. For **Actions**, choose **Take snapshot**\.

   The **Take DB snapshot** window appears\.

1. For **Snapshot name**, enter the name of the snapshot\.

1. Choose **Take snapshot**\.

### AWS CLI<a name="USER_CreateSnapshot.CLI"></a>

You create a snapshot of an RDS Custom DB instance by using the [create\-db\-snapshot](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-snapshot.html) AWS CLI command\.

Specify the following options:
+ `--db-instance-identifier` – Identifies which RDS Custom DB instance you are going to back up
+ `--db-snapshot-identifier` – Names your RDS Custom snapshot so you can restore from it later

In this example, you create a DB snapshot called *`my-custom-snapshot`* for an RDS Custom DB instance called `my-custom-instance`\.

**Example**  
For Linux, macOS, or Unix:  

```
1. aws rds create-db-snapshot \
2.     --db-instance-identifier my-custom-instance \
3.     --db-snapshot-identifier my-custom-snapshot
```
For Windows:  

```
1. aws rds create-db-snapshot ^
2.     --db-instance-identifier my-custom-instance ^
3.     --db-snapshot-identifier my-custom-snapshot
```

## Restoring from an RDS Custom for Oracle DB snapshot<a name="custom-backup.restoring"></a>

When you restore an RDS Custom for Oracle DB instance, you provide the name of the DB snapshot and a name for the new instance\. You can't restore from a snapshot to an existing RDS Custom DB instance\. A new RDS Custom for Oracle DB instance is created when you restore\.

The restore process differs in the following ways from restore in Amazon RDS:
+ Before restoring a snapshot, RDS Custom for Oracle backs up existing configuration files\. These files are available on the restored instance in the directory `/rdsdbdata/config/backup`\. RDS Custom for Oracle restores the DB snapshot with default parameters and overwrites the previous database configuration files with existing ones\. Thus, the restored instance doesn't preserve custom parameters and changes to database configuration files\.
+ The restored database has the same name as in the snapshot\. You can't specify a different name\. \(For RDS Custom for Oracle, the default is `ORCL`\.\)

### Console<a name="custom-backup.restoring.console"></a>

**To restore an RDS Custom DB instance from a DB snapshot**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Snapshots**\.

1. Choose the DB snapshot that you want to restore from\.

1. For **Actions**, choose **Restore snapshot**\.

1. On the **Restore DB instance** page, for **DB instance identifier**, enter the name for your restored RDS Custom DB instance\.

1. Choose **Restore DB instance**\. 

### AWS CLI<a name="custom-backup.restoring.CLI"></a>

You restore an RDS Custom DB snapshot by using the [ restore\-db\-instance\-from\-db\-snapshot](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-from-db-snapshot.html) AWS CLI command\.

If the snapshot you are restoring from is for a private DB instance, make sure to specify both the correct `db-subnet-group-name` and `no-publicly-accessible`\. Otherwise, the DB instance defaults to publicly accessible\. The following options are required:
+ `db-snapshot-identifier` – Identifies the snapshot from which to restore
+ `db-instance-identifier` – Specifies the name of the RDS Custom DB instance to create from the DB snapshot
+ `custom-iam-instance-profile` – Specifies the instance profile associated with the underlying Amazon EC2 instance of an RDS Custom DB instance\.

The following code restores the snapshot named `my-custom-snapshot` for `my-custom-instance`\.

**Example**  
For Linux, macOS, or Unix:  

```
aws rds restore-db-instance-from-db-snapshot \
  --db-snapshot-identifier my-custom-snapshot \
  --db-instance-identifier my-custom-instance \
  --custom-iam-instance-profile AWSRDSCustomInstanceProfileForRdsCustomInstance \
  --no-publicly-accessible
```
For Windows:  

```
aws rds restore-db-instance-from-db-snapshot ^
  --db-snapshot-identifier my-custom-snapshot ^
  --db-instance-identifier my-custom-instance ^
  --custom-iam-instance-profile AWSRDSCustomInstanceProfileForRdsCustomInstance ^
  --no-publicly-accessible
```

## Restoring an RDS Custom for Oracle instance to a point in time<a name="custom-backup.pitr"></a>

You can restore a DB instance to a specific point in time \(PITR\), creating a new DB instance\. To support PITR, your DB instances must have backup retention set to a nonzero value\.

The latest restorable time for an RDS Custom for Oracle DB instance depends on several factors, but is typically within 5 minutes of the current time\. To see the latest restorable time for a DB instance, use the AWS CLI [describe\-db\-instances](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instances.html) command and look at the value returned in the `LatestRestorableTime` field for the DB instance\. To see the latest restorable time for each DB instance in the Amazon RDS console, choose **Automated backups**\.

You can restore to any point in time within your backup retention period\. To see the earliest restorable time for each DB instance, choose **Automated backups** in the Amazon RDS console\.

For general information about PITR, see [Restoring a DB instance to a specified time](USER_PIT.md)\.

**Topics**
+ [PITR considerations for RDS Custom for Oracle](#custom-backup.pitr.oracle)

### PITR considerations for RDS Custom for Oracle<a name="custom-backup.pitr.oracle"></a>

In RDS Custom for Oracle, PITR differs in the following important ways from PITR in Amazon RDS:
+ The restored database has the same name as in the source DB instance\. You can't specify a different name\. The default is `ORCL`\.
+ `AWSRDSCustomIamRolePolicy` requires new permissions\. For more information, see [Add an access policy to AWSRDSCustomInstanceRoleForRdsCustomInstance](custom-setup-orcl.md#custom-setup-orcl.iam.add-policy)\.
+ All RDS Custom for Oracle DB instances must have backup retention set to a nonzero value\.
+ If you change the operating system or DB instance time zone, PITR might not work\. For information about changing time zones, see [Changing the time zone of an RDS Custom for Oracle DB instance](custom-managing.md#custom-managing.timezone)\.
+ If you set automation to `ALL_PAUSED`, RDS Custom pauses the upload of archived redo logs, including logs created before the latest restorable time \(LRT\)\. We recommend that you pause automation for a brief period\.

  To illustrate, assume that your LRT is 10 minutes ago\. You pause automation\. During the pause, RDS Custom doesn't upload archived redo logs\. If your DB instance crashes, you can only recover to a time before the LRT that existed when you paused\. When you resume automation, RDS Custom resumes uploading logs\. The LRT advances\. Normal PITR rules apply\. 
+ In RDS Custom, you can manually specify an arbitrary number of hours to retain archived redo logs before RDS Custom deletes them after upload\. In RDS Custom, specify the number of hours manually in the following file: `/opt/aws/rdscustomagent/config/redo_logs_custom_configuration.json`\. The format is `{"archivedLogRetentionHours" : "num_of_hours"}`\. The number must be an integer in the range 1–840\.
+ Assume that you plug a non\-CDB into a container database \(CDB\) as a PDB and then attempt PITR\. The operation succeeds only if you previously backed up the PDB\. After you create or modify a PDB, we recommend that you always back it up\.
+ We recommend that you don't customize database initialization parameters\. For example, modifying the following parameters affects PITR:
  + `CONTROL_FILE_RECORD_KEEP_TIME` affects the rules for uploading and deleting logs\.
  + `LOG_ARCHIVE_DEST_n` doesn't support multiple destinations\.
  + `ARCHIVE_LAG_TARGET` affects the latest restorable time\.
+ If you customize database initialization parameters, we strongly recommend that you only customize the following:
  + `COMPATIBLE` 
  + `MAX_STRING_SIZE`
  + `DB_FILES` 
  + `UNDO_TABLESPACE` 
  + `ENABLE_PLUGGABLE_DATABASE` 
  + `CONTROL_FILES` 
  + `AUDIT_TRAIL` 
  + `AUDIT_TRAIL_DEST` 

  For all other initialization parameters, RDS Custom restores the default values\. If you modify a parameter that isn't in the preceding list, it might have an adverse effect on PITR and lead to unpredictable results\. For example, `CONTROL_FILE_RECORD_KEEP_TIME` affects the rules for uploading and deleting logs\.

You can restore an RDS Custom DB instance to a point in time using the AWS Management Console, the AWS CLI, or the RDS API\.

### Console<a name="custom-backup.pitr2.CON"></a>

**To restore an RDS Custom DB instance to a specified time**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Automated backups**\.

1. Choose the RDS Custom DB instance that you want to restore\.

1. For **Actions**, choose **Restore to point in time**\.

   The **Restore to point in time** window appears\.

1. Choose **Latest restorable time** to restore to the latest possible time, or choose **Custom** to choose a time\.

   If you chose **Custom**, enter the date and time to which you want to restore the instance\.

   Times are shown in your local time zone, which is indicated by an offset from Coordinated Universal Time \(UTC\)\. For example, UTC\-5 is Eastern Standard Time/Central Daylight Time\.

1. For **DB instance identifier**, enter the name of the target restored RDS Custom DB instance\. The name must be unique\.

1. Choose other options as needed, such as DB instance class\.

1. Choose **Restore to point in time**\.

### AWS CLI<a name="custom-backup.pitr2.CLI"></a>

You restore a DB instance to a specified time by using the [ restore\-db\-instance\-to\-point\-in\-time](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-to-point-in-time.html) AWS CLI command to create a new RDS Custom DB instance\.

Use one of the following options to specify the backup to restore from:
+ `--source-db-instance-identifier mysourcedbinstance`
+ `--source-dbi-resource-id dbinstanceresourceID`
+ `--source-db-instance-automated-backups-arn backupARN`

The `custom-iam-instance-profile` option is required\.

The following example restores `my-custom-db-instance` to a new DB instance named `my-restored-custom-db-instance`, as of the specified time\.

**Example**  
For Linux, macOS, or Unix:  

```
1. aws rds restore-db-instance-to-point-in-time \
2.     --source-db-instance-identifier my-custom-db-instance\
3.     --target-db-instance-identifier my-restored-custom-db-instance \
4.     --custom-iam-instance-profile AWSRDSCustomInstanceProfileForRdsCustomInstance \
5.     --restore-time 2022-10-14T23:45:00.000Z
```
For Windows:  

```
1. aws rds restore-db-instance-to-point-in-time ^
2.     --source-db-instance-identifier my-custom-db-instance ^
3.     --target-db-instance-identifier my-restored-custom-db-instance ^
4.     --custom-iam-instance-profile AWSRDSCustomInstanceProfileForRdsCustomInstance ^
5.     --restore-time 2022-10-14T23:45:00.000Z
```

## Deleting an RDS Custom for Oracle snapshot<a name="custom-backup.deleting"></a>

You can delete DB snapshots managed by RDS Custom for Oracle when you no longer need them\. The deletion procedure is the same for both Amazon RDS and RDS Custom DB instances\.

The Amazon EBS snapshots for the binary and root volumes remain in your account for a longer time because they might be linked to some instances running in your account or to other RDS Custom for Oracle snapshots\. These EBS snapshots are automatically deleted after they're no longer related to any existing RDS Custom for Oracle resources \(DB instances or backups\)\.

### Console<a name="USER_DeleteSnapshot.CON"></a>

**To delete a snapshot of an RDS Custom DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Snapshots**\.

1. Choose the DB snapshot that you want to delete\.

1. For **Actions**, choose **Delete snapshot**\.

1. Choose **Delete** on the confirmation page\.

### AWS CLI<a name="USER_DeleteSnapshot.CLI"></a>

To delete an RDS Custom snapshot, use the AWS CLI command [delete\-db\-snapshot](https://docs.aws.amazon.com/cli/latest/reference/rds/delete-db-snapshot.html)\.

The following option is required:
+ `--db-snapshot-identifier` – The snapshot to be deleted

The following example deletes the `my-custom-snapshot` DB snapshot\.

**Example**  
For Linux, macOS, or Unix:  

```
1. aws rds delete-db-snapshot \  
2.   --db-snapshot-identifier my-custom-snapshot
```
For Windows:  

```
1. aws rds delete-db-snapshot ^
2.   --db-snapshot-identifier my-custom-snapshot
```

## Deleting RDS Custom for Oracle automated backups<a name="custom-backup.deleting-backups"></a>

You can delete retained automated backups for RDS Custom for Oracle when they are no longer needed\. The procedure is the same as the procedure for deleting Amazon RDS backups\.

### Console<a name="USER_WorkingWithAutomatedBackups-Deleting.CON"></a>

**To delete a retained automated backup**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Automated backups**\.

1. Choose **Retained**\.

1. Choose the retained automated backup that you want to delete\.

1. For **Actions**, choose **Delete**\.

1. On the confirmation page, enter **delete me** and choose **Delete**\. 

### AWS CLI<a name="USER_WorkingWithAutomatedBackups-Deleting.CLI"></a>

You can delete a retained automated backup by using the AWS CLI command [delete\-db\-instance\-automated\-backup](https://docs.aws.amazon.com/cli/latest/reference/rds/delete-db-instance-automated-backup.html)\.

The following option is used to delete a retained automated backup:
+ `--dbi-resource-id` – The resource identifier for the source RDS Custom DB instance\.

  You can find the resource identifier for the source DB instance of a retained automated backup by using the AWS CLI command [describe\-db\-instance\-automated\-backups](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instance-automated-backups.html)\.

The following example deletes the retained automated backup with source DB instance resource identifier `custom-db-123ABCEXAMPLE`\.

**Example**  
For Linux, macOS, or Unix:  

```
1. aws rds delete-db-instance-automated-backup \
2.     --dbi-resource-id custom-db-123ABCEXAMPLE
```
For Windows:  

```
1. aws rds delete-db-instance-automated-backup ^
2.     --dbi-resource-id custom-db-123ABCEXAMPLE
```