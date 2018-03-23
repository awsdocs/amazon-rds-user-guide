# Amazon Aurora MySQL Database Engine Updates: 2016\-08\-30<a name="AuroraMySQL.Updates.20160830"></a>

**Version:** 1\.7\.0

## New Features<a name="AuroraMySQL.Updates.20160830.New"></a>
+ **NUMA aware scheduler** – The task scheduler for the Amazon Aurora MySQL engine is now Non\-Uniform Memory Access \(NUMA\) aware\. This minimizes cross\-CPU socket contention resulting in improved performance throughput for the `db.r3.8xlarge` DB instance class\.
+ **Parallel read\-ahead operates asynchronously in the background** – Parallel read\-ahead has been revised to improve performance by using a dedicated thread to reduce thread contention\.
+ **Improved index build \(lab mode\)** – The implementation for building secondary indexes now operates by building the index in a bottom\-up fashion, which eliminates unnecessary page splits\. This can reduce the time needed to create an index or rebuild a table\. This feature is disabled by default and can be activated by enabling Aurora lab mode\. For information, see [Aurora Lab Mode](AuroraMySQL.Updates.md#AuroraMySQL.Updates.LabMode)\.

## Improvements<a name="AuroraMySQL.Updates.20160830.Improvements"></a>
+ Fixed an issue where establishing a connection was taking a long time if there was a surge in the number of connections requested for an instance\.
+ Fixed an issue where a crash occurred if ALTER TABLE was run on a partitioned table that did not use InnoDB\.
+ Fixed an issue where heavy write workload can cause a failover\.
+ Fixed an erroneous assertion that caused a failure if RENAME TABLE was run on a partitioned table\.
+ Improved stability when rolling back a transaction during insert\-heavy workload\.
+ Fixed an issue where full\-text search indexes were not viable on an Aurora Replica\.

## Integration of MySQL Bug Fixes<a name="AuroraMySQL.Updates.20160830.BugFixes"></a>
+ Improve scalability by partitioning LOCK\_grant lock\. \(Port WL \#8355\)
+ Opening cursor on SELECT in stored procedure causes segfault\. \(Port Bug\#16499751\)
+ MySQL gives the wrong result with some special usage\. \(Bug \#11751794\)
+ Crash in GET\_SEL\_ARG\_FOR\_KEYPART – caused by patch for bug \#11751794\. \(Bug \#16208709\)
+ Wrong results for a simple query with GROUP BY\. \(Bug \#17909656\)
+ Extra rows on semijoin query with range predicates\. \(Bug \#16221623\)
+ Adding an ORDER BY clause following an IN subquery could cause duplicate rows to be returned\. \(Bug \#16308085\)
+ Crash with explain for a query with loose scan for GROUP BY, MyISAM\. \(Bug \#16222245\)
+ Loose index scan with quoated int predicate returns random data\. \(Bug \#16394084\)
+ If the optimizer was using a loose index scan, the server could exit while attempting to create a temporary table\. \(Bug \#16436567\)
+ COUNT\(DISTINCT\) should not count NULL values, but they were counted when the optimizer used loose index scan\. \(Bug \#17222452\)
+ If a query had both MIN\(\)/MAX\(\) and aggregate\_function\(DISTINCT\) \(for example, SUM\(DISTINCT\)\) and was executed using loose index scan, the result values of MIN\(\)/MAX\(\) were set improperly\. \(Bug \#17217128\)