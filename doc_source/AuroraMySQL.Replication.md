# Replication with Amazon Aurora MySQL<a name="AuroraMySQL.Replication"></a>

## Using Aurora Replicas<a name="AuroraMySQL.Replication.Replicas"></a>

Aurora Replicas are independent endpoints in an Aurora DB cluster, best used for scaling read operations and increasing availability\. Up to 15 Aurora Replicas can be distributed across the Availability Zones that a DB cluster spans within an AWS Region\. Although the DB cluster volume is made up of multiple copies of the data for the DB cluster, the data in the cluster volume is represented as a single, logical volume to the primary instance and to Aurora Replicas in the DB cluster\. For more information about Aurora Replicas, see [Aurora Replicas](Aurora.Replication.md#Aurora.Replication.Replicas)\.

Aurora Replicas work well for read scaling because they are fully dedicated to read operations on your cluster volume\. Write operations are managed by the primary instance\. Because the cluster volume is shared among all instances in your Aurora MySQL DB cluster, no additional work is required to replicate a copy of the data for each Aurora Replica\. In contrast, MySQL Read Replicas must replay, on a single thread, all write operations from the master DB instance to their local data store\. This limitation can affect the ability of MySQL Read Replicas to support large volumes of read traffic\.

**Important**  
Aurora Replicas for Aurora MySQL always use the `REPEATABLE READ` default transaction isolation level for operations on InnoDB tables\. You can use the `SET TRANSACTION ISOLATION LEVEL` command to change the transaction level only for the primary instance of an Aurora MySQL DB cluster\. This restriction avoids user\-level locks on Aurora Replicas, and allows Aurora Replicas to scale to support thousands of active user connections while still keeping replica lag to a minimum\.

## Replication Options for Amazon Aurora MySQL<a name="AuroraMySQL.Replication.Options"></a>

You can set up replication between any of the following options:

+ Two Aurora MySQL DB clusters in different AWS regions, by creating an Aurora Read Replica of an Aurora MySQL DB cluster in a different AWS Region\.

  For more information, see [Replicating Amazon Aurora MySQL DB Clusters Across AWS Regions](AuroraMySQL.Replication.CrossRegion.md)\.

+ Two Aurora MySQL DB clusters in the same region, by using MySQL binary log \(binlog\) replication\.

  For more information, see [Replication Between Aurora and MySQL or Between Aurora and Another Aurora DB Cluster](AuroraMySQL.Replication.MySQL.md)\.

+ An Amazon RDS MySQL DB instance as the master and an Aurora MySQL DB cluster, by creating an Aurora Read Replica of an Amazon RDS MySQL DB instance\.

   Typically, this approach is used for migration to Aurora MySQL, rather than for ongoing replication\. For more information, see [Migrating Data from a MySQL DB Instance to an Amazon Aurora MySQL DB Cluster by Using a DB Snapshot](AuroraMySQL.Migrating.RDSMySQL.md)\.

**Note**  
Rebooting the primary instance of an Amazon Aurora DB cluster also automatically reboots the Aurora Replicas for that DB cluster, in order to re\-establish an entry point that guarantees read/write consistency across the DB cluster\.

## Monitoring Amazon Aurora MySQL Replication<a name="AuroraMySQL.Replication.Monitoring"></a>

Read scaling and high availability depend on minimal lag time\. You can monitor how far an Aurora Replica is lagging behind the primary instance of your Aurora MySQL DB cluster by monitoring the Amazon CloudWatch `ReplicaLag` metric\. Because Aurora Replicas read from the same cluster volume as the primary instance, the `ReplicaLag` metric has a different meaning for an Aurora MySQL DB cluster\. The `ReplicaLag` metric for an Aurora Replica indicates the lag for the page cache of the Aurora Replica compared to that of the primary instance\.

If you need the most current value for Aurora Replica lag, you can query the `mysql.ro_replica_status` table in your Aurora MySQL DB cluster and check the value in the `Replica_lag_in_msec` column\. This column value is provided to Amazon CloudWatch as the value for the `ReplicaLag` metric\. The values in the `mysql.ro_replica_status` are also provided in the `INFORMATION_SCHEMA.REPLICA_HOST_STATUS` table in your Aurora MySQL DB cluster\.

For more information on monitoring RDS instances and CloudWatch metrics, see [Monitoring Amazon RDS](CHAP_Monitoring.md)\.