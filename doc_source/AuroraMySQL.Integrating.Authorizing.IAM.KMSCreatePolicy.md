# Creating an IAM Policy to Access AWS KMS Resources<a name="AuroraMySQL.Integrating.Authorizing.IAM.KMSCreatePolicy"></a>

Aurora can access AWS Key Management Service keys used for encrypting their database backups\. However, you must first create an IAM policy that provides the permissions that allow Aurora to access KMS keys\.

The following policy adds the permissions required by Aurora to access KMS keys on your behalf\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "kms:Decrypt",
            ],
            "Resource": "arn:aws:kms:<region>:<123456789012>:key/<key-ID>"
        }
    ]
}
```

You can use the following steps to create an IAM policy that provides the minimum required permissions for Aurora to access KMS keys on your behalf\.

**To create an IAM policy to grant access to your KMS keys**

1. Open the [IAM console](https://console.aws.amazon.com/iam/home?#home)\.

1. In the navigation pane, choose **Policies**\.

1. Choose **Create policy**\.

1. On the **Visual editor** tab, choose **Choose a service**, and then choose **KMS**\.

1. In **Actions**, expand **Write**, and then choose **Decrypt**\.

1. Choose **Resources**, and choose **Add ARN**\.

1. In the **Add ARN\(s\)** dialog box, enter the following values:
   + **Region** – Type the AWS Region, such as `us-west-2`\.
   + **Account** – Type the user account number\.
   + **Log Stream Name** – Type the KMS key ID\.

1. In the **Add ARN\(s\)** dialog box, choose **Add**

1. Choose **Review policy**\.

1. Set **Name** to a name for your IAM policy, for example `AmazonRDSKMSKey`\. You use this name when you create an IAM role to associate with your Aurora DB cluster\. You can also add an optional **Description** value\.

1. Choose **Create policy**\.

1. Complete the steps in [Creating an IAM Role to Allow Amazon Aurora to Access AWS Services](AuroraMySQL.Integrating.Authorizing.IAM.CreateRole.md)\.