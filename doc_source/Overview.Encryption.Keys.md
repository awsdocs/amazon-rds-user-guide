# Key Management<a name="Overview.Encryption.Keys"></a>

You can manage keys used for Amazon RDS encrypted DB instances using the [AWS Key Management Service \(AWS KMS\)](https://docs.aws.amazon.com/kms/latest/developerguide/) in the IAM console\. If you want full control over a key, then you must create a customer\-managed key\.

You can't delete, revoke, or rotate default keys provisioned by AWS KMS\. You can't share a snapshot that has been encrypted using the default AWS KMS encryption key of the AWS account that shared the snapshot\.

You can view audit logs of every action taken with a customer\-managed key by using [AWS CloudTrail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/)\.

**Important**  
When RDS encounters a DB instance encrypted by a key that RDS doesn't have access to, RDS puts the DB instance into a terminal state\. In this state, the DB instance is no longer available and the current state of the database can't be recovered\. To restore the DB instance, you must re\-enable access to the encryption key for RDS, and then restore the DB instance from a backup\.