# Amazon Aurora MySQL Database Engine Updates: 2018\-03\-13<a name="AuroraMySQL.Updates.1144"></a>

**Version:** 1\.14\.4

Amazon Aurora MySQL v1\.14\.4 is generally available\. If you wish to create new DB clusters in Aurora v1\.14\.4, you can do so using the AWS CLI or the Amazon RDS API and specifying the engine version\. You have the option, but are not required, to upgrade existing 1\.14\.x DB clusters to Aurora v1\.14\.4\.

With version 1\.14\.4 of Aurora, we are using a cluster\-patching model where all nodes in an Aurora DB cluster are patched at the same time\. We support zero\-downtime patching, which works on a best\-effort basis to preserve client connections through the patching process\. For more information, see [Maintaining an Amazon RDS DB Instance](USER_UpgradeDBInstance.Maintenance.md)\.

Should you have any questions or concerns, the AWS Support Team is available on the community forums and through AWS Premium Support at [http://aws\.amazon\.com/support](http://aws.amazon.com/support)\. For more information, see [Maintaining an Amazon RDS DB Instance](USER_UpgradeDBInstance.Maintenance.md)\.

## Zero\-Downtime Patching<a name="AuroraMySQL.Updates.1144.ZDP"></a>

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

## New Features<a name="AuroraMySQL.Updates.1144.New"></a>
+ Aurora MySQL now supports db\.r4 instance classes\.

## Improvements<a name="AuroraMySQL.Updates.1144.Improvements"></a>
+ Fixed an issue where `LOST_EVENTS` were generated when writing large binlog events\.

## Integration of MySQL Bug Fixes<a name="AuroraMySQL.Updates.1144.BugFixes"></a>
+ Ignorable events do not work and are not tested \(Bug \#74683\)
+ NEW\->OLD ASSERT FAILURE 'GTID\_MODE > 0' \(Bug \#20436436\)