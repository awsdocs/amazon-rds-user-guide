# Logging Performance Insights Operations by Using AWS CloudTrail<a name="USER_PerfInsights.CloudTrail"></a>

Performance Insights is integrated with AWS CloudTrail\. CloudTrail captures low\-level API requests made by or on behalf of Performance Insights in your AWS account\. CloudTrail then delivers the log files to an Amazon S3 bucket that you specify\. CloudTrail captures calls made from the Performance Insights in the RDS console or from the Performance Insights low\-level API\. 

Using the information collected by CloudTrail, you can determine what request was made to Performance Insights\. You can also determine the source IP address it was made from, who made it, when it was made, and so on\. CloudTrail logging is automatically enabled in your AWS account\. To learn more about CloudTrail, see the [https://docs.aws.amazon.com/awscloudtrail/latest/userguide/](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/)\.

Any low\-level API calls made to Performance Insights actions are tracked in log files\. Performance Insights records are written together with other AWS service records in a log file\. CloudTrail determines when to create and write to a new file based on a time period and file size\. The following API operations are supported:
+ [DescribeDimensionKeys](https://docs.aws.amazon.com/performance-insights/latest/APIReference/API_DescribeDimensionKeys.html)
+ [GetResourceMetrics](https://docs.aws.amazon.com/performance-insights/latest/APIReference/API_GetResourceMetrics.html)