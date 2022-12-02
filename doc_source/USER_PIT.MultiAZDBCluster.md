# Restoring a Multi\-AZ DB cluster to a specified time<a name="USER_PIT.MultiAZDBCluster"></a>

You can restore a Multi\-AZ DB cluster to a specific point in time, creating a new Multi\-AZ DB cluster\.

RDS uploads transaction logs for Multi\-AZ DB clusters to Amazon S3 continuously\. You can restore to any point in time within your backup retention period\. To see the earliest restorable time for a Multi\-AZ DB cluster, use the AWS CLI [describe\-db\-clusters](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-clusters.html) command\. Look at the value returned in the `EarliestRestorableTime` field for the DB cluster\. To see the latest restorable time for a Multi\-AZ DB cluster, look at the value returned in the `LatestRestorableTime` field for the DB cluster\.

When you restore a Multi\-AZ DB cluster to a point in time, you can choose the default VPC security group for your Multi\-AZ DB cluster\. Or you can apply a custom VPC security group to your Multi\-AZ DB cluster\.

Restored Multi\-AZ DB clusters are automatically associated with the default DB cluster parameter group\. However, you can apply a customer DB cluster parameter group by specifying it during a restore\.

If the source DB instance has resource tags, RDS adds the latest tags to the restored DB instance\.

**Note**  
We recommend that you restore to the same or similar Multi\-AZ DB cluster size as the source DB cluster\. We also recommend that you restore with the same or similar IOPS value if you're using Provisioned IOPS storage\. You might get an error if, for example, you choose a DB cluster size with an incompatible IOPS value\.

You can restore a Multi\-AZ DB cluster to a point in time using the AWS Management Console, the AWS CLI, or the RDS API\.

## Console<a name="USER_PIT.MultiAZDBCluster.CON"></a>

**To restore a Multi\-AZ DB cluster to a specified time**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the Multi\-AZ DB cluster that you want to restore\.

1. For **Actions**, choose **Restore to point in time**\.

   The **Restore to point in time** window appears\.

1. Choose **Latest restorable time** to restore to the latest possible time, or choose **Custom** to choose a time\.

   If you chose **Custom**, enter the date and time to which you want to restore the Multi\-AZ DB cluster\.
**Note**  
Times are shown in your local time zone, which is indicated by an offset from Coordinated Universal Time \(UTC\)\. For example, UTC\-5 is Eastern Standard Time/Central Daylight Time\.

1. For **DB cluster identifier**, enter the name for your restored Multi\-AZ DB cluster\.

1. In **Availability and durability**, choose **Multi\-AZ DB cluster**\.  
![\[Multi-AZ DB cluster choice\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/multi-az-db-cluster-create.png)

1. In **DB instance class**, choose a DB instance class\.

   Currently, Multi\-AZ DB clusters only support db\.m6gd and db\.r6gd DB instance classes\. For more information about DB instance classes, see [DB instance classes](Concepts.DBInstanceClass.md)\.

1. For the remaining sections, specify your DB cluster settings\. For information about each setting, see [Settings for creating Multi\-AZ DB clusters](create-multi-az-db-cluster.md#create-multi-az-db-cluster-settings)\.

1. Choose **Restore to point in time**\.

## AWS CLI<a name="USER_PIT.MultiAZDBCluster.CLI"></a>

To restore a Multi\-AZ DB cluster to a specified time, use the AWS CLI command [ restore\-db\-cluster\-to\-point\-in\-time](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-cluster-to-point-in-time.html) to create a new Multi\-AZ DB cluster\.

Currently, Multi\-AZ DB clusters only support db\.m6gd and db\.r6gd DB instance classes\. For more information about DB instance classes, see [DB instance classes](Concepts.DBInstanceClass.md)\.

**Example**  
For Linux, macOS, or Unix:  

```
1. aws rds restore-db-cluster-to-point-in-time \
2.     --source-db-cluster-identifier mysourcemultiazdbcluster \
3.     --db-cluster-identifier mytargetmultiazdbcluster \
4.     --restore-to-time 2021-08-14T23:45:00.000Z \
5.     --db-cluster-instance-class db.r6gd.xlarge
```
For Windows:  

```
1. aws rds restore-db-cluster-to-point-in-time ^
2.     --source-db-cluster-identifier mysourcemultiazdbcluster ^
3.     --db-cluster-identifier mytargetmultiazdbcluster ^
4.     --restore-to-time 2021-08-14T23:45:00.000Z ^
5.     --db-cluster-instance-class db.r6gd.xlarge
```

## RDS API<a name="USER_PIT.MultiAZDBCluster.API"></a>

To restore a DB cluster to a specified time, call the Amazon RDS API [RestoreDBClusterToPointInTime](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBClusterToPointInTime.html) operation with the following parameters:
+ `SourceDBClusterIdentifier`
+ `DBClusterIdentifier`
+ `RestoreToTime`