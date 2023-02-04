# Working with read replicas for Amazon RDS for PostgreSQL<a name="USER_PostgreSQL.Replication.ReadReplicas"></a>

You can scale reads for your Amazon RDS for PostgreSQL DB instance by adding read replicas to the instance\. As with other Amazon RDS database engines, RDS for PostgreSQL uses the native replication mechanisms of PostgreSQL to keep read replicas up to date with changes on the source DB\. For general information about read replicas and Amazon RDS, see [Working with read replicas](USER_ReadRepl.md)\. 

Following, you can find information specific to working with read replicas with RDS for PostgreSQL\. 

**Contents**
+ [Read replica limitations with PostgreSQL](#USER_PostgreSQL.Replication.ReadReplicas.Limitations)
+ [Read replica configuration with PostgreSQL](#USER_PostgreSQL.Replication.ReadReplicas.Configuration)
  + [Using RDS for PostgreSQL read replicas with Multi\-AZ configurations](#USER_PostgreSQL.Replication.ReadReplicas.Configuration.multi-az)
  + [Using cascading read replicas with RDS for PostgreSQL](#USER_PostgreSQL.Replication.ReadReplicas.Configuration.cascading)
+ [How streaming replication works for different RDS for PostgreSQL versions](#USER_PostgreSQL.Replication.ReadReplicas.Configuration.mechanisms-versions)
  + [Understanding the parameters that control PostgreSQL replication](#USER_PostgreSQL.Replication.ReadReplicas.Parameters)
    + [Example: How a read replica recovers from replication interruptions](#USER_PostgreSQL.Replication.example-how-it-works)
+ [Monitoring and tuning the replication process](#USER_PostgreSQL.Replication.ReadReplicas.Monitor)
  + [Monitoring replication slots for your RDS for PostgreSQL DB instance](#USER_PostgreSQL.Replication.ReadReplicas.Monitor-monitor-replication-slots)

## Read replica limitations with PostgreSQL<a name="USER_PostgreSQL.Replication.ReadReplicas.Limitations"></a>

The following are limitations for PostgreSQL read replicas: 
+ PostgreSQL read replicas are read\-only\. Although a read replica isn't a writeable DB instance, you can promote it to become a standalone RDS for PostgreSQL DB instance\. However, the process isn't reversible\.
+ You can't create a read replica from another read replica if your RDS for PostgreSQL DB instance is running a PostgreSQL version earlier than 14\.1\. RDS for PostgreSQL supports cascading read replicas on RDS for PostgreSQL version 14\.1 and higher releases only\. For more information, see [Using cascading read replicas with RDS for PostgreSQL](#USER_PostgreSQL.Replication.ReadReplicas.Configuration.cascading)\.
+ If you promote a PostgreSQL read replica, it becomes a writable DB instance\. It stops receiving write\-ahead log \(WAL\) files from a source DB instance, and it's no longer a read\-only instance\. You can create new read replicas from the promoted DB instance as you do for any RDS for PostgreSQL DB instance\. For more information, see [Promoting a read replica to be a standalone DB instance](USER_ReadRepl.md#USER_ReadRepl.Promote)\. 
+ If you promote a PostgreSQL read replica from within a replication chain \(a series of cascading read replicas\), any existing downstream read replicas continue receiving WAL files from the promoted instance, automatically\. For more information, see [Using cascading read replicas with RDS for PostgreSQL](#USER_PostgreSQL.Replication.ReadReplicas.Configuration.cascading)\. 
+ If no user transactions are running on the source DB instance, the associated PostgreSQL read replica reports a replication lag of up to five minutes\. The replica lag is calculated as `currentTime - lastCommitedTransactionTimestamp`, which means that when no transactions are being processed, the value of replica lag increases for a period of time until the write\-ahead log \(WAL\) segment switches\. By default RDS for PostgreSQL switches the WAL segment every 5 minutes, which results in a transaction record and a decrease in the reported lag\. 
+ You can't turn on automated backups for PostgreSQL read replicas for RDS for PostgreSQL versions earlier than 14\.1\. Automated backups for read replicas are supported for RDS for PostgreSQL 14\.1 and higher versions only\. For RDS for PostgreSQL 13 and earlier versions, create a snapshot from a read replica if you want a backup of it\.
+ Point\-in\-time recovery \(PITR\) isn't supported for read replicas\. You can use PITR with a primary \(writer\) instance only, not a read replica\. To learn more, see [Restoring a DB instance to a specified time](USER_PIT.md)\.

## Read replica configuration with PostgreSQL<a name="USER_PostgreSQL.Replication.ReadReplicas.Configuration"></a>

RDS for PostgreSQL uses PostgreSQL native streaming replication to create a read\-only copy of a source DB instance\. This read replica DB instance is an asynchronously created physical replica of the source DB instance\. It's created by a special connection that transmits write ahead log \(WAL\) data from the source DB instance to the read replica\. For more information, see [Streaming Replication](https://www.postgresql.org/docs/14/warm-standby.html#STREAMING-REPLICATION) in the PostgreSQL documentation\.

PostgreSQL asynchronously streams database changes to this secure connection as they're made on the source DB instance\. You can encrypt communications from your client applications to the source DB instance or any read replicas by setting the `ssl` parameter to `1`\. For more information, see [Using SSL with a PostgreSQL DB instance](PostgreSQL.Concepts.General.SSL.md) \.

PostgreSQL uses a *replication* role to perform streaming replication\. The role is privileged, but you can't use it to modify any data\. PostgreSQL uses a single process for handling replication\. 

You can create a PostgreSQL read replica without affecting operations or users of the source DB instance\. Amazon RDS sets the necessary parameters and permissions for you, on the source DB instance and the read replica, without affecting the service\. A snapshot is taken of the source DB instance, and this snapshot is used to create the read replica\. If you delete the read replica at some point in the future, no outage occurs\.

You can create up to 15 read replicas from one source DB instance within the same Region\. As of RDS for PostgreSQL 14\.1, you can also create up to three levels of read replica in a chain \(cascade\) from a source DB instance\. For more information, see [Using cascading read replicas with RDS for PostgreSQL](#USER_PostgreSQL.Replication.ReadReplicas.Configuration.cascading)\. In all cases, the source DB instance needs to have automated backups configured\. You do this by setting the backup retention period on your DB instance to any value other than 0\. For more information, see [Creating a read replica](USER_ReadRepl.md#USER_ReadRepl.Create)\. 

You can create read replicas for your RDS for PostgreSQL DB instance in the same AWS Region as your source DB instance\. This is known as *in\-Region* replication\. You can also create read replicas in different AWS Regions than the source DB instance\. This is known as *cross\-Region* replication\. For more information about setting up cross\-Region read replicas, see [Creating a read replica in a different AWS Region](USER_ReadRepl.md#USER_ReadRepl.XRgn)\. The various mechanisms supporting the replication process for in\-Region and cross\-Region differ slightly depending on the RDS for PostgreSQL version as explained in [How streaming replication works for different RDS for PostgreSQL versions](#USER_PostgreSQL.Replication.ReadReplicas.Configuration.mechanisms-versions)\. 

For replication to operate effectively, each read replica should have the same amount of compute and storage resources as the source DB instance\. If you scale the source DB instance, be sure to also scale the read replicas\. 

Amazon RDS overrides any incompatible parameters on a read replica if they prevent the read replica from starting\. For example, suppose that the `max_connections` parameter value is higher on the source DB instance than on the read replica\. In that case, Amazon RDS updates the parameter on the read replica to be the same value as that on the source DB instance\. 

RDS for PostgreSQL read replicas have access to external databases that are available through foreign data wrappers \(FDWs\) on the source DB instance\. For example, suppose that your RDS for PostgreSQL DB instance is using the `mysql_fdw` wrapper to access data from RDS for MySQL\. If so, your read replicas can also access that data\. Other supported FDWs include `oracle_fdw`, `postgres_fdw`, and `tds_fdw`\. For more information, see [Working with the supported foreign data wrappers for Amazon RDS for PostgreSQL](Appendix.PostgreSQL.CommonDBATasks.Extensions.foreign-data-wrappers.md)\. 

### Using RDS for PostgreSQL read replicas with Multi\-AZ configurations<a name="USER_PostgreSQL.Replication.ReadReplicas.Configuration.multi-az"></a>

You can create a read replica from a single\-AZ or Multi\-AZ DB instance\. You can use Multi\-AZ deployments to improve the durability and availability of critical data, with a standby replica\. A *standby replica* is a dedicated read replica that can assume the workload if the source DB fails over\. You can't use your standby replica to serve read traffic\. However, you can create read replicas from high\-traffic Multi\-AZ DB instances to offload read\-only queries\. To learn more about Multi\-AZ deployments, see [Multi\-AZ DB instance deployments](Concepts.MultiAZSingleStandby.md)\. 

If the source DB instance of a Multi\-AZ deployment fails over to a standby, the associated read replicas switch to using the standby \(now primary\) as their replication source\. The read replicas might need to restart, depending on the RDS for PostgreSQL version, as follows: 
+ **PostgreSQL 13 and higher versions** – Restarting isn't required\. The read replicas are automatically synchronized with the new primary\. However, in some cases your client application might cache Domain Name Service \(DNS\) details for your read replicas\. If so, set the time\-to\-live \(TTL\) value to less than 30 seconds\. Doing this prevents the read replica from holding on to a stale IP address \(and thus, prevents it from synchronizing with the new primary\)\. To learn more about this and other best practices, see [Amazon RDS basic operational guidelines](CHAP_BestPractices.md#CHAP_BestPractices.DiskPerformance)\. 
+ **PostgreSQL 12 and all earlier versions** – The read replicas restart automatically after a fail over to the standby replica because the standby \(now primary\) has a different IP address and a different instance name\. Restarting synchronizes the read replica with the new primary\. 

To learn more about failover, see [Failover process for Amazon RDS](Concepts.MultiAZSingleStandby.md#Concepts.MultiAZ.Failover)\. To learn more about how read replicas work in a Multi\-AZ deployment, see [Working with read replicas](USER_ReadRepl.md)\. 

To provide failover support for a read replica, you can create the read replica as a Multi\-AZ DB instance so that Amazon RDS creates a standby of your replica in another Availability Zone \(AZ\)\. Creating your read replica as a Multi\-AZ DB instance is independent of whether the source database is a Multi\-AZ DB instance\. 

### Using cascading read replicas with RDS for PostgreSQL<a name="USER_PostgreSQL.Replication.ReadReplicas.Configuration.cascading"></a>

As of version 14\.1, RDS for PostgreSQL supports cascading read replicas\. With *cascading read replicas*, you can scale reads without adding overhead to your source RDS for PostgreSQL DB instance\. Updates to the WAL log aren't sent by the source DB instance to each read replica\. Instead, each read replica in a cascading series sends WAL log updates to the next read replica in the series\. This reduces the burden on the source DB instance\. 

With cascading read replicas, your RDS for PostgreSQL DB instance sends WAL data to the first read replica in the chain\. That read replica then sends WAL data to the second replica in the chain, and so on\. The end result is that all read replicas in the chain have the changes from the RDS for PostgreSQL DB instance, but without the overhead solely on the source DB instance\.

You can create a series of up to three read replicas in a chain from a source RDS for PostgreSQL DB instance\. For example, suppose that you have an RDS for PostgreSQL 14\.1 DB instance, `rpg-db-main`\. You can do the following: 
+ Starting with `rpg-db-main`, create the first read replica in the chain, `read-replica-1`\.
+ Next, from `read-replica-1`, create the next read replica in the chain, `read-replica-2`\. 
+ Finally, from `read-replica-2`, create the third read replica in the chain, `read-replica-3`\.

You can't create another read replica beyond this third cascading read replica in the series for `rpg-db-main`\. A complete series of instances from an RDS for PostgreSQL source DB instance through to the end of a series of cascading read replicas can consist of at most four DB instances\. 

For cascading read replicas to work, turn on automatic backups on your RDS for PostgreSQL\. Create the read replica first and then turn on automatic backups on the RDS for PostgreSQL DB instance\. The process is the same as for other Amazon RDS DB engines\. For more information, see [Creating a read replica](USER_ReadRepl.md#USER_ReadRepl.Create)\. 

As with any read replica, you can promote a read replica that's part of a cascade\. Promoting a read replica from within a chain of read replicas removes that replica from the chain\. For example, suppose that you want to move some of the workload off of your `rpg-db-main` DB instance to a new instance for use by the accounting department only\. Assuming the chain of three read replicas from the example, you decide to promote `read-replica-2`\. The chain is affected as follows:
+ Promoting `read-replica-2` removes it from the replication chain\.
  + It is now a full read/write DB instance\. 
  + It continues replicating to `read-replica-3`, just as it was doing before promotion\.
+ Your `rpg-db-main` continues replicating to `read-replica-1`\.

For more information about promoting read replicas, see [Promoting a read replica to be a standalone DB instance](USER_ReadRepl.md#USER_ReadRepl.Promote)\.

**Note**  
For cascading read replicas, RDS for PostgreSQL supports 15 read replicas for each source DB instance at first level of replication, and 5 read replicas for each source DB instance at the second and third level of replication\.

## How streaming replication works for different RDS for PostgreSQL versions<a name="USER_PostgreSQL.Replication.ReadReplicas.Configuration.mechanisms-versions"></a>

As discussed in [Read replica configuration with PostgreSQL](#USER_PostgreSQL.Replication.ReadReplicas.Configuration), RDS for PostgreSQL uses PostgreSQL's native streaming replication protocol to send WAL data from the source DB instance\. It sends source WAL data to read replicas for both in\-Region and cross\-Region read replicas\. With version 9\.4, PostgreSQL introduced physical replication slots as a supporting mechanism for the replication process\.

A *physical replication slot* prevents a source DB instance from removing WAL data before it's consumed by all read replicas\. Each read replica has its own physical slot on the source DB instance\. The slot keeps track of the oldest WAL \(by logical sequence number, LSN\) that might be needed by the replica\. After all slots and DB connections have progressed beyond a given WAL \(LSN\), that LSN becomes a candidate for removal at the next checkpoint\.

Amazon RDS uses Amazon S3 to archive WAL data\. For in\-Region read replicas, you can use this archived data to recover the read replica when necessary\. An example of when you might do so is if the connection between source DB and read replica is interrupted for any reason\. 

In the following table, you can find a summary of differences between PostgreSQL versions and the supporting mechanisms for in\-Region and cross\-Region used by RDS for PostgreSQL\. 


| In\-Region | Cross\-Region | 
| --- |--- |
| PostgreSQL 14\.1 and higher versions | 
| --- |
|   Replication slots Amazon S3 archive   |   Replication slots   | 
| PostgreSQL 13 and lower versions | 
| --- |
|   Amazon S3 archive   |   Replication slots   | 

For more information, see [Monitoring and tuning the replication process](#USER_PostgreSQL.Replication.ReadReplicas.Monitor)\. 

### Understanding the parameters that control PostgreSQL replication<a name="USER_PostgreSQL.Replication.ReadReplicas.Parameters"></a>

The following parameters affect the replication process and determine how well read replicas stay up to date with the source DB instance:

**max\_wal\_senders**  
The `max_wal_senders` parameter specifies the maximum number of connections that the source DB instance can support at the same time over the streaming replication protocol\. The default for RDS for PostgreSQL 13 and higher releases is 20\. This parameter should be set to slightly higher than the actual number of read replicas\. If this parameter is set too low for the number of read replicas, replication stops\.   
For more information, see [max\_wal\_senders](https://www.postgresql.org/docs/devel/runtime-config-replication.html#GUC-MAX-WAL-SENDERS) in the PostgreSQL documentation\. 

**wal\_keep\_segments**  
The `wal_keep_segments` parameter specifies the number of write\-ahead log \(WAL\) files that the source DB instance keeps in the `pg_wal` directory\. The default setting is 32\.   
If `wal_keep_segments` isn't set to a large enough value for your deployment, a read replica can fall so far behind that streaming replication stops\. If that happens, Amazon RDS generates a replication error and begins recovery on the read replica\. It does so by replaying the source DB instance's archived WAL data from Amazon S3\. This recovery process continues until the read replica has caught up enough to continue streaming replication\. You can see this process in action as captured by the PostgreSQL log in [Example: How a read replica recovers from replication interruptionsExample: Read replica recovery from replication interruptions](#USER_PostgreSQL.Replication.example-how-it-works)\.   
In PostgreSQL version 13, the `wal_keep_segments` parameter is named `wal_keep_size`\. It serves the same purpose as `wal_keep_segments`, but its default value is in megabytes \(MB\) \(2048 MB\) rather than the number of files\. For more information, see [wal\_keep\_segments](https://www.postgresql.org/docs/12/runtime-config-replication.html#GUC-WAL-KEEP-SEGMENTS) and [wal\_keep\_size](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-WAL-KEEP-SIZE) in the PostgreSQL documentation\. 

**max\_slot\_wal\_keep\_size**  
The `max_slot_wal_keep_size` parameter controls the quantity of WAL data that the RDS for PostgreSQL DB instance retains in the `pg_wal` directory to serve slots\. This parameter is used for configurations that use replication slots\. The default value for this parameter is `-1`, meaning that there's no limit to how much WAL data is kept on the source DB instance\. For information about monitoring your replication slots, see [Monitoring replication slots for your RDS for PostgreSQL DB instance](#USER_PostgreSQL.Replication.ReadReplicas.Monitor-monitor-replication-slots)\.  
For more information about this parameter, see [max\_slot\_wal\_keep\_size](https://www.postgresql.org/docs/devel/runtime-config-replication.html#GUC-MAX-SLOT-WAL-KEEP-SIZE) in the PostgreSQL documentation\.

Whenever the stream that provides WAL data to a read replica is interrupted, PostgreSQL switches into recovery mode\. It restores the read replica by using archived WAL data from Amazon S3 or by using the using WAL data associated with the replication slot\. When this process is complete, PostgreSQL re\-establishes streaming replication\. 

#### Example: How a read replica recovers from replication interruptions<a name="USER_PostgreSQL.Replication.example-how-it-works"></a>

In the following example, you find the log details that demonstrate the recovery process for a read replica\. The example is from an RDS for PostgreSQL DB instance running PostgreSQL version 12\.9 in the same AWS Region as the source DB, so replication slots aren't used\. The recovery process is the same for other RDS for PostgreSQL DB instances running PostgreSQL earlier than version 14\.1 with in\-Region read replicas\. 

When the read replica lost contact with the source DB instance, Amazon RDS records the issue in the log as `FATAL: could not receive data from WAL stream` message, along with the `ERROR: requested WAL segment ... has already been removed`\. As shown in the bold line, Amazon RDS recovers the replica by replaying an archived WAL file\. 

```
2014-11-07 19:01:10 UTC::@:[23180]:DEBUG:  switched WAL source from archive to stream after failure
2014-11-07 19:01:10 UTC::@:[11575]:LOG: started streaming WAL from primary at 1A/D3000000 on timeline 1
2014-11-07 19:01:10 UTC::@:[11575]:FATAL: could not receive data from WAL stream:
ERROR:  requested WAL segment 000000010000001A000000D3 has already been removed
2014-11-07 19:01:10 UTC::@:[23180]:DEBUG: could not restore file "00000002.history" from archive: return code 0
2014-11-07 19:01:15 UTC::@:[23180]:DEBUG: switched WAL source from stream to archive after failure recovering 000000010000001A000000D3
2014-11-07 19:01:16 UTC::@:[23180]:LOG:  restored log file "000000010000001A000000D3" from archive
```

When Amazon RDS replays enough archived WAL data on the replica to catch up, streaming to the read replica begins again\. When streaming resumes, Amazon RDS writes an entry to the log file similar to the following\.

```
2014-11-07 19:41:36 UTC::@:[24714]:LOG:started streaming WAL from primary at 1B/B6000000 on timeline 1
```

## Monitoring and tuning the replication process<a name="USER_PostgreSQL.Replication.ReadReplicas.Monitor"></a>

We strongly recommend that you routinely monitor your RDS for PostgreSQL DB instance and read replicas\. You need to ensure that your read replicas are keeping up with changes on the source DB instance\. Amazon RDS transparently recovers your read replicas when interruptions to the replication process occur\. However, it's best to avoid needing to recover at all\. Recovering using replication slots is faster than using the Amazon S3 archive, but any recovery process can affect read performance\. 

To determine how well your read replicas are keeping up with the source DB instance, you can do the following: 
+ **Check the amount of `ReplicaLag` between source DB instance and replicas\.** *Replica lag* is the amount of time, in milliseconds, that a read replica lags behind its source DB instance\. This metric reports the result of the following query\.

  ```
  SELECT extract(epoch from now() - pg_last_xact_replay_timestamp()) AS replica_lag
  ```

  Replica lag is an indication of how well a read replica is keeping up with the source DB instance\. It's the amount of latency between the source DB instance and a specific read instance\. A high value for replica lag can indicate a mismatch between the DB instance classes or storage types \(or both\) used by the source DB instance and its read replicas\. The DB instance class and storage types for DB source instance and all read replicas should be the same\. 

  Replica lag can also be the result of intermittent connection issues\. You can monitor replication lag in Amazon CloudWatch by viewing the Amazon RDS `ReplicaLag` metric\. To learn more about `ReplicaLag` and other metrics for Amazon RDS, see [Amazon CloudWatch metrics for Amazon RDS](rds-metrics.md)\.
+ **Check the PostgreSQL log for information you can use to adjust your settings\.** At every checkpoint, the PostgreSQL log captures the number of recycled transaction log files, as shown in the following example\.

  ```
  2014-11-07 19:59:35 UTC::@:[26820]:LOG:  checkpoint complete: wrote 376 buffers (0.2%);
  0 transaction log file(s) added, 0 removed, 1 recycled; write=35.681 s, sync=0.013 s, total=35.703 s;
  sync files=10, longest=0.013 s, average=0.001 s
  ```

  You can use this information to figure out how many transaction files are being recycled in a given time period\. You can then change the setting for `wal_keep_segments` if necessary\. For example, suppose that the PostgreSQL log at `checkpoint complete` shows `35 recycled` for a 5\-minute interval\. In this case, the `wal_keep_segments` default value of 32 isn't sufficient to keep pace with the streaming activity, so you should increase the value of this parameter\.
+ **Use Amazon CloudWatch to monitor metrics that can predict replication issues\.** Rather than analyzing the PostgreSQL log directly, you can use Amazon CloudWatch to check metrics that have been collected\. For example, you can check the value of the `TransactionLogsGeneration` metric to see how much WAL data is being generated by the source DB instance\. In some cases, the workload on your DB instance might generate a large amount of WAL data\. If so, you might need to change the DB instance class for your source DB instance and read replicas\. Using an instance class with high \(10 Gbps\) network performance can reduce replica lag\. 

### Monitoring replication slots for your RDS for PostgreSQL DB instance<a name="USER_PostgreSQL.Replication.ReadReplicas.Monitor-monitor-replication-slots"></a>

All versions of RDS for PostgreSQL use replication slots for cross\-Region read replicas\. RDS for PostgreSQL 14\.1 and higher versions use replication slots for in\-Region read replicas\. In\-region read replicas also use Amazon S3 to archive WAL data\. In other words, if your DB instance and read replicas are running PostgreSQL 14\.1 or higher, replication slots and Amazon S3 archives are both available for recovering the read replica\. Recovering a read replica using its replication slot is faster than recovering from Amazon S3 archive\. So, we recommend that you monitor the replication slots and related metrics\. 

You can view the replication slots on your RDS for PostgreSQL DB instances by querying the `pg_replication_slots` view, as follows\.

```
postgres=> SELECT * FROM pg_replication_slots;
slot_name                  | plugin | slot_type | datoid | database | temporary | active | active_pid | xmin | catalog_xmin | restart_lsn | confirmed_flush_lsn | wal_status | safe_wal_size | two_phase
---------------------------+--------+-----------+--------+----------+-----------+--------+------------+------+--------------+-------------+---------------------+------------+---------------+-----------
rds_us_west_1_db_555555555 |        | physical  |        |          | f         | t      |      13194 |      |              | 23/D8000060 |                     | reserved   |               | f
(1 row)
```

The `wal_status` of `reserved` value means that the amount of WAL data held by the slot is within the bounds of the `max_wal_size` parameter\. In other words, the replication slot is properly sized\. Other possible status values are as follows: 
+ `extended` – The slot exceeds the `max_wal_size` setting, but the WAL data is retained\.
+ `unreserved` – The slot no longer has the all required WAL data\. Some of it will be removed at the next checkpoint\.
+ `lost` – Some required WAL data has been removed\. The slot is no longer usable\.

The `pg_replication_slots` view shows you the current state of your replication slots\. To assess the performance of your replication slots, you can use Amazon CloudWatch and monitor the following metrics:
+ **`OldestReplicationSlotLag`** – Lists the slot that has the most lag, that is the one that's furthest behind the primary\. This lag can be associated with the read replica but also the connection\. 
+ **`TransactionLogsDiskUsage`** – Shows how much storage is being used for WAL data\. When a read replica lags significantly, the value of this metric can increase substantially\.

To learn more about using Amazon CloudWatch and its metrics for RDS for PostgreSQL, see [Monitoring Amazon RDS metrics with Amazon CloudWatch](monitoring-cloudwatch.md)\. For more information about monitoring streaming replication on your RDS for PostgreSQL DB instances, see [Best practices for Amazon RDS PostgreSQL replication](http://aws.amazon.com/blogs/database/best-practices-for-amazon-rds-postgresql-replication/) on the *AWS Database Blog*\. 