# Restoring from a snapshot to a Multi\-AZ DB cluster<a name="USER_RestoreFromMultiAZDBClusterSnapshot.Restoring"></a>

You can restore a snapshot to a Multi\-AZ DB cluster using the AWS Management Console, the AWS CLI, or the RDS API\. You can restore each of these types of snapshots to a Multi\-AZ DB cluster:
+ A snapshot of a Single\-AZ deployment
+ A snapshot of a Multi\-AZ DB instance deployment with a single DB instance
+ A snapshot of a Multi\-AZ DB cluster

For information about Multi\-AZ deployments, see [Multi\-AZ deployments for high availability](Concepts.MultiAZ.md)\.

**Tip**  
You can migrate a Single\-AZ deployment or a Multi\-AZ DB instance deployment to a Multi\-AZ DB cluster deployment by restoring a snapshot\.

## Console<a name="USER_RestoreFromMultiAZDBClusterSnapshot.CON"></a>

**To restore a snapshot to a Multi\-AZ DB cluster**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Snapshots**\.

1. Choose the snapshot that you want to restore from\.

1. For **Actions**, choose **Restore snapshot**\.

1. On the **Restore snapshot** page, in **Availability and durability**, choose **Multi\-AZ DB cluster**\.  
![\[Multi-AZ DB cluster choice\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/multi-az-db-cluster-create.png)

1. For **DB cluster identifier**, enter the name for your restored Multi\-AZ DB cluster\.

1. For the remaining sections, specify your DB cluster settings\. For information about each setting, see [Settings for creating Multi\-AZ DB clusters](create-multi-az-db-cluster.md#create-multi-az-db-cluster-settings)\.

1. Choose **Restore DB instance**\. 

## AWS CLI<a name="USER_RestoreFromMultiAZDBClusterSnapshot.CLI"></a>

To restore a snapshot to a Multi\-AZ DB cluster, use the AWS CLI command [restore\-db\-cluster\-from\-snapshot](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-cluster-from-snapshot.html)\.

In the following example, you restore from a previously created snapshot named `mysnapshot`\. You restore to a new Multi\-AZ DB cluster named `mynewmultiazdbcluster`\. You also specify the DB instance class used by the DB instances in the Multi\-AZ DB cluster\. Specify either `mysql` or `postgres` for the DB engine\.

For the `--snapshot-identifier` option, you can use either the name or the Amazon Resource Name \(ARN\) to specify a DB cluster snapshot\. However, you can use only the ARN to specify a DB snapshot\.

For the `--db-cluster-instance-class` option, specify the DB instance class for the new Multi\-AZ DB cluster\. Multi\-AZ DB clusters only support specific DB instance classes, such as the db\.m6gd and db\.r6gd DB instance classes\. For more information about DB instance classes, see [DB instance classes](Concepts.DBInstanceClass.md)\.

You can also specify other options\.

**Example**  
For Linux, macOS, or Unix:  

```
1. aws rds restore-db-cluster-from-snapshot \
2.     --db-cluster-identifier mynewmultiazdbcluster \
3.     --snapshot-identifier mysnapshot \
4.     --engine mysql|postgres \
5.     --db-cluster-instance-class db.r6gd.xlarge
```
For Windows:  

```
1. aws rds restore-db-cluster-from-snapshot ^
2.     --db-cluster-identifier mynewmultiazdbcluster ^
3.     --snapshot-identifier mysnapshot ^
4.     --engine mysql|postgres ^
5.     --db-cluster-instance-class db.r6gd.xlarge
```

After you restore the DB cluster, you can add the Multi\-AZ DB cluster to the security group associated with the DB cluster or DB instance that you used to create the snapshot, if applicable\. Completing this action provides the same functions of the previous DB cluster or DB instance\.

## RDS API<a name="USER_RestoreFromMultiAZDBClusterSnapshot.API"></a>

To restore a snapshot to a Multi\-AZ DB cluster, call the RDS API operation [RestoreDBClusterFromSnapshot](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBClusterFromSnapshot.html) with the following parameters: 
+ `DBClusterIdentifier` 
+ `SnapshotIdentifier` 
+ `Engine` 

You can also specify other optional parameters\.

After you restore the DB cluster, you can add the Multi\-AZ DB cluster to the security group associated with the DB cluster or DB instance that you used to create the snapshot, if applicable\. Completing this action provides the same functions of the previous DB cluster or DB instance\.