# Working with PostgreSQL Read Replicas in Amazon RDS<a name="USER_PostgreSQL.Replication.ReadReplicas"></a>

You usually use read replicas to configure replication between Amazon RDS DB instances\. For general information about read replicas, see [Working with Read Replicas](USER_ReadRepl.md)\. 

This section contains specific information about working with read replicas on PostgreSQL\.

**Topics**
+ [Read Replica Configuration with PostgreSQL](#USER_PostgreSQL.Replication.ReadReplicas.Configuration)
+ [Monitoring PostgreSQL Read Replicas](#USER_PostgreSQL.Replication.ReadReplicas.Monitor)
+ [Read Replica Limitations with PostgreSQL](#USER_PostgreSQL.Replication.ReadReplicas.Limitations)
+ [Replication Interruptions with PostgreSQL Read Replicas](#USER_PostgreSQL.Replication.ReadReplicas.Interruptions)
+ [Troubleshooting a PostgreSQL Read Replica Problem](#USER_ReadRepl.TroubleshootingPostgreSQL)

## Read Replica Configuration with PostgreSQL<a name="USER_PostgreSQL.Replication.ReadReplicas.Configuration"></a>

Amazon RDS PostgreSQL uses PostgreSQL native streaming replication to create a read\-only copy of a source DB instance\. This read replica \(a "standby" in PostgreSQL terms\) DB instance is an asynchronously created physical replication of the source DB instance\. It's created by a special connection that transmits write ahead log \(WAL\) data between the source DB instance and the read replica where PostgreSQL asynchronously streams database changes as they are made\. 

PostgreSQL uses a "replication" role to perform streaming replication\. The role is privileged, but can't be used to modify any data\. PostgreSQL uses a single process for handling replication\. 

Before a DB instance can serve as a source DB instance, you must enable automatic backups on the source DB instance by setting the backup retention period to a value other than 0\. 

Creating a PostgreSQL read replica doesn't require an outage for the source DB instance\. Amazon RDS sets the necessary parameters and permissions for the source DB instance and the read replica without any service interruption\. A snapshot is taken of the source DB instance, and this snapshot becomes the read replica\. No outage occurs when you delete a read replica\. 

You can create up to five read replicas from one source DB instance\. For replication to operate effectively, each read replica should have the same amount of compute and storage resources as the source DB instance\. If you scale the source DB instance, also scale the read replicas\. 

Amazon RDS overrides any incompatible parameters on a read replica if it prevents the read replica from starting\. For example, suppose that the `max_connections` parameter value is higher on the source DB instance than on the read replica\. In that case, Amazon RDS updates the parameter on the read replica to be the same value as that on the source DB instance\. 

PostgreSQL DB instances use a secure connection that you can encrypt by setting the `ssl` parameter to `1` for both the source and the read replica instances\.

You can create a read replica from either single\-AZ or Multi\-AZ DB instance deployments\. You use Multi\-AZ deployments to improve the durability and availability of critical data, but you can't use the Multi\-AZ secondary to serve read\-only queries\. Instead, you can create read replicas from high\-traffic Multi\-AZ DB instances to offload read\-only queries\. If the source instance of a Multi\-AZ deployment fails over to the secondary, any associated read replicas automatically switch to use the secondary \(now primary\) as their replication source\. For more information, see [High Availability \(Multi\-AZ\) for Amazon RDS](Concepts.MultiAZ.md)\. 

You can create a read replica as a Multi\-AZ DB instance\. Amazon RDS creates a standby of your replica in another Availability Zone for failover support for the replica\. Creating your read replica as a Multi\-AZ DB instance is independent of whether the source database is a Multi\-AZ DB instance\. 

If you use the [postgres\_fdw](https://www.postgresql.org/docs/10/static/postgres-fdw.html) extension to access data from a remote server, the read replica will also have access to the remote server\. For more information about using `postgres_fdw`, see [Accessing External Data with the postgres\_fdw Extension](Appendix.PostgreSQL.CommonDBATasks.md#postgresql-commondbatasks-fdw)\.

## Monitoring PostgreSQL Read Replicas<a name="USER_PostgreSQL.Replication.ReadReplicas.Monitor"></a>

For PostgreSQL read replicas, you can monitor replication lag in Amazon CloudWatch by viewing the Amazon RDS `ReplicaLag` metric\. The `ReplicaLag` metric reports the value of `SELECT extract(epoch from now() - pg_last_xact_replay_timestamp()) AS replica_lag`\. 

## Read Replica Limitations with PostgreSQL<a name="USER_PostgreSQL.Replication.ReadReplicas.Limitations"></a>

The following are limitations for PostgreSQL read replicas: 
+ Each PostgreSQL read replicas is read\-only and can't be made a writable read replica\.
+ You can't create a read replica from another read replica \(that is, you can't create cascading read replicas\)\. 
+ You can promote a PostgreSQL read replica to be a new source DB instance\. However, the read replica doesn't become the new source DB instance automatically\. The read replica, when promoted, stops receiving WAL communications and is no longer a read\-only instance\. You must set up any replication you intend to have going forward because the promoted read replica is now a new source DB instance\. 
+ A PostgreSQL read replica reports a replication lag of up to five minutes if there are no user transactions occurring on the source DB instance\. 

## Replication Interruptions with PostgreSQL Read Replicas<a name="USER_PostgreSQL.Replication.ReadReplicas.Interruptions"></a>

In several situations, a PostgreSQL source DB instance can unintentionally break replication with a read replica\. These situations include the following: 
+ The `max_wal_senders` parameter is set too low to provide enough data to the number of read replicas\. This situation causes replication to stop\. 
+ The PostgreSQL parameter `wal_keep_segments` dictates how many WAL files are kept to provide data to the read replicas\. The parameter value specifies the number of logs to keep\. If you set the parameter value too low, you can cause a read replica to fall so far behind that streaming replication stops\. In this case, Amazon RDS reports a replication error and begins recovery on the read replica by replaying the source DB instance's archived WAL logs\. This recovery process continues until the read replica has caught up enough to continue streaming replication\. For more information, see [Troubleshooting a PostgreSQL Read Replica Problem](#USER_ReadRepl.TroubleshootingPostgreSQL)\. 
+ A PostgreSQL read replica requires a reboot if the IP address of the source DB instance endpoint changes\.

When the WAL stream that provides data to a read replica is broken, PostgreSQL switches into recovery mode to restore the read replica by using archived WAL files\. When this process is complete, PostgreSQL attempts to re\-establish streaming replication\. 

## Troubleshooting a PostgreSQL Read Replica Problem<a name="USER_ReadRepl.TroubleshootingPostgreSQL"></a>

PostgreSQL uses replication slots for cross\-Region replication, so the process for troubleshooting same\-region replication problems and cross\-Region replication problems is different\.

### Troubleshooting PostgreSQL Read Replica Problems Within an AWS Region<a name="USER_ReadRepl.TroubleshootingPostgreSQL.WithinARegion"></a>

The PostgreSQL parameter, `wal_keep_segments`, dictates how many write ahead log \(WAL\) files are kept to provide data to the read replicas\. The parameter value specifies the number of logs to keep\. If you set the parameter value too low, you can cause a read replica to fall so far behind that streaming replication stops\. In this case, Amazon RDS reports a replication error and begins recovery on the read replica by replaying the source DB instance's archived WAL logs\. This recovery process continues until the read replica has caught up enough to continue streaming replication\. 

 The PostgreSQL log on the read replica shows when Amazon RDS is recovering a read replica that is this state by replaying archived WAL files\. 

```
  2014-11-07 19:01:10 UTC::@:[23180]:DEBUG:  switched WAL source from archive to stream after
      failure 2014-11-07 19:01:10 UTC::@:[11575]:LOG:  started streaming WAL from primary at
      1A/D3000000 on timeline 1 2014-11-07 19:01:10 UTC::@:[11575]:FATAL:  could not receive
      data from WAL stream: ERROR:  requested WAL segment 000000010000001A000000D3 has already been
      removed 2014-11-07 19:01:10 UTC::@:[23180]:DEBUG:  could not restore file
      "00000002.history" from archive: return code 0 2014-11-07 19:01:15
      UTC::@:[23180]:DEBUG:  switched WAL source from stream to archive after failure
      recovering 000000010000001A000000D3 2014-11-07 19:01:16 UTC::@:[23180]:LOG:  restored log file "000000010000001A000000D3"
        from archive
```

After a certain amount of time, Amazon RDS replays enough archived WAL files on the replica to catch up and allow the read replica to begin streaming again\. At this point, PostgreSQL resumes streaming and writes a similar line to the following to the log file\.

```
  2014-11-07 19:41:36 UTC::@:[24714]:LOG:  started streaming WAL from primary at 1B/B6000000
      on timeline 1
```

You can determine how many WAL files you should keep by looking at the checkpoint information in the log\. The PostgreSQL log shows the following information at each checkpoint\. By looking at the "\# recycled" transaction log files of these log statements, you can understand how many transaction files will be recycled during a time range and use this information to tune the `wal_keep_segments` parameter\. 

```
  2014-11-07 19:59:35 UTC::@:[26820]:LOG:  checkpoint complete: wrote 376 buffers (0.2%); 0
      transaction log file(s) added, 0 removed, 1 recycled; write=35.681 s, sync=0.013 s,
      total=35.703 s; sync files=10, longest=0.013 s, average=0.001 s
```

For example, suppose that the PostgreSQL log shows that 35 files are recycled from the "checkpoint completed" log statements within a 5\-minute time frame\. In that case, we know that with this usage pattern a read replica relies on 35 transaction files in five minutes\. A read replica can't survive five minutes in a nonstreaming state if the source DB instance is set to the default `wal_keep_segments` parameter value of 32\.

### Troubleshooting PostgreSQL Read Replica Problems Across AWS Regions<a name="USER_ReadRepl.TroubleshootingPostgreSQL.AcrossRegions"></a>

PostgreSQL version 9\.5\.2 uses physical replication slots to manage write ahead log \(WAL\) retention on the source DB instance\. For each cross\-Region read replica instance, Amazon RDS creates and associates a physical replication slot\. You can use two Amazon CloudWatch metrics, `Oldest Replication Slot Lag` and `Transaction Logs Disk Usage`, to see how far behind the most lagging replica is in terms of WAL data received and to see how much storage is being used for WAL data\. The `Transaction Logs Disk Usage` value can substantially increase when a cross\-Region read replica is lagging significantly\.

If the workload on your DB instance generates a large amount of WAL data, you might need to change the DB instance class of your source DB instance and read replica\. In that case, you change it to one with high \(10 Gbps\) network performance for the replica to keep up\. The Amazon CloudWatch metric `Transaction Logs Generation` can help you understand the rate at which your workload is generating WAL data\. 

 To determine the status of a cross\-Region read replica, you can query `pg_replication_slots` on the source instance, as in the following example: 

```
postgres=# select * from pg_replication_slots;

                  slot_name                  | plugin | slot_type | datoid | database | active | active_pid | xmin | catalog_xmin | restart_lsn
_______________________________________________________________________________________________________________________________________________
 rds_us_east_1_db_uzwlholddgpblksce6hgw4nkte |        | physical  |        |          | t      |      12598 |      |              | 4E/95000060
(1 row)
```