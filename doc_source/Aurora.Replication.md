# Replication with Amazon Aurora<a name="Aurora.Replication"></a>

## Aurora Replicas<a name="Aurora.Replication.Replicas"></a>

Aurora Replicas are independent endpoints in an Aurora DB cluster, best used for scaling read operations and increasing availability\. Up to 15 Aurora Replicas can be distributed across the Availability Zones that a DB cluster spans within an AWS Region\. The DB cluster volume is made up of multiple copies of the data for the DB cluster\. However, the data in the cluster volume is represented as a single, logical volume to the primary instance and to Aurora Replicas in the DB cluster\.

As a result, all Aurora Replicas return the same data for query results with minimal replica lag—usually much less than 100 milliseconds after the primary instance has written an update\. Replica lag varies depending on the rate of database change\. That is, during periods where a large amount of write operations occur for the database, you might see an increase in replica lag\.

Aurora Replicas work well for read scaling because they are fully dedicated to read operations on your cluster volume\. Write operations are managed by the primary instance\. Because the cluster volume is shared among all DB instances in your DB cluster, no additional work is required to replicate a copy of the data for each Aurora Replica\.

To increase availability, you can use Aurora Replicas as failover targets\. That is, if the primary instance fails, an Aurora Replica is promoted to the primary instance\. There is a brief interruption during which read and write requests made to the primary instance fail with an exception\. If your Aurora DB cluster doesn't include any Aurora Replicas, then the primary instance is recreated during a failure event\. However, promoting an Aurora Replica is much faster than recreating the primary instance\. For high\-availability scenarios, we recommend that you create one or more Aurora Replicas\. These should be of the same DB instance class as the primary instance and in different Availability Zones for your Aurora DB cluster\. For more information on Aurora Replicas as failover targets, see [Fault Tolerance for an Aurora DB Cluster](Aurora.Managing.md#Aurora.Managing.FaultTolerance)\.

For details on how to create an Aurora Replica, see [Creating an Aurora Replica Using the Console](Aurora.CreateInstance.md#Aurora.CreateInstance.Console.ReadOnlyInstance)\.

## Replication with Aurora MySQL<a name="Aurora.Replication.AuroraMySQL"></a>

In addition to Aurora Replicas, you have the following options for replication with Aurora MySQL:

+ Two Aurora MySQL DB clusters in different AWS Regions, by creating an Aurora Read Replica of an Aurora MySQL DB cluster in a different AWS Region\.

+ Two Aurora MySQL DB clusters in the same region, by using MySQL binary log \(binlog\) replication\.

+ An Amazon RDS MySQL DB instance as the master and an Aurora MySQL DB cluster, by creating an Aurora Read Replica of an Amazon RDS MySQL DB instance\. Typically, this approach is used for migration to Aurora MySQL, rather than for ongoing replication\.

For more information about replication with Aurora MySQL, see [Replication with Amazon Aurora MySQL](AuroraMySQL.Replication.md)\.

## Replication with Aurora PostgreSQL<a name="Aurora.Replication.AuroraPostgreSQL"></a>

In addition to Aurora Replicas, you can set up replication between an Amazon RDS PostgreSQL DB instance as the master and an Aurora PostgreSQL DB cluster\. You do so by creating an Aurora Read Replica of an Amazon RDS PostgreSQL DB instance\.

For more information about replication with Aurora PostgreSQL, see [Replication with Amazon Aurora PostgreSQL](AuroraPostgreSQL.Replication.md)\.