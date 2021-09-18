# Subscribing to Amazon RDS event notification<a name="USER_Events.Subscribing"></a>

The simplest way to create a subscription is with the RDS console\. If you choose to create event notification subscriptions using the CLI or API, you must create an Amazon Simple Notification Service topic and subscribe to that topic with the Amazon SNS console or Amazon SNS API\. You will also need to retain the Amazon Resource Name \(ARN\) of the topic because it is used when submitting CLI commands or API operations\. For information on creating an SNS topic and subscribing to it, see [Getting started with Amazon SNS](https://docs.aws.amazon.com/sns/latest/dg/GettingStarted.html) in the *Amazon Simple Notification Service Developer Guide*\.

You can specify the type of source you want to be notified of and the Amazon RDS source that triggers the event\. These are defined by the **SourceType** \(type of source\) and the **SourceIdentifier** \(the Amazon RDS source generating the event\)\. For example, **SourceType** might be `SourceType = db-instance`, whereas**SourceIdentifier** might be `SourceIdentifier = myDBInstance1`\. The following table shows possible combinations\.


|  SourceType  |  SourceIdentifier  |  Description  | 
| --- | --- | --- | 
|  Specified  |  Specified  |  You receive notice of all DB instance events for the specified source\.  | 
|  Specified  |  Not specified  |  You receive notice of the events for that source type for all your Amazon RDS sources\.  | 
|  Not specified  |  Not specified  |  You receive notice of all events from all Amazon RDS sources belonging to your customer account\.  | 

## Console<a name="USER_Events.Subscribing.Console"></a>

**To subscribe to RDS event notification**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In navigation pane, choose **Event subscriptions**\. 

1. In the **Event subscriptions** pane, choose **Create event subscription**\. 

1. In the **Create event subscription** dialog box, do the following:

   1. For **Name**, enter a name for the event notification subscription\.

   1. For **Send notifications to**, choose an existing Amazon SNS ARN for an Amazon SNS topic, or choose **create topic** to enter the name of a topic and a list of recipients\.

   1. For **Source type**, choose a source type\.

   1. Choose **Yes** to enable the subscription\. If you want to create the subscription but to not have notifications sent yet, choose **No**\.

   1. Depending on the source type you selected, choose the event categories and sources that you want to receive event notifications for\.

   1. Choose **Create**\.

The Amazon RDS console indicates that the subscription is being created\.

![\[List DB event notification subscriptions\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/EventNotification-Create2.png)



## AWS CLI<a name="USER_Events.Subscribing.CLI"></a>

To subscribe to RDS event notification, use the AWS CLI [https://docs.aws.amazon.com/cli/latest/reference/rds/create-event-subscription.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-event-subscription.html) command\. Include the following required parameters:
+ `--subscription-name`
+ `--sns-topic-arn`

**Example**  
For Linux, macOS, or Unix:  

```
aws rds create-event-subscription \
    --subscription-name myeventsubscription \
    --sns-topic-arn arn:aws:sns:us-east-1:802#########:myawsuser-RDS \
    --enabled
```
For Windows:  

```
aws rds create-event-subscription ^
    --subscription-name myeventsubscription ^
    --sns-topic-arn arn:aws:sns:us-east-1:802#########:myawsuser-RDS ^
    --enabled
```

## API<a name="USER_Events.Subscribing.API"></a>

To subscribe to Amazon RDS event notification, call the Amazon RDS API function [ `CreateEventSubscription`](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateEventSubscription.html)\. Include the following required parameters: 
+ `SubscriptionName`
+ `SnsTopicArn`