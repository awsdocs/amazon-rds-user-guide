# Creating an IAM Policy to Access CloudWatch Logs Resources<a name="AuroraMySQL.Integrating.Authorizing.IAM.CWCreatePolicy"></a>

Aurora can access CloudWatch Logs to export audit log data from an Aurora DB cluster\. However, you must first create an IAM policy that provides the log group and log stream permissions that allow Aurora to access CloudWatch Logs\. 

The following policy adds the permissions required by Aurora to access Amazon CloudWatch Logs on your behalf, and the minimum required permissions to create log groups and export data\. 

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "EnableCreationAndManagementOfRDSCloudwatchLogGroups",
            "Effect": "Allow",
            "Action": [
                "logs:CreateLogGroup",
                "logs:PutRetentionPolicy"
            ],
            "Resource": [
                "arn:aws:logs:*:*:log-group:/aws/rds/*"
            ]
        },
        {
            "Sid": "EnableCreationAndManagementOfRDSCloudwatchLogStreams",
            "Effect": "Allow",
            "Action": [
                "logs:CreateLogStream",
                "logs:PutLogEvents",
                "logs:DescribeLogStreams",
                "logs:GetLogEvents"
            ],
            "Resource": [
                "arn:aws:logs:*:*:log-group:/aws/rds/*:log-stream:*"
            ]
        }
    ]
}
```

You can use the following steps to create an IAM policy that provides the minimum required permissions for Aurora to access CloudWatch Logs on your behalf\. To allow Aurora full access to CloudWatch Logs, you can skip these steps and use the `CloudWatchLogsFullAccess` predefined IAM policy instead of creating your own\. For more information, see [Using Identity\-Based Policies \(IAM Policies\) for CloudWatch Logs](http://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring//iam-identity-based-access-control-cwl.html#managed-policies-cwl) in the* Amazon CloudWatch User Guide\.*

**To create an IAM policy to grant access to your CloudWatch Logs resources**

1. Open the [IAM console](https://console.aws.amazon.com/iam/home?#home)\.

1. In the navigation pane, choose **Policies**\.

1. Choose **Create policy**\.

1. On the **Visual editor** tab, choose **Choose a service**, and then choose **CloudWatch Logs**\.

1. Choose **Select actions** and then choose the Amazon CloudWatch Logs permissions needed for the IAM policy\.

   Ensure that the following permissions are selected:
   + `CreateLogGroup`
   + `CreateLogStream`
   + `DescribeLogStreams`
   + `GetLogEvents`
   + `PutLogEvents`
   + `PutRetentionPolicy`

1. Choose **Resources** and choose **Add ARN** for **log\-group**\.

1. In the **Add ARN\(s\)** dialog box, enter `log-group:/aws/rds/*` for **Log Group Name**, and choose **Add**\.

1. Choose **Add ARN** for **log\-stream**\.

1. In the **Add ARN\(s\)** dialog box, enter the following values:
   + **Log Group Name** – `log-group:/aws/rds/*`
   + **Log Stream** – `log-stream`
   + **Log Stream Name** – `*`

1. In the **Add ARN\(s\)** dialog box, choose **Add**

1. Choose **Review policy**\.

1. Set **Name** to a name for your IAM policy, for example `AmazonRDSCloudWatchLogs`\. You use this name when you create an IAM role to associate with your Aurora DB cluster\. You can also add an optional **Description** value\.

1. Choose **Create policy**\.

1. Complete the steps in [Creating an IAM Role to Allow Amazon Aurora to Access AWS Services](AuroraMySQL.Integrating.Authorizing.IAM.CreateRole.md)\.