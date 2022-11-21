# Overview of Amazon RDS event notification<a name="USER_Events.overview"></a>

Amazon RDS groups events into categories that you can subscribe to so that you can be notified when an event in that category occurs\.

**Topics**
+ [RDS resources eligible for event subscription](#USER_Events.overview.resources)
+ [Basic process for subscribing to Amazon RDS event notifications](#USER_Events.overview.process)
+ [Delivery of RDS event notifications](#USER_Events.overview.subscriptions)
+ [Billing for Amazon RDS event notifications](#USER_Events.overview.billing)
+ [Examples of Amazon RDS events](#events-examples)

## RDS resources eligible for event subscription<a name="USER_Events.overview.resources"></a>

You can subscribe to an event category for the following resources:
+ DB instance
+ DB snapshot
+ DB parameter group
+ DB security group
+ RDS Proxy
+ Custom engine version

For example, if you subscribe to the backup category for a given DB instance, you're notified whenever a backup\-related event occurs that affects the DB instance\. If you subscribe to a configuration change category for a DB instance, you're notified when the DB instance is changed\. You also receive notification when an event notification subscription changes\.

You might want to create several different subscriptions\. For example, you might create one subscription that receives all event notifications for all DB instances and another subscription that includes only critical events for a subset of the DB instances\. For the second subscription, specify one or more DB instances in the filter\.

## Basic process for subscribing to Amazon RDS event notifications<a name="USER_Events.overview.process"></a>

The process for subscribing to Amazon RDS event notification is as follows:

1. You create an Amazon RDS event notification subscription by using the Amazon RDS console, AWS CLI, or API\.

   Amazon RDS uses the ARN of an Amazon SNS topic to identify each subscription\. The Amazon RDS console creates the ARN for you when you create the subscription\. Create the ARN by using the Amazon SNS console, the AWS CLI, or the Amazon SNS API\.

1. Amazon RDS sends an approval email or SMS message to the addresses you submitted with your subscription\.

1. You confirm your subscription by choosing the link in the notification you received\.

1. The Amazon RDS console updates the **My Event Subscriptions** section with the status of your subscription\.

1. Amazon RDS begins sending the notifications to the addresses that you provided when you created the subscription\.

To learn about identity and access management when using Amazon SNS, see [Identity and access management in Amazon SNS](https://docs.aws.amazon.com/sns/latest/dg/sns-authentication-and-access-control.html) in the *Amazon Simple Notification Service Developer Guide*\.

You can use AWS Lambda to process event notifications from a DB instance\. For more information, see [Using AWS Lambda with Amazon RDS](https://docs.aws.amazon.com/lambda/latest/dg/services-rds.html) in the *AWS Lambda Developer Guide*\.

## Delivery of RDS event notifications<a name="USER_Events.overview.subscriptions"></a>

Amazon RDS sends notifications to the addresses that you provide when you create the subscription\. The notification can include message attributes which provide structured metadata about the message\. For more information about message attributes, see [Amazon RDS event categories and event messages](USER_Events.Messages.md)\.

Event notifications might take up to five minutes to be delivered\.

**Important**  
Amazon RDS doesn't guarantee the order of events sent in an event stream\. The event order is subject to change\.

When Amazon SNS sends a notification to a subscribed HTTP or HTTPS endpoint, the POST message sent to the endpoint has a message body that contains a JSON document\. For more information, see [Amazon SNS message and JSON formats](https://docs.aws.amazon.com/sns/latest/dg/sns-message-and-json-formats.html) in the *Amazon Simple Notification Service Developer Guide*\.

You can configure SNS to notify you with text messages\. For more information, see [ Mobile text messaging \(SMS\)](https://docs.aws.amazon.com/sns/latest/dg/sns-mobile-phone-number-as-subscriber.html) in the *Amazon Simple Notification Service Developer Guide*\.

To turn off notifications without deleting a subscription, choose **No** for **Enabled** in the Amazon RDS console\. Or you can set the `Enabled` parameter to `false` using the AWS CLI or Amazon RDS API\.

## Billing for Amazon RDS event notifications<a name="USER_Events.overview.billing"></a>

Billing for Amazon RDS event notification is through Amazon SNS\. Amazon SNS fees apply when using event notification\. For more information about Amazon SNS billing, see [ Amazon Simple Notification Service pricing](http://aws.amazon.com/sns/#pricing)\.

## Examples of Amazon RDS events<a name="events-examples"></a>

The following examples illustrate different types of Amazon RDS events in JSON format\. For a tutorial that shows you how to capture and view events in JSON format, see [Tutorial: Log DB instance state changes using Amazon EventBridge](rds-cloud-watch-events.md#log-rds-instance-state)\.

**Topics**
+ [Example of a DB instance event](#rds-cloudwatch-events.db-instances)
+ [Example of a DB parameter group event](#rds-cloudwatch-events.db-parameter-groups)
+ [Example of a DB snapshot event](#rds-cloudwatch-events.db-snapshots)

### Example of a DB instance event<a name="rds-cloudwatch-events.db-instances"></a>

The following is an example of a DB instance event in JSON format\. The event shows that RDS performed a multi\-AZ failover for the instance named `my-db-instance`\. The event ID is RDS\-EVENT\-0049\.

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
    "Message": "A Multi-AZ failover has completed.",
    "SourceIdentifier": "rds:my-db-instance",
    "EventID": "RDS-EVENT-0049"
  }
}
```

### Example of a DB parameter group event<a name="rds-cloudwatch-events.db-parameter-groups"></a>

The following is an example of a DB parameter group event in JSON format\. The event shows that the parameter `time_zone` was updated in parameter group `my-db-param-group`\. The event ID is RDS\-EVENT\-0037\.

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
    "Message": "Updated parameter time_zone to UTC with apply method immediate",
    "SourceIdentifier": "rds:my-db-param-group",
    "EventID": "RDS-EVENT-0037"
  }
}
```

### Example of a DB snapshot event<a name="rds-cloudwatch-events.db-snapshots"></a>

The following is an example of a DB snapshot event in JSON format\. The event shows the deletion of the snapshot named `my-db-snapshot`\. The event ID is RDS\-EVENT\-0041\.

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
    "Message": "Deleted manual snapshot",
    "SourceIdentifier": "rds:my-db-snapshot",
    "EventID": "RDS-EVENT-0041"
  }
}
```