# SQL statistics for MariaDB and MySQL<a name="USER_PerfInsights.UsingDashboard.AnalyzeDBLoad.AdditionalMetrics.MySQL"></a>

MariaDB and MySQL collect SQL statistics only at the digest level\. No statistics are shown at the statement level\.

**Topics**
+ [Digest statistics for MariaDB and MySQL](#USER_PerfInsights.UsingDashboard.AnalyzeDBLoad.AdditionalMetrics.MySQL.truncation)
+ [Per\-second statistics for MariaDB and MySQL](#USER_PerfInsights.UsingDashboard.AnalyzeDBLoad.AdditionalMetrics.MySQL.per-second)
+ [Per\-call statistics for MariaDB and MySQL](#USER_PerfInsights.UsingDashboard.AnalyzeDBLoad.AdditionalMetrics.MySQL.truncation.per-call)

## Digest statistics for MariaDB and MySQL<a name="USER_PerfInsights.UsingDashboard.AnalyzeDBLoad.AdditionalMetrics.MySQL.truncation"></a>

Performance Insights collects SQL digest statistics from the `events_statements_summary_by_digest` table\. The `events_statements_summary_by_digest` table is managed by your database\. 

The digest table doesn't have an eviction policy\. When the table is full, the AWS Management Console shows the following message:

```
Performance Insights is unable to collect SQL Digest statistics on new queries because the table events_statements_summary_by_digest is full. 
Please truncate events_statements_summary_by_digest table to clear the issue. Check the User Guide for more details.
```

In this situation, MariaDB and MySQL don't track SQL queries\. To address this issue, Performance Insights automatically truncates the digest table when both of the following conditions are met:
+ The table is full\.
+ Performance Insights manages the Performance Schema automatically\.

  For automatic management, the `performance_schema` parameter must be set to `0` and the **Source** must not be set to `user`\. If Performance Insights isn't managing the Performance Schema automatically, see [Turning on the Performance Schema for Performance Insights on Amazon RDS for MariaDB or MySQL](USER_PerfInsights.EnableMySQL.md)\.

In the AWS CLI, check the source of a parameter value by running the [describe\-db\-parameters](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-parameters.html) command\.

## Per\-second statistics for MariaDB and MySQL<a name="USER_PerfInsights.UsingDashboard.AnalyzeDBLoad.AdditionalMetrics.MySQL.per-second"></a>

The following SQL statistics are available for MariaDB and MySQL DB instances\.


| Metric | Unit | 
| --- | --- | 
| db\.sql\_tokenized\.stats\.count\_star\_per\_sec | Calls per second | 
| db\.sql\_tokenized\.stats\.sum\_timer\_wait\_per\_sec | Average active executions per second \(AAE\) | 
| db\.sql\_tokenized\.stats\.sum\_select\_full\_join\_per\_sec | Select full join per second | 
| db\.sql\_tokenized\.stats\.sum\_select\_range\_check\_per\_sec | Select range check per second | 
| db\.sql\_tokenized\.stats\.sum\_select\_scan\_per\_sec | Select scan per second | 
| db\.sql\_tokenized\.stats\.sum\_sort\_merge\_passes\_per\_sec | Sort merge passes per second | 
| db\.sql\_tokenized\.stats\.sum\_sort\_scan\_per\_sec | Sort scans per second | 
| db\.sql\_tokenized\.stats\.sum\_sort\_range\_per\_sec | Sort ranges per second | 
| db\.sql\_tokenized\.stats\.sum\_sort\_rows\_per\_sec | Sort rows per second | 
| db\.sql\_tokenized\.stats\.sum\_rows\_affected\_per\_sec | Rows affected per second | 
| db\.sql\_tokenized\.stats\.sum\_rows\_examined\_per\_sec | Rows examined per second | 
| db\.sql\_tokenized\.stats\.sum\_rows\_sent\_per\_sec | Rows sent per second | 
| db\.sql\_tokenized\.stats\.sum\_created\_tmp\_disk\_tables\_per\_sec | Created temporary disk tables per second | 
| db\.sql\_tokenized\.stats\.sum\_created\_tmp\_tables\_per\_sec | Created temporary tables per second | 
| db\.sql\_tokenized\.stats\.sum\_lock\_time\_per\_sec | Lock time per second \(in ms\) | 

## Per\-call statistics for MariaDB and MySQL<a name="USER_PerfInsights.UsingDashboard.AnalyzeDBLoad.AdditionalMetrics.MySQL.truncation.per-call"></a>

The following metrics provide per call statistics for a SQL statement\.


| Metric | Unit | 
| --- | --- | 
| db\.sql\_tokenized\.stats\.sum\_timer\_wait\_per\_call | Average latency per call \(in ms\)  | 
| db\.sql\_tokenized\.stats\.sum\_select\_full\_join\_per\_call | Select full joins per call | 
| db\.sql\_tokenized\.stats\.sum\_select\_range\_check\_per\_call | Select range check per call | 
| db\.sql\_tokenized\.stats\.sum\_select\_scan\_per\_call | Select scans per call | 
| db\.sql\_tokenized\.stats\.sum\_sort\_merge\_passes\_per\_call | Sort merge passes per call | 
| db\.sql\_tokenized\.stats\.sum\_sort\_scan\_per\_call | Sort scans per call | 
| db\.sql\_tokenized\.stats\.sum\_sort\_range\_per\_call | Sort ranges per call | 
| db\.sql\_tokenized\.stats\.sum\_sort\_rows\_per\_call | Sort rows per call | 
| db\.sql\_tokenized\.stats\.sum\_rows\_affected\_per\_call | Rows affected per call | 
| db\.sql\_tokenized\.stats\.sum\_rows\_examined\_per\_call | Rows examined per call | 
| db\.sql\_tokenized\.stats\.sum\_rows\_sent\_per\_call | Rows sent per call | 
| db\.sql\_tokenized\.stats\.sum\_created\_tmp\_disk\_tables\_per\_call | Created temporary disk tables per call | 
| db\.sql\_tokenized\.stats\.sum\_created\_tmp\_tables\_per\_call | Created temporary tables per call | 
| db\.sql\_tokenized\.stats\.sum\_lock\_time\_per\_call | Lock time per call \(in ms\) | 