# Amazon Aurora MySQL Database Engine Updates 2017\-11\-20<a name="AuroraMySQL.Updates.20171120"></a>

**Version:** 1\.15\.1

Amazon Aurora v1\.15\.1 is generally available\. All new database clusters, including those restored from snapshots, will be created in Aurora v1\.15\.1\. You have the option, but are not required, to upgrade existing DB clusters to Aurora v1\.15\.1\. If you wish to create new DB clusters in Aurora v1\.14\.1, you can do so using the AWS CLI or the Amazon RDS API and specifying the engine version\.

With version 1\.15\.1 of Aurora, we are using a cluster patching model where all nodes in an Aurora DB cluster are patched at the same time\. We are enabling zero\-downtime patching, which works on a best\-effort basis to preserve client connections through the patching process\. For more information, see [Amazon RDS Maintenance](USER_UpgradeDBInstance.Maintenance.md)\.

Should you have any questions or concerns, the AWS Support Team is available on the community forums and through AWS Premium Support at [http://aws\.amazon\.com/support](http://aws.amazon.com/support)\. For more information, see [Amazon RDS Maintenance](USER_UpgradeDBInstance.Maintenance.md)\.

## Zero\-Downtime Patching<a name="AuroraMySQL.Updates.20171120.ZDP"></a>

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

## Improvements<a name="AuroraMySQL.Updates.20171120.Improvements"></a>
+ Fixed an issue in the adaptive segment selector for a read request that would cause it to choose the same segment twice causing a spike in read latency under certain conditions\.
+ Fixed an issue that stems from an optimization in Aurora MySQL for the thread scheduler\. This problem manifests itself into what are spurious errors while writing to the slow log, while the associated queries themselves perform fine\.
+ Fixed an issue with stability of read replicas on large \(> 5 TB\) volumes\.
+ Fixed an issue where worker thread count increases continuously due to a bogus outstanding connection count\.
+ Fixed an issue with table locks that caused long semaphore waits during insert workloads\.
+ Reverted MySQL bug fixes for Full Text Indexes included in release 15\.

## Integration of MySQL Bug Fixes<a name="AuroraMySQL.Updates.20171024.BugFixes"></a>

None