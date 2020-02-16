# Getting CloudWatch Events and Amazon EventBridge Events for Amazon RDS<a name="rds-cloud-watch-events"></a>

Amazon CloudWatch Events and Amazon EventBridge both enable you to automate AWS services and respond to system events such as application availability issues or resource changes\. Events from AWS services are delivered to CloudWatch Events and EventBridge nearly in real time\. You can write simple rules to indicate which events interest you and what automated actions to take when an event matches a rule\.

You can set a variety of targets—such as an AWS Lambda function or an Amazon SNS topic—which receive events in JSON format\. For more information, see the [Amazon CloudWatch Events User Guide](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/) and the [Amazon EventBridge User Guide](https://docs.aws.amazon.com/eventbridge/latest/userguide/)\.

For example, you can configure Amazon RDS to send events to CloudWatch Events or Amazon EventBridge whenever a DB instance is created or deleted\.

**Topics**
+ [Sending Amazon RDS Events to CloudWatch Events](#rds-cloudwatch-events.sending-to-cloudwatch-events)
+ [DB Instance Events](#rds-cloudwatch-events.db-instances)
+ [DB Parameter Group Events](#rds-cloudwatch-events.db-parameter-groups)
+ [DB Security Group Events](#rds-cloudwatch-events.db-security-groups)
+ [DB Snapshot Events](#rds-cloudwatch-events.db-snapshots)

## Sending Amazon RDS Events to CloudWatch Events<a name="rds-cloudwatch-events.sending-to-cloudwatch-events"></a>

You can create CloudWatch Events rules to send Amazon RDS events to CloudWatch Events\.

Use the following steps to create a CloudWatch Events rule that triggers on an event emitted by an AWS service\.

**To create a rule that triggers on an event:**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. Under **Events** in the navigation pane, choose **Rules**\.

1. Choose **Create rule**\.

1. For **Event Source**, do the following:

   1. Choose **Event Pattern**\.

   1. For **Service Name**, choose **Relational Database Service \(RDS\)**\.

   1. For **Event Type**, choose the type of Amazon RDS resource that triggers the event\. For example, if a DB instance triggers the event, choose **RDS DB Instance Event**\.

1. For **Targets**, choose **Add Target**, then choose the **CloudWatch log group**\. 

1. For **Log Group**, enter a name for the log group to store the events\. 

1. Choose **Configure details**\. For **Rule definition**, type a name and description for the rule\. 

1. Choose **Create rule**\.

## DB Instance Events<a name="rds-cloudwatch-events.db-instances"></a>

The following is an example of a DB instance event\.

```
{
  "version": "0",
  "id": "68f6e973-1a0c-d37b-f2f2-94a7f62ffd4e",
  "detail-type": "RDS DB Instance Event",
  "source": "aws.rds",
  "account": "123456789012",
  "time": "2018-09-27T22:36:43Z",
  "region": "us-east-1",
  "resources": [
    "arn:aws:rds:us-east-1:123456789012:db:my-db-instance"
  ],
  "detail": {
    "EventCategories": [
      "failover"
    ],
    "SourceType": "DB_INSTANCE",
    "SourceArn": "arn:aws:rds:us-east-1:123456789012:db:my-db-instance",
    "Date": "2018-09-27T22:36:43.292Z",
    "SourceIdentifier": "rds:my-db-instance",
    "Message": "A Multi-AZ failover has completed."
  }
}
```

## DB Parameter Group Events<a name="rds-cloudwatch-events.db-parameter-groups"></a>

The following is an example of a DB parameter group event\.

```
{
  "version": "0",
  "id": "844e2571-85d4-695f-b930-0153b71dcb42",
  "detail-type": "RDS DB Parameter Group Event",
  "source": "aws.rds",
  "account": "123456789012",
  "time": "2018-10-06T12:26:13Z",
  "region": "us-east-1",
  "resources": [
    "arn:aws:rds:us-east-1:123456789012:pg:my-db-param-group"
  ],
  "detail": {
    "EventCategories": [
      "configuration change"
    ],
    "SourceType": "DB_PARAM",
    "SourceArn": "arn:aws:rds:us-east-1:123456789012:pg:my-db-param-group",
    "Date": "2018-10-06T12:26:13.882Z",
    "SourceIdentifier": "rds:my-db-param-group",
    "Message": "Updated parameter time_zone to UTC with apply method immediate"
  }
}
```

## DB Security Group Events<a name="rds-cloudwatch-events.db-security-groups"></a>

The following is an example of a DB security group event\.

```
{
  "version": "0",
  "id": "844e2571-85d4-695f-b930-0153b71dcb42",
  "detail-type": "RDS DB Security Group Event",
  "source": "aws.rds",
  "account": "123456789012",
  "time": "2018-10-06T12:26:13Z",
  "region": "us-east-1",
  "resources": [
    "arn:aws:rds:us-east-1:123456789012:secgrp:my-security-group"
  ],
  "detail": {
    "EventCategories": [
      "configuration change"
    ],
    "SourceType": "SECURITY_GROUP",
    "SourceArn": "arn:aws:rds:us-east-1:123456789012:secgrp:my-security-group",
    "Date": "2018-10-06T12:26:13.882Z",
    "SourceIdentifier": "rds:my-security-group",
    "Message": "Applied change to security group"
  }
}
```

## DB Snapshot Events<a name="rds-cloudwatch-events.db-snapshots"></a>

The following is an example of a DB snapshot event\.

```
{
  "version": "0",
  "id": "844e2571-85d4-695f-b930-0153b71dcb42",
  "detail-type": "RDS DB Snapshot Event",
  "source": "aws.rds",
  "account": "123456789012",
  "time": "2018-10-06T12:26:13Z",
  "region": "us-east-1",
  "resources": [
    "arn:aws:rds:us-east-1:123456789012:snapshot:rds:my-db-snapshot"
  ],
  "detail": {
    "EventCategories": [
      "deletion"
    ],
    "SourceType": "SNAPSHOT",
    "SourceArn": "arn:aws:rds:us-east-1:123456789012:snapshot:rds:my-db-snapshot",
    "Date": "2018-10-06T12:26:13.882Z",
    "SourceIdentifier": "rds:my-db-snapshot",
    "Message": "Deleted manual snapshot"
  }
}
```