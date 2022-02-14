# Viewing metrics in the Amazon RDS console<a name="USER_Monitoring"></a>

Amazon RDS integrates with Amazon CloudWatch to display a variety of RDS DB instance metrics in the RDS console\. For descriptions of these metrics, see [Metrics reference for Amazon RDS](metrics-reference.md)\.

The **Monitoring** tab for your RDS DB instance shows the following categories of metrics:
+ **CloudWatch** – Shows the Amazon CloudWatch metrics for that you can access in the RDS console\. You can also access these metrics in the CloudWatch console\. Each metric includes a graph that shows the metric monitored over a specific time span\. For a list of CloudWatch metrics, see [Amazon CloudWatch metrics for Amazon RDS](rds-metrics.md)\.
+ **Enhanced monitoring** – Shows a summary of operating\-system metrics when your RDS DB instance has turned on Enhanced Monitoring\. RDS delivers the metrics from Enhanced Monitoring to your Amazon CloudWatch Logs account\. Each OS metric includes a graph showing the metric monitored over a specific time span\. For an overview, see [Monitoring OS metrics with Enhanced Monitoring](USER_Monitoring.OS.md)\. For a list of Enhanced Monitoring metrics, see [OS metrics in Enhanced Monitoring](USER_Monitoring-Available-OS-Metrics.md)\.
+ **OS Process list** – Shows details for each process running in your DB instance\.
+ **Performance Insights** – Opens the Amazon RDS Performance Insights dashboard for a DB instance\. For an overview of Performance Insights, see [Monitoring DB load with Performance Insights on Amazon RDS](USER_PerfInsights.md)\. For a list of Performance Insights metrics, see [ Amazon CloudWatch metrics for Performance InsightsCloudWatch metrics for Performance Insights  Performance Insights automatically publishes metrics to Amazon CloudWatch\. The same data can be queried from Performance Insights, but having the metrics in CloudWatch makes it easy to add CloudWatch alarms\. It also makes it easy to add the metrics to existing CloudWatch Dashboards\. 


| Metric | Description | 
| --- | --- | 
|  DBLoad  |  The number of active sessions for the DB engine\. Typically, you want the data for the average number of active sessions\. In Performance Insights, this data is queried as `db.load.avg`\.  | 
|  DBLoadCPU  |  The number of active sessions where the wait event type is CPU\. In Performance Insights, this data is queried as `db.load.avg`, filtered by the wait event type `CPU`\.  | 
|  DBLoadNonCPU  |  The number of active sessions where the wait event type is not CPU\.  |   These metrics are published to CloudWatch only if there is load on the DB instance\.  You can examine these metrics using the CloudWatch console, the AWS CLI, or the CloudWatch API\. For example, you can get the statistics for the `DBLoad` metric by running the [get\-metric\-statistics](https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/get-metric-statistics.html) command\. 

```
aws cloudwatch get-metric-statistics \
    --region us-west-2 \
    --namespace AWS/RDS \
    --metric-name DBLoad  \
    --period 60 \
    --statistics Average \
    --start-time 1532035185 \
    --end-time 1532036185 \
    --dimensions Name=DBInstanceIdentifier,Value=db-loadtest-0
``` This example generates output similar to the following\. 

```
{
		"Datapoints": [
		{
		"Timestamp": "2021-07-19T21:30:00Z",
		"Unit": "None",
		"Average": 2.1
		},
		{
		"Timestamp": "2021-07-19T21:34:00Z",
		"Unit": "None",
		"Average": 1.7
		},
		{
		"Timestamp": "2021-07-19T21:35:00Z",
		"Unit": "None",
		"Average": 2.8
		},
		{
		"Timestamp": "2021-07-19T21:31:00Z",
		"Unit": "None",
		"Average": 1.5
		},
		{
		"Timestamp": "2021-07-19T21:32:00Z",
		"Unit": "None",
		"Average": 1.8
		},
		{
		"Timestamp": "2021-07-19T21:29:00Z",
		"Unit": "None",
		"Average": 3.0
		},
		{
		"Timestamp": "2021-07-19T21:33:00Z",
		"Unit": "None",
		"Average": 2.4
		}
		],
		"Label": "DBLoad"
		}
``` For more information about CloudWatch, see [What is Amazon CloudWatch?](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/WhatIsCloudWatch.html) in the *Amazon CloudWatch User Guide*\.  ](USER_PerfInsights.Cloudwatch.md)\.

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