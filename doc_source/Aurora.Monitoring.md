# Monitoring an Amazon Aurora DB Cluster<a name="Aurora.Monitoring"></a>

Amazon Aurora provides a variety of Amazon CloudWatch metrics that you can monitor to determine the health and performance of your Aurora DB cluster\. You can use various tools, such as the Amazon RDS Management Console, AWS CLI, and CloudWatch API, to view Aurora metrics\. For more information, see [Monitoring Amazon RDS](CHAP_Monitoring.md)\.

## Amazon Aurora MySQL Metrics<a name="Aurora.AuroraMySQL.Monitoring.Metrics"></a>

The following metrics are available from Amazon Aurora MySQL\.

### Amazon Aurora Metrics<a name="aurora-metrics"></a>

The `AWS/RDS` namespace includes the following metrics that apply to database entities running on Amazon Aurora\.


| Metric | Description | 
| --- | --- | 
|  `ActiveTransactions`  |  The average number of current transactions executing on an Aurora database instance per second\.  | 
|  `AuroraBinlogReplicaLag`  |  The amount of time a replica DB cluster running on Aurora with MySQL compatibility lags behind the source DB cluster\. This metric reports the value of the `Seconds_Behind_Master` field of the MySQL `SHOW SLAVE STATUS` command\. This metric is useful for monitoring replica lag between Aurora DB clusters that are replicating across different AWS Regions\. For more information, see [Aurora MySQL Replication](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/AuroraMySQL.Replication.CrossRegion.html)\.  | 
|  `AuroraReplicaLag`  |  For an Aurora Replica, the amount of lag when replicating updates from the primary instance, in milliseconds\.  | 
|  `AuroraReplicaLagMaximum`  |  The maximum amount of lag between the primary instance and each Aurora DB instance in the DB cluster, in milliseconds\.  | 
|  `AuroraReplicaLagMinimum`  |  The minimum amount of lag between the primary instance and each Aurora DB instance in the DB cluster, in milliseconds\.  | 
|  `BinLogDiskUsage`  | The amount of disk space occupied by binary logs on the master, in bytes\.  | 
|  `BlockedTransactions`  | The average number of transactions in the database that are blocked per second\.  | 
|  `BufferCacheHitRatio`  |  The percentage of requests that are served by the buffer cache\.  | 
|  `CommitLatency`  | The amount of latency for commit operations, in milliseconds\.  | 
|  `CommitThroughput`  | The average number of commit operations per second\.  | 
|  `CPUCreditBalance`  |  The number of CPU credits that an instance has accumulated\. This metric applies only to `db.t2.small ` and `db.t2.medium ` instances\. It is used to determine how long an Aurora MySQL DB instance can burst beyond its baseline performance level at a given rate\.  CPU credit metrics are reported at 5\-minute intervals\.   | 
|  `CPUCreditUsage`  |  The number of CPU credits consumed during the specified period\. This metric applies only to `db.t2.small` and `db.t2.medium` instances\. It identifies the amount of time during which physical CPUs have been used for processing instructions by virtual CPUs allocated to the Aurora MySQL DB instance\.   CPU credit metrics are reported at 5\-minute intervals\.   | 
|  `CPUUtilization`  |  The percentage of CPU used by an Aurora DB instance\.  | 
|  `DatabaseConnections`  |  The number of connections to an Aurora DB instance\.  | 
|  `DDLLatency`  |  The amount of latency for data definition language \(DDL\) requests, in milliseconds—for example, create, alter, and drop requests\.  | 
|  `DDLThroughput`  |  The average number of DDL requests per second\.  | 
|  `Deadlocks`  | The average number of deadlocks in the database per second\.  | 
|  `DeleteLatency`  |  The amount of latency for delete queries, in milliseconds\.  | 
|  `DeleteThroughput`  |  The average number of delete queries per second\.  | 
|  `DiskQueueDepth`  |  The number of outstanding read/write requests waiting to access the disk\.  | 
|  `DMLLatency`  |  The amount of latency for inserts, updates, and deletes, in milliseconds\.  | 
|  `DMLThroughput`  | The average number of inserts, updates, and deletes per second\.  | 
|  `EngineUptime`  |  The amount of time that the instance has been running, in seconds\.  | 
|  `FreeableMemory`  |  The amount of available random access memory, in bytes\.  | 
|  `FreeLocalStorage`  |  The amount of storage available for temporary tables and logs, in bytes\. Unlike for other DB engines, for Aurora DB instances this metric reports the amount of storage available to each DB instance for temporary tables and logs\. This value depends on the DB instance class \(for pricing information, see the [Amazon RDS product page](http://aws.amazon.com/rds/#pricing)\)\. You can increase the amount of free storage space for an instance by choosing a larger DB instance class for your instance\.  | 
|  `InsertLatency`  |  The amount of latency for insert queries, in milliseconds\.  | 
|  `InsertThroughput`  |  The average number of insert queries per second\.  | 
|  `LoginFailures`  | The average number of failed login attempts per second\.  | 
|  `MaximumUsedTransactionIDs`  | The age of the oldest unvacuumed transaction ID, in transactions\. If this value reaches 2,146,483,648 \(2^31 \- 1,000,000\), the database is forced into read\-only mode, to avoid transaction ID wraparound\. For more information, see [Preventing Transaction ID Wraparound Failures](https://www.postgresql.org/docs/current/static/routine-vacuuming.html#VACUUM-FOR-WRAPAROUND) in the PostgreSQL documentation\.  | 
|  `NetworkReceiveThroughput`  |  The amount of network throughput received from clients by each instance in the Aurora MySQL DB cluster, in bytes per second\. This throughput doesn't include network traffic between instances in the Aurora DB cluster and the cluster volume\.  | 
|  `NetworkThroughput`  |  The amount of network throughput both received from and transmitted to clients by each instance in the Aurora MySQL DB cluster, in bytes per second\. This throughput doesn't include network traffic between instances in the DB cluster and the cluster volume\.  | 
|  `NetworkTransmitThroughput`  | The amount of network throughput sent to clients by each instance in the Aurora DB cluster, in bytes per second\. This throughput doesn't include network traffic between instances in the DB cluster and the cluster volume\.  | 
|  `Queries`  | The average number of queries executed per second\. | 
|  `ReadIOPS`  |  The average number of disk I/O operations per second\. Aurora with PostgreSQL compatibility reports read and write IOPS separately, on 1\-minute intervals\.  | 
|  `ReadLatency`  |  The average amount of time taken per disk I/O operation\.  | 
|  `ReadThroughput`  |  The average number of bytes read from disk per second\.  | 
|  `ResultSetCacheHitRatio`  |  The percentage of requests that are served by the Resultset cache\.  | 
|  `SelectLatency`  |  The amount of latency for select queries, in milliseconds\.  | 
|  `SelectThroughput`  |  The average number of select queries per second\.  | 
|  `SwapUsage`  |  The amount of swap space used on the Aurora PostgreSQL DB instance\.  | 
|  `TransactionLogsDiskUsage`  |  The amount of disk space occupied by transaction logs on the Aurora PostgreSQL DB instance\.  | 
|  `UpdateLatency`  |  The amount of latency for update queries, in milliseconds\.  | 
|  `UpdateThroughput`  |  The average number of update queries per second\.  | 
| `VolumeBytesUsed` |  The amount of storage used by your Aurora DB instance, in bytes\. This value affects the cost of the Aurora DB cluster \(for pricing information, see the [Amazon RDS product page](http://aws.amazon.com/rds/#pricing)\)\.  | 
|  `VolumeReadIOPs`  |  The average number of billed read I/O operations from a cluster volume, reported at 5\-minute intervals\. Billed read operations are calculated at the cluster volume level, aggregated from all instances in the Aurora DB cluster, and then reported at 5\-minute intervals\. The value is calculated by taking the value of the **Read operations** metric over a 5\-minute period\. You can determine the amount of billed read operations per second by taking the value of the **Billed read operations** metric and dividing by 300 seconds\. For example, if the **Billed read operations** returns 13,686, then the billed read operations per second is 45 \(13,686 / 300 = 45\.62\)\.  You accrue billed read operations for queries that request database pages that aren't in the buffer cache and therefore must be loaded from storage\. You might see spikes in billed read operations as query results are read from storage and then loaded into the buffer cache\.   | 
|  `VolumeWriteIOPs`  |  The average number of write disk I/O operations to the cluster volume, reported at 5\-minute intervals\.  | 
|  `WriteIOPS`  |  The average number of disk I/O operations per second\.  Aurora PostgreSQL reports read and write IOPS separately, on 1\-minute intervals\.  | 
|  `WriteLatency`  |  The average amount of time taken per disk I/O operation\.  | 
|  `WriteThroughput`  |  The average number of bytes written to disk per second\.  | 

## Viewing Aurora Metrics in the Amazon RDS Console<a name="Aurora.Monitoring.Metrics.RDS"></a>

To monitor the health and performance of your Aurora DB cluster, you can view some, but not all, of the metrics provided by Amazon Aurora in the Amazon RDS console\. For a detailed list of Aurora metrics available to the Amazon RDS console, see [Aurora Metrics Available in the Amazon RDS Console](#Aurora.Monitoring.Metrics.RDSAvailability)\.

**To view Aurora metrics in the Amazon RDS console**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Instances**\.

1. In the navigation pane, choose **Instances**, and then select the DB instance that you want to monitor\.

1. Choose **Instance actions**, and then choose **See details**\. 

1. In the Cloudwatch section, choose one of the following options from **Monitoring** for how you want to view your metrics:

   + **Cloudwatch** – Shows a summary of CloudWatch metrics\. Each metric includes a graph showing the metric monitored over a specific time span\. For more information, see [Monitoring Amazon RDS](CHAP_Monitoring.md)\.

   + **Enhanced monitoring** – Shows a summary of OS metrics available to an Aurora DB instance with Enhanced Monitoring enabled\. Each metric includes a graph showing the metric monitored over a specific time span\. For more information, see [Enhanced Monitoring](USER_Monitoring.OS.md)\.

   + **OS process list** – Shows the processes running on the DB instance or DB cluster and their related metrics including CPU percentage, memory usage, and so on\.   
![\[RDS metrics viewing options\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/AuroraMetrics01.png)

1. The following image shows the metrics view with **Enhanced monitoring** selected\.  
![\[Latest Metrics View\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/AuroraMetrics02.png)

## Aurora Metrics Available in the Amazon RDS Console<a name="Aurora.Monitoring.Metrics.RDSAvailability"></a>

Not all of the metrics provided by Amazon Aurora are available to you in the Amazon RDS console\. You can view them using other tools, however, such as the AWS CLI and CloudWatch API\. In addition, some of the metrics that are available in the Amazon RDS console are either shown only for specific instance classes, or with different names and different units of measurement\. 

The following Aurora metrics are not available in the Amazon RDS console:

+ `AuroraBinlogReplicaLag`

+ `DeleteLatency`

+ `DeleteThroughput`

+ `EngineUptime`

+ `InsertLatency`

+ `InsertThroughput`

+ `NetworkThroughput`

+ `Queries`

+ `UpdateLatency`

+ `UpdateThroughput`

In addition, some Aurora metrics are either shown only for specific instance classes, or only for DB instances, or with different names and different units of measurement:

+ The `CPUCreditBalance` and `CPUCreditUsage` metrics are displayed only for `db.t2.small` and `db.t2.medium` instances

+ The following metrics that are displayed with different names, as listed:    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Aurora.Monitoring.html)

+ The following metrics apply to an entire Aurora DB cluster, but are displayed only when viewing DB instances for an Aurora DB cluster in the Amazon RDS console:

  + `VolumeBytesUsed`

  + `VolumeReadIOPs`

  + `VolumeWriteIOPs`

+ The following metrics are displayed in megabytes, instead of bytes, in the Amazon RDS console:

  + `FreeableMemory`

  + `FreeLocalStorage`

  + `NetworkReceiveThroughput`

  + `NetworkTransmitThroughput`

### Latest Metrics View<a name="Aurora.Monitoring.Metrics.RDSAvailability.LMV"></a>

You can view a subset of categorized Aurora metrics in the Latest Metrics view of the Amazon RDS console\. The following table lists the categories and associated metrics displayed in the Amazon RDS console for an Aurora instance\.


| Category | Metrics | 
| --- | --- | 
| SQL |  `ActiveTransactions` `BlockedTransactions` `BufferCacheHitRatio` `CommitLatency` `CommitThroughput` `DatabaseConnections` `DDLLatency` `DDLThroughput` `Deadlocks` `DMLLatency` `DMLThroughput` `LoginFailures` `ResultSetCacheHitRatio` `SelectLatency` `SelectThroughput`  | 
| System |  `AuroraReplicaLag` `AuroraReplicaLagMaximum` `AuroraReplicaLagMinimum` `CPUCreditBalance` `CPUCreditUsage` `CPUUtilization` `FreeableMemory` `FreeLocalStorage` `NetworkReceiveThroughput`  | 
| Deployment |  `AuroraReplicaLag` `BufferCacheHitRatio` `ResultSetCacheHitRatio` `SelectThroughput`  | 

**Note**  
The **Failed SQL Statements** metric, displayed under the **SQL** category of the Latest Metrics view in the Amazon RDS console, does not apply to Amazon Aurora\.

## Related Topics<a name="Aurora.Monitoring.RelatedTopics"></a>

+ [Amazon Aurora on Amazon RDS](CHAP_Aurora.md)