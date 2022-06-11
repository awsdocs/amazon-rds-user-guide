# Working with the PostgreSQL autovacuum on Amazon RDS for PostgreSQL<a name="Appendix.PostgreSQL.CommonDBATasks.Autovacuum"></a>

We strongly recommend that you use the autovacuum feature to maintain the health of your PostgreSQL DB instance\. Autovacuum automates the start of the VACUUM and the ANALYZE commands\. It checks for tables with a large number of inserted, updated, or deleted tuples\. After this check, it reclaims storage by removing obsolete data or tuples from the PostgreSQL database\. 

By default, autovacuum is turned on for the Amazon RDS for PostgreSQL DB instances that you create using any of the default PostgreSQL DB parameter groups\. These include `default.postgres10`, `default.postgres11`, and so on\. All default PostgreSQL DB parameter groups have an `rds.adaptive_autovacuum` parameter that's set to `1`, thus activating the feature\. Other configuration parameters associated with the autovacuum feature are also set by default\. Because these defaults are somewhat generic, you can benefit from tuning some of the parameters associated with the autovacuum feature for your specific workload\. 

Following, you can find more information about the autovacuum and how to tune some of its parameters on your RDS for PostgreSQL DB instance\. For high\-level information, see [Best practices for working with PostgreSQL](CHAP_BestPractices.md#CHAP_BestPractices.PostgreSQL)\.

**Topics**
+ [Allocating memory for autovacuum](#Appendix.PostgreSQL.CommonDBATasks.Autovacuum.WorkMemory)
+ [Reducing the likelihood of transaction ID wraparound](#Appendix.PostgreSQL.CommonDBATasks.Autovacuum.AdaptiveAutoVacuuming)
+ [Determining if the tables in your database need vacuuming](#Appendix.PostgreSQL.CommonDBATasks.Autovacuum.NeedVacuuming)
+ [Determining which tables are currently eligible for autovacuum](#Appendix.PostgreSQL.CommonDBATasks.Autovacuum.EligibleTables)
+ [Determining if autovacuum is currently running and for how long](#Appendix.PostgreSQL.CommonDBATasks.Autovacuum.AutovacuumRunning)
+ [Performing a manual vacuum freeze](#Appendix.PostgreSQL.CommonDBATasks.Autovacuum.VacuumFreeze)
+ [Reindexing a table when autovacuum is running](#Appendix.PostgreSQL.CommonDBATasks.Autovacuum.Reindexing)
+ [Other parameters that affect autovacuum](#Appendix.PostgreSQL.CommonDBATasks.Autovacuum.OtherParms)
+ [Setting table\-level autovacuum parameters](#Appendix.PostgreSQL.CommonDBATasks.Autovacuum.TableParameters)
+ [Logging autovacuum and vacuum activities](#Appendix.PostgreSQL.CommonDBATasks.Autovacuum.Logging)

## Allocating memory for autovacuum<a name="Appendix.PostgreSQL.CommonDBATasks.Autovacuum.WorkMemory"></a>

One of the most important parameters influencing autovacuum performance is the [maintenance\_work\_mem](https://www.postgresql.org/docs/current/static/runtime-config-resource.html#GUC-MAINTENANCE-WORK-MEM) parameter\. This parameter determines how much memory that you allocate for autovacuum to use to scan a database table and to hold all the row IDs that are going to be vacuumed\. If you set the value of the `maintenance_work_mem` parameter too low, the vacuum process might have to scan the table multiple times to complete its work\. Such multiple scans can have a negative impact on performance\.

When doing calculations to determine the `maintenance_work_mem` parameter value, keep in mind two things:  
+ The default unit is kilobytes \(KB\) for this parameter\.
+ The `maintenance_work_mem` parameter works in conjunction with the [https://www.postgresql.org/docs/current/static/runtime-config-autovacuum.html#GUC-AUTOVACUUM-MAX-WORKERS](https://www.postgresql.org/docs/current/static/runtime-config-autovacuum.html#GUC-AUTOVACUUM-MAX-WORKERS) parameter\. If you have many small tables, allocate more `autovacuum_max_workers` and less `maintenance_work_mem`\. If you have large tables \(say, larger than 100 GB\), allocate more memory and fewer worker processes\. You need to have enough memory allocated to succeed on your biggest table\. Each `autovacuum_max_workers` can use the memory that you allocate\. Thus, make sure that the combination of worker processes and memory equal the total memory that you want to allocate\.

In general terms, for large hosts set the `maintenance_work_mem` parameter to a value between one and two gigabytes \(between 1,048,576 and 2,097,152 KB\)\. For extremely large hosts, set the parameter to a value between two and four gigabytes \(between 2,097,152 and 4,194,304 KB\)\. The value that you set for this parameter depends on the workload\. Amazon RDS has updated its default for this parameter to be kilobytes calculated as follows\.

 `GREATEST({DBInstanceClassMemory/63963136*1024},65536)`\. 

## Reducing the likelihood of transaction ID wraparound<a name="Appendix.PostgreSQL.CommonDBATasks.Autovacuum.AdaptiveAutoVacuuming"></a>

In some cases, parameter group settings related to autovacuum might not be aggressive enough to prevent transaction ID wraparound\. To address this, RDS for PostgreSQL provides a mechanism that adapts the autovacuum parameter values automatically\. *Adaptive autovacuum parameter tuning* is a feature for RDS for PostgreSQL\. A detailed explanation of [TransactionID wraparound](https://www.postgresql.org/docs/current/static/routine-vacuuming.html#VACUUM-FOR-WRAPAROUND) is found in the PostgreSQL documentation\. 

Adaptive autovacuum parameter tuning is turned on by default for RDS for PostgreSQL instances with the dynamic parameter `rds.adaptive_autovacuum` set to ON\. We strongly recommend that you keep this turned on\. However, to turn off adaptive autovacuum parameter tuning, set the `rds.adaptive_autovacuum` parameter to 0 or OFF\. 

Transaction ID wraparound is still possible even when Amazon RDS tunes the autovacuum parameters\. We encourage you to implement an Amazon CloudWatch alarm for transaction ID wraparound\. For more information, see the post [Implement an early warning system for transaction ID wraparound in RDS for PostgreSQL](http://aws.amazon.com/blogs/database/implement-an-early-warning-system-for-transaction-id-wraparound-in-amazon-rds-for-postgresql/) on the AWS Database Blog\.

With adaptive autovacuum parameter tuning turned on, Amazon RDS begins adjusting autovacuum parameters when the CloudWatch metric `MaximumUsedTransactionIDs` reaches the value of the `autovacuum_freeze_max_age` parameter or 500,000,000, whichever is greater\. 

Amazon RDS continues to adjust parameters for autovacuum if a table continues to trend toward transaction ID wraparound\. Each of these adjustments dedicates more resources to autovacuum to avoid wraparound\. Amazon RDS updates the following autovacuum\-related parameters: 
+ [autovacuum\_vacuum\_cost\_delay](https://www.postgresql.org/docs/current/static/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-COST-DELAY)
+ [ autovacuum\_vacuum\_cost\_limit](https://www.postgresql.org/docs/current/static/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-COST-LIMIT)
+  [autovacuum\_work\_mem](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-AUTOVACUUM-WORK-MEM) 
+  [autovacuum\_naptime](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-NAPTIME) 

RDS modifies these parameters only if the new value makes autovacuum more aggressive\. The parameters are modified in memory on the DB instance\. The values in the parameter group aren't changed\. To view the current in\-memory settings, use the PostgreSQL [SHOW](https://www.postgresql.org/docs/current/sql-show.html) SQL command\. 

When Amazon RDS modifies any of these autovacuum parameters, it generates an event for the affected DB instance\. This event is visible on the AWS Management Console and through the Amazon RDS API\. After the `MaximumUsedTransactionIDs` CloudWatch metric returns below the threshold, Amazon RDS resets the autovacuum\-related parameters in memory back to the values specified in the parameter group\. It then generates another event corresponding to this change\.

## Determining if the tables in your database need vacuuming<a name="Appendix.PostgreSQL.CommonDBATasks.Autovacuum.NeedVacuuming"></a>

You can use the following query to show the number of unvacuumed transactions in a database\. The `datfrozenxid` column of a database's `pg_database` row is a lower bound on the normal transaction IDs appearing in that database\. This column is the minimum of the per\-table `relfrozenxid` values within the database\. 

```
SELECT datname, age(datfrozenxid) FROM pg_database ORDER BY age(datfrozenxid) desc limit 20;
```

For example, the results of running the preceding query might be the following\.

```
datname    | age
mydb       | 1771757888
template0  | 1721757888
template1  | 1721757888
rdsadmin   | 1694008527
postgres   | 1693881061
(5 rows)
```

When the age of a database reaches 2 billion transaction IDs, transaction ID \(XID\) wraparound occurs and the database becomes read\-only\. You can use this query to produce a metric and run a few times a day\. By default, autovacuum is set to keep the age of transactions to no more than 200,000,000 \([https://www.postgresql.org/docs/current/static/runtime-config-autovacuum.html#GUC-AUTOVACUUM-FREEZE-MAX-AGE](https://www.postgresql.org/docs/current/static/runtime-config-autovacuum.html#GUC-AUTOVACUUM-FREEZE-MAX-AGE)\)\.

A sample monitoring strategy might look like this:
+ Set the `autovacuum_freeze_max_age` value to 200 million transactions\.
+ If a table reaches 500 million unvacuumed transactions, that triggers a low\-severity alarm\. This isn't an unreasonable value, but it can indicate that autovacuum isn't keeping up\.
+ If a table ages to 1 billion, this should be treated as an alarm to take action on\. In general, you want to keep ages closer to `autovacuum_freeze_max_age` for performance reasons\. We recommend that you investigate using the recommendations that follow\.
+ If a table reaches 1\.5 billion unvacuumed transactions, that triggers a high\-severity alarm\. Depending on how quickly your database uses transaction IDs, this alarm can indicate that the system is running out of time to run autovacuum\. In this case, we recommend that you resolve this immediately\.

If a table is constantly breaching these thresholds, modify your autovacuum parameters further\. By default, using VACUUM manually \(which has cost\-based delays disabled\) is more aggressive than using the default autovacuum, but it is also more intrusive to the system as a whole\.

We recommend the following:
+ Be aware and turn on a monitoring mechanism so that you are aware of the age of your oldest transactions\.

  For information on creating a process that warns you about transaction ID wraparound, see the AWS Database Blog post [Implement an early warning system for transaction ID wraparound in Amazon RDS for PostgreSQL](http://aws.amazon.com/blogs/database/implement-an-early-warning-system-for-transaction-id-wraparound-in-amazon-rds-for-postgresql/)\.
+ For busier tables, perform a manual vacuum freeze regularly during a maintenance window, in addition to relying on autovacuum\. For information on performing a manual vacuum freeze, see [ Performing a manual vacuum freeze](#Appendix.PostgreSQL.CommonDBATasks.Autovacuum.VacuumFreeze)\.

## Determining which tables are currently eligible for autovacuum<a name="Appendix.PostgreSQL.CommonDBATasks.Autovacuum.EligibleTables"></a>

Often, it is one or two tables in need of vacuuming\. Tables whose `relfrozenxid` value is greater than the number of transactions in `autovacuum_freeze_max_age` are always targeted by autovacuum\. Otherwise, if the number of tuples made obsolete since the last VACUUM exceeds the vacuum threshold, the table is vacuumed\.

The [autovacuum threshold](https://www.postgresql.org/docs/current/static/routine-vacuuming.html#AUTOVACUUM) is defined as:

```
Vacuum-threshold = vacuum-base-threshold + vacuum-scale-factor * number-of-tuples
```

While you are connected to your database, run the following query to see a list of tables that autovacuum sees as eligible for vacuuming\.

```
WITH vbt AS (SELECT setting AS autovacuum_vacuum_threshold FROM 
pg_settings WHERE name = 'autovacuum_vacuum_threshold'),
vsf AS (SELECT setting AS autovacuum_vacuum_scale_factor FROM 
pg_settings WHERE name = 'autovacuum_vacuum_scale_factor'), 
fma AS (SELECT setting AS autovacuum_freeze_max_age FROM pg_settings WHERE name = 'autovacuum_freeze_max_age'),
sto AS (select opt_oid, split_part(setting, '=', 1) as param,
split_part(setting, '=', 2) as value from (select oid opt_oid, unnest(reloptions) setting from pg_class) opt)
SELECT '"'||ns.nspname||'"."'||c.relname||'"' as relation,
pg_size_pretty(pg_table_size(c.oid)) as table_size,
age(relfrozenxid) as xid_age,
coalesce(cfma.value::float, autovacuum_freeze_max_age::float) autovacuum_freeze_max_age,
(coalesce(cvbt.value::float, autovacuum_vacuum_threshold::float) +
coalesce(cvsf.value::float,autovacuum_vacuum_scale_factor::float) * c.reltuples)
AS autovacuum_vacuum_tuples, n_dead_tup as dead_tuples FROM
pg_class c join pg_namespace ns on ns.oid = c.relnamespace 
join pg_stat_all_tables stat on stat.relid = c.oid join vbt on (1=1) join vsf on (1=1) join fma on (1=1)
left join sto cvbt on cvbt.param = 'autovacuum_vacuum_threshold' and c.oid = cvbt.opt_oid 
left join sto cvsf on cvsf.param = 'autovacuum_vacuum_scale_factor' and c.oid = cvsf.opt_oid
left join sto cfma on cfma.param = 'autovacuum_freeze_max_age' and c.oid = cfma.opt_oid
WHERE c.relkind = 'r' and nspname <> 'pg_catalog'
AND (age(relfrozenxid) >= coalesce(cfma.value::float, autovacuum_freeze_max_age::float)
OR coalesce(cvbt.value::float, autovacuum_vacuum_threshold::float) + 
coalesce(cvsf.value::float,autovacuum_vacuum_scale_factor::float) * 
c.reltuples <= n_dead_tup)
ORDER BY age(relfrozenxid) DESC LIMIT 50;
```

## Determining if autovacuum is currently running and for how long<a name="Appendix.PostgreSQL.CommonDBATasks.Autovacuum.AutovacuumRunning"></a>

If you need to manually vacuum a table, make sure to determine if autovacuum is currently running\. If it is, you might need to adjust parameters to make it run more efficiently, or turn off autovacuum temporarily so that you can manually run VACUUM\.

Use the following query to determine if autovacuum is running, how long it has been running, and if it is waiting on another session\. 

```
SELECT datname, usename, pid, state, wait_event, current_timestamp - xact_start AS xact_runtime, query
FROM pg_stat_activity 
WHERE upper(query) LIKE '%VACUUM%' 
ORDER BY xact_start;
```

After running the query, you should see output similar to the following\.

```
 datname | usename  |  pid  | state  | wait_event |      xact_runtime       | query  
 --------+----------+-------+--------+------------+-------------------------+--------------------------------------------------------------------------------------------------------
 mydb    | rdsadmin | 16473 | active |            | 33 days 16:32:11.600656 | autovacuum: VACUUM ANALYZE public.mytable1 (to prevent wraparound)
 mydb    | rdsadmin | 22553 | active |            | 14 days 09:15:34.073141 | autovacuum: VACUUM ANALYZE public.mytable2 (to prevent wraparound)
 mydb    | rdsadmin | 41909 | active |            | 3 days 02:43:54.203349  | autovacuum: VACUUM ANALYZE public.mytable3
 mydb    | rdsadmin |   618 | active |            | 00:00:00                | SELECT datname, usename, pid, state, wait_event, current_timestamp - xact_start AS xact_runtime, query+
         |          |       |        |            |                         | FROM pg_stat_activity                                                                                 +
         |          |       |        |            |                         | WHERE query like '%VACUUM%'                                                                           +
         |          |       |        |            |                         | ORDER BY xact_start;                                                                                  +
```

Several issues can cause a long\-running autovacuum session \(that is, multiple days long\)\. The most common issue is that your [https://www.postgresql.org/docs/current/static/runtime-config-resource.html#GUC-MAINTENANCE-WORK-MEM](https://www.postgresql.org/docs/current/static/runtime-config-resource.html#GUC-MAINTENANCE-WORK-MEM) parameter value is set too low for the size of the table or rate of updates\. 

We recommend that you use the following formula to set the `maintenance_work_mem` parameter value\.

```
GREATEST({DBInstanceClassMemory/63963136*1024},65536)
```

Short running autovacuum sessions can also indicate problems:
+ It can indicate that there aren't enough `autovacuum_max_workers` for your workload\. In this case, you need to indicate the number of workers\.
+ It can indicate that there is an index corruption \(autovacuum crashes and restarts on the same relation but makes no progress\)\. In this case, run a manual `vacuum freeze verbose table` to see the exact cause\. 

## Performing a manual vacuum freeze<a name="Appendix.PostgreSQL.CommonDBATasks.Autovacuum.VacuumFreeze"></a>

You might want to perform a manual vacuum on a table that has a vacuum process already running\. This is useful if you have identified a table with an age approaching 2 billion transactions \(or above any threshold you are monitoring\)\.

The following steps are guidelines, with several variations to the process\. For example, during testing, suppose that you find that the [https://www.postgresql.org/docs/current/static/runtime-config-resource.html#GUC-MAINTENANCE-WORK-MEM](https://www.postgresql.org/docs/current/static/runtime-config-resource.html#GUC-MAINTENANCE-WORK-MEM) parameter value is set too small and that you need to take immediate action on a table\. However, perhaps you don't want to bounce the instance at the moment\. Using the queries in previous sections, you determine which table is the problem and notice a long running autovacuum session\. You know that you need to change the `maintenance_work_mem` parameter setting, but you also need to take immediate action and vacuum the table in question\. The following procedure shows what to do in this situation\.

**To manually perform a vacuum freeze**

1. Open two sessions to the database containing the table you want to vacuum\. For the second session, use "screen" or another utility that maintains the session if your connection is dropped\.

1. In session one, get the process ID \(PID\) of the autovacuum session running on the table\. 

   Run the following query to get the PID of the autovacuum session\.

   ```
   SELECT datname, usename, pid, current_timestamp - xact_start 
   AS xact_runtime, query
   FROM pg_stat_activity WHERE upper(query) LIKE '%VACUUM%' ORDER BY 
   xact_start;
   ```

1. In session two, calculate the amount of memory that you need for this operation\. In this example, we determine that we can afford to use up to 2 GB of memory for this operation, so we set [https://www.postgresql.org/docs/current/static/runtime-config-resource.html#GUC-MAINTENANCE-WORK-MEM](https://www.postgresql.org/docs/current/static/runtime-config-resource.html#GUC-MAINTENANCE-WORK-MEM) for the current session to 2 GB\.

   ```
   SET maintenance_work_mem='2 GB';
   SET
   ```

1. In session two, issue a `vacuum freeze verbose` command for the table\. The verbose setting is useful because, although there is no progress report for this in PostgreSQL currently, you can see activity\.

   ```
   \timing on
   Timing is on.
   vacuum freeze verbose pgbench_branches;
   ```

   ```
   INFO:  vacuuming "public.pgbench_branches"
   INFO:  index "pgbench_branches_pkey" now contains 50 row versions in 2 pages
   DETAIL:  0 index row versions were removed.
   0 index pages have been deleted, 0 are currently reusable.
   CPU 0.00s/0.00u sec elapsed 0.00 sec.
   INFO:  index "pgbench_branches_test_index" now contains 50 row versions in 2 pages
   DETAIL:  0 index row versions were removed.
   0 index pages have been deleted, 0 are currently reusable.
   CPU 0.00s/0.00u sec elapsed 0.00 sec.
   INFO:  "pgbench_branches": found 0 removable, 50 nonremovable row versions 
        in 43 out of 43 pages
   DETAIL:  0 dead row versions cannot be removed yet.
   There were 9347 unused item pointers.
   0 pages are entirely empty.
   CPU 0.00s/0.00u sec elapsed 0.00 sec.
   VACUUM
   Time: 2.765 ms
   ```

1. In session one, if autovacuum was blocking the vacuum session, you see in `pg_stat_activity` that waiting is "T" for your vacuum session\. In this case, you need to end the autovacuum process as follows\.

   ```
   SELECT pg_terminate_backend('the_pid'); 
   ```

   At this point, your session begins\. It's important to note that autovacuum restarts immediately because this table is probably the highest on its list of work\. 

1. Initiate your `vacuum freeze verbose` command in session two, and then end the autovacuum process in session one\.

## Reindexing a table when autovacuum is running<a name="Appendix.PostgreSQL.CommonDBATasks.Autovacuum.Reindexing"></a>

If an index has become corrupt, autovacuum continues to process the table and fails\. If you attempt a manual vacuum in this situation, you receive an error message like the following\.

```
postgres=>  vacuum freeze pgbench_branches;
ERROR: index "pgbench_branches_test_index" contains unexpected 
   zero page at block 30521
HINT: Please REINDEX it.
```

When the index is corrupted and autovacuum is attempting to run on the table, you contend with an already running autovacuum session\. When you issue a [REINDEX](https://www.postgresql.org/docs/current/static/sql-reindex.html) command, you take out an exclusive lock on the table\. Write operations are blocked, and also read operations that use that specific index\.

**To reindex a table when autovacuum is running on the table**

1. Open two sessions to the database containing the table that you want to vacuum\. For the second session, use "screen" or another utility that maintains the session if your connection is dropped\.

1. In session one, get the PID of the autovacuum session running on the table\.

   Run the following query to get the PID of the autovacuum session\.

   ```
   SELECT datname, usename, pid, current_timestamp - xact_start 
   AS xact_runtime, query
   FROM pg_stat_activity WHERE upper(query) like '%VACUUM%' ORDER BY 
   xact_start;
   ```

1. In session two, issue the reindex command\.

   ```
   \timing on
   Timing is on.
   reindex index pgbench_branches_test_index;
   REINDEX
     Time: 9.966 ms
   ```

1. In session one, if autovacuum was blocking the process, you see in `pg_stat_activity` that waiting is "T" for your vacuum session\. In this case, you end the autovacuum process\. 

   ```
   SELECT pg_terminate_backend('the_pid');
   ```

   At this point, your session begins\. It's important to note that autovacuum restarts immediately because this table is probably the highest on its list of work\. 

1. Initiate your command in session two, and then end the autovacuum process in session 1\.

## Other parameters that affect autovacuum<a name="Appendix.PostgreSQL.CommonDBATasks.Autovacuum.OtherParms"></a>

The following query shows the values of some of the parameters that directly affect autovacuum and its behavior\. The [autovacuum parameters](https://www.postgresql.org/docs/current/static/runtime-config-autovacuum.html) are described fully in the PostgreSQL documentation\.

```
SELECT name, setting, unit, short_desc
FROM pg_settings
WHERE name IN (
'autovacuum_max_workers',
'autovacuum_analyze_scale_factor',
'autovacuum_naptime',
'autovacuum_analyze_threshold',
'autovacuum_analyze_scale_factor',
'autovacuum_vacuum_threshold',
'autovacuum_vacuum_scale_factor',
'autovacuum_vacuum_threshold',
'autovacuum_vacuum_cost_delay',
'autovacuum_vacuum_cost_limit',
'vacuum_cost_limit',
'autovacuum_freeze_max_age',
'maintenance_work_mem',
'vacuum_freeze_min_age');
```

While these all affect autovacuum, some of the most important ones are:
+ [maintenance\_work\_mem](https://www.postgresql.org/docs/current/static/runtime-config-resource.html#GUC-MAINTENANCE_WORK_MEM)
+ [autovacuum\_freeze\_max\_age](https://www.postgresql.org/docs/current/static/runtime-config-autovacuum.html#GUC-AUTOVACUUM-FREEZE-MAX-AGE)
+ [autovacuum\_max\_workers](https://www.postgresql.org/docs/current/static/runtime-config-autovacuum.html#GUC-AUTOVACUUM-MAX-WORKERS)
+ [autovacuum\_vacuum\_cost\_delay](https://www.postgresql.org/docs/current/static/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-COST-DELAY)
+ [ autovacuum\_vacuum\_cost\_limit](https://www.postgresql.org/docs/current/static/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-COST-LIMIT)

## Setting table\-level autovacuum parameters<a name="Appendix.PostgreSQL.CommonDBATasks.Autovacuum.TableParameters"></a>

You can set autovacuum\-related [storage parameters](https://www.postgresql.org/docs/current/static/sql-createtable.html#SQL-CREATETABLE-STORAGE-PARAMETERS) at a table level, which can be better than altering the behavior of the entire database\. For large tables, you might need to set aggressive settings and you might not want to make autovacuum behave that way for all tables\.

The following query shows which tables currently have table\-level options in place\.

```
SELECT relname, reloptions
FROM pg_class
WHERE reloptions IS NOT null;
```

An example where this might be useful is on tables that are much larger than the rest of your tables\. Suppose that you have one 300\-GB table and 30 other tables less than 1 GB\. In this case, you might set some specific parameters for your large table so you don't alter the behavior of your entire system\.

```
ALTER TABLE mytable set (autovacuum_vacuum_cost_delay=0);
```

Doing this turns off the cost\-based autovacuum delay for this table at the expense of more resource usage on your system\. Normally, autovacuum pauses for `autovacuum_vacuum_cost_delay` each time `autovacuum_cost_limit` is reached\. For more details, see the PostgreSQL documentation about [cost\-based vacuuming](https://www.postgresql.org/docs/current/static/runtime-config-resource.html#RUNTIME-CONFIG-RESOURCE-VACUUM-COST)\.

## Logging autovacuum and vacuum activities<a name="Appendix.PostgreSQL.CommonDBATasks.Autovacuum.Logging"></a>

Information about autovacuum activities is sent to the `postgresql.log` based on the level specified in the `rds.force_autovacuum_logging_level` parameter\. Following are the values allowed for this parameter and the PostgreSQL versions for which that value is the default setting:
+ `disabled` \(PostgreSQL 10, PostgreSQL 9\.6\)
+ `debug5`, `debug4`, `debug3`, `debug2`, `debug1`
+ `info` \(PostgreSQL 12, PostgreSQL 11\)
+ `notice`
+ `warning` \(PostgreSQL 14, PostgreSQL 13\)
+ `error`, log, `fatal`, `panic`

The `rds.force_autovacuum_logging_level` works with the `log_autovacuum_min_duration` parameter\. The `log_autovacuum_min_duration` parameter's value is the threshold \(in milliseconds\) above which autovacuum actions get logged\. A setting of `-1` logs nothing, while a setting of 0 logs all actions\. As with `rds.force_autovacuum_logging_level`, default values for `log_autovacuum_min_duration` are version dependent, as follows: 
+ `10000 ms` – PostgreSQL 14, PostgreSQL 13, PostgreSQL 12, and PostgreSQL 11 
+ `(empty)` – No default value for PostgreSQL 10 and PostgreSQL 9\.6

We recommend that you set `rds.force_autovacuum_logging_level` to `WARNING`\. We also recommend that you set `log_autovacuum_min_duration` to a value from 1000 to 5000\. A setting of 5000 logs activity that takes longer than 5,000 milliseconds\. Any setting other than \-1 also logs messages if the autovacuum action is skipped because of a conflicting lock or concurrently dropped relations\. For more information, see [Automatic Vacuuming](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html) in the PostgreSQL documentation\. 

To troubleshoot issues, you can change the `rds.force_autovacuum_logging_level` parameter to one of the debug levels, from `debug1` up to `debug5` for the most verbose information\. We recommend that you use debug settings for short periods of time and for troubleshooting purposes only\. To learn more, see [When to log](https://www.postgresql.org/docs/current/static/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHEN) in the PostgreSQL documentation\. 

**Note**  
PostgreSQL allows the `rds_superuser` account to view autovacuum sessions in `pg_stat_activity`\. For example, you can identify and end an autovacuum session that is blocking a command from running, or running slower than a manually issued vacuum command\.