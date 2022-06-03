# Granting permissions to publish notifications to an Amazon SNS topic<a name="USER_Events.GrantingPermissions"></a>

To grant Amazon RDS permissions to publish notifications to an Amazon Simple Notification Service \(Amazon SNS\) topic, attach an AWS Identity and Access Management \(IAM\) policy to the destination topic\. For more information about permissions, see [ Example cases for Amazon Simple Notification Service access control](https://docs.aws.amazon.com/sns/latest/dg/sns-access-policy-use-cases.html) in the *Amazon Simple Notification Service Developer Guide*\.

By default, an Amazon SNS topic has a policy allowing all Amazon RDS resources within the same account to publish notifications to it\. You can attach a custom policy to allow cross\-account notifications, or to restrict access to certain resources\.

The following is an example of an IAM policy that you attach to the destination Amazon SNS topic\. It restricts the topic to DB instances with names that match the specified prefix\. To use this policy, specify the following values:
+ `Resource` – The Amazon Resource Name \(ARN\) for your Amazon SNS topic
+ `SourceARN` – Your RDS resource ARN
+ `SourceAccount` – Your AWS account ID

To see a list of resource types and their ARNs, see [Resources Defined by Amazon RDS](https://docs.aws.amazon.com/service-authorization/latest/reference/list_amazonrds.html#amazonrds-resources-for-iam-policies) in the *Service Authorization Reference*\.

```
{
  "Version": "2008-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "events.rds.amazonaws.com"
      },
      "Action": [
        "sns:Publish"
      ],
      "Resource": "arn:aws:sns:us-east-1:123456789012:topic_name",
      "Condition": {
        "ArnLike": {
          "aws:SourceArn": "arn:aws:rds:us-east-1:123456789012:db:prefix-*"
        },
        "StringEquals": {
          "aws:SourceAccount": "123456789012"
        }
      }
    }
  ]
}
```