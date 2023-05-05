# AWS KMS key management<a name="Overview.Encryption.Keys"></a>

Amazon RDS automatically integrates with [AWS Key Management Service \(AWS KMS\)](https://docs.aws.amazon.com/kms/latest/developerguide/) for key management\. Amazon RDS uses envelope encryption\. For more information about envelope encryption, see [ Envelope encryption](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#enveloping) in the *AWS Key Management Service Developer Guide*\.

You can use two types of AWS KMS keys to encrypt your DB instances\.
+ If you want full control over a KMS key, you must create a *customer managed key*\. For more information about customer managed keys, see [Customer managed keys](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#customer-cmk) in the *AWS Key Management Service Developer Guide*\.

  You can't share a snapshot that has been encrypted using the AWS managed key of the AWS account that shared the snapshot\.
+ *AWS managed keys* are KMS keys in your account that are created, managed, and used on your behalf by an AWS service that is integrated with AWS KMS\. By default, the RDS AWS managed key \(`aws/rds`\) is used for encryption\. You can't manage, rotate, or delete the RDS AWS managed key\. For more information about AWS managed keys, see [AWS managed keys](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#aws-managed-cmk) in the *AWS Key Management Service Developer Guide*\.

To manage KMS keys used for Amazon RDS encrypted DB instances, use the [AWS Key Management Service \(AWS KMS\)](https://docs.aws.amazon.com/kms/latest/developerguide/) in the [AWS KMS console](https://console.aws.amazon.com/kms), the AWS CLI, or the AWS KMS API\. To view audit logs of every action taken with an AWS managed or customer managed key, use [AWS CloudTrail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/)\. For more information about key rotation, see [Rotating AWS KMS keys](https://docs.aws.amazon.com/kms/latest/developerguide/rotate-keys.html)\.

**Important**  
If you turn off or revoke permissions to a KMS key used by an RDS database, RDS puts your database into a terminal state when access to the KMS key is required\. This change could be immediate, or deferred, depending on the use case that required access to the KMS key\. In this state, the DB instance is no longer available, and the current state of the database can't be recovered\. To restore the DB instance, you must re\-enable access to the KMS key for RDS, and then restore the DB instance from the latest available backup\.

## Authorizing use of a customer managed key<a name="Overview.Encryption.Keys.Authorizing"></a>

When RDS uses a customer managed key in cryptographic operations, it acts on behalf of the user who is creating or changing the RDS resource\.

To create an RDS resource using a customer managed key, a user must have permissions to call the following operations on the customer managed key:
+ kms:CreateGrant
+ kms:DescribeKey

You can specify these required permissions in a key policy, or in an IAM policy if the key policy allows it\.

You can make the IAM policy stricter in various ways\. For example, if you want to allow the customer managed key to be used only for requests that originate in RDS, you can use the [ kms:ViaService condition key](https://docs.aws.amazon.com/kms/latest/developerguide/policy-conditions.html#conditions-kms-via-service) with the `rds.<region>.amazonaws.com` value\. Also, you can use the keys or values in the [Amazon RDS encryption context](#Overview.Encryption.Keys.encryptioncontext) as a condition for using the customer managed key for encryption\.

For more information, see [Allowing users in other accounts to use a KMS key](https://docs.aws.amazon.com/kms/latest/developerguide/key-policy-modifying-external-accounts.html) in the *AWS Key Management Service Developer Guide* and [Key policies in AWS KMS](https://docs.aws.amazon.com/kms/latest/developerguide/key-policies)\.

## Amazon RDS encryption context<a name="Overview.Encryption.Keys.encryptioncontext"></a>

When RDS uses your KMS key, or when Amazon EBS uses the KMS key on behalf of RDS, the service specifies an [encryption context](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#encrypt_context)\. The encryption context is [additional authenticated data](https://docs.aws.amazon.com/crypto/latest/userguide/cryptography-concepts.html#term-aad) \(AAD\) that AWS KMS uses to ensure data integrity\. When an encryption context is specified for an encryption operation, the service must specify the same encryption context for the decryption operation\. Otherwise, decryption fails\. The encryption context is also written to your [AWS CloudTrail](https://aws.amazon.com/cloudtrail/) logs to help you understand why a given KMS key was used\. Your CloudTrail logs might contain many entries describing the use of a KMS key, but the encryption context in each log entry can help you determine the reason for that particular use\.

At minimum, Amazon RDS always uses the DB instance ID for the encryption context, as in the following JSON\-formatted example:

```
{ "aws:rds:db-id": "db-CQYSMDPBRZ7BPMH7Y3RTDG5QY" }
```

This encryption context can help you identify the DB instance for which your KMS key was used\.

When your KMS key is used for a specific DB instance and a specific Amazon EBS volume, both the DB instance ID and the Amazon EBS volume ID are used for the encryption context, as in the following JSON\-formatted example:

```
{
  "aws:rds:db-id": "db-BRG7VYS3SVIFQW7234EJQOM5RQ",
  "aws:ebs:id": "vol-ad8c6542"
}
```