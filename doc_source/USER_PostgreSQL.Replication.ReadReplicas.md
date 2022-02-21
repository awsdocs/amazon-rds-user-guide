# Working with PostgreSQL read replicas in Amazon RDS<a name="USER_PostgreSQL.Replication.ReadReplicas"></a>

You usually use read replicas to configure replication between Amazon RDS DB instances\. For general information about read replicas, see [Working with read replicas](USER_ReadRepl.md)\. 

Following, you can find information specific to working with read replicas on PostgreSQL\.

**Topics**
+ [Read replica configuration with PostgreSQL](#USER_PostgreSQL.Replication.ReadReplicas.Configuration)
+ [Monitoring PostgreSQL read replicas](#USER_PostgreSQL.Replication.ReadReplicas.Monitor)
+ [Read replica limitations with PostgreSQL](#USER_PostgreSQL.Replication.ReadReplicas.Limitations)
+ [Replication interruptions with PostgreSQL read replicas](#USER_PostgreSQL.Replication.ReadReplicas.Interruptions)
+ [Troubleshooting PostgreSQL read replica problems](#USER_ReadRepl.TroubleshootingPostgreSQL)

## Read replica configuration with PostgreSQL<a name="USER_PostgreSQL.Replication.ReadReplicas.Configuration"></a>

Amazon RDS PostgreSQL uses PostgreSQL native streaming replication to create a read\-only copy of a source DB instance\. This read replica \(a "standby" in PostgreSQL terms\) DB instance is an asynchronously created physical replication of the source DB instance\. It's created by a special connection that transmits write ahead log \(WAL\) data between the source DB instance and the read replica\. PostgreSQL asynchronously streams database changes to this connection as they are made\. 

PostgreSQL uses a "replication" role to perform streaming replication\. The role is privileged, but can't be used to modify any data\. PostgreSQL uses a single process for handling replication\. 

So that your DB instance can serve as a source DB instance, make sure to set up automatic backups on that DB instance\. To do this, set the backup retention period to a value other than 0\. 

You can create a PostgreSQL read replica without affecting the source DB instance and users of the source\. Amazon RDS sets the necessary parameters and permissions for the source DB instance and the read replica without interrupting the service\. A snapshot is taken of the source DB instance, and this snapshot becomes the read replica\. No outage occurs when you delete a read replica\. 

You can create up to five read replicas from one source DB instance\. For replication to operate effectively, each read replica should have the same amount of compute and storage resources as the source DB instance\. If you scale the source DB instance, also scale the read replicas\. 

Amazon RDS overrides any incompatible parameters on a read replica if it prevents the read replica from starting\. For example, suppose that the `max_connections` parameter value is higher on the source DB instance than on the read replica\. In that case, Amazon RDS updates the parameter on the read replica to be the same value as that on the source DB instance\. 

PostgreSQL DB instances use a secure connection that you can encrypt by setting the `ssl` parameter to `1` for both the source and the read replica instances\.

You can create a read replica from a single\-AZ or Multi\-AZ DB instance\. You can use Multi\-AZ deployments to improve the durability and availability of critical data, but you can't use the Multi\-AZ standby replica to serve read traffic\. Instead, you can create read replicas from high\-traffic Multi\-AZ DB instances to offload read\-only queries\.

If the source DB instance of a Multi\-AZ deployment fails over to the standby, any associated read replicas switch to using the standby \(now primary\) as their replication source\. The new primary has a different IP address than its predecessor\. As a result, in some cases the read replicas reboot after a failover so that they can get back into sync with the new primary\. This is the case for all PostgreSQL versions prior to PostgreSQL 13\. For PostgreSQL 13 and higher releases, rebooting isn't needed\.

**Note**  
Changing a DB instance class or changing a DB instance name also changes the IP address for the instance\. Prior to PostgreSQL 13, such changes required that the DB instance be rebooted\. 

To learn more about Multi\-AZ deployments, see [Multi\-AZ DB instance deployments](Concepts.MultiAZSingleStandby.md)\. For information about the failover mechanism, see [Failover process for Amazon RDS](Concepts.MultiAZSingleStandby.md#Concepts.MultiAZ.Failover)\. To learn more about how read replicas work in a Multi\-AZ deployment, see [Working with read replicas](USER_ReadRepl.md)\. 

You can create a read replica as a Multi\-AZ DB instance\. Amazon RDS creates a standby of your replica in another Availability Zone \(AZ\) for failover support for the replica\. Creating your read replica as a Multi\-AZ DB instance is independent of whether the source database is a Multi\-AZ DB instance\. 

If you use the [postgres\_fdw](https://www.postgresql.org/docs/10/static/postgres-fdw.html) extension to access data from a remote server, the read replica also has access to the remote server\. For more information about using `postgres_fdw`, see [Accessing external data with the postgres\_fdw extension](Appendix.PostgreSQL.CommonDBATasks.md#postgresql-commondbatasks-fdw)\.

## Monitoring PostgreSQL read replicas<a name="USER_PostgreSQL.Replication.ReadReplicas.Monitor"></a>

For your RDS for PostgreSQL read replicas, you can monitor replication lag in Amazon CloudWatch by viewing the Amazon RDS `ReplicaLag` metric\. *Replica lag* is the amount of time, in milliseconds, that a read replica lags behind the source DB instance\. The `ReplicaLag` metric reports the value of `SELECT extract(epoch from now() - pg_last_xact_replay_timestamp()) AS replica_lag`\. 

To learn more about `ReplicaLag` and other metrics for Amazon RDS, see [Amazon CloudWatch metrics for Amazon RDS](rds-metrics.md)\. 

## Read replica limitations with PostgreSQL<a name="USER_PostgreSQL.Replication.ReadReplicas.Limitations"></a>

The following are limitations for PostgreSQL read replicas: 
+ Each PostgreSQL read replica is read\-only\. You can't make a writable read replica\.
+ You can't create a read replica from another read replica\. Thus, you can't create cascading read replicas\. 
+ You can promote a PostgreSQL read replica to be a new source DB instance\. However, the read replica doesn't become the new source DB instance automatically\. The read replica, when promoted, stops receiving write\-head log \(WAL\) communications and is no longer a read\-only instance\. Make sure to set up any replication that you intend to have in the future, because the promoted read replica is now a new source DB instance\. 
+ If no user transactions are occurring on the source DB instance, a PostgreSQL read replica reports a replication lag of up to five minutes\.
+ You can't turn on automated backups for PostgreSQL read replicas\.

## Replication interruptions with PostgreSQL read replicas<a name="USER_PostgreSQL.Replication.ReadReplicas.Interruptions"></a>

Several PostgreSQL parameters control the replication process\. They can unintentionally break replication with a read replica in some situations\. Following are some examples:
+ The `[max\_wal\_senders](https://www.postgresql.org/docs/devel/runtime-config-replication.html#GUC-MAX-WAL-SENDERS)` parameter is set too low to provide enough data to the number of read replicas\. If this happens, replication stops\. 
+ The `wal_keep_segments` parameter specifies the number of write\-head log \(WAL\) files \(logs\) to keep to provide data to the read replicas\. If you set the parameter value too low, you can cause a read replica to fall so far behind that streaming replication stops\. In this case, Amazon RDS reports a replication error\. RDS then begins recovery on the read replica by replaying the source DB instance's archived WAL logs from Amazon S3\. This recovery process continues until the read replica has caught up enough to continue streaming replication\. For more information, see [Troubleshooting PostgreSQL read replica problems](#USER_ReadRepl.TroubleshootingPostgreSQL)\. 
+ The [max\_slot\_wal\_keep\_size](https://www.postgresql.org/docs/devel/runtime-config-replication.html#GUC-MAX-SLOT-WAL-KEEP-SIZE) parameter dictates the maximum size of WAL files that [replication slots](https://www.postgresql.org/docs/devel/warm-standby.html#STREAMING-REPLICATION-SLOTS) are allowed to keep in the `pg_wal` directory at checkpoint time\. If `max_slot_wal_keep_size` is \-1 \(the default\), replication slots might retain an unlimited number of WAL files\. Otherwise, the `restart_lsn` value of a replication slot might fall behind the current log sequence number \(LSN\) by more than the given size\. If so, the standby using the slot might no longer be able to continue replication due to removal of required WAL files\. You can see the WAL availability of replication slots in [pg\_replication\_slots](https://www.postgresql.org/docs/devel/view-pg-replication-slots.html)\. 

When the WAL stream that provides data to a read replica is broken, PostgreSQL switches into recovery mode\. It restores the read replica by using archived WAL files from Amazon S3\. When this process is complete, PostgreSQL attempts to re\-establish streaming replication\. 

## Troubleshooting PostgreSQL read replica problems<a name="USER_ReadRepl.TroubleshootingPostgreSQL"></a>

How to troubleshoot PostgreSQL read replica problems differs based on a couple of factors\. These are the version of RDS for PostgreSQL that you are running and whether replication is across AWS Regions or within the same Region:
+  Starting with PostgreSQL version 9\.5\.2, RDS for PostgreSQL uses physical replication slots to manage write ahead log \(WAL\) retention on each source DB instance across AWS Regions\. 
+ As of RDS for PostgreSQL version 14\.1 and higher, physical replication slots are used for replicas across Regions and also within the same Region\.

To troubleshoot replication issues when physical replication slots are used, see [Read replicas and physical replication slots](#USER_ReadRepl.TroubleshootingPostgreSQL.AcrossRegions)\. The information in that section applies to in\-Region read replicas for RDS for PostgreSQL 14\.1 and higher\. It also applies to cross\-Region read replicas for RDS for PostgreSQL 9\.5\.2 and higher \(but before PostgreSQL 14\.1\)\. 

To troubleshoot replication issues that might be caused by your PostgreSQL parameter settings, see [Read replicas and WAL parameter settings](#USER_ReadRepl.TroubleshootingPostgreSQL.WithinARegion)\. 

### Read replicas and WAL parameter settings<a name="USER_ReadRepl.TroubleshootingPostgreSQL.WithinARegion"></a>

As mentioned in [Replication interruptions with PostgreSQL read replicas](#USER_PostgreSQL.Replication.ReadReplicas.Interruptions), the PostgreSQL parameter `wal_keep_segments` parameter value can interrupt replication if it's set too low\. The value that you set for this parameter specifies the number of logs to keep\. If the value is too low, a read replica can fall so far behind that streaming replication stops\. If that happens, Amazon RDS reports a replication error and begins recovery on the read replica by replaying the source DB instance's archived WAL logs\. This recovery process continues until the read replica has caught up enough to continue streaming replication\.

The read replica's PostgreSQL log captures the process, as shown in this example\. The bold line shows that Amazon RDS recovered a read replica that fell behind and lost contact with the primary, by replaying an archived WAL file\. 

```
2014-11-07 19:01:10 UTC::@:[23180]:DEBUG:  switched WAL source from archive to stream after failure
2014-11-07 19:01:10 UTC::@:[11575]:LOG: started streaming WAL from primary at 1A/D3000000 on timeline 1
2014-11-07 19:01:10 UTC::@:[11575]:FATAL: could not receive data from WAL stream:
           ERROR:  requested WAL segment 000000010000001A000000D3 has already been removed 
2014-11-07 19:01:10 UTC::@:[23180]:DEBUG: could not restore file "00000002.history" from archive: return code 0
2014-11-07 19:01:15 UTC::@:[23180]:DEBUG: switched WAL source from stream to archive after failure recovering 000000010000001A000000D3
2014-11-07 19:01:16 UTC::@:[23180]:LOG:  restored log file "000000010000001A000000D3" from archive
```

When Amazon RDS replays enough archived WAL files on the replica to catch up, streaming to the read replica can begin again\. When PostgreSQL resumes streaming, it writes an entry to the log file, similar to the following\.

```
2014-11-07 19:41:36 UTC::@:[24714]:LOG:  started streaming WAL from primary at 1B/B6000000
   on timeline 1
```

The PostgreSQL log captures other important information that you can use to help you troubleshoot read replica issues\. For example, at every checkpoint, the PostgreSQL log captures the number of recycled transaction log files, as shown in the following example\. 

```
2014-11-07 19:59:35 UTC::@:[26820]:LOG:  checkpoint complete: wrote 376 buffers (0.2%); 0
      transaction log file(s) added, 0 removed, 1 recycled; write=35.681 s, sync=0.013 s,
      total=35.703 s; sync files=10, longest=0.013 s, average=0.001 s
```

You can use this information to figure out how many transaction files will be recycled in a given time period, and tune the `wal_keep_segments` parameter as needed\. For example, suppose that the PostgreSQL log at `checkpoint complete` shows `35 recycled` for a 5\-minute interval\. In this case, the `wal_keep_segments` default value of 32 isn't sufficient to keep pace with the streaming activity, and we recommend that you increase the value of this parameter\. 

Also, if the workload on your DB instance generates a large amount of WAL data, you might need to change the DB instance class of your source DB instance and read replica\. Changing it to an instance class with high \(10 Gbps\) network performance helps the replica keep up with the source\. To learn more about the rate at which your workload is generating WAL data, check the Amazon CloudWatch `TransactionLogsGeneration` metric\.

### Read replicas and physical replication slots<a name="USER_ReadRepl.TroubleshootingPostgreSQL.AcrossRegions"></a>

[Physical replication slots](https://www.postgresql.org/docs/14/warm-standby.html#STREAMING-REPLICATION-SLOTS) have been used since RDS for PostgreSQL 9\.5\.2 to manage write ahead log \(WAL\) retention on the source DB instance for cross\-Region read replicas\. As of RDS for PostgreSQL 14\.1, physical replication slots are also used within an AWS Region to manage WAL retention\. For each read replica, Amazon RDS creates and associates a physical replication slot that holds onto WAL data\. Physical replication slots provide faster recovery than using the archived log files from Amazon S3, so the goal is to ensure that your RDS for PostgreSQL DB instance uses them when possible to provide fast recovery\. 

You can use two Amazon CloudWatch metrics to help you manage the physical slots:
+ `OldestReplicationSlotLag` – Lists the replica that has the most lag, that is, it's furthest behind the primary
+ `TransactionLogsDiskUsage` – Shows how much storage is being used for WAL data\. When a read replica lags significantly, the value of this metric can increase substantially\.

To learn more about using Amazon CloudWatch and its metrics for RDS for PostgreSQL, see [Monitoring Amazon RDS metrics with Amazon CloudWatch](monitoring-cloudwatch.md)\.

To check the status of a physical replication slot for a read replica, you can query `pg_replication_slots` on the source instance, as in the following example\.

```
postgres=#select * from pg_replication_slots;

                  slot_name                  | plugin | slot_type | datoid | database | active | active_pid | xmin | catalog_xmin | restart_lsn
_______________________________________________________________________________________________________________________________________________
 rds_us_east_1_db_uzwlholddgpblksce6hgw4nkte |        | physical  |        |          | t      |      12598 |      |              | 4E/95000060
(1 row)
```

As noted in [Replication interruptions with PostgreSQL read replicas](#USER_PostgreSQL.Replication.ReadReplicas.Interruptions), the value of [max\_slot\_wal\_keep\_size](https://www.postgresql.org/docs/devel/runtime-config-replication.html#GUC-MAX-SLOT-WAL-KEEP-SIZE) parameter has an effect on the replication process\. By default, this parameter has a value of \-1, which allows replication slots to retain an unlimited number of WAL files\. You can change this to a specific value, but be aware that after your DB instance reaches that value, its slots can hold no more WAL logs\. The resulting behavior of your Amazon RDS DB instance if the `max_slot_wal_keep_size` parameter reaches a limit depends on whether replication is for in\-Region or cross\-Region read replicas: 
+ For in\-Region replicas, if the `max_slot_wal_keep_size` limit is reached, Amazon RDS uses the archive logs from Amazon S3 until it catches up to the source\. It can then start streaming again\. To avoid this, you can monitor your physical replication slots and adjust the `max_slot_wal_keep_size` to minimize the need for the archive log files\.
+ For cross\-Region replicas, if the `max_slot_wal_keep_size` limit is reached, replication stops\. Archive logs aren't used, so be careful if you adjust the `max_slot_wal_keep_size` parameter for cross\-Region read replicas\. 