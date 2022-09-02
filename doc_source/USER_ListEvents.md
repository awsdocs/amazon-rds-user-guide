# Viewing Amazon RDS events<a name="USER_ListEvents"></a>

You can retrieve the following event information for your Amazon RDS resources:
+ Resource name
+ Resource type
+ Time of the event
+ Message summary of the event

Access the events through the AWS Management Console, which shows events from the past 24 hours\. You can also retrieve events by using the [describe\-events](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-events.html) AWS CLI command, or the [DescribeEvents](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeEvents.html) RDS API operation\. If you use the AWS CLI or the RDS API to view events, you can retrieve events for up to the past 14 days\. 

**Note**  
If you need to store events for longer periods of time, you can send Amazon RDS events to CloudWatch Events\. For more information, see [Creating a rule that triggers on an Amazon RDS event](rds-cloud-watch-events.md)

For descriptions of the Amazon RDS events, see [Amazon RDS event categories and event messages](USER_Events.Messages.md)\.

To access detailed information about events using AWS CloudTrail, including request parameters, see [CloudTrail events](logging-using-cloudtrail.md#service-name-info-in-cloudtrail.events)\.

## Console<a name="USER_ListEvents.CON"></a>

**To view all Amazon RDS events for the past 24 hours**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Events**\. 

   The available events appear in a list\.

1. \(Optional\) Enter a search term to filter your results\. 

   The following example shows a list of events filtered by the characters **stopped**\.  
![\[List DB events\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/ListEvents.png)

## AWS CLI<a name="USER_ListEvents.CLI"></a>

To view all events generated in the last hour, call [describe\-events](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-events.html) with no parameters\.

```
aws rds describe-events
```

The following sample output shows that a DB instance has been stopped\.

```
{
    "Events": [
        {
            "EventCategories": [
                "notification"
            ], 
            "SourceType": "db-instance", 
            "SourceArn": "arn:aws:rds:us-east-1:123456789012:db:testinst", 
            "Date": "2022-04-22T21:31:00.681Z", 
            "Message": "DB instance stopped", 
            "SourceIdentifier": "testinst"
        }
    ]
}
```

To view all Amazon RDS events for the past 10080 minutes \(7 days\), call the [describe\-events](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-events.html) AWS CLI command and set the `--duration` parameter to `10080`\.

```
1. aws rds describe-events --duration 10080
```

The following example shows the events in the specified time range for DB instance *test\-instance*\.

```
aws rds describe-events \
    --source-identifier test-instance \
    --source-type db-instance \
    --start-time 2022-03-13T22:00Z \
    --end-time 2022-03-13T23:59Z
```

The following sample output shows the status of a backup\.

```
{
    "Events": [
        {
            "SourceType": "db-instance",
            "SourceIdentifier": "test-instance",
            "EventCategories": [
                "backup"
            ],
            "Message": "Backing up DB instance",
            "Date": "2022-03-13T23:09:23.983Z",
            "SourceArn": "arn:aws:rds:us-east-1:123456789012:db:test-instance"
        },
        {
            "SourceType": "db-instance",
            "SourceIdentifier": "test-instance",
            "EventCategories": [
                "backup"
            ],
            "Message": "Finished DB Instance backup",
            "Date": "2022-03-13T23:15:13.049Z",
            "SourceArn": "arn:aws:rds:us-east-1:123456789012:db:test-instance"
        }
    ]
}
```

## API<a name="USER_ListEvents.API"></a>

You can view all Amazon RDS instance events for the past 14 days by calling the [DescribeEvents](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeEvents.html) RDS API operation and setting the `Duration` parameter to `20160`\.