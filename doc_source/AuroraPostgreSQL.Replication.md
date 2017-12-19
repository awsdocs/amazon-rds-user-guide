# Replication with Amazon Aurora PostgreSQL<a name="AuroraPostgreSQL.Replication"></a>

## Using Aurora Replicas<a name="AuroraPostgreSQL.Replication.Replicas"></a>

Aurora Replicas are independent endpoints in an Aurora DB cluster, best used for scaling read operations and increasing availability\. Up to 15 Aurora Replicas can be distributed across the Availability Zones that a DB cluster spans within an AWS Region\. Although the DB cluster volume is made up of multiple copies of the data for the DB cluster, the data in the cluster volume is represented as a single, logical volume to the primary instance and to Aurora Replicas in the DB cluster\. For more information about Aurora Replicas, see [Aurora Replicas](Aurora.Replication.md#Aurora.Replication.Replicas)\.

Aurora Replicas work well for read scaling because they are fully dedicated to read operations on your cluster volume\. Write operations are managed by the primary instance\. Because the cluster volume is shared among all instances in your Aurora PostgreSQL DB cluster, no additional work is required to replicate a copy of the data for each Aurora Replica\. In contrast, PostgreSQL Read Replicas must apply, on a single thread, all write operations from the master DB instance to their local data store\. This limitation can affect the ability of PostgreSQL Read Replicas to support large volumes of read traffic\.

## Replication Options for Amazon Aurora PostgreSQL<a name="AuroraPostgreSQL.Replication.Options"></a>

**Note**  
Rebooting the primary instance of an Amazon Aurora DB cluster also automatically reboots the Aurora Replicas for that DB cluster, in order to re\-establish an entry point that guarantees read/write consistency across the DB cluster\.

## Monitoring Amazon Aurora PostgreSQL Replication<a name="AuroraPostgreSQL.Replication.Monitoring"></a>

Read scaling and high availability depend on minimal lag time\. You can monitor how far an Aurora Replica is lagging behind the primary instance of your Aurora PostgreSQL DB cluster by monitoring the Amazon CloudWatch `ReplicaLag` metric\. Because Aurora Replicas read from the same cluster volume as the primary instance, the `ReplicaLag` metric has a different meaning for an Aurora PostgreSQL DB cluster\. The `ReplicaLag` metric for an Aurora Replica indicates the lag for the page cache of the Aurora Replica compared to that of the primary instance\.

For more information on monitoring RDS instances and CloudWatch metrics, see [Monitoring Amazon RDS](CHAP_Monitoring.md)\.