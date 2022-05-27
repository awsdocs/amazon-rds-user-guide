# Deleting a Multi\-AZ DB cluster<a name="USER_DeleteMultiAZDBCluster.Deleting"></a>

You can delete a DB Multi\-AZ DB cluster using the AWS Management Console, the AWS CLI, or the RDS API\.

The time required to delete a Multi\-AZ DB cluster can vary depending on certain factors\. These are the backup retention period \(that is, how many backups to delete\), how much data is deleted, and whether a final snapshot is taken\.

You can't delete a Multi\-AZ DB cluster when deletion protection is turned on for it\. For more information, see [Deletion protection](USER_DeleteInstance.md#USER_DeleteInstance.DeletionProtection)\. You can turn off deletion protection by modifying the Multi\-AZ DB cluster\. For more information, see [Modifying a Multi\-AZ DB cluster](modify-multi-az-db-cluster.md)\.

## Console<a name="USER_DeleteMultiAZDBCluster.Deleting.CON"></a>

**To delete a Multi\-AZ DB cluster**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then choose the Multi\-AZ DB cluster that you want to delete\.

1. For **Actions**, choose **Delete**\.

1. Choose **Create final snapshot?** to create a final DB snapshot for the Multi\-AZ DB cluster\. 

   If you create a final snapshot, enter a name for **Final snapshot name**\.

1. Choose **Retain automated backups** to retain automated backups\.

1. Enter **delete me** in the box\.

1. Choose **Delete**\.

## AWS CLI<a name="USER_DeleteMultiAZDBCluster.Deleting.CLI"></a>

To delete a Multi\-AZ DB cluster by using the AWS CLI, call the [delete\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/delete-db-cluster.html) command with the following options:
+ `--db-cluster-identifier`
+ `--final-db-snapshot-identifier` or `--skip-final-snapshot`

**Example With a final snapshot**  
For Linux, macOS, or Unix:  

```
aws rds delete-db-cluster \
    --db-cluster-identifier mymultiazdbcluster \
    --final-db-snapshot-identifier mymultiazdbclusterfinalsnapshot
```
For Windows:  

```
aws rds delete-db-cluster ^
    --db-cluster-identifier mymultiazdbcluster ^
    --final-db-snapshot-identifier mymultiazdbclusterfinalsnapshot
```

**Example With no final snapshot**  
For Linux, macOS, or Unix:  

```
aws rds delete-db-cluster \
    --db-cluster-identifier mymultiazdbcluster \
    --skip-final-snapshot
```
For Windows:  

```
aws rds delete-db-cluster ^
    --db-cluster-identifier mymultiazdbcluster ^
    --skip-final-snapshot
```

## RDS API<a name="USER_DeleteMultiAZDBCluster.Deleting.API"></a>

To delete a Multi\-AZ DB cluster by using the Amazon RDS API, call the [DeleteDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DeleteDBCluster.html) operation with the following parameters:
+ `DBClusterIdentifier`
+ `FinalDBSnapshotIdentifier` or `SkipFinalSnapshot`