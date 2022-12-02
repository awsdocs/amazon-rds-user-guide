# Lock:tuple<a name="wait-event.locktuple"></a>

The `Lock:tuple` event occurs when a backend process is waiting to acquire a lock on a tuple\.

**Topics**
+ [Supported engine versions](#wait-event.locktuple.context.supported)
+ [Context](#wait-event.locktuple.context)
+ [Likely causes of increased waits](#wait-event.locktuple.causes)
+ [Actions](#wait-event.locktuple.actions)

## Supported engine versions<a name="wait-event.locktuple.context.supported"></a>

This wait event information is supported for all versions of RDS for PostgreSQL\.

## Context<a name="wait-event.locktuple.context"></a>

The event `Lock:tuple` indicates that a backend is waiting to acquire a lock on a tuple while another backend holds a conflicting lock on the same tuple\. The following table illustrates a scenario in which sessions generate the `Lock:tuple` event\.


|  Time  |  Session 1  |  Session 2  |  Session 3  | 
| --- | --- | --- | --- | 
|  t1  |  Starts a transaction\.  |    |    | 
|  t2  |  Updates row 1\.  |    |    | 
|  t3  |    |  Updates row 1\. The session acquires an exclusive lock on the tuple and then waits for session 1 to release the lock by committing or rolling back\.  |    | 
|  t4  |    |    |  Updates row 1\. The session waits for session 2 to release the exclusive lock on the tuple\.  | 

Or you can simulate this wait event by using the benchmarking tool `pgbench`\. Configure a high number of concurrent sessions to update the same row in a table with a custom SQL file\.

To learn more about conflicting lock modes, see [Explicit Locking](https://www.postgresql.org/docs/current/explicit-locking.html) in the PostgreSQL documentation\. To learn more about `pgbench`, see [pgbench](https://www.postgresql.org/docs/current/pgbench.html) in the PostgreSQL documentation\.

## Likely causes of increased waits<a name="wait-event.locktuple.causes"></a>

When this event appears more than normal, possibly indicating a performance problem, typical causes include the following:
+ A high number of concurrent sessions are trying to acquire a conflicting lock for the same tuple by running `UPDATE` or `DELETE` statements\.
+ Highly concurrent sessions are running a `SELECT` statement using the `FOR UPDATE` or `FOR NO KEY UPDATE` lock modes\.
+ Various factors drive application or connection pools to open more sessions to execute the same operations\. As new sessions are trying to modify the same rows, DB load can spike, and `Lock:tuple` can appear\.

For more information, see [Row\-Level Locks](https://www.postgresql.org/docs/current/explicit-locking.html#LOCKING-ROWS) in the PostgreSQL documentation\.

## Actions<a name="wait-event.locktuple.actions"></a>

We recommend different actions depending on the causes of your wait event\.

**Topics**
+ [Investigate your application logic](#wait-event.locktuple.actions.problem)
+ [Find the blocker session](#wait-event.locktuple.actions.find-blocker)
+ [Reduce concurrency when it is high](#wait-event.locktuple.actions.concurrency)
+ [Troubleshoot bottlenecks](#wait-event.locktuple.actions.bottlenecks)

### Investigate your application logic<a name="wait-event.locktuple.actions.problem"></a>

Find out whether a blocker session has been in the `idle in transaction` state for long time\. If so, consider ending the blocker session as a short\-term solution\. You can use the `pg_terminate_backend` function\. For more information about this function, see [Server Signaling Functions](https://www.postgresql.org/docs/13/functions-admin.html#FUNCTIONS-ADMIN-SIGNAL) in the PostgreSQL documentation\.

For a long\-term solution, do the following:
+ Adjust the application logic\.
+ Use the `idle_in_transaction_session_timeout` parameter\. This parameter ends any session with an open transaction that has been idle for longer than the specified amount of time\. For more information, see [Client Connection Defaults](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-IDLE-IN-TRANSACTION-SESSION-TIMEOUT) in the PostgreSQL documentation\.
+ Use autocommit as much as possible\. For more information, see [SET AUTOCOMMIT](https://www.postgresql.org/docs/current/ecpg-sql-set-autocommit.html) in the PostgreSQL documentation\.

### Find the blocker session<a name="wait-event.locktuple.actions.find-blocker"></a>

While the `Lock:tuple` wait event is occurring, identify the blocker and blocked session by finding out which locks depend on one another\. For more information, see [Lock dependency information](https://wiki.postgresql.org/wiki/Lock_dependency_information) in the PostgreSQL wiki\. 

The following example queries all sessions, filtering on `tuple` and ordering by `wait_time`\.

```
SELECT blocked_locks.pid AS blocked_pid,
         blocking_locks.pid AS blocking_pid,
         blocked_activity.usename AS blocked_user,
         blocking_activity.usename AS blocking_user,
         now() - blocked_activity.xact_start AS blocked_transaction_duration,
         now() - blocking_activity.xact_start AS blocking_transaction_duration,
         concat(blocked_activity.wait_event_type,':',blocked_activity.wait_event) AS blocked_wait_event,
         concat(blocking_activity.wait_event_type,':',blocking_activity.wait_event) AS blocking_wait_event,
         blocked_activity.state AS blocked_state,
         blocking_activity.state AS blocking_state,
         blocked_locks.locktype AS blocked_locktype,
         blocking_locks.locktype AS blocking_locktype,
         blocked_activity.query AS blocked_statement,
         blocking_activity.query AS blocking_statement
    FROM pg_catalog.pg_locks blocked_locks
    JOIN pg_catalog.pg_stat_activity blocked_activity ON blocked_activity.pid = blocked_locks.pid
    JOIN pg_catalog.pg_locks blocking_locks
        ON blocking_locks.locktype = blocked_locks.locktype
        AND blocking_locks.DATABASE IS NOT DISTINCT FROM blocked_locks.DATABASE
        AND blocking_locks.relation IS NOT DISTINCT FROM blocked_locks.relation
        AND blocking_locks.page IS NOT DISTINCT FROM blocked_locks.page
        AND blocking_locks.tuple IS NOT DISTINCT FROM blocked_locks.tuple
        AND blocking_locks.virtualxid IS NOT DISTINCT FROM blocked_locks.virtualxid
        AND blocking_locks.transactionid IS NOT DISTINCT FROM blocked_locks.transactionid
        AND blocking_locks.classid IS NOT DISTINCT FROM blocked_locks.classid
        AND blocking_locks.objid IS NOT DISTINCT FROM blocked_locks.objid
        AND blocking_locks.objsubid IS NOT DISTINCT FROM blocked_locks.objsubid
        AND blocking_locks.pid != blocked_locks.pid
    JOIN pg_catalog.pg_stat_activity blocking_activity ON blocking_activity.pid = blocking_locks.pid
    WHERE NOT blocked_locks.GRANTED;
```

### Reduce concurrency when it is high<a name="wait-event.locktuple.actions.concurrency"></a>

The `Lock:tuple` event might occur constantly, especially in a busy workload time\. In this situation, consider reducing the high concurrency for very busy rows\. Often, just a few rows control a queue or the Boolean logic, which makes these rows very busy\.

You can reduce concurrency by using different approaches based in the business requirement, application logic, and workload type\. For example, you can do the following:
+ Redesign your table and data logic to reduce high concurrency\.
+ Change the application logic to reduce high concurrency at the row level\.
+ Leverage and redesign queries with row\-level locks\.
+ Use the `NOWAIT` clause with retry operations\.
+ Consider using optimistic and hybrid\-locking logic concurrency control\.
+ Consider changing the database isolation level\.

### Troubleshoot bottlenecks<a name="wait-event.locktuple.actions.bottlenecks"></a>

The `Lock:tuple` can occur with bottlenecks such as CPU starvation or maximum usage of Amazon EBS bandwidth\. To reduce bottlenecks, consider the following approaches:
+ Scale up your instance class type\.
+ Optimize resource\-intensive queries\.
+ Change the application logic\.
+ Archive data that is rarely accessed\.