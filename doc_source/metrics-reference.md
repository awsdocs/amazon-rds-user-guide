# Metrics reference for Amazon RDS<a name="metrics-reference"></a>

In this reference, you can find descriptions of Amazon RDS metrics for Amazon CloudWatch, Performance Insights, and Enhanced Monitoring\.

**Topics**
+ [Amazon CloudWatch metrics for Amazon RDS](rds-metrics.md)
+ [Amazon CloudWatch dimensions for Amazon RDS](dimensions.md)
+ [Amazon CloudWatch metrics for Performance Insights](USER_PerfInsights.Cloudwatch.md)
+ [Performance Insights counter metrics](USER_PerfInsights_Counters.md)
+ [SQL statistics for Performance Insights](#sql-statistics)
+ [OS metrics in Enhanced Monitoring](USER_Monitoring-Available-OS-Metrics.md)

## SQL statistics for Performance Insights<a name="sql-statistics"></a>

*SQL statistics* are performance\-related metrics about SQL queries that are collected by Performance Insights\. Performance Insights gathers statistics for each second that a query is running and for each SQL call\. All engines support statistics for digest queries\. All engines support statement\-level statistics except for RDS PostgreSQL\.

**Topics**
+ [SQL statistics for MariaDB and MySQL](USER_PerfInsights.UsingDashboard.AnalyzeDBLoad.AdditionalMetrics.MySQL.md)
+ [SQL statistics for Oracle](USER_PerfInsights.UsingDashboard.AnalyzeDBLoad.AdditionalMetrics.Oracle.md)
+ [SQL statistics for RDS PostgreSQL](USER_PerfInsights.UsingDashboard.AnalyzeDBLoad.AdditionalMetrics.PostgreSQL.md)