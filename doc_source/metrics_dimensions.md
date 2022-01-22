# Viewing Amazon RDS metrics and dimensions<a name="metrics_dimensions"></a>

When you use Amazon RDS resources, Amazon RDS sends metrics and dimensions to Amazon CloudWatch every minute\. You can use the following procedures to view the metrics for Amazon RDS\.

## Console<a name="metrics_dimensions.console"></a>

**To view metrics using the Amazon CloudWatch console**

Metrics are grouped first by the service namespace, and then by the various dimension combinations within each namespace\.

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. If necessary, change the AWS Region\. From the navigation bar, choose the AWS Region where your AWS resources are\. For more information, see [Regions and endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html)\.

1. In the navigation pane, choose **Metrics**\. Choose the **RDS** metric namespace\.  
![\[Choose metric namespace\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/rds-monitoring-01.png)

1. Choose a metric dimension, for example **By Database Class**\.

1. To sort the metrics, use the column heading\. To graph a metric, select the check box next to the metric\. To filter by resource, choose the resource ID, and then choose **Add to search**\. To filter by metric, choose the metric name, and then choose **Add to search**\.  
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