# Amazon Aurora MySQL Database Engine Updates 2017\-12\-11<a name="AuroraMySQL.Updates.20171211"></a>

**Version:** 1\.16

Amazon Aurora v1\.16 is generally available\. All new database clusters, including those restored from snapshots, will be created in Aurora v1\.16\. You have the option, but are not required, to upgrade existing database clusters to Aurora v1\.16\. If you wish to create new DB clusters in Aurora v1\.14\.1 or Aurora 1\.15\.1, you can do so using the AWS CLI or the Amazon RDS API and specifying the engine version\.

With version 1\.16 of Aurora, we are using a cluster patching model where all nodes in an Aurora DB cluster are patched at the same time\. We are enabling zero\-downtime patching, which works on a best\-effort basis to preserve client connections through the patching process\. For more information, see [Maintaining an Amazon RDS DB Instance](USER_UpgradeDBInstance.Maintenance.md)\.

Should you have any questions or concerns, the AWS Support Team is available on the community forums and through AWS Premium Support at [http://aws\.amazon\.com/support](http://aws.amazon.com/support)\.

## Zero\-Downtime Patching<a name="AuroraMySQL.Updates.20171211.ZDP"></a>

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

## New Features<a name="AuroraMySQL.Updates.20171211.New"></a>
+ Aurora MySQL now supports synchronous AWS Lambda invocations via the native function `lambda_sync()`\. Also available is native function `lambda_async()`, which can be used as an alternative to the existing stored procedure for asynchronous Lambda invocation\. For more information, see [Invoking a Lambda Function from an Amazon Aurora MySQL DB Cluster](AuroraMySQL.Integrating.Lambda.md)\.
+ Aurora MySQL now supports hash joins to speed up equijoin queries\. Aurora's cost\-based optimizer can automatically decide when to use hash joins; you can also force their use in a query plan\. For more information, see [Working with Hash Joins in Aurora MySQL](AuroraMySQL.BestPractices.md#Aurora.BestPractices.HashJoin)\.
+ Aurora MySQL now supports scan batching to speed up in\-memory scan\-oriented queries significantly\. The feature boosts the performance of table full scans, index full scans, and index range scans by batch processing\.

## Improvements<a name="AuroraMySQL.Updates.20171211.Improvements"></a>
+ Fixed an issue where read replicas crashed when running queries on tables that have just been dropped on the master\. 
+ Fixed an issue where restarting the writer on a database cluster with a very large number of `FULLTEXT` indexes results in longer than expected recovery\.
+ Fixed an issue where flushing binary logs causes `LOST_EVENTS` incidents in binlog events\.
+ Fixed stability issues with the scheduler when performance schema is enabled\.
+ Fixed an issue where a subquery that uses temporary tables could return partial results\.

## Integration of MySQL Bug Fixes<a name="AuroraMySQL.Updates.20171211.BugFixes"></a>

None