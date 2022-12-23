# Renaming a Multi\-AZ DB cluster<a name="multi-az-db-cluster-rename"></a>

You can rename a Multi\-AZ DB cluster by using the AWS Management Console, the AWS CLI `modify-db-cluster` command, or the Amazon RDS API `ModifyDBCluster` operation\. Renaming a Multi\-AZ DB cluster can have significant effects\. The following is a list of considerations before you rename a Multi\-AZ DB cluster\.
+ When you rename a Multi\-AZ DB cluster, the cluster endpoints for the Multi\-AZ DB cluster change\. These endpoints change because they include the name you assigned to the Multi\-AZ DB cluster\. You can redirect traffic from an old endpoint to a new one\. For more information about Multi\-AZ DB cluster endpoints, see [Managing connections for Multi\-AZ DB clusters](multi-az-db-clusters-concepts.md#multi-az-db-clusters-concepts-connection-management)\.
+ When you rename a Multi\-AZ DB cluster, the old DNS name that was used by the Multi\-AZ DB cluster is deleted, although it could remain cached for a few minutes\. The new DNS name for the renamed Multi\-AZ DB cluster becomes effective in about two minutes\. The renamed Multi\-AZ DB cluster isn't available until the new name becomes effective\.
+ You can't use an existing Multi\-AZ DB cluster name when renaming a cluster\.
+ Metrics and events associated with the name of a Multi\-AZ DB cluster are maintained if you reuse a DB cluster name\.
+ Multi\-AZ DB cluster tags remain with the Multi\-AZ DB cluster, regardless of renaming\.
+ DB cluster snapshots are retained for a renamed Multi\-AZ DB cluster\.

**Note**  
A Multi\-AZ DB cluster is an isolated database environment running in the cloud\. A Multi\-AZ DB cluster can host multiple databases\. For information about changing a database name, see the documentation for your DB engine\.

## Renaming to replace an existing Multi\-AZ DB cluster<a name="multi-az-db-cluster-rename-to-replace"></a>

The most common scenarios for renaming a Multi\-AZ DB cluster include restoring data from a DB cluster snapshot or performing point\-in\-time recovery \(PITR\)\. By renaming the Multi\-AZ DB cluster, you can replace the Multi\-AZ DB cluster without changing any application code that references the Multi\-AZ DB cluster\. In these cases, complete the following steps: 

1. Stop all traffic going to the Multi\-AZ DB cluster\. You can redirect traffic from accessing the databases on the Multi\-AZ DB cluster, or choose another way to prevent traffic from accessing your databases on the Multi\-AZ DB cluster\. 

1. Rename the existing Multi\-AZ DB cluster\.

1. Create a new Multi\-AZ DB cluster by restoring from a DB cluster snapshot or recovering to a point in time\. Then, give the new Multi\-AZ DB cluster the name of the previous Multi\-AZ DB cluster\.

If you delete the old Multi\-AZ DB cluster, you are responsible for deleting any unwanted DB cluster snapshots of the old Multi\-AZ DB cluster\.

## Console<a name="multi-az-db-cluster-rename.CON"></a>

**To rename a Multi\-AZ DB cluster**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the Multi\-AZ DB cluster that you want to rename\.

1. Choose **Modify**\.

1. In **Settings**, enter a new name for **DB cluster identifier**\.

1. Choose **Continue**\.

1. To apply the changes immediately, choose **Apply immediately**\. Choosing this option can cause an outage in some cases\. For more information, see [Using the Apply Immediately setting](modify-multi-az-db-cluster.md#modify-multi-az-db-cluster-apply-immediately)\. 

1. On the confirmation page, review your changes\. If they are correct, choose **Modify cluster** to save your changes\.

   Alternatively, choose **Back** to edit your changes, or choose **Cancel** to discard your changes\.

## AWS CLI<a name="multi-az-db-cluster-rename.CLI"></a>

To rename a Multi\-AZ DB cluster, use the AWS CLI command [ modify\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html)\. Provide the current `--db-cluster-identifier` value and `--new-db-cluster-identifier` parameter with the new name of the Multi\-AZ DB cluster\.

**Example**  
For Linux, macOS, or Unix:  

```
1. aws rds modify-db-cluster \
2.     --db-cluster-identifier DBClusterIdentifier \
3.     --new-db-cluster-identifier NewDBClusterIdentifier
```
For Windows:  

```
1. aws rds modify-db-cluster ^
2.     --db-cluster-identifier DBClusterIdentifier ^
3.     --new-db-cluster-identifier NewDBClusterIdentifier
```

## RDS API<a name="multi-az-db-cluster-rename.API"></a>

To rename a Multi\-AZ DB cluster, call the Amazon RDS API operation [ ModifyDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) with the following parameters:
+ `DBClusterIdentifier` – The existing name of the DB cluster\.
+ `NewDBClusterIdentifier` – The new name of the DB cluster\.