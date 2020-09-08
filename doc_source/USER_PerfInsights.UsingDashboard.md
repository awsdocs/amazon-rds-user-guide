# Monitoring with the Performance Insights Dashboard<a name="USER_PerfInsights.UsingDashboard"></a>

The Performance Insights dashboard contains database performance information to help you analyze and troubleshoot performance issues\. On the main dashboard page, you can view information about the database load\. You can also drill into details for a particular wait state, SQL query, host, or user\.

## Opening the Performance Insights Dashboard<a name="USER_PerfInsights.UsingDashboard.Opening"></a>

To see the Performance Insights dashboard, use the following procedure\.

**To view the Performance Insights dashboard in the AWS Management Console**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Performance Insights**\.

1. Choose a DB instance\. The Performance Insights dashboard is displayed for that DB instance\.

   For DB instances with Performance Insights enabled, you can also reach the dashboard by choosing the **Sessions** item in the list of DB instances\. Under **Current activity**, the **Sessions** item shows the database load in average active sessions over the last five minutes\. The bar graphically shows the load\. When the bar is empty, the DB instance is idle\. As the load increases, the bar fills with blue\. When the load passes the number of virtual CPUs \(vCPUs\) on the DB instance class, the bar turns red, indicating a potential bottleneck\.  
![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_insights_0a.png)

   The following screenshot shows the dashboard for a DB instance\. By default, the Performance Insights dashboard shows data for the last hour\.  
![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_insights_0b.png)

1. \(Optional\) Choose a different time interval by selecting a button in the upper right\. For example, to change the interval to 5 hours, select **5h**\.  
![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_insights_0c.png)

   In the following screenshot, the DB load interval is 5 hours\.  
![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_insights_1.png)

1. \(Optional\) To refresh your data automatically, enable **Auto refresh**\.  
![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_insights_1b.png)

   The Performance Insight dashboard automatically refreshes with new data\. The refresh rate depends on the amount of data displayed: 
   + 5 minutes refreshes every 5 seconds\.
   + 1 hour refreshes every minute\.
   + 5 hours refreshes every minute\.
   + 24 hours refreshes every 5 minutes\.
   + 1 week refreshes every hour\.

## Performance Insights Dashboard Components<a name="USER_PerfInsights.UsingDashboard.Components"></a>

The dashboard is divided into three parts:

1. **Counter Metrics** – Shows data for specific performance counter metrics\.

1. **DB Load Chart** – Shows how the DB load compares to DB instance capacity as represented by the **Max vCPU** line\.

1.  **Top *items*** – Shows the top waits, SQL, hosts, and users contributing to DB load\.

### Counter Metrics Chart<a name="USER_PerfInsights.UsingDashboard.Components.Countermetrics"></a>

 The **Counter Metrics** chart displays data for performance counters\. The default metrics depend on the DB engine\.
+ MySQL and MariaDB – `db.SQL.Innodb_rows_read.avg`
+ Oracle – `db.User.user calls.avg`
+ Microsoft SQL Server – `db.Databases.Active Transactions(_Total).avg`
+ PostgreSQL – `db.Transactions.xact_commit.avg`

![\[Counter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/oracle_perf_insights_counters.png)

Change the performance counters by choosing **Manage Metrics**\. You can select multiple **OS metrics** or **Database metrics**, as shown in the following screenshot\. To see details for any metric, hover over the metric name\.

![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_insights_select_metrics.png)

For more information, see [Customizing the Performance Insights Dashboard](USER_PerfInsights_Counters.md)\.

### Average Active Sessions Chart<a name="USER_PerfInsights.UsingDashboard.Components.AvgActiveSessions"></a>

The **DB Load Chart** shows how the database load compares to DB instance capacity as represented by the **Max vCPU** line\. By default, load is shown as active sessions grouped by wait states in a bar graph\. 

![\[Database load\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_insights_2.png)

You can choose to display load as active sessions grouped by waits, SQL, users, or hosts\. You can also choose a line graph\.

![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_insights_2b.png)

To see details about a DB load item such as a SQL statement, hover over the item name\.

![\[Database load item details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_insights_2c.png)

To see details for any item for the selected time period in the legend, hover over that item\.

![\[Time period details for DB load\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_insights_3.png)

### Top Load Items Table<a name="USER_PerfInsights.UsingDashboard.Components.AvgActiveSessions.TopLoadItemsTable"></a>

The **Top Load Items** table shows the top items contributing to database load\. By default, the console displays top SQL queries that are contributing to the database load, along with relevant statistics for each statement\. You can choose to display top waits, hosts, or users instead\.

![\[Top SQL\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_insights_4.png)

To see the components of a query, select the query, and then choose the \+\. A *SQL digest* is a composite of multiple actual queries that are structurally similar but that possibly have different literal values\. In the following screenshot, the selected query is a digest\.

![\[Selected SQL digest\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_insights_4b.png)

In **Top sql**, the **Load by waits \(AAS\)** column illustrates the percentage of the database load associated with each top load item\. This column reflects the load for that item by whatever grouping is currently selected in the **DB Load Chart**\. For example, you might group the **DB Load Chart** chart by wait states\. You examine SQL queries in the top load items table\. In this case, the **DB Load by Waits** bar is sized, segmented, and color\-coded to show how much of a given wait state that query is contributing to\. It also shows which wait states are affecting the selected query\.

![\[DB load by waits\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_insights_6.png)

In the **Top sql** table, you can open a statement to view its information\. The information appears in the bottom pane\.

![\[Top SQL table with literal query selected\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf-insights-sql-ids-open.png)

In the **Top sql** tab, you can view the following types of identifiers \(IDs\) that are associated with SQL statements:
+ **Support SQL ID** – A hash value of the SQL ID\. This value is only for referencing a SQL ID when you are working with AWS Support\. AWS Support doesn't have access to your actual SQL IDs and SQL text\.
+ **Support Digest ID** – A hash value of the digest ID\. This value is only for referencing a digest ID when you are working with AWS Support\. AWS Support doesn't have access to your actual digest IDs and SQL text\.

You can control the statistics displayed in the **Top sql** tab by choosing the **Preferences** icon\.

![\[Statistics preferences\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf-insights-sql-ids-preferences-icon.png)

When you choose the **Preferences** icon, the **Preferences** window opens\.

![\[Preferences window\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf-insights-sql-ids-preferences.png)

Enable the statistics that you want to have visible in the **Top sql** tab, use your mouse to scroll to the bottom of the window, and then choose **Continue**\.

## Analyzing Database Load Using the Performance Insights Dashboard<a name="USER_PerfInsights.UsingDashboard.AnalyzeDBLoad"></a>

If the **Average Active Sessions** chart shows a bottleneck, you can find out where the load is coming from\. To do so, look at the top load items table below the **Average Active Sessions** chart\. Choose a particular item, like a SQL query or a user, to drill down into that item and see details about it\.

DB load grouped by waits and top SQL queries is the default Performance Insights dashboard view\. This combination typically provides the most insight into performance issues\. DB load grouped by waits shows if there are any resource or concurrency bottlenecks in the database\. In this case, the **SQL** tab of the top load items table shows which queries are driving that load\.

Your typical workflow for diagnosing performance issues is as follows:

1. Review the **Average Active Sessions** chart and see if there are any incidents of database load exceeding the **Max CPU** line\.

1. If there is, look at the **Average Active Sessions** chart and identify which wait state or states are primarily responsible\.

1. Identify the digest queries causing the load by seeing which of the queries the **SQL** tab on the top load items table are contributing most to those wait states\. You can identify these by the **DB Load by Wait** column\.

1. Choose one of these digest queries in the **SQL** tab to expand it and see the child queries that it is composed of\.

For example, in the dashboard following, **log file sync** waits account for most of the DB load\. The **LGWR all worker groups** wait is also high\. The **Top sql** chart shows what is causing the **log file sync** waits: frequent `COMMIT` statements\. In this case, committing less frequently will reduce DB load\.

![\[log file sync errors\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_insights_7.png)

## Analyzing Statistics for Running Queries<a name="USER_PerfInsights.UsingDashboard.AnalyzeDBLoad.AdditionalMetrics"></a>

In Amazon RDS Performance Insights, you can find statistics on running queries in the **Top *load\_items*** section\. To view these statistics, view top SQL\. Performance Insights collects statistics only for the most common queries\. Typically, these match the top queries by load shown in the Performance Insights dashboard\.

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

The following screenshot shows the statistics for a SQL statement\.

![\[Statistics for a SQL statement\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_insights_per_sql_stats_oracle.png)

## Viewing More SQL Text in the Performance Insights Dashboard<a name="USER_PerfInsights.UsingDashboard.SQLTextSize"></a>

By default, each row in the **Top sql** table shows 500 bytes of SQL text for each SQL statement\. When a SQL statement is larger than 500 bytes, you can view more of the SQL statement by opening the statement in the Performance Insights dashboard\. The dashboard displays text up to the following per\-engine limits:
+ Amazon RDS for Microsoft SQL Server – 4,096 characters
+ Amazon RDS for MySQL and MariaDB – 1,024 bytes
+ Amazon RDS for Oracle – 1,000 bytes

You can copy the text that is displayed on the dashboard, or choose **Download**\.

Amazon RDS for PostgreSQL handles text differently\. Using the Performance Insights dashboard, you can view and download up to 500 bytes\. To access more than 500 bytes, set the size limit with the DB instance parameter `track_activity_query_size`\. The maximum value is 102,400 bytes\. To view or download text over 500 bytes, use the AWS Management Console, not the Performance Insights CLI or API\. For more information, see [Setting the SQL Text Limit for Amazon RDS for PostgreSQL DB Instances](#USER_PerfInsights.UsingDashboard.SQLTextLimit)\.

**To view more SQL text in the Performance Insights dashboard**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Performance Insights**\.

1. Choose a DB instance\. The Performance Insights dashboard is displayed for that DB instance\.

   SQL statements with text larger than 500 bytes look similar to the following image\.  
![\[SQL statements with large text\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf-insights-large-text-1.png)

1. Examine the SQL information section to view more of the SQL text\.  
![\[SQL information section shows more of the SQL text\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf-insights-large-text-2.png)

   The Performance Insights dashboard can display up to 4,096 bytes for each SQL statement\.

1. \(Optional\) Choose **Copy** to copy the displayed SQL statement, or choose **Download** to download the SQL statement to view the SQL text up to the DB engine limit\.
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

## Zooming In on the DB Load Chart<a name="USER_PerfInsights.UIcontrols"></a>

You can use other features of the Performance Insights user interface to help analyze performance data\.

**Click\-and\-Drag Zoom In**  
In the Performance Insights interface, you can choose a small portion of the load chart and zoom in on the detail\.

![\[Zoom in\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_insights_zoom_in.png)

To zoom in on a portion of the load chart, choose the start time and drag to the end of the time period you want\. When you do this, the selected area is highlighted\. When you release the mouse, the load chart zooms in on the selected AWS Region, and the **Top *items*** table is recalculated\.

![\[Zoom in\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_insights_zoom_in_b.png)