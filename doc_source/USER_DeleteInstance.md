# Deleting a DB Instance<a name="USER_DeleteInstance"></a>

To delete a DB instance, you must do the following:
+ Provide the name of the instance
+ Enable or disable the option to take a final DB snapshot of the instance
+ Enable or disable the option to retain automated backups

You can only delete instances that don't have deletion protection enabled\. When you create or modify a DB instance, you have the option to enable deletion protection so that users can't delete the DB instance\. Deletion protection is disabled by default for you when you use AWS CLI and API commands\. Deletion protection is enabled for you when you use the AWS Management Console to create a production DB instance\. However, Amazon RDS enforces deletion protection when you use the console, the CLI, or the API to delete a DB instance\. To delete a DB instance that has deletion protection enabled, first modify the instance and disable deletion protection\.

If the DB instance that you want to delete has a Read Replica, you should either promote the Read Replica or delete it\. For more information, see [Promoting a Read Replica to Be a Standalone DB Instance](USER_ReadRepl.md#USER_ReadRepl.Promote)\. 

## Creating a Final Snapshot and Retaining Automated Backups<a name="USER_DeleteInstance.Snapshot"></a>

When you delete a DB instance, you can choose whether to create a final snapshot of the DB instance\. You can also choose to retain automated backups after the DB instance is deleted\. To be able to restore the DB instance at a later time, create a final snapshot or retain automated backups\. 


****  

|  | With Final Snapshot | Without Final Snapshot | Retain Automated Backups | 
| --- | --- | --- | --- | 
|  How to choose  |  To be able to restore your deleted DB instance at a later time, create a final DB snapshot\.   |  To delete a DB instance quickly, you can skip creating a final DB snapshot\.   If you skip the snapshot, to restore your DB instance you need one of the following:  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_DeleteInstance.html)  | Instead of creating a snapshot, you can choose to enable Retain automated backups when you delete a DB instance\. These backups are still subject to the retention period of the DB instance and age out the same way systems snapshots do\. | 
|  Automated backups  |  All automated backups are deleted and can't be recovered, unless you enable **Retain automated backups**\.  |  All automated backups are deleted and can't be recovered, unless you choose to retain automated backups when you delete the DB instance\.  | Automated backups are retained for a set period of time, regardless of whether you chose to create a final snapshot\. They are retained for retention period that was set on the DB instance at the time you deleted it\. | 
|  Manual snapshots  |  Earlier manual snapshots aren't deleted\.   |  Earlier manual snapshots aren't deleted\.   |  No snapshots are deleted\.   | 

You can't create a final snapshot of your DB instance if it has the status `creating`, `failed`, `incompatible-restore`, or `incompatible-network`\. For more information about DB instance statuses, see [DB Instance Status](Overview.DBInstance.Status.md)\. 

## Deleting a DB Instance by Using the Console, CLI, and API<a name="USER_DeleteInstance.Deleting"></a>

You can delete a DB instance using the AWS Management Console, the AWS CLI, or the RDS API\.

### Console<a name="USER_DeleteInstance.CON"></a>

**To delete a DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then choose the DB instance that you want to delete\. 

1. For **Actions**, choose **Delete**\. 

1. To create a final DB snapshot for the DB instance, enable **Create final snapshot?**\. 

1. If you enabled **Create final snapshot?** in the previous step, for **Final snapshot name** enter the name of your final DB snapshot\. 

1. To retain automated backups, choose **Retain automated backups**\.

1. Enter **delete me** in the box\.

1. Choose **Delete**\. 

### AWS CLI<a name="USER_DeleteInstance.CLI"></a>

To delete a DB instance by using the AWS CLI, call the [delete\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/delete-db-instance.html) command with the following options: 
+ `--db-instance-identifier`
+ `--final-db-snapshot-identifier` or `--skip-final-snapshot`

**Example With a final snapshot and no retained automated backups**  
For Linux, OS X, or Unix:  

```
aws rds delete-db-instance \
    --db-instance-identifier mydbinstance  \
    --final-db-snapshot-identifier mydbinstancefinalsnapshot \
    --delete-automated-backups
```
For Windows:  

```
aws rds delete-db-instance ^
    --db-instance-identifier mydbinstance  ^
    --final-db-snapshot-identifier mydbinstancefinalsnapshot ^
    --delete-automated-backups
```

**Example With retained automated backups and no final snapshot**  
For Linux, OS X, or Unix:  

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

To delete a DB instance by using the Amazon RDS API, call the [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DeleteDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DeleteDBInstance.html) action with the following parameters: 
+ `DBInstanceIdentifier`
+ `FinalDBSnapshotIdentifier` or `SkipFinalSnapshot`

**Example With a final snapshot and no retained automated backups**  

```
https://rds.amazonaws.com/ 
    ?Action=DeleteDBInstance
    &DBInstanceIdentifier=mydbinstance
    &FinalDBSnapshotIdentifier=mydbinstancefinalsnapshot
    &DeleteAutomatedBackups=true
    &SignatureMethod=HmacSHA256
    &SignatureVersion=4
    &Version=2014-10-31
    &X-Amz-Algorithm=AWS4-HMAC-SHA256
    &X-Amz-Credential=AKIADQKE4SARGYLE/20140305/us-west-1/rds/aws4_request
    &X-Amz-Date=20140305T185838Z
    &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
    &X-Amz-Signature=b441901545441d3c7a48f63b5b1522c5b2b37c137500c93c45e209d4b3a064a3
```

**Example With retained automated backups and no final snapshot**  

```
https://rds.amazonaws.com/
    ?Action=DeleteDBInstance
    &DBInstanceIdentifier=mydbinstance
    &SkipFinalSnapshot=true
    &DeleteAutomatedBackups=false
    &SignatureMethod=HmacSHA256
    &SignatureVersion=4
    &Version=2014-10-31
    &X-Amz-Algorithm=AWS4-HMAC-SHA256
    &X-Amz-Credential=AKIADQKE4SARGYLE/20140305/us-west-1/rds/aws4_request
    &X-Amz-Date=20140305T185838Z
    &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
    &X-Amz-Signature=b441901545441d3c7a48f63b5b1522c5b2b37c137500c93c45e209d4b3a064a3
```