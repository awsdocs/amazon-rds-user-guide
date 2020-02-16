# Performance Insights Metrics Published to Amazon CloudWatch<a name="USER_PerfInsights.Cloudwatch"></a>

Performance Insights automatically publishes metrics to Amazon CloudWatch\. The same data can be queried from Performance Insights, but having the metrics in CloudWatch makes it easy to add CloudWatch alarms\. It also makes it easy to add the metrics to existing CloudWatch Dashboards\.


| Metric | Description | 
| --- | --- | 
|  DBLoad  |  The number of active sessions for the DB engine\. Typically, you want the data for the average number of active sessions\. In Performance Insights, this data is queried as `db.load.avg`\.  | 
|  DBLoadCPU  |  The number of active sessions where the wait event type is CPU\. In Performance Insights, this data is queried as `db.load.avg`, filtered by the wait event type `CPU`\.  | 
|  DBLoadNonCPU  |  The number of active sessions where the wait event type is not CPU\.  | 

**Note**  
These metrics are published to CloudWatch only if there is load on the DB instance\.

You can examine these metrics using the CloudWatch console, the AWS CLI, or the CloudWatch API\.

For example, you can get the statistics for the `DBLoad` metric by running the [get\-metric\-statistics](https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/get-metric-statistics.html) command\.

```
aws cloudwatch get-metric-statistics --region us-west-2 --namespace AWS/RDS --metric-name DBLoad  --period 60 --statistics Average --start-time 1532035185 --end-time 1532036185 --dimensions  Name=DBInstanceIdentifier,Value=db-loadtest-0
```

This example generates output similar to the following\.

```
		{
		"Datapoints": [
		{
		"Timestamp": "2018-07-19T21:30:00Z",
		"Unit": "None",
		"Average": 2.1
		},
		{
		"Timestamp": "2018-07-19T21:34:00Z",
		"Unit": "None",
		"Average": 1.7
		},
		{
		"Timestamp": "2018-07-19T21:35:00Z",
		"Unit": "None",
		"Average": 2.8
		},
		{
		"Timestamp": "2018-07-19T21:31:00Z",
		"Unit": "None",
		"Average": 1.5
		},
		{
		"Timestamp": "2018-07-19T21:32:00Z",
		"Unit": "None",
		"Average": 1.8
		},
		{
		"Timestamp": "2018-07-19T21:29:00Z",
		"Unit": "None",
		"Average": 3.0
		},
		{
		"Timestamp": "2018-07-19T21:33:00Z",
		"Unit": "None",
		"Average": 2.4
		}
		],
		"Label": "DBLoad"
		}
```

For more information about CloudWatch, see [What is Amazon CloudWatch?](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/WhatIsCloudWatch.html) in the *Amazon CloudWatch User Guide*\. 