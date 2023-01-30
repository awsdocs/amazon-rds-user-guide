# MariaDB feature support on Amazon RDS<a name="MariaDB.Concepts.FeatureSupport"></a>

RDS for MariaDB supports most of the features and capabilities of MariaDB\. Some features might have limited support or restricted privileges\.

You can filter new Amazon RDS features on the [What's New with Database?](http://aws.amazon.com/about-aws/whats-new/database/) page\. For **Products**, choose **Amazon RDS**\. Then search using keywords such as **MariaDB 2022**\.

**Note**  
The following lists are not exhaustive\.

**Topics**
+ [MariaDB feature support on Amazon RDS for MariaDB major versions](#MariaDB.Concepts.FeatureSupport.MajorVersions)
+ [Supported storage engines for MariaDB on Amazon RDS](#MariaDB.Concepts.Storage)
+ [Cache warming for MariaDB on Amazon RDS](#MariaDB.Concepts.XtraDBCacheWarming)
+ [MariaDB features not supported by Amazon RDS](#MariaDB.Concepts.FeatureNonSupport)

## MariaDB feature support on Amazon RDS for MariaDB major versions<a name="MariaDB.Concepts.FeatureSupport.MajorVersions"></a>

In the following sections, find information about MariaDB feature support on Amazon RDS for MariaDB major versions:

**Topics**
+ [MariaDB 10\.6 support on Amazon RDS](#MariaDB.Concepts.FeatureSupport.10-6)
+ [MariaDB 10\.5 support on Amazon RDS](#MariaDB.Concepts.FeatureSupport.10-5)
+ [MariaDB 10\.4 support on Amazon RDS](#MariaDB.Concepts.FeatureSupport.10-4)
+ [MariaDB 10\.3 support on Amazon RDS](#MariaDB.Concepts.FeatureSupport.10-3)

For information about supported minor versions of Amazon RDS for MariaDB, see [MariaDB on Amazon RDS versions](MariaDB.Concepts.VersionMgmt.md)\.

### MariaDB 10\.6 support on Amazon RDS<a name="MariaDB.Concepts.FeatureSupport.10-6"></a>

Amazon RDS supports the following new features for your DB instances running MariaDB version 10\.6 or higher: 
+ **MyRocks storage engine** – You can use the MyRocks storage engine with RDS for MariaDB to optimize storage consumption of your write\-intensive, high\-performance web applications\. For more information, see [Supported storage engines for MariaDB on Amazon RDS](#MariaDB.Concepts.Storage) and [MyRocks](https://mariadb.com/kb/en/myrocks/)\.
+ **AWS Identity and Access Management \(IAM\) DB authentication** – You can use IAM DB authentication for better security and central management of connections to your MariaDB DB instances\. For more information, see [IAM database authentication for MariaDB, MySQL, and PostgreSQL](UsingWithRDS.IAMDBAuth.md)\. 
+ **Upgrade options** – You can now upgrade to RDS for MariaDB version 10\.6 from any prior major release \(10\.3, 10\.4, 10\.5\)\. You can also restore a snapshot of an existing MySQL 5\.6 or 5\.7 DB instance to a MariaDB 10\.6 instance\. For more information, see [Upgrading the MariaDB DB engine](USER_UpgradeDBInstance.MariaDB.md)\.
+ **Delayed replication** – You can now set a configurable time period for which a read replica lags behind the source database\. In a standard MariaDB replication configuration, there is minimal replication delay between the source and the replica\. With delayed replication, you can set an intentional delay as a strategy for disaster recovery\. For more information, see [Configuring delayed replication with MariaDB](USER_MariaDB.Replication.ReadReplicas.md#USER_MariaDB.Replication.ReadReplicas.DelayReplication)\.
+ **Oracle PL/SQL compatibility** – By using RDS for MariaDB version 10\.6, you can more easily migrate your legacy Oracle applications to Amazon RDS\. For more information, see [SQL\_MODE=ORACLE](https://mariadb.com/kb/en/sql_modeoracle/)\.
+ **Atomic DDL** – Your dynamic data language \(DDL\) statements can be relatively crash\-safe with RDS for MariaDB version 10\.6\. `CREATE TABLE`, `ALTER TABLE`, `RENAME TABLE`, `DROP TABLE`, `DROP DATABASE` and related DDL statements are now atomic\. Either the statement succeeds, or it's completely reversed\. For more information, see [Atomic DDL](https://mariadb.com/kb/en/atomic-ddl/)\.
+ **Other enhancements** – These enhancements include a `JSON_TABLE` function for transforming JSON data to relational format within SQL, and faster empty table data load with Innodb\. They also include new `sys_schema` for analysis and troubleshooting, optimizer enhancement for ignoring unused indexes, and performance improvements\. For more information, see [JSON\_TABLE](https://mariadb.com/kb/en/json_table/)\.
+ **New default values for parameters** – The following parameters have new default values for MariaDB version 10\.6 DB instances:
  + The default value for the following parameters has changed from `utf8` to `utf8mb3`: 
    + [character\_set\_client](https://mariadb.com/kb/en/server-system-variables/#character_set_client)
    + [character\_set\_connection](https://mariadb.com/kb/en/server-system-variables/#character_set_connection)
    + [character\_set\_results](https://mariadb.com/kb/en/server-system-variables/#character_set_results)
    + [character\_set\_system](https://mariadb.com/kb/en/server-system-variables/#character_set_system)

    Although the default values have changed for these parameters, there is no functional change\. For more information, see [Supported Character Sets and Collations](https://mariadb.com/kb/en/supported-character-sets-and-collations/) in the MariaDB documentation\.
  + The default value of the [ collation\_connection](https://mariadb.com/kb/en/server-system-variables/#collation_connection) parameter has changed from `utf8_general_ci` to `utf8mb3_general_ci`\. Although the default value has changed for this parameter, there is no functional change\.
  + The default value of the [ old\_mode](https://mariadb.com/kb/en/server-system-variables/#old_mode) parameter has changed from unset to `UTF8_IS_UTF8MB3`\. Although the default value has changed for this parameter, there is no functional change\.

For a list of all MariaDB 10\.6 features and their documentation, see [Changes and improvements in MariaDB 10\.6](https://mariadb.com/kb/en/changes-improvements-in-mariadb-106/) and [Release notes \- MariaDB 10\.6 series](https://mariadb.com/kb/en/release-notes-mariadb-106-series/) on the MariaDB website\. 

For a list of unsupported features, see [MariaDB features not supported by Amazon RDS](#MariaDB.Concepts.FeatureNonSupport)\. 

### MariaDB 10\.5 support on Amazon RDS<a name="MariaDB.Concepts.FeatureSupport.10-5"></a>

Amazon RDS supports the following new features for your DB instances running MariaDB version 10\.5 or later: 
+ **InnoDB enhancements** – MariaDB version 10\.5 includes InnoDB enhancements\. For more information, see [ InnoDB: Performance Improvements etc\.](https://mariadb.com/kb/en/changes-improvements-in-mariadb-105/#innodb-performance-improvements-etc) in the MariaDB documentation\.
+ **Performance schema updates** – MariaDB version 10\.5 includes performance schema updates\. For more information, see [ Performance Schema Updates to Match MySQL 5\.7 Instrumentation and Tables](https://mariadb.com/kb/en/changes-improvements-in-mariadb-105/#performance-schema-updates-to-match-mysql-57-instrumentation-and-tables) in the MariaDB documentation\. 
+ **One file in the InnoDB redo log** – In versions of MariaDB before version 10\.5, the value of the `innodb_log_files_in_group` parameter was set to `2`\. In MariaDB version 10\.5, the value of this parameter is set to `1`\.

  If you are upgrading from a prior version to MariaDB version 10\.5, and you don't modify the parameters, the `innodb_log_file_size` parameter value is unchanged\. However, it applies to one log file instead of two\. The result is that your upgraded MariaDB version 10\.5 DB instance uses half of the redo log size that it was using before the upgrade\. This change can have a noticeable performance impact\. To address this issue, you can double the value of the `innodb_log_file_size` parameter\. For information about modifying parameters, see [Modifying parameters in a DB parameter group](USER_WorkingWithDBInstanceParamGroups.md#USER_WorkingWithParamGroups.Modifying)\. 
+ **SHOW SLAVE STATUS command not supported** – In versions of MariaDB before version 10\.5, the `SHOW SLAVE STATUS` command required the `REPLICATION SLAVE` privilege\. In MariaDB version 10\.5, the equivalent `SHOW REPLICA STATUS` command requires the `REPLICATION REPLICA ADMIN` privilege\. This new privilege isn't granted to the RDS master user\.

  Instead of using the `SHOW REPLICA STATUS` command, run the new `mysql.rds_replica_status` stored procedure to return similar information\. For more information, see [mysql\.rds\_replica\_status](mysql_rds_replica_status.md)\.
+ **SHOW RELAYLOG EVENTS command not supported** – In versions of MariaDB before version 10\.5, the `SHOW RELAYLOG EVENTS` command required the `REPLICATION SLAVE` privilege\. In MariaDB version 10\.5, this command requires the `REPLICATION REPLICA ADMIN` privilege\. This new privilege isn't granted to the RDS master user\.
+ **New default values for parameters** – The following parameters have new default values for MariaDB version 10\.5 DB instances:
  + The default value of the [max\_connections](https://mariadb.com/kb/en/server-system-variables/#max_connections) parameter has changed to `LEAST({DBInstanceClassMemory/25165760},12000)`\. For information about the `LEAST` parameter function, see [DB parameter functions](USER_ParamValuesRef.md#USER_ParamFunctions)\. 
  + The default value of the [ innodb\_adaptive\_hash\_index](https://mariadb.com/kb/en/innodb-system-variables/#innodb_adaptive_hash_index) parameter has changed to `OFF` \(`0`\)\.
  + The default value of the [ innodb\_checksum\_algorithm](https://mariadb.com/kb/en/innodb-system-variables/#innodb_checksum_algorithm) parameter has changed to `full_crc32`\.
  + The default value of the [innodb\_log\_file\_size](https://mariadb.com/kb/en/innodb-system-variables/#innodb_log_file_size) parameter has changed to 2 GB\. 

For a list of all MariaDB 10\.5 features and their documentation, see [Changes and improvements in MariaDB 10\.5](https://mariadb.com/kb/en/changes-improvements-in-mariadb-105/) and [Release notes \- MariaDB 10\.5 series](https://mariadb.com/kb/en/release-notes-mariadb-105-series/) on the MariaDB website\. 

For a list of unsupported features, see [MariaDB features not supported by Amazon RDS](#MariaDB.Concepts.FeatureNonSupport)\. 

### MariaDB 10\.4 support on Amazon RDS<a name="MariaDB.Concepts.FeatureSupport.10-4"></a>

Amazon RDS supports the following new features for your DB instances running MariaDB version 10\.4 or later: 
+ **User account security enhancements** – [Password expiration](https://mariadb.com/kb/en/user-password-expiry/) and [account locking](https://mariadb.com/kb/en/account-locking/) improvements
+ **Optimizer enhancements** – [Optimizer trace feature](https://mariadb.com/kb/en/optimizer-trace-overview/)
+ **InnoDB enhancements ** – [Instant DROP COLUMN support](https://mariadb.com/kb/en/alter-table/#drop-column) and instant `VARCHAR` extension for `ROW_FORMAT=DYNAMIC` and `ROW_FORMAT=COMPACT` 
+ **New parameters** – Including [tcp\_nodedelay](https://mariadb.com/kb/en/server-system-variables/#tcp_nodelay), [tls\_version](https://mariadb.com/kb/en/ssltls-system-variables/#tls_version), and [gtid\_cleanup\_batch\_size](https://mariadb.com/kb/en/gtid/#gtid_cleanup_batch_size)

For a list of all MariaDB 10\.4 features and their documentation, see [Changes and improvements in MariaDB 10\.4](https://mariadb.com/kb/en/library/changes-improvements-in-mariadb-104/) and [Release notes \- MariaDB 10\.4 series](https://mariadb.com/kb/en/library/release-notes-mariadb-104-series/) on the MariaDB website\. 

For a list of unsupported features, see [MariaDB features not supported by Amazon RDS](#MariaDB.Concepts.FeatureNonSupport)\. 

### MariaDB 10\.3 support on Amazon RDS<a name="MariaDB.Concepts.FeatureSupport.10-3"></a>

Amazon RDS supports the following new features for your DB instances running MariaDB version 10\.3 or later: 
+ **Oracle compatibility** – PL/SQL compatibility parser, sequences, INTERSECT and EXCEPT to complement UNION, new TYPE OF and ROW TYPE OF declarations, and invisible columns
+ **Temporal data processing** – System versioned tables for querying of past and present states of the database
+ **Flexibility** – User\-defined aggregates, storage\-independent column compression, and proxy protocol support to relay the client IP address to the server
+ **Manageability** – Instant ADD COLUMN operations and fast\-fail data definition language \(DDL\) operations

For a list of all MariaDB 10\.3 features and their documentation, see [Changes & improvements in MariaDB 10\.3](https://mariadb.com/kb/en/library/changes-improvements-in-mariadb-103/) and [Release notes \- MariaDB 10\.3 series](https://mariadb.com/kb/en/library/release-notes-mariadb-103-series/) on the MariaDB website\. 

For a list of unsupported features, see [MariaDB features not supported by Amazon RDS](#MariaDB.Concepts.FeatureNonSupport)\. 

## Supported storage engines for MariaDB on Amazon RDS<a name="MariaDB.Concepts.Storage"></a>

RDS for MariaDB supports the following storage engines\.

**Topics**
+ [The InnoDB storage engine](#MariaDB.Concepts.Storage.InnoDB)
+ [The MyRocks storage engine](#MariaDB.Concepts.Storage.MyRocks)

Other storage engines aren't currently supported by RDS for MariaDB\.

### The InnoDB storage engine<a name="MariaDB.Concepts.Storage.InnoDB"></a>

Although MariaDB supports multiple storage engines with varying capabilities, not all of them are optimized for recovery and data durability\. InnoDB is the recommended storage engine for MariaDB DB instances on Amazon RDS\. Amazon RDS features such as point\-in\-time restore and snapshot restore require a recoverable storage engine and are supported only for the recommended storage engine for the MariaDB version\.

For more information, see [InnoDB](https://mariadb.com/kb/en/innodb/)\.

### The MyRocks storage engine<a name="MariaDB.Concepts.Storage.MyRocks"></a>

The MyRocks storage engine is available in RDS for MariaDB version 10\.6 and higher\. Before using the MyRocks storage engine in a production database, we recommend that you perform thorough benchmarking and testing to verify any potential benefits over InnoDB for your use case\.

The default parameter group for MariaDB version 10\.6 includes MyRocks parameters\. For more information, see [Parameters for MariaDB](Appendix.MariaDB.Parameters.md) and [Working with parameter groups](USER_WorkingWithParamGroups.md)\.

To create a table that uses the MyRocks storage engine, specify `ENGINE=RocksDB` in the `CREATE TABLE` statement\. The following example creates a table that uses the MyRocks storage engine\.

```
CREATE TABLE test (a INT NOT NULL, b CHAR(10)) ENGINE=RocksDB;
```

We strongly recommend that you don't run transactions that span both InnoDB and MyRocks tables\. MariaDB doesn't guarantee ACID \(atomicity, consistency, isolation, durability\) for transactions across storage engines\. Although it is possible to have both InnoDB and MyRocks tables in a DB instance, we don't recommend this approach except during a migration from one storage engine to the other\. When both InnoDB and MyRocks tables exist in a DB instance, each storage engine has its own buffer pool, which might cause performance to degrade\.

MyRocks doesn’t support `SERIALIZABLE` isolation or gap locks\. So, generally you can't use MyRocks with statement\-based replication\. For more information, see [ MyRocks and Replication](https://mariadb.com/kb/en/myrocks-and-replication/)\.

Currently, you can modify only the following MyRocks parameters:
+ [https://mariadb.com/kb/en/myrocks-system-variables/#rocksdb_block_cache_size](https://mariadb.com/kb/en/myrocks-system-variables/#rocksdb_block_cache_size)
+ [https://mariadb.com/kb/en/myrocks-system-variables/#rocksdb_bulk_load](https://mariadb.com/kb/en/myrocks-system-variables/#rocksdb_bulk_load)
+ [https://mariadb.com/kb/en/myrocks-system-variables/#rocksdb_bulk_load_size](https://mariadb.com/kb/en/myrocks-system-variables/#rocksdb_bulk_load_size)
+ [https://mariadb.com/kb/en/myrocks-system-variables/#rocksdb_deadlock_detect](https://mariadb.com/kb/en/myrocks-system-variables/#rocksdb_deadlock_detect)
+ [https://mariadb.com/kb/en/myrocks-system-variables/#rocksdb_deadlock_detect_depth](https://mariadb.com/kb/en/myrocks-system-variables/#rocksdb_deadlock_detect_depth)
+ [https://mariadb.com/kb/en/myrocks-system-variables/#rocksdb_max_latest_deadlocks](https://mariadb.com/kb/en/myrocks-system-variables/#rocksdb_max_latest_deadlocks)

The MyRocks storage engine and the InnoDB storage engine can compete for memory based on the settings for the `rocksdb_block_cache_size` and `innodb_buffer_pool_size` parameters\. In some cases, you might only intend to use the MyRocks storage engine on a particular DB instance\. If so, we recommend setting the `innodb_buffer_pool_size minimal` parameter to a minimal value and setting the `rocksdb_block_cache_size` as high as possible\.

You can access MyRocks log files by using the [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBLogFiles.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBLogFiles.html) and [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DownloadDBLogFilePortion.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DownloadDBLogFilePortion.html) operations\.

For more information about MyRocks, see [MyRocks](https://mariadb.com/kb/en/myrocks/) on the MariaDB website\.

## Cache warming for MariaDB on Amazon RDS<a name="MariaDB.Concepts.XtraDBCacheWarming"></a>

InnoDB cache warming can provide performance gains for your MariaDB DB instance by saving the current state of the buffer pool when the DB instance is shut down, and then reloading the buffer pool from the saved information when the DB instance starts up\. This approach bypasses the need for the buffer pool to "warm up" from normal database use and instead preloads the buffer pool with the pages for known common queries\. For more information on cache warming, see [ Dumping and restoring the buffer pool](http://mariadb.com/kb/en/mariadb/xtradbinnodb-buffer-pool/#dumping-and-restoring-the-buffer-pool) in the MariaDB documentation\.

Cache warming is enabled by default on MariaDB 10\.3 and higher DB instances\. To enable it, set the `innodb_buffer_pool_dump_at_shutdown` and `innodb_buffer_pool_load_at_startup` parameters to 1 in the parameter group for your DB instance\. Changing these parameter values in a parameter group affects all MariaDB DB instances that use that parameter group\. To enable cache warming for specific MariaDB DB instances, you might need to create a new parameter group for those DB instances\. For information on parameter groups, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\.

Cache warming primarily provides a performance benefit for DB instances that use standard storage\. If you use PIOPS storage, you don't commonly see a significant performance benefit\.

**Important**  
If your MariaDB DB instance doesn't shut down normally, such as during a failover, then the buffer pool state isn't saved to disk\. In this case, MariaDB loads whatever buffer pool file is available when the DB instance is restarted\. No harm is done, but the restored buffer pool might not reflect the most recent state of the buffer pool before the restart\. To ensure that you have a recent state of the buffer pool available to warm the cache on startup, we recommend that you periodically dump the buffer pool "on demand\." You can dump or load the buffer pool on demand\.  
You can create an event to dump the buffer pool automatically and at a regular interval\. For example, the following statement creates an event named `periodic_buffer_pool_dump` that dumps the buffer pool every hour\.   

```
1. CREATE EVENT periodic_buffer_pool_dump 
2.    ON SCHEDULE EVERY 1 HOUR 
3.    DO CALL mysql.rds_innodb_buffer_pool_dump_now();
```
For more information, see [Events](http://mariadb.com/kb/en/mariadb/stored-programs-and-views-events/) in the MariaDB documentation\.

### Dumping and loading the buffer pool on demand<a name="MariaDB.Concepts.XtraDBCacheWarming.OnDemand"></a>

You can save and load the cache on demand using the following stored procedures:
+ To dump the current state of the buffer pool to disk, call the [mysql\.rds\_innodb\_buffer\_pool\_dump\_now](mysql_rds_innodb_buffer_pool_dump_now.md) stored procedure\.
+ To load the saved state of the buffer pool from disk, call the [mysql\.rds\_innodb\_buffer\_pool\_load\_now](mysql_rds_innodb_buffer_pool_load_now.md) stored procedure\.
+ To cancel a load operation in progress, call the [mysql\.rds\_innodb\_buffer\_pool\_load\_abort](mysql_rds_innodb_buffer_pool_load_abort.md) stored procedure\.

## MariaDB features not supported by Amazon RDS<a name="MariaDB.Concepts.FeatureNonSupport"></a>

The following MariaDB features are not supported on Amazon RDS:
+ S3 storage engine
+ Authentication plugin – GSSAPI
+ Authentication plugin – Unix Socket
+ AWS Key Management encryption plugin
+ Delayed replication for MariaDB versions lower than 10\.6
+ Native MariaDB encryption at rest for InnoDB and Aria\.

  You can enable encryption at rest for a MariaDB DB instance by following the instructions in [Encrypting Amazon RDS resources](Overview.Encryption.md)\.
+ HandlerSocket
+ JSON table type for MariaDB versions lower than 10\.6
+ MariaDB ColumnStore
+ MariaDB Galera Cluster
+ Multisource replication
+ MyRocks storage engine for MariaDB versions lower than 10\.6
+ Password validation plugin, `simple_password_check`, and `cracklib_password_check` 
+ Spider storage engine
+ Sphinx storage engine
+ TokuDB storage engine
+ Storage engine\-specific object attributes, as described in [ Engine\-defined new Table/Field/Index attributes](http://mariadb.com/kb/en/mariadb/engine-defined-new-tablefieldindex-attributes/) in the MariaDB documentation
+ Table and tablespace encryption

To deliver a managed service experience, Amazon RDS doesn't provide shell access to DB instances, and it restricts access to certain system procedures and tables that require advanced privileges\. Amazon RDS supports access to databases on a DB instance using any standard SQL client application\. Amazon RDS doesn't allow direct host access to a DB instance by using Telnet, Secure Shell \(SSH\), or Windows Remote Desktop Connection\. 