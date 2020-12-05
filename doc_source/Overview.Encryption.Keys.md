# Customer master key \(CMK\) management<a name="Overview.Encryption.Keys"></a>

Amazon RDS automatically integrates with AWS Key Management Service \(AWS KMS\) for key management\. Amazon RDS uses envelope encryption\. For more information about envelope encryption, see [ Envelope encryption](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#enveloping) in the *AWS Key Management Service Developer Guide*\.

A *customer master key* \(CMK\) is a logical representation of a master key\. The CMK includes metadata, such as the key ID, creation date, description, and key state\. The CMK also contains the key material used to encrypt and decrypt data\. For more information about customer managed CMKs, see [Customer managed CMKs](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#customer-cmk) in the *AWS Key Management Service Developer Guide*\.

You can manage CMKs used for Amazon RDS encrypted DB instances using the [AWS Key Management Service \(AWS KMS\)](https://docs.aws.amazon.com/kms/latest/developerguide/) in the [AWS KMS console](https://console.aws.amazon.com/kms), the AWS CLI, or the AWS KMS API\. If you want full control over a CMK, then you must create a customer managed CMK\.

*AWS managed CMKs* are CMKs in your account that are created, managed, and used on your behalf by an AWS service that is integrated with AWS KMS\. You can't delete, edit, or rotate AWS managed CMKs\. For more information about AWS managed CMKs, see [AWS managed CMKs](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#aws-managed-cmk) in the *AWS Key Management Service Developer Guide*\.

You can't share a snapshot that has been encrypted using the AWS managed CMK of the AWS account that shared the snapshot\. 

You can view audit logs of every action taken with an AWS managed or customer managed CMK by using [AWS CloudTrail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/)\.

**Important**  
When RDS encounters a DB instance encrypted by a CMK that RDS doesn't have access to, RDS puts the DB instance into a terminal state\. In this state, the DB instance is no longer available and the current state of the database can't be recovered\. To restore the DB instance, you must re\-enable access to the CMK for RDS, and then restore the DB instance from a backup\.

## Authorizing use of the CMK<a name="Overview.Encryption.Keys.Authorizing"></a>

When RDS uses a CMK in cryptographic operations, it acts on behalf of the user who is creating or changing the RDS resource\.

To use the customer managed CMK for an RDS resource on your behalf, a user must have permissions to call the following operations on the CMK:
+ kms:GenerateDataKey
+ kms:Decrypt

You can specify these required permissions in a key policy, or in an IAM policy if the key policy allows it\.

You can make the IAM policy stricter in various ways\. For example, to allow the CMK to be used only for requests that originate in RDS , you can use the [ kms:ViaService condition key](https://docs.aws.amazon.com/kms/latest/developerguide/policy-conditions.html#conditions-kms-via-service) with the `rds.<region>.amazonaws.com` value\.

You can also use the keys or values in the [encryption context](https://docs.aws.amazon.com/kms/latest/developerguide/services-rds.html#rds-encryptioncontext) as a condition for using the CMK for cryptographic operations\.