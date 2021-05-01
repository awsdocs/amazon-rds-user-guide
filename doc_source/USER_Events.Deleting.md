# Deleting an Amazon RDS event notification subscription<a name="USER_Events.Deleting"></a>

You can delete a subscription when you no longer need it\. All subscribers to the topic will no longer receive event notifications specified by the subscription\.

## Console<a name="USER_Events.Deleting.Console"></a>

**To delete an Amazon RDS event notification subscription**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1.  In the navigation pane, choose **DB Event Subscriptions**\. 

1.  In the **My DB Event Subscriptions** pane, choose the subscription that you want to delete\. 

1. Choose **Delete**\.

1. The Amazon RDS console indicates that the subscription is being deleted\.  
![\[Delete an event notification subscription\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/EventNotification-Delete.png)

   

## AWS CLI<a name="USER_Events.Deleting.CLI"></a>

To delete an Amazon RDS event notification subscription, use the AWS CLI [https://docs.aws.amazon.com/cli/latest/reference/rds/delete-event-subscription.html](https://docs.aws.amazon.com/cli/latest/reference/rds/delete-event-subscription.html) command\. Include the following required parameter:
+ `--subscription-name`

**Example**  
The following example deletes the subscription `myrdssubscription`\.  

```
aws rds delete-event-subscription --subscription-name myrdssubscription
```

## API<a name="USER_Events.Deleting.API"></a>

To delete an Amazon RDS event notification subscription, use the RDS API [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DeleteEventSubscription.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DeleteEventSubscription.html) command\. Include the following required parameter:
+ `SubscriptionName`