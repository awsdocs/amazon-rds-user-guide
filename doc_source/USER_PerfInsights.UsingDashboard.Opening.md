# Opening the Performance Insights dashboard<a name="USER_PerfInsights.UsingDashboard.Opening"></a>

To see the Performance Insights dashboard, use the following procedure\.

**To view the Performance Insights dashboard in the AWS Management Console**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Performance Insights**\.

1. Choose a DB instance\. The Performance Insights dashboard is shown for that DB instance\.

   For DB instances with Performance Insights enabled, you can also reach the dashboard by choosing the **Sessions** item in the list of DB instances\. Under **Current activity**, the **Sessions** item shows the database load in average active sessions over the last five minutes\. The bar graphically shows the load\. When the bar is empty, the DB instance is idle\. As the load increases, the bar fills with blue\. When the load passes the number of virtual CPUs \(vCPUs\) on the DB instance class, the bar turns red, indicating a potential bottleneck\.  
![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/./images/perf_insights_0a.png)

1. \(Optional\) Choose a different time interval by selecting a button in the upper right\. For example, to change the interval to 5 hours, select **5h**\.  
![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/./images/perf_insights_0c.png)

   In the following screenshot, the DB load interval is 5 hours\.  
![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/./images/perf_insights_1.png)

1. \(Optional\) To refresh your data automatically, enable **Auto refresh**\.  
![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/./images/perf_insights_1b.png)

   The Performance Insight dashboard automatically refreshes with new data\. The refresh rate depends on the amount of data displayed: 
   + 5 minutes refreshes every 5 seconds\.
   + 1 hour refreshes every minute\.
   + 5 hours refreshes every minute\.
   + 24 hours refreshes every 5 minutes\.
   + 1 week refreshes every hour\.