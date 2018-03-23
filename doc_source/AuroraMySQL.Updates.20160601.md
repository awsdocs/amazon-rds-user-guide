# Amazon Aurora MySQL Database Engine Updates: 2016\-06\-01<a name="AuroraMySQL.Updates.20160601"></a>

**Version:** 1\.6\.5

## New Features<a name="AuroraMySQL.Updates.20160601.New"></a>
+ **Efficient storage of Binary Logs** – Efficient storage of binary logs is now enabled by default for all Amazon Aurora MySQL DB clusters, and is not configurable\. Efficient storage of binary logs was introduced in the April 2016 update\. For more information, see [Amazon Aurora MySQL Database Engine Updates: 2016\-04\-06](AuroraMySQL.Updates.20160406.md)\.

## Improvements<a name="AuroraMySQL.Updates.20160601.Improvements"></a>
+ Improved stability for Aurora Replicas when the primary instance is encountering a heavy workload\. 
+ Improved stability for Aurora Replicas when running queries on partitioned tables and tables with special characters in the table name\. 
+ Fixed connection issues when using secure connections\.

## Integration of MySQL Bug Fixes<a name="AuroraMySQL.Updates.20160601.BugFixes"></a>
+ SLAVE CAN'T CONTINUE REPLICATION AFTER MASTER'S CRASH RECOVERY \(Port Bug \#17632285\) 