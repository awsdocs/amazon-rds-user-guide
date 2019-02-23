# Performance Insights Counters<a name="USER_PerfInsights_Counters"></a>

With counter metrics, you can customize the Performance Insights dashboard to include up to 10 additional graphs that show a selection of dozens of operating system and database performance metrics\. This information can be correlated with database load to help identify and analyze performance problems\.

**Topics**
+ [Performance Insights Operating System Counters](#USER_PerfInsights_Counters.OS)
+ [Performance Insights Counters for Amazon RDS MySQL](#USER_PerfInsights_Counters.MySQL)
+ [Performance Insights Counters for Amazon RDS PostgreSQL](#USER_PerfInsights_Counters.PostgreSQL)

## Performance Insights Operating System Counters<a name="USER_PerfInsights_Counters.OS"></a>

The following operating system counters are available with Performance Insights for Aurora PostgreSQL\. You can find definitions for these metrics in [Viewing Enhanced Monitoring by Using CloudWatch Logs](USER_Monitoring.OS.md#USER_Monitoring.OS.CloudWatchLogs)\. 


| Counter | Type | 
| --- | --- | 
|  active  |  memory  | 
| buffers | memory | 
| cached | memory | 
| dirty | memory | 
| free | memory | 
| inactive | memory | 
| hugePagesFree | memory | 
| hugePagesRsvd | memory | 
| hugePagesSize | memory | 
| hugePagesSurp | memory | 
| hugePagesTotal | memory | 
| mapped | memory | 
| pageTables | memory | 
| slab | memory | 
| total | memory | 
| writeback | memory | 
| guest | cpuUtilization | 
| idle | cpuUtilization | 
| irq | cpuUtilization | 
| nice | cpuUtilization | 
| steal | cpuUtilization | 
| system | cpuUtilization | 
| total | cpuUtilization | 
| user | cpuUtilization | 
| wait | cpuUtilization | 
| avgQueueLen | diskIO | 
| avgReqSz | diskIO | 
| await | diskIO | 
| readIOsPS | diskIO | 
| readKb | diskIO | 
| readKbPS | diskIO | 
| rrqmPS | diskIO | 
| tps | diskIO | 
| util | diskIO | 
| writeIOsPS | diskIO | 
| writeKb | diskIO | 
| writeKbPS | diskIO | 
| wrqmPS | diskIO | 
| blocked | tasks | 
| running | tasks | 
| sleeping | tasks | 
| stopped | tasks | 
| total | tasks | 
| zombie | tasks | 
| one | loadAverageMinute | 
| fifteen | loadAverageMinute | 
| five | loadAverageMinute | 
| cached | swap | 
| free | swap | 
| in | swap | 
| out | swap | 
| total | swap | 
| maxFiles | fileSys | 
| usedFiles | fileSys | 
| usedFilePercent | fileSys | 
| usedPercent | fileSys | 
| used | fileSys | 
| total | fileSys | 
| rx | network | 
| tx | network | 
| numVCPUs | general | 

## Performance Insights Counters for Amazon RDS MySQL<a name="USER_PerfInsights_Counters.MySQL"></a>

The following database counters are available with Performance Insights for Amazon RDS MySQL\.

**Topics**
+ [Native Counters for Amazon RDS MySQL](#USER_PerfInsights_Counters.MySQL.Native)
+ [Non\-Native Counters for Amazon RDS MySQL](#USER_PerfInsights_Counters.MySQL.NonNative)

### Native Counters for Amazon RDS MySQL<a name="USER_PerfInsights_Counters.MySQL.Native"></a>

You can find definitions for these native metrics in [Server Status Variables](https://dev.mysql.com/doc/refman/5.6/en/server-status-variables.html) in the MySQL documentation\.


| Counter | Type | Unit | 
| --- | --- | --- | 
| Com\_analyze | SQL | Queries per second | 
| Com\_optimize | SQL | Queries per second | 
| Com\_select | SQL | Queries per second | 
| Innodb\_rows\_deleted | SQL | Rows per second | 
| Innodb\_rows\_inserted | SQL | Rows per second | 
| Innodb\_rows\_read | SQL | Rows per second | 
| Innodb\_rows\_updated | SQL | Rows per second | 
| Select\_full\_join | SQL | Queries per second | 
| Select\_full\_range\_join | SQL | Queries per second | 
| Select\_range | SQL | Queries per second | 
| Select\_range\_check | SQL | Queries per second | 
| Select\_scan | SQL | Queries per second | 
| Slow\_queries | SQL | Queries per second | 
| Sort\_merge\_passes | SQL | Queries per second | 
| Sort\_range | SQL | Queries per second | 
| Sort\_rows | SQL | Queries per second | 
| Sort\_scan | SQL | Queries per second | 
| Questions | SQL | Queries per second | 
| Table\_locks\_immediate | Locks | Requests per second | 
| Table\_locks\_waited | Locks | Requests per second | 
| Innodb\_row\_lock\_time | Locks | Milliseconds \(average\) | 
| Aborted\_clients | Users | Connections | 
| Aborted\_connects | Users | Connections | 
| Threads\_created | Users | Connections | 
| Threads\_running | Users | Connections | 
| Innodb\_data\_writes | IO | Operations per second | 
| Innodb\_dblwr\_writes | IO | Operations per second | 
| Innodb\_log\_write\_requests | IO | Operations per second | 
| Innodb\_log\_writes | IO | Operations per second | 
| Innodb\_pages\_written | IO | Pages per second | 
| Created\_tmp\_disk\_tables | Temp | Tables per second | 
| Created\_tmp\_tables | Temp | Tables per second | 
| Innodb\_buffer\_pool\_pages\_data | Cache | Pages | 
| Innodb\_buffer\_pool\_pages\_total | Cache | Pages | 
| Innodb\_buffer\_pool\_read\_requests | Cache | Pages per second | 
| Innodb\_buffer\_pool\_reads | Cache | Pages per second | 
| Qcache\_hits | Cache | Queries | 

### Non\-Native Counters for Amazon RDS MySQL<a name="USER_PerfInsights_Counters.MySQL.NonNative"></a>

Non\-native counter metrics are counters defined by Amazon RDS\. A non\-native metric can be a metric that you get with a specific query\. A non\-native metric also can be a derived metric, where two or more native counters are used in calculations for ratios, hit rates, or latencies\.


| Counter | Type | Description | Definition | 
| --- | --- | --- | --- | 
| innodb\_buffer\_pool\_hits | Cache | The number of reads that InnoDB could satisfy from the buffer pool\. | innodb\_buffer\_pool\_read\_requests \- innodb\_buffer\_pool\_reads | 
| innodb\_buffer\_pool\_hit\_rate | Cache | The percentage of reads that InnoDB could satisfy from the buffer pool\. | 100 \* innodb\_buffer\_pool\_read\_requests / \(innodb\_buffer\_pool\_read\_requests \+ innodb\_buffer\_pool\_reads\) | 
| innodb\_buffer\_pool\_usage | Cache |  The percentage of the InnoDB buffer pool that contains data \(pages\)\.  When using compressed tables, this value can vary\. For more information, see the information about `Innodb_buffer_pool_pages_data` and `Innodb_buffer_pool_pages_total` in [Server Status Variables](https://dev.mysql.com/doc/refman/5.6/en/server-status-variables.html) in the MySQL documentation\.   | Innodb\_buffer\_pool\_pages\_data / Innodb\_buffer\_pool\_pages\_total \* 100\.0 | 
| query\_cache\_hit\_rate | Cache | MySQL result set cache \(query cache\) hit ratio\. | Qcache\_hits / \(QCache\_hits \+ Com\_select\) \* 100 | 
| innodb\_datafile\_writes\_to\_disk | IO | The number of InnoDB data file writes to disk, excluding double write and redo logging write operations\. | Innodb\_data\_writes \- Innodb\_log\_writes \- Innodb\_dblwr\_writes | 
| innodb\_rows\_changed | SQL | The total InnoDB row operations\. | db\.SQL\.Innodb\_rows\_inserted \+ db\.SQL\.Innodb\_rows\_deleted \+ db\.SQL\.Innodb\_rows\_updated | 
| active\_transactions | Transactions | The total active transactions\. | SELECT COUNT\(1\) AS active\_transactions FROM INFORMATION\_SCHEMA\.INNODB\_TRX | 
| innodb\_deadlocks | Locks | The total number of deadlocks\. | SELECT COUNT AS innodb\_deadlocks FROM INFORMATION\_SCHEMA\.INNODB\_METRICS WHERE NAME='lock\_deadlocks' | 
| innodb\_lock\_timeouts | Locks | The total number of deadlocks that timed out\. | SELECT COUNT AS innodb\_lock\_timeouts FROM INFORMATION\_SCHEMA\.INNODB\_METRICS WHERE NAME='lock\_timeouts' | 
| innodb\_row\_lock\_waits | Locks | The total number of row locks that resulted in a wait\. | SELECT COUNT AS innodb\_row\_lock\_waits FROM INFORMATION\_SCHEMA\.INNODB\_METRICS WHERE NAME='lock\_row\_lock\_waits' | 

## Performance Insights Counters for Amazon RDS PostgreSQL<a name="USER_PerfInsights_Counters.PostgreSQL"></a>

The following database counters are available with Performance Insights for Amazon RDS PostgreSQL\.

**Topics**
+ [Native Counters for Amazon RDS PostgreSQL](#USER_PerfInsights_Counters.PostgreSQL.Native)
+ [Non\-Native Counters for Amazon RDS PostgreSQL](#USER_PerfInsights_Counters.PostgreSQL.NonNative)

### Native Counters for Amazon RDS PostgreSQL<a name="USER_PerfInsights_Counters.PostgreSQL.Native"></a>

You can find definitions for these native metrics in [Viewing Statistics](https://www.postgresql.org/docs/10/monitoring-stats.html#MONITORING-STATS-VIEWS) in the PostgreSQL documentation\.


| Counter | Type | Unit | 
| --- | --- | --- | 
| blks\_hit | Cache | Blocks per second | 
| buffers\_alloc | Cache | Blocks per second | 
| buffers\_checkpoint | Checkpoint | Blocks per second | 
| checkpoint\_sync\_time | Checkpoint | Milliseconds per checkpoint | 
| checkpoint\_write\_time | Checkpoint | Milliseconds per checkpoint | 
| checkpoints\_req | Checkpoint | Checkpoints per minute | 
| checkpoints\_timed | Checkpoint | Checkpoints per minute | 
| maxwritten\_clean | Checkpoint | Bgwriter clean stops per minute  | 
| time\_since\_checkpoint | Checkpoint | Seconds | 
| deadlocks | Concurrency | Deadlocks per minute | 
| blk\_read\_time | IO | Milliseconds | 
| blks\_read | IO | Blocks per second | 
| buffers\_backend | IO | Blocks per second | 
| buffers\_backend\_fsync | IO | Blocks per second | 
| buffers\_clean | IO | Blocks per second | 
| tup\_deleted | SQL | Tuples per second | 
| tup\_fetched | SQL | Tuples per second | 
| tup\_inserted | SQL | Tuples per second | 
| tup\_returned | SQL | Tuples per second | 
| tup\_updated | SQL | Tuples per second | 
| temp\_bytes | Temp | Bytes per second | 
| temp\_files | Temp | Files per minute | 
| active\_transactions | Transactions | Transactions | 
| blocked\_transactions | Transactions | Transactions | 
| max\_used\_xact\_ids | Transactions | Transactions | 
| xact\_commit | Transactions | Commits per second | 
| xact\_rollback | Transactions | Rollbacks per second | 
| numbackends | User | Connections | 
| archived\_count | WAL | Files per minute | 
| archive\_failed\_count | WAL | Files per minute | 

### Non\-Native Counters for Amazon RDS PostgreSQL<a name="USER_PerfInsights_Counters.PostgreSQL.NonNative"></a>

Non\-native counter metrics are counters defined by Amazon RDS\. A non\-native metric can be a metric that you get with a specific query\. A non\-native metric also can be a derived metric, where two or more native counters are used in calculations for ratios, hit rates, or latencies\.


| Counter | Type | Description | Definition | 
| --- | --- | --- | --- | 
| checkpoint\_sync\_latency | Checkpoint | The total amount of time that has been spent in the portion of checkpoint processing where files are synchronized to disk\. | checkpoint\_sync\_time / \(checkpoints\_timed \+ checkpoints\_req\) | 
| checkpoint\_write\_latency | Checkpoint | The total amount of time that has been spent in the portion of checkpoint processing where files are written to disk\. | checkpoint\_write\_time / \(checkpoints\_timed \+ checkpoints\_req\) | 
| read\_latency | IO | The time spent reading data file blocks by backends in this instance\. | blk\_read\_time / blks\_read | 