# Monitoring Amazon RDS metrics with Amazon CloudWatch<a name="monitoring-cloudwatch"></a>

Amazon CloudWatch is a metrics repository\. The repository collects and processes raw data from Amazon RDS into readable, near real\-time metrics\. For a complete list of Amazon RDS metrics sent to CloudWatch, see [Metrics reference for Amazon RDS](https://docs.aws.amazon.com/en_us/AmazonRDS/latest/UserGuide/metrics-reference.html) \.

By default, Amazon RDS automatically sends metric data to CloudWatch in 1\-minute periods\. Data points with a period of 60 seconds \(1 minute\) are available for 15 days\. This means that you can access historical information and see how your web application or service is performing\.

![\[RDS metrics in AWS CloudWatch\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/rds-cloudwatch.png)

For more information about CloudWatch, see [ What is Amazon CloudWatch?](https://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/WhatIsCloudWatch.html) in the *Amazon CloudWatch User Guide*\. For more information about CloudWatch metrics retention, see [Metrics retention](https://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/cloudwatch_concepts.html#metrics-retention)\.

**Note**  
If you are using Amazon RDS Performance Insights, additional metrics are available\. For more information, see [Amazon CloudWatch metrics for Performance Insights](USER_PerfInsights.Cloudwatch.md)\.

**Topics**
+ [Viewing DB instance metrics in the CloudWatch console and CLI](metrics_dimensions.md)
+ [Creating CloudWatch alarms to monitor Amazon RDS](creating_alarms.md)
+ [Tutorial: Creating an Amazon CloudWatch alarm for Multi\-AZ DB cluster replica lag](multi-az-db-cluster-cloudwatch-alarm.md)