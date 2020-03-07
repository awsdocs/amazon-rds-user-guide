# Working with MySQL Read Replicas<a name="USER_MySQL.Replication.ReadReplicas"></a>

This section contains specific information about working with Read Replicas on Amazon RDS MySQL\. For general information about Read Replicas and instructions for using them, see [Working with Read Replicas](USER_ReadRepl.md)\.

**Topics**
+ [Read Replica Configuration with MySQL](#USER_MySQL.Replication.ReadReplicas.Configuration)
+ [Configuring Delayed Replication with MySQL](#USER_MySQL.Replication.ReadReplicas.DelayReplication)
+ [Read Replica Updates with MySQL](#USER_MySQL.Replication.ReadReplicas.Updates)
+ [Multi\-AZ Read Replica Deployments with MySQL](#USER_MySQL.Replication.ReadReplicas.MultiAZ)
+ [Monitoring MySQL Read Replicas](#USER_MySQL.Replication.ReadReplicas.Monitor)
+ [Starting and Stopping Replication with MySQL Read Replicas](#USER_MySQL.Replication.ReadReplicas.StartStop)
+ [Deleting Read Replicas with MySQL](#USER_MySQL.Replication.ReadReplicas.Delete)
+ [Troubleshooting a MySQL Read Replica Problem](#USER_ReadRepl.Troubleshooting)

## Read Replica Configuration with MySQL<a name="USER_MySQL.Replication.ReadReplicas.Configuration"></a>

Before a MySQL DB instance can serve as a replication source, make sure to enable automatic backups on the source DB instance\. To do this, set the backup retention period to a value other than 0\. This requirement also applies to a Read Replica that is the source DB instance for another Read Replica\. Automatic backups are supported only for Read Replicas running any version of MySQL 5\.6 and later\. You can configure replication based on binary log coordinates for a MySQL DB instance\. 

On Amazon RDS MySQL version 5\.7\.23 and later MySQL 5\.7 versions, you can configure replication using global transaction identifiers \(GTIDs\)\. For more information, see [Using GTID\-Based Replication for Amazon RDS MySQL](mysql-replication-gtid.md)\.

You can create up to five Read Replicas from one DB instance\. For replication to operate effectively, each Read Replica should have as the same amount of compute and storage resources as the source DB instance\. If you scale the source DB instance, you should also scale the Read Replicas\. 

If a Read Replica is running any version of MySQL 5\.6 and later, you can specify it as the source DB instance for another Read Replica\. For example, you can create ReadReplica1 from MyDBInstance, and then create ReadReplica2 from ReadReplica1\. Updates made to MyDBInstance are replicated to ReadReplica1 and then replicated from ReadReplica1 to ReadReplica2\. You can't have more than four instances involved in a replication chain\. For example, you can create ReadReplica1 from MySourceDBInstance, and then create ReadReplica2 from ReadReplica1, and then create ReadReplica3 from ReadReplica2, but you can't create a ReadReplica4 from ReadReplica3\. 

If you promote a MySQL Read Replica that is in turn replicating to other Read Replicas, those Read Replicas remain active\. Consider an example where MyDBInstance1 replicates to MyDBInstance2, and MyDBInstance2 replicates to MyDBInstance3\. If you promote MyDBInstance2, replication from MyDBInstance1 to MyDBInstance2 no longer occurs, but MyDBInstance2 still replicates to MyDBInstance3\. 

To enable automatic backups on a Read Replica for Amazon RDS MySQL version 5\.6 and later, first create the Read Replica\. Then modify the Read Replica to enable automatic backups\. 

You can run multiple Read Replica create or delete actions at the same time that reference the same source DB instance\. To do this, stay within the limit of five Read Replicas for each source instance\. 

### Preparing MySQL DB Instances That Use MyISAM<a name="USER_MySQL.Replication.ReadReplicas.Configuration-MyISAM-Instances"></a>

If your MySQL DB instance uses a nontransactional engine such as MyISAM, you need to perform the following steps to successfully set up your Read Replica\. These steps are required to make sure that the Read Replica has a consistent copy of your data\. These steps are not required if all of your tables use a transactional engine such as InnoDB\. 

1. Stop all data manipulation language \(DML\) and data definition language \(DDL\) operations on non\-transactional tables in the source DB instance and wait for them to complete\. SELECT statements can continue running\. 

1. Flush and lock the tables in the source DB instance\.

1. Create the Read Replica using one of the methods in the following sections\.

1. Check the progress of the Read Replica creation using, for example, the `DescribeDBInstances` API operation\. Once the Read Replica is available, unlock the tables of the source DB instance and resume normal database operations\. 

## Configuring Delayed Replication with MySQL<a name="USER_MySQL.Replication.ReadReplicas.DelayReplication"></a>

You can use delayed replication as a strategy for disaster recovery\. With delayed replication, you specify the minimum amount of time, in seconds, to delay replication from the source to the Read Replica\. In the event of a disaster, such as a table deleted unintentionally, you complete the following steps to recover from the disaster quickly:
+ Stop replication to the Read Replica before the change that caused the disaster is sent to it\.

  Use the [mysql\.rds\_stop\_replication](mysql_rds_stop_replication.md) stored procedure to stop replication\.
+ Start replication and specify that replication stops automatically at a log file location\.

  You specify a location just before the disaster using the [mysql\.rds\_start\_replication\_until](mysql_rds_start_replication_until.md) stored procedure\.
+ Promote the Read Replica to be the new source DB instance by using the instructions in [Promoting a Read Replica to Be a Standalone DB Instance](USER_ReadRepl.md#USER_ReadRepl.Promote)\.

**Note**  
On Amazon RDS MySQL 5\.7, delayed replication is supported for MySQL 5\.7\.22 and later\. On Amazon RDS MySQL 5\.6, delayed replication is supported for MySQL 5\.6\.40 and later\. Delayed replication is not supported on Amazon RDS MySQL 8\.0\.
Use stored procedures to configure delayed replication\. You can't configure delayed replication with the AWS Management Console, the AWS CLI, or the Amazon RDS API\.
On Amazon RDS MySQL 5\.7\.23 and later MySQL 5\.7 versions, you can use GTID\-based replication in a delayed replication configuration\. If you use GTID\-based replication, use the [mysql\.rds\_start\_replication\_until\_gtid](mysql_rds_start_replication_until_gtid.md) stored procedure instead of the [mysql\.rds\_start\_replication\_until](mysql_rds_start_replication_until.md) stored procedure\. For more information about GTID\-based replication, see [Using GTID\-Based Replication for Amazon RDS MySQL](mysql-replication-gtid.md)\.

**Topics**
+ [Configuring Delayed Replication During Read Replica Creation](#USER_MySQL.Replication.ReadReplicas.DelayReplication.ReplicaCreation)
+ [Modifying Delayed Replication for an Existing Read Replica](#USER_MySQL.Replication.ReadReplicas.DelayReplication.ExistingReplica)
+ [Setting a Location to Stop Replication to a Read Replica](#USER_MySQL.Replication.ReadReplicas.DelayReplication.StartUntil)

### Configuring Delayed Replication During Read Replica Creation<a name="USER_MySQL.Replication.ReadReplicas.DelayReplication.ReplicaCreation"></a>

To configure delayed replication for any future Read Replica created from a DB instance, run the [mysql\.rds\_set\_configuration](mysql_rds_set_configuration.md) stored procedure with the `target delay` parameter\.

**To configure delayed replication during Read Replica creation**

1. Using a MySQL client, connect to the MySQL DB instance to be the source for Read Replicas as the master user\.

1. Run the [mysql\.rds\_set\_configuration](mysql_rds_set_configuration.md) stored procedure with the `target delay` parameter\.

   For example, run the following stored procedure to specify that replication is delayed by at least one hour \(3,600 seconds\) for any Read Replica created from the current DB instance\.

   ```
   call mysql.rds_set_configuration('target delay', 3600);
   ```
**Note**  
After running this stored procedure, any Read Replica you create using the AWS CLI or Amazon RDS API is configured with replication delayed by the specified number of seconds\.

### Modifying Delayed Replication for an Existing Read Replica<a name="USER_MySQL.Replication.ReadReplicas.DelayReplication.ExistingReplica"></a>

To modify delayed replication for an existing Read Replica, run the [mysql\.rds\_set\_source\_delay](mysql_rds_set_source_delay.md) stored procedure\.

**To modify delayed replication for an existing Read Replica**

1. Using a MySQL client, connect to the Read Replica as the master user\.

1. Use the [mysql\.rds\_stop\_replication](mysql_rds_stop_replication.md) stored procedure to stop replication\.

1. Run the [mysql\.rds\_set\_source\_delay](mysql_rds_set_source_delay.md) stored procedure\.

   For example, run the following stored procedure to specify that replication to the Read Replica is delayed by at least one hour \(3600 seconds\)\.

   ```
   call mysql.rds_set_source_delay(3600);
   ```

1. Use the [mysql\.rds\_start\_replication](mysql_rds_start_replication.md) stored procedure to start replication\.

### Setting a Location to Stop Replication to a Read Replica<a name="USER_MySQL.Replication.ReadReplicas.DelayReplication.StartUntil"></a>

After stopping replication to the Read Replica, you can start replication and then stop it at a specified binary log file location using the [mysql\.rds\_start\_replication\_until](mysql_rds_start_replication_until.md) stored procedure\.

**To start replication to a Read Replica and stop replication at a specific location**

1. Using a MySQL client, connect to the source MySQL DB instance as the master user\.

1. Run the [mysql\.rds\_start\_replication\_until](mysql_rds_start_replication_until.md) stored procedure\.

   The following example initiates replication and replicates changes until it reaches location `120` in the `mysql-bin-changelog.000777` binary log file\. In a disaster recovery scenario, assume that location `120` is just before the disaster\.

   ```
   call mysql.rds_start_replication_until(
     'mysql-bin-changelog.000777',
     120);
   ```

Replication stops automatically when the stop point is reached\. The following RDS event is generated: `Replication has been stopped since the replica reached the stop point specified by the rds_start_replication_until stored procedure`\.

After replication is stopped, in a disaster recovery scenario, you can [Promoting a Read Replica to Be a Standalone DB Instance](USER_ReadRepl.md#USER_ReadRepl.Promote) promote the Read Replica to be the new source DB instance\. For information about promoting the Read Replica, see [Promoting a Read Replica to Be a Standalone DB Instance](USER_ReadRepl.md#USER_ReadRepl.Promote)\.

## Read Replica Updates with MySQL<a name="USER_MySQL.Replication.ReadReplicas.Updates"></a>

Read replicas are designed to support read queries, but you might need occasional updates\. For example, you might need to add an index to optimize the specific types of queries accessing the replica\. You can enable updates by setting the `read_only` parameter to `0` in the DB parameter group for the Read Replica\. Be careful when disabling read\-only on a Read Replica because it can cause problems if the Read Replica becomes incompatible with the source DB instance\. Change the value of the `read_only` parameter back to `1` as soon as possible\. 

## Multi\-AZ Read Replica Deployments with MySQL<a name="USER_MySQL.Replication.ReadReplicas.MultiAZ"></a>

You can create a Read Replica from either single\-AZ or Multi\-AZ DB instance deployments\. You use Multi\-AZ deployments to improve the durability and availability of critical data, but you can't use the Multi\-AZ secondary to serve read\-only queries\. Instead, you can create Read Replicas from high\-traffic Multi\-AZ DB instances to offload read\-only queries\. If the source instance of a Multi\-AZ deployment fails over to the secondary, any associated Read Replicas automatically switch to use the secondary \(now primary\) as their replication source\. For more information, see [High Availability \(Multi\-AZ\) for Amazon RDS](Concepts.MultiAZ.md)\. 

You can create a Read Replica as a Multi\-AZ DB instance\. Amazon RDS creates a standby of your replica in another Availability Zone for failover support for the replica\. Creating your Read Replica as a Multi\-AZ DB instance is independent of whether the source database is a Multi\-AZ DB instance\. 

**Note**  
To create a Read Replica as a Multi\-AZ DB instance, the DB instance must be MySQL 5\.6 or later\.

## Monitoring MySQL Read Replicas<a name="USER_MySQL.Replication.ReadReplicas.Monitor"></a>

For MySQL Read Replicas, you can monitor replication lag in Amazon CloudWatch by viewing the Amazon RDS `ReplicaLag` metric\. The `ReplicaLag` metric reports the value of the `Seconds_Behind_Master` field of the `SHOW SLAVE STATUS` command\. 

Common causes for replication lag for MySQL are the following: 
+ A network outage\.
+ Writing to tables that have different indexes on a Read Replica\. If the `read_only` parameter is set to `0` on the Read Replica, replication can break if the Read Replica becomes incompatible with the source DB instance\. After you've performed maintenance tasks on the Read Replica, we recommend that you set the `read_only` parameter back to `1`\.
+ Using a nontransactional storage engine such as MyISAM\. Replication is only supported for the InnoDB storage engine on MySQL\.

When the `ReplicaLag` metric reaches 0, the replica has caught up to the source DB instance\. If the `ReplicaLag` metric returns \-1, then replication is currently not active\. `ReplicaLag` = \-1 is equivalent to `Seconds_Behind_Master` = `NULL`\. 

## Starting and Stopping Replication with MySQL Read Replicas<a name="USER_MySQL.Replication.ReadReplicas.StartStop"></a>

You can stop and restart the replication process on an Amazon RDS DB instance by calling the system stored procedures [mysql\.rds\_stop\_replication](mysql_rds_stop_replication.md) and [mysql\.rds\_start\_replication](mysql_rds_start_replication.md)\. You can do this when replicating between two Amazon RDS instances for long\-running operations such as creating large indexes\. You also need to stop and start replication when importing or exporting databases\. For more information, see [Importing Data to an Amazon RDS MySQL or MariaDB DB Instance with Reduced Downtime](MySQL.Procedural.Importing.NonRDSRepl.md) and [Exporting Data from a MySQL DB Instance by Using Replication](MySQL.Procedural.Exporting.NonRDSRepl.md)\. 

If replication is stopped for more than 30 consecutive days, either manually or due to a replication error, Amazon RDS terminates replication between the source DB instance and all Read Replicas\. It does so to prevent increased storage requirements on the source DB instance and long failover times\. The Read Replica DB instance is still available\. However, replication can't be resumed because the binary logs required by the Read Replica are deleted from the source DB instance after replication is terminated\. You can create a new Read Replica for the source DB instance to reestablish replication\. 

## Deleting Read Replicas with MySQL<a name="USER_MySQL.Replication.ReadReplicas.Delete"></a>

To delete Read Replicas, do so explicitly using the same mechanisms as for deleting a DB instance\. If you delete the source DB instance without deleting the replicas, each replica is promoted to a standalone DB instance\. 

## Troubleshooting a MySQL Read Replica Problem<a name="USER_ReadRepl.Troubleshooting"></a>

For MySQL DB instances, in some cases Read Replicas present replication errors or data inconsistencies between the Read Replica and its source DB instance\. This problem occurs when some binary log \(binlog\) events or InnoDB redo logs aren't flushed during a failure of the Read Replica or the source DB instance\. In these cases, manually delete and recreate the Read Replicas\. You can reduce the chance of this happening by setting the following dynamic variable values: `sync_binlog=1`, `innodb_flush_log_at_trx_commit=1`, and `innodb_support_xa=1`\. These settings might reduce performance, so test their impact before implementing the changes in a production environment\. For MySQL 5\.5, `sync_binlog` defaults to `0`, but in MySQL 5\.6 and later, problems are less likely to occur because these parameters are all set to the recommended values by default\.

The replication technologies for MySQL are asynchronous\. Because they are asynchronous, occasional `BinLogDiskUsage` increases on the source DB instance and `ReplicaLag` on the Read Replica are to be expected\. For example, a high volume of write operations to the source DB instance can occur in parallel\. In contrast, write operations to the Read Replica are serialized using a single I/O thread, which can lead to a lag between the source instance and Read Replica\. For more information about read\-only replicas in the MySQL documentation, see [Replication Implementation Details](http://dev.mysql.com/doc/refman/5.5/en/replication-implementation-details.html)\.

You can do several things to reduce the lag between updates to a source DB instance and the subsequent updates to the Read Replica, such as the following:
+ Sizing a Read Replica to have a storage size and DB instance class comparable to the source DB instance\.
+ Ensuring that parameter settings in the DB parameter groups used by the source DB instance and the Read Replica are compatible\. For more information and an example, see the discussion of the `max_allowed_packet` parameter later in this section\.

Amazon RDS monitors the replication status of your Read Replicas and updates the `Replication State` field of the Read Replica instance to `Error` if replication stops for any reason\. An example might be if DML queries run on your Read Replica conflict with the updates made on the source DB instance\. 

You can review the details of the associated error thrown by the MySQL engine by viewing the `Replication Error` field\. Events that indicate the status of the Read Replica are also generated, including [RDS-EVENT-0045](USER_Events.md#RDS-EVENT-0045), [RDS-EVENT-0046](USER_Events.md#RDS-EVENT-0046), and [RDS-EVENT-0047](USER_Events.md#RDS-EVENT-0047)\. For more information about events and subscribing to events, see [Using Amazon RDS Event Notification](USER_Events.md)\. If a MySQL error message is returned, review the error number in the [MySQL error message documentation](https://dev.mysql.com/doc/refman/8.0/en/server-error-reference.html)\.

One common issue that can cause replication errors is when the value for the `max_allowed_packet` parameter for a Read Replica is less than the `max_allowed_packet` parameter for the source DB instance\. The `max_allowed_packet` parameter is a custom parameter that you can set in a DB parameter group\. You use `max_allowed_packet` to specify the maximum size of DML code that can be executed on the database\. In some cases, the `max_allowed_packet` value in the DB parameter group associated with a source DB instance is smaller than the `max_allowed_packet` value in the DB parameter group associated with the source's Read Replica\. In these cases, the replication process can throw the error `Packet bigger than 'max_allowed_packet' bytes` and stop replication\. To fix the error, have the source and Read Replica use DB parameter groups with the same `max_allowed_packet` parameter values\. 

Other common situations that can cause replication errors include the following:
+ Writing to tables on a Read Replica\. In some cases, you might create indexes on a Read Replica that are different from the indexes on the source DB instance\. If you do, set the `read_only` parameter to `0` to create the indexes\. If you write to tables on the Read Replica, it might break replication if the Read Replica becomes incompatible with the source DB instance\. After you perform maintenance tasks on the Read Replica, we recommend that you set the `read_only` parameter back to `1`\.
+  Using a non\-transactional storage engine such as MyISAM\. Read replicas require a transactional storage engine\. Replication is only supported for the InnoDB storage engine on MySQL\.
+  Using unsafe nondeterministic queries such as `SYSDATE()`\. For more information, see [Determination of Safe and Unsafe Statements in Binary Logging](http://dev.mysql.com/doc/refman/5.5/en/replication-rbr-safe-unsafe.html)\. 

If you decide that you can safely skip an error, you can follow the steps described in the section [Skipping the Current Replication Error](Appendix.MySQL.CommonDBATasks.md#Appendix.MySQL.CommonDBATasks.SkipError)\. Otherwise, you can first delete the Read Replica\. Then you create an instance using the same DB instance identifier so that the endpoint remains the same as that of your old Read Replica\. If a replication error is fixed, the `Replication State` changes to *replicating*\.