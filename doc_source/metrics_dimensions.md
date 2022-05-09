# Viewing DB instance metrics in the CloudWatch console and AWS CLI<a name="metrics_dimensions"></a>

Following, you can find details about how to view metrics for your DB instance using CloudWatch\. For information on monitoring metrics for your DB instance's operating system in real time using CloudWatch Logs, see [Monitoring OS metrics with Enhanced Monitoring](USER_Monitoring.OS.md)\.

When you use Amazon RDS resources, Amazon RDS sends metrics and dimensions to Amazon CloudWatch every minute\. You can use the following procedures to view the metrics for Amazon RDS in the CloudWatch console and CLI\.

## Console<a name="metrics_dimensions.console"></a>

**To view metrics using the Amazon CloudWatch console**

Metrics are grouped first by the service namespace, and then by the various dimension combinations within each namespace\.

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

   The CloudWatch overview home page appears\.  
![\[CloudWatch overview page\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/monitoring-overviewpage-console2.png)

1. If necessary, change the AWS Region\. From the navigation bar, choose the AWS Region where your AWS resources are\. For more information, see [Regions and endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html)\.

1. In the navigation pane, choose **Metrics** and then **All metrics**\.  
![\[Choose metric namespace\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/cw-all-metrics.png)

1. Scroll down and choose the **RDS** metric namespace\.

   The page displays the Amazon RDS dimensions\. For descriptions of these dimensions, see [Amazon CloudWatch dimensions for Amazon RDS](dimensions.md)\.  
![\[Choose metric namespace\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/rds-monitoring-01.png)

1. Choose a metric dimension, for example **By Database Class**\.  
![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/metrics-by-instance-class.png)

1. Do any of the following actions:
   + To sort the metrics, use the column heading\.
   + To graph a metric, select the check box next to the metric\.
   + To filter by resource, choose the resource ID, and then choose **Add to search**\.
   + To filter by metric, choose the metric name, and then choose **Add to search**\.

   The following example filters on the **db\.t3\.medium** class and graphs the **CPUUtilization** metric\.  
![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/rds-monitoring-03.png)

## AWS CLI<a name="metrics_dimensions.CLI"></a>

To obtain metric information by using the AWS CLI, use the CloudWatch command [https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/list-metrics.html](https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/list-metrics.html)\. In the following example, you list all metrics in the `AWS/RDS` namespace\.

```
aws cloudwatch list-metrics --namespace AWS/RDS
```

To obtain metric statistics, use the command [https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/get-metric-statistics.html](https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/get-metric-statistics.html)\. The following command gets `CPUUtilization` statistics for instance `my-instance` over the specific 24\-hour period, with a 5\-minute granularity\.

**Example**  
For Linux, macOS, or Unix:  

```
1. aws cloudwatch get-metric-statistics --namespace AWS/RDS \
2.      --metric-name CPUUtilization \
3.      --start-time 2021-12-15T00:00:00Z \
4.      --end-time 2021-12-16T00:00:00Z \
5.      --period 360 \
6.      --statistics Minimum \
7.      --dimensions Name=DBInstanceIdentifier,Value=my-instance
```
For Windows:  

```
1. aws cloudwatch get-metric-statistics --namespace AWS/RDS ^
2.      --metric-name CPUUtilization ^
3.      --start-time 2021-12-15T00:00:00Z ^
4.      --end-time 2021-12-16T00:00:00Z ^
5.      --period 360 ^
6.      --statistics Minimum ^
7.      --dimensions Name=DBInstanceIdentifier,Value=my-instance
```
Sample output appears as follows:  

```
{
    "Datapoints": [
        {
            "Timestamp": "2021-12-15T18:00:00Z", 
            "Minimum": 8.7, 
            "Unit": "Percent"
        }, 
        {
            "Timestamp": "2021-12-15T23:54:00Z", 
            "Minimum": 8.12486458559024, 
            "Unit": "Percent"
        }, 
        {
            "Timestamp": "2021-12-15T17:24:00Z", 
            "Minimum": 8.841666666666667, 
            "Unit": "Percent"
        }, ...
        {
            "Timestamp": "2021-12-15T22:48:00Z", 
            "Minimum": 8.366248354248954, 
            "Unit": "Percent"
        }
    ], 
    "Label": "CPUUtilization"
}
```
For more information, see [Getting statistics for a metric](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/getting-metric-statistics.html) in the *Amazon CloudWatch User Guide*\.