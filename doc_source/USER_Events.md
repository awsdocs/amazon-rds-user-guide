# Using Amazon RDS event notification<a name="USER_Events"></a>

Amazon RDS uses the Amazon Simple Notification Service \(Amazon SNS\) to provide notification when an Amazon RDS event occurs\. These notifications can be in any notification form supported by Amazon SNS for an AWS Region, such as an email, a text message, or a call to an HTTP endpoint\. 

**Topics**
+ [Amazon RDS event categories and event messages](USER_Events.Messages.md)
+ [Subscribing to Amazon RDS event notification](USER_Events.Subscribing.md)
+ [Listing Amazon RDS event notification subscriptions](USER_Events.ListSubscription.md)
+ [Modifying an Amazon RDS event notification subscription](USER_Events.Modifying.md)
+ [Adding a source identifier to an Amazon RDS event notification subscription](USER_Events.AddingSource.md)
+ [Removing a source identifier from an Amazon RDS event notification subscription](USER_Events.RemovingSource.md)
+ [Listing the Amazon RDS event notification categories](USER_Events.ListingCategories.md)
+ [Deleting an Amazon RDS event notification subscription](USER_Events.Deleting.md)

 Amazon RDS uses the Amazon Simple Notification Service \(Amazon SNS\) to provide notification when an Amazon RDS event occurs\. These notifications can be in any notification form supported by Amazon SNS for an AWS Region, such as an email, a text message, or a call to an HTTP endpoint\. 

Amazon RDS groups these events into categories that you can subscribe to so that you can be notified when an event in that category occurs\. You can subscribe to an event category for a DB instance, DB snapshot,  DB parameter group, or DB security group\. For example, if you subscribe to the Backup category for a given DB instance, you are notified whenever a backup\-related event occurs that affects the DB instance\. If you subscribe to a configuration change category for a DB security group, you are notified when the DB security group is changed\. You also receive notification when an event notification subscription changes\.

Event notifications are sent to the addresses that you provide when you create the subscription\. You might want to create several different subscriptions, such as one subscription receiving all event notifications and another subscription that includes only critical events for your production DB instances\. You can easily turn off notification without deleting a subscription by choosing **No** for **Enabled** in the Amazon RDS console or by setting the `Enabled` parameter to `false` using the AWS CLI or Amazon RDS API\.

**Important**  
Amazon RDS doesn't guarantee the order of events sent in an event stream\. The event order is subject to change\.

**Note**  
For more information on using text messages with SNS, see [ Mobile text messaging \(SMS\)](https://docs.aws.amazon.com/sns/latest/dg/sns-mobile-phone-number-as-subscriber.html) in the *Amazon Simple Notification Service Developer Guide*\.

Amazon RDS uses the ARN of an Amazon SNS topic to identify each subscription\. The Amazon RDS console creates the ARN for you when you create the subscription\. If you use the CLI or API, you create the ARN by using the Amazon SNS console or the Amazon SNS API when you create a subscription\.

Billing for Amazon RDS event notification is through the Amazon Simple Notification Service \(Amazon SNS\)\. Amazon SNS fees apply when using event notification\. For more information on Amazon SNS billing, see [ Amazon Simple Notification Service pricing](http://aws.amazon.com/sns/#pricing)\.

The process for subscribing to Amazon RDS event notification is as follows:

1. Create an Amazon RDS event notification subscription by using the Amazon RDS console, AWS CLI, or API\.

1. Amazon RDS sends an approval email or SMS message to the addresses you submitted with your subscription\. To confirm your subscription, choose the link in the notification you were sent\.

1. When you have confirmed the subscription, the status of your subscription is updated in the Amazon RDS console's **My Event Subscriptions** section\.

1. You then begin to receive event notifications\.

**Note**  
When Amazon SNS sends a notification to a subscribed HTTP or HTTPS endpoint, the POST message sent to the endpoint has a message body that contains a JSON document\. For more information, see [Amazon SNS message and JSON formats](https://docs.aws.amazon.com/sns/latest/dg/sns-message-and-json-formats.html) in the *Amazon Simple Notification Service Developer Guide*\.  
You can use AWS Lambda to process event notifications from a DB instance\. For more information, see [Using AWS Lambda with Amazon RDS](https://docs.aws.amazon.com/lambda/latest/dg/services-rds.html) in the *AWS Lambda Developer Guide*\.

The following section lists all categories and events that you can be notified of\. It also provides information about subscribing to and working with Amazon RDS event subscriptions\.

**Topics**
+ [Amazon RDS event categories and event messages](USER_Events.Messages.md)
+ [Subscribing to Amazon RDS event notification](USER_Events.Subscribing.md)
+ [Listing Amazon RDS event notification subscriptions](USER_Events.ListSubscription.md)
+ [Modifying an Amazon RDS event notification subscription](USER_Events.Modifying.md)
+ [Adding a source identifier to an Amazon RDS event notification subscription](USER_Events.AddingSource.md)
+ [Removing a source identifier from an Amazon RDS event notification subscription](USER_Events.RemovingSource.md)
+ [Listing the Amazon RDS event notification categories](USER_Events.ListingCategories.md)
+ [Deleting an Amazon RDS event notification subscription](USER_Events.Deleting.md)