# Deleting a DB instance<a name="USER_DeleteInstance"></a>

To delete a DB instance, you must do the following:
+ Provide the name of the instance
+ Enable or disable the option to take a final DB snapshot of the instance
+ Enable or disable the option to retain automated backups

If you delete a DB instance that has read replicas in the same AWS Region, each read replica is promoted to a standalone DB instance\. For more information, see [Promoting a read replica to be a standalone DB instance](USER_ReadRepl.md#USER_ReadRepl.Promote)\. If your DB instance has read replicas in different AWS Regions, see [Cross\-Region replication considerations](USER_ReadRepl.md#USER_ReadRepl.XRgn.Cnsdr) for information related to deleting the source DB instance for a cross\-Region read replica\.

**Note**  
When the status for a DB instance is `deleting`, its CA certificate value doesn't appear in the RDS console or in output for AWS CLI commands or RDS API operations\. For more information about CA certificates, see [Using SSL/TLS to encrypt a connection to a DB instance](UsingWithRDS.SSL.md)\.

## Deletion protection<a name="USER_DeleteInstance.DeletionProtection"></a>

You can only delete instances that don't have deletion protection enabled\. When you create or modify a DB instance, you have the option to enable deletion protection so that users can't delete the DB instance\. Deletion protection is disabled by default for you when you use AWS CLI and API commands\. Deletion protection is enabled for you when you use the AWS Management Console to create a production DB instance\. However, Amazon RDS enforces deletion protection when you use the console, the CLI, or the API to delete a DB instance\. To delete a DB instance that has deletion protection enabled, first modify the instance and disable deletion protection\. Enabling or disabling deletion protection doesn't cause an outage\.

## Creating a final snapshot and retaining automated backups<a name="USER_DeleteInstance.Snapshot"></a>

When you delete a DB instance, you can choose to do one or both of the following:
+ Create a final DB snapshot\.
  + To be able to restore your deleted DB instance later, create a final DB snapshot\. The final snapshot is retained, along with any manual snapshots that were taken\.
  + To delete a DB instance quickly, you can skip creating a final DB snapshot\.
**Note**  
You can't create a final DB snapshot of your DB instance if it has the status `creating`, `failed`, `incompatible-restore`, or `incompatible-network`\. For more information, see [Viewing Amazon RDS DB instance status](accessing-monitoring.md#Overview.DBInstance.Status)\.
+ Retain automated backups\.
  + Your automated backups are retained for the retention period that is set on the DB instance at the time when you delete it\. This set retention period occurs whether or not you choose to create a final DB snapshot\.
  + If you don't choose to retain automated backups, your automated backups in the same AWS Region as the DB instance are deleted\. They can't be recovered after you delete the DB instance\.
**Note**  
Automated backups that are replicated to another AWS Region are retained even if you choose not to retain automated backups\. For more information, see [Replicating automated backups to another AWS Region](USER_ReplicateBackups.md)\.
  + You typically don't need to retain automated backups if you create a final DB snapshot\.
  + To delete a retained automated backup, follow the instructions in [Deleting retained automated backups](USER_WorkingWithAutomatedBackups.md#USER_WorkingWithAutomatedBackups-Deleting)\.

**Important**  
If you skip the final DB snapshot, to restore your DB instance do one of the following:  
Use an earlier manual snapshot of the DB instance to restore the DB instance to that DB snapshot's point in time\.
Retain automated backups\. You can use them to restore your DB instance during your retention period, but not after your retention period has ended\.

**Note**  
Regardless of your choice, manual DB snapshots aren't deleted\. For more information on snapshots, see [Creating a DB snapshot](USER_CreateSnapshot.md)\.

## Deleting a DB instance<a name="USER_DeleteInstance.Deleting"></a>

You can delete a DB instance using the AWS Management Console, the AWS CLI, or the RDS API\.

The time required to delete a DB instance can vary depending on the backup retention period \(that is, how many backups to delete\), how much data is deleted, and whether a final snapshot is taken\.

**Note**  
You can't delete a DB instance when deletion protection is enabled for it\. For more information, see [Deletion protection](#USER_DeleteInstance.DeletionProtection)\.  
You can disable deletion protection by modifying the DB instance\. For more information, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\.

### Console<a name="USER_DeleteInstance.CON"></a>

**To delete a DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then choose the DB instance that you want to delete\.

1. For **Actions**, choose **Delete**\.

1. To create a final DB snapshot for the DB instance, choose **Create final snapshot?**\.

1. If you chose to create a final snapshot, enter the **Final snapshot name**\.

1. To retain automated backups, choose **Retain automated backups**\.

1. Enter **delete me** in the box\.

1. Choose **Delete**\.

### AWS CLI<a name="USER_DeleteInstance.CLI"></a>

To delete a DB instance by using the AWS CLI, call the [delete\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/delete-db-instance.html) command with the following options:
+ `--db-instance-identifier`
+ `--final-db-snapshot-identifier` or `--skip-final-snapshot`

**Example With a final snapshot and no retained automated backups**  
For Linux, macOS, or Unix:  

```
aws rds delete-db-instance \
    --db-instance-identifier mydbinstance \
    --final-db-snapshot-identifier mydbinstancefinalsnapshot \
    --delete-automated-backups
```
For Windows:  

```
aws rds delete-db-instance ^
    --db-instance-identifier mydbinstance ^
    --final-db-snapshot-identifier mydbinstancefinalsnapshot ^
    --delete-automated-backups
```

**Example With retained automated backups and no final snapshot**  
For Linux, macOS, or Unix:  

```
aws rds delete-db-instance \
    --db-instance-identifier mydbinstance \
    --skip-final-snapshot \
    --no-delete-automated-backups
```
For Windows:  

```
aws rds delete-db-instance ^
    --db-instance-identifier mydbinstance ^
    --skip-final-snapshot ^
    --no-delete-automated-backups
```

### RDS API<a name="USER_DeleteInstance.API"></a>

To delete a DB instance by using the Amazon RDS API, call the [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DeleteDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DeleteDBInstance.html) operation with the following parameters:
+ `DBInstanceIdentifier`
+ `FinalDBSnapshotIdentifier` or `SkipFinalSnapshot`