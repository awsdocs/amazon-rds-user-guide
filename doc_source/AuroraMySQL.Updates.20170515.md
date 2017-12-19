# Amazon Aurora MySQL Database Engine Updates: 2017\-05\-15<a name="AuroraMySQL.Updates.20170515"></a>

**Version:** 1\.13

**Note**  
We enabled a new feature – SELECT INTO OUTFILE S3 – in Amazon Aurora MySQL version 1\.13 after the initial release, and have updated the release notes to reflect that change\.

Amazon Aurora MySQL 1\.13 is generally available\. All new database clusters, including those restored from snapshots, will be created in Aurora MySQL v1\.13\. You have the option, but are not required, to upgrade existing database clusters to Aurora MySQL v1\.13\. With version 1\.13 of Aurora, we are using a cluster patching model where all nodes in an Aurora DB cluster are patched at the same time\. We are enabling zero\-downtime patching, which works on a best\-effort basis to preserve client connections through the patching process\. For more information, see [Amazon RDS Maintenance](USER_UpgradeDBInstance.Maintenance.md)\. 

## Zero\-Downtime Patching<a name="AuroraMySQL.Updates.20170515.ZDP"></a>

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

## New Features:<a name="AuroraMySQL.Updates.20170515.NewFeatures"></a>

+ **SELECT INTO OUTFILE S3** – Amazon Aurora MySQL now allows you to upload the results of a query to one or more files in an Amazon S3 bucket\. For more information, see [Saving Data from an Amazon Aurora MySQL DB Cluster into Text Files in an Amazon S3 Bucket](AuroraMySQL.Integrating.SaveIntoS3.md)\.

## Improvements:<a name="AuroraMySQL.Updates.20170515.Improvements"></a>

+ Implemented truncation of CSV format log files at engine startup to avoid long recovery time\. The `general_log_backup`, `general_log`, `slow_log_backup`, and `slow_log` tables now do not survive a database restart\. 

+ Fixed an issue where migration of a database named **test** would fail\.

+ Improved stability in the lock manager’s garbage collector by reusing the correct lock segments\.

+ Improved stability of the lock manager by removing invalid assertions during deadlock detection algorithm\. 

+ Re\-enabled asynchronous replication, and fixed an associated issue which caused incorrect replica lag to be reported under no\-load or read\-only workload\. The replication pipeline improvements that were introduced in version 1\.10\. These improvements were introduced in order to apply log stream updates to the buffer cache of an Aurora Replica\. which helps to improve read performance and stability on Aurora Replicas\.

+ Fixed an issue where autocommit=OFF leads to scheduled events being blocked and long transactions being held open until the server reboots\.

+ Fixed an issue where general, audit, and slow query logs could not log queries handled by asynchronous commit\.

+ Improved the performance of the logical read ahead \(LRA\) feature by up to 2\.5 times\. This was done by allowing pre\-fetches to continue across intermediate pages in a B\-tree\.

+ Added parameter validation for audit variables to trim unnecessary spaces\.

+ Fixed a regression, introduced in Aurora MySQL version 1\.11, in which queries can return incorrect results when using the SQL\_CALC\_FOUND\_ROWS option and invoking the FOUND\_ROWS\(\) function\.

+ Fixed a stability issue when the Metadata Lock list was incorrectly formed\.

+ Improved stability when sql\_mode is set to PAD\_CHAR\_TO\_FULL\_LENGTH and the command `SHOW FUNCTION STATUS WHERE Db='string'` is executed\.

+ Fixed a rare case when instances would not come up after Aurora version upgrade because of a false volume consistency check\.

+ Fixed the performance issue, introduced in Aurora MySQL version 1\.12, where the performance of the Aurora writer was reduced when users have a large number of tables\. 

+ Improved stability issue when the Aurora writer is configured as a binlog slave and the number of connections approaches 16,000\. 

+ Fixed a rare issue where an Aurora Replica could restart when a connection gets blocked waiting for Metadata Lock when running DDL on the Aurora master\. 

## Integration of MySQL Bug Fixes<a name="AuroraMySQL.Updates.20170515.BugFixes"></a>

+ With an empty InnoDB table, it's not possible to decrease the auto\_increment value using an ALTER TABLE statement, even when the table is empty\. \(Bug \#69882\)

+ MATCH\(\) \.\.\. AGAINST queries that use a long string as an argument for AGAINST\(\) could result in an error when run on an InnoDB table with a full\-text search index\. \(Bug \#17640261\)

+ Handling of SQL\_CALC\_FOUND\_ROWS in combination with ORDER BY and LIMIT could lead to incorrect results for FOUND\_ROWS\(\)\. \(Bug \#68458, Bug \# 16383173\)

+ ALTER TABLE does not allow to change nullability of the column if foreign key exists\. \(Bug \#77591\)