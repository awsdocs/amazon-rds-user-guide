# Viewing Amazon RDS events<a name="USER_ListEvents"></a>

You can retrieve events for your RDS resources through the AWS Management Console, which shows events from the past 24 hours\. You can also retrieve events for your RDS resources by using the [describe\-events](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-events.html) AWS CLI command, or the [DescribeEvents](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeEvents.html) RDS API operation\. If you use the AWS CLI or the RDS API to view events, you can retrieve events for up to the past 14 days\. 

**Note**  
If you need to store events for longer periods of time, you can send Amazon RDS events to CloudWatch Events\. For more information, see [Creating a rule that triggers on an Amazon RDS event](rds-cloud-watch-events.md)

## Console<a name="USER_ListEvents.CON"></a>

**To view all Amazon RDS instance events for the past 24 hours**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Events**\. The available events appear in a list\.

1. Use the **Filter** list to filter the events by type, and use the text box to the right of the **Filter** list to further filter your results\. For example, the following screenshot shows a list of events filtered by the characters **stopped**\.  
![\[List DB events\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/ListEvents.png)

## AWS CLI<a name="USER_ListEvents.CLI"></a>

You can view all Amazon RDS instance events for the past 7 days by calling the [describe\-events](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-events.html) AWS CLI command and setting the `--duration` parameter to `10080`\. 

```
1. aws rds describe-events --duration 10080
```

## API<a name="USER_ListEvents.API"></a>

You can view all Amazon RDS instance events for the past 14 days by calling the [DescribeEvents](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeEvents.html) RDS API operation and setting the `Duration` parameter to `20160`\.