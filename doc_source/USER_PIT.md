# Restoring a DB instance to a specified time<a name="USER_PIT"></a>

You can restore a DB instance to a specific point in time, creating a new DB instance\.

When you restore a DB instance to a point in time, you can choose the default virtual private cloud \(VPC\) security group\. Or you can apply a custom VPC security group to your DB instance\.

Restored DB instances are automatically associated with the default DB parameter and option groups\. However, you can apply a custom parameter group and option group by specifying them during a restore\.

If the source DB instance has resource tags, RDS adds the latest tags to the restored DB instance\.

RDS uploads transaction logs for DB instances to Amazon S3 every five minutes\. To see the latest restorable time for a DB instance, use the AWS CLI [describe\-db\-instances](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instances.html) command and look at the value returned in the `LatestRestorableTime` field for the DB instance\. To see the latest restorable time for each DB instance in the Amazon RDS console, choose **Automated backups**\.

You can restore to any point in time within your backup retention period\. To see the earliest restorable time for each DB instance, choose **Automated backups** in the Amazon RDS console\.

![\[Automated backups\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/automated-backups.png)

**Note**  
We recommend that you restore to the same or similar DB instance size—and IOPS if using Provisioned IOPS storage—as the source DB instance\. You might get an error if, for example, you choose a DB instance size with an incompatible IOPS value\.

Some of the database engines used by Amazon RDS have special considerations when restoring from a point in time:
+ When you restore an Oracle DB instance to a point in time, you can specify a different Oracle DB engine, license model, and DBName \(SID\) to be used by the new DB instance\.
+ When you restore a Microsoft SQL Server DB instance to a point in time, each database within that instance is restored to a point in time within 1 second of each other database within the instance\. Transactions that span multiple databases within the instance might be restored inconsistently\.
+ For a SQL Server DB instance, the `OFFLINE`, `EMERGENCY`, and `SINGLE_USER` modes aren't supported\. Setting any database into one of these modes causes the latest restorable time to stop moving ahead for the whole instance\.
+ Some actions, such as changing the recovery model of a SQL Server database, can break the sequence of logs that are used for point\-in\-time recovery\. In some cases, Amazon RDS can detect this issue and the latest restorable time is prevented from moving forward\. In other cases, such as when a SQL Server database uses the `BULK_LOGGED` recovery model, the break in log sequence isn't detected\. It might not be possible to restore a SQL Server DB instance to a point in time if there is a break in the log sequence\. For these reasons, Amazon RDS doesn't support changing the recovery model of SQL Server databases\.

You can also use AWS Backup to manage backups of Amazon RDS DB instances\. If your DB instance is associated with a backup plan in AWS Backup, that backup plan is used for point\-in\-time recovery\. Backups that were created with AWS Backup have names ending in `awsbackup:AWS-Backup-job-number`\. For information about AWS Backup, see the [https://docs.aws.amazon.com/aws-backup/latest/devguide](https://docs.aws.amazon.com/aws-backup/latest/devguide)\.

**Note**  
Information in this topic applies to Amazon RDS\. For information on restoring an Amazon Aurora DB cluster, see [Restoring a DB cluster to a specified time](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/aurora-pitr.html)\.

You can restore a DB instance to a point in time using the AWS Management Console, the AWS CLI, or the RDS API\.

**Note**  
You can't reduce the amount of storage when you restore a DB instance\. When you increase the allocated storage, it must be by at least 10 percent\. If you try to increase the value by less than 10 percent, you get an error\. You can't increase the allocated storage when restoring RDS for SQL Server DB instances\.

## Console<a name="USER_PIT.CON"></a>

**To restore a DB instance to a specified time**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Automated backups**\.

   The automated backups are displayed on the **Current Region** tab\.

1. Choose the DB instance that you want to restore\.

1. For **Actions**, choose **Restore to point in time**\.

   The **Restore to point in time** window appears\.

1. Choose **Latest restorable time** to restore to the latest possible time, or choose **Custom** to choose a time\.

   If you chose **Custom**, enter the date and time to which you want to restore the instance\.
**Note**  
Times are shown in your local time zone, which is indicated by an offset from Coordinated Universal Time \(UTC\)\. For example, UTC\-5 is Eastern Standard Time/Central Daylight Time\.

1. For **DB instance identifier**, enter the name of the target restored DB instance\. The name must be unique\.

1. Choose other options as needed, such as DB instance class, storage, and whether you want to use storage autoscaling\.

   For information about each setting, see [Settings for DB instances](USER_CreateDBInstance.md#USER_CreateDBInstance.Settings)\.

1. Choose **Restore to point in time**\.

## AWS CLI<a name="USER_PIT.CLI"></a>

To restore a DB instance to a specified time, use the AWS CLI command [restore\-db\-instance\-to\-point\-in\-time](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-to-point-in-time.html) to create a new DB instance\. This example also sets the allocated storage size and enables storage autoscaling\.

Resource tagging is supported for this operation\. When you use the `--tags` option, the source DB instance tags are ignored and the provided ones are used\. Otherwise, the latest tags from the source instance are used\.

You can specify other settings\. For information about each setting, see [Settings for DB instances](USER_CreateDBInstance.md#USER_CreateDBInstance.Settings)\.

**Example**  
For Linux, macOS, or Unix:  

```
1. aws rds restore-db-instance-to-point-in-time \
2.     --source-db-instance-identifier mysourcedbinstance \
3.     --target-db-instance-identifier mytargetdbinstance \
4.     --restore-time 2017-10-14T23:45:00.000Z \
5.     --allocated-storage 100 \
6.     --max-allocated-storage 1000
```
For Windows:  

```
1. aws rds restore-db-instance-to-point-in-time ^
2.     --source-db-instance-identifier mysourcedbinstance ^
3.     --target-db-instance-identifier mytargetdbinstance ^
4.     --restore-time 2017-10-14T23:45:00.000Z ^
5.     --allocated-storage 100 ^
6.     --max-allocated-storage 1000
```

## RDS API<a name="USER_PIT.API"></a>

To restore a DB instance to a specified time, call the Amazon RDS API [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBInstanceToPointInTime.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBInstanceToPointInTime.html) operation with the following parameters:
+ `SourceDBInstanceIdentifier`
+ `TargetDBInstanceIdentifier`
+ `RestoreTime`