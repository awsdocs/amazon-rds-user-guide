# Amazon Aurora MySQL Database Engine Updates 2018\-05\-03<a name="AuroraMySQL.Updates.202"></a>

**Version:** 2\.02

Amazon Aurora MySQL 2\.02 is generally available\. Please note that Aurora MySQL 2\.x versions are compatible with MySQL 5\.7 and Aurora MySQL 1\.x versions are compatible with MySQL 5\.6\.

When creating a new Aurora MySQL DB cluster, you have the option of choosing compatibility with either MySQL 5\.7 or MySQL 5\.6\. When restoring a MySQL 5\.6\-compatible snapshot, you have the option of choosing compatibility with either MySQL 5\.7 or MySQL 5\.6\.

You can restore snapshots of Aurora MySQL 1\.14\*, 1\.15\*, 1\.16\*, 1\.17\* and 2\.01\* into Aurora MySQL 2\.02\. You can also perform an in\-place upgrade from Aurora MySQL 2\.01\* to Aurora MySQL 2\.02\.

We do not allow in\-place upgrade of Aurora MySQL 1\.x clusters into Aurora MySQL 2\.02 or restore to Aurora MySQL 2\.02 from an Amazon S3 backup\. We plan to remove these restrictions in a later Aurora MySQL 2\.x release\.

The performance schema is disabled for Aurora MySQL 5\.7\.

Should you have any questions or concerns, the AWS Support Team is available on the community forums and through [AWS Premium Support](http://aws.amazon.com/support)\. For more information, see [Maintaining an Amazon RDS DB Instance](USER_UpgradeDBInstance.Maintenance.md)\.

## Improvements<a name="AuroraMySQL.Updates.202.Improvements"></a>
+ Fixed an issue where an Aurora Writer restarts when running INSERT statements and exploiting the Fast Insert optimization\.
+ Fixed an issue where an Aurora Replica restarts when running ALTER DATABASE statements on the Aurora Replica\.
+ Fixed an issue where an Aurora Replica restarts when running queries on tables that have just been dropped on the Aurora Writer\.
+ Fixed an issue where an Aurora Replica restarts when setting `innodb_adaptive_hash_index` to `OFF` on the Aurora Replica\.
+ Fixed an issue where an Aurora Replica restarts when running TRUNCATE TABLE queries on the Aurora Writer\.
+ Fixed an issue where the Aurora Writer freezes in certain circumstances when running INSERT statements\. On a multi\-node cluster, this can result in a failover\. 
+ Fixed a memory leak associated with setting session variables\.
+ Fixed an issue where the Aurora Writer freezes in certain circumstances associated with purging undo for tables with generated columns\.
+ Fixed an issue where the Aurora Writer can sometimes restart when binary logging is enabled\.

## Integration of MySQL Bug Fixes<a name="AuroraMySQL.Updates.202.BugFixes"></a>
+ Left join returns incorrect results on the outer side \(Bug \#22833364\)\.