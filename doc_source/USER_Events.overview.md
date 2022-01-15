# Overview of Amazon RDS event notification<a name="USER_Events.overview"></a>

Amazon RDS groups events into categories that you can subscribe to so that you can be notified when an event in that category occurs\. Amazon RDS event notification is only available for unencrypted SNS topics\. If you specify an encrypted SNS topic, event notifications aren't sent for the topic\.

## RDS resources eligible for event subscription<a name="USER_Events.overview.resources"></a>

You can subscribe to an event category for the following resources:
+ DB instance
+ DB snapshot
+ DB parameter group
+ DB security group
+ RDS Proxy

For example, if you subscribe to the backup category for a given DB instance, you're notified whenever a backup\-related event occurs that affects the DB instance\. If you subscribe to a configuration change category for a DB security group, you're notified when the DB security group is changed\. You also receive notification when an event notification subscription changes\.

You might want to create several different subscriptions\. For example, you might create one subscription receiving all event notifications and another subscription that includes only critical events for your production DB instances\.

## Basic process for subscribing to Amazon RDS event notifications<a name="USER_Events.overview.process"></a>

The process for subscribing to Amazon RDS event notification is as follows:

1. You create an Amazon RDS event notification subscription by using the Amazon RDS console, AWS CLI, or API\.

   Amazon RDS uses the ARN of an Amazon SNS topic to identify each subscription\. The Amazon RDS console creates the ARN for you when you create the subscription\. Create the ARN by using the Amazon SNS console, the AWS CLI, or the Amazon SNS API\.

1. Amazon RDS sends an approval email or SMS message to the addresses you submitted with your subscription\. To confirm your subscription, choose the link in the notification you were sent\.

1. When you have confirmed the subscription, the status of your subscription is updated in the Amazon RDS console's **My Event Subscriptions** section\.

1. You then begin to receive event notifications\.

To learn about identity and access management when using Amazon SNS, see [Identity and access management in Amazon SNS](https://docs.aws.amazon.com/dg/sns-authentication-and-access-control.html) in the *Amazon Simple Notification Service Developer Guide*\.

You can use AWS Lambda to process event notifications from a DB instance\. For more information, see [Using AWS Lambda with Amazon RDS](https://docs.aws.amazon.com/lambda/latest/dg/services-rds.html) in the *AWS Lambda Developer Guide*\.

## Delivery of RDS event notifications<a name="USER_Events.overview.subscriptions"></a>

Amazon RDS sends notifications to the addresses that you provide when you create the subscription\. Event notifications might take up to five minutes to be delivered\. 

**Important**  
Amazon RDS doesn't guarantee the order of events sent in an event stream\. The event order is subject to change\.

When Amazon SNS sends a notification to a subscribed HTTP or HTTPS endpoint, the POST message sent to the endpoint has a message body that contains a JSON document\. For more information, see [Amazon SNS message and JSON formats](https://docs.aws.amazon.com/sns/latest/dg/sns-message-and-json-formats.html) in the *Amazon Simple Notification Service Developer Guide*\.

You can configure SNS to notify you with text messages\. For more information, see [ Mobile text messaging \(SMS\)](https://docs.aws.amazon.com/sns/latest/dg/sns-mobile-phone-number-as-subscriber.html) in the *Amazon Simple Notification Service Developer Guide*\.

To turn off notifications without deleting a subscription, choose **No** for **Enabled** in the Amazon RDS console\. Or you can set the `Enabled` parameter to `false` using the AWS CLI or Amazon RDS API\.

## Billing for Amazon RDS event notifications<a name="USER_Events.overview.billing"></a>

Billing for Amazon RDS event notification is through Amazon SNS\. Amazon SNS fees apply when using event notification\. For more information about Amazon SNS billing, see [ Amazon Simple Notification Service pricing](http://aws.amazon.com/sns/#pricing)\.