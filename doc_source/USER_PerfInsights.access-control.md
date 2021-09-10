# Configuring access policies for Performance Insights<a name="USER_PerfInsights.access-control"></a>

To access Performance Insights, you must have the appropriate permissions from AWS Identity and Access Management \(IAM\)\. You have the following options for granting access:
+ Attach the `AmazonRDSFullAccess` managed policy to an IAM user or role\.
+ Create a custom IAM policy and attach it to an IAM user or role\.

Also, if you specified a customer managed CMK when you turned on Performance Insights, make sure that users in your account have the `kms:Decrypt` and `kms:GenerateDataKey` permissions on the CMK\.



## Attaching the AmazonRDSFullAccess policy to an IAM principal<a name="USER_PerfInsights.access-control.managed-policy"></a>

`AmazonRDSFullAccess` is an AWS\-managed policy that grants access to all of the Amazon RDS API operations\. This policy does the following:
+ Grants access to related services used by the Amazon RDS console\. For example, this policy grants access to event notifications using Amazon SNS\.
+ Grants permissions needed for using Performance Insights\. 

If you attach `AmazonRDSFullAccess` to an IAM user or role, the recipient can use Performance Insights with other console features\.

## Creating a custom IAM policy for Performance Insights<a name="USER_PerfInsights.access-control.custom-policy"></a>

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
To use Performance Insights, make sure that you have access to Amazon RDS in addition to the custom policy\. For example, the `AmazonRDSReadOnlyAccess` predefined policy provides read\-only access to Amazon RDS\. For more information, see [Managing access using policies](UsingWithRDS.IAM.md#security_iam_access-manage)\.

1. On the **Summary** page, choose **Add permissions**\.

1. Choose **Attach existing policies directly**\. For **Search**, type the first few characters of your policy name, as shown following\.  
![\[Choose a Policy\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_insights_attach_iam_policy.png)

1. Choose your policy, and then choose **Next: Review**\.

1. Choose **Add permissions**\.

## Configuring a KMS policy for Performance Insights<a name="USER_PerfInsights.access-control.cmk-policy"></a>

Performance Insights uses an AWS KMS customer master key \(CMK\) to encrypt sensitive data\. When you enable Performance Insights through the API or the console, you have the following options:
+ Choose the default AWS managed CMK\.

  Amazon RDS uses the AWS managed CMK for your new DB instance\. Amazon RDS creates an AWS managed CMK for your AWS account\. Your AWS account has a different AWS managed CMK for Amazon RDS for each AWS Region\.
+ Choose a customer managed CMK\.

  If you specify a customer\-managed CMK, users in your account that call the Performance Insights API need the `kms:Decrypt` and `kms:GenerateDataKey` permissions on the CMK\. You can configure these permissions through IAM policies\. However, we recommend that you manage these permissions through your KMS key policy\. For more information, see [ Using key policies in AWS KMS](https://docs.aws.amazon.com/kms/latest/developerguide/key-policies.html)\. 

**Example**  
The following sample key policy shows how to add statements to your CMK policy\. These statements allow access to Performance Insights\. Depending on how you use the KMS key, you might want to change some restrictions\. Before adding statements to your policy, remove all comments\.  

```
{
 "Version" : "2012-10-17",
 "Id" : "your-policy",
 "Statement" : [ {
    //This represents a statement that currently exists in your policy.
 }
 ....,
 //Starting here, add new statement to your policy for Performance Insights.
 //We recommend that you add one new statement for every RDS instance
{
    "Sid" : "Allow viewing RDS Performance Insights",
    "Effect": "Allow",
    "Principal": {
        "AWS": [
            //One or more principals allowed to access Performance Insights
            "arn:aws:iam::444455556666:role/Role1"
        ]
    },
    "Action": [
        "kms:Decrypt",
        "kms:GenerateDataKey"
    ],
    "Resource": "*",
    "Condition" : {
        "StringEquals" : {
            //Restrict access to only RDS APIs (including Performance Insights).
            //Replace region with your AWS Region. 
            //For example, specify us-west-2.
            "kms:ViaService" : "rds.region.amazonaws.com"
        },
        "ForAnyValue:StringEquals": {
            //Restrict access to only data encrypted by Performance Insights.
            "kms:EncryptionContext:aws:pi:service": "rds",
            "kms:EncryptionContext:service": "pi",
            
            //Restrict access to a specific RDS instance.
            //The value is a DbiResourceId.
           "kms:EncryptionContext:aws:rds:db-id": "db-AAAAABBBBBCCCCDDDDDEEEEE"
        }
    }
}
```
