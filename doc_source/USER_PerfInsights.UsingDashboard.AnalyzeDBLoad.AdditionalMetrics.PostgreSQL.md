# SQL statistics for RDS PostgreSQL<a name="USER_PerfInsights.UsingDashboard.AnalyzeDBLoad.AdditionalMetrics.PostgreSQL"></a>

For each SQL call and for each second that a query runs, Performance Insights collects SQL statistics\. RDS for PostgreSQL collect SQL statistics only at the digest–level\. No statistics are shown at the statement\-level\. 

Following, you can find information about digest\-level statistics for RDS for PostgreSQL\. 

**Topics**
+ [Digest statistics for RDS PostgreSQL](#USER_PerfInsights.UsingDashboard.AnalyzeDBLoad.AdditionalMetrics.PostgreSQL.digest)
+ [Per\-second digest statistics for RDS PostgreSQL](#USER_PerfInsights.UsingDashboard.AnalyzeDBLoad.AdditionalMetrics.PostgreSQL.per-second)
+ [Per\-call digest statistics for RDS PostgreSQL](#USER_PerfInsights.UsingDashboard.AnalyzeDBLoad.AdditionalMetrics.PostgreSQL.per-call)

## Digest statistics for RDS PostgreSQL<a name="USER_PerfInsights.UsingDashboard.AnalyzeDBLoad.AdditionalMetrics.PostgreSQL.digest"></a>

To view SQL digest statistics, RDS PostgreSQL must load the `pg_stat_statements` library\. For PostgreSQL DB instances that are compatible with PostgreSQL 11 or later, the database loads this library by default\. For PostgreSQL DB instances that are compatible with PostgreSQL 10 or earlier, enable this library manually\. To enable it manually, add `pg_stat_statements` to `shared_preload_libraries` in the DB parameter group associated with the DB instance\. Then reboot your DB instance\. For more information, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\.

**Note**  
Performance Insights can only collect statistics for queries in `pg_stat_activity` that aren't truncated\. By default, PostgreSQL databases truncate queries longer than 1,024 bytes\. To increase the query size, change the `track_activity_query_size` parameter in the DB parameter group associated with your DB instance\. When you change this parameter, a DB instance reboot is required\.

## Per\-second digest statistics for RDS PostgreSQL<a name="USER_PerfInsights.UsingDashboard.AnalyzeDBLoad.AdditionalMetrics.PostgreSQL.per-second"></a>

The following SQL digest statistics are available for PostgreSQL DB instances\.


| Metric | Unit | 
| --- | --- | 
| db\.sql\_tokenized\.stats\.calls\_per\_sec | Calls per second | 
| db\.sql\_tokenized\.stats\.rows\_per\_sec | Rows per second | 
| db\.sql\_tokenized\.stats\.total\_time\_per\_sec | Average active executions per second \(AAE\) | 
| db\.sql\_tokenized\.stats\.shared\_blks\_hit\_per\_sec | Block hits per second | 
| db\.sql\_tokenized\.stats\.shared\_blks\_read\_per\_sec | Block reads per second | 
| db\.sql\_tokenized\.stats\.shared\_blks\_dirtied\_per\_sec | Blocks dirtied per second | 
| db\.sql\_tokenized\.stats\.shared\_blks\_written\_per\_sec | Block writes per second | 
| db\.sql\_tokenized\.stats\.local\_blks\_hit\_per\_sec | Local block hits per second | 
| db\.sql\_tokenized\.stats\.local\_blks\_read\_per\_sec | Local block reads per second | 
| db\.sql\_tokenized\.stats\.local\_blks\_dirtied\_per\_sec | Local block dirty per second | 
| db\.sql\_tokenized\.stats\.local\_blks\_written\_per\_sec | Local block writes per second | 
| db\.sql\_tokenized\.stats\.temp\_blks\_written\_per\_sec | Temporary writes per second | 
| db\.sql\_tokenized\.stats\.temp\_blks\_read\_per\_sec | Temporary reads per second | 
| db\.sql\_tokenized\.stats\.blk\_read\_time\_per\_sec | Average concurrent reads per second | 
| db\.sql\_tokenized\.stats\.blk\_write\_time\_per\_sec | Average concurrent writes per second | 

## Per\-call digest statistics for RDS PostgreSQL<a name="USER_PerfInsights.UsingDashboard.AnalyzeDBLoad.AdditionalMetrics.PostgreSQL.per-call"></a>

The following metrics provide per call statistics for a SQL statement\.


| Metric | Unit | 
| --- | --- | 
| db\.sql\_tokenized\.stats\.rows\_per\_call | Rows per call | 
| db\.sql\_tokenized\.stats\.avg\_latency\_per\_call | Average latency per call \(in ms\) | 
| db\.sql\_tokenized\.stats\.shared\_blks\_hit\_per\_call | Block hits per call | 
| db\.sql\_tokenized\.stats\.shared\_blks\_read\_per\_call | Block reads per call | 
| db\.sql\_tokenized\.stats\.shared\_blks\_written\_per\_call | Block writes per call | 
| db\.sql\_tokenized\.stats\.shared\_blks\_dirtied\_per\_call | Blocks dirtied per call | 
| db\.sql\_tokenized\.stats\.local\_blks\_hit\_per\_call | Local block hits per call | 
| db\.sql\_tokenized\.stats\.local\_blks\_read\_per\_call | Local block reads per call | 
| db\.sql\_tokenized\.stats\.local\_blks\_dirtied\_per\_call | Local block dirty per call | 
| db\.sql\_tokenized\.stats\.local\_blks\_written\_per\_call | Local block writes per call | 
| db\.sql\_tokenized\.stats\.temp\_blks\_written\_per\_call | Temporary block writes per call | 
| db\.sql\_tokenized\.stats\.temp\_blks\_read\_per\_call | Temporary block reads per call | 
| db\.sql\_tokenized\.stats\.blk\_read\_time\_per\_call | Read time per call \(in ms\) | 
| db\.sql\_tokenized\.stats\.blk\_write\_time\_per\_call | Write time per call \(in ms\) | 

For more information about these metrics, see [pg\_stat\_statements](https://www.postgresql.org/docs/current/pgstatstatements.html) in the PostgreSQL documentation\.