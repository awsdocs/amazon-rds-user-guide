# Improving query performance for RDS for MySQL with Amazon RDS Optimized Reads<a name="rds-optimized-reads"></a>

You can achieve faster query processing for RDS for MySQL with Amazon RDS Optimized Reads\. An RDS for MySQL DB instance that uses RDS Optimized Reads can achieve up to 2x faster query processing compared to a DB instance that doesn't use it\.

**Topics**
+ [Overview of RDS Optimized Reads](#rds-optimized-reads-overview)
+ [Use cases for RDS Optimized Reads](#rds-optimized-reads-use-cases)
+ [Best practices for RDS Optimized Reads](#rds-optimized-reads-best-practices)
+ [Using RDS Optimized Reads](#rds-optimized-reads-using)
+ [Monitoring DB instances that use RDS Optimized Reads](#rds-optimized-reads-monitoring)
+ [Limitations for RDS Optimized Reads](#rds-optimized-reads-limitations)

## Overview of RDS Optimized Reads<a name="rds-optimized-reads-overview"></a>

When you use an RDS for MySQL DB instance that has RDS Optimized Reads turned on, your DB instance achieves faster query performance through the use of an instance store\. An *instance store* provides temporary block\-level storage for your DB instance\. The storage is located on Non\-Volatile Memory Express \(NVMe\) solid state drives \(SSDs\) that are physically attached to the host server\. This storage is optimized for low latency, high random I/O performance, and high sequential read throughput\.

RDS Optimized Reads is turned on by default when a DB instance uses a DB instance class with an instance store, such as db\.m5d or db\.m6gd\. With RDS Optimized Reads, some temporary objects are stored on the instance store\. These temporary objects include internal temporary files, internal on\-disk temp tables, memory map files, and binary log \(binlog\) cache files\. For more information about the instance store, see [Amazon EC2 instance store](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/InstanceStorage.html) in the *Amazon Elastic Compute Cloud User Guide for Linux Instances*\.

The workloads that generate temporary objects in MySQL for query processing can take advantage of the instance store for faster query processing\. This type of workload includes queries involving sorts, hash aggregations, high\-load joins, Common Table Expressions \(CTEs\), and queries on unindexed columns\. These instance store volumes provide higher IOPS and performance, regardless of the storage configurations used for persistent Amazon EBS storage\. Because RDS Optimized Reads offloads operations on temporary objects to the instance store, the input/output operations per second \(IOPS\) or throughput of the persistent storage \(Amazon EBS\) can now be used for operations on persistent objects\. These operations include regular data file reads and writes, and background engine operations, such as flushing and insert buffer merges\.

**Note**  
Both manual and automated RDS snapshots only contain engine files for persistent objects\. The temporary objects created in the instance store aren't included in RDS snapshots\.

## Use cases for RDS Optimized Reads<a name="rds-optimized-reads-use-cases"></a>

If you have workloads that rely heavily on temporary objects, such as internal tables or files, for their query execution, then you can benefit from turning on RDS Optimized Reads\. The following use cases are candidates for RDS Optimized Reads:
+ Applications that run analytical queries with complex common table expressions \(CTEs\), derived tables, and grouping operations
+ Read replicas that serve heavy read traffic with unoptimized queries
+ Applications that run on\-demand or dynamic reporting queries that involve complex operations, such as queries with `GROUP BY` and `ORDER BY` clauses
+ Workloads that use internal temporary tables for query processing

  You can monitor the engine status variable `created_tmp_disk_tables` to determine the number of disk\-based temporary tables created on your DB instance\.
+ Applications that create large temporary tables, either directly or in procedures, to store intermediate results
+ Database queries that perform grouping or ordering on non\-indexed columns

## Best practices for RDS Optimized Reads<a name="rds-optimized-reads-best-practices"></a>

Use the following best practices for RDS Optimized Reads:
+ Add retry logic for read\-only queries in case they fail because the instance store is full during the execution\.
+ Monitor the storage space available on the instance store with the CloudWatch metric `FreeLocalStorage`\. If the instance store is reaching its limit because of workload on the DB instance, modify the DB instance to use a larger DB instance class\.
+ When your DB instance has sufficient memory but is still reaching the storage limit on the instance store, increase the `binlog_cache_size` value to maintain the session\-specific binlog entries in memory\. This configuration prevents writing the binlog entries to temporary binlog cache files on disk\.

  The `binlog_cache_size` parameter is session\-specific\. You can change the value for each new session\. The setting for this parameter can increase the memory utilization on the DB instance during peak workload\. Therefore, consider increasing the parameter value based on the workload pattern of your application and available memory on the DB instance\.
+ Use the default value of `MIXED` for the `binlog_format`\. Depending on the size of the transactions, setting `binlog_format` to `ROW` can result in large binlog cache files on the instance store\.
+ Set the [internal\_tmp\_mem\_storage\_engine](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_internal_tmp_mem_storage_engine) parameter to `TempTable`, and set the [temptable\_max\_mmap](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_temptable_max_mmap) parameter to match the size of the available storage on the instance store\.
+ Avoid performing bulk changes in a single transaction\. These types of transactions can generate large binlog cache files on the instance store and can cause issues when the instance store is full\. Consider splitting writes into multiple small transactions to minimize storage use for binlog cache files\.
+ Use the default value of `ABORT_SERVER` for the `binlog_error_action` parameter\. Doing so avoids issues with the binary logging on DB instances with backups enabled\.

## Using RDS Optimized Reads<a name="rds-optimized-reads-using"></a>

When you provision an RDS for MySQL DB instance with one of the following DB instance classes in a Single\-AZ DB instance deployment or Multi\-AZ DB instance deployment, the DB instance automatically uses RDS Optimized Reads:
+ db\.x2idn
+ db\.x2iedn
+ db\.m6gd
+ db\.r6gd
+ db\.m5d
+ db\.r5d

To turn on RDS Optimized Reads, do one of the following:
+ Create an RDS for MySQL DB instance using one of these DB instance classes\. For more information, see [Creating an Amazon RDS DB instance](USER_CreateDBInstance.md)\.
+ Modify an existing RDS for MySQL DB instance to use one of these DB instance classes\. For more information, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\.

RDS Optimized Reads is available in all AWS Regions where one or more of these DB instance classes are supported\. For more information, see [DB instance classes](Concepts.DBInstanceClass.md)\.

If you don't want to use RDS Optimized Reads, modify your DB instance so that it doesn't use a DB instance class that supports the feature\.

## Monitoring DB instances that use RDS Optimized Reads<a name="rds-optimized-reads-monitoring"></a>

You can monitor DB instances that use RDS Optimized Reads with the following CloudWatch metrics:
+ `FreeLocalStorage`
+ `ReadIOPSLocalStorage`
+ `ReadLatencyLocalStorage`
+ `ReadThroughputLocalStorage`
+ `WriteIOPSLocalStorage`
+ `WriteLatencyLocalStorage`
+ `WriteThroughputLocalStorage`

These metrics provide data about available instance store storage, IOPS, and throughput\. For more information about these metrics, see [Amazon CloudWatch instance\-level metrics for Amazon RDS](rds-metrics.md#rds-cw-metrics-instance)\.

## Limitations for RDS Optimized Reads<a name="rds-optimized-reads-limitations"></a>

The following limitations apply to RDS Optimized Reads:
+ RDS Optimized Reads is supported for RDS for MySQL version 8\.0\.28 and higher\. For information about RDS for MySQL versions, see [MySQL on Amazon RDS versions](MySQL.Concepts.VersionMgmt.md)\.
+ You can't change the location of temporary objects to persistent storage \(Amazon EBS\) on the DB instance classes that support RDS Optimized Reads\.
+ When binary logging is enabled on a DB instance, the maximum transaction size is limited by the size of the instance store\. In MySQL, any session that requires more storage than the value of `binlog_cache_size` writes transaction changes to temporary binlog cache files, which are created on the instance store\.
+ Transactions can fail when the instance store is full\.
+ RDS Optimized Reads isn't supported for Multi\-AZ DB cluster deployments\.