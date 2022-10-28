# Creating CloudWatch alarms to monitor Amazon RDS<a name="creating_alarms"></a>

You can create a CloudWatch alarm that sends an Amazon SNS message when the alarm changes state\. An alarm watches a single metric over a time period that you specify\. The alarm can also perform one or more actions based on the value of the metric relative to a given threshold over a number of time periods\. The action is a notification sent to an Amazon SNS topic or Amazon EC2 Auto Scaling policy\.

Alarms invoke actions for sustained state changes only\. CloudWatch alarms don't invoke actions simply because they are in a particular state\. The state must have changed and have been maintained for a specified number of time periods\. The following procedures show how to create alarms for Amazon RDS\.

**To set alarms using the CloudWatch console**

1. Sign in to the AWS Management Console and open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. Choose **Alarms**, **All alarms**\.

   Choose **Create an alarm**\. 

   This action launches a wizard\. 

1. Choose **Select metric**\.

1. In **Browse**, choose **RDS**\.

1. Search for the metric that you want to place an alarm on\. For example, search for **CPUUtilization**\. To display only Amazon RDS metrics, search for the identifier of your resource\. 

1. Choose the metric to create an alarm on\. Then choose **Select metric**\.

1. In **Conditions**, define the alarm condition\. Then choose **Next**\. 

   For example, you can specify that the alarm should be set when CPU utilization is over 75%\.

1. Choose your notification method\. Then choose **Next**\.

   For example, to configure CloudWatch to send you an email when the alarm state is reached, do the following:

   1. Choose **Create new topic** \(if one doesn't exist\)\.

   1. Enter a topic name\.

   1. Enter the email endpoints\.

      The email addresses must be verified before they receive notifications\. Emails are only sent when the alarm enters an alarm state\. If this alarm state change happens before the email addresses are verified, the addresses don't receive a notification\.

   1. Choose **Create topic**\.

   1. Choose **Next**\.

1. Enter a name and description for the alarm\. The name must contain only ASCII characters\. Then choose **Next**\.

1. Preview the alarm that you're about to create\. Then choose **Create alarm**\.

**To set an alarm using the AWS CLI**
+ Call [https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/put-metric-alarm.html](https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/put-metric-alarm.html)\. For more information, see *[AWS CLI Command Reference](https://docs.aws.amazon.com/cli/latest/reference/)*\.

**To set an alarm using the CloudWatch API**
+ Call [https://docs.aws.amazon.com/AmazonCloudWatch/latest/APIReference/API_PutMetricAlarm.html](https://docs.aws.amazon.com/AmazonCloudWatch/latest/APIReference/API_PutMetricAlarm.html)\. For more information, see *[Amazon CloudWatch API Reference](https://docs.aws.amazon.com/AmazonCloudWatch/latest/APIReference/)* 

For more information about setting up Amazon SNS topics and creating alarms, see [Using Amazon CloudWatch alarms](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/AlarmThatSendsEmail.html)\.