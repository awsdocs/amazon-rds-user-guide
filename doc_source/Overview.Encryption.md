# Encrypting Amazon RDS resources<a name="Overview.Encryption"></a>

Amazon RDS can encrypt your Amazon RDS DB instances\. Data that is encrypted at rest includes the underlying storage for DB instances, its automated backups, read replicas, and snapshots\.

Amazon RDS encrypted DB instances use the industry standard AES\-256 encryption algorithm to encrypt your data on the server that hosts your Amazon RDS DB instances\. After your data is encrypted, Amazon RDS handles authentication of access and decryption of your data transparently with a minimal impact on performance\. You don't need to modify your database client applications to use encryption\.

**Note**  
For encrypted and unencrypted DB instances, data that is in transit between the source and the read replicas is encrypted, even when replicating across AWS Regions\.

**Topics**
+ [Overview of encrypting Amazon RDS resources](#Overview.Encryption.Overview)
+ [Enabling Amazon RDS encryption for a DB instance](#Overview.Encryption.Enabling)
+ [Availability of Amazon RDS encryption](#Overview.Encryption.Availability)
+ [Limitations of Amazon RDS encrypted DB instances](#Overview.Encryption.Limitations)

## Overview of encrypting Amazon RDS resources<a name="Overview.Encryption.Overview"></a>

Amazon RDS encrypted DB instances provide an additional layer of data protection by securing your data from unauthorized access to the underlying storage\. You can use Amazon RDS encryption to increase data protection of your applications deployed in the cloud, and to fulfill compliance requirements for encryption at rest\.

Amazon RDS also supports encrypting an Oracle or SQL Server DB instance with Transparent Data Encryption \(TDE\)\. TDE can be used with encryption at rest, although using TDE and encryption at rest simultaneously might slightly affect the performance of your database\. You must manage different keys for each encryption method\. For more information on TDE, see [Oracle Transparent Data Encryption](Appendix.Oracle.Options.AdvSecurity.md) or [Support for Transparent Data Encryption in SQL Server](Appendix.SQLServer.Options.TDE.md)\.

For an Amazon RDS encrypted DB instance, all logs, backups, and snapshots are encrypted\. Amazon RDS uses an AWS KMS key to encrypt these resources\. For more information about KMS keys, see [AWS KMS keys](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#kms_keys) in the *AWS Key Management Service Developer Guide*\. If you copy an encrypted snapshot, you can use a different KMS key to encrypt the target snapshot than the one that was used to encrypt the source snapshot\.

A read replica of an Amazon RDS encrypted instance must be encrypted using the same KMS key as the primary DB instance when both are in the same AWS Region\. If the primary DB instance and read replica are in different AWS Regions, you encrypt the read replica using the KMS key for that AWS Region\.

You can use an AWS managed key, or you can create customer managed keys\. To manage the customer managed keys used for encrypting and decrypting your Amazon RDS resources, you use the [AWS Key Management Service \(AWS KMS\)](https://docs.aws.amazon.com/kms/latest/developerguide/)\. AWS KMS combines secure, highly available hardware and software to provide a key management system scaled for the cloud\. Using AWS KMS, you can create customer managed keys and define the policies that control how these customer managed keys can be used\. AWS KMS supports CloudTrail, so you can audit KMS key usage to verify that customer managed keys are being used appropriately\. You can use your customer managed keys with Amazon Aurora and supported AWS services such as Amazon S3, Amazon EBS, and Amazon Redshift\. For a list of services that are integrated with AWS KMS, see [AWS Service Integration](http://aws.amazon.com/kms/features/#AWS_Service_Integration)\.

## Enabling Amazon RDS encryption for a DB instance<a name="Overview.Encryption.Enabling"></a>

To enable encryption for a new DB instance, choose **Enable encryption** on the Amazon RDS console\. For information on creating a DB instance, see [Creating an Amazon RDS DB instance](USER_CreateDBInstance.md)\.

If you use the [create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) AWS CLI command to create an encrypted DB instance, set the `--storage-encrypted` parameter\. If you use the [CreateDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html) API operation, set the `StorageEncrypted` parameter to true\.

When you create an encrypted DB instance, you can choose a customer managed key or the AWS managed key for Amazon RDS to encrypt your DB instance\. If you don't specify the key identifier for a customer managed key, Amazon RDS uses the AWS managed key for your new DB instance\. Amazon RDS creates an AWS managed key for Amazon RDS for your AWS account\. Your AWS account has a different AWS managed key for Amazon RDS for each AWS Region\.

Once you have created an encrypted DB instance, you can't change the KMS key used by that DB instance\. Therefore, be sure to determine your KMS key requirements before you create your encrypted DB instance\.

If you use the AWS CLI `create-db-instance` command to create an encrypted DB instance with a customer managed key, set the `--kms-key-id` parameter to any key identifier for the KMS key\. If you use the Amazon RDS API `CreateDBInstance` operation, set the `KmsKeyId` parameter to any key identifier for the KMS key\. To use a customer managed key in a different AWS account, specify the key ARN or alias ARN\.

**Important**  
If Amazon RDS loses access to the KMS key for a DB instance—for example, when RDS access to a KMS key is revoked—then the encrypted DB instance goes into a terminal state\. In this case, you can only restore the DB instance from a backup\. We strongly recommend that you always enable backups for encrypted DB instances to guard against the loss of encrypted data in your databases\.

## Availability of Amazon RDS encryption<a name="Overview.Encryption.Availability"></a>

Amazon RDS encryption is currently available for all database engines and storage types\.

Amazon RDS encryption is available for most DB instance classes\. The following table lists DB instance classes that *do not support* Amazon RDS encryption:


| Instance type | Instance class | 
| --- | --- | 
| General purpose \(M1\) |  db\.m1\.small db\.m1\.medium db\.m1\.large db\.m1\.xlarge  | 
| Memory optimized \(M2\) |  db\.m2\.xlarge db\.m2\.2xlarge db\.m2\.4xlarge  | 
| Burstable \(T2\) |  db\.t2\.micro  | 

**Note**  
Encryption at rest is not available for DB instances running SQL Server Express Edition\.   

## Limitations of Amazon RDS encrypted DB instances<a name="Overview.Encryption.Limitations"></a>

The following limitations exist for Amazon RDS encrypted DB instances:
+ You can only enable encryption for an Amazon RDS DB instance when you create it, not after the DB instance is created\.

  However, because you can encrypt a copy of an unencrypted snapshot, you can effectively add encryption to an unencrypted DB instance\. That is, you can create a snapshot of your DB instance, and then create an encrypted copy of that snapshot\. You can then restore a DB instance from the encrypted snapshot, and thus you have an encrypted copy of your original DB instance\. For more information, see [Copying a snapshot](USER_CopySnapshot.md)\.
+ You can't disable encryption on an encrypted DB instance\.
+ You can't create an encrypted snapshot of an unencrypted DB instance\.
+ A snapshot of an encrypted DB instance must be encrypted using the same KMS key as the DB instance\.
+ You can't have an encrypted read replica of an unencrypted DB instance or an unencrypted read replica of an encrypted DB instance\.
+ Encrypted read replicas must be encrypted with the same KMS key as the source DB instance when both are in the same AWS Region\.
+ You can't restore an unencrypted backup or snapshot to an encrypted DB instance\.
+ To copy an encrypted snapshot from one AWS Region to another, you must specify the KMS key in the destination AWS Region\. This is because KMS keys are specific to the AWS Region that they are created in\.

  The source snapshot remains encrypted throughout the copy process\. Amazon RDS uses envelope encryption to protect data during the copy process\. For more information about envelope encryption, see [ Envelope encryption](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#enveloping) in the *AWS Key Management Service Developer Guide*\.
+ You can't unencrypt an encrypted DB instance\. However, you can export data from an encrypted DB instance and import the data into an unencrypted DB instance\.