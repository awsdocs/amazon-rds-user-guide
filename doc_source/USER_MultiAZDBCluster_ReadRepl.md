# Working with Multi\-AZ DB cluster read replicas<a name="USER_MultiAZDBCluster_ReadRepl"></a>

A DB cluster read replica is a special type of cluster that you create from a source DB instance\. After you create a read replica, any updates made to the primary DB instance are asynchronously copied to the Multi\-AZ DB cluster read replica\. You can reduce the load on your primary DB instance by routing read queries from your applications to the read replica\. Using read replicas, you can elastically scale out beyond the capacity constraints of a single DB instance for read\-heavy database workloads\.

You can also create one or more DB instance read replicas from a Multi\-AZ DB cluster\. DB instance read replicas let you scale beyond the compute or I/O capacity of the source Multi\-AZ DB cluster by directing excess read traffic to the read replicas\. Currently, you can't create a Multi\-AZ DB cluster read replica from an existing Multi\-AZ DB cluster\.

**Topics**
+ [Migrating to a Multi\-AZ DB cluster using a read replica](#multi-az-db-clusters-migrating-to-with-read-replica)
+ [Creating a DB instance read replica from a Multi\-AZ DB cluster](#multi-az-db-clusters-create-instance-read-replica)

## Migrating to a Multi\-AZ DB cluster using a read replica<a name="multi-az-db-clusters-migrating-to-with-read-replica"></a>

To migrate a Single\-AZ deployment or Multi\-AZ DB instance deployment to a Multi\-AZ DB cluster deployment with reduced downtime, you can create a Multi\-AZ DB cluster read replica\. For the source, you specify the DB instance in the Single\-AZ deployment or the primary DB instance in the Multi\-AZ DB instance deployment\. The DB instance can process write transactions during the migration to a Multi\-AZ DB cluster\.

Consider the following before you create a Multi\-AZ DB cluster read replica:
+ The source DB instance must be on a version that supports Multi\-AZ DB clusters\. For more information, see [Multi\-AZ DB clusters](Concepts.RDS_Fea_Regions_DB-eng.Feature.MultiAZDBClusters.md)\.
+ The Multi\-AZ DB cluster read replica must be on the same major version as its source, and the same or higher minor version\.
+ You must turn on automatic backups on the source DB instance by setting the backup retention period to a value other than 0\.
+ The allocated storage of the source DB instance must be 100 GiB or higher\.
+ For RDS for MySQL, both the `gtid-mode` and `enforce_gtid_consistency` parameters must be set to `ON` for the source DB instance\.
+ An active, long\-running transaction can slow the process of creating the read replica\. We recommend that you wait for long\-running transactions to complete before creating a read replica\.
+ We strongly recommend that you create all read replicas in the same virtual private cloud \(VPC\) based on Amazon VPC of the source DB instance\.

  If you create a read replica in a different VPC from the source DB instance, classless inter\-domain routing \(CIDR\) ranges can overlap between the replica and the RDS system\. CIDR overlap makes the replica unstable, which can negatively impact applications connecting to it\. If you receive an error when creating the read replica, choose a different destination DB subnet group\. For more information, see [Working with a DB instance in a VPC](USER_VPC.WorkingWithRDSInstanceinaVPC.md)\.
+ If you delete the source DB instance for a Multi\-AZ DB cluster read replica, the read replica is promoted to a standalone Multi\-AZ DB cluster\.

### Creating and promoting the Multi\-AZ DB cluster read replica<a name="multi-az-db-clusters-migrating-to-create-promote"></a>

You can create and promote a Multi\-AZ DB cluster read replica using the AWS Management Console, AWS CLI, or RDS API\.

**Note**  
For RDS for MySQL, make sure that both the `gtid-mode` and `enforce_gtid_consistency` parameters are set to `ON` for the parameter group associated with the source DB instance\. You must use a custom parameter group, not the default parameter group\. For more information, see [Working with DB parameter groups](USER_WorkingWithDBInstanceParamGroups.md)\.

#### Console<a name="multi-az-db-clusters-migrating-to-create-promote-console"></a>

To migrate a Single\-AZ deployment or Multi\-AZ DB instance deployment to a Multi\-AZ DB cluster using a read replica, complete the following steps using the AWS Management Console\.

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Create the Multi\-AZ DB cluster read replica\.

   1. In the navigation pane, choose **Databases**\.

   1. Choose the DB instance that you want to use as the source for a read replica\.

   1. For **Actions**, choose **Create read replica**\.

   1. For **Availability and durability**, choose **Multi\-AZ DB cluster**\.

   1. For **DB instance identifier**, enter a name for the read replica\.

   1. For the remaining sections, specify your DB cluster settings\. For information about a setting, see [Settings for creating Multi\-AZ DB clusters](create-multi-az-db-cluster.md#create-multi-az-db-cluster-settings)\.

   1. Choose **Create read replica**\.

1. When you are ready, promote the read replica to be a standalone Multi\-AZ DB cluster:

   1. Stop any transactions from being written to the source DB instance, and then wait for all updates to be made to the read replica\.

      Database updates occur on the read replica after they have occurred on the primary DB instance\. This replication lag can vary significantly\. Use the `ReplicaLag` metric to determine when all updates have been made to the read replica\. For more information about replica lag, see [Monitoring read replication](USER_ReadRepl.md#USER_ReadRepl.Monitoring)\.

   1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

   1. In the Amazon RDS console, choose **Databases**\.

      The **Databases** pane appears\. Each read replica shows **Replica** in the **Role** column\.

   1. Choose the Multi\-AZ DB cluster read replica that you want to promote\.

   1. For **Actions**, choose **Promote**\.

   1. On the **Promote read replica** page, enter the backup retention period and the backup window for the newly promoted Multi\-AZ DB cluster\.

   1. When the settings are as you want them, choose **Promote read replica**\.

   1. Wait for the status of the promoted Multi\-AZ DB cluster to be `Available`\.

   1. Direct your applications to use the promoted Multi\-AZ DB cluster\.

   Optionally, delete the Single\-AZ deployment or Multi\-AZ DB instance deployment if it is no longer needed\. For instructions, see [Deleting a DB instance](USER_DeleteInstance.md)\.

#### AWS CLI<a name="multi-az-db-clusters-migrating-to-create-promote-cli"></a>

To migrate a Single\-AZ deployment or Multi\-AZ DB instance deployment to a Multi\-AZ DB cluster using a read replica, complete the following steps using the AWS CLI\.

1. Create the Multi\-AZ DB cluster read replica\.

   To create a read replica from the source DB instance, use the AWS CLI command [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html)\. For `--replication-source-identifier`, specify the Amazon Resource Name \(ARN\) of the source DB instance\.

   For Linux, macOS, or Unix:

   ```
   aws rds create-db-cluster \
   --db-cluster-identifier mymultiazdbcluster \
   --replication-source-identifier arn:aws:rds:us-east-2:123456789012:db:mydbinstance
   ```

   For Windows:

   ```
   aws rds create-db-cluster ^
   --db-cluster-identifier mymultiazdbcluster ^
   --replication-source-identifier arn:aws:rds:us-east-2:123456789012:db:mydbinstance
   ```

1. Stop any transactions from being written to the source DB instance, and then wait for all updates to be made to the read replica\.

   Database updates occur on the read replica after they have occurred on the primary DB instance\. This replication lag can vary significantly\. Use the `Replica Lag` metric to determine when all updates have been made to the read replica\. For more information about replica lag, see [Monitoring read replication](USER_ReadRepl.md#USER_ReadRepl.Monitoring)\.

1. When you are ready, promote the read replica to be a standalone Multi\-AZ DB cluster\.

   To promote a Multi\-AZ DB cluster read replica, use the AWS CLI command [https://docs.aws.amazon.com/cli/latest/reference/rds/promote-read-replica-db-cluster.html](https://docs.aws.amazon.com/cli/latest/reference/rds/promote-read-replica-db-cluster.html)\. For `--db-cluster-identifier`, specify the identifier of the Multi\-AZ DB cluster read replica\.

   ```
   aws rds promote-read-replica-db-cluster --db-cluster-identifier mymultiazdbcluster
   ```

1. Wait for the status of the promoted Multi\-AZ DB cluster to be `Available`\.

1. Direct your applications to use the promoted Multi\-AZ DB cluster\.

Optionally, delete the Single\-AZ deployment or Multi\-AZ DB instance deployment if it is no longer needed\. For instructions, see [Deleting a DB instance](USER_DeleteInstance.md)\.

#### RDS API<a name="multi-az-db-clusters-migrating-to-create-promote-api"></a>

To migrate a Single\-AZ deployment or Multi\-AZ DB instance deployment to a Multi\-AZ DB cluster using a read replica, complete the following steps using the RDS API\.

1. Create the Multi\-AZ DB cluster read replica\.

   To create a Multi\-AZ DB cluster read replica, use the [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html) operation with the required parameter `DBClusterIdentifier`\. For `ReplicationSourceIdentifier`, specify the Amazon Resource Name \(ARN\) of the source DB instance\.

1. Stop any transactions from being written to the source DB instance, and then wait for all updates to be made to the read replica\.

   Database updates occur on the read replica after they have occurred on the primary DB instance\. This replication lag can vary significantly\. Use the `Replica Lag` metric to determine when all updates have been made to the read replica\. For more information about replica lag, see [Monitoring read replication](USER_ReadRepl.md#USER_ReadRepl.Monitoring)\.

1. When you are ready, promote read replica to be a standalone Multi\-AZ DB cluster\.

   To promote a Multi\-AZ DB cluster read replica, use the [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_PromoteReadReplicaDBCluster.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_PromoteReadReplicaDBCluster.html) operation with the required parameter `DBClusterIdentifier`\. Specify the identifier of the Multi\-AZ DB cluster read replica\.

1. Wait for the status of the promoted Multi\-AZ DB cluster to be `Available`\.

1. Direct your applications to use the promoted Multi\-AZ DB cluster\.

Optionally, delete the Single\-AZ deployment or Multi\-AZ DB instance deployment if it is no longer needed\. For instructions, see [Deleting a DB instance](USER_DeleteInstance.md)\.

### Limitations for creating a Multi\-AZ DB cluster read replica<a name="multi-az-db-clusters-migrating-to-limitations"></a>

The following limitations apply to creating a Multi\-AZ DB cluster read replica from a Single\-AZ deployment or Multi\-AZ DB instance deployment\.
+ You can't create a Multi\-AZ DB cluster read replica in an AWS account that is different from the AWS account that owns the source DB instance\.
+ You can't create a Multi\-AZ DB cluster read replica in a different AWS Region from the source DB instance\.
+ You can't recover a Multi\-AZ DB cluster read replica to a point in time\.
+ Storage encryption must have the same settings on the source DB instance and Multi\-AZ DB cluster\.
+ If the source DB instance is encrypted, the Multi\-AZ DB cluster read replica must be encrypted using the same KMS key\.
+ To perform a minor version upgrade on the source DB instance, you must first perform the minor version upgrade on the Multi\-AZ DB cluster read replica\.
+ You can't perform a major version upgrade on a Multi\-AZ DB cluster\.
+ You can perform a major version upgrade on the source DB instance of a Multi\-AZ DB cluster read replica, but replication to the read replica stops and can't be restarted\.
+ The Multi\-AZ DB cluster read replica doesn't support cascading read replicas\.
+ For RDS for PostgreSQL, Multi\-AZ DB cluster read replicas can't fail over\.

## Creating a DB instance read replica from a Multi\-AZ DB cluster<a name="multi-az-db-clusters-create-instance-read-replica"></a>

You can create a DB instance read replica from a Multi\-AZ DB cluster in order to scale beyond the compute or I/O capacity of the cluster for read\-heavy database workloads\. You can direct this excess read traffic to one or more DB instance read replicas\. You can also use read replicas to migrate from a Multi\-AZ DB cluster to a DB instance\.

To create a read replica, specify a Multi\-AZ DB cluster as the replication source\. One of the reader instances of the Multi\-AZ DB cluster is always the source of replication, not the writer instance\. This condition ensures that the replica is always in sync with the source cluster, even in cases of failover\.

**Topics**
+ [Comparing reader DB instances and DB instance read replicas](#multi-az-db-clusters-readerdb-vs-dbrr)
+ [Considerations](#multi-az-db-clusters-instance-read-replica-considerations)
+ [Creating a DB instance read replica](#multi-az-db-clusters-instance-read-replica-create)
+ [Promoting the DB instance read replica](#multi-az-db-clusters-promote-instance-read-replica)
+ [Limitations for creating a DB instance read replica from a Multi\-AZ DB cluster](#multi-az-db-clusters-create-instance-read-replica-limitations)

### Comparing reader DB instances and DB instance read replicas<a name="multi-az-db-clusters-readerdb-vs-dbrr"></a>

A *DB instance read replica* of a Multi\-AZ DB cluster is different than the *reader DB instances* of the Multi\-AZ DB cluster in the following ways:
+ The reader DB instances act as automatic failover targets, while DB instance read replicas do not\.
+ Reader DB instances must acknowledge a change from the writer DB instance before the change can be committed\. For DB instance read replicas, however, updates are asynchronously copied to the read replica without requiring acknowledgement\.
+ Reader DB instances always share the same instance class, storage type, and engine version as the writer DB instance of the Multi\-AZ DB cluster\. DB instance read replicas, however, don’t necessarily have to share the same configurations as the source cluster\.
+ You can promote a DB instance read replica to a standalone DB instance\. You can’t promote a reader DB instance of a Multi\-AZ DB cluster to a standalone instance\.
+ The reader endpoint only routes requests to the reader DB instances of the Multi\-AZ DB cluster\. It never routes requests to a DB instance read replica\.

For more information about reader and writer DB instances, see [Overview of Multi\-AZ DB clusters](multi-az-db-clusters-concepts.md#multi-az-db-clusters-concepts-overview)\.

### Considerations<a name="multi-az-db-clusters-instance-read-replica-considerations"></a>

Consider the following before you create a DB instance read replica from a Multi\-AZ DB cluster:
+ When you create the DB instance read replica, it must be on the same major version as its source cluster, and the same or higher minor version\. After you create it, you can optionally upgrade the read replica to a higher minor version than the source cluster\.
+ When you create the DB instance read replica, the allocated storage must be the same as the allocated storage of the source Multi\-AZ DB cluster\. You can change the allocated storage after the read replica is created\.
+ For RDS for MySQL, the `gtid-mode` parameter must be set to `ON` for the source Multi\-AZ DB cluster\. For more information, see [Working with DB cluster parameter groups for Multi\-AZ DB clusters](USER_WorkingWithDBClusterParamGroups.md)\.
+ An active, long\-running transaction can slow the process of creating the read replica\. We recommend that you wait for long\-running transactions to complete before creating a read replica\.
+ We strongly recommend that you create all read replicas in the same virtual private cloud \(VPC\) based on Amazon VPC of the source Multi\-AZ DB cluster\.

  If you create a read replica in a different VPC from the source Multi\-AZ DB cluster, Classless Inter\-Domain Routing \(CIDR\) ranges can overlap between the replica and the RDS system\. CIDR overlap makes the replica unstable, which can negatively impact applications connecting to it\. If you receive an error when creating the read replica, choose a different destination DB subnet group\. For more information, see [Working with a DB instance in a VPC](USER_VPC.WorkingWithRDSInstanceinaVPC.md)\.
+ If you delete the source Multi\-AZ DB cluster for a DB instance read replica, any read replicas that it's writing to are promoted to standalone DB instances\.

### Creating a DB instance read replica<a name="multi-az-db-clusters-instance-read-replica-create"></a>

You can create a DB instance read replica from a Multi\-AZ DB cluster using the AWS Management Console, AWS CLI, or RDS API\.

#### Console<a name="multi-az-db-clusters-create-instance-read-replica-console"></a>

To create a DB instance read replica from a Multi\-AZ DB cluster, complete the following steps using the AWS Management Console\.

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the Multi\-AZ DB cluster that you want to use as the source for a read replica\.

1. For **Actions**, choose **Create read replica**\.

1. For **Replica source**, make sure that the correct Multi\-AZ DB cluster is selected\.

1. For **DB identifier**, enter a name for the read replica\.

1. For the remaining sections, specify your DB instance settings\. For information about a setting, see [Settings for DB instances](USER_CreateDBInstance.md#USER_CreateDBInstance.Settings)\.
**Note**  
The allocated storage for the DB instance read replica must be the same as the allocated storage for the source Multi\-AZ DB cluster\.

1. Choose **Create read replica**\.

#### AWS CLI<a name="multi-az-db-clusters-create-instance-read-replica-cli"></a>

To create a DB instance read replica from a Multi\-AZ DB cluster, use the AWS CLI command [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance-read-replica.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance-read-replica.html)\. For `--source-db-cluster-identifier`, specify the identifier of the Multi\-AZ DB cluster\.

For Linux, macOS, or Unix:

```
aws rds create-db-instance-read-replica \
--db-instance-identifier myreadreplica \
--source-db-cluster-identifier mymultiazdbcluster
```

For Windows:

```
aws rds create-db-instance-read-replica ^
--db-instance-identifier myreadreplica ^
--source-db-cluster-identifier mymultiazdbcluster
```

#### RDS API<a name="multi-az-db-clusters-create-instance-read-replica-api"></a>

To create a DB instance read replica from a Multi\-AZ DB cluster, use the [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstanceReadReplica.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstanceReadReplica.html) operation\.

### Promoting the DB instance read replica<a name="multi-az-db-clusters-promote-instance-read-replica"></a>

If you no longer need the DB instance read replica, you can promote it into a standalone DB instance\. When you promote a read replica, the DB instance is rebooted before it becomes available\. For instructions, see [Promoting a read replica to be a standalone DB instance](USER_ReadRepl.md#USER_ReadRepl.Promote)\.

If you're using the read replica to migrate a Multi\-AZ DB cluster deployment to a Single\-AZ or Multi\-AZ DB instance deployment, make sure to stop any transactions that are being written to the source DB cluster\. Then, wait for all updates to be made to the read replica\. Database updates occur on the read replica after they occur on one of the reader DB instances of the Multi\-AZ DB cluster\. This replication lag can vary significantly\. Use the `ReplicaLag` metric to determine when all updates have been made to the read replica\. For more information about replica lag, see [Monitoring read replication](USER_ReadRepl.md#USER_ReadRepl.Monitoring)\.

After you promote the read replica, wait for the status of the promoted DB instance to be `Available` before you direct your applications to use the promoted DB instance\. Optionally, delete the Multi\-AZ DB cluster deployment if you no longer need it\. For instructions, see [Deleting a Multi\-AZ DB cluster](USER_DeleteMultiAZDBCluster.Deleting.md)\.

### Limitations for creating a DB instance read replica from a Multi\-AZ DB cluster<a name="multi-az-db-clusters-create-instance-read-replica-limitations"></a>

The following limitations apply to creating a DB instance read replica from a Multi\-AZ DB cluster deployment\.
+ You can't create a DB instance read replica in an AWS account that's different from the AWS account that owns the source Multi\-AZ DB cluster\.
+ You can't create a DB instance read replica in a different AWS Region from the source Multi\-AZ DB cluster\.
+ You can't recover a DB instance read replica to a point in time\.
+ Storage encryption must have the same settings on the source Multi\-AZ DB cluster and DB instance read replica\.
+ If the source Multi\-AZ DB cluster is encrypted, the DB instance read replica must be encrypted using the same KMS key\.
+ To perform a minor version upgrade on the source Multi\-AZ DB cluster, you must first perform the minor version upgrade on the DB instance read replica\.
+ The DB instance read replica doesn't support cascading read replicas\.
+ For RDS for PostgreSQL, the source Multi\-AZ DB cluster must be running version 15\.2\-R2 or higher in order to create a DB instance read replica\. 
+ You can perform a major version upgrade on the source Multi\-AZ DB cluster of a DB instance read replica, but replication to the read replica stops and can't be restarted\. 