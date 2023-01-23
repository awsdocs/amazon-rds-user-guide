# Restoring from a Multi\-AZ DB cluster snapshot to a DB instance<a name="USER_RestoreFromMultiAZDBClusterSnapshot"></a>

A *Multi\-AZ DB cluster snapshot* is a storage volume snapshot of your DB cluster, backing up the entire DB cluster and not just individual databases\. You can restore a Multi\-AZ DB cluster snapshot to a Single\-AZ deployment or Multi\-AZ DB instance deployment\. For information about Multi\-AZ deployments, see [Multi\-AZ deployments for high availability](Concepts.MultiAZ.md)\.

**Note**  
You can also restore a Multi\-AZ DB cluster snapshot to a new Multi\-AZ DB cluster\. For instructions, see [Restoring from a snapshot to a Multi\-AZ DB cluster](USER_RestoreFromMultiAZDBClusterSnapshot.Restoring.md)\.

Use the AWS Management Console, the AWS CLI, or the RDS API to restore a Multi\-AZ DB cluster snapshot to a Single\-AZ deployment or Multi\-AZ DB instance deployment\.

## Console<a name="USER_RestoreFromMultiAZDBClusterSnapshot.CON"></a>

**To restore a Multi\-AZ DB cluster snapshot to a Single\-AZ deployment or Multi\-AZ DB instance deployment**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Snapshots**\.

1. Choose the Multi\-AZ DB cluster snapshot that you want to restore from\.

1. For **Actions**, choose **Restore snapshot**\.

1. On the **Restore snapshot** page, in **Availability and durability**, choose one of the following:
   + **Single DB instance** – Restores the snapshot to one DB instance with no standby DB instance\.
   + **Multi\-AZ DB instance** – Restores the snapshot to a Multi\-AZ DB instance deployment with one primary DB instance and one standby DB instance\.

1. For **DB instance identifier**, enter the name for your restored DB instance\.

1. For the remaining sections, specify your DB instance settings\. For information about each setting, see [Settings for DB instances](USER_CreateDBInstance.md#USER_CreateDBInstance.Settings)\.

1. Choose **Restore DB instance**\.

## AWS CLI<a name="USER_RestoreFromMultiAZDBClusterSnapshot.CLI"></a>

To restore a Multi\-AZ DB cluster snapshot to a DB instance deployment, use the AWS CLI command [restore\-db\-instance\-from\-db\-snapshot](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-from-db-snapshot.html)\.

In the following example, you restore from a previously created Multi\-AZ DB cluster snapshot named `myclustersnapshot`\. You restore to a new Multi\-AZ DB instance deployment with a primary DB instance named `mynewdbinstance`\. For the `--db-cluster-snapshot-identifier` option, specify the name of the Multi\-AZ DB cluster snapshot\.

For the `--db-instance-class` option, specify the DB instance class for the new DB instance deployment\. For more information about DB instance classes, see [DB instance classes](Concepts.DBInstanceClass.md)\.

You can also specify other options\.

**Example**  
For Linux, macOS, or Unix:  

```
1. aws rds restore-db-instance-from-db-snapshot \
2.     --db-instance-identifier mynewdbinstance \
3.     --db-cluster-snapshot-identifier myclustersnapshot \
4.     --engine mysql \
5.     --multi-az \
6.     --db-instance-class db.r6g.xlarge
```
For Windows:  

```
1. aws rds restore-db-instance-from-db-snapshot ^
2.     --db-instance-identifier mynewdbinstance ^
3.     --db-cluster-snapshot-identifier myclustersnapshot ^
4.     --engine mysql ^
5.     --multi-az ^
6.     --db-instance-class db.r6g.xlarge
```

After you restore the DB instance, you can add it to the security group associated with the Multi\-AZ DB cluster that you used to create the snapshot, if applicable\. Completing this action provides the same functions of the previous Multi\-AZ DB cluster\.

## RDS API<a name="USER_RestoreFromMultiAZDBClusterSnapshot.API"></a>

To restore a Multi\-AZ DB cluster snapshot to a DB instance deployment, call the RDS API operation [RestoreDBInstanceFromDBSnapshot](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBInstanceFromDBSnapshot.html) with the following parameters:
+ `DBInstanceIdentifier` 
+ `DBClusterSnapshotIdentifier` 
+ `Engine` 

You can also specify other optional parameters\.

After you restore the DB instance, you can add it to the security group associated with the Multi\-AZ DB cluster that you used to create the snapshot, if applicable\. Completing this action provides the same functions of the previous Multi\-AZ DB cluster\.