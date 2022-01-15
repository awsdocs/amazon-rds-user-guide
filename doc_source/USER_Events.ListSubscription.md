# Listing Amazon RDS event notification subscriptions<a name="USER_Events.ListSubscription"></a>

You can list your current Amazon RDS event notification subscriptions\.

## Console<a name="USER_Events.ListSubscription.Console"></a>

**To list your current Amazon RDS event notification subscriptions**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1.  In the navigation pane, choose **Event subscriptions**\. The **Event subscriptions** pane shows all your event notification subscriptions\.  
![\[List DB event notification subscriptions\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/EventNotification-ListSubs.png)

   

## AWS CLI<a name="USER_Events.ListSubscription.CLI"></a>

To list your current Amazon RDS event notification subscriptions, use the AWS CLI [https://docs.aws.amazon.com/cli/latest/reference/rds/describe-event-subscriptions.html](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-event-subscriptions.html) command\. 

**Example**  
The following example describes all event subscriptions\.  

```
aws rds describe-event-subscriptions
```
The following example describes the `myfirsteventsubscription`\.  

```
aws rds describe-event-subscriptions --subscription-name myfirsteventsubscription
```

## API<a name="USER_Events.ListSubscription.API"></a>

To list your current Amazon RDS event notification subscriptions, call the Amazon RDS API [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeEventSubscriptions.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeEventSubscriptions.html) action\.