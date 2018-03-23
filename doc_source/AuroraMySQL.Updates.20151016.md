# Amazon Aurora MySQL Database Engine Updates: 2015\-10\-16<a name="AuroraMySQL.Updates.20151016"></a>

**Versions:** 1\.2, 1\.3

This update includes the following improvements:

## Fixes<a name="AuroraMySQL.Updates.20151016.Fixes"></a>
+ Resolved out\-of\-memory issue in the new lock manager with long\-running transactions
+ Resolved security vulnerability when replicating with non\-RDS MySQL databases
+ Updated to ensure that quorum writes retry correctly with storage failures
+ Updated to report replica lag more accurately
+ Improved performance by reducing contention when many concurrent transactions are trying to modify the same row
+ Resolved query cache invalidation for views that are created by joining two tables
+ Disabled query cache for transactions with `UNCOMMITTED_READ` isolation

## Improvements<a name="AuroraMySQL.Updates.20151016.Improvements"></a>
+ Better performance for slow catalog queries on warm caches
+ Improved concurrency in dictionary statistics
+ Better stability for the new query cache resource manager, extent management, files stored in Amazon Aurora smart storage, and batch writes of log records

## Integration of MySQL Bug Fixes<a name="AuroraMySQL.Updates.20151016.BugFixes"></a>
+ Killing a query inside innodb causes it to eventually crash with an assertion\. \(Bug \#1608883\)
+ For failure to create a new thread for the event scheduler, event execution, or new connection, no message was written to the error log\. \(Bug \#16865959\)
+ If one connection changed its default database and simultaneously another connection executed SHOW PROCESSLIST, the second connection could access invalid memory when attempting to display the first connection's default database memory\. \(Bug \#11765252\)
+ PURGE BINARY LOGS by design does not remove binary log files that are in use or active, but did not provide any notice when this occurred\. \(Bug \#13727933\)
+ For some statements, memory leaks could result when the optimizer removed unneeded subquery clauses\. \(Bug \#15875919\) 
+ During shutdown, the server could attempt to lock an uninitialized mutex\. \(Bug \#16016493\)
+ A prepared statement that used GROUP\_CONCAT\(\) and an ORDER BY clause that named multiple columns could cause the server to exit\. \( Bug \#16075310\)
+ Performance Schema instrumentation was missing for slave worker threads\. \(Bug \#16083949\)
+ STOP SLAVE could cause a deadlock when issued concurrently with a statement such as SHOW STATUS that retrieved the values for one or more of the status variables Slave\_retried\_transactions, Slave\_heartbeat\_period, Slave\_received\_heartbeats, Slave\_last\_heartbeat, or Slave\_running\. \(Bug \#16088188\)
+ A full\-text query using Boolean mode could return zero results in some cases where the search term was a quoted phrase\. \(Bug \#16206253\)
+ The optimizer's attempt to remove redundant subquery clauses raised an assertion when executing a prepared statement with a subquery in the ON clause of a join in a subquery\. \(Bug \#16318585\)
+ GROUP\_CONCAT unstable, crash in ITEM\_SUM::CLEAN\_UP\_AFTER\_REMOVAL\. \(Bug \#16347450\)
+ Attempting to replace the default InnoDB full\-text search \(FTS\) stopword list by creating an InnoDB table with the same structure as INFORMATION\_SCHEMA\.INNODB\_FT\_DEFAULT\_STOPWORD would result in an error\. \(Bug \#16373868\)
+ After the client thread on a slave performed a FLUSH TABLES WITH READ LOCK and was followed by some updates on the master, the slave hung when executing SHOW SLAVE STATUS\. \(Bug \#16387720\)
+ When parsing a delimited search string such as "abc\-def" in a full\-text search, InnoDB now uses the same word delimiters as MyISAM\. \(Bug \#16419661\)
+ Crash in FTS\_AST\_TERM\_SET\_WILDCARD\. \(Bug \#16429306\)
+ SEGFAULT in FTS\_AST\_VISIT\(\) for FTS RQG test\. \(Bug \# 16435855\)
+ For debug builds, when the optimizer removed an Item\_ref pointing to a subquery, it caused a server exit\. \(Bug \#16509874\)
+ Full\-text search on InnoDB tables failed on searches for literal phrases combined with \+ or \- operators\. \(Bug \#16516193\)
+ START SLAVE failed when the server was started with the options \-\-master\-info\-repository=TABLE relay\-log\-info\-repository=TABLE and with autocommit set to 0, together with \-\-skip\-slave\-start\. \(Bug \#16533802\)
+ Very large InnoDB full\-text search \(FTS\) results could consume an excessive amount of memory\. \(Bug \#16625973\)
+ In debug builds, an assertion could occur in OPT\_CHECK\_ORDER\_BY when using binary directly in a search string, as binary may include NULL bytes and other non\-meaningful characters\. \(Bug \#16766016\)
+ For some statements, memory leaks could result when the optimizer removed unneeded subquery clauses\. \(Bug \#16807641\)
+ It was possible to cause a deadlock after issuing FLUSH TABLES WITH READ LOCK by issuing STOP SLAVE in a new connection to the slave, then issuing SHOW SLAVE STATUS using the original connection\. \(Bug \#16856735\)
+ GROUP\_CONCAT\(\) with an invalid separator could cause a server exit\. \(Bug \#16870783\)
+ The server did excessive locking on the LOCK\_active\_mi and active\_mi\->rli\->data\_lock mutexes for any SHOW STATUS LIKE 'pattern' statement, even when the pattern did not match status variables that use those mutexes \(Slave\_heartbeat\_period, Slave\_last\_heartbeat, Slave\_received\_heartbeats, Slave\_retried\_transactions, Slave\_running\)\. \(Bug \#16904035\)
+ A full\-text search using the IN BOOLEAN MODE modifier would result in an assertion failure\. \(Bug \#16927092\)
+ Full\-text search on InnoDB tables failed on searches that used the \+ boolean operator\. \(Bug \#17280122\)
+ 4\-way deadlock: zombies, purging binlogs, show processlist, show binlogs\. \(Bug \#17283409\)
+ When an SQL thread which was waiting for a commit lock was killed and restarted it caused a transaction to be skipped on slave\. \(Bug \#17450876\)
+ An InnoDB full\-text search failure would occur due to an "unended" token\. The string and string length should be passed for string comparison\. \(Bug \#17659310\)
+ Large numbers of partitioned InnoDB tables could consume much more memory when used in MySQL 5\.6 or 5\.7 than the memory used by the same tables used in previous releases of the MySQL Server\. \(Bug \#17780517\)
+ For full\-text queries, a failure to check that num\_token is less than max\_proximity\_item could result in an assertion\. \(Bug \#18233051\)
+ Certain queries for the INFORMATION\_SCHEMA TABLES and COLUMNS tables could lead to excessive memory use when there were large numbers of empty InnoDB tables\. \(Bug \#18592390\)
+ When committing a transaction, a flag is now used to check whether a thread has been created, rather than checking the thread itself, which uses more resources, particularly when running the server with master\_info\_repository=TABLE\. \(Bug \#18684222\)
+ If a client thread on a slave executed FLUSH TABLES WITH READ LOCK while the master executed a DML, executing SHOW SLAVE STATUS in the same client became blocked, causing a deadlock\. \(Bug \#19843808\)
+ Ordering by a GROUP\_CONCAT\(\) result could cause a server exit\. \(Bug \#19880368\)