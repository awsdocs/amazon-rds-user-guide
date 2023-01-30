# Multi\-AZ DB cluster deployments<a name="multi-az-db-clusters-concepts"></a>

A *Multi\-AZ DB cluster deployment* is a high availability deployment mode of Amazon RDS with two readable standby DB instances\. A Multi\-AZ DB cluster has a writer DB instance and two reader DB instances in three separate Availability Zones in the same AWS Region\. Multi\-AZ DB clusters provide high availability, increased capacity for read workloads, and lower write latency when compared to Multi\-AZ DB instance deployments\.

**Topics**
+ [Region and version availability](#multi-az-db-clusters-concepts.RegionVersionAvailability)
+ [Overview of Multi\-AZ DB clusters](#multi-az-db-clusters-concepts-overview)
+ [Creating and managing a Multi\-AZ DB cluster](#multi-az-db-clusters-creating-managing)
+ [Managing connections for Multi\-AZ DB clusters](#multi-az-db-clusters-concepts-connection-management)
+ [Managing a Multi\-AZ DB cluster with the AWS Management Console](#multi-az-db-clusters-concepts-managing-console)
+ [Working with parameter groups for Multi\-AZ DB clusters](#multi-az-db-clusters-concepts-parameter-groups)
+ [Replica lag and Multi\-AZ DB clusters](#multi-az-db-clusters-concepts-replica-lag)
+ [Migrating to a Multi\-AZ DB cluster using a read replica](#multi-az-db-clusters-migrating-to-with-read-replica)
+ [Failover process for Multi\-AZ DB clusters](#multi-az-db-clusters-concepts-failover)
+ [Limitations for Multi\-AZ DB clusters](#multi-az-db-clusters-concepts.Limitations)

**Important**  
Multi\-AZ DB clusters aren't the same as Aurora DB clusters\. For information about Aurora DB clusters, see the [Amazon Aurora User Guide](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/CHAP_AuroraOverview.html)\.

## Region and version availability<a name="multi-az-db-clusters-concepts.RegionVersionAvailability"></a>

Feature availability and support varies across specific versions of each database engine, and across AWS Regions\. For more information on version and Region availability of Amazon RDS with Multi\-AZ DB clusters, see [Multi\-AZ DB clusters](Concepts.RDS_Fea_Regions_DB-eng.Feature.MultiAZDBClusters.md)\. 

## Overview of Multi\-AZ DB clusters<a name="multi-az-db-clusters-concepts-overview"></a>

With a Multi\-AZ DB cluster, Amazon RDS replicates data from the writer DB instance to both of the reader DB instances using the DB engine's native replication capabilities\. When a change is made on the writer DB instance, it's sent to each reader DB instance\. Acknowledgment from at least one reader DB instance is required for a change to be committed\.

Reader DB instances act as automatic failover targets and also serve read traffic to increase application read throughput\. If an outage occurs on your writer DB instance, RDS manages failover to one of the reader DB instances\. RDS does this based on which reader DB instance has the most recent change record\.

The following diagram shows a Multi\-AZ DB cluster\.

![\[Multi-AZ DB cluster\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/multi-az-db-cluster.png)

Multi\-AZ DB clusters typically have lower write latency when compared to Multi\-AZ DB instance deployments\. They also allow read\-only workloads to run on reader DB instances\. The RDS console shows the Availability Zone of the writer DB instance and the Availability Zones of the reader DB instances\. You can also use the [describe\-db\-clusters](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-clusters.html) CLI command or the [DescribeDBClusters](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBClusters.html) API operation to find this information\. 

To prevent replication errors in RDS for MySQL Multi\-AZ DB clusters, we strongly recommend that all tables have a primary key\.

## Creating and managing a Multi\-AZ DB cluster<a name="multi-az-db-clusters-creating-managing"></a>

You can create a Multi\-AZ DB cluster directly or by restoring from a snapshot\. For instructions, see these topics:
+ [Creating a Multi\-AZ DB cluster](create-multi-az-db-cluster.md)
+ [Restoring from a snapshot to a Multi\-AZ DB cluster](USER_RestoreFromMultiAZDBClusterSnapshot.Restoring.md)

You can modify, reboot, or delete a Multi\-AZ DB by following the instructions in these topics:
+ [Modifying a Multi\-AZ DB cluster](modify-multi-az-db-cluster.md)
+ [Rebooting Multi\-AZ DB clusters and reader DB instances](multi-az-db-clusters-concepts-rebooting.md)
+ [Deleting a Multi\-AZ DB cluster](USER_DeleteMultiAZDBCluster.Deleting.md)

You can create a snapshot of a Multi\-AZ DB cluster by following the instructions in [Creating a Multi\-AZ DB cluster snapshot](USER_CreateMultiAZDBClusterSnapshot.md)\.

You can restore a Multi\-AZ DB cluster to a point in time by following the instructions in [Restoring a Multi\-AZ DB cluster to a specified time](USER_PIT.MultiAZDBCluster.md)\.

You can import data from an on\-premises database to a Multi\-AZ DB cluster by following the instructions in [Importing data to an Amazon RDS MariaDB or MySQL database with reduced downtime](MySQL.Procedural.Importing.NonRDSRepl.md)\.

## Managing connections for Multi\-AZ DB clusters<a name="multi-az-db-clusters-concepts-connection-management"></a>

 A Multi\-AZ DB cluster has three DB instances instead of a single DB instance\. Each connection is handled by a specific DB instance\. When you connect to a Multi\-AZ DB cluster, the host name and port that you specify point to a fully qualified domain name called an *endpoint*\. The Multi\-AZ DB cluster uses the endpoint mechanism to abstract these connections\. Thus, you don't have to hardcode all the host names or write your own logic for rerouting connections when some DB instances aren't available\. 

The writer endpoint connects to the writer DB instance of the DB cluster, which supports both read and write operations\. The reader endpoint connects to either of the two reader DB instances, which support only read operations\.

 Using endpoints, you can map each connection to the appropriate DB instance or group of DB instances based on your use case\. For example, to perform DDL and DML statements, you can connect to whichever DB instance is the writer DB instance\. To perform queries, you can connect to the reader endpoint, with the Multi\-AZ DB cluster automatically managing connections among the reader DB instances\. For diagnosis or tuning, you can connect to a specific DB instance endpoint to examine details about a specific DB instance\.

For information about connecting to a DB instance, see [Connecting to an Amazon RDS DB instance](CHAP_CommonTasks.Connect.md)\.

**Topics**
+ [Types of Multi\-AZ DB cluster endpoints](#multi-az-db-clusters-concepts-connection-management-endpoint-types)
+ [Viewing the endpoints for a Multi\-AZ DB cluster](#multi-az-db-clusters-concepts-connection-management-viewing)
+ [Using the cluster endpoint](#multi-az-db-clusters-concepts-connection-management-endpoints-cluster)
+ [Using the reader endpoint](#multi-az-db-clusters-concepts-connection-management-endpoints-reader)
+ [Using the instance endpoints](#multi-az-db-clusters-concepts-connection-management-endpoints-instance)
+ [How Multi\-AZ DB endpoints work with high availability](#multi-az-db-clusters-concepts-connection-management-endpoints-ha)

### Types of Multi\-AZ DB cluster endpoints<a name="multi-az-db-clusters-concepts-connection-management-endpoint-types"></a>

 An endpoint is represented by a unique identifier that contains a host address\. The following types of endpoints are available from a Multi\-AZ DB cluster: 

**Cluster endpoint**  
 A *cluster endpoint* \(or *writer endpoint*\) for a Multi\-AZ DB cluster connects to the current writer DB instance for that DB cluster\. This endpoint is the only one that can perform write operations such as DDL and DML statements\. This endpoint can also perform read operations\.   
 Each Multi\-AZ DB cluster has one cluster endpoint and one writer DB instance\.   
 You use the cluster endpoint for all write operations on the DB cluster, including inserts, updates, deletes, and DDL changes\. You can also use the cluster endpoint for read operations, such as queries\.   
 If the current writer DB instance of a DB cluster fails, the Multi\-AZ DB cluster automatically fails over to a new writer DB instance\. During a failover, the DB cluster continues to serve connection requests to the cluster endpoint from the new writer DB instance, with minimal interruption of service\.   
 The following example illustrates a cluster endpoint for a Multi\-AZ DB cluster\.   
 `mydbcluster.cluster-123456789012.us-east-1.rds.amazonaws.com` 

**Reader endpoint**  
 A *reader endpoint* for a Multi\-AZ DB cluster provides support for read\-only connections to the DB cluster\. Use the reader endpoint for read operations, such as `SELECT` queries\. By processing those statements on the reader DB instances, this endpoint reduces the overhead on the writer DB instance\. It also helps the cluster to scale the capacity to handle simultaneous `SELECT` queries\. Each Multi\-AZ DB cluster has one reader endpoint\.   
 The reader endpoint sends each connection request to one of the reader DB instances\. When you use the reader endpoint for a session, you can only perform read\-only statements such as `SELECT` in that session\.   
 The following example illustrates a reader endpoint for a Multi\-AZ DB cluster\. The read\-only intent of a reader endpoint is denoted by the `-ro` within the cluster endpoint name\.   
 `mydbcluster.cluster-ro-123456789012.us-east-1.rds.amazonaws.com` 

**Instance endpoint**  
 An *instance endpoint* connects to a specific DB instance within a Multi\-AZ DB cluster\. Each DB instance in a DB cluster has its own unique instance endpoint\. So there is one instance endpoint for the current writer DB instance of the DB cluster, and there is one instance endpoint for each of the reader DB instances in the DB cluster\.   
 The instance endpoint provides direct control over connections to the DB cluster\. This control can help you address scenarios where using the cluster endpoint or reader endpoint might not be appropriate\. For example, your client application might require more fine\-grained load balancing based on workload type\. In this case, you can configure multiple clients to connect to different reader DB instances in a DB cluster to distribute read workloads\.   
 The following example illustrates an instance endpoint for a DB instance in a Multi\-AZ DB cluster\.   
 `mydbinstance.123456789012.us-east-1.rds.amazonaws.com` 

### Viewing the endpoints for a Multi\-AZ DB cluster<a name="multi-az-db-clusters-concepts-connection-management-viewing"></a>

 In the AWS Management Console, you see the cluster endpoint and the reader endpoint on the details page for each Multi\-AZ DB cluster\. You see the instance endpoint in the details page for each DB instance\. 

 With the AWS CLI, you see the writer and reader endpoints in the output of the [describe\-db\-clusters](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-clusters.html) command\. For example, the following command shows the endpoint attributes for all clusters in your current AWS Region\. 

```
aws rds describe-db-cluster-endpoints
```

 With the Amazon RDS API, you retrieve the endpoints by calling the [DescribeDBClusterEndpoints](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBClusterEndpoints.html) action\. The output also shows Amazon Aurora DB cluster endpoints, if any exist\.

### Using the cluster endpoint<a name="multi-az-db-clusters-concepts-connection-management-endpoints-cluster"></a>

Each Multi\-AZ DB cluster has a single built\-in cluster endpoint, whose name and other attributes are managed by Amazon RDS\. You can't create, delete, or modify this kind of endpoint\. 

 You use the cluster endpoint when you administer your DB cluster, perform extract, transform, load \(ETL\) operations, or develop and test applications\. The cluster endpoint connects to the writer DB instance of the cluster\. The writer DB instance is the only DB instance where you can create tables and indexes, run `INSERT` statements, and perform other DDL and DML operations\. 

 The physical IP address pointed to by the cluster endpoint changes when the failover mechanism promotes a new DB instance to be the writer DB instance for the cluster\. If you use any form of connection pooling or other multiplexing, be prepared to flush or reduce the time\-to\-live for any cached DNS information\. Doing so ensures that you don't try to establish a read/write connection to a DB instance that became unavailable or is now read\-only after a failover\. 

### Using the reader endpoint<a name="multi-az-db-clusters-concepts-connection-management-endpoints-reader"></a>

 You use the reader endpoint for read\-only connections to your Multi\-AZ DB cluster\. This endpoint helps your DB cluster handle a query\-intensive workload\. The reader endpoint is the endpoint that you supply to applications that do reporting or other read\-only operations on the cluster\. The reader endpoint sends connections to available reader DB instances in a Multi\-AZ DB cluster\. 

 Each Multi\-AZ cluster has a single built\-in reader endpoint, whose name and other attributes are managed by Amazon RDS\. You can't create, delete, or modify this kind of endpoint\. 

### Using the instance endpoints<a name="multi-az-db-clusters-concepts-connection-management-endpoints-instance"></a>

 Each DB instance in a Multi\-AZ DB cluster has its own built\-in instance endpoint, whose name and other attributes are managed by Amazon RDS\. You can't create, delete, or modify this kind of endpoint\. With a Multi\-AZ DB cluster, you typically use the writer and reader endpoints more often than the instance endpoints\. 

In day\-to\-day operations, the main way that you use instance endpoints is to diagnose capacity or performance issues that affect one specific DB instance in a Multi\-AZ DB cluster\. While connected to a specific DB instance, you can examine its status variables, metrics, and so on\. Doing this can help you determine what's happening for that DB instance that's different from what's happening for other DB instances in the cluster\. 

### How Multi\-AZ DB endpoints work with high availability<a name="multi-az-db-clusters-concepts-connection-management-endpoints-ha"></a>

For Multi\-AZ DB clusters where high availability is important, use the writer endpoint for read/write or general\-purpose connections and the reader endpoint for read\-only connections\. The writer and reader endpoints manage DB instance failover better than instance endpoints do\. Unlike the instance endpoints, the writer and reader endpoints automatically change which DB instance they connect to if a DB instance in your cluster becomes unavailable\. 

 If the writer DB instance of a DB cluster fails, Amazon RDS automatically fails over to a new writer DB instance\. It does so by promoting a reader DB instance to a new writer DB instance\. If a failover occurs, you can use the writer endpoint to reconnect to the newly promoted writer DB instance\. Or you can use the reader endpoint to reconnect to one of the reader DB instances in the DB cluster\. During a failover, the reader endpoint might direct connections to the new writer DB instance of a DB cluster for a short time after a reader DB instance is promoted to the new writer DB instance\. If you design your own application logic to manage instance endpoint connections, you can manually or programmatically discover the resulting set of available DB instances in the DB cluster\. 

## Managing a Multi\-AZ DB cluster with the AWS Management Console<a name="multi-az-db-clusters-concepts-managing-console"></a>

You can manage a Multi\-AZ DB cluster with the console\.

**To manage a Multi\-AZ DB cluster with the console**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then choose the Multi\-AZ DB cluster that you want to manage\.

The following image shows a Multi\-AZ DB cluster in the console\.

![\[Multi-AZ DB cluster in the AWS Management Console\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/multi-az-db-cluster-view-console-postgresql.png)

The available actions in the **Actions** menu depend on whether the Multi\-AZ DB cluster is selected or a DB instance in the cluster is selected\.

Choose the Multi\-AZ DB cluster to view the cluster details and perform actions at the cluster level\.

![\[Multi-AZ DB cluster actions in the AWS Management Console\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/multi-az-db-cluster-actions-console-postgresql.png)

Choose a DB instance in a Multi\-AZ DB cluster to view the DB instance details and perform actions at the DB instance level\.

![\[DB instance actions in a Multi-AZ DB cluster in the AWS Management Console\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/multi-az-db-cluster-instance-actions-console-postgresql.png)

## Working with parameter groups for Multi\-AZ DB clusters<a name="multi-az-db-clusters-concepts-parameter-groups"></a>

In a Multi\-AZ DB cluster, a *DB cluster parameter group* acts as a container for engine configuration values that are applied to every DB instance in the Multi\-AZ DB cluster\.

In a Multi\-AZ DB cluster, a *DB parameter group* is set to the default DB parameter group for the DB engine and DB engine version\. The settings in the DB cluster parameter group are used for all of the DB instances in the cluster\.

For information about parameter groups, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\.

## Replica lag and Multi\-AZ DB clusters<a name="multi-az-db-clusters-concepts-replica-lag"></a>

*Replica lag* is the difference in time between the latest transaction on the writer DB instance and the latest applied transaction on a reader DB instance\. The Amazon CloudWatch metric `ReplicaLag` represents this time difference\. For more information about CloudWatch metrics, see [Monitoring Amazon RDS metrics with Amazon CloudWatch](monitoring-cloudwatch.md)\.

Although Multi\-AZ DB clusters allow for high write performance, replica lag can still occur due to the nature of engine\-based replication\. Because any failover must first resolve the replica lag before it promotes a new writer DB instance, monitoring and managing this replica lag is a consideration\.

For RDS for MySQL Multi\-AZ DB clusters, failover time depends on replica lag of both remaining reader DB instances\. Both the reader DB instances must apply unapplied transactions before one of them is promoted to the new writer DB instance\. 

For RDS for PostgreSQL Multi\-AZ DB clusters, failover time depends on the lowest replica lag of the two remaining reader DB instances\. The reader DB instance with the lowest replica lag must apply unapplied transactions before it is promoted to the new writer DB instance\.

For a tutorial that shows you how to create a CloudWatch alarm when replica lag exceeds a set amount of time, see [Tutorial: Creating an Amazon CloudWatch alarm for Multi\-AZ DB cluster replica lag](multi-az-db-cluster-cloudwatch-alarm.md)\.

### Common causes of replica lag<a name="multi-az-db-clusters-concepts-replica-lag-causes"></a>

In general, replica lag occurs when the write workload is too high for the reader DB instances to apply the transactions efficiently\. Various workloads can incur temporary or continuous replica lag\. Some examples of common causes are the following:
+ High write concurrency or heavy batch updating on the writer DB instance, causing the apply process on the reader DB instances to fall behind\.
+ Heavy read workload that is using resources on one or more reader DB instances\. Running slow or large queries can affect the apply process and can cause replica lag\.
+ Transactions that modify large amounts of data or DDL statements can sometimes cause a temporary increase in replica lag because the database must preserve commit order\.

### Mitigating replica lag<a name="multi-az-db-clusters-concepts-replica-lag-mitigating"></a>

For Multi\-AZ DB clusters for RDS for MySQL and RDS for PostgreSQL, you can mitigate replica lag by reducing the load on your writer DB instance\. You can also use flow control to reduce replica lag\. *Flow control* works by throttling writes on the writer DB instance, which ensures that replica lag doesn't continue to grow unbounded\. Write throttling is accomplished by adding a delay into the end of a transaction, which decreases the write throughput on the writer DB instance\. Although flow control doesn't guarantee lag elimination, it can help reduce overall lag in many workloads\. The following sections provide information about using flow control with RDS for MySQL and RDS for PostgreSQL\.

#### Mitigating replica lag with flow control for RDS for MySQL<a name="multi-az-db-clusters-concepts-replica-lag-mitigating.mysql"></a>

When you are using RDS for MySQL Multi\-AZ DB clusters, flow control is turned on by default using the dynamic parameter `rpl_semi_sync_master_target_apply_lag`\. This parameter specifies the upper limit that you want for replica lag\. As replica lag approaches this configured limit, flow control throttles the write transactions on the writer DB Instance to try to contain the replica lag below the specified value\. In some cases, replica lag can exceed the specified limit\. By default, this parameter is set to 120 seconds\. To turn off flow control, set this parameter to its maximum value of 86400 seconds \(one day\)\.

To view the current delay injected by flow control, show the parameter `Rpl_semi_sync_master_flow_control_current_delay` by running the following query\.

```
SHOW GLOBAL STATUS like '%flow_control%';
```

Your output looks similar to the following\.

```
+-------------------------------------------------+-------+
| Variable_name                                   | Value |
+-------------------------------------------------+-------+
| Rpl_semi_sync_master_flow_control_current_delay | 2010  |
+-------------------------------------------------+-------+
1 row in set (0.00 sec)
```

**Note**  
The delay is shown in microseconds\.

When you have Performance Insights turned on for an RDS for MySQL Multi\-AZ DB cluster, you can monitor the wait event corresponding to a SQL statement indicating that the queries were delayed by a flow control\. When a delay was introduced by a flow control, you can view the wait event `/wait/synch/cond/semisync/semi_sync_flow_control_delay_cond` corresponding to the SQL statement on the Performance Insights dashboard\. To view these metrics, make sure that the Performance Schema is turned on\. For information about Performance Insights, see [Monitoring DB load with Performance Insights on Amazon RDS](USER_PerfInsights.md)\.

#### Mitigating replica lag with flow control for RDS for PostgreSQL<a name="multi-az-db-clusters-concepts-replica-lag-mitigating.postgresql"></a>

When you are using RDS for PostgreSQL Multi\-AZ DB clusters, flow control is deployed as an extension\. It turns on a background worker for all DB instances in the DB cluster\. By default, the background workers on the reader DB instances communicate the current replica lag with the background worker on the writer DB instance\. If the lag exceeds two minutes on any reader DB instance, the background worker on the writer DB instance adds a delay at the end of a transaction\. To control the lag threshold, use the parameter `flow_control.target_standby_apply_lag`\.

When a flow control throttles a PostgreSQL process, the `Extension` wait event in `pg_stat_activity` and Performance Insights indicates that\. The function `get_flow_control_stats` displays details about how much delay is currently being added\.

Flow control can benefit most online transaction processing \(OLTP\) workloads that have short but highly concurrent transactions\. If the lag is caused by long\-running transactions, such as batch operations, flow control doesn't provide as strong a benefit\.

You can turn off flow control by removing the extension from the `preload_shared_libraries` and rebooting your DB instance\.

## Migrating to a Multi\-AZ DB cluster using a read replica<a name="multi-az-db-clusters-migrating-to-with-read-replica"></a>

To migrate a Single\-AZ deployment or Multi\-AZ DB instance deployment to a Multi\-AZ DB cluster deployment with reduced downtime, you can create a Multi\-AZ DB cluster read replica\. For the source, you specify the DB instance in the Single\-AZ deployment or the primary DB instance in the Multi\-AZ DB instance deployment\. The DB instance can process write transactions during the migration to a Multi\-AZ DB cluster\.

The following are considerations before creating the Multi\-AZ DB cluster read replica:
+ The source DB instance must be on a version that supports Multi\-AZ DB clusters\. For more information, see [Multi\-AZ DB clusters](Concepts.RDS_Fea_Regions_DB-eng.Feature.MultiAZDBClusters.md)\.
+ The Multi\-AZ DB cluster read replica must be on the same major version as its source, and the same or higher minor version\.
+ You must turn on automatic backups on the source DB instance by setting the backup retention period to a value other than 0\.
+ The allocated storage of the source DB instance must be 100 GiB or higher\.
+ An active, long\-running transaction can slow the process of creating the read replica\. We recommend that you wait for long\-running transactions to complete before creating a read replica\.
+ Within an AWS Region, we strongly recommend that you create all read replicas in the same virtual private cloud \(VPC\) based on Amazon VPC of the source DB instance\.

  If you create a read replica in a different VPC from the source DB instance, classless inter\-domain routing \(CIDR\) ranges can overlap between the replica and the RDS system\. CIDR overlap makes the replica unstable, which can negatively impact applications connecting to it\. If you receive an error when creating the read replica, choose a different destination DB subnet group\. For more information, see [Working with a DB instance in a VPC](USER_VPC.WorkingWithRDSInstanceinaVPC.md)\.
+ If you delete the source DB instance for a Multi\-AZ DB cluster read replica, the read replica is promoted to a standalone Multi\-AZ DB cluster\.

**Topics**
+ [Creating and promoting the Multi\-AZ DB cluster read replica](#multi-az-db-clusters-migrating-to-create-promote)
+ [Limitations for creating a Multi\-AZ DB cluster read replica](#multi-az-db-clusters-migrating-to-limitations)

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

## Failover process for Multi\-AZ DB clusters<a name="multi-az-db-clusters-concepts-failover"></a>

If there is a planned or unplanned outage of your writer DB instance in a Multi\-AZ DB cluster, Amazon RDS automatically fails over to a reader DB instance in different Availability Zone\. The time it takes for the failover to complete depends on the database activity and other conditions when the writer DB instance became unavailable\. Failover times are typically under 35 seconds\. Failover completes when both reader DB instances have applied outstanding transactions from the failed writer\. When the failover is complete, it can take additional time for the RDS console to reflect the new Availability Zone\.

**Topics**
+ [Automatic failovers](#multi-az-db-clusters-concepts-failover-automatic)
+ [Manually failing over a Multi\-AZ DB cluster](#multi-az-db-clusters-concepts-failover-manual)
+ [Determining whether a Multi\-AZ DB cluster has failed over](#multi-az-db-clusters-concepts-failover-determining)
+ [Setting the JVM TTL for DNS name lookups](#multi-az-db-clusters-concepts-failover-java-dns)

### Automatic failovers<a name="multi-az-db-clusters-concepts-failover-automatic"></a>

Amazon RDS handles failovers automatically so you can resume database operations as quickly as possible without administrative intervention\. To fail over, the writer DB instance switches automatically to a reader DB instance\.

### Manually failing over a Multi\-AZ DB cluster<a name="multi-az-db-clusters-concepts-failover-manual"></a>

You can fail over a Multi\-AZ DB cluster manually using the AWS Management Console, the AWS CLI, or the RDS API\.

#### Console<a name="multi-az-db-clusters-concepts-failover-manual-con"></a>

**To fail over a Multi\-AZ DB cluster manually**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the Multi\-AZ DB cluster that you want to fail over\.

1. For **Actions**, choose **Failover**\.

   The **Failover DB Cluster** page appears\.

1. Choose **Failover** to confirm the manual failover\.

#### AWS CLI<a name="multi-az-db-clusters-concepts-failover-manual-cli"></a>

To fail over a Multi\-AZ DB cluster manually, use the AWS CLI command [ failover\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/failover-db-cluster.html)\.

**Example**  

```
1. aws rds failover-db-cluster --db-cluster-identifier mymultiazdbcluster
```

#### RDS API<a name="multi-az-db-clusters-concepts-failover-manual-api"></a>

To fail over a Multi\-AZ DB cluster manually, call the Amazon RDS API [FailoverDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_FailoverDBCluster.html) and specify the `DBClusterIdentifier`\.

### Determining whether a Multi\-AZ DB cluster has failed over<a name="multi-az-db-clusters-concepts-failover-determining"></a>

To determine if your Multi\-AZ DB cluster has failed over, you can do the following:
+ Set up DB event subscriptions to notify you by email or SMS that a failover has been initiated\. For more information about events, see [Working with Amazon RDS event notification](USER_Events.md)\.
+ View your DB events by using the Amazon RDS console or API operations\.
+ View the current state of your Multi\-AZ DB cluster by using the Amazon RDS console, the AWS CLI, and the RDS API\.

For information on how you can respond to failovers, reduce recovery time, and other best practices for Amazon RDS, see [Best practices for Amazon RDS](CHAP_BestPractices.md)\.

### Setting the JVM TTL for DNS name lookups<a name="multi-az-db-clusters-concepts-failover-java-dns"></a>

The failover mechanism automatically changes the Domain Name System \(DNS\) record of the DB instance to point to the reader DB instance\. As a result, you need to re\-establish any existing connections to your DB instance\. In a Java virtual machine \(JVM\) environment, due to how the Java DNS caching mechanism works, you might need to reconfigure JVM settings\.

The JVM caches DNS name lookups\. When the JVM resolves a host name to an IP address, it caches the IP address for a specified period of time, known as the *time\-to\-live* \(TTL\)\.

Because AWS resources use DNS name entries that occasionally change, we recommend that you configure your JVM with a TTL value of no more than 60 seconds\. Doing this makes sure that when a resource's IP address changes, your application can receive and use the resource's new IP address by requerying the DNS\.

On some Java configurations, the JVM default TTL is set so that it never refreshes DNS entries until the JVM is restarted\. Thus, if the IP address for an AWS resource changes while your application is still running, it can't use that resource until you manually restart the JVM and the cached IP information is refreshed\. In this case, it's crucial to set the JVM's TTL so that it periodically refreshes its cached IP information\.

**Note**  
The default TTL can vary according to the version of your JVM and whether a security manager is installed\. Many JVMs provide a default TTL less than 60 seconds\. If you're using such a JVM and not using a security manager, you can ignore the rest of this topic\. For more information on security managers in Oracle, see [The security manager](https://docs.oracle.com/javase/tutorial/essential/environment/security.html) in the Oracle documentation\.

To modify the JVM's TTL, set the [https://docs.oracle.com/javase/7/docs/technotes/guides/net/properties.html](https://docs.oracle.com/javase/7/docs/technotes/guides/net/properties.html) property value\. Use one of the following methods, depending on your needs:
+ To set the property value globally for all applications that use the JVM, set `networkaddress.cache.ttl` in the `$JAVA_HOME/jre/lib/security/java.security` file\.

  ```
  networkaddress.cache.ttl=60					
  ```
+ To set the property locally for your application only, set `networkaddress.cache.ttl` in your application's initialization code before any network connections are established\.

  ```
  java.security.Security.setProperty("networkaddress.cache.ttl" , "60");					
  ```

## Limitations for Multi\-AZ DB clusters<a name="multi-az-db-clusters-concepts.Limitations"></a>

The following limitations apply to Multi\-AZ DB clusters:
+ Multi\-AZ DB clusters only support Provisioned IOPS storage\.
+ You can't change a Single\-AZ DB instance deployment or Multi\-AZ DB instance deployment into a Multi\-AZ DB cluster\. As an alternative, you can restore a snapshot of a Single\-AZ DB instance deployment or Multi\-AZ DB instance deployment to a Multi\-AZ DB cluster\.
+ Multi\-AZ DB clusters don't support modifications at the DB instance level because all modifications are done at the DB cluster level\.
+ Multi\-AZ DB clusters don't support the following features:
  + Amazon RDS Proxy
  + Support for IPv6 connections \(dual\-stack mode\)
  + Exporting Multi\-AZ DB cluster snapshot data to an Amazon S3 bucket
  + IAM DB authentication
  + Kerberos authentication
  + Modifying the port

    As an alternative, you can restore a Multi\-AZ DB cluster to a point in time and specify a different port\.
  + Option groups
  + Creating a read replica with a Multi\-AZ DB cluster source
  + Reserved DB instances
  + Restoring a Multi\-AZ DB cluster snapshot from an Amazon S3 bucket
  + Storage autoscaling by setting the maximum allocated storage

    As an alternative, you can scale storage manually\.
  + Stopping and starting the DB cluster
  + Copying a snapshot of a Multi\-AZ DB cluster
  + Encrypting an unencrypted Multi\-AZ DB cluster
+ RDS for MySQL Multi\-AZ DB clusters don't support replication to an external target database\.
+ RDS for MySQL Multi\-AZ DB clusters support only the following system stored procedures:
  + `mysql.rds_rotate_general_log`
  + `mysql.rds_rotate_slow_log`
  + `mysql.rds_show_configuration`

  RDS for MySQL Multi\-AZ DB clusters don't support other system stored procedures\. For information about these procedures, see [MySQL on Amazon RDS SQL reference](Appendix.MySQL.SQLRef.md)\.
+ RDS for PostgreSQL Multi\-AZ DB clusters don't support the following PostgreSQL extensions: `aws_s3`, `pg_transport`, and `pglogical`\.
+ RDS for PostgreSQL Multi\-AZ DB clusters don't support using a custom DNS server for outbound network access\.
+ RDS for PostgreSQL Multi\-AZ DB clusters don't support logical replication\.