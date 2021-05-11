# Modifying an Amazon RDS event notification subscription<a name="USER_Events.Modifying"></a>

After you have created a subscription, you can change the subscription name, source identifier, categories, or topic ARN\.

## Console<a name="USER_Events.Modifying.Console"></a>

**To modify an Amazon RDS event notification subscription**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1.  In the navigation pane, choose **Event subscriptions**\. 

1.  In the **Event subscriptions** pane, choose the subscription that you want to modify and choose **Edit**\. 

1.  Make your changes to the subscription in either the **Target** or **Source** section\.

1. Choose **Edit**\. The Amazon RDS console indicates that the subscription is being modified\.  
![\[List DB event notification subscriptions\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/EventNotification-Modify2.png)

   

## AWS CLI<a name="USER_Events.Modifying.CLI"></a>

To modify an Amazon RDS event notification subscription, use the AWS CLI [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-event-subscription.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-event-subscription.html) command\. Include the following required parameter:
+ `--subscription-name`

**Example**  
The following code enables `myeventsubscription`\.  
For Linux, macOS, or Unix:  

```
aws rds modify-event-subscription \
    --subscription-name myeventsubscription \
    --enabled
```
For Windows:  

```
aws rds modify-event-subscription ^
    --subscription-name myeventsubscription ^
    --enabled
```

## API<a name="USER_Events.Modifying.API"></a>

To modify an Amazon RDS event, call the Amazon RDS API operation [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyEventSubscription.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyEventSubscription.html)\. Include the following required parameter:
+ `SubscriptionName`