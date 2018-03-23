# Amazon Aurora MySQL Database Engine Updates: 2017\-08\-07<a name="AuroraMySQL.Updates.20170807"></a>

**Version:** 1\.14

Amazon Aurora MySQL 1\.14 is generally available\. All new database clusters, including those restored from snapshots, will be created in Aurora MySQL v1\.14\. Aurora MySQL v1\.14 is also a mandatory upgrade for existing Aurora MySQL DB clusters\. We will send a separate announcement with the timeline for deprecating earlier versions of Aurora MySQL\. 

With version 1\.14 of Aurora MySQL, we are using a cluster patching model where all nodes in an Aurora DB cluster are patched at the same time\. Updates require a database restart, so you will experience 20 to 30 seconds of downtime, after which you can resume using your DB cluster or clusters\. If your DB clusters are currently running version 1\.13, Aurora's zero\-downtime patching feature may allow client connections to your Aurora primary instance to persist through the upgrade, depending on your workload\.

Should you have any questions or concerns, the AWS Support Team is available on the community forums and through AWS Premium Support at [http://aws\.amazon\.com/support](http://aws.amazon.com/support)\.

## Zero\-Downtime Patching<a name="AuroraMySQL.Updates.20170807.ZDP"></a>

The zero\-downtime patching \(ZDP\) attempts, on a *best\-effort* basis, to preserve client connections through an engine patch\. If ZDP executes successfully, application sessions are preserved and the database engine restarts while patching\. The database engine restart can cause a transient \(5 second or so\) drop in throughput\.

ZDP will not execute successfully under the following conditions:
+ Long\-running queries are in progress
+ Open long\-running transactions exist
+ Binary logging is enabled
+ Binary log replication is running
+ Pending parameter changes exist
+ Temporary tables are in use
+ Table locks are in use
+ Open SSL connections exist

If no suitable time window for executing ZDP becomes available because of one or more of these conditions, patching reverts to the standard behavior\.

**Note**  
ZDP applies only to the primary instance of an Aurora DB cluster\. ZDP is not applicable to Aurora Replicas\.

## Improvements<a name="AuroraMySQL.Updates.20170807.Improvements"></a>
+ Fixed an incorrect "record not found" error when a record is found in the secondary index but not in the primary index\.
+ Fixed a stability issue that can occur due to a defensive assertion \(added in 1\.12\) that was too strong in the case when an individual write spans over 32 pages\. Such a situation can occur, for instance, with large BLOB values\.
+ Fixed a stability issue because of inconsistencies between the tablespace cache and the dictionary cache\.
+ Fixed an issue in which an Aurora Replica becomes unresponsive after it has exceeded the maximum number of attempts to connect to the primary instance\. An Aurora Replica now restarts if the period of inactivity is more than the heartbeat time period used for health check by the primary instance\.
+ Fixed a livelock that can occur under very high concurrency when one connection tries to acquire an exclusive meta data lock \(MDL\) while issuing a command, such as `ALTER TABLE`\.
+ Fixed a stability issue in an Aurora Read Replica in the presence of logical/parallel read ahead\.
+ Improved `LOAD FROM S3` in two ways:

  1. Better handling of Amazon S3 timeout errors by using the SDK retry in addition to the existing retry\.

  1. Performance optimization when loading very big files or large numbers of files by caching and reusing client state\.
+ Fixed the following stability issues with Fast DDL for `ALTER TABLE` operations:

  1.  When the `ALTER TABLE` statement has multiple `ADD COLUMN` commands and the column names are not in ascending order\. 

  1. When the name string of the column to be updated and its corresponding name string, fetched from the internal system table, differs by a null terminating character \(/0\)\.

  1. Under certain B\-tree split operations\.

  1. When the table has a variable length primary key\.
+ Fixed a stability issue with Aurora Replicas when it takes too long to make its Full Text Search \(FTS\) index cache consistent with that of the primary instance\. This can happen if a large portion of the newly created FTS index entries on the primary instance have not yet been flushed to disk\.
+ Fixed a stability issue that can happen during index creation\.
+ New infrastructure that tracks memory consumption per connection and associated telemetry that will be used for building out Out\-Of\-Memory \(OOM\) avoidance strategies\.
+ Fixed an issue where `ANALYZE TABLE` was incorrectly allowed on Aurora Replicas\. This has now been blocked\.
+ Fixed a stability issue caused by a rare deadlock as a result of a race condition between logical read\-ahead and purge\.

## Integration of MySQL Bug Fixes<a name="AuroraMySQL.Updates.20170807.BugFixes"></a>
+ A full\-text search combined with derived tables \(subqueries in the `FROM` clause\) caused a server exit\. Now, if a full\-text operation depends on a derived table, the server produces an error indicating that a full\-text search cannot be done on a materialized table\. \(Bug \#68751, Bug \#16539903\)