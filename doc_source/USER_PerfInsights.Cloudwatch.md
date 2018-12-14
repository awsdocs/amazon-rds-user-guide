# Performance Insights Metrics Published to Amazon CloudWatch<a name="USER_PerfInsights.Cloudwatch"></a>

Performance Insights automatically publishes metrics to Amazon CloudWatch\. Each of these per second metrics represents the average over the last 60 seconds\.


| Metric | Description | 
| --- | --- | 
|  DBLoad  |  The average number of active sessions for the DB engine\.  | 
|  DBLoadCPU  |  The number of active sessions where the wait event type is CPU\.  | 
|  DBLoadNonCPU  |  The number of active sessions where the wait event type is not CPU\.  | 

You can examine these metrics using the CloudWatch console, the AWS CLI, or the CloudWatch API\.

For example, you can get the statistics for the `DBLoad` metric by running the [get\-metric\-statistics](https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/get-metric-statistics.html) command\.

```
aws cloudwatch get-metric-statistics --region us-west-2 --namespace AWS/RDS --metric-name DBLoad  --period 60 --statistics Sum --start-time 1532035185 --end-time 1532036185 --dimensions  Name=DBInstanceIdentifier,Value=db-loadtest-0
```

This example generates output similar to the following\.

```
		{
		"Datapoints": [
		{
		"Timestamp": "2018-07-19T21:30:00Z",
		"Unit": "None",
		"Sum": 1380.0
		},
		{
		"Timestamp": "2018-07-19T21:34:00Z",
		"Unit": "None",
		"Sum": 1380.0
		},
		{
		"Timestamp": "2018-07-19T21:35:00Z",
		"Unit": "None",
		"Sum": 1380.0
		},
		{
		"Timestamp": "2018-07-19T21:31:00Z",
		"Unit": "None",
		"Sum": 1380.0
		},
		{
		"Timestamp": "2018-07-19T21:32:00Z",
		"Unit": "None",
		"Sum": 1380.0
		},
		{
		"Timestamp": "2018-07-19T21:29:00Z",
		"Unit": "None",
		"Sum": 8280.0
		},
		{
		"Timestamp": "2018-07-19T21:33:00Z",
		"Unit": "None",
		"Sum": 1380.0
		}
		],
		"Label": "DBLoad"
		}
```

For more information about CloudWatch, see [What is Amazon CloudWatch?](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring//WhatIsCloudWatch.html) in the *Amazon CloudWatch User Guide*\. 