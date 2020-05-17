# Using the Performance Insights Dashboard<a name="USER_PerfInsights.UsingDashboard"></a>

The Performance Insights dashboard contains database performance information to help you analyze and troubleshoot performance issues\. On the main dashboard page, you can view information about the database load\. You can also drill into details for a particular wait state, SQL query, host, or user\.

**Topics**
+ [Opening the Performance Insights Dashboard](#USER_PerfInsights.UsingDashboard.Opening)
+ [Performance Insights Dashboard Components](#USER_PerfInsights.UsingDashboard.Components)
+ [Analyzing Database Load Using the Performance Insights Dashboard](#USER_PerfInsights.UsingDashboard.AnalyzeDBLoad)
+ [Analyzing Statistics for Running Queries](#USER_PerfInsights.UsingDashboard.AnalyzeDBLoad.AdditionalMetrics)
+ [Viewing More SQL Text in the Performance Insights Dashboard](#USER_PerfInsights.UsingDashboard.SQLTextSize)

## Opening the Performance Insights Dashboard<a name="USER_PerfInsights.UsingDashboard.Opening"></a>

To see the Performance Insights dashboard, use the following procedure\.

**To view the Performance Insights dashboard in the AWS Management Console**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Performance Insights**\.

1. Choose a DB instance\. The Performance Insights dashboard is displayed for that DB instance\.

   For DB instances with Performance Insights enabled, you can also reach the dashboard by choosing the **Sessions** item in the list of DB instances\. Under **Current activity**, the **Sessions** item shows the database load in average active sessions over the last five minutes\. The bar graphically shows the load\. When the bar is empty, the DB instance is idle\. As the load increases, the bar fills with blue\. When the load passes the number of virtual CPUs \(vCPUs\) on the DB instance class, the bar turns red, indicating a potential bottleneck\.  
![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_insights_0a.png)![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/)![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/)

   The following screenshot shows the dashboard for a DB instance\.  
![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_insights_0b.png)![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/)![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/)

By default, the Performance Insights dashboard shows data for the last 60 minutes\. You can modify it to display data for the last 5 minutes, 60 minutes, 5 hours, 24 hours, or 1 week\. You can also show all of the data available\.

The Performance Insight dashboard automatically refreshes with new data\. The refresh rate depends on the amount of data displayed: 
+ 5 minutes refreshes every 5 seconds\.
+ 1 hour and 5 hours both refresh every minute\.
+ 24 hours refreshes every 5 minutes\.
+ 1 week refreshes every hour\.

![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_insights_1.png)![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/)![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/)

## Performance Insights Dashboard Components<a name="USER_PerfInsights.UsingDashboard.Components"></a>

The dashboard is divided into three parts:

1. **Counter Metrics chart** – Shows data for specific performance counter metrics\.

1. **Average Active Sessions chart** – Shows how the database load compares to DB instance capacity as represented by the **Max CPU** line\.

1.  **Top load items table** – Shows the top items contributing to database load\.

### Counter Metrics Chart<a name="USER_PerfInsights.UsingDashboard.Components.Countermetrics"></a>

 The **Counter Metrics** chart displays data for performance counters\. The default metrics shown are `blks_read.avg` and `xact_commit.avg`\. Choose which performance counters to display by selecting the gear icon in the upper\-right corner of the chart\. 

![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/aurora_perf_insights_counters.png)![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/)![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/)

For more information, see [Performance Insights Counters](USER_PerfInsights_Counters.md)\.

### Average Active Sessions Chart<a name="USER_PerfInsights.UsingDashboard.Components.AvgActiveSessions"></a>

The **Average Active Sessions** chart shows how the database load compares to DB instance capacity as represented by the **Max CPU** line\. By default, load is shown as active sessions grouped by wait states\. You can also choose instead to display load as active sessions grouped by SQL queries, hosts, or users\.

![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_insights_2.png)![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/)![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/)

To see details for any item for the selected time period in the legend, hover over that item on the **Average Active Sessions** chart\.

![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_insights_3.png)![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/)![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/)

### Top Load Items Table<a name="USER_PerfInsights.UsingDashboard.Components.AvgActiveSessions.TopLoadItemsTable"></a>

The **Top Load Items** table shows the top items contributing to database load\. By default, the top SQL queries that are contributing to the database load are shown\. Queries are displayed as digests of multiple actual queries that are structurally similar but that possibly have different parameters\. You can choose to display top wait states, hosts, or users instead\.

![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_insights_4.png)![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/)![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/)

The percentage of the database load associated with each top load item is illustrated in the **DB Load by Waits** column\. This column reflects the load for that item by whatever grouping is currently selected in the **Average Active Sessions** chart\. Take the case where the **Average Active Sessions** chart is grouping by hosts and you are looking at SQL queries in the top load items table\. In this case, the **DB Load by Waits** bar reflects the load that query represents on the related host\. Here it's colored\-coded to map to the representation of that host in the **Average Active Sessions** chart\.

For another example, suppose that the **Average Active Sessions** chart is grouping by wait states and you are looking at SQL queries in the top load items table\. In this case, the **DB Load by Waits** bar is sized, segmented, and color\-coded to show how much of a given wait state that query is contributing to\. It also shows what wait states are affecting that query\.

![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_insights_6.png)![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/)![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/)

In the **Top Load Items** table, you can view the following types of identifiers \(IDs\) that are associated with SQL statements:
+ **SQL ID** – An ID that the database uses to uniquely identify a SQL statement\.

  For Oracle and SQL Server DB instances, you can use a SQL ID to find a specific SQL statement\.
+ **Support SQL ID** – A hash value of the SQL ID\. This value is only for referencing a SQL ID when you are working with AWS Support\. AWS Support doesn't have access to your actual SQL IDs and SQL text\.
+ **Digest ID** – An ID that the database uses to uniquely identify a SQL digest\. A SQL digest can contain one or more SQL statements with literals removed and white space standardized\. The literals are replaced with question marks \(?\)\.

  For Amazon RDS for MariaDB, MySQL, and PostgreSQL DB instances, you can use a digest ID to find a specific SQL digest\.

  For Oracle and SQL Server DB instances, the digest ID is the same as the SQL ID\. The top row in the **Top Load Items** table is the actual SQL statement, including the literals\.
+ **Support Digest ID** – A hash value of the digest ID\. This value is only for referencing a digest ID when you are working with AWS Support\. AWS Support doesn't have access to your actual digest IDs and SQL text\.

In the **Top Load Items** table, you can open a top statement to view its IDs\. The following screenshot shows an open top statement\.

![\[SQL IDs\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf-insights-sql-ids-open.png)

You can control the IDs that the **Top Load Items** table shows by choosing the **Preferences** icon\.

![\[SQL IDs\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf-insights-sql-ids-preferences-icon.png)

When you choose the **Preferences** icon, the **Preferences** window opens\.

![\[SQL IDs\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf-insights-sql-ids-preferences.png)

Enable the IDs that you want to have visible in the **Top Load Items** table, and choose **Save**\.

## Analyzing Database Load Using the Performance Insights Dashboard<a name="USER_PerfInsights.UsingDashboard.AnalyzeDBLoad"></a>

If the **Average Active Sessions** chart shows a bottleneck, you can find out where the load is coming from\. To do so, look at the top load items table below the **Average Active Sessions** chart\. Choose a particular item, like a SQL query or a user, to drill down into that item and see details about it\.

DB load grouped by waits and top SQL queries is the default Performance Insights dashboard view\. This combination typically provides the most insight into performance issues\. DB load grouped by waits shows if there are any resource or concurrency bottlenecks in the database\. In this case, the **SQL** tab of the top load items table shows which queries are driving that load\.

Your typical workflow for diagnosing performance issues is as follows:

1. Review the **Average Active Sessions** chart and see if there are any incidents of database load exceeding the **Max CPU** line\.

1. If there is, look at the **Average Active Sessions** chart and identify which wait state or states are primarily responsible\.

1. Identify the digest queries causing the load by seeing which of the queries the **SQL** tab on the top load items table are contributing most to those wait states\. You can identify these by the **DB Load by Wait** column\.

1. Choose one of these digest queries in the **SQL** tab to expand it and see the child queries that it is composed of\.

For example, in the dashboard following, **IO:XactSync** waits are a frequent issue\. **CPU** wait is less, but it still contributes to load\. 

The first four roll\-up queries in the **SQL** tab of the top load items table correlate strongly to the first state\. Thus, those are the ones to drill into and examine the child queries of\. You do so to determine how they are contributing to the performance issue\. 

The last three roll\-up queries are the major contributors to CPU\. These are the queries to investigate if CPU load is an issue\.

![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_insights_7.png)![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/)![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/)

## Analyzing Statistics for Running Queries<a name="USER_PerfInsights.UsingDashboard.AnalyzeDBLoad.AdditionalMetrics"></a>

In Amazon RDS Performance Insights, you can find statistics on running queries in the **Top Load Items** section\. To view these statistics, view top SQL\. Performance Insights collects statistics only for the most common queries, and these usually match the top queries by load shown in the Performance Insights dashboard\.

**Topics**
+ [Statistics for MariaDB and MySQL](#USER_PerfInsights.UsingDashboard.AnalyzeDBLoad.AdditionalMetrics.MySQL)
+ [Statistics for Amazon RDS for Oracle](#USER_PerfInsights.UsingDashboard.AnalyzeDBLoad.AdditionalMetrics.Oracle)

### Statistics for MariaDB and MySQL<a name="USER_PerfInsights.UsingDashboard.AnalyzeDBLoad.AdditionalMetrics.MySQL"></a>

Performance Insights collects SQL digest statistics from the `events_statements_summary_by_digest` table\. This table is managed by the database and doesn't have an eviction policy\. If the table becomes full, new SQL queries aren't tracked\. To address this issue, Performance Insights automatically truncates the table when it's nearly full\.

Performance Insights automatically truncates the table only if your parameter group doesn't have an explicitly set value for the `performance_schema` parameter\. You can examine the `performance_schema` parameter, and if the value of source is `user`, then you set a value\. If you want Performance Insights to truncate the table automatically, then reset the value for the `performance_schema` parameter\. You can view the source of a parameter value by viewing the parameter in the AWS Management Console or by running the AWS CLI [describe\-db\-parameters](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-parameters.html) command\. The following message is shown in the AWS Management Console when the table is full:

```
Performance Insights is unable to collect SQL Digest statistics on new queries because the table events_statements_summary_by_digest is full. 
Please truncate events_statements_summary_by_digest table to clear the issue. Check the User Guide for more details.
```

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

#### Analyzing MariaDB and MySQL Metrics for SQL Statements That Are Running<a name="USER_PerfInsights.UsingDashboard.AnalyzeDBLoad.AdditionalMetrics.AnalyzingSQLLevelMariaDBMySQL"></a>

Using the AWS Management Console, you can view the metrics for a running SQL query by choosing the **SQL** tab and expanding the query\.

![\[Viewing metrics for running queries\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_insights_per_sql_digest.png)

Choose which statistics to display by choosing the gear icon in the upper\-right corner of the chart\.

The following screenshot shows the preferences for MariaDB and MySQL DB instances\.

![\[Preferences for metrics for running queries for MariaDB and MySQL DB instances.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_insights_per_sql_pref_ams.png)

### Statistics for Amazon RDS for Oracle<a name="USER_PerfInsights.UsingDashboard.AnalyzeDBLoad.AdditionalMetrics.Oracle"></a>

The following SQL statistics are available for Oracle DB instances\.


| Metric | Unit | 
| --- | --- | 
| db\.sql\.stats\.executions\_per\_sec | Number of executions per second | 
| db\.sql\.stats\.elapsed\_time\_per\_sec | Average active executions \(AAE\) | 
| db\.sql\.stats\.rows\_processed\_per\_sec | Rows processed per second | 
| db\.sql\.stats\.buffer\_gets\_per\_sec | Buffer gets per second | 
| db\.sql\.stats\.physical\_read\_requests\_per\_sec | Physical reads per second | 
| db\.sql\.stats\.physical\_write\_requests\_per\_sec | Physical writes per second | 
| db\.sql\.stats\.total\_sharable\_mem\_per\_sec | Total shareable memory per second \(in bytes\)  | 
| db\.sql\.stats\.cpu\_time\_per\_sec | CPU time per second \(in ms\) | 

The following metrics provide per call statistics for a SQL statement\.


| Metric | Unit | 
| --- | --- | 
| db\.sql\.stats\.elapsed\_time\_per\_exec | Elapsed time per executions \(in ms\)  | 
| db\.sql\.stats\.rows\_processed\_per\_exec | Rows processed per execution | 
| db\.sql\.stats\.buffer\_gets\_per\_exec | Buffer gets per execution | 
| db\.sql\.stats\.physical\_read\_requests\_per\_exec | Physical reads per execution | 
| db\.sql\.stats\.physical\_write\_requests\_per\_exec | Physical writes per execution | 
| db\.sql\.stats\.total\_sharable\_mem\_per\_exec | Total shareable memory per execution \(in bytes\) | 
| db\.sql\.stats\.cpu\_time\_per\_exec | CPU time per execution \(in ms\) | 

#### Analyzing Oracle Metrics for SQL Statements That Are Running<a name="USER_PerfInsights.UsingDashboard.AnalyzeDBLoad.AdditionalMetrics.AnalyzingSQLLevel"></a>

Using the AWS Management Console, you can view the metrics for a running SQL query by choosing the **SQL** tab and expanding the query\.

![\[Viewing metrics for running queries\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_insights_per_sql_sql.png)

Choose which statistics to display by choosing the gear icon in the upper\-right corner of the chart\.

The following screenshot shows the preferences for Oracle DB instances\.

![\[Preferences for metrics for running queries for Oracle DB instances\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_insights_per_sql_pref_oracle.png)

## Viewing More SQL Text in the Performance Insights Dashboard<a name="USER_PerfInsights.UsingDashboard.SQLTextSize"></a>

By default, each row in the **Top Load Items** table shows 500 bytes of SQL text for each SQL statement\. When a SQL statement is larger than 500 bytes, you can view more of the SQL statement by opening the statement in the Performance Insights dashboard\. The Performance Insights dashboard can display up to 4,096 bytes for a SQL statement\. You can copy the displayed SQL statement\. To view more than 4,096 bytes, choose **Download full SQL** to view the SQL text up to the DB engine limit\.

The limit for SQL text depends on the DB engine\. The following limits apply:
+ Amazon RDS for Microsoft SQL Server – 4,096 characters
+ Amazon RDS for MySQL and MariaDB – 1,024 bytes
+ Amazon RDS for Oracle – 1,000 bytes
+ Amazon RDS for PostgreSQL – Set by the `track_activity_query_size` DB instance parameter

For Amazon RDS for PostgreSQL DB instances, you can control the limit of the SQL text size by setting the `track_activity_query_size` DB instance parameter, up to 102,400 bytes\. You can use the AWS Management Console to download SQL text up to the limit you set with this parameter\. For more information, see [Setting the SQL Text Limit for Amazon RDS for PostgreSQL DB Instances](#USER_PerfInsights.UsingDashboard.SQLTextLimit)\.

**Important**  
Currently, you can only view and download more SQL text with the AWS Management Console\. The AWS Performance Insights CLI and API can return a maximum of 500 bytes of text\.

**To view more SQL text in the Performance Insights dashboard**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Performance Insights**\.

1. Choose a DB instance\. The Performance Insights dashboard is displayed for that DB instance\.

   SQL statements with text larger than 500 bytes look similar to the following image\.  
![\[SQL statements with large text\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf-insights-large-text-1.png)

1. Open a SQL statement to view more of the SQL text\.  
![\[Viewing more SQL text\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf-insights-large-text-2.png)

   The Performance Insights dashboard can display up to 4,096 bytes for each SQL statement\.

1. \(Optional\) Choose **Copy snippet** to copy the displayed SQL statement, or choose **Download full SQL** to download the SQL statement to view the SQL text up to the DB engine limit\.
**Note**  
To copy or download the SQL statement, disable pop\-up blockers\. 

### Setting the SQL Text Limit for Amazon RDS for PostgreSQL DB Instances<a name="USER_PerfInsights.UsingDashboard.SQLTextLimit"></a>

For Amazon RDS for PostgreSQL DB instances, you can control the limit for the SQL text that can be shown on the Performance Insights dashboard\. 

To do so, modify the `track_activity_query_size` DB instance parameter\. The default setting for the `track_activity_query_size` parameter is 1,024 bytes\. 

You can increase the number of bytes to increase the SQL text size visible in the Performance Insights dashboard\. The limit for the parameter is 102,400 bytes\. For more information about the `track_activity_query_size` DB instance parameter, see [Run\-time Statistics](https://www.postgresql.org/docs/current/runtime-config-statistics.html) in the PostgreSQL documentation\.

To modify the parameter, change the parameter setting in the parameter group that is associated with the Amazon RDS for PostgreSQL DB instance\.

If the Amazon RDS for PostgreSQL DB instance is using the default parameter group, complete the following steps:

1. Create a new DB instance parameter group for the appropriate DB engine and DB engine version\.

1. Set the parameter in the new parameter group\.

1. Associate the new parameter group with the DB instance\.

For information about setting a DB instance parameter, see [Modifying Parameters in a DB Parameter Group](USER_WorkingWithParamGroups.md#USER_WorkingWithParamGroups.Modifying)\.