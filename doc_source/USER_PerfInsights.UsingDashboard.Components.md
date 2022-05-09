# Overview of the Performance Insights dashboard<a name="USER_PerfInsights.UsingDashboard.Components"></a>

The dashboard is the easiest way to interact with Performance Insights\. The following example shows the dashboard for a MySQL DB instance\.

![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/./images/perf_insights_0b.png)

**Topics**
+ [Time range filter](#USER_PerfInsights.UsingDashboard.Components.time-range)
+ [Counter metrics chart](#USER_PerfInsights.UsingDashboard.Components.Countermetrics)
+ [Database load chart](#USER_PerfInsights.UsingDashboard.Components.AvgActiveSessions)
+ [Top dimensions table](#USER_PerfInsights.UsingDashboard.Components.AvgActiveSessions.TopLoadItemsTable)

## Time range filter<a name="USER_PerfInsights.UsingDashboard.Components.time-range"></a>

By default, the Performance Insights dashboard shows DB load for the last hour\. You can adjust this range to be as short as 5 minutes or as long as 2 years\. You can also select a custom relative range\.

![\[Performance Insights relative time\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/./images/perf-insights-relative-time.png)

You can select an absolute range with a beginning and ending date and time\. The following example shows the time range beginning at midnight on 4/11/22 and ending at 11:59 PM on 4/14/22\.

![\[Performance Insights absolute time\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/./images/perf-insights-absolute-time.png)

## Counter metrics chart<a name="USER_PerfInsights.UsingDashboard.Components.Countermetrics"></a>

With counter metrics, you can customize the Performance Insights dashboard to include up to 10 additional graphs\. These graphs show a selection of dozens of operating system and database performance metrics\. You can correlate this information with DB load to help identify and analyze performance problems\.

 The **Counter metrics** chart displays data for performance counters\. The default metrics depend on the DB engine:
+ MySQL and MariaDB – `db.SQL.Innodb_rows_read.avg`
+ Oracle – `db.User.user calls.avg`
+ Microsoft SQL Server – `db.Databases.Active Transactions(_Total).avg`
+ PostgreSQL – `db.Transactions.xact_commit.avg`

![\[Counter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/./images/oracle_perf_insights_counters.png)

To change the performance counters, choose **Manage Metrics**\. You can select multiple **OS metrics** or **Database metrics**, as shown in the following screenshot\. To see details for any metric, hover over the metric name\.

![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/./images/perf_insights_select_metrics.png)

For descriptions of the counter metrics that you can add for each DB engine, see [Performance Insights counter metrics](USER_PerfInsights_Counters.md)\.

## Database load chart<a name="USER_PerfInsights.UsingDashboard.Components.AvgActiveSessions"></a>

The **Database load** chart shows how the database activity compares to DB instance capacity as represented by the **Max vCPU** line\. By default, the stacked line chart represents DB load as average active sessions per unit of time\. The DB load is sliced \(grouped\) by wait states\. 

![\[Database load\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/./images/perf_insights_2.png)

### DB load sliced by dimensions<a name="USER_PerfInsights.UsingDashboard.Components.AvgActiveSessions.dims"></a>

You can choose to display load as active sessions grouped by any supported dimensions\. The following table shows which dimensions are supported for the different engines\.


| Dimension | Oracle | SQL Server | PostgreSQL | MySQL | 
| --- | --- | --- | --- | --- | 
|  Host  |  Yes  |  Yes  |  Yes  |  Yes  | 
|  SQL  |  Yes  |  Yes  |  Yes  |  Yes  | 
|  User  |  Yes  |  Yes  |  Yes  |  Yes  | 
|  Waits  |  Yes  |  Yes  |  Yes  |  Yes  | 
|  Plans  |  Yes  |  No  |  No  |  No  | 
|  Application  |  No  |  No  |  Yes  |  No  | 
|  Database  |  No  |  No  |  Yes  |  Yes  | 
|  Session type  |  No  |  No  |  Yes  |  No  | 

The following image shows the dimensions for a PostgreSQL DB instance\.

![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/./images/perf_insights_2b.png)

### DB load details for a dimension item<a name="USER_PerfInsights.UsingDashboard.Components.AvgActiveSessions.item-details"></a>

To see details about a DB load item within a dimension, hover over the item name\. The following image shows details for a SQL statement\.

![\[Database load item details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/./images/perf_insights_2c.png)

To see details for any item for the selected time period in the legend, hover over that item\.

![\[Time period details for DB load\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/./images/perf_insights_3.png)

## Top dimensions table<a name="USER_PerfInsights.UsingDashboard.Components.AvgActiveSessions.TopLoadItemsTable"></a>

The Top dimensions table slices DB load by different dimensions\. A dimension is a category or "slice by" for different characteristics of DB load\. If the dimension is SQL, **Top SQL** shows the SQL statements that contribute the most to DB load\.

![\[Top N dimensions\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/./images/perf_insights_4c.png)

Choose any of the following dimension tabs\.


| Tab | Description | Supported engines | 
| --- | --- | --- | 
|  Top SQL  |  The SQL statements that are currently running  |  All  | 
|  Top waits  |  The event for which the database backend is waiting  |  All  | 
|  Top hosts  |  The host name of the connected client  |  All  | 
|  Top users  |  The user logged in to the database  |  All  | 
|  Top databases  |  The name of the database to which the client is connected  |  PostgreSQL, MySQL, and MariaDB only  | 
|  Top applications  |  The name of the application that is connected to the database  |  PostgreSQL only  | 
|  Top session types  |  The type of the current session  | PostgreSQL only | 

To learn how to analyze queries by using the **Top SQL** tab, see [Overview of the Top SQL tab](USER_PerfInsights.UsingDashboard.Components.AvgActiveSessions.TopLoadItemsTable.TopSQL.md)\.