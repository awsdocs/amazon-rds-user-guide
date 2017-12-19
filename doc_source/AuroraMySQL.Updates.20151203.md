# Amazon Aurora MySQL Database Engine Updates: 2015\-12\-03<a name="AuroraMySQL.Updates.20151203"></a>

**Version:** 1\.4

This update includes the following improvements:

## New Features<a name="AuroraMySQL.Updates.20151203.New"></a>

+ **Fast Insert** – Accelerates parallel inserts sorted by primary key\. For more information, see [Amazon Aurora Performance Enhancements](Aurora.Overview.md#Aurora.Overview.Performance)\.

+ **Large dataset read performance** – Amazon Aurora MySQL automatically detects an IO heavy workload and launches more threads in order to boost the performance of the DB cluster\. The Aurora scheduler looks into IO activity and decides to dynamically adjust the optimal number of threads in the system, quickly adjusting between IO heavy and CPU heavy workloads with low overhead\.

+ **Parallel read\-ahead** – Improves the performance of B\-Tree scans that are too large for the memory available on your primary instance or Aurora Replica \(including range queries\)\. Parallel read\-ahead automatically detects page read patterns and pre\-fetches pages into the buffer cache in advance of when they are needed\. Parallel read\-ahead works multiple tables at the same time within the same transaction\.

## Improvements:<a name="AuroraMySQL.Updates.20151203.Improvements"></a>

+ Fixed brief Aurora database availability issues during Aurora storage deployments\. 

+ Correctly enforce the `max_connection` limit\.

+ Improve binlog purging where Aurora is the binlog master and the database is restarting after a heavy data load\. 

+ Fixed memory management issues with the table cache\. 

+ Add support for huge pages in shared memory buffer cache for faster recovery\. 

+ Fixed an issue with thread\-local storage not being initialized\. 

+ Allow 16K connections by default\. 

+ Dynamic thread pool for IO heavy workloads\. 

+ Fixed an issue with properly invalidating views involving UNION in the query cache\. 

+ Fixed a stability issue with the dictionary stats thread\. 

+ Fixed a memory leak in the dictionary subsystem related to cache eviction\. 

+ Fixed high read latency issue on Aurora Replicas when there is very low write load on the master\. 

+ Fixed stability issues on Aurora Replicas when performing operations on DDL partitioned tables such as ALTER TABLE \.\.\. REORGANIZE PARTITION on the master\. 

+ Fixed stability issues on Aurora Replicas during volume growth\. 

+ Fixed performance issue for scans on non\-clustered indexes in Aurora Replicas\. 

+ Fix stability issue that makes Aurora Replicas lag and eventually get deregistered and re\-started\. 

## Integration of MySQL Bug Fixes<a name="AuroraMySQL.Updates.20151203.BugFixes"></a>

+ SEGV in FTSPARSE\(\)\. \(Bug \#16446108\)

+ InnoDB data dictionary is not updated while renaming the column\. \(Bug \#19465984\)

+ FTS crash after renaming table to different database\. \(Bug \#16834860\)

+ Failed preparing of trigger on truncated tables cause error 1054\. \(Bug \#18596756\)

+ Metadata changes might cause problems with trigger execution\. \(Bug \#18684393\)

+ Materialization is not chosen for long UTF8 VARCHAR field\. \(Bug \#17566396\)

+ Poor execution plan when ORDER BY with limit X\. \(Bug \#16697792\)

+ Backport bug \#11765744 TO 5\.1, 5\.5 AND 5\.6\. \(Bug \#17083851\)

+ Mutex issue in SQL/SQL\_SHOW\.CC resulting in SIG6\. Source likely FILL\_VARIABLES\. \(Bug \#20788853\)

+ Backport bug \#18008907 to 5\.5\+ versions\. \(Bug \#18903155\)

+ Adapt fix for a stack overflow error in MySQL 5\.7\. \(Bug \#19678930\)