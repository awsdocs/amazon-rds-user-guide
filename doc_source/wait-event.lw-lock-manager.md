# LWLock:lock\_manager \(LWLock:lockmanager\)<a name="wait-event.lw-lock-manager"></a>

This event occurs when the RDS for PostgreSQL engine maintains the shared lock's memory area to allocate, check, and deallocate a lock when a fast path lock isn't possible\.

**Topics**
+ [Supported engine versions](#wait-event.lw-lock-manager.context.supported)
+ [Context](#wait-event.lw-lock-manager.context)
+ [Likely causes of increased waits](#wait-event.lw-lock-manager.causes)
+ [Actions](#wait-event.lw-lock-manager.actions)

## Supported engine versions<a name="wait-event.lw-lock-manager.context.supported"></a>

This wait event information is relevant for RDS for PostgreSQL version 9\.6 and higher\. For RDS for PostgreSQL releases older than version 13, the name of this wait event is `LWLock:lock_manager`\. For RDS for PostgreSQL version 13 and higher, the name of this wait event is `LWLock:lockmanager`\. 

## Context<a name="wait-event.lw-lock-manager.context"></a>

When you issue a SQL statement, RDS for PostgreSQL records locks to protect the structure, data, and integrity of your database during concurrent operations\. The engine can achieve this goal using a fast path lock or a path lock that isn't fast\. A path lock that isn't fast is more expensive and creates more overhead than a fast path lock\.

### Fast path locking<a name="wait-event.lw-lock-manager.context.fast-path"></a>

To reduce the overhead of locks that are taken and released frequently, but that rarely conflict, backend processes can use fast path locking\. The database uses this mechanism for locks that meet the following criteria:
+ They use the DEFAULT lock method\.
+ They represent a lock on a database relation rather than a shared relation\.
+ They are weak locks that are unlikely to conflict\.
+ The engine can quickly verify that no conflicting locks can possibly exist\.

The engine can't use fast path locking when either of the following conditions is true:
+ The lock doesn't meet the preceding criteria\.
+ No more slots are available for the backend process\.

To tune your queries for fast\-path lockcing, you can use the following query\.

```
SELECT count(*), pid, mode, fastpath
  FROM pg_locks
 WHERE fastpath IS NOT NULL
 GROUP BY 4,3,2
 ORDER BY pid, mode;
 count | pid  |      mode       | fastpath
-------+------+-----------------+----------
16 | 9185 | AccessShareLock | t
336 | 9185 | AccessShareLock | f
1 | 9185 | ExclusiveLock   | t
```

The following query shows only the total across the database\.

```
SELECT count(*), mode, fastpath
  FROM pg_locks
 WHERE fastpath IS NOT NULL
 GROUP BY 3,2
 ORDER BY mode,1;
count |      mode       | fastpath
-------+-----------------+----------
16 | AccessShareLock | t
337 | AccessShareLock | f
1 | ExclusiveLock   | t
(3 rows)
```

For more information about fast path locking, see [fast path](https://github.com/postgres/postgres/blob/master/src/backend/storage/lmgr/README#L70-L76) in the PostgreSQL lock manager README and [pg\-locks](https://www.postgresql.org/docs/9.3/view-pg-locks.html#AEN98195) in the PostgreSQL documentation\. 

### Example of a scaling problem for the lock manager<a name="wait-event.lw-lock-manager.context.lock-manager"></a>

In this example, a table named `purchases` stores five years of data, partitioned by day\. Each partition has two indexes\. The following sequence of events occurs:

1. You query many days worth of data, which requires the database to read many partitions\.

1. The database creates a lock entry for each partition\. If partition indexes are part of the optimizer access path, the database creates a lock entry for them, too\.

1. When the number of requested locks entries for the same backend process is higher than 16, which is the value of `FP_LOCK_SLOTS_PER_BACKEND`, the lock manager uses the non–fast path lock method\.

Modern applications might have hundreds of sessions\. If concurrent sessions are querying the parent without proper partition pruning, the database might create hundreds or even thousands of non–fast path locks\. Typically, when this concurrency is higher than the number of vCPUs, the `LWLock:lock_manager` wait event appears\.

**Note**  
The `LWLock:lock_manager` wait event isn't related to the number of partitions or indexes in a database schema\. Instead, it's related to the number of non–fast path locks that the database must control\.

## Likely causes of increased waits<a name="wait-event.lw-lock-manager.causes"></a>

When the `LWLock:lock_manager` wait event occurs more than normal, possibly indicating a performance problem, the most likely causes of sudden spikes are as follows:
+ Concurrent active sessions are running queries that don't use fast path locks\. These sessions also exceed the maximum vCPU\.
+ A large number of concurrent active sessions are accessing a heavily partitioned table\. Each partition has multiple indexes\.
+ The database is experiencing a connection storm\. By default, some applications and connection pool software create more connections when the database is slow\. This practice makes the problem worse\. Tune your connection pool software so that connection storms don't occur\.
+ A large number of sessions query a parent table without pruning partitions\.
+ A data definition language \(DDL\), data manipulation language \(DML\), or a maintenance command exclusively locks either a busy relation or tuples that are frequently accessed or modified\.

## Actions<a name="wait-event.lw-lock-manager.actions"></a>

If the `CPU` wait event occurs, it doesn't necessarily indicate a performance problem\. Respond to this event only when performance degrades and this wait event is dominating DB load\.

**Topics**
+ [Use partition pruning](#wait-event.lw-lock-manager.actions.pruning)
+ [Remove unnecessary indexes](#wait-event.lw-lock-manager.actions.indexes)
+ [Tune your queries for fast path locking](#wait-event.lw-lock-manager.actions.tuning)
+ [Tune for other wait events](#wait-event.lw-lock-manager.actions.other-waits)
+ [Reduce hardware bottlenecks](#wait-event.lw-lock-manager.actions.hw-bottlenecks)
+ [Use a connection pooler](#wait-event.lw-lock-manager.actions.pooler)
+ [Upgrade your RDS for PostgreSQL version](#wait-event.lw-lock-manager.actions.pg-version)

### Use partition pruning<a name="wait-event.lw-lock-manager.actions.pruning"></a>

*Partition pruning* is a query optimization strategy for declaratively partitioned tables that excludes unneeded partitions from table scans, thereby improving performance\. Partition pruning is turned on by default\. If it is turned off, turn it on as follows\.

```
SET enable_partition_pruning = on;
```

Queries can take advantage of partition pruning when their `WHERE` clause contains the column used for the partitioning\. For more information, see [Partition Pruning](https://www.postgresql.org/docs/current/ddl-partitioning.html#DDL-PARTITION-PRUNING) in the PostgreSQL documentation\.

### Remove unnecessary indexes<a name="wait-event.lw-lock-manager.actions.indexes"></a>

Your database might contain unused or rarely used indexes\. If so, consider deleting them\. Do either of the following:
+ Learn how to find unnecessary indexes by reading [Unused Indexes](https://wiki.postgresql.org/wiki/Index_Maintenance#Unused_Indexes) in the PostgreSQL wiki\.
+ Run PG Collector\. This SQL script gathers database information and presents it in a consolidated HTML report\. Check the "Unused indexes" section\. For more information, see [pg\-collector](https://github.com/awslabs/pg-collector) in the AWS Labs GitHub repository\.

### Tune your queries for fast path locking<a name="wait-event.lw-lock-manager.actions.tuning"></a>

To find out whether your queries use fast path locking, query the `fastpath` column in the `pg_locks` table\. If your queries aren't using fast path locking, try to reduce number of relations per query to fewer than 16\.

### Tune for other wait events<a name="wait-event.lw-lock-manager.actions.other-waits"></a>

If `LWLock:lock_manager` is first or second in the list of top waits, check whether the following wait events also appear in the list:
+ `Lock:Relation`
+ `Lock:transactionid`
+ `Lock:tuple`

If the preceding events appear high in the list, consider tuning these wait events first\. These events can be a driver for `LWLock:lock_manager`\.

### Reduce hardware bottlenecks<a name="wait-event.lw-lock-manager.actions.hw-bottlenecks"></a>

You might have a hardware bottleneck, such as CPU starvation or maximum usage of your Amazon EBS bandwidth\. In these cases, consider reducing the hardware bottlenecks\. Consider the following actions:
+ Scale up your instance class\.
+ Optimize queries that consume large amounts of CPU and memory\.
+ Change your application logic\.
+ Archive your data\.

For more information about CPU, memory, and EBS network bandwidth, see [Amazon RDS Instance Types](https://aws.amazon.com/rds/instance-types/)\.

### Use a connection pooler<a name="wait-event.lw-lock-manager.actions.pooler"></a>

If your total number of active connections exceeds the maximum vCPU, more OS processes require CPU than your instance type can support\. In this case, consider using or tuning a connection pool\. For more information about the vCPUs for your instance type, see [Amazon RDS Instance Types](https://aws.amazon.com/rds/instance-types/)\.

For more information about connection pooling, see the following resources:
+ [Using Amazon RDS Proxy](rds-proxy.md)
+ [pgbouncer](http://www.pgbouncer.org/usage.html)
+ [Connection Pools and Data Sources](https://www.postgresql.org/docs/7.4/jdbc-datasource.html) in the *PostgreSQL Documentation*

### Upgrade your RDS for PostgreSQL version<a name="wait-event.lw-lock-manager.actions.pg-version"></a>

If your current version of RDS for PostgreSQL is lower than 12, upgrade to version 12 or higher\. PostgreSQL versions 12 and later have an improved partition mechanism\. For more information about version 12, see [PostgreSQL 12\.0 Release Notes]( https://www.postgresql.org/docs/release/12.0/)\. For more information about upgrading RDS for PostgreSQL, see [Upgrading the PostgreSQL DB engine for Amazon RDS](USER_UpgradeDBInstance.PostgreSQL.md)\.