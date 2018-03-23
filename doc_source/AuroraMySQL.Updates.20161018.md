# Amazon Aurora MySQL Database Engine Updates: 2016\-10\-18<a name="AuroraMySQL.Updates.20161018"></a>

**Version:** 1\.8

## New Features<a name="AuroraMySQL.Updates.20161018.New"></a>
+ **AWS Lambda integration** – You can now asynchronously invoke an AWS Lambda function from an Aurora DB cluster using the `mysql.lambda_async` procedure\. For more information, see [Invoking a Lambda Function from an Amazon Aurora MySQL DB Cluster](AuroraMySQL.Integrating.Lambda.md)\.
+ **Load data from Amazon S3** – You can now load text or XML files from an Amazon S3 bucket into your Aurora DB cluster using the `LOAD DATA FROM S3` or `LOAD XML FROM S3` commands\. For more information, see [Loading Data into an Amazon Aurora MySQL DB Cluster from Text Files in an Amazon S3 Bucket](AuroraMySQL.Integrating.LoadFromS3.md)\.
+ **Catalog migration** – Aurora now persists catalog metadata in the cluster volume to support versioning\. This enables seamless catalog migration across versions and restores\.
+ **Cluster\-level maintenance and patching** – Aurora now manages maintenance updates for an entire DB cluster\. For more information, see [Amazon RDS Maintenance](USER_UpgradeDBInstance.Maintenance.md)\.

## Improvements<a name="AuroraMySQL.Updates.20161018.Improvements"></a>
+ Fixed an issue where an Aurora Replica crashes when not granting a metadata lock to an inflight DDL table\.
+ Allowed Aurora Replicas to modify non\-InnoDB tables to facilitate rotation of the slow and general log CSV files where `log_output=TABLE`\.
+ Fixed a lag when updating statistics from the primary instance to an Aurora Replica\. Without this fix, the statistics of the Aurora Replica can get out of sync with the statistics of the primary instance and result in a different \(and possibly under\-performing\) query plan on an Aurora Replica\. 
+ Fixed a race condition that ensures that an Aurora Replica does not acquire locks\.
+ Fixed a rare scenario where an Aurora Replica that registers or de\-registers with the primary instance could fail\. 
+ Fixed a race condition that could lead to a deadlock on `db.r3.large` instances when opening or closing a volume\.
+ Fixed an out\-of\-memory issue that can occur due to a combination of a large write workload and failures in the Aurora Distributed Storage service\.
+ Fixed an issue with high CPU consumption because of the purge thread spinning in the presence of a long\-running transaction\. 
+ Fixed an issue when running information schema queries to get information about locks under heavy load\.
+ Fixed an issue with a diagnostics process that could in rare cases cause Aurora writes to storage nodes to stall and restart/fail\-over\.
+ Fixed a condition where a successfully created table may be deleted during crash recovery if the crash occurred while a `CREATE TABLE [if not exists]` statement was being handled\.
+ Fixed a case where the log rotation procedure is broken when the general log and slow log are not stored on disk using catalog mitigation\.
+ Fixed a crash when a user creates a temporary table within a user defined function, and then uses the user defined function in the select list of the query\.
+ Fixed a crash that occurred when replaying GTID events\. GTID is not supported by Amazon Aurora MySQL\.

## Integration of MySQL Bug Fixes:<a name="AuroraMySQL.Updates.20161018.Fixes"></a>
+ When dropping all indexes on a column with multiple indexes, InnoDB failed to block a DROP INDEX operation when a foreign key constraint requires an index\. \(Bug \#16896810\)
+ Solve add foreign key constraint crash\. \(Bug \#16413976\)
+ Fixed a crash when fetching a cursor in a stored procedure, and analyzing or flushing the table at the same time\. \(Bug \# 18158639\)
+ Fixed an auto\-increment bug when a user alters a table to change the AUTO\_INCREMENT value to less than the maximum auto\-increment column value\. \(Bug \# 16310273\)