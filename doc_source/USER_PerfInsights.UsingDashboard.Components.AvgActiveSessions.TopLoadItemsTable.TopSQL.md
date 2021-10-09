# Overview of the Top SQL tab<a name="USER_PerfInsights.UsingDashboard.Components.AvgActiveSessions.TopLoadItemsTable.TopSQL"></a>

By default, the **Top SQL** tab shows the SQL queries that are contributing the most to DB load\. To help tune your queries, you can analyze information such as the query text, statistics, and Support SQL ID\. You can also choose the statistics that you want to appear in the **Top SQL** tab\.

**Topics**
+ [SQL statistics](#USER_PerfInsights.UsingDashboard.Components.AvgActiveSessions.TopLoadItemsTable.TopSQL.statistics)
+ [Load by waits \(AAS\)](#USER_PerfInsights.UsingDashboard.Components.AvgActiveSessions.TopLoadItemsTable.TopSQL.Load-by-waits)
+ [SQL information](#USER_PerfInsights.UsingDashboard.Components.AvgActiveSessions.TopLoadItemsTable.TopSQL.SQL-information)
+ [Preferences](#USER_PerfInsights.UsingDashboard.Components.AvgActiveSessions.TopLoadItemsTable.TopSQL.Preferences)

## SQL statistics<a name="USER_PerfInsights.UsingDashboard.Components.AvgActiveSessions.TopLoadItemsTable.TopSQL.statistics"></a>

*SQL statistics* are performance\-related metrics about SQL queries\. For example, Performance Insights might show executions per second or rows processed per second\. Performance Insights collects statistics for only the most common queries\. Typically, these match the top queries by load shown in the Performance Insights dashboard\. 

A *SQL digest* is a composite of multiple actual queries that are structurally similar but might have different literal values\. The digest replaces hardcoded values with a question mark\. For example, a digest might be `SELECT * FROM emp WHERE lname= ?`\. This digest might include the following child queries:

```
SELECT * FROM emp WHERE lname = 'Miller'
SELECT * FROM emp WHERE lname = 'Olagappan'
SELECT * FROM emp WHERE lname = 'Wu'
```

Every line in the **Top SQL** table shows relevant statistics for the SQL statement or digest, as shown in the following example\.

![\[Top SQL\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_insights_4.png)

To see the literal SQL statements in a digest, select the query, and then choose the plus symbol \(\+\)\. In the following screenshot, the selected query is a digest\. 

![\[Selected SQL digest\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_insights_4b.png)

**Note**  
A SQL digest groups similar SQL statements, but does not redact sensitive information\.

## Load by waits \(AAS\)<a name="USER_PerfInsights.UsingDashboard.Components.AvgActiveSessions.TopLoadItemsTable.TopSQL.Load-by-waits"></a>

In **Top SQL**, the **Load by waits \(AAS\)** column illustrates the percentage of the database load associated with each top load item\. This column reflects the load for that item by whatever grouping is currently selected in the **DB Load Chart**\. 

For example, you might group the **DB load** chart by wait states\. You examine SQL queries in the top load items table\. In this case, the **DB Load by Waits** bar is sized, segmented, and color\-coded to show how much of a given wait state that query is contributing to\. It also shows which wait states are affecting the selected query\.

![\[DB load by waits\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_insights_6.png)

## SQL information<a name="USER_PerfInsights.UsingDashboard.Components.AvgActiveSessions.TopLoadItemsTable.TopSQL.SQL-information"></a>

In the **Top SQL** table, you can open a statement to view its information\. The information appears in the bottom pane\.

![\[Top SQL table with literal query selected\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf-insights-sql-ids-open.png)

The following types of identifiers \(IDs\) that are associated with SQL statements:
+ **Support SQL ID** – A hash value of the SQL ID\. This value is only for referencing a SQL ID when you are working with AWS Support\. AWS Support doesn't have access to your actual SQL IDs and SQL text\.
+ **Support Digest ID** – A hash value of the digest ID\. This value is only for referencing a digest ID when you are working with AWS Support\. AWS Support doesn't have access to your actual digest IDs and SQL text\.

## Preferences<a name="USER_PerfInsights.UsingDashboard.Components.AvgActiveSessions.TopLoadItemsTable.TopSQL.Preferences"></a>

You can control the statistics displayed in the **Top SQL** tab by choosing the **Preferences** icon\.

![\[Statistics preferences\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf-insights-sql-ids-preferences-icon.png)

When you choose the **Preferences** icon, the **Preferences** window opens\.

![\[Preferences window\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf-insights-sql-ids-preferences.png)

To enable the statistics that you want to appear in the **Top SQL** tab, use your mouse to scroll to the bottom of the window, and then choose **Continue**\.