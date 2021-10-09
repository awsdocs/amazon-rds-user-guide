# Analyzing running queries in PostgreSQL<a name="USER_PerfInsights.UsingDashboard.AnalyzeDBLoad.AdditionalMetrics.PostgreSQL"></a>

PostgreSQL collects SQL statistics only at the digest level\. No statistics are shown at the statement level\.

**Topics**
+ [Digest statistics for RDS PostgreSQL](#USER_PerfInsights.UsingDashboard.AnalyzeDBLoad.AdditionalMetrics.PostgreSQL.digest)
+ [Per\-second digest statistics for PostgreSQL](#USER_PerfInsights.UsingDashboard.AnalyzeDBLoad.AdditionalMetrics.PostgreSQL.per-second)
+ [Per\-call digest statistics for PostgreSQL](#USER_PerfInsights.UsingDashboard.AnalyzeDBLoad.AdditionalMetrics.PostgreSQL.per-call)
+ [Viewing SQL statistics for PostgreSQL](#USER_PerfInsights.UsingDashboard.AnalyzeDBLoad.AdditionalMetrics.AnalyzingDigestLevel)

## Digest statistics for RDS PostgreSQL<a name="USER_PerfInsights.UsingDashboard.AnalyzeDBLoad.AdditionalMetrics.PostgreSQL.digest"></a>

To view SQL digest statistics, PostgreSQL must load the `pg_stat_statements` library\. For RDS PostgreSQL DB instances that are compatible with PostgreSQL 11 or later, the database loads this library by default\. For PostgreSQL DB instances that are compatible with PostgreSQL 10 or earlier, enable this library manually\. To enable it manually, add `pg_stat_statements` to `shared_preload_libraries` in the DB parameter group associated with the DB instance\. Then reboot your DB instance\. For more information, see [Working with DB parameter groups](USER_WorkingWithParamGroups.md)\.

**Note**  
Performance Insights can only collect statistics for queries in `pg_stat_activity` that aren't truncated\. By default, PostgreSQL databases truncate queries longer than 1,024 bytes\. To increase the query size, change the `track_activity_query_size` parameter in the DB parameter group associated with your DB instance\. When you change this parameter, a DB instance reboot is required\.

## Per\-second digest statistics for PostgreSQL<a name="USER_PerfInsights.UsingDashboard.AnalyzeDBLoad.AdditionalMetrics.PostgreSQL.per-second"></a>

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

## Per\-call digest statistics for PostgreSQL<a name="USER_PerfInsights.UsingDashboard.AnalyzeDBLoad.AdditionalMetrics.PostgreSQL.per-call"></a>

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

For more information about these metrics, see [pg\_stat\_statements](https://www.postgresql.org/docs/10/pgstatstatements.html) in the PostgreSQL documentation\.

## Viewing SQL statistics for PostgreSQL<a name="USER_PerfInsights.UsingDashboard.AnalyzeDBLoad.AdditionalMetrics.AnalyzingDigestLevel"></a>

The statistics are available in the **Top SQL** tab of the **Database load** chart\.

**To view the SQL statistics**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Go to the Performance Insights dashboard\.

1. Choose the **SQL** tab\.  
![\[Viewing metrics for running queries\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_insights_per_sql_digest.png)

1. Choose which statistics to display by choosing the gear icon in the upper\-right corner of the chart\.

   The following screenshot shows the preferences for PostgreSQL\.  
![\[Preferences for metrics for running queries for PostgreSQL DB instances\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_insights_per_sql_pref_apg.png)