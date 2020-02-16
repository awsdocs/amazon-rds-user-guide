# Viewing Amazon RDS Events<a name="USER_ListEvents"></a>

 Amazon RDS keeps a record of events that relate to your DB instances, DB snapshots, DB security groups, and DB parameter groups\. This information includes the date and time of the event, the source name and source type of the event, and a message associated with the event\.

You can retrieve events for your RDS resources through the AWS Management Console, which shows events from the past 24 hours\. You can also retrieve events for your RDS resources by using the [describe\-events](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-events.html) AWS CLI command, or the [DescribeEvents](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeEvents.html) RDS API operation\. If you use the AWS CLI or the RDS API to view events, you can retrieve events for up to the past 14 days\. 

**Note**  
If you need to store events for longer periods of time, you can send Amazon RDS events to CloudWatch Events\. For more information, see [Getting CloudWatch Events and Amazon EventBridge Events for Amazon RDS](rds-cloud-watch-events.md)

## Console<a name="USER_ListEvents.CON"></a>

**To view all Amazon RDS instance events for the past 24 hours**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Events**\. The available events appear in a list\.

1. Use the **Filter** list to filter the events by type, and use the text box to the right of the **Filter** list to further filter your results\. For example, the following screenshot shows a list of events filtered by the DB instance event type and containing the characters **1318**\.  
![\[List DB events\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/ListEvents.png)

## AWS CLI<a name="USER_ListEvents.CLI"></a>

**To view all Amazon RDS instance events for the past 7 days**

You can view all Amazon RDS instance events for the past 7 days by calling the [describe\-events](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-events.html) AWS CLI command and setting the `--duration` parameter to `10080`\. 

```
1. aws rds describe-events --duration 10080
```

## API<a name="USER_ListEvents.API"></a>

**To view all Amazon RDS instance events for the past 14 days**

You can view all Amazon RDS instance events for the past 14 days by calling the [DescribeEvents](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeEvents.html) RDS API operation and setting the `Duration` parameter to `20160`\. 

```
 1. https://rds.us-west-2.amazonaws.com/
 2.    ?Action=DescribeEvents
 3.    &Duration=20160 
 4.    &MaxRecords=100
 5.    &SignatureMethod=HmacSHA256
 6.    &SignatureVersion=4
 7.    &Version=2014-10-31
 8.    &X-Amz-Algorithm=AWS4-HMAC-SHA256
 9.    &X-Amz-Credential=AKIADQKE4SARGYLE/20140421/us-west-2/rds/aws4_request
10.    &X-Amz-Date=20140421T194733Z
11.    &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
12.    &X-Amz-Signature=8e313cabcdbd9766c56a2886b5b298fd944e0b7cfa248953c82705fdd0374f27
```

## <a name="USER_ListEvents.related"></a>