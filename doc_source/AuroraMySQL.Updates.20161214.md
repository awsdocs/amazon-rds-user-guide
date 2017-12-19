# Amazon Aurora MySQL Database Engine Updates: 2016\-12\-14<a name="AuroraMySQL.Updates.20161214"></a>

**Version:** 1\.10

## New Features<a name="AuroraMySQL.Updates.20161214.New"></a>

+ **Zero downtime patch** – This feature allows a DB instance to be patched without any downtime\. That is, database upgrades are performed without disconnecting client applications, or rebooting the database\. This approach increases the availability of your Aurora DB clusters during the maintenance window\. Note that temporary data like that in the performance schema is reset during the upgrade process\. This feature applies to service\-delivered patches during a maintenance window as well as user\-initiated patches\. 

  When a patch is initiated, the service ensures there are no open locks, transactions or temporary tables, and then waits for a suitable window during which the database can be patched and restarted\. Application sessions are preserved, although there is a drop in throughput while the patch is in progress \(for approximately 5 seconds\)\. If no suitable window can be found, then patching defaults to the standard patching behavior\.

  Zero downtime patching takes place on a best\-effort basis, subject to certain limitations as described following:

  + This feature is currently applicable for patching single\-node DB clusters or writer instances in multi\-node DB clusters\.

  + SSL connections are not supported in conjunction with this feature\. If there are active SSL connections, Amazon Aurora MySQL won't perform a zero downtime patch, and instead will retry periodically to see if the SSL connections have terminated\. If they have, zero downtime patching proceeds\. If the SSL connections persist after more than a couple seconds, standard patching with downtime proceeds\.

  + The feature is available in Aurora release 1\.10 and beyond\. Going forward, we will identify any releases or patches that can't be applied by using zero downtime patching\.

  + This feature is not applicable if replication based on binary logging is active\.

+ **Spatial indexing ** – Spatial indexing improves query performance on large datasets for queries that use spatial data\. For more information about using spatial indexing, see [Amazon Aurora MySQL and Spatial Data](Aurora.AuroraMySQL.md#Aurora.AuroraMySQL.Spatial)\. 

  This feature is disabled by default and can be activated by enabling Aurora lab mode\. For information, see [Aurora Lab Mode](AuroraMySQL.Updates.md#AuroraMySQL.Updates.LabMode)\. 

+ **Replication pipeline improvements** – Amazon Aurora MySQL now uses an improved mechanism to apply log stream updates to the buffer cache of an Aurora Replica\. This feature improves the read performance and stability on Aurora Replicas when there is a heavy write load on the master as well as a significant read load on the Replica\. This feature is enabled by default\. 

+ **Throughput improvement for workloads with cached reads** – Amazon Aurora MySQL now uses a latch\-free concurrent algorithm to implement read views, which leads to better throughput for read queries served by the buffer cache\. As a result of this and other improvements, Amazon Aurora MySQL can achieve throughput of up to 625K reads per second compared to 164K reads per second by MySQL 5\.7 for a sysbench SELECT\-only workload\. 

+ **Throughput improvement for workloads with hot row contention** – Amazon Aurora MySQL uses a new lock release algorithm that improves performance, particularly when there is hot page contention \(that is, many transactions contending for the rows on the same page\)\. In tests with the TPC\-C benchmark, this can result in up to16x throughput improvement in transactions per minute relative to MySQL 5\.7\. This feature is disabled by default and can be activated by enabling Aurora lab mode\. For information, see [Aurora Lab Mode](AuroraMySQL.Updates.md#AuroraMySQL.Updates.LabMode)\.

## Improvements<a name="AuroraMySQL.Updates.20161214.Improvements"></a>

+ Full\-text search index cache replication speed has been improved by updating the cache only after a read request to an Aurora Replica\. This approach avoids any reads from disk by the replication thread\. 

+ Fixed an issue where dictionary cache invalidation does not work on an Aurora Replica for tables that have a special character in the database name or table name\.

+ Fixed a `STUCK IO` issue during data migration for distributed storage nodes when storage heat management is enabled\.

+ Fixed an issue in the lock manager where an assertion check fails for the transaction lock wait thread when preparing to rollback or commit a transaction\.

+ Fixed an issue when opening a corrupted dictionary table by correctly updating the reference count to the dictionary table entries\.

+ Fixed a bug where the DB cluster minimum read point can be held by slow Aurora Replicas\.

+ Fixed a potential memory leak in the query cache\.

+ Fixed a bug where an Aurora Replica places a row\-level lock on a table when a query is used in an `IF` statement of a stored procedure\.

## Integration of MySQL Bug Fixes<a name="AuroraMySQL.Updates.20161214.BugFixes"></a>

+ UNION of derived tables returns wrong results with '1=0/false'\-clauses\. \(Bug \#69471\)

+ Server crashes in ITEM\_FUNC\_GROUP\_CONCAT::FIX\_FIELDS on 2nd execution of stored procedure\. \(Bug \#20755389\)

+ Avoid MySQL queries from stalling for too long during FTS cache sync to disk by offloading the cache sync task to a separate thread, as soon as the cache size crosses 10% of the total size\. \(Bug \#22516559, \#73816\)