# Using the Performance Insights Dashboard<a name="USER_PerfInsights.UsingDashboard"></a>

The Performance Insights dashboard contains database performance information to help you analyze and troubleshoot performance issues\. On the main dashboard page, you can view information about the database load\. You can also drill into details for a particular wait state, SQL query, host, or user\.

**Topics**
+ [Opening the Performance Insights Dashboard](#USER_PerfInsights.UsingDashboard.Opening)
+ [Performance Insights Dashboard Components](#USER_PerfInsights.UsingDashboard.Components)
+ [Analyzing Database Load Using the Performance Insights Dashboard](#USER_PerfInsights.UsingDashboard.AnalyzeDBLoad)

## Opening the Performance Insights Dashboard<a name="USER_PerfInsights.UsingDashboard.Opening"></a>

To see the Performance Insights dashboard, use the following procedure\.

**To view the Performance Insights dashboard in the AWS Management Console**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Performance Insights**\.

1. Choose a DB instance\. The Performance Insights dashboard is displayed for that DB instance\.

   For DB instances with Performance Insights enabled, you can also reach the dashboard by choosing the **Sessions** item in the list of DB instances\. Under **Current activity**, the **Sessions** item shows the database load in average active sessions over the last five minutes\. The bar graphically shows the load\. When the bar is empty, the DB instance is idle\. As the load increases, the bar fills with blue\. When the load passes the number of virtual CPUs \(vCPUs\) on the DB instance class, the bar turns red, indicating a potential bottleneck\.  
![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_insights_0a.png)![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/)![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/)

   The following image shows the dashboard for a DB instance\.  
![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_insights_0b.png)![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/)![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/)

By default, the Performance Insights dashboard shows data for the last 60 minutes\. You can modify it to display data for the last 5 minutes, 60 minutes, 5 hours, 24 hours, or 1 week\. You can also show all of the data available\.

The Performance Insight dashboard automatically refreshes with new data\. The refresh rate depends on the amount of data displayed: 
+ 5 minutes refreshes every 5 seconds\.
+ 1 hour and 5 hours both refresh every minute\.
+ 24 hours refreshes every 5 minutes\.
+ 1 week refreshes every hour\.

![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_insights_1.png)![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/)![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/)

## Performance Insights Dashboard Components<a name="USER_PerfInsights.UsingDashboard.Components"></a>

The dashboard is divided into two parts:

1. **Average Active Sessions chart** – Shows how the database load compares to DB instance capacity as represented by the **Max CPU** line\.

1.  **Top load items table** – Shows the top items contributing to database load\.

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

## Analyzing Database Load Using the Performance Insights Dashboard<a name="USER_PerfInsights.UsingDashboard.AnalyzeDBLoad"></a>

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