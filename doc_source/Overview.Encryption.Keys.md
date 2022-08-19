# AWS KMS key management<a name="Overview.Encryption.Keys"></a>

Amazon RDS automatically integrates with AWS Key Management Service \(AWS KMS\) for key management\. Amazon RDS uses envelope encryption\. For more information about envelope encryption, see [ Envelope encryption](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#enveloping) in the *AWS Key Management Service Developer Guide*\.

An *AWS KMS key* is a logical representation of a key\. The KMS key includes metadata, such as the key ID, creation date, description, and key state\. The KMS key also contains the key material used to encrypt and decrypt data\. For more information about KMS keys, see [AWS KMS keys](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#kms_keys) in the *AWS Key Management Service Developer Guide*\.

You can manage KMS keys used for Amazon RDS encrypted DB instances using the [AWS Key Management Service \(AWS KMS\)](https://docs.aws.amazon.com/kms/latest/developerguide/) in the [AWS KMS console](https://console.aws.amazon.com/kms), the AWS CLI, or the AWS KMS API\. If you want full control over a KMS key, then you must create a customer managed key\. For more information about customer managed keys, see [Customer managed keys](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#customer-cmk) in the *AWS Key Management Service Developer Guide*\.

*AWS managed keys* are KMS keys in your account that are created, managed, and used on your behalf by an AWS service that is integrated with AWS KMS\. You can't delete, edit, or rotate AWS managed keys\. For more information about AWS managed keys, see [AWS managed keys](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#aws-managed-cmk) in the *AWS Key Management Service Developer Guide*\.

You can't share a snapshot that has been encrypted using the AWS managed key of the AWS account that shared the snapshot\. 

You can view audit logs of every action taken with an AWS managed or customer managed key by using [AWS CloudTrail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/)\.

**Important**  
If you turn off or revoke permissions to a KMS key used by an RDS database, RDS puts your database into a terminal state when access to the KMS key is required\. This change could be immediate, or deferred, depending on the use case that required access to the KMS key\. In this state, the DB instance is no longer available, and the current state of the database can't be recovered\. To restore the DB instance, you must re\-enable access to the KMS key for RDS, and then restore the DB instance from the latest available backup\.

## Authorizing use of a customer managed key<a name="Overview.Encryption.Keys.Authorizing"></a>

When RDS uses a customer managed key in cryptographic operations, it acts on behalf of the user who is creating or changing the RDS resource\.

To create an RDS resource using a customer managed key, a user must have permissions to call the following operations on the customer managed key:
+ kms:CreateGrant
+ kms:DescribeKey

You can specify these required permissions in a key policy, or in an IAM policy if the key policy allows it\.

You can make the IAM policy stricter in various ways\. For example, to allow the customer managed key to be used only for requests that originate in RDS, you can use the [ kms:ViaService condition key](https://docs.aws.amazon.com/kms/latest/developerguide/policy-conditions.html#conditions-kms-via-service) with the `rds.<region>.amazonaws.com` value\.

You can also use the keys or values in the [encryption context](https://docs.aws.amazon.com/kms/latest/developerguide/services-rds.html#rds-encryptioncontext) as a condition for using the customer managed key for cryptographic operations\.

For more information, see [Allowing users in other accounts to use a KMS key](https://docs.aws.amazon.com/kms/latest/developerguide/key-policy-modifying-external-accounts.html) in the *AWS Key Management Service Developer Guide*\.