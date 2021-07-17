# Viewing OS metrics using CloudWatch Logs<a name="USER_Monitoring.OS.CloudWatchLogs"></a>

After you have enabled Enhanced Monitoring for your DB instance, you can view the metrics for your DB instance using CloudWatch Logs, with each log stream representing a single DB instance being monitored\. The log stream identifier is the resource identifier \(`DbiResourceId`\) for the DB instance\.

**To view Enhanced Monitoring log data**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. If necessary, choose the region that your DB instance is in\. For more information, see [Regions and endpoints](https://docs.aws.amazon.com/general/latest/gr/index.html?rande.html) in the *Amazon Web Services General Reference*\.

1. Choose **Logs** in the navigation pane\.

1. Choose **RDSOSMetrics** from the list of log groups\.

   In a Multi\-AZ deployment, log files with `-secondary` appended to the name are for the Multi\-AZ standby replica\.  
![\[Multi-AZ standby replica log file\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/enhanced-monitoring-cloudwatch-secondary.png)

1. Choose the log stream that you want to view from the list of log streams\.