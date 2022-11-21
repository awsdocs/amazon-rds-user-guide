# Monitoring events, logs, and streams in an Amazon RDS DB instance<a name="CHAP_Monitor_Logs_Events"></a>

When you monitor your Amazon RDS databases and your other AWS solutions, your goal is to maintain the following:
+ Reliability
+ Availability
+ Performance
+ Security

[Monitoring metrics in an Amazon RDS instance](CHAP_Monitoring.md) explains how to monitor your instance  using metrics\. A complete solution must also monitor database events, log files, and activity streams\. AWS provides you with the following monitoring tools:
+ *Amazon EventBridge* is a serverless event bus service that makes it easy to connect your applications with data from a variety of sources\. EventBridge delivers a stream of real\-time data from your own applications, Software\-as\-a\-Service \(SaaS\) applications, and AWS services\. EventBridge routes that data to targets such as AWS Lambda\. This way, you can monitor events that happen in services and build event\-driven architectures\. For more information, see the [Amazon EventBridge User Guide](https://docs.aws.amazon.com/eventbridge/latest/userguide/)\.
+ *Amazon CloudWatch Logs* provides a way to monitor, store, and access your log files from Amazon RDS instances, AWS CloudTrail, and other sources\. Amazon CloudWatch Logs can monitor information in the log files and notify you when certain thresholds are met\. You can also archive your log data in highly durable storage\. For more information, see the [Amazon CloudWatch Logs User Guide](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/)\.
+ *AWS CloudTrail* captures API calls and related events made by or on behalf of your AWS account\. CloudTrail delivers the log files to an Amazon S3 bucket that you specify\. You can identify which users and accounts called AWS, the source IP address from which the calls were made, and when the calls occurred\. For more information, see the [AWS CloudTrail User Guide](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/)\.
+ *Database Activity Streams* is an Amazon RDS  feature that provides a near real\-time stream of the activity in your Oracle DB instance\. Amazon RDS pushes activities to an Amazon Kinesis data stream\. The Kinesis stream is created automatically\. From Kinesis, you can configure AWS services such as Amazon Kinesis Data Firehose and AWS Lambda to consume the stream and store the data\.

**Topics**
+ [Viewing logs, events, and streams in the Amazon RDS console](logs-events-streams-console.md)
+ [Monitoring Amazon RDS events](working-with-events.md)
+ [Monitoring Amazon RDS log files](USER_LogAccess.md)
+ [Monitoring Amazon RDS API calls in AWS CloudTrail](logging-using-cloudtrail.md)
+ [Monitoring Amazon RDS for Oracle with Database Activity Streams](DBActivityStreams.md)