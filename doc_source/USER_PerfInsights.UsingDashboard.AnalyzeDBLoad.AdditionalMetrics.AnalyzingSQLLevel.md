# Viewing SQL statistics in the Performance Insights dashboard<a name="USER_PerfInsights.UsingDashboard.AnalyzeDBLoad.AdditionalMetrics.AnalyzingSQLLevel"></a>

In the Performance Insights dashboard, SQL statistics are available in the **Top SQL** tab of the **Database load** chart\.

**To view SQL statistics**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the left navigation pane, choose **Performance Insights**\.

1. At the top of the page, choose the database whose SQL statistics you want to see\.

1. Scroll to the bottom of the page and choose the **Top SQL** tab\.

1. Choose an individual statement or digest query\.  
![\[Viewing metrics for running queries\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/./images/perf_insights_per_sql_sql.png)

1. Choose which statistics to display by choosing the gear icon in the upper\-right corner of the chart\. For descriptions of the SQL statistics for the Amazon RDS engines, see [SQL statistics for Performance Insights](sql-statistics.md)\.

   The following example shows the statistics preferences for Oracle DB instances\.  
![\[Preferences for metrics for running queries for Oracle DB instances\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/./images/perf_insights_per_sql_pref_oracle.png)

   The following example shows the preferences for MariaDB and MySQL DB instances\.  
![\[Preferences for metrics for running queries for MariaDB and MySQL DB instances.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/./images/perf_insights_per_sql_pref_ams.png)

1. Choose Save to save your preferences\.

   The **Top SQL** table refreshes\.

   The following example shows statistics for an Oracle SQL query\.  
![\[Statistics for a SQL query\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/./images/perf_insights_per_sql_stats_oracle.png)