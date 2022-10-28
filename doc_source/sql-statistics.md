# SQL statistics for Performance Insights<a name="sql-statistics"></a>

*SQL statistics* are performance\-related metrics about SQL queries that are collected by Performance Insights\. Performance Insights gathers statistics for each second that a query is running and for each SQL call\.

A SQL digest is a composite of all queries having a given pattern but not necessarily having the same literal values\. The digest replaces literal values with a question mark\. For example, `SELECT * FROM emp WHERE lname= ?`\. This digest might consist of the following child queries:

```
           SELECT * FROM emp WHERE lname = 'Sanchez'
           SELECT * FROM emp WHERE lname = 'Olagappan'
           SELECT * FROM emp WHERE lname = 'Wu'
```

All engines support SQL statistics for digest queries\.

**Topics**
+ [SQL statistics for MariaDB and MySQL](USER_PerfInsights.UsingDashboard.AnalyzeDBLoad.AdditionalMetrics.MySQL.md)
+ [SQL statistics for Oracle](USER_PerfInsights.UsingDashboard.AnalyzeDBLoad.AdditionalMetrics.Oracle.md)
+ [SQL statistics for RDS PostgreSQL](USER_PerfInsights.UsingDashboard.AnalyzeDBLoad.AdditionalMetrics.PostgreSQL.md)