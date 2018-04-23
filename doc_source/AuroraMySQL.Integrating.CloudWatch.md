# Publishing Audit Log Data From Amazon Aurora to Amazon CloudWatch Logs<a name="AuroraMySQL.Integrating.CloudWatch"></a>

You can configure an Amazon Aurora DB cluster to publish audit log events to a log group in Amazon CloudWatch Logs\. By using audit log events, you can perform real\-time analysis of the audit events observed on your DB cluster, using CloudWatch to create alarms and view metrics\. You can use CloudWatch Logs to store your log data in highly durable storage\. You can also change the log retention setting so that any log events older than this setting are automatically deleted\. The CloudWatch Logs agent makes it easier to quickly send both rotated and nonrotated log data off of a host and into the log service\. You can then access the raw log data when you need it\.

**Note**  
Publishing audit log data from Aurora to CloudWatch Logs can incur data ingestion and archived storage charges on CloudWatch Logs\. You are charged only for published audit log data that exceeds the free tier provided by CloudWatch Logs\. For more information, see [Amazon CloudWatch Pricing](https://aws.amazon.com/cloudwatch/pricing/)\.

## Giving Aurora Access to CloudWatch Logs<a name="AuroraMySQL.Integrating.CloudWatch.Access"></a>

Before you can publish audit logs to Amazon CloudWatch Logs, you must first give your Aurora MySQL DB cluster permission to access CloudWatch Logs\.

**To give Aurora MySQL access to CloudWatch Logs**

1. Create an AWS Identity and Access Management \(IAM\) policy that provides the permissions that allow your Aurora MySQL DB cluster to publish audit logs to CloudWatch Logs\. For instructions, see [Creating an IAM Policy to Access CloudWatch Logs Resources](AuroraMySQL.Integrating.Authorizing.IAM.CWCreatePolicy.md)\.

1. Create an IAM role, and attach the IAM policy you created in [Creating an IAM Policy to Access CloudWatch Logs Resources](AuroraMySQL.Integrating.Authorizing.IAM.CWCreatePolicy.md) to the new IAM role\. For instructions, see [Creating an IAM Role to Allow Amazon Aurora to Access AWS Services](AuroraMySQL.Integrating.Authorizing.IAM.CreateRole.md)\.

1. Set the `aws_default_logs_role` DB cluster parameter to the Amazon Resource Name \(ARN\) of the new IAM role\. If an IAM role isn't specified for `aws_default_logs_role`, the logs aren't streamed to CloudWatch\. If the specified role doesn't have the required permissions, we report this situation through events\.

   For more information about DB cluster parameters, see [Amazon Aurora MySQL Parameters](AuroraMySQL.Reference.md#AuroraMySQL.Reference.ParameterGroups)\.

1. To permit database users in an Aurora MySQL DB cluster to access CloudWatch, associate the role that you created in [Creating an IAM Role to Allow Amazon Aurora to Access AWS Services](AuroraMySQL.Integrating.Authorizing.IAM.CreateRole.md) with the DB cluster\. For information about associating an IAM role with a DB cluster, see [Associating an IAM Role with an Amazon Aurora MySQL DB Cluster](AuroraMySQL.Integrating.Authorizing.IAM.AddRoleToDBCluster.md)\.

## Enabling Audit Log Publishing to Amazon CloudWatch Logs in an Aurora DB Cluster<a name="AuroraMySQL.Integrating.CloudWatch.Enable"></a>

To start exporting audit logs to Amazon CloudWatch Logs, you first enable Advanced Auditing\. For more information about enabling Advanced Auditing, see [Enabling Advanced Auditing](AuroraMySQL.Auditing.md#AuroraMySQL.Auditing.Enable)\.

After you enable Advanced Auditing and define the events to be audited, you can enable audit log publishing to CloudWatch Logs by setting the cluster\-level DB parameter `server_audit_logs_upload`\. This parameter enables or disables audit log exporting\. Set it to 1 to enable audit log exporting; it defaults to 0\.

You can configure audit log publishing to CloudWatch Logs in the same way that you configure Advanced Auditing, by setting these parameters in the parameter group used by your DB cluster\. You can use the procedure shown in [Modifying Parameters in a DB Parameter Group](USER_WorkingWithParamGroups.md#USER_WorkingWithParamGroups.Modifying) to modify DB cluster parameters using the AWS Management Console\. You can use the `modify-db-cluster-parameter-group` AWS CLI command or the `ModifyDBClusterParameterGroup` Amazon RDS API command to modify DB cluster parameters programmatically\. Modifying these parameters doesn't require a DB cluster restart\.

**Note**  
You can identify the DB instance for each event by using the `serverhost` field included in the audit log details\. The full content of the event is published to CloudWatch Logs\. For more information about the `serverhost` field, see [Audit Log Details](AuroraMySQL.Auditing.md#AuroraMySQL.Auditing.Logs)\.  
CloudWatch Logs events persist after your DB instances are terminated\. You can configure the log retention period for each log group using the CloudWatch Logs Management Console, the AWS CLI, or the CloudWatch Logs API\.  
For more information about CloudWatch Logs, see [What is Amazon CloudWatch Logs?](http://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/WhatIsCloudWatchLogs.html) 

Audit log data is automatically encrypted in transit and at rest by CloudWatch Logs, using its default AWS Key Management Service \(AWS KMS\) encryption key\. Currently, you can't specify a custom AWS KMS key for encrypting audit log data in CloudWatch Logs\.

## Monitoring Audit Log Events in Amazon CloudWatch<a name="AuroraMySQL.Integrating.CloudWatch.Stream"></a>

After enabling audit log events, you can monitor the events in Amazon CloudWatch Logs\. A new log group is automatically created for the Aurora DB cluster under the following prefix, in which `cluster-name` represents the DB cluster name\.

```
/aws/rds/cluster/cluster-name
```

If a log group with the specified name exists, Aurora uses that log group to export audit log data for the Aurora DB cluster\. You can use automated configuration, such as AWS CloudFormation, to create log groups with predefined log retention periods, metric filters, and customer access\. Otherwise, a new log group is automatically created using the default log retention period, **Never Expire**, in CloudWatch Logs\. You can use the CloudWatch Logs console, the AWS CLI, or the CloudWatch Logs API to change the log retention period\. For more information about changing log retention periods in CloudWatch Logs, see [Change Log Data Retention in CloudWatch Logs](http://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/Working-with-log-groups-and-streams.html#SettingLogRetention)\.

All the audit events from all of the DB instances in a DB cluster are pushed to this log group using different log streams\. You can use the CloudWatch Logs console, the AWS CLI, or the CloudWatch Logs API to search for information within the log events for a DB cluster\. For more information about searching and filtering log data, see [Searching and Filtering Log Data](http://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/MonitoringLogData.html)\.

**Note**  
Aurora doesn't delete existing log groups or log streams if exporting audit log data is disabled\. Existing audit log data remains available in CloudWatch Logs, depending on log retention, and you still incur charges for stored audit log data\. You can delete log streams and log groups using the CloudWatch Logs console, the AWS CLI, or the CloudWatch Logs API\.