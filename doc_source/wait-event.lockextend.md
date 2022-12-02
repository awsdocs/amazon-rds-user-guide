# Lock:extend<a name="wait-event.lockextend"></a>

The `Lock:extend` event occurs when a backend process is waiting to lock a relation to extend it while another process has a lock on that relation for the same purpose\.

**Topics**
+ [Supported engine versions](#wait-event.lockextend.context.supported)
+ [Context](#wait-event.lockextend.context)
+ [Likely causes of increased waits](#wait-event.lockextend.causes)
+ [Actions](#wait-event.lockextend.actions)

## Supported engine versions<a name="wait-event.lockextend.context.supported"></a>

This wait event information is supported for all versions of RDS for PostgreSQL\.

## Context<a name="wait-event.lockextend.context"></a>

The event `Lock:extend` indicates that a backend process is waiting to extend a relation that another backend process holds a lock on while it's extending that relation\. Because only one process at a time can extend a relation, the system generates a `Lock:extend` wait event\. `INSERT`, `COPY`, and `UPDATE` operations can generate this event\.

## Likely causes of increased waits<a name="wait-event.lockextend.causes"></a>

When the `Lock:extend` event appears more than normal, possibly indicating a performance problem, typical causes include the following:

**Surge in concurrent inserts or updates to the same table **  
There might be an increase in the number of concurrent sessions with queries that insert into or update the same table\.

**Insufficient network bandwidth**  
The network bandwidth on the DB instance might be insufficient for the storage communication needs of the current workload\. This can contribute to storage latency that causes an increase in `Lock:extend` events\.

## Actions<a name="wait-event.lockextend.actions"></a>

We recommend different actions depending on the causes of your wait event\.

**Topics**
+ [Reduce concurrent inserts and updates to the same relation](#wait-event.lockextend.actions.action1)
+ [Increase network bandwidth](#wait-event.lockextend.actions.increase-network-bandwidth)

### Reduce concurrent inserts and updates to the same relation<a name="wait-event.lockextend.actions.action1"></a>

First, determine whether there's an increase in `tup_inserted` and `tup_updated` metrics and an accompanying increase in this wait event\. If so, check which relations are in high contention for insert and update operations\. To determine this, query the `pg_stat_all_tables` view for the values in `n_tup_ins` and `n_tup_upd` fields\. For information about the `pg_stat_all_tables` view, see [pg\_stat\_all\_tables](https://www.postgresql.org/docs/13/monitoring-stats.html#MONITORING-PG-STAT-ALL-TABLES-VIEW) in the PostgreSQL documentation\. 

To get more information about blocking and blocked queries, query `pg_stat_activity` as in the following example:

```
SELECT
    blocked.pid,
    blocked.usename,
    blocked.query,
    blocking.pid AS blocking_id,
    blocking.query AS blocking_query,
    blocking.wait_event AS blocking_wait_event,
    blocking.wait_event_type AS blocking_wait_event_type
FROM pg_stat_activity AS blocked
JOIN pg_stat_activity AS blocking ON blocking.pid = ANY(pg_blocking_pids(blocked.pid))
where
blocked.wait_event = 'extend'
and blocked.wait_event_type = 'Lock';
 
   pid  | usename  |            query             | blocking_id |                         blocking_query                           | blocking_wait_event | blocking_wait_event_type
  ------+----------+------------------------------+-------------+------------------------------------------------------------------+---------------------+--------------------------
   7143 |  myuser  | insert into tab1 values (1); |        4600 | INSERT INTO tab1 (a) SELECT s FROM generate_series(1,1000000) s; | DataFileExtend      | IO
```

After you identify relations that contribute to increase `Lock:extend` events, use the following techniques to reduce the contention:
+ Find out whether you can use partitioning to reduce contention for the same table\. Separating inserted or updated tuples into different partitions can reduce contention\. For information about partitioning, see [Managing PostgreSQL partitions with the pg\_partman extension](PostgreSQL_Partitions.md)\.
+ If the wait event is mainly due to update activity, consider reducing the relation's fillfactor value\. This can reduce requests for new blocks during the update\. The fillfactor is a storage parameter for a table that determines the maximum amount of space for packing a table page\. It's expressed as a percentage of the total space for a page\. For more information about the fillfactor parameter, see [CREATE TABLE](https://www.postgresql.org/docs/13/sql-createtable.html) in the PostgreSQL documentation\. 
**Important**  
We highly recommend that you test your system if you change the fillfactor because changing this value can negatively impact performance, depending on your workload\.

### Increase network bandwidth<a name="wait-event.lockextend.actions.increase-network-bandwidth"></a>

To see whether there's an increase in write latency, check the `WriteLatency` metric in CloudWatch\. If there is, use the `WriteThroughput` and `ReadThroughput` Amazon CloudWatch metrics to monitor the storage related traffic on the DB instance\. These metrics can help you to determine if network bandwidth is sufficient for the storage activity of your workload\.

If your network bandwidth isn't enough, increase it\. If your DB instance is reaching the network bandwidth limits, the only way to increase the bandwidth is to increase your DB instance size\.

For more information about CloudWatch metrics, see [Amazon CloudWatch instance\-level metrics for Amazon RDS](rds-metrics.md#rds-cw-metrics-instance)\. For information about network performance for each DB instance class, see [Amazon CloudWatch instance\-level metrics for Amazon RDS](rds-metrics.md#rds-cw-metrics-instance)\. 