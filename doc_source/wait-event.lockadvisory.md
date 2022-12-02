# Lock:advisory<a name="wait-event.lockadvisory"></a>

The `Lock:advisory` event occurs when a PostgreSQL application uses a lock to coordinate activity across multiple sessions\.

**Topics**
+ [Relevant engine versions](#wait-event.lockadvisory.context.supported)
+ [Context](#wait-event.lockadvisory.context)
+ [Causes](#wait-event.lockadvisory.causes)
+ [Actions](#wait-event.lockadvisory.actions)

## Relevant engine versions<a name="wait-event.lockadvisory.context.supported"></a>

This wait event information is relevant for RDS for PostgreSQL versions 9\.6 and higher\.

## Context<a name="wait-event.lockadvisory.context"></a>

PostgreSQL advisory locks are application\-level, cooperative locks explicitly locked and unlocked by the user's application code\. An application can use PostgreSQL advisory locks to coordinate activity across multiple sessions\. Unlike regular, object\- or row\-level locks, the application has full control over the lifetime of the lock\. For more information, see [Advisory Locks](https://www.postgresql.org/docs/12/explicit-locking.html#ADVISORY-LOCKS) in the PostgreSQL documentation\.

Advisory locks can be released before a transaction ends or be held by a session across transactions\. This isn't true for implicit, system\-enforced locks, such as an access\-exclusive lock on a table acquired by a `CREATE INDEX` statement\.

For a description of the functions used to acquire \(lock\) and release \(unlock\) advisory locks, see [Advisory Lock Functions](https://www.postgresql.org/docs/current/functions-admin.html#FUNCTIONS-ADVISORY-LOCKS) in the PostgreSQL documentation\.

Advisory locks are implemented on top of the regular PostgreSQL locking system and are visible in the `pg_locks` system view\.

## Causes<a name="wait-event.lockadvisory.causes"></a>

This lock type is exclusively controlled by an application explicitly using it\. Advisory locks that are acquired for each row as part of a query can cause a spike in locks or a long\-term buildup\.

These effects happen when the query is run in a way that acquires locks on more rows than are returned by the query\. The application must eventually release every lock, but if locks are acquired on rows that aren't returned, the application can't find all of the locks\.

The following example is from [Advisory Locks](https://www.postgresql.org/docs/12/explicit-locking.html#ADVISORY-LOCKS) in the PostgreSQL documentation\.

```
SELECT pg_advisory_lock(id) FROM foo WHERE id > 12345 LIMIT 100;
```

In this example, the `LIMIT` clause can only stop the query's output after the rows have already been internally selected and their ID values locked\. This can happen suddenly when a growing data volume causes the planner to choose a different execution plan that wasn't tested during development\. The buildup in this case happens because the application explicitly calls `pg_advisory_unlock` for every ID value that was locked\. However, in this case it can't find the set of locks acquired on rows that weren't returned\. Because the locks are acquired on the session level, they aren't released automatically at the end of the transaction\.

Another possible cause for spikes in blocked lock attempts is unintended conflicts\. In these conflicts, unrelated parts of the application share the same lock ID space by mistake\.

## Actions<a name="wait-event.lockadvisory.actions"></a>

Review application usage of advisory locks and detail where and when in the application flow each type of advisory lock is acquired and released\.

Determine whether a session is acquiring too many locks or a long\-running session isn't releasing locks early enough, leading to a slow buildup of locks\. You can correct a slow buildup of session\-level locks by ending the session using `pg_terminate_backend(pid)`\.  

A client waiting for an advisory lock appears in `pg_stat_activity` with `wait_event_type=Lock` and `wait_event=advisory`\. You can obtain specific lock values by querying the `pg_locks` system view for the same `pid`, looking for `locktype=advisory` and `granted=f`\.

You can then identify the blocking session by querying `pg_locks` for the same advisory lock having `granted=t`, as shown in the following example\.

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

All of the advisory lock API functions have two sets of arguments, either one `bigint` argument or two `integer` arguments:
+ For the API functions with one `bigint` argument, the upper 32 bits are in `pg_locks.classid` and the lower 32 bits are in `pg_locks.objid`\.
+ For the API functions with two `integer` arguments, the first argument is `pg_locks.classid` and the second argument is `pg_locks.objid`\.

The `pg_locks.objsubid` value indicates which API form was used: `1` means one `bigint` argument; `2` means two `integer` arguments\.