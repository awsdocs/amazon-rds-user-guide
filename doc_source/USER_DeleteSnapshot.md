# Deleting a Snapshot<a name="USER_DeleteSnapshot"></a>

You can delete DB snapshots managed by Amazon RDS when you no longer need them\.

**Note**  
To delete backups managed by AWS Backup, use the AWS Backup console\. For information about AWS Backup, see the [https://docs.aws.amazon.com/aws-backup/latest/devguide](https://docs.aws.amazon.com/aws-backup/latest/devguide)\.

## Deleting a DB Snapshot<a name="USER_DeleteRDSSnapshot"></a>

You can delete a manual, shared, or public DB snapshot using the AWS Management Console, the AWS CLI, or the RDS API\.

To delete a shared or public snapshot, you must sign in to the AWS account that owns the snapshot\.

If you have automated DB snapshots that you want to delete without deleting the DB instance, change the backup retention period for the DB instance to 0\. The automated snapshots are deleted when the change is applied\. You can apply the change immediately if you don't want to wait until the next maintenance period\. After the change is complete, you can then re\-enable automatic backups by setting the backup retention period to a number greater than 0\. For information about modifying a DB instance, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\.

If you deleted a DB instance, you can delete its automated DB snapshots by removing the automated backups for the DB instance\. For information about automated backups, see [Working With Backups](USER_WorkingWithAutomatedBackups.md)\.

### Console<a name="USER_DeleteSnapshot.CON"></a>

**To delete a DB snapshot**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Snapshots**\.

1. Choose the DB snapshot that you want to delete\.

1. For **Actions**, choose **Delete Snapshot**\. 

1. Choose **Delete** on the confirmation page\. 

### AWS CLI<a name="USER_DeleteSnapshot.CLI"></a>

You can delete a DB snapshot by using the AWS CLI command [delete\-db\-snapshot](https://docs.aws.amazon.com/cli/latest/reference/rds/delete-db-snapshot.html)\. 

The following options are used to delete a DB snapshot\. 
+ `--db-snapshot-identifier` – The identifier for the DB snapshot\. 

**Example**  
The following code deletes the `mydbsnapshot` DB snapshot\.   
For Linux, macOS, or Unix:  

```
1. aws rds delete-db-snapshot \
2.     --db-snapshot-identifier mydbsnapshot
```
For Windows:  

```
1. aws rds delete-db-snapshot ^
2.     --db-snapshot-identifier mydbsnapshot
```

### RDS API<a name="USER_DeleteSnapshot.API"></a>

You can delete a DB snapshot by using the Amazon RDS API operation [DeleteDBSnapshot](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DeleteDBSnapshot.html)\. 

The following parameters are used to delete a DB snapshot\. 
+ `DBSnapshotIdentifier` – The identifier for the DB snapshot\. 