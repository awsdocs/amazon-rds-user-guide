# Preview: Using Amazon Performance Insights<a name="USER_PerfInsights"></a>

Amazon RDS Performance Insights monitors your Amazon RDS DB instance load so that you can analyze and troubleshoot your database performance\. Amazon RDS Performance Insights is currently available only for use with Amazon Aurora \(PostgreSQL\)\.

Performance Insights expands on existing Amazon RDS monitoring features to illustrate your database's performance and help you analyze any issues that affect it\. With the Performance Insights dashboard, you can visualize the database load and filter the load by waits, SQL statements, hosts, or users\. Performance Insights is on by default for the Aurora PostgreSQL database engine\. If you have more than one database on the DB instance, performance data for all of the databases is aggregated for the DB instance\. Database performance data is kept for 24 hours\.

The central metric for Performance Insights is **DB Load**, which represents the average number of active sessions for the database engine\. An *active session* is a connection that has submitted work to the database engine and is waiting for a response from it\. For example, if you submit a SQL query to the database engine, the database session is active while the database engine is processing that query\. 

By combining **DB Load** with *wait event* data you can get a complete picture of the state for an active session\. Wait events vary by database engine\. You can see a list of the most commonly used wait events for Aurora PostgreSQL at [Amazon RDS PostgreSQL Events ](AuroraPostgreSQL.Reference.md#AuroraPostgreSQL.Reference.Waitevents)\. For a complete list of all PostgreSQL wait events see, [ PostgreSQL Wait Events](https://www.postgresql.org/docs/10/static/monitoring-stats.html#WAIT-EVENT-TABLE)\. 

Session information is collected, aggregated, and displayed in the dashboard as the **Average Active Sessions** chart\. The **Average Active Sessions** chart displays the **Max CPU** value as a line, so you can see if active sessions are exceeding it or not\. The **Max CPU** value is determined by the number of **vCPU** \(virtual CPU\) cores for your DB instance\. 

If you find that the load in the **Average Active Sessions** chart is often above the **Max CPU** line and the primary wait state is CPU, the system CPU is overloaded\. In these cases, you might want to throttle connections to the instance, tune any SQL queries with a high CPU load, or consider a larger instance class\. High and consistent instances of any wait state indicate that there might be bottlenecks or resource contention issues that you should resolve, even if the load does not cross the **Max CPU** line\.

The following video provides an overview of Performance Insights\.

[![AWS Videos](http://img.youtube.com/vi/4462hcfkApM/0.jpg)](http://www.youtube.com/watch?v=4462hcfkApM)

**Topics**
+ [Using the Performance Insights Dashboard](#USER_PerfInsights.UsingDashboard)
+ [Additional User Interface Features](#USER_PerfInsights.UIcontrols)
+ [Access Control for Performance Insights](USER_PerfInsights.access-control.md)
+ [Frequently Asked Questions](USER_PerfInsights.FAQ.md)
+ [Performance Insights Reference](USER_PerfInsights.Reference.md)

## Using the Performance Insights Dashboard<a name="USER_PerfInsights.UsingDashboard"></a>

The Performance Insights dashboard contains database performance information to help you analyze and troubleshoot performance issues\. On the main dashboard page, you can view information about the database load, and drill into details for a particular wait state, SQL query, host, or user\.

**Topics**
+ [Opening the Performance Insights Dashboard](#USER_PerfInsights.UsingDashboard.Opening)
+ [Performance Insights Dashboard Components](#USER_PerfInsights.UsingDashboard.Components)
+ [Analyzing Database Load Using the Performance Insights Dashboard](#USER_PerfInsights.UsingDashboard.AnalyzeDBLoad)

### Opening the Performance Insights Dashboard<a name="USER_PerfInsights.UsingDashboard.Opening"></a>

To see the Performance Insights dashboard, use the following procedure\.

**To view the Performance Insights dashboard in the AWS Management Console**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Performance Insights**\.

1. Choose a DB instance\. The Performance Insights dashboard is displayed for that instance\.

   You can also reach the dashboard by choosing the **Current Activity** widget in the instance listing\.  
![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_insights_0a.png)![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/)![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/)  
![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_insights_0b.png)![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/)![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/)

By default, the Performance Insights dashboard shows data for the last 60 minutes\. You can modify it to display data for the last 5 minutes, 60 minutes, 6 hours, or 24 hours\.

![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_insights_1.png)![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/)![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/)

### Performance Insights Dashboard Components<a name="USER_PerfInsights.UsingDashboard.Components"></a>

The dashboard is divided into two parts:

1. **Average Active Sessions chart** – Shows how the database load compares to DB instance capacity as represented by the **Max CPU** line\.

1.  **Top load items table** – Shows the top items contributing to database load\.

#### Average Active Sessions Chart<a name="USER_PerfInsights.UsingDashboard.Components.AvgActiveSessions"></a>

The **Average Active Sessions** chart shows how the database load compares to DB instance capacity as represented by the **Max CPU** line\. By default, load is shown as active sessions grouped by wait states\. You can also choose to display load as active sessions grouped by SQL queries, hosts, or users instead\.

![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_insights_2.png)![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/)![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/)

To see details for any item for the selected time period in the legend, hover over that item on the **Average Active Sessions** chart\.

![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_insights_3.png)![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/)![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/)

#### Top Load Items Table<a name="USER_PerfInsights.UsingDashboard.Components.AvgActiveSessions.TopLoadItemsTable"></a>

The **Top Load Items** table shows the top items contributing to database load\. By default, the top SQL queries that are contributing to the database load are shown\. Queries are displayed as digests of multiple actual queries that are structurally similar but that possibly have different parameters\. You can choose to display top wait states, hosts, or users instead\.

![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_insights_4.png)![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/)![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/)

The percentage of the database load associated with each top load item is illustrated in the **DB Load by Waits** column\. This column reflects the load for that item by whatever grouping is currently selected in the **Average Active Sessions** chart\. Take the case where the **Average Active Sessions** chart is grouping by hosts and you are looking at SQL queries in the top load items table\. In this case, the **DB Load by Waits** bar reflects the load that query represents on the related host\. Here it's colored\-coded to map to the representation of that host in the **Average Active Sessions** chart\.

For another example, suppose that the **Average Active Sessions** chart is grouping by wait states and you are looking at SQL queries in the top load items table\. In this case, the **DB Load by Waits** bar is sized, segmented, and color\-coded to show how much of a given wait state that query is contributing to\. It also shows what wait states are affecting that query\.

![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_insights_6.png)![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/)![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/)

### Analyzing Database Load Using the Performance Insights Dashboard<a name="USER_PerfInsights.UsingDashboard.AnalyzeDBLoad"></a>

If the **Average Active Sessions** chart shows a bottleneck, you can find out where the load is coming from\. To do so, look at the top load items table below the **Average Active Sessions** chart\. Choose a particular item, like a SQL query or a user, to drill down into that item and see details about it\.

DB load grouped by waits and top SQL queries is the default Performance Insights dashboard view, because this is the combination that typically provides the most insight into performance issues\. DB load grouped by waits shows if there are any resource or concurrency bottlenecks in the database\. In this case, the **SQL** tab of the top load items table shows which queries are driving that load\.

Your typical workflow for diagnosing performance issues is as follows:

1. Review the **Average Active Sessions** chart and see if there are any incidents of database load exceeding the **Max CPU** line\.

1. If there is, look at the **Average Active Sessions** chart and identify which wait state or states are primarily responsible\.

1. Identify the digest queries causing the load by seeing which of the queries the **SQL** tab on the top load items table are contributing most to those wait states\. You can identify these by the **DB Load by Wait** column\.

1. Choose one of these digest queries in the **SQL** tab to expand it and see the child queries that it is composed of\.

For example, in the dashboard following, **IO:XactSync** waits are a frequent issue\. **CPU** wait is less, but it still contributes to load\. 

The first four roll\-up queries in the **SQL** tab of the top load items table correlate strongly to the first state\. Thus, those are the ones to drill into and examine the child queries of\. You do so to determine how they are contributing to the performance issue\. 

The last three roll\-up queries are the major contributors to CPU\. These are the queries to investigate if CPU load is an issue\.

![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_insights_7.png)![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/)![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/)

## Additional User Interface Features<a name="USER_PerfInsights.UIcontrols"></a>

You can use other features of the Performance Insights user interface to help analyze performance data\.

**Click\-and\-Drag Zoom In**  
In the Performance Insights interface, you can select a small portion of the load chart and zoom in on the detail\.

![\[Zoom in\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_insights_zoom_in.png)![\[Zoom in\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/)![\[Zoom in\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/)

To zoom in on a portion of the load chart, choose the start time and drag to the end of the time period you want\. When you do this, the selected area is highlighted\. When you release the mouse, the load chart zooms in on the selected region, and the **Top N** table is recalculated\. 

**Pause and Zoom Out**  
In the upper\-right corner of the load chart, you can find the **Pause** and **Zoom out** tools\.

![\[Pause and zoom out\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_insights_zoom_out.png)![\[Pause and zoom out\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/)![\[Pause and zoom out\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/)

When you choose **Pause**, the load chart stops autorefreshing\. When you choose **Pause** again, the chart resumes autorefreshing\.

When you choose **Zoom out**, the load chart zooms out to the next largest time interval\.

### Related Topics<a name="USER_PerfInsights.related"></a>
+ [Using Amazon RDS Event Notification](USER_Events.md)
+ [Amazon RDS Database Log Files](USER_LogAccess.md)