# Working with Multi\-AZ DB cluster read replicas<a name="USER_MultiAZDBCluster_ReadRepl"></a>

A DB cluster read replica is a special type of cluster that you create from a source DB instance\. After you create a read replica, any updates made to the primary DB instance are asynchronously copied to the DB cluster read replica\. You can reduce the load on your primary DB instance by routing read queries from your applications to the read replica\. Using read replicas, you can elastically scale out beyond the capacity constraints of a single DB instance for read\-heavy database workloads\.

## Migrating to a Multi\-AZ DB cluster using a read replica<a name="multi-az-db-clusters-migrating-to-with-read-replica"></a>

To migrate a Single\-AZ deployment or Multi\-AZ DB instance deployment to a Multi\-AZ DB cluster deployment with reduced downtime, you can create a Multi\-AZ DB cluster read replica\. For the source, you specify the DB instance in the Single\-AZ deployment or the primary DB instance in the Multi\-AZ DB instance deployment\. The DB instance can process write transactions during the migration to a Multi\-AZ DB cluster\.

Consider the following before you create a Multi\-AZ DB cluster read replica:
+ The source DB instance must be on a version that supports Multi\-AZ DB clusters\. For more information, see [Multi\-AZ DB clusters](Concepts.RDS_Fea_Regions_DB-eng.Feature.MultiAZDBClusters.md)\.
+ The Multi\-AZ DB cluster read replica must be on the same major version as its source, and the same or higher minor version\.
+ You must turn on automatic backups on the source DB instance by setting the backup retention period to a value other than 0\.
+ The allocated storage of the source DB instance must be 100 GiB or higher\.
+ An active, long\-running transaction can slow the process of creating the read replica\. We recommend that you wait for long\-running transactions to complete before creating a read replica\.
+ Within an AWS Region, we strongly recommend that you create all read replicas in the same virtual private cloud \(VPC\) based on Amazon VPC of the source DB instance\.

  If you create a read replica in a different VPC from the source DB instance, classless inter\-domain routing \(CIDR\) ranges can overlap between the replica and the RDS system\. CIDR overlap makes the replica unstable, which can negatively impact applications connecting to it\. If you receive an error when creating the read replica, choose a different destination DB subnet group\. For more information, see [Working with a DB instance in a VPC](USER_VPC.WorkingWithRDSInstanceinaVPC.md)\.
+ If you delete the source DB instance for a Multi\-AZ DB cluster read replica, the read replica is promoted to a standalone Multi\-AZ DB cluster\.

### Creating and promoting the Multi\-AZ DB cluster read replica<a name="multi-az-db-clusters-migrating-to-create-promote"></a>

You can create and promote a Multi\-AZ DB cluster read replica using the AWS Management Console, AWS CLI, or RDS API\.

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
+ You can only create a Multi\-AZ DB cluster read replica with an RDS for PostgreSQL DB instance as the source\. You can't create a Multi\-AZ DB cluster read replica with an RDS for MySQL DB instance as the source\.
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