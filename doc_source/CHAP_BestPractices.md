# Best Practices for Amazon RDS<a name="CHAP_BestPractices"></a>

Learn best practices for working with Amazon RDS\. As new best practices are identified, we will keep this section up to date\. 

**Topics**
+ [Amazon RDS Basic Operational Guidelines](#CHAP_BestPractices.DiskPerformance)
+ [DB Instance RAM Recommendations](#CHAP_BestPractices.Performance.RAM)
+ [Using Enhanced Monitoring to Identify Operating System Issues](#CHAP_BestPractices.EnhancedMonitoring)
+ [Using Metrics to Identify Performance Issues](#CHAP_BestPractices.UsingMetrics)
+ [Best Practices for Working with MySQL Storage Engines](#CHAP_BestPractices.MySQLStorage)
+ [Best Practices for Working with MariaDB Storage Engines](#CHAP_BestPractices.MariaDB)
+ [Best Practices for Working with Oracle](#CHAP_BestPractices.Oracle)
+ [Best Practices for Working with PostgreSQL](#CHAP_BestPractices.PostgreSQL)
+ [Best Practices for Working with SQL Server](#CHAP_BestPractices.SQLServer)
+ [Working with DB Parameter Groups](#CHAP_BestPractices.DBParameterGroup)
+ [Amazon RDS New Features and Best Practices Presentation Video](#CHAP_BestPractices.Presentation)

**Note**  
For common recommendations for Amazon RDS, see [Using Amazon RDS Recommendations](USER_Recommendations.md)\.

## Amazon RDS Basic Operational Guidelines<a name="CHAP_BestPractices.DiskPerformance"></a>

The following are basic operational guidelines that everyone should follow when working with Amazon RDS\. Note that the Amazon RDS Service Level Agreement requires that you follow these guidelines:
+ Monitor your memory, CPU, and storage usage\. Amazon CloudWatch can be set up to notify you when usage patterns change or when you approach the capacity of your deployment, so that you can maintain system performance and availability\.
+ Scale up your DB instance when you are approaching storage capacity limits\. You should have some buffer in storage and memory to accommodate unforeseen increases in demand from your applications\. 
+ Enable automatic backups and set the backup window to occur during the daily low in write IOPS\. That's when a backup is least disruptive to your database usage\.
+ If your database workload requires more I/O than you have provisioned, recovery after a failover or database failure will be slow\. To increase the I/O capacity of a DB instance, do any or all of the following:
  + Migrate to a different DB instance class with high I/O capacity\.
  + Convert from magnetic storage to either General Purpose or Provisioned IOPS storage, depending on how much of an increase you need\. For information on available storage types, see [Amazon RDS Storage Types](CHAP_Storage.md#Concepts.Storage)\.

    If you convert to Provisioned IOPS storage, make sure you also use a DB instance class that is optimized for Provisioned IOPS\. For information on Provisioned IOPS, see [Provisioned IOPS SSD Storage](CHAP_Storage.md#USER_PIOPS)\.
  + If you are already using Provisioned IOPS storage, provision additional throughput capacity\. 
+ If your client application is caching the Domain Name Service \(DNS\) data of your DB instances, set a time\-to\-live \(TTL\) value of less than 30 seconds\. Because the underlying IP address of a DB instance can change after a failover, caching the DNS data for an extended time can lead to connection failures if your application tries to connect to an IP address that no longer is in service\.
+ Test failover for your DB instance to understand how long the process takes for your particular use case and to ensure that the application that accesses your DB instance can automatically connect to the new DB instance after failover occurs\.

## DB Instance RAM Recommendations<a name="CHAP_BestPractices.Performance.RAM"></a>

An Amazon RDS performance best practice is to allocate enough RAM so that your *working set* resides almost completely in memory\. The working set is the data and indexes that are frequently in use on your instance\. The more you use the DB instance, the more the working set will grow\.

To tell if your working set is almost all in memory, check the ReadIOPS metric \(using Amazon CloudWatch\) while the DB instance is under load\. The value of ReadIOPS should be small and stable\. If scaling up the DB instance class—to a class with more RAM—results in a dramatic drop in ReadIOPS, your working set was not almost completely in memory\. Continue to scale up until ReadIOPS no longer drops dramatically after a scaling operation, or ReadIOPS is reduced to a very small amount\. For information on monitoring a DB instance's metrics, see [Viewing DB Instance Metrics](MonitoringOverview.md#USER_Monitoring)\.

## Using Enhanced Monitoring to Identify Operating System Issues<a name="CHAP_BestPractices.EnhancedMonitoring"></a>

When Enhanced Monitoring is enabled, Amazon RDS provides metrics in real time for the operating system \(OS\) that your DB instance runs on\. You can view the metrics for your DB instance using the console, or consume the Enhanced Monitoring JSON output from Amazon CloudWatch Logs in a monitoring system of your choice\. For more information about Enhanced Monitoring, see [Enhanced Monitoring](USER_Monitoring.OS.md)

## Using Metrics to Identify Performance Issues<a name="CHAP_BestPractices.UsingMetrics"></a>

 To identify performance issues caused by insufficient resources and other common bottlenecks, you can monitor the metrics available for your Amazon RDS DB instance\. 

### Viewing Performance Metrics<a name="CHAP_BestPractices.UsingMetrics.ViewingMetrics"></a>

You should monitor performance metrics on a regular basis to see the average, maximum, and minimum values for a variety of time ranges\. If you do so, you can identify when performance is degraded\. You can also set Amazon CloudWatch alarms for particular metric thresholds so you are alerted if they are reached\. 

To troubleshoot performance issues, it's important to understand the baseline performance of the system\. When you set up a new DB instance and get it running with a typical workload, you should capture the average, maximum, and minimum values of all of the performance metrics at a number of different intervals \(for example, one hour, 24 hours, one week, two weeks\) to get an idea of what is normal\. It helps to get comparisons for both peak and off\-peak hours of operation\. You can then use this information to identify when performance is dropping below standard levels\.

**To view performance metrics**

1.  Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\. 

1.  In the navigation pane, choose **Databases**, and then choose a DB instance\. 

1.  Choose **Monitoring**\. The first eight performance metrics display\. The metrics default to showing information for the current day\. 

1.  Use the numbered buttons at top right to page through the additional metrics, or choose adjust the settings to see more metrics\. 

1.  Choose a performance metric to adjust the time range in order to see data for other than the current day\. You can change the **Statistic**, **Time Range**, and **Period** values to adjust the information displayed\. For example, to see the peak values for a metric for each day of the last two weeks, set **Statistic** to **Maximum**, **Time Range** to **Last 2 Weeks**, and **Period** to **Day**\. 
**Note**  
 Changing the **Statistic**, **Time Range**, and **Period** values changes them for all metrics\. The updated values persist for the remainder of your session or until you change them again\. 

 You can also view performance metrics using the CLI or API\. For more information, see [Viewing DB Instance Metrics](MonitoringOverview.md#USER_Monitoring)\. 

****To set a CloudWatch alarm****

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\. 

1. In the navigation pane, choose **Databases**, and then choose a DB instance\. 

1. Choose **Logs & events**\.

1. In the **CloudWatch alarms** section, choose **Create alarm**\.  
![\[Create Alarm dialog box\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/CreateAlarm.png)

1. For **Send notifications**, choose **Yes**, and for **Send notifications to**, choose **New email or SMS topic**\.

1. For **Topic name**, enter a name for the notification, and for **With these recipients**, enter a comma\-separated list of email addresses and phone numbers\.

1. For **Metric**, choose the alarm statistic and metric to set\. 

1. For **Threshold**, specify whether the metric must be greater than, less than, or equal to the threshold, and specify the threshold value\. 

1. For **Evaluation period**, choose the evaluation period for the alarm, and for **consecutive period\(s\) of**, choose the period during which the threshold must have been reached in order to trigger the alarm\.

1. For **Name of alarm**, enter a name for the alarm\.

1. Choose **Create Alarm**\.

The alarm appears in the **CloudWatch alarms** section\.

### Evaluating Performance Metrics<a name="CHAP_BestPractices.EvaluatingMetrics"></a>

 A DB instance has a number of different categories of metrics, and how to determine acceptable values depends on the metric\. 

**CPU**
+  CPU Utilization – Percentage of computer processing capacity used\. 

**Memory**
+ Freeable Memory – How much RAM is available on the DB instance, in megabytes\. The red line in the Monitoring tab metrics is marked at 75% for CPU, Memory and Storage Metrics\. If instance memory consumption frequently crosses that line, then this indicates that you should check your workload or upgrade your instance\. 
+  Swap Usage – How much swap space is used by the DB instance, in megabytes\. 

**Disk space**
+  Free Storage Space – How much disk space is not currently being used by the DB instance, in megabytes\. 

**Input/output operations**
+  Read IOPS, Write IOPS – The average number of disk read or write operations per second\. 
+  Read Latency, Write Latency – The average time for a read or write operation in milliseconds\. 
+  Read Throughput, Write Throughput – The average number of megabytes read from or written to disk per second\. 
+  Queue Depth – The number of I/O operations that are waiting to be written to or read from disk\. 

**Network traffic**
+  Network Receive Throughput, Network Transmit Throughput – The rate of network traffic to and from the DB instance in megabytes per second\. 

**Database connections**
+  DB Connections – The number of client sessions that are connected to the DB instance\. 

 For more detailed individual descriptions of the performance metrics available, see [Monitoring with Amazon CloudWatch](MonitoringOverview.md#monitoring-cloudwatch)\. 

 Generally speaking, acceptable values for performance metrics depend on what your baseline looks like and what your application is doing\. Investigate consistent or trending variances from your baseline\. Advice about specific types of metrics follows: 
+  **High CPU or RAM consumption –** High values for CPU or RAM consumption might be appropriate, provided that they are in keeping with your goals for your application \(like throughput or concurrency\) and are expected\. 
+  **Disk space consumption – ** Investigate disk space consumption if space used is consistently at or above 85 percent of the total disk space\. See if it is possible to delete data from the instance or archive data to a different system to free up space\. 
+  **Network traffic –** For network traffic, talk with your system administrator to understand what expected throughput is for your domain network and Internet connection\. Investigate network traffic if throughput is consistently lower than expected\. 
+  **Database connections –** Consider constraining database connections if you see high numbers of user connections in conjunction with decreases in instance performance and response time\. The best number of user connections for your DB instance will vary based on your instance class and the complexity of the operations being performed\. You can determine the number of database connections by associating your DB instance with a parameter group where the *User Connections* parameter is set to other than 0 \(unlimited\)\. You can either use an existing parameter group or create a new one\. For more information, see [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)\. 
+  **IOPS metrics –** The expected values for IOPS metrics depend on disk specification and server configuration, so use your baseline to know what is typical\. Investigate if values are consistently different than your baseline\. For best IOPS performance, make sure your typical working set will fit into memory to minimize read and write operations\. 

 For issues with any performance metrics, one of the first things you can do to improve performance is tune the most used and most expensive queries to see if that lowers the pressure on system resources\. For more information, see [ Tuning Queries ](#CHAP_BestPractices.TuningQueries)

 If your queries are tuned and an issue persists, consider upgrading your Amazon RDS [DB Instance Classes](Concepts.DBInstanceClass.md) to one with more of the resource \(CPU, RAM, disk space, network bandwidth, I/O capacity\) that is related to the issue you are experiencing\. 

### Tuning Queries<a name="CHAP_BestPractices.TuningQueries"></a>

 One of the best ways to improve DB instance performance is to tune your most commonly used and most resource\-intensive queries to make them less expensive to run\. 

 MySQL Query Tuning 

 Go to [Optimizing SELECT Statements](https://dev.mysql.com/doc/refman/8.0/en/select-optimization.html) in the MySQL documentation for more information on writing queries for better performance\. You can also go to [MySQL Performance Tuning and Optimization Resources](http://www.mysql.com/why-mysql/performance/) for additional query tuning resources\. 

 Oracle Query Tuning 

 Go to the [Database SQL Tuning Guide](https://docs.oracle.com/database/121/TGSQL/toc.htm) in the Oracle documentation for more information on writing and analyzing queries for better performance\. 

 SQL Server Query Tuning 

 Go to [Analyzing a Query](http://technet.microsoft.com/en-us/library/ms191227.aspx) in the SQL Server documentation to improve queries for SQL Server DB instances\. You can also use the execution\-, index\- and I/O\-related data management views \(DMVs\) described in the [Dynamic Management Views and Functions](http://msdn.microsoft.com/en-us/library/ms188754%28v=sql.110%29.aspx) documentation to troubleshoot SQL Server query issues\. 

 A common aspect of query tuning is creating effective indexes\. You can use the [Database Engine Tuning Advisor](http://msdn.microsoft.com/en-us/library/hh231122%28v=sql.110%29.aspx) to get potential index improvements for your DB instance\. For more information, see [Analyzing Your Database Workload on an Amazon RDS DB Instance with SQL Server Tuning Advisor](Appendix.SQLServer.CommonDBATasks.Workload.md)\. 

 PostgreSQL Query Tuning 

 Go to [Using EXPLAIN](http://www.postgresql.org/docs/8.2/static/using-explain.html) in the PostgreSQL documentation to learn how to analyze a query plan\. You can use this information to modify a query or underlying tables in order to improve query performance\. You can also go to [Controlling the Planner with Explicit JOIN Clauses](http://www.postgresql.org/docs/8.2/static/explicit-joins.html) to get tips about how to specify joins in your query for the best performance\. 

 MariaDB Query Tuning 

 Go to [Query Optimizations](https://mariadb.com/kb/en/mariadb/query-optimizations/) in the MariaDB documentation for more information on writing queries for better performance\.

## Best Practices for Working with MySQL Storage Engines<a name="CHAP_BestPractices.MySQLStorage"></a>

On a MySQL DB instance, observe the following table creation limits:
+ You're limited to 10,000 tables if you are either using Provisioned IOPS storage, or using General Purpose storage and the DB instance is 200 GiB or larger in size\.
+ You're limited to 1000 tables if you are either using magnetic storage, or using General Purpose storage and the DB instance is less than 200 GiB in size\.

We recommend these limits because having large numbers of tables significantly increases database recovery time after a failover or database crash\. If you need to create more tables than recommended, set the `innodb_file_per_table` parameter to 0\. For more information, see [Working with InnoDB Tablespaces to Improve Crash Recovery Times](Appendix.MySQL.CommonDBATasks.md#Appendix.MySQL.CommonDBATasks.Tables) and [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)\.

For MySQL DB instances that use version 5\.7 or later, you can exceed these table creation limits due to improvements in InnoDB crash recovery\. However, we still recommend that you take caution due to the potential performance impact of creating very large numbers of tables\.

On a MySQL DB instance, avoid tables in your database growing too large\. Although the general storage limit is 64 TiB, provisioned storage limits restrict the maximum size of a MySQL table file to 16 TiB\. Partition your large tables so that file sizes are well under the 16 TiB limit\. This approach can also improve performance and recovery time\. For more information, see [MySQL File Size Limits in Amazon RDS](MySQL.KnownIssuesAndLimitations.md#MySQL.Concepts.Limits.FileSize)\.

The Point\-In\-Time Restore and snapshot restore features of Amazon RDS for MySQL require a crash\-recoverable storage engine and are supported for the InnoDB storage engine only\. Although MySQL supports multiple storage engines with varying capabilities, not all of them are optimized for crash recovery and data durability\. For example, the MyISAM storage engine does not support reliable crash recovery and might prevent a Point\-In\-Time Restore or snapshot restore from working as intended\. This might result in lost or corrupt data when MySQL is restarted after a crash\. 

InnoDB is the recommended and supported storage engine for MySQL DB instances on Amazon RDS\. InnoDB instances can also be migrated to Aurora, while MyISAM instances can't be migrated\. However, MyISAM performs better than InnoDB if you require intense, full\-text search capability\. If you still choose to use MyISAM with Amazon RDS, following the steps outlined in [Automated Backups with Unsupported MySQL Storage Engines](USER_WorkingWithAutomatedBackups.md#Overview.BackupDeviceRestrictions) can be helpful in certain scenarios for snapshot restore functionality\.

If you want to convert existing MyISAM tables to InnoDB tables, you can use the process outlined in the [MySQL documentation](http://dev.mysql.com/doc/refman/5.0/en/converting-tables-to-innodb.html)\. MyISAM and InnoDB have different strengths and weaknesses, so you should fully evaluate the impact of making this switch on your applications before doing so\. 

In addition, Federated Storage Engine is currently not supported by Amazon RDS for MySQL\.

## Best Practices for Working with MariaDB Storage Engines<a name="CHAP_BestPractices.MariaDB"></a>

The point\-in\-time restore and snapshot restore features of Amazon RDS for MariaDB require a crash\-recoverable storage engine\. Although MariaDB supports multiple storage engines with varying capabilities, not all of them are optimized for crash recovery and data durability\. For example, although Aria is a crash\-safe replacement for MyISAM, it might still prevent a point\-in\-time restore or snapshot restore from working as intended\. This might result in lost or corrupt data when MariaDB is restarted after a crash\. InnoDB \(for version 10\.2 and higher\) and XtraDB \(for version 10\.0 and 10\.1\) are the recommended and supported storage engines for MariaDB DB instances on Amazon RDS\. If you still choose to use Aria with Amazon RDS, following the steps outlined in [Automated Backups with Unsupported MariaDB Storage Engines](USER_WorkingWithAutomatedBackups.md#Overview.BackupDeviceRestrictionsMariaDB) can be helpful in certain scenarios for snapshot restore functionality\.

## Best Practices for Working with Oracle<a name="CHAP_BestPractices.Oracle"></a>

For information about best practices for working with Amazon RDS for Oracle, see [ Best Practices for Running Oracle Database on Amazon Web Services](https://docs.aws.amazon.com/aws-technical-content/latest/oracle-database-aws-best-practices/introduction.html)\.

A 2020 AWS virtual workshop included a presentation on running production Oracle databases on Amazon RDS\. A video of the presentation is available here:

[![AWS Videos](http://img.youtube.com/vi/https://www.youtube.com/embed/vpSWZx4-M-M/0.jpg)](http://www.youtube.com/watch?v=https://www.youtube.com/embed/vpSWZx4-M-M)

## Best Practices for Working with PostgreSQL<a name="CHAP_BestPractices.PostgreSQL"></a>

Two important areas where you can improve performance with PostgreSQL on Amazon RDS are when loading data into a DB instance and when using the PostgreSQL autovacuum feature\. The following sections cover some of the practices we recommend for these areas\.

### Loading Data into a PostgreSQL DB Instance<a name="CHAP_BestPractices.PostgreSQL.LoadingData"></a>

When loading data into an Amazon RDS PostgreSQL DB instance, you should modify your DB instance settings and your DB parameter group values to allow for the most efficient importing of data into your DB instance\.

Modify your DB instance settings to the following:
+ Disable DB instance backups \(set backup\_retention to 0\)
+ Disable Multi\-AZ

Modify your DB parameter group to include the following settings\. You should test the parameter settings to find the most efficient settings for your DB instance:
+ Increase the value of the `maintenance_work_mem` parameter\. For more information about PostgreSQL resource consumption parameters, see the [PostgreSQL documentation](http://www.postgresql.org/docs/9.4/static/runtime-config-resource.html)\.
+ Increase the value of the `checkpoint_segments` and `checkpoint_timeout` parameters to reduce the number of writes to the wal log\.
+ Disable the `synchronous_commit` parameter \(do not turn off FSYNC\)\.
+ Disable the PostgreSQL autovacuum parameter\.
+ Make sure none of the tables you are importing are unlogged\. Data stored in unlogged tables can be lost during a failover\. For more information, see [CREATE TABLE UNLOGGED](https://www.postgresql.org/docs/current/static/sql-createtable.html)\.

Use the `pg_dump -Fc` \(compressed\) or `pg_restore -j` \(parallel\) commands with these settings\.

After the load operation completes, return your DB instance and DB parameters to their normal settings\.

### Working with the PostgreSQL Autovacuum Feature<a name="CHAP_BestPractices.PostgreSQL.Autovacuum"></a>

 The autovacuum feature for PostgreSQL databases is a feature that we strongly recommend you use to maintain the health of your PostgreSQL DB instance\. Autovacuum automates the execution of the VACUUM and ANALYZE command; using autovacuum is required by PostgreSQL, not imposed by Amazon RDS, and its use is critical to good performance\. The feature is enabled by default for all new Amazon RDS PostgreSQL DB instances, and the related configuration parameters are appropriately set by default\. 

 Your database administrator needs to know and understand this maintenance operation\. For the PostgreSQL documentation on autovacuum, see [ Routine Vacuuming](http://www.postgresql.org/docs/current/static/routine-vacuuming.html#AUTOVACUUM)\. 

 Autovacuum is not a “resource free” operation, but it works in the background and yields to user operations as much as possible\. When enabled, autovacuum checks for tables that have had a large number of updated or deleted tuples\. It also protects against loss of very old data due to transaction ID wraparound\. For more information, see [Preventing Transaction ID Wraparound Failures](https://www.postgresql.org/docs/current/routine-vacuuming.html#VACUUM-FOR-WRAPAROUND)\.

 Autovacuum should not be thought of as a high\-overhead operation that can be reduced to gain better performance\. On the contrary, tables that have a high velocity of updates and deletes will quickly deteriorate over time if autovacuum is not run\. 

**Important**  
 Not running autovacuum can result in an eventual required outage to perform a much more intrusive vacuum operation\. When an Amazon RDS PostgreSQL DB instance becomes unavailable because of an over conservative use of autovacuum, the PostgreSQL database will shut down to protect itself\. At that point, Amazon RDS must perform a single\-user\-mode full vacuum directly on the DB instance , which can result in a multi\-hour outage\.* *Thus, we strongly recommend that you do not turn off autovacuum, which is enabled by default\. 

The autovacuum parameters determine when and how hard autovacuum works\. The` autovacuum_vacuum_threshold` and `autovacuum_vacuum_scale_factor` parameters determine when autovacuum is run\. The `autovacuum_max_workers`, `autovacuum_nap_time`, `autovacuum_cost_limit`, and `autovacuum_cost_delay` parameters determine how hard autovacuum works\. For more information about autovacuum, when it runs, and what parameters are required, see the [PostgreSQL documentation](https://www.postgresql.org/docs/current/routine-vacuuming.html)\.

 The following query shows the number of "dead" tuples in a table named table1 : 

```
PROMPT> select relname, n_dead_tup, last_vacuum, last_autovacuum from 
pg_catalog.pg_stat_all_tables
where n_dead_tup > 0 and relname =  'table1';
```

The results of the query will resemble the following:

```
relname | n_dead_tup | last_vacuum | last_autovacuum
---------+------------+-------------+-----------------
 tasks   |   81430522 |             |
(1 row)
```

## Best Practices for Working with SQL Server<a name="CHAP_BestPractices.SQLServer"></a>

Best practices for a Multi\-AZ deployment with a SQL Server DB instance include the following:
+ Use Amazon RDS DB events to monitor failovers\. For example, you can be notified by text message or email when a DB instance fails over\. For more information about Amazon RDS events, see [Using Amazon RDS Event Notification](USER_Events.md)\.
+ If your application caches DNS values, set time to live \(TTL\) to less than 30 seconds\. Setting TTL as so is a good practice in case there is a failover, where the IP address might change and the cached value might no longer be in service\.
+ We recommend that you *do not* enable the following modes because they turn off transaction logging, which is required for Multi\-AZ: 
  + Simple recover mode
  + Offline mode
  + Read\-only mode
+ Test to determine how long it takes for your DB instance to failover\. Failover time can vary due to the type of database, the instance class, and the storage type you use\. You should also test your application's ability to continue working if a failover occurs\.
+ To shorten failover time, you should do the following:
  + Ensure that you have sufficient Provisioned IOPS allocated for your workload\. Inadequate I/O can lengthen failover times\. Database recovery requires I/O\.
  + Use smaller transactions\. Database recovery relies on transactions, so if you can break up large transactions into multiple smaller transactions, your failover time should be shorter\.
+ Take into consideration that during a failover, there will be elevated latencies\. As part of the failover process, Amazon RDS automatically replicates your data to a new standby instance\. This replication means that new data is being committed to two different DB instances, so there might be some latency until the standby DB instance has caught up to the new primary DB instance\.
+ Deploy your applications in all Availability Zones\. If an Availability Zone does go down, your applications in the other Availability Zones will still be available\.

When working with a Multi\-AZ deployment of SQL Server, remember that Amazon RDS creates replicas for all SQL Server databases on your instance\. If you don't want specific databases to have secondary replicas, set up a separate DB instance that doesn't use Multi\-AZ for those databases\.

### Amazon RDS SQL Server Best Practices Video<a name="CHAP_BestPractices.SQLServer.Presentation"></a>

The 2019 AWS re:Invent conference included a presentation on new features and best practices for working with SQL Server on Amazon RDS\. A video of the presentation is available here:

[![AWS Videos](http://img.youtube.com/vi/https://www.youtube.com/embed/R4Vj88iqu5s/0.jpg)](http://www.youtube.com/watch?v=https://www.youtube.com/embed/R4Vj88iqu5s)

## Working with DB Parameter Groups<a name="CHAP_BestPractices.DBParameterGroup"></a>

We recommend that you try out DB parameter group changes on a test DB instance before applying parameter group changes to your production DB instances\. Improperly setting DB engine parameters in a DB parameter group can have unintended adverse effects, including degraded performance and system instability\. Always exercise caution when modifying DB engine parameters and back up your DB instance before modifying a DB parameter group\. 

For information about backing up your DB instance, see [Backing Up and Restoring an Amazon RDS DB Instance](CHAP_CommonTasks.BackupRestore.md)\.

## Amazon RDS New Features and Best Practices Presentation Video<a name="CHAP_BestPractices.Presentation"></a>

The 2019 AWS re:Invent conference included a presentation on new Amazon RDS features and best practices for monitoring, analyzing, and tuning database performance using RDS\. A video of the presentation is available here:

[![AWS Videos](http://img.youtube.com/vi/https://www.youtube.com/embed/KhxEQQOiqus/0.jpg)](http://www.youtube.com/watch?v=https://www.youtube.com/embed/KhxEQQOiqus)