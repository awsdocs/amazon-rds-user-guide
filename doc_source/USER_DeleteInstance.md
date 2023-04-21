# Deleting a DB instance<a name="USER_DeleteInstance"></a>

You can delete a DB instance using the AWS Management Console, the AWS CLI, or the RDS API\. If you want to delete a DB instance in an Aurora DB cluster, see [Deleting Aurora DB clusters and DB instances](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/USER_DeleteCluster.html)\.

**Topics**
+ [Prerequisites for deleting a DB instance](#USER_DeleteInstance.DeletionProtection)
+ [Considerations when deleting a DB instance](#USER_DeleteInstance.Snapshot)
+ [Deleting a DB instance](#USER_DeleteInstance.Deleting)

## Prerequisites for deleting a DB instance<a name="USER_DeleteInstance.DeletionProtection"></a>

Before you try to delete your DB instance, make sure that deletion protection is turned off\. By default, deletion protection is turned on for a DB instance that was created with the console\. 

If your DB instance has deletion protection turned on, you can turn it off by modifying your instance settings\. Choose **Modify** in the database details page or call the [modify\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) command\. This operation doesn't cause an outage\. For more information, see [Settings for DB instances](Overview.DBInstance.Modifying.md#USER_ModifyInstance.Settings)\.

## Considerations when deleting a DB instance<a name="USER_DeleteInstance.Snapshot"></a>

Deleting a DB instance has an effect on instance recoverability, backup availability, and read replica status\. Consider the following issues:
+ You can choose whether to create a final DB snapshot\. You have the following options:
  + If you take a final snapshot, you can use it to restore your deleted DB instance\. RDS retains both the final snapshot and any manual snapshots that you took previously\. You can't create a final DB snapshot of your DB instance if it has the status `creating`, `failed`, `incompatible-restore`, or `incompatible-network`\. For more information, see [Viewing Amazon RDS DB instance status](accessing-monitoring.md#Overview.DBInstance.Status)\.
  + If you don't take a final snapshot, deletion is faster\. However, you can't use a final snapshot to restore your DB instance\. If you later decide to restore your deleted DB instance, either retain automated backups or use an earlier manual snapshot to restore your DB instance to the point in time of the snapshot\.
+ You can choose whether to retain automated backups\. You have the following options:
  + If you retain automated backups, RDS keeps them for the retention period that is in effect for the DB instance at the time when you delete it\. You can use automated backups to restore your DB instance to a time during but not after your retention period\. The retention period is in effect regardless of whether you create a final DB snapshot\. To delete a retained automated backup, see [Deleting retained automated backups](USER_WorkingWithAutomatedBackups.md#USER_WorkingWithAutomatedBackups-Deleting)\.
  + If you don't retain automated backups, RDS deletes the automated backups that reside in the same AWS Region as your DB instance\. You can't recover these backups\. If your automated backups have been replicated to another AWS Region, RDS keeps them even if you don't choose to retain automated backups\. For more information, see [Replicating automated backups to another AWS Region](USER_ReplicateBackups.md)\.
**Note**  
Typically, if you create a final DB snapshot, you don't need to retain automated backups\.
+ When you delete your DB instance, RDS doesn't delete manual DB snapshots\. For more information, see [Creating a DB snapshot](USER_CreateSnapshot.md)\.
+ If you want to delete all RDS resources, note that the following resources incur billing charges:
  + DB instances
  + DB snapshots
  + DB clusters

  If you purchased reserved instances, then they are billed according to contract that you agreed to when you purchased the instance\. For more information, see [Reserved DB instances for Amazon RDS](USER_WorkingWithReservedDBInstances.md)\. You can get billing information for all your AWS resources by using the AWS Cost Explorer\. For more information, see [Analyzing your costs with AWS Cost Explorer](https://docs.aws.amazon.com/cost-management/latest/userguide/ce-what-is.html)\.
+ If you delete a DB instance that has read replicas in the same AWS Region, each read replica is automatically promoted to a standalone DB instance\. For more information, see [Promoting a read replica to be a standalone DB instance](USER_ReadRepl.md#USER_ReadRepl.Promote)\. If your DB instance has read replicas in different AWS Regions, see [Cross\-Region replication considerations](USER_ReadRepl.md#USER_ReadRepl.XRgn.Cnsdr) for information related to deleting the source DB instance for a cross\-Region read replica\.
+ When the status for a DB instance is `deleting`, its CA certificate value doesn't appear in the RDS console or in output for AWS CLI commands or RDS API operations\. For more information about CA certificates, see [Using SSL/TLS to encrypt a connection to a DB instance](UsingWithRDS.SSL.md)\.
+ The time required to delete a DB instance varies depending on the backup retention period \(that is, how many backups to delete\), how much data is deleted, and whether a final snapshot is taken\.

## Deleting a DB instance<a name="USER_DeleteInstance.Deleting"></a>

You can delete a DB instance using the AWS Management Console, the AWS CLI, or the RDS API\. You must do the following:
+ Provide the name of the DB instance
+ Enable or disable the option to take a final DB snapshot of the instance
+ Enable or disable the option to retain automated backups

**Note**  
You can't delete a DB instance when deletion protection is turned on\. For more information, see [Prerequisites for deleting a DB instance](#USER_DeleteInstance.DeletionProtection)\.

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

To find the instance IDs of the DB instances in your account, call the [describe\-db\-instances](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instances.html) command:

```
aws rds describe-db-instances --query 'DBInstances[*].[DBInstanceIdentifier]' --output text
```

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