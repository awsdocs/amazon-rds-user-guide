# Lock:Relation<a name="wait-event.lockrelation"></a>

The `Lock:Relation` event occurs when a query is waiting to acquire a lock on a table or view \(relation\) that's currently locked by another transaction\.

**Topics**
+ [Supported engine versions](#wait-event.lockrelation.context.supported)
+ [Context](#wait-event.lockrelation.context)
+ [Likely causes of increased waits](#wait-event.lockrelation.causes)
+ [Actions](#wait-event.lockrelation.actions)

## Supported engine versions<a name="wait-event.lockrelation.context.supported"></a>

This wait event information is supported for all versions of RDS for PostgreSQL\.

## Context<a name="wait-event.lockrelation.context"></a>

Most PostgreSQL commands implicitly use locks to control concurrent access to data in tables\. You can also use these locks explicitly in your application code with the `LOCK` command\. Many lock modes aren't compatible with each other, and they can block transactions when they're trying to access the same object\. When this happens, RDS for PostgreSQL generates a `Lock:Relation` event\. Some common examples are the following:
+ Exclusive locks such as `ACCESS EXCLUSIVE` can block all concurrent access\. Data definition language \(DDL\) operations such as `DROP TABLE`, `TRUNCATE`, `VACUUM FULL`, and `CLUSTER` acquire `ACCESS EXCLUSIVE` locks implicitly\. `ACCESS EXCLUSIVE` is also the default lock mode for `LOCK TABLE` statements that don't specify a mode explicitly\.
+ Using `CREATE INDEX (without CONCURRENT)` on a table conflicts with data manipulation language \(DML\) statements `UPDATE`, `DELETE`, and `INSERT`, which acquire `ROW EXCLUSIVE` locks\.

For more information about table\-level locks and conflicting lock modes, see [Explicit Locking](https://www.postgresql.org/docs/13/explicit-locking.html) in the PostgreSQL documentation\.

Blocking queries and transactions typically unblock in one of the following ways:
+ Blocking query – The application can cancel the query or the user can end the process\. The engine can also force the query to end because of a session's statement\-timeout or a deadlock detection mechanism\.
+ Blocking transaction – A transaction stops blocking when it runs a `ROLLBACK` or `COMMIT` statement\. Rollbacks also happen automatically when sessions are disconnected by a client or by network issues, or are ended\. Sessions can be ended when the database engine is shut down, when the system is out of memory, and so forth\.

## Likely causes of increased waits<a name="wait-event.lockrelation.causes"></a>

When the `Lock:Relation` event occurs more frequently than normal, it can indicate a performance issue\. Typical causes include the following:

**Increased concurrent sessions with conflicting table locks**  
There might be an increase in the number of concurrent sessions with queries that lock the same table with conflicting locking modes\.

**Maintenance operations**  
Health maintenance operations such as `VACUUM` and `ANALYZE` can significantly increase the number of conflicting locks\. `VACUUM FULL` acquires an `ACCESS EXCLUSIVE` lock, and `ANALYSE` acquires a `SHARE UPDATE EXCLUSIVE` lock\. Both types of locks can cause a `Lock:Relation` wait event\. Application data maintenance operations such as refreshing a materialized view can also increase blocked queries and transactions\.

**Locks on reader instances**  
There might be a conflict between the relation locks held by the writer and readers\. Currently, only `ACCESS EXCLUSIVE` relation locks are replicated to reader instances\. However, the `ACCESS EXCLUSIVE` relation lock will conflict with any `ACCESS SHARE` relation locks held by the reader\. This can cause an increase in lock relation wait events on the reader\. 

## Actions<a name="wait-event.lockrelation.actions"></a>

We recommend different actions depending on the causes of your wait event\.

**Topics**
+ [Reduce the impact of blocking SQL statements](#wait-event.lockrelation.actions.reduce-blocks)
+ [Minimize the effect of maintenance operations](#wait-event.lockrelation.actions.maintenance)

### Reduce the impact of blocking SQL statements<a name="wait-event.lockrelation.actions.reduce-blocks"></a>

To reduce the impact of blocking SQL statements, modify your application code where possible\. Following are two common techniques for reducing blocks:
+ Use the `NOWAIT` option – Some SQL commands, such as `SELECT` and `LOCK` statements, support this option\. The `NOWAIT` directive cancels the lock\-requesting query if the lock can't be acquired immediately\. This technique can help prevent a blocking session from causing a pile\-up of blocked sessions behind it\.

  For example: Assume that transaction A is waiting on a lock held by  transaction B\. Now, if B requests a lock on a table that’s locked by transaction C, transaction A might be blocked until transaction C completes\. But if transaction B uses a `NOWAIT` when it requests the lock on C, it can fail fast and ensure that transaction A doesn't have to wait indefinitely\.
+ Use `SET lock_timeout` – Set a `lock_timeout` value to limit the time a SQL statement waits to acquire a lock on a relation\. If the lock isn't acquired within the timeout specified, the transaction requesting the lock is cancelled\. Set this value at the session level\.

### Minimize the effect of maintenance operations<a name="wait-event.lockrelation.actions.maintenance"></a>

Maintenance operations such as `VACUUM` and `ANALYZE` are important\. We recommend that you don't turn them off because you find `Lock:Relation` wait events related to these maintenance operations\. The following approaches can minimize the effect of these operations:
+ Run maintenance operations manually during off\-peak hours\.
+ To reduce `Lock:Relation` waits caused by autovacuum tasks, perform any needed autovacuum tuning\. For information about tuning autovacuum, see [ Working with PostgreSQL autovacuum on Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Appendix.PostgreSQL.CommonDBATasks.Autovacuum.html) in the * Amazon RDS User Guide*\.