# Creating a Multi\-AZ DB cluster snapshot<a name="USER_CreateMultiAZDBClusterSnapshot"></a>

When you create a Multi\-AZ DB cluster snapshot, make sure to identify which Multi\-AZ DB cluster you are going to back up, and then give your DB cluster snapshot a name so you can restore from it later\.

You can create a Multi\-AZ DB cluster snapshot using the AWS Management Console, the AWS CLI, or the RDS API\.

## Console<a name="USER_CreateMultiAZDBClusterSnapshot.CON"></a>

**To create a DB cluster snapshot**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. In the list, choose the Multi\-AZ DB cluster for which you want to take a snapshot\.

1. For **Actions**, choose **Take snapshot**\.

   The **Take DB snapshot** window appears\.

1. For **Snapshot name**, enter the name of the snapshot\.

1. Choose **Take snapshot**\.

The **Snapshots** page appears, with the new Multi\-AZ DB cluster snapshot's status shown as `Creating`\. After its status is `Available`, you can see its creation time\.

## AWS CLI<a name="USER_CreateMultiAZDBClusterSnapshot.CLI"></a>

You can create a Multi\-AZ DB cluster snapshot by using the AWS CLI [ create\-db\-cluster\-snapshot](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster-snapshot.html) command with the following options:
+ `--db-cluster-identifier`
+ `--db-cluster-snapshot-identifier`

In this example, you create a Multi\-AZ DB cluster snapshot called *`mymultiazdbclustersnapshot`* for a DB cluster called *`mymultiazdbcluster`*\.

**Example**  
For Linux, macOS, or Unix:  

```
1. aws rds create-db-cluster-snapshot \
2.     --db-cluster-identifier mymultiazdbcluster \
3.     --db-cluster-snapshot-identifier mymultiazdbclustersnapshot
```
For Windows:  

```
1. aws rds create-db-cluster-snapshot ^
2.     --db-cluster-identifier mymultiazdbcluster ^
3.     --db-cluster snapshot-identifier mymultiazdbclustersnapshot
```

## RDS API<a name="USER_CreateMultiAZDBClusterSnapshot.API"></a>

You can create a Multi\-AZ DB cluster snapshot by using the Amazon RDS API [CreateDBClusterSnapshot](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBClusterSnapshot.html) operation with the following parameters:
+ `DBClusterIdentifier`
+ `DBClusterSnapshotIdentifier`