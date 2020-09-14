# Working with MariaDB Read Replicas<a name="USER_MariaDB.Replication.ReadReplicas"></a>

This section contains specific information about working with read replicas on Amazon RDS MariaDB\. For general information about read replicas and instructions for using them, see [Working with Read Replicas](USER_ReadRepl.md)\.

**Topics**
+ [Read Replica Configuration with MariaDB](#USER_MariaDB.Replication.ReadReplicas.Configuration)
+ [Read Replica Updates with MariaDB](#USER_MariaDB.Replication.ReadReplicas.Updates)
+ [Multi\-AZ Read Replica Deployments with MariaDB](#USER_MariaDB.Replication.ReadReplicas.MultiAZ)
+ [Monitoring MariaDB Read Replicas](#USER_MariaDB.Replication.ReadReplicas.Monitor)
+ [Starting and Stopping Replication with MariaDB Read Replicas](#USER_MariaDB.Replication.ReadReplicas.StartStop)
+ [Troubleshooting a MariaDB Read Replica Problem](#USER_ReadRepl.Troubleshooting.MariaDB)

## Read Replica Configuration with MariaDB<a name="USER_MariaDB.Replication.ReadReplicas.Configuration"></a>

Before a MariaDB DB instance can serve as a replication source, you must enable automatic backups on the source DB instance by setting the backup retention period to a value other than 0\. This requirement also applies to a read replica that is the source DB instance for another read replica\. 

You can create up to five read replicas from one DB instance\. For replication to operate effectively, each read replica should have as the same amount of compute and storage resources as the source DB instance\. If you scale the source DB instance, also scale the read replicas\. 

If a read replica is running any version of MariaDB, you can specify it as the source DB instance for another read replica\. For example, you can create ReadReplica1 from MyDBInstance, and then create ReadReplica2 from ReadReplica1\. Updates made to MyDBInstance are replicated to ReadReplica1 and then replicated from ReadReplica1 to ReadReplica2\. You can't have more than four instances involved in a replication chain\. For example, you can create ReadReplica1 from MySourceDBInstance, and then create ReadReplica2 from ReadReplica1, and then create ReadReplica3 from ReadReplica2, but you can't create a ReadReplica4 from ReadReplica3\. 

If you promote a MariaDB read replica that is in turn replicating to other read replicas, those read replicas remain active\. Consider an example where MyDBInstance1 replicates to MyDBInstance2, and MyDBInstance2 replicates to MyDBInstance3\. If you promote MyDBInstance2, replication from MyDBInstance1 to MyDBInstance2 no longer occurs, but MyDBInstance2 still replicates to MyDBInstance3\. 

To enable automatic backups on a read replica for Amazon RDS MariaDB, first create the read replica, then modify the read replica to enable automatic backups\. 

You can run multiple concurrent read replica create or delete actions that reference the same source DB instance, as long as you stay within the limit of five read replicas for the source instance\. 

## Read Replica Updates with MariaDB<a name="USER_MariaDB.Replication.ReadReplicas.Updates"></a>

Read replicas are designed to support read queries, but you might need occasional updates\. For example, you might need to add an index to speed the specific types of queries accessing the replica\. You can enable updates by setting the `read_only` parameter to **0** in the DB parameter group for the read replica\. 

## Multi\-AZ Read Replica Deployments with MariaDB<a name="USER_MariaDB.Replication.ReadReplicas.MultiAZ"></a>

You can create a read replica from either single\-AZ or Multi\-AZ DB instance deployments\. You use Multi\-AZ deployments to improve the durability and availability of critical data, but you can't use the Multi\-AZ secondary to serve read\-only queries\. Instead, you can create read replicas from high\-traffic Multi\-AZ DB instances to offload read\-only queries\. If the source instance of a Multi\-AZ deployment fails over to the secondary, any associated read replicas automatically switch to use the secondary \(now primary\) as their replication source\. For more information, see [High Availability \(Multi\-AZ\) for Amazon RDS](Concepts.MultiAZ.md)\. 

You can create a read replica as a Multi\-AZ DB instance\. Amazon RDS creates a standby of your replica in another Availability Zone for failover support for the replica\. Creating your read replica as a Multi\-AZ DB instance is independent of whether the source database is a Multi\-AZ DB instance\. 

## Monitoring MariaDB Read Replicas<a name="USER_MariaDB.Replication.ReadReplicas.Monitor"></a>

For MariaDB read replicas, you can monitor replication lag in Amazon CloudWatch by viewing the Amazon RDS `ReplicaLag` metric\. The `ReplicaLag` metric reports the value of the `Seconds_Behind_Master` field of the `SHOW SLAVE STATUS` command\. 

Common causes for replication lag for MariaDB are the following: 
+ A network outage\.
+ Writing to tables with indexes on a read replica\. If the `read_only` parameter is not set to 0 on the read replica, it can break replication\.
+ Using a nontransactional storage engine such as MyISAM\. Replication is only supported for the InnoDB storage engine on MariaDB 10\.2 and later and the XtraDB storage engine on MariaDB 10\.1 and earlier\.

When the `ReplicaLag` metric reaches 0, the replica has caught up to the source DB instance\. If the `ReplicaLag` metric returns \-1, then replication is currently not active\. `ReplicaLag` = \-1 is equivalent to `Seconds_Behind_Master` = `NULL`\. 

## Starting and Stopping Replication with MariaDB Read Replicas<a name="USER_MariaDB.Replication.ReadReplicas.StartStop"></a>

You can stop and restart the replication process on an Amazon RDS DB instance by calling the system stored procedures [mysql\.rds\_stop\_replication](mysql_rds_stop_replication.md) and [mysql\.rds\_start\_replication](mysql_rds_start_replication.md)\. You can do this when replicating between two Amazon RDS instances for long\-running operations such as creating large indexes\. You also need to stop and start replication when importing or exporting databases\. For more information, see [Importing Data to an Amazon RDS MySQL or MariaDB DB Instance with Reduced Downtime](MySQL.Procedural.Importing.NonRDSRepl.md) and [Exporting Data from a MySQL DB Instance by Using Replication](MySQL.Procedural.Exporting.NonRDSRepl.md)\. 

If replication is stopped for more than 30 consecutive days, either manually or due to a replication error, Amazon RDS ends replication between the source DB instance and all read replicas\. It does so to prevent increased storage requirements on the source DB instance and long failover times\. The read replica DB instance is still available\. However, replication can't be resumed because the binary logs required by the read replica are deleted from the source DB instance after replication is ended\. You can create a new read replica for the source DB instance to reestablish replication\. 

## Troubleshooting a MariaDB Read Replica Problem<a name="USER_ReadRepl.Troubleshooting.MariaDB"></a>

The replication technologies for MariaDB are asynchronous\. Because they are asynchronous, occasional `BinLogDiskUsage` increases on the source DB instance and `ReplicaLag` on the read replica are to be expected\. For example, a high volume of write operations to the source DB instance can occur in parallel\. In contrast, write operations to the read replica are serialized using a single I/O thread, which can lead to a lag between the source instance and read replica\. For more information about read\-only replicas in the MariaDB documentation, go to [Replication Overview](http://mariadb.com/kb/en/mariadb/replication-overview/)\.

You can do several things to reduce the lag between updates to a source DB instance and the subsequent updates to the read replica, such as the following:
+ Sizing a read replica to have a storage size and DB instance class comparable to the source DB instance\.
+ Ensuring that parameter settings in the DB parameter groups used by the source DB instance and the read replica are compatible\. For more information and an example, see the discussion of the `max_allowed_packet` parameter later in this section\.

Amazon RDS monitors the replication status of your read replicas and updates the `Replication State` field of the read replica instance to `Error` if replication stops for any reason\. An example might be if DML queries run on your read replica conflict with the updates made on the source DB instance\. 

You can review the details of the associated error thrown by the MariaDB engine by viewing the `Replication Error` field\. Events that indicate the status of the read replica are also generated, including [RDS-EVENT-0045](USER_Events.md#RDS-EVENT-0045), [RDS-EVENT-0046](USER_Events.md#RDS-EVENT-0046), and [RDS-EVENT-0047](USER_Events.md#RDS-EVENT-0047)\. For more information about events and subscribing to events, see [Using Amazon RDS Event Notification](USER_Events.md)\. If a MariaDB error message is returned, review the error in the [MariaDB error message documentation](http://mariadb.com/kb/en/mariadb/mariadb-error-codes/)\.

One common issue that can cause replication errors is when the value for the `max_allowed_packet` parameter for a read replica is less than the `max_allowed_packet` parameter for the source DB instance\. The `max_allowed_packet` parameter is a custom parameter that you can set in a DB parameter group that is used to specify the maximum size of DML code that can be run on the database\. In some cases, the `max_allowed_packet` parameter value in the DB parameter group associated with a source DB instance is smaller than the `max_allowed_packet` parameter value in the DB parameter group associated with the source's read replica\. In these cases, the replication process can throw an error \(Packet bigger than 'max\_allowed\_packet' bytes\) and stop replication\. You can fix the error by having the source and read replica use DB parameter groups with the same `max_allowed_packet` parameter values\. 

Other common situations that can cause replication errors include the following:
+ Writing to tables on a read replica\. If you are creating indexes on a read replica, you need to have the `read_only` parameter set to **0** to create the indexes\. If you are writing to tables on the read replica, it might break replication\. 
+ Using a non\-transactional storage engine such as MyISAM\. read replicas require a transactional storage engine\. Replication is only supported for the InnoDB storage engine on MariaDB 10\.2 and later and the XtraDB storage engine on MariaDB 10\.1 and earlier\.
+ Using unsafe nondeterministic queries such as `SYSDATE()`\. For more information, see [Determination of Safe and Unsafe Statements in Binary Logging](https://dev.mysql.com/doc/refman/8.0/en/replication-rbr-safe-unsafe.html)\. 

If you decide that you can safely skip an error, you can follow the steps described in the section [Skipping the Current Replication Error](Appendix.MySQL.CommonDBATasks.md#Appendix.MySQL.CommonDBATasks.SkipError)\. Otherwise, you can delete the read replica and create an instance using the same DB instance identifier so that the endpoint remains the same as that of your old read replica\. If a replication error is fixed, the `Replication State` changes to *replicating*\.

For MariaDB DB instances, in some cases read replicas can't be switched to the secondary if some binlog events aren't flushed during the failure\. In these cases, you must manually delete and recreate the read replicas\. You can reduce the chance of this happening by setting the following dynamic variable values: `sync_binlog=1`, `innodb_flush_log_at_trx_commit=1`, and `innodb_support_xa=1`\. These settings might reduce performance, so test their impact before implementing the changes in a production environment\.