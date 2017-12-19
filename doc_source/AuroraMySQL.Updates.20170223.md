# Amazon Aurora MySQL Database Engine Updates: 2017\-02\-23<a name="AuroraMySQL.Updates.20170223"></a>

**Version:** 1\.11

We will patch all Amazon Aurora MySQL DB clusters with the latest version over a short period following the release\. DB clusters are patched using the legacy procedure with a downtime of about 5\-30 seconds\. 

Patching occurs during the system maintenance window that you have specified for each of your database instances\. You can view or change this window using the AWS Management Console\. For more information, see [Amazon RDS Maintenance](USER_UpgradeDBInstance.Maintenance.md)\.

Alternatively, you can apply the patch immediately in the AWS Management Console by choosing a DB cluster, choosing **Cluster Actions**, and then choosing **Upgrade Now**\.

With version 1\.11 of Aurora MySQL, we are using a cluster patching model where all nodes in an Aurora DB cluster are patched at the same time\.

## New Features<a name="AuroraMySQL.Updates.20170223.New"></a>

+ **MANIFEST option for LOAD DATA FROM S3** – LOAD DATA FROM S3 was released in version 1\.8\. The options for this command have been expanded, and you can now specify a list of files to be loaded into an Aurora DB cluster from Amazon S3 by using a manifest file\. This makes it easy to load data from specific files in one or more locations, as opposed to loading data from a single file by using the FILE option or loading data from multiple files that have the same location and prefix by using the PREFIX option\. The manifest file format is the same as that used by Amazon Redshift\. For more information about using LOAD DATA FROM S3 with the MANIFEST option, see [Using a Manifest to Specify Data Files to Load](AuroraMySQL.Integrating.LoadFromS3.md#AuroraMySQL.Integrating.LoadFromS3.Manifest)\.

+ **Spatial indexing enabled by default** – This feature was released in lab mode in version 1\.10, and is now turned on by default\. Spatial indexing improves query performance on large datasets for queries that use spatial data\. For more information about using spatial indexing, see [Amazon Aurora MySQL and Spatial Data](Aurora.AuroraMySQL.md#Aurora.AuroraMySQL.Spatial)\.

+ **Throughput improvement for workloads with hot row contention** – This feature was released in lab mode in version 1\.10, and is now available outside of lab mode\. Throughput for workloads with hot row contention was improved by changing the lock release algorithm used by Aurora\. This change improves TPC\-C benchmark performance by up to 16x relative to MySQL 5\.7\.

+ **Advanced Auditing timing change** – This feature was released in version 1\.10\.1 to provide a high\-performance facility for auditing database activity\. In this release, the precision of audit log timestamps has been changed from one second to one microsecond\. The more accurate timestamps allow you to better understand when an audit event happened\. For more information about audit, see [Using Advanced Auditing with an Amazon Aurora MySQL DB Cluster](AuroraMySQL.Auditing.md)\.

## Improvements<a name="AuroraMySQL.Updates.20170223.Improvements"></a>

+ Modified the `thread_handling` parameter to prevent you from setting it to anything other than **multiple\-connections\-per\-thread**, which is the only supported model with Aurora’s thread pool\.

+ Fixed an issue caused when you set either the `buffer_pool_size` or the `query_cache_size` parameter to be larger than the DB cluster's total memory\. In this circumstance, Aurora sets the modified parameter to the default value, so the DB cluster can start up and not crash\.

+ Fixed an issue in the query cache where a transaction gets stale read results if the table is invalidated in another transaction\.

+ Fixed an issue where binlog files marked for deletion are removed after a small delay rather than right away\.

+ Fixed an issue where a database created with the name **tmp** is treated as a system database stored on ephemeral storage and not persisted to Aurora distributed storage\.

+ Modified the behavior of SHOW TABLES to exclude certain internal system tables\. This change helps avoid an unnecessary failover caused by mysqldump locking all files listed in SHOW TABLES, which in turn prevents writes on the internal system table, causing the failover\.

+ Fixed an issue where an Aurora Replica incorrectly restarts when creating a temporary table from a query that invokes a function whose argument is a column of an InnoDB table\.

+ Fixed an issue related to a metadata lock conflict in an Aurora Replica node that causes the Aurora Replica to fall behind the primary DB cluster and eventually get restarted\.

+ Fixed a dead latch in the replication pipeline in reader nodes, which causes an Aurora Replica to fall behind and eventually get restarted\.

+ Fixed an issue where an Aurora Replica lags too much with encrypted volumes larger than 1 terabyte \(TB\)\.

+ Improved Aurora Replica dead latch detection by using an improved way to read the system clock time\.

+ Fixed an issue where an Aurora Replica can restart twice instead of once following de\-registration by the writer\.

+ Fixed a slow query performance issue on Aurora Replicas that occurs when transient statistics cause statistics discrepancy on non\-unique index columns\.

+ Fixed an issue where an Aurora Replica can crash when a DDL statement is replicated on the Aurora Replica at the same time that the Aurora Replica is processing a related query\.

+ Changed the replication pipeline improvements that were introduced in version 1\.10 from enabled by default to disabled by default\. These improvements were introduced in order to apply log stream updates to the buffer cache of an Aurora Replica, and although this feature helps to improve read performance and stability on Aurora Replicas, it increases replica lag in certain workloads\.

+ Fixed an issue where the simultaneous occurrence of an ongoing DDL statement and pending Parallel Read Ahead on the same table causes an assertion failure during the commit phase of the DDL transaction\.

+ Enhanced the general log and slow query log to survive DB cluster restart\.

+ Fixed an out\-of\-memory issue for certain long running queries by reducing memory consumption in the ACL module\.

+ Fixed a restart issue that occurs when a table has non\-spatial indexes, there are spatial predicates in the query, the planner chooses to use a non\-spatial index, and the planner incorrectly pushes the spatial condition down to the index\.

+ Fixed an issue where the DB cluster restarts when there is a delete, update, or purge of very large geospatial objects that are stored externally \(like LOBs\)\.

+ Fixed an issue where fault simulation using ALTER SYSTEM SIMULATE … FOR INTERVAL isn't working properly\.

+ Fixed a stability issue caused by an invalid assertion on an incorrect invariant in the lock manager\.

+ Disabled the following two improvements to InnoDB Full\-Text Search that were introduced in version 1\.10 because they introduce stability issues for some demanding workloads:

  +  Updating the cache only after a read request to an Aurora Replica in order to improve full\-text search index cache replication speed\. 

  + Offloading the cache sync task to a separate thread as soon as the cache size crosses 10% of the total size, in order to avoid MySQL queries stalling for too long during FTS cache sync to disk\. \(Bugs \#22516559, \#73816\)\.

## Integration of MySQL Bug Fixes<a name="AuroraMySQL.Updates.20170223.BugFixes"></a>

+ Running ALTER table DROP foreign key simultaneously with another DROP operation causes the table to disappear\. \(Bug \#16095573\)

+ Some INFORMATION\_SCHEMA queries that used ORDER BY did not use a filesort optimization as they did previously\. \(Bug \#16423536\)

+ FOUND\_ROWS \(\) returns the wrong count of rows on a table\. \(Bug \#68458\)

+ The server fails instead of giving an error when too many temp tables are open\. \(Bug \#18948649\)