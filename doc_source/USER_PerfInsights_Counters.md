# Customizing the Performance Insights Dashboard<a name="USER_PerfInsights_Counters"></a>

With counter metrics, you can customize the Performance Insights dashboard to include up to 10 additional graphs\. These graphs that show a selection of dozens of operating system and database performance metrics\. This information can be correlated with database load to help identify and analyze performance problems\.

**Topics**
+ [Performance Insights Operating System Counters](#USER_PerfInsights_Counters.OS)
+ [Performance Insights Counters for Amazon RDS for MariaDB and MySQL](#USER_PerfInsights_Counters.MySQL)
+ [Performance Insights Counters for Amazon RDS for Microsoft SQL Server](#USER_PerfInsights_Counters.SQLServer)
+ [Performance Insights Counters for Amazon RDS for Oracle](#USER_PerfInsights_Counters.Oracle)
+ [Performance Insights Counters for Amazon RDS for PostgreSQL](#USER_PerfInsights_Counters.PostgreSQL)

## Performance Insights Operating System Counters<a name="USER_PerfInsights_Counters.OS"></a>

The following operating system counters are available with Performance Insights for Aurora PostgreSQL\. You can find definitions for these metrics in [Viewing Enhanced Monitoring by Using CloudWatch Logs](USER_Monitoring.OS.md#USER_Monitoring.OS.CloudWatchLogs)\. 


| Counter | Type | Metric | 
| --- | --- | --- | 
| active | memory | os\.memory\.active | 
| buffers | memory | os\.memory\.buffers | 
| cached | memory | os\.memory\.cached | 
| dirty | memory | os\.memory\.dirty | 
| free | memory | os\.memory\.free | 
| hugePagesFree | memory | os\.memory\.hugePagesFree | 
| hugePagesRsvd | memory | os\.memory\.hugePagesRsvd | 
| hugePagesSize | memory | os\.memory\.hugePagesSize | 
| hugePagesSurp | memory | os\.memory\.hugePagesSurp | 
| hugePagesTotal | memory | os\.memory\.hugePagesTotal | 
| inactive | memory | os\.memory\.inactive | 
| mapped | memory | os\.memory\.mapped | 
| pageTables | memory | os\.memory\.pageTables | 
| slab | memory | os\.memory\.slab | 
| total | memory | os\.memory\.total | 
| writeback | memory | os\.memory\.writeback | 
| guest | cpuUtilization | os\.cpuUtilization\.guest | 
| idle | cpuUtilization | os\.cpuUtilization\.idle | 
| irq | cpuUtilization | os\.cpuUtilization\.irq | 
| nice | cpuUtilization | os\.cpuUtilization\.nice | 
| steal | cpuUtilization | os\.cpuUtilization\.steal | 
| system | cpuUtilization | os\.cpuUtilization\.system | 
| total | cpuUtilization | os\.cpuUtilization\.total | 
| user | cpuUtilization | os\.cpuUtilization\.user | 
| wait | cpuUtilization | os\.cpuUtilization\.wait | 
| avgQueueLen | diskIO | os\.diskIO\.avgQueueLen | 
| avgReqSz | diskIO | os\.diskIO\.avgReqSz | 
| await | diskIO | os\.diskIO\.await | 
| readIOsPS | diskIO | os\.diskIO\.readIOsPS | 
| readKb | diskIO | os\.diskIO\.readKb | 
| readKbPS | diskIO | os\.diskIO\.readKbPS | 
| rrqmPS | diskIO | os\.diskIO\.rrqmPS | 
| tps | diskIO | os\.diskIO\.tps | 
| util | diskIO | os\.diskIO\.util | 
| writeIOsPS | diskIO | os\.diskIO\.writeIOsPS | 
| writeKb | diskIO | os\.diskIO\.writeKb | 
| writeKbPS | diskIO | os\.diskIO\.writeKbPS | 
| wrqmPS | diskIO | os\.diskIO\.wrqmPS | 
| blocked | tasks | os\.tasks\.blocked | 
| running | tasks | os\.tasks\.running | 
| sleeping | tasks | os\.tasks\.sleeping | 
| stopped | tasks | os\.tasks\.stopped | 
| total | tasks | os\.tasks\.total | 
| zombie | tasks | os\.tasks\.zombie | 
| one | loadAverageMinute | os\.loadAverageMinute\.one | 
| fifteen | loadAverageMinute | os\.loadAverageMinute\.fifteen | 
| five | loadAverageMinute | os\.loadAverageMinute\.five | 
| cached | swap | os\.swap\.cached | 
| free | swap | os\.swap\.free | 
| in | swap | os\.swap\.in | 
| out | swap | os\.swap\.out | 
| total | swap | os\.swap\.total | 
| maxFiles | fileSys | os\.fileSys\.maxFiles | 
| usedFiles | fileSys | os\.fileSys\.usedFiles | 
| usedFilePercent | fileSys | os\.fileSys\.usedFilePercent | 
| usedPercent | fileSys | os\.fileSys\.usedPercent | 
| used | fileSys | os\.fileSys\.used | 
| total | fileSys | os\.fileSys\.total | 
| rx | network | os\.network\.rx | 
| tx | network | os\.network\.tx | 
| numVCPUs | general | os\.general\.numVCPUs | 

## Performance Insights Counters for Amazon RDS for MariaDB and MySQL<a name="USER_PerfInsights_Counters.MySQL"></a>

The following database counters are available with Performance Insights for Amazon RDS for MariaDB and MySQL\.

**Topics**
+ [Native Counters for RDS MariaDB and RDS MySQL](#USER_PerfInsights_Counters.MySQL.Native)
+ [Non\-Native Counters for Amazon RDS for MariaDB and MySQL](#USER_PerfInsights_Counters.MySQL.NonNative)

### Native Counters for RDS MariaDB and RDS MySQL<a name="USER_PerfInsights_Counters.MySQL.Native"></a>

You can find definitions for these native metrics in [Server Status Variables](https://dev.mysql.com/doc/refman/5.6/en/server-status-variables.html) in the MySQL documentation\.


| Counter | Type | Unit | Metric | 
| --- | --- | --- | --- | 
| Com\_analyze | SQL | Queries per second | db\.SQL\.Com\_analyze | 
| Com\_optimize | SQL | Queries per second | db\.SQL\.Com\_optimize | 
| Com\_select | SQL | Queries per second | db\.SQL\.Com\_select | 
| Innodb\_rows\_deleted | SQL | Rows per second | db\.SQL\.Innodb\_rows\_deleted | 
| Innodb\_rows\_inserted | SQL | Rows per second | db\.SQL\.Innodb\_rows\_inserted | 
| Innodb\_rows\_read | SQL | Rows per second | db\.SQL\.Innodb\_rows\_read | 
| Innodb\_rows\_updated | SQL | Rows per second | db\.SQL\.Innodb\_rows\_updated | 
| Select\_full\_join | SQL | Queries per second | db\.SQL\.Select\_full\_join | 
| Select\_full\_range\_join | SQL | Queries per second | db\.SQL\.Select\_full\_range\_join | 
| Select\_range | SQL | Queries per second | db\.SQL\.Select\_range | 
| Select\_range\_check | SQL | Queries per second | db\.SQL\.Select\_range\_check | 
| Select\_scan | SQL | Queries per second | db\.SQL\.Select\_scan | 
| Slow\_queries | SQL | Queries per second | db\.SQL\.Slow\_queries | 
| Sort\_merge\_passes | SQL | Queries per second | db\.SQL\.Sort\_merge\_passes | 
| Sort\_range | SQL | Queries per second | db\.SQL\.Sort\_range | 
| Sort\_rows | SQL | Queries per second | db\.SQL\.Sort\_rows | 
| Sort\_scan | SQL | Queries per second | db\.SQL\.Sort\_scan | 
| Questions | SQL | Queries per second | db\.SQL\.Questions | 
| Innodb\_row\_lock\_time | Locks | Milliseconds \(average\) | db\.Locks\.Innodb\_row\_lock\_time | 
| Table\_locks\_immediate | Locks | Requests per second | db\.Locks\.Table\_locks\_immediate | 
| Table\_locks\_waited | Locks | Requests per second | db\.Locks\.Table\_locks\_waited | 
| Aborted\_clients | Users | Connections | db\.Users\.Aborted\_clients | 
| Aborted\_connects | Users | Connections | db\.Users\.Aborted\_connects | 
| Threads\_created | Users | Connections | db\.Users\.Threads\_created | 
| Threads\_running | Users | Connections | db\.Users\.Threads\_running | 
| Innodb\_data\_writes | I/O | Operations per second | db\.IO\.Innodb\_data\_writes | 
| Innodb\_dblwr\_writes | I/O | Operations per second | db\.IO\.Innodb\_dblwr\_writes | 
| Innodb\_log\_write\_requests | I/O | Operations per second | db\.IO\.Innodb\_log\_write\_requests | 
| Innodb\_log\_writes | I/O | Operations per second | db\.IO\.Innodb\_log\_writes | 
| Innodb\_pages\_written | I/O | Pages per second | db\.IO\.Innodb\_pages\_written | 
| Created\_tmp\_disk\_tables | Temp | Tables per second | db\.Temp\.Created\_tmp\_disk\_tables | 
| Created\_tmp\_tables | Temp | Tables per second | db\.Temp\.Created\_tmp\_tables | 
| Innodb\_buffer\_pool\_pages\_data | Cache | Pages | db\.Cache\.Innodb\_buffer\_pool\_pages\_data | 
| Innodb\_buffer\_pool\_pages\_total | Cache | Pages | db\.Cache\.Innodb\_buffer\_pool\_pages\_total | 
| Innodb\_buffer\_pool\_read\_requests | Cache | Pages per second | db\.Cache\.Innodb\_buffer\_pool\_read\_requests | 
| Innodb\_buffer\_pool\_reads | Cache | Pages per second | db\.Cache\.Innodb\_buffer\_pool\_reads | 
| Opened\_tables | Cache | Tables | db\.Cache\.Opened\_tables | 
| Opened\_table\_definitions | Cache | Tables | db\.Cache\.Opened\_table\_definitions | 
| Qcache\_hits | Cache | Queries | db\.Cache\.Qcache\_hits | 

### Non\-Native Counters for Amazon RDS for MariaDB and MySQL<a name="USER_PerfInsights_Counters.MySQL.NonNative"></a>

Non\-native counter metrics are counters defined by Amazon RDS\. A non\-native metric can be a metric that you get with a specific query\. A non\-native metric also can be a derived metric, where two or more native counters are used in calculations for ratios, hit rates, or latencies\.


| Counter | Type | Metric | Description | Definition | 
| --- | --- | --- | --- | --- | 
| innodb\_buffer\_pool\_hits | Cache | db\.Cache\.innodb\_buffer\_pool\_hits | The number of reads that InnoDB could satisfy from the buffer pool\. | innodb\_buffer\_pool\_read\_requests \- innodb\_buffer\_pool\_reads | 
| innodb\_buffer\_pool\_hit\_rate | Cache | db\.Cache\.innodb\_buffer\_pool\_hit\_rate | The percentage of reads that InnoDB could satisfy from the buffer pool\. | 100 \* innodb\_buffer\_pool\_read\_requests / \(innodb\_buffer\_pool\_read\_requests \+ innodb\_buffer\_pool\_reads\) | 
| innodb\_buffer\_pool\_usage | Cache | db\.Cache\.innodb\_buffer\_pool\_usage |  The percentage of the InnoDB buffer pool that contains data \(pages\)\.  When using compressed tables, this value can vary\. For more information, see the information about `Innodb_buffer_pool_pages_data` and `Innodb_buffer_pool_pages_total` in [Server Status Variables](https://dev.mysql.com/doc/refman/5.6/en/server-status-variables.html) in the MySQL documentation\.   | Innodb\_buffer\_pool\_pages\_data / Innodb\_buffer\_pool\_pages\_total \* 100\.0 | 
| query\_cache\_hit\_rate | Cache | db\.Cache\.query\_cache\_hit\_rate | MySQL result set cache \(query cache\) hit ratio\. | Qcache\_hits / \(QCache\_hits \+ Com\_select\) \* 100 | 
| innodb\_datafile\_writes\_to\_disk | I/O | db\.IO\.innodb\_datafile\_writes\_to\_disk | The number of InnoDB data file writes to disk, excluding double write and redo logging write operations\. | Innodb\_data\_writes \- Innodb\_log\_writes \- Innodb\_dblwr\_writes | 
| innodb\_rows\_changed | SQL | db\.SQL\.innodb\_rows\_changed | The total InnoDB row operations\. | db\.SQL\.Innodb\_rows\_inserted \+ db\.SQL\.Innodb\_rows\_deleted \+ db\.SQL\.Innodb\_rows\_updated | 
| active\_transactions | Transactions | db\.Transactions\.active\_transactions | The total active transactions\. | SELECT COUNT\(1\) AS active\_transactions FROM INFORMATION\_SCHEMA\.INNODB\_TRX | 
| innodb\_deadlocks | Locks | db\.Locks\.innodb\_deadlocks | The total number of deadlocks\. | SELECT COUNT AS innodb\_deadlocks FROM INFORMATION\_SCHEMA\.INNODB\_METRICS WHERE NAME='lock\_deadlocks' | 
| innodb\_lock\_timeouts | Locks | db\.Locks\.innodb\_lock\_timeouts | The total number of deadlocks that timed out\. | SELECT COUNT AS innodb\_lock\_timeouts FROM INFORMATION\_SCHEMA\.INNODB\_METRICS WHERE NAME='lock\_timeouts' | 
| innodb\_row\_lock\_waits | Locks | db\.Locks\.innodb\_row\_lock\_waits | The total number of row locks that resulted in a wait\. | SELECT COUNT AS innodb\_row\_lock\_waits FROM INFORMATION\_SCHEMA\.INNODB\_METRICS WHERE NAME='lock\_row\_lock\_waits' | 

## Performance Insights Counters for Amazon RDS for Microsoft SQL Server<a name="USER_PerfInsights_Counters.SQLServer"></a>

The following database counters are available with Performance Insights for RDS for Microsoft SQL Server\.

### Native Counters for RDS for Microsoft SQL Server<a name="USER_PerfInsights_Counters.SQLServer.Native"></a>

You can find definitions for these native metrics in [Use SQL Server Objects](https://docs.microsoft.com/en-us/sql/relational-databases/performance-monitor/use-sql-server-objects?view=sql-server-2017) in the Microsoft SQL Server documentation\.


| Counter | Type | Unit | Metric | 
| --- | --- | --- | --- | 
| Forwarded Records | [Access Methods](https://docs.microsoft.com/en-us/sql/relational-databases/performance-monitor/sql-server-access-methods-object?view=sql-server-2017) | Records per second | db\.Access Methods\.Forwarded Records | 
| Page Splits | [Access Methods](https://docs.microsoft.com/en-us/sql/relational-databases/performance-monitor/sql-server-access-methods-object?view=sql-server-2017) | Splits per second | db\.Access Methods\.Page Splits | 
| Buffer cache hit ratio | [Buffer Manager](https://docs.microsoft.com/en-us/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object?view=sql-server-2017) | Ratio | db\.Buffer Manager\.Buffer cache hit ratio | 
| Page life expectancy | [Buffer Manager](https://docs.microsoft.com/en-us/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object?view=sql-server-2017) | Expectancy in seconds | db\.Buffer Manager\.Page life expectancy | 
| Page lookups | [Buffer Manager](https://docs.microsoft.com/en-us/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object?view=sql-server-2017) | Lookups per second | db\.Buffer Manager\.Page lookups | 
| Page reads | [Buffer Manager](https://docs.microsoft.com/en-us/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object?view=sql-server-2017) | Reads per second | db\.Buffer Manager\.Page reads | 
| Page writes | [Buffer Manager](https://docs.microsoft.com/en-us/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object?view=sql-server-2017) | Writes per second | db\.Buffer Manager\.Page writes | 
| Active Transactions | [Databases](https://docs.microsoft.com/en-us/sql/relational-databases/performance-monitor/sql-server-databases-object?view=sql-server-2017) | Transactions | db\.Databases\.Active Transactions \(\_Total\) | 
| Log Bytes Flushed | [Databases](https://docs.microsoft.com/en-us/sql/relational-databases/performance-monitor/sql-server-databases-object?view=sql-server-2017) | Bytes flushed per second | db\.Databases\.Log Bytes Flushed \(\_Total\) | 
| Log Flush Waits | [Databases](https://docs.microsoft.com/en-us/sql/relational-databases/performance-monitor/sql-server-databases-object?view=sql-server-2017) | Waits per second | db\.Databases\.Log Flush Waits \(\_Total\) | 
| Log Flushes | [Databases](https://docs.microsoft.com/en-us/sql/relational-databases/performance-monitor/sql-server-databases-object?view=sql-server-2017) | Flushes per second | db\.Databases\.Log Flushes \(\_Total\) | 
| Write Transactions | [Databases](https://docs.microsoft.com/en-us/sql/relational-databases/performance-monitor/sql-server-databases-object?view=sql-server-2017) | Transactions per second | db\.Databases\.Write Transactions \(\_Total\) | 
| Processes blocked | [General Statistics](https://docs.microsoft.com/en-us/sql/relational-databases/performance-monitor/sql-server-general-statistics-object?view=sql-server-2017) | Processes blocked | db\.General Statistics\.Processes blocked | 
| User Connections | [General Statistics](https://docs.microsoft.com/en-us/sql/relational-databases/performance-monitor/sql-server-general-statistics-object?view=sql-server-2017) | Connections | db\.General Statistics\.User Connections | 
| Latch Waits | [Latches](https://docs.microsoft.com/en-us/sql/relational-databases/performance-monitor/sql-server-latches-object?view=sql-server-2017) | Waits per second | db\.Latches\.Latch Waits | 
| Number of Deadlocks | [Locks](https://docs.microsoft.com/en-us/sql/relational-databases/performance-monitor/sql-server-locks-object?view=sql-server-2017) | Deadlocks per second | db\.Locks\.Number of Deadlocks \(\_Total\) | 
| Memory Grants Pending | [Memory Manager](https://docs.microsoft.com/en-us/sql/relational-databases/performance-monitor/sql-server-memory-manager-object?view=sql-server-2017) | Memory grants | db\.Memory Manager\.Memory Grants Pending | 
| Batch Requests | [SQL Statistics](https://docs.microsoft.com/en-us/sql/relational-databases/performance-monitor/sql-server-sql-statistics-object?view=sql-server-2017) | Requests per second | db\.SQL Statistics\.Batch Requests | 
| SQL Compilations | [SQL Statistics](https://docs.microsoft.com/en-us/sql/relational-databases/performance-monitor/sql-server-sql-statistics-object?view=sql-server-2017) | Compilations per second | db\.SQL Statistics\.SQL Compilations | 
| SQL Re\-Compilations | [SQL Statistics](https://docs.microsoft.com/en-us/sql/relational-databases/performance-monitor/sql-server-sql-statistics-object?view=sql-server-2017) | Re\-compilations per second | db\.SQL Statistics\.SQL Re\-Compilations | 

## Performance Insights Counters for Amazon RDS for Oracle<a name="USER_PerfInsights_Counters.Oracle"></a>

The following database counters are available with Performance Insights for RDS for Oracle\.

### Native Counters for RDS for Oracle<a name="USER_PerfInsights_Counters.Oracle.Native"></a>

You can find definitions for these native metrics in [Statistics Descriptions](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/statistics-descriptions-2.html#GUID-2FBC1B7E-9123-41DD-8178-96176260A639) in the Oracle documentation\.

**Note**  
For the `CPU used by this session` counter metric, the unit has been transformed from the native centiseconds to active sessions to make the value easier to use\. For example, CPU send in the DB Load chart represents the demand for CPU\. The counter metric `CPU used by this session` represents the amount of CPU used by Oracle sessions\. You can compare CPU send to the `CPU used by this session` counter metric\. When demand for CPU is higher than CPU used, sessions are waiting for CPU time\.


| Counter | Type | Unit | Metric | 
| --- | --- | --- | --- | 
| CPU used by this session | User | Active sessions | db\.User\.CPU used by this session | 
| SQL\*Net roundtrips to/from client | User | Roundtrips per second | db\.User\.SQL\*Net roundtrips to/from client | 
| Bytes received via SQL\*Net from client | User | Bytes per second | db\.User\.bytes received via SQL\*Net from client | 
| User commits | User | Commits per second | db\.User\.user commits | 
| Logons cumulative | User | Logons per second | db\.User\.logons cumulative | 
| User calls | User | Calls per second | db\.User\.user calls | 
| Bytes sent via SQL\*Net to client | User | Bytes per second | db\.User\.bytes sent via SQL\*Net to client | 
| User rollbacks | User | Rollbacks per second | db\.User\.user rollbacks | 
| Redo size | Redo | Bytes per second | db\.Redo\.redo size | 
| Parse count \(total\) | SQL | Parses per second | db\.SQL\.parse count \(total\) | 
| Parse count \(hard\) | SQL | Parses per second | db\.SQL\.parse count \(hard\) | 
| Table scan rows gotten | SQL | Rows per second | db\.SQL\.table scan rows gotten | 
| Sorts \(memory\) | SQL | Sorts per second | db\.SQL\.sorts \(memory\) | 
| Sorts \(disk\) | SQL | Sorts per second | db\.SQL\.sorts \(disk\) | 
| Sorts \(rows\) | SQL | Sorts per second | db\.SQL\.sorts \(rows\) | 
| Physical read bytes | Cache | Bytes per second | db\.Cache\.physical read bytes | 
| DB block gets | Cache | Blocks per second | db\.Cache\.db block gets | 
| DBWR checkpoints | Cache | Checkpoints per minute | db\.Cache\.DBWR checkpoints | 
| Physical reads | Cache | Reads per second | db\.Cache\.physical reads | 
| Consistent gets from cache | Cache | Gets per second | db\.Cache\.consistent gets from cache | 
| DB block gets from cache | Cache | Gets per second | db\.Cache\.db block gets from cache | 
| Consistent gets | Cache | Gets per second | db\.Cache\.consistent gets | 

## Performance Insights Counters for Amazon RDS for PostgreSQL<a name="USER_PerfInsights_Counters.PostgreSQL"></a>

The following database counters are available with Performance Insights for Amazon RDS for PostgreSQL\.

**Topics**
+ [Native Counters for Amazon RDS for PostgreSQL](#USER_PerfInsights_Counters.PostgreSQL.Native)
+ [Non\-Native Counters for Amazon RDS for PostgreSQL](#USER_PerfInsights_Counters.PostgreSQL.NonNative)

### Native Counters for Amazon RDS for PostgreSQL<a name="USER_PerfInsights_Counters.PostgreSQL.Native"></a>

You can find definitions for these native metrics in [Viewing Statistics](https://www.postgresql.org/docs/10/monitoring-stats.html#MONITORING-STATS-VIEWS) in the PostgreSQL documentation\.


| Counter | Type | Unit | Metric | 
| --- | --- | --- | --- | 
| blks\_hit | Cache | Blocks per second | db\.Cache\.blks\_hit | 
| buffers\_alloc | Cache | Blocks per second | db\.Cache\.buffers\_alloc | 
| buffers\_checkpoint | Checkpoint | Blocks per second | db\.Checkpoint\.buffers\_checkpoint | 
| checkpoint\_sync\_time | Checkpoint | Milliseconds per checkpoint | db\.Checkpoint\.checkpoint\_sync\_time | 
| checkpoint\_write\_time | Checkpoint | Milliseconds per checkpoint | db\.Checkpoint\.checkpoint\_write\_time | 
| checkpoints\_req | Checkpoint | Checkpoints per minute | db\.Checkpoint\.checkpoints\_req | 
| checkpoints\_timed | Checkpoint | Checkpoints per minute | db\.Checkpoint\.checkpoints\_timed | 
| maxwritten\_clean | Checkpoint | Bgwriter clean stops per minute  | db\.Checkpoint\.maxwritten\_clean | 
| deadlocks | Concurrency | Deadlocks per minute | db\.Concurrency\.deadlocks | 
| blk\_read\_time | I/O | Milliseconds | db\.IO\.blk\_read\_time | 
| blks\_read | I/O | Blocks per second | db\.IO\.blks\_read | 
| buffers\_backend | I/O | Blocks per second | db\.IO\.buffers\_backend | 
| buffers\_backend\_fsync | I/O | Blocks per second | db\.IO\.buffers\_backend\_fsync | 
| buffers\_clean | I/O | Blocks per second | db\.IO\.buffers\_clean | 
| tup\_deleted | SQL | Tuples per second | db\.SQL\.tup\_deleted | 
| tup\_fetched | SQL | Tuples per second | db\.SQL\.tup\_fetched | 
| tup\_inserted | SQL | Tuples per second | db\.SQL\.tup\_inserted | 
| tup\_returned | SQL | Tuples per second | db\.SQL\.tup\_returned | 
| tup\_updated | SQL | Tuples per second | db\.SQL\.tup\_updated | 
| temp\_bytes | Temp | Bytes per second | db\.Temp\.temp\_bytes | 
| temp\_files | Temp | Files per minute | db\.Temp\.temp\_files | 
| active\_transactions | Transactions | Transactions | db\.Transactions\.active\_transactions | 
| blocked\_transactions | Transactions | Transactions | db\.Transactions\.blocked\_transactions | 
| max\_used\_xact\_ids | Transactions | Transactions | db\.Transactions\.max\_used\_xact\_ids | 
| xact\_commit | Transactions | Commits per second | db\.Transactions\.xact\_commit | 
| xact\_rollback | Transactions | Rollbacks per second | db\.Transactions\.xact\_rollback | 
| numbackends | User | Connections | db\.User\.numbackends | 
| archived\_count | Write\-ahead log \(WAL\) | Files per minute | db\.WAL\.archived\_count | 
| archive\_failed\_count | WAL | Files per minute | db\.WAL\.archive\_failed\_count | 

### Non\-Native Counters for Amazon RDS for PostgreSQL<a name="USER_PerfInsights_Counters.PostgreSQL.NonNative"></a>

Non\-native counter metrics are counters defined by Amazon RDS\. A non\-native metric can be a metric that you get with a specific query\. A non\-native metric also can be a derived metric, where two or more native counters are used in calculations for ratios, hit rates, or latencies\.


| Counter | Type | Metric | Description | Definition | 
| --- | --- | --- | --- | --- | 
| checkpoint\_sync\_latency | Checkpoint | db\.Checkpoint\.checkpoint\_sync\_latency | The total amount of time that has been spent in the portion of checkpoint processing where files are synchronized to disk\. | checkpoint\_sync\_time / \(checkpoints\_timed \+ checkpoints\_req\) | 
| checkpoint\_write\_latency | Checkpoint | db\.Checkpoint\.checkpoint\_write\_latency | The total amount of time that has been spent in the portion of checkpoint processing where files are written to disk\. | checkpoint\_write\_time / \(checkpoints\_timed \+ checkpoints\_req\) | 
| read\_latency | I/O | db\.IO\.read\_latency | The time spent reading data file blocks by backends in this instance\. | blk\_read\_time / blks\_read | 