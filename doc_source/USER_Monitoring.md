# Viewing metrics in the Amazon RDS console<a name="USER_Monitoring"></a>

Amazon RDS integrates with Amazon CloudWatch to display a variety of RDS DB instance metrics in the RDS console\. For descriptions of these metrics, see [Metrics reference for Amazon RDS](metrics-reference.md)\.

The **Monitoring** tab for your RDS DB instance shows the following categories of metrics:
+ **CloudWatch** – Shows the Amazon CloudWatch metrics for that you can access in the RDS console\. You can also access these metrics in the CloudWatch console\. Each metric includes a graph that shows the metric monitored over a specific time span\. For a list of CloudWatch metrics, see [Amazon CloudWatch metrics for Amazon RDS](rds-metrics.md)\.
+ **Enhanced monitoring** – Shows a summary of operating\-system metrics when your RDS DB instance has turned on Enhanced Monitoring\. RDS delivers the metrics from Enhanced Monitoring to your Amazon CloudWatch Logs account\. Each OS metric includes a graph showing the metric monitored over a specific time span\. For an overview, see [Monitoring OS metrics with Enhanced Monitoring](USER_Monitoring.OS.md)\. For a list of Enhanced Monitoring metrics, see [OS metrics in Enhanced Monitoring](USER_Monitoring-Available-OS-Metrics.md)\.
+ **OS Process list** – Shows details for each process running in your DB instance\.
+ **Performance Insights** – Opens the Amazon RDS Performance Insights dashboard for a DB instance\. For an overview of Performance Insights, see [Monitoring DB load with Performance Insights on Amazon RDS](USER_PerfInsights.md)\. For a list of Performance Insights metrics, see [Amazon CloudWatch metrics for Performance Insights](USER_PerfInsights.Cloudwatch.md)\.

**To view metrics for your DB instance in the RDS console**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the name of the DB instance that you want to monitor\.

   The database page appears\. The following example shows an Oracle database named `orclb`\.  
![\[Database page with monitoring tab shown\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/oracle-with-monitoring-tab.png)

1. Scroll down and choose **Monitoring**\.

   The monitoring section appears\. By default, CloudWatch metrics are shown\. For descriptions of these metrics, see [Amazon CloudWatch metrics for Amazon RDS](rds-metrics.md)\.  
![\[Database page with monitoring tab shown\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/oracle-monitoring-subpage.png)

1. Choose **Monitoring** to see the metric categories\.  
![\[Monitoring options\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/monitoring-options.png)

1. Choose the category of metrics that you want to see\.

   The following example shows Enhanced Monitoring metrics\. For descriptions of these metrics, see [OS metrics in Enhanced Monitoring](USER_Monitoring-Available-OS-Metrics.md)\.  
![\[Enhanced Monitoring\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/oracle-em-metrics.png)
**Tip**  
To choose the time range of the metrics represented by the graphs, you can use the time range list\.  
To bring up a more detailed view, you can choose any graph\. You can also apply metric\-specific filters to the data\. 