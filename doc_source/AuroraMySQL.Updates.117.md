# Amazon Aurora MySQL Database Engine Updates 2018\-03\-13<a name="AuroraMySQL.Updates.117"></a>

**Version:** 1\.17

Amazon Aurora 1\.17 is generally available\. Please note that 1\.x versions are only compatible with MySQL 5\.6, and not MySQL 5\.7\. All new 5\.6\-compatible database clusters, including those restored from snapshots, will be created in Aurora v1\.17\. You have the option, but are not required, to upgrade existing database clusters to Aurora v1\.17\. If you wish to create new DB clusters in Aurora v1\.14\.1, Aurora 1\.15\.1, or Aurora 1\.16, you can do so using the AWS CLI or the Amazon RDS API and specifying the engine version\.

With version 1\.17 of Aurora, we are using a cluster patching model where all nodes in an Aurora DB cluster are patched at the same time\. We support zero\-downtime patching, which works on a best\-effort basis to preserve client connections through the patching process\. For more information, see [Maintaining an Amazon RDS DB Instance](USER_UpgradeDBInstance.Maintenance.md)\. 

Should you have any questions or concerns, the AWS Support Team is available on the community forums and through [AWS Premium Support](http://aws.amazon.com/support)\.

## Zero\-Downtime Patching<a name="AuroraMySQL.Updates.117.ZDP"></a>

The zero\-downtime patching \(ZDP\) attempts, on a *best\-effort* basis, to preserve client connections through an engine patch\. If ZDP executes successfully, application sessions are preserved and the database engine restarts while patching\. The database engine restart can cause a transient \(5 second or so\) drop in throughput\.

ZDP will not execute successfully under the following conditions:
+ Long\-running queries or transactions are in progress
+ Binary logging is enabled or binary log replication is in\-progress
+ Open SSL connections exist
+ Temporary tables or table locks are in use
+ Pending parameter changes exist

If no suitable time window for executing ZDP becomes available because of one or more of these conditions, patching reverts to the standard behavior\.

**Note**  
ZDP applies only to the primary instance of a DB cluster\. ZDP is not applicable to Aurora Replicas\.

## New Features<a name="AuroraMySQL.Updates.117.New"></a>
+ Aurora MySQL now supports lock compression, which optimizes the lock manager’s memory usage\.

## Improvements<a name="AuroraMySQL.Updates.117.Improvements"></a>
+ Fixed an issue predominantly seen on instances with fewer cores where a single core may have 100% CPU utilization even when the database is idle\.
+ Improved the performance of fetching binary logs from Aurora clusters\.
+ Fixed an issue where Aurora Replicas attempt to write table statistics to persistent storage, and crash\.
+ Fixed an issue where query cache did not work as expected on Aurora Replicas\.
+ Fixed a race condition in lock manager that resulted in an engine restart\.
+ Fixed an issue where locks taken by read\-only, auto\-commit transactions resulted in an engine restart\.
+ Fixed an issue where some queries are not written to the audit logs\.
+ Fixed an issue with recovery of certain partition maintenance operations on failover\.

## Integration of MySQL Bug Fixes<a name="AuroraMySQL.Updates.117.BugFixes"></a>
+ `LAST_INSERT_ID` is replicated incorrectly if replication filters are used \(Bug \#69861\)\.
+ QUERY RETURNS DIFFERENT RESULTS DEPENDING ON WHETHER INDEX\_MERGE SETTING \(Bug \#16862316\)\.
+ QUERY PROC RE\-EXECUTE OF STORED ROUTINE, INEFFICIENT QUERY PLAN \(Bug\#16346367\)\.
+ INNODB FTS : ASSERT IN FTS\_CACHE\_APPEND\_DELETED\_DOC\_IDS \(BUG\#18079671\)\.
+ ASSERT RBT\_EMPTY\(INDEX\_CACHE\->WORDS\) IN ALTER TABLE CHANGE COLUMN \(BUG\#17536995\)\.
+ INNODB FULLTEXT SEARCH DOESN'T FIND RECORDS WHEN SAVEPOINTS ARE INVOLVED \(BUG\#17458835\)\.