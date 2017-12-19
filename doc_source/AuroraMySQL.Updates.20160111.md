# Amazon Aurora MySQL Database Engine Updates: 2016\-01\-11<a name="AuroraMySQL.Updates.20160111"></a>

**Version:** 1\.5

This update includes the following improvements:

## Improvements<a name="AuroraMySQL.Updates.20160111.Improvements"></a>

+ Fixed a 10 second pause of write operations for idle instances during Aurora storage deployments\.

+ Logical read\-ahead now works when `innodb_file_per_table` is set to `No`\. For more information on logical read\-ahead, see [Amazon Aurora MySQL Database Engine Updates: 2015\-12\-03](AuroraMySQL.Updates.20151203.md)\.

+ Fixed issues with Aurora Replicas reconnecting with the primary instance\. This improvement also fixes an issue when you specify a large value for the `quantity` parameter when testing Aurora Replica failures using fault\-injection queries\. For more information, see [Testing an Aurora Replica Failure](AuroraMySQL.Managing.md#AuroraMySQL.Managing.FaultInjectionQueries.ReplicaFailure)\.

+ Improved monitoring of Aurora Replicas falling behind and restarting\.

+ Fixed an issue that caused an Aurora Replica to lag, become deregistered, and then restart\.

+ Fixed an issue when you run the `show innodb status` command during a deadlock\.

+ Fixed an issue with failovers for larger instances during high write throughput\.

## Integration of MySQL Bug Fixes<a name="AuroraMySQL.Updates.20160111.BugFixes"></a>

+ Addressed incomplete fix in MySQL full text search affecting tables where the database name begins with a digit\. \(Port Bug \#17607956\) 