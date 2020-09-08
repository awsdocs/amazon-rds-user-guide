# Accessing Performance Insights<a name="USER_PerfInsights.access-control"></a>

To access Performance Insights, you must have the appropriate permissions from AWS Identity and Access Management \(IAM\)\. There are two options available for granting access:

1. Attach the `AmazonRDSFullAccess` managed policy to an IAM user or role\.

1. Create a custom IAM policy and attach it to an IAM user or role\.

## AmazonRDSFullAccess Managed Policy<a name="USER_PerfInsights.access-control.managed-policy"></a>

`AmazonRDSFullAccess` is an AWS\-managed policy that grants access to all of the Amazon RDS API operations\. The policy also grants access to related services that are used by the Amazon RDS console—for example, event notifications using Amazon SNS\.

In addition, `AmazonRDSFullAccess` contains all the permissions needed for using Performance Insights\. If you attach this policy to an IAM user or role, the recipient can use Performance Insights\. along with other console features\.

## Using a Custom IAM Policy<a name="USER_PerfInsights.access-control.custom-policy"></a>

For users who don't have full access with the `AmazonRDSFullAccess` policy, you can grant access to Performance Insights by creating or modifying a user\-managed IAM policy\. When you attach the policy to an IAM user or role, the recipient can use Performance Insights\.

**To create a custom policy**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Policies**\.

1. Choose **Create policy**\.

1. On the **Create Policy** page, choose the JSON tab\. 

1. Copy and paste the following\.

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Effect": "Allow",
               "Action": "pi:*",
               "Resource": "arn:aws:pi:*:*:metrics/rds/*"
           }
       ]
   }
   ```

1. Choose **Review policy**\.

1. Provide a name for the policy and optionally a description, and then choose **Create policy**\.

You can now attach the policy to an IAM user or role\. The following procedure assumes that you already have an IAM user available for this purpose\.

**To attach the policy to an IAM user**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Users**\.

1. Choose an existing user from the list\.
**Important**  
To use Performance Insights, make sure that you have access to Amazon RDS in addition to the custom policy\. For example, the `AmazonRDSReadOnlyAccess` predefined policy provides read\-only access to Amazon RDS\. For more information, see [Managing Access Using Policies](UsingWithRDS.IAM.md#security_iam_access-manage)\.

1. On the **Summary** page, choose **Add permissions**\.

1. Choose **Attach existing policies directly**\. For **Search**, type the first few characters of your policy name, as shown following\.  
![\[Choose a Policy\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_insights_attach_iam_policy.png)

1. Choose your policy, and then choose **Next: Review**\.

1. Choose **Add permissions**\.