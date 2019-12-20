# Working with PostgreSQL Read Replicas<a name="USER_PostgreSQL.Replication.ReadReplicas"></a>

You usually use Read Replicas to configure replication between Amazon RDS DB instances\. For general information about Read Replicas, see [Working with Read Replicas](USER_ReadRepl.md)\. 

This section contains specific information about working with Read Replicas on PostgreSQL\.

**Topics**
+ [Read Replica Configuration with PostgreSQL](#USER_PostgreSQL.Replication.ReadReplicas.Configuration)
+ [Monitoring PostgreSQL Read Replicas](#USER_PostgreSQL.Replication.ReadReplicas.Monitor)
+ [Read Replica Limitations with PostgreSQL](#USER_PostgreSQL.Replication.ReadReplicas.Limitations)
+ [Replication Interruptions with PostgreSQL Read Replicas](#USER_PostgreSQL.Replication.ReadReplicas.Interruptions)
+ [Troubleshooting a PostgreSQL Read Replica Problem](#USER_ReadRepl.TroubleshootingPostgreSQL)

## Read Replica Configuration with PostgreSQL<a name="USER_PostgreSQL.Replication.ReadReplicas.Configuration"></a>

Amazon RDS PostgreSQL 9\.3\.5 and later uses PostgreSQL native streaming replication to create a read\-only copy of a source \(a "master" in PostgreSQL terms\) DB instance\. This Read Replica \(a "standby" in PostgreSQL terms\) DB instance is an asynchronously created physical replication of the master DB instance\. It's created by a special connection that transmits write ahead log \(WAL\) data between the source DB instance and the Read Replica where PostgreSQL asynchronously streams database changes as they are made\. 

PostgreSQL uses a "replication" role to perform streaming replication\. The role is privileged, but can't be used to modify any data\. PostgreSQL uses a single process for handling replication\. 

Before a DB instance can serve as a source DB instance, you must enable automatic backups on the source DB instance by setting the backup retention period to a value other than 0\. 

Creating a PostgreSQL Read Replica doesn't require an outage for the master DB instance\. Amazon RDS sets the necessary parameters and permissions for the source DB instance and the Read Replica without any service interruption\. A snapshot is taken of the source DB instance, and this snapshot becomes the Read Replica\. No outage occurs when you delete a Read Replica\. 

You can create up to five Read Replicas from one source DB instance\. For replication to operate effectively, each Read Replica should have the same amount of compute and storage resources as the source DB instance\. If you scale the source DB instance, you should also scale the Read Replicas\. 

Amazon RDS overrides any incompatible parameters on a Read Replica if it prevents the Read Replica from starting\. For example, suppose that the `max_connections` parameter value is higher on the source DB instance than on the Read Replica\. In that case, Amazon RDS updates the parameter on the Read Replica to be the same value as that on the source DB instance\. 

PostgreSQL DB instances use a secure connection that you can encrypt by setting the `ssl` parameter to `1` for both the source and the Read Replica instances\.

You can create a Read Replica from either single\-AZ or Multi\-AZ DB instance deployments\. You use Multi\-AZ deployments to improve the durability and availability of critical data, but you can't use the Multi\-AZ secondary to serve read\-only queries\. Instead, you can create Read Replicas from high\-traffic Multi\-AZ DB instances to offload read\-only queries\. If the source instance of a Multi\-AZ deployment fails over to the secondary, any associated Read Replicas automatically switch to use the secondary \(now primary\) as their replication source\. For more information, see [High Availability \(Multi\-AZ\) for Amazon RDS](Concepts.MultiAZ.md)\. 

You can create a Read Replica as a Multi\-AZ DB instance\. Amazon RDS creates a standby of your replica in another Availability Zone for failover support for the replica\. Creating your Read Replica as a Multi\-AZ DB instance is independent of whether the source database is a Multi\-AZ DB instance\. 

If you use the [postgres\_fdw](https://www.postgresql.org/docs/10/static/postgres-fdw.html) extension to access data from a remote server, the Read Replica will also have access to the remote server\. For more information about using `postgres_fdw`, see [Accessing External Data with the postgres\_fdw Extension](Appendix.PostgreSQL.CommonDBATasks.md#postgresql-commondbatasks-fdw)\.

## Monitoring PostgreSQL Read Replicas<a name="USER_PostgreSQL.Replication.ReadReplicas.Monitor"></a>

For PostgreSQL Read Replicas, you can monitor replication lag in Amazon CloudWatch by viewing the Amazon RDS `ReplicaLag` metric\. The `ReplicaLag` metric reports the value of `SELECT extract(epoch from now() - pg_last_xact_replay_timestamp()) AS slave_lag`\. 

## Read Replica Limitations with PostgreSQL<a name="USER_PostgreSQL.Replication.ReadReplicas.Limitations"></a>

The following are limitations for PostgreSQL Read Replicas: 
+ Each PostgreSQL Read Replicas is read\-only and can't be made a writable Read Replica\.
+ You can't create a Read Replica from another Read Replica \(that is, you can't create cascading Read Replicas\)\. 
+ You can promote a PostgreSQL Read Replica to be a new source DB instance\. However, the Read Replica doesn't become the new source DB instance automatically\. The Read Replica, when promoted, stops receiving WAL communications and is no longer a read\-only instance\. You must set up any replication you intend to have going forward because the promoted Read Replica is now a new source DB instance\. 
+ A PostgreSQL Read Replica reports a replication lag of up to five minutes if there are no user transactions occurring on the source DB instance\. 

## Replication Interruptions with PostgreSQL Read Replicas<a name="USER_PostgreSQL.Replication.ReadReplicas.Interruptions"></a>

In several situations, a PostgreSQL source DB instance can unintentionally break replication with a Read Replica\. These situations include the following: 
+ The `max_wal_senders` parameter is set too low to provide enough data to the number of Read Replicas\. This situation causes replication to stop\. 
+ The PostgreSQL parameter `wal_keep_segments` dictates how many WAL files are kept to provide data to the Read Replicas\. The parameter value specifies the number of logs to keep\. If you set the parameter value too low, you can cause a Read Replica to fall so far behind that streaming replication stops\. In this case, Amazon RDS reports a replication error and begins recovery on the Read Replica by replaying the source DB instance's archived WAL logs\. This recovery process continues until the Read Replica has caught up enough to continue streaming replication\. For more information, see [Troubleshooting a PostgreSQL Read Replica Problem](#USER_ReadRepl.TroubleshootingPostgreSQL)\. 
+ A PostgreSQL Read Replica requires a reboot if the source DB instance endpoint changes\. 

When the WAL stream that provides data to a Read Replica is broken, PostgreSQL switches into recovery mode to restore the Read Replica by using archived WAL files\. When this process is complete, PostgreSQL attempts to re\-establish streaming replication\. 

## Troubleshooting a PostgreSQL Read Replica Problem<a name="USER_ReadRepl.TroubleshootingPostgreSQL"></a>

PostgreSQL uses replication slots for cross\-region replication, so the process for troubleshooting same\-region replication problems and cross\-region replication problems is different\.

### Troubleshooting PostgreSQL Read Replica Problems Within an AWS Region<a name="USER_ReadRepl.TroubleshootingPostgreSQL.WithinARegion"></a>

The PostgreSQL parameter, `wal_keep_segments`, dictates how many Write Ahead Log \(WAL\) files are kept to provide data to the Read Replicas\. The parameter value specifies the number of logs to keep\. If you set the parameter value too low, you can cause a Read Replica to fall so far behind that streaming replication stops\. In this case, Amazon RDS reports a replication error and begins recovery on the Read Replica by replaying the source DB instance's archived WAL logs\. This recovery process continues until the Read Replica has caught up enough to continue streaming replication\. 

 The PostgreSQL log on the Read Replica shows when Amazon RDS is recovering a Read Replica that is this state by replaying archived WAL files\. 

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

After a certain amount of time, Amazon RDS replays enough archived WAL files on the replica to catch up and allow the Read Replica to begin streaming again\. At this point, PostgreSQL resumes streaming and writes a similar line to the following to the log file\.

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

For example, suppose that the PostgreSQL log shows that 35 files are recycled from the "checkpoint completed" log statements within a 5\-minute time frame\. In that case, we know that with this usage pattern a Read Replica relies on 35 transaction files in five minutes\. A Read Replica can't survive five minutes in a nonstreaming state if the source DB instance is set to the default `wal_keep_segments` parameter value of 32\.

### Troubleshooting PostgreSQL Read Replica Problems Across AWS Regions<a name="USER_ReadRepl.TroubleshootingPostgreSQL.AcrossRegions"></a>

PostgreSQL \(versions 9\.4\.7 and 9\.5\.2 exclusively\) uses physical replication slots to manage Write Ahead Log \(WAL\) retention on the source DB instance\. For each cross\-region Read Replica instance, Amazon RDS creates and associates a physical replication slot\. You can use two Amazon CloudWatch metrics, `Oldest Replication Slot Lag` and `Transaction Logs Disk Usage`, to see how far behind the most lagging replica is in terms of WAL data received and to see how much storage is being used for WAL data\. The `Transaction Logs Disk Usage` value can substantially increase when a cross\-region Read Replica is lagging significantly\.

If the workload on your DB instance generates a large amount of WAL data, you might need to change the DB instance class of your source DB instance and Read Replica\. In that case, you change it to one with high \(10 Gbps\) network performance for the replica to keep up\. The Amazon CloudWatch metric `Transaction Logs Generation` can help you understand the rate at which your workload is generating WAL data\. 

 To determine the status of a cross\-region Read Replica, you can query `pg_replication_slots` on the source instance, as in the following example: 

```
postgres=# select * from pg_replication_slots;

                  slot_name                  | plugin | slot_type | datoid | database | active | active_pid | xmin | catalog_xmin | restart_lsn
_______________________________________________________________________________________________________________________________________________
 rds_us_east_1_db_uzwlholddgpblksce6hgw4nkte |        | physical  |        |          | t      |      12598 |      |              | 4E/95000060
(1 row)
```