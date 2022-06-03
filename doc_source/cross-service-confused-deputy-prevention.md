# Preventing cross\-service confused deputy problems<a name="cross-service-confused-deputy-prevention"></a>

The *confused deputy problem* is a security issue where an entity that doesn't have permission to perform an action can coerce a more\-privileged entity to perform the action\. In AWS, cross\-service impersonation can result in the confused deputy problem\. 

Cross\-service impersonation can occur when one service \(the *calling service*\) calls another service \(the *called service*\)\. The calling service can be manipulated to use its permissions to act on another customer's resources in a way that it shouldn't have permission to access\. To prevent this, AWS provides tools that can help you protect your data for all services with service principals that have been given access to resources in your account\. For more information, see [The confused deputy problem](https://docs.aws.amazon.com/IAM/latest/UserGuide/confused-deputy.html) in the *IAM User Guide*\.

To limit the permissions that Amazon RDS gives another service for a specific resource, we recommend using the [https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-sourcearn](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-sourcearn) and [https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-sourceaccount](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-sourceaccount) global condition context keys in resource policies\. 

In some cases, the `aws:SourceArn` value doesn't contain the account ID, for example when you use the Amazon Resource Name \(ARN\) for an Amazon S3 bucket\. In these cases, make sure to use both global condition context keys to limit permissions\. In some cases, you use both global condition context keys and the `aws:SourceArn` value contains the account ID\. In these cases, make sure that the `aws:SourceAccount` value and the account in the `aws:SourceArn` use the same account ID when they're used in the same policy statement\. If you want only one resource to be associated with the cross\-service access, use `aws:SourceArn`\. If you want to allow any resource in the specified AWS account to be associated with the cross\-service use, use `aws:SourceAccount`\.

Make sure that the value of `aws:SourceArn` is an ARN for an Amazon RDS resource type\. For more information, see [Working with Amazon Resource Names \(ARNs\) in Amazon RDS](USER_Tagging.ARN.md)\.

The most effective way to protect against the confused deputy problem is to use the `aws:SourceArn` global condition context key with the full ARN of the resource\. In some cases, you might not know the full ARN of the resource or you might be specifying multiple resources\. In these cases, use the `aws:SourceArn` global context condition key with wildcards \(`*`\) for the unknown portions of the ARN\. An example is `arn:aws:rds:*:123456789012:*`\. 

The following example shows how you can use the `aws:SourceArn` and `aws:SourceAccount` global condition context keys in Amazon RDS to prevent the confused deputy problem\.

```
{
  "Version": "2012-10-17",
  "Statement": {
    "Sid": "ConfusedDeputyPreventionExamplePolicy",
    "Effect": "Allow",
    "Principal": {
      "Service": "rds.amazonaws.com"
    },
    "Action": "sts:AssumeRole",
    "Condition": {
      "ArnLike": {
        "aws:SourceArn": "arn:aws:rds:us-east-1:123456789012:db:mydbinstance"
      },
      "StringEquals": {
        "aws:SourceAccount": "123456789012"
      }
    }
  }
}
```

For more examples of policies that use the `aws:SourceArn` and `aws:SourceAccount` global condition context keys, see the following sections:
+ [Granting permissions to publish notifications to an Amazon SNS topic](USER_Events.GrantingPermissions.md)
+ [Manually creating an IAM role for native backup and restore](SQLServer.Procedural.Importing.md#SQLServer.Procedural.Importing.Native.Enabling.IAM)
+ [Setting up Windows Authentication for SQL Server DB instances](USER_SQLServerWinAuth.md#USER_SQLServerWinAuth.SettingUp)
+ [Prerequisites for integrating RDS for SQL Server with S3](User.SQLServer.Options.S3-integration.md#Appendix.SQLServer.Options.S3-integration.preparing)
+ [Manually creating an IAM role for SQL Server Audit](Appendix.SQLServer.Options.Audit.md#Appendix.SQLServer.Options.Audit.IAM)
+ [Configuring IAM permissions for RDS for Oracle integration with Amazon S3](oracle-s3-integration.md#oracle-s3-integration.preparing)
+ [Setting up access to an Amazon S3 bucket](USER_PostgreSQL.S3Import.md#USER_PostgreSQL.S3Import.AccessPermission) \(PostgreSQL import\)
+ [Setting up access to an Amazon S3 bucket](postgresql-s3-export.md#postgresql-s3-export-access-bucket) \(PostgreSQL export\)