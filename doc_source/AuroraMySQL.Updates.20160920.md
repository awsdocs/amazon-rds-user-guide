# Amazon Aurora MySQL Database Engine Updates: 2016\-09\-20<a name="AuroraMySQL.Updates.20160920"></a>

**Version:** 1\.7\.1

## Improvements<a name="AuroraMySQL.Updates.20160920.Improvements"></a>

+ Fixes an issue where an Aurora Replica crashes if the InnoDB full\-text search cache is full\.

+ Fixes an issue where the database engine crashes if a worker thread in the thread pool waits for itself\.

+ Fixes an issue where an Aurora Replica crashes if a metadata lock on a table causes a deadlock\.

+ Fixes an issue where the database engine crashes due to a race condition between two worker threads in the thread pool\.

+ Fixes an issue where an unnecessary failover occurs under heavy load if the monitoring agent doesn't detect the advancement of write operations to the distributed storage subsystem\.