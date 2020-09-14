# Encrypting Amazon RDS Resources<a name="Overview.Encryption"></a>

You can encrypt your Amazon RDS DB instances and snapshots at rest by enabling the encryption option for your Amazon RDS DB instances\. Data that is encrypted at rest includes the underlying storage for DB instances, its automated backups, read replicas, and snapshots\.

Amazon RDS encrypted DB instances use the industry standard AES\-256 encryption algorithm to encrypt your data on the server that hosts your Amazon RDS DB instances\. After your data is encrypted, Amazon RDS handles authentication of access and decryption of your data transparently with a minimal impact on performance\. You don't need to modify your database client applications to use encryption\.

**Note**  
For encrypted and unencrypted DB instances, data that is in transit between the source and the read replicas is encrypted, even when replicating across AWS Regions\.

**Topics**
+ [Overview of Encrypting Amazon RDS Resources](#Overview.Encryption.Overview)
+ [Enabling Amazon RDS Encryption for a DB Instance](#Overview.Encryption.Enabling)
+ [Availability of Amazon RDS Encryption](#Overview.Encryption.Availability)
+ [Limitations of Amazon RDS Encrypted DB Instances](#Overview.Encryption.Limitations)

## Overview of Encrypting Amazon RDS Resources<a name="Overview.Encryption.Overview"></a>

Amazon RDS encrypted DB instances provide an additional layer of data protection by securing your data from unauthorized access to the underlying storage\. You can use Amazon RDS encryption to increase data protection of your applications deployed in the cloud, and to fulfill compliance requirements for data\-at\-rest encryption\.

Amazon RDS also supports encrypting an Oracle or SQL Server DB instance with Transparent Data Encryption \(TDE\)\. TDE can be used with encryption at rest, although using TDE and encryption at rest simultaneously might slightly affect the performance of your database\. You must manage different keys for each encryption method\. For more information on TDE, see [Oracle Transparent Data Encryption](Appendix.Oracle.Options.AdvSecurity.md) or [Support for Transparent Data Encryption in SQL Server](Appendix.SQLServer.Options.TDE.md)\.

To manage the customer master keys \(CMKs\) used for encrypting and decrypting your Amazon RDS resources, you use the [AWS Key Management Service \(AWS KMS\)](https://docs.aws.amazon.com/kms/latest/developerguide/)\. AWS KMS combines secure, highly available hardware and software to provide a key management system scaled for the cloud\. Using AWS KMS, you can create CMKs and define the policies that control how these CMKs can be used\. AWS KMS supports CloudTrail, so you can audit CMK usage to verify that CMKs are being used appropriately\. You can use your AWS KMS CMKs with Amazon RDS and supported AWS services such as Amazon S3, Amazon EBS, and Amazon Redshift\. For a list of services that support AWS KMS, see [Supported Services](https://docs.aws.amazon.com/kms/latest/developerguide/services.html) in the *AWS Key Management Service Developer Guide*\.

For an Amazon RDS encrypted DB instance, all logs, backups, and snapshots are encrypted\. A read replica of an Amazon RDS encrypted instance is also encrypted using the same CMK as the primary DB instance when both are in the same AWS Region\. If the primary DB instance and read replica are in different AWS Regions, you encrypt using the CMK for that AWS Region\.

## Enabling Amazon RDS Encryption for a DB Instance<a name="Overview.Encryption.Enabling"></a>

To enable encryption for a new DB instance, choose **Enable encryption** on the Amazon RDS console\. For information on creating a DB instance, see [Creating an Amazon RDS DB Instance](USER_CreateDBInstance.md)\.

If you use the [create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) AWS CLI command to create an encrypted DB instance, set the `--storage-encrypted` parameter to true\. If you use the [CreateDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html) API operation, set the `StorageEncrypted` parameter to true\.

When you create an encrypted DB instance, you can also supply the AWS KMS customer master key \(CMK\) identifier for your CMK\. If you don't specify an AWS KMS CMK identifier, then Amazon RDS uses your default CMK for your new DB instance\. AWS KMS creates your default CMK for Amazon RDS for your AWS account\. Your AWS account has a different default CMK for each AWS Region\.

Once you have created an encrypted DB instance, you can't change the type of CMK used by that DB instance\. Therefore, be sure to determine your CMK requirements before you create your encrypted DB instance\.

If you use the AWS CLI `create-db-instance` command to create an encrypted DB instance, set the `--kms-key-id` parameter to the Amazon Resource Name \(ARN\) for the AWS KMS CMK for the DB instance\. If you use the Amazon RDS API `CreateDBInstance` action, set the `KmsKeyId` parameter to the ARN for your AWS KMS CMK for the DB instance\.

You can use the ARN of a CMK from another account to encrypt a DB instance\. Or you might create a DB instance with the same AWS account that owns the AWS KMS CMK used to encrypt that new DB instance\. In this case, the AWS KMS key identifier that you pass can be the AWS KMS CMK alias instead of the CMK's ARN\.

## Availability of Amazon RDS Encryption<a name="Overview.Encryption.Availability"></a>

Amazon RDS encryption is currently available for all database engines and storage types\.

Amazon RDS encryption is available for most DB instance classes\. The following table lists DB instance classes that *do not support* Amazon RDS encryption:


| Instance Type | Instance Class | 
| --- | --- | 
| General Purpose \(M1\) |  db\.m1\.small db\.m1\.medium db\.m1\.large db\.m1\.xlarge  | 
| Memory Optimized \(M2\) |  db\.m2\.xlarge db\.m2\.2xlarge db\.m2\.4xlarge  | 
| Burst Capable \(T2\) |  db\.t2\.micro  | 

**Note**  
Encryption at rest is not available for DB instances running SQL Server Express Edition\.   

## Limitations of Amazon RDS Encrypted DB Instances<a name="Overview.Encryption.Limitations"></a>

The following limitations exist for Amazon RDS encrypted DB instances:
+ You can only enable encryption for an Amazon RDS DB instance when you create it, not after the DB instance is created\.

  However, because you can encrypt a copy of an unencrypted DB snapshot, you can effectively add encryption to an unencrypted DB instance\. That is, you can create a snapshot of your DB instance, and then create an encrypted copy of that snapshot\. You can then restore a DB instance from the encrypted snapshot, and thus you have an encrypted copy of your original DB instance\. For more information, see [Copying a Snapshot](USER_CopySnapshot.md)\.
+ DB instances that are encrypted can't be modified to disable encryption\.
+ You can't have an encrypted read replica of an unencrypted DB instance or an unencrypted read replica of an encrypted DB instance\.
+ Encrypted read replicas must be encrypted with the same CMK as the source DB instance when both are in the same AWS Region\.
+ You can't restore an unencrypted backup or snapshot to an encrypted DB instance\.
+ To copy an encrypted snapshot from one AWS Region to another, you must specify the AWS KMS key identifier of the destination AWS Region\. This is because AWS KMS CMKs are specific to the AWS Region that they are created in\.

  The source snapshot remains encrypted throughout the copy process\. AWS KMS uses envelope encryption to protect data during the copy process\. For more information about envelope encryption, see [ Envelope Encryption](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#enveloping)\.