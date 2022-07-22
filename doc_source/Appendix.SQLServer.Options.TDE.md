# Support for Transparent Data Encryption in SQL Server<a name="Appendix.SQLServer.Options.TDE"></a>

Amazon RDS supports using Transparent Data Encryption \(TDE\) to encrypt stored data on your DB instances running Microsoft SQL Server\. TDE automatically encrypts data before it is written to storage, and automatically decrypts data when the data is read from storage\. 

Amazon RDS supports TDE for the following SQL Server versions and editions:
+ SQL Server 2019 Standard and Enterprise Editions
+ SQL Server 2017 Enterprise Edition
+ SQL Server 2016 Enterprise Edition
+ SQL Server 2014 Enterprise Edition

Transparent Data Encryption for SQL Server provides encryption key management by using a two\-tier key architecture\. A certificate, which is generated from the database master key, is used to protect the data encryption keys\. The database encryption key performs the actual encryption and decryption of data on the user database\. Amazon RDS backs up and manages the database master key and the TDE certificate\.

Transparent Data Encryption is used in scenarios where you need to encrypt sensitive data\. For example, you might want to provide data files and backups to a third party, or address security\-related regulatory compliance issues\. You can't encrypt the system databases for SQL Server, such as the `model` or `master` databases\.

A detailed discussion of Transparent Data Encryption is beyond the scope of this guide, but make sure that you understand the security strengths and weaknesses of each encryption algorithm and key\. For information about Transparent Data Encryption for SQL Server, see [Transparent Data Encryption \(TDE\)](http://msdn.microsoft.com/en-us/library/bb934049.aspx) in the Microsoft documentation\.

**Topics**
+ [Turning on TDE for RDS for SQL Server](#TDE.Enabling)
+ [Encrypting data on RDS for SQL Server](#TDE.Encrypting)
+ [Backing up and restoring TDE certificates on RDS for SQL Server](#TDE.BackupRestoreRDS)
+ [Backing up and restoring TDE certificates for on\-premises databases](#TDE.BackupRestoreOnPrem)
+ [Turning off TDE for RDS for SQL Server](#TDE.Disabling)

## Turning on TDE for RDS for SQL Server<a name="TDE.Enabling"></a>

To turn on Transparent Data Encryption for an RDS for SQL Server DB instance, specify the TDE option in an RDS option group that's associated with that DB instance:

1. Determine whether your DB instance is already associated with an option group that has the TDE option\. To view the option group that a DB instance is associated with, use the RDS console, the [describe\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instances.html) AWS CLI command, or the API operation [DescribeDBInstances](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBInstances.html)\.

1.  If the DB instance isn't associated with an option group that has TDE turned on, you have two choices\. You can create an option group and add the TDE option, or you can modify the associated option group to add it\.
**Note**  
In the RDS console, the option is named `TRANSPARENT_DATA_ENCRYPTION`\. In the AWS CLI and RDS API, it's named `TDE`\.

   For information about creating or modifying an option group, see [Working with option groups](USER_WorkingWithOptionGroups.md)\. For information about adding an option to an option group, see [Adding an option to an option group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.AddOption)\.

1.  Associate the DB instance with the option group that has the TDE option\. For information about associating a DB instance with an option group, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\.

### Option group considerations<a name="TDE.Options"></a>

The TDE option is a persistent option\. You can't remove it from an option group unless all DB instances and backups are no longer associated with the option group\. After you add the TDE option to an option group, the option group can be associated only with DB instances that use TDE\. For more information about persistent options in an option group, see [Option groups overview](USER_WorkingWithOptionGroups.md#Overview.OptionGroups)\. 

Because the TDE option is a persistent option, you can have a conflict between the option group and an associated DB instance\. You can have a conflict in the following situations:
+ The current option group has the TDE option, and you replace it with an option group that doesn't have the TDE option\.
+ You restore from a DB snapshot to a new DB instance that doesn't have an option group that contains the TDE option\. For more information about this scenario, see [Option group considerations](USER_CopySnapshot.md#USER_CopySnapshot.Options)\. 

### SQL Server performance considerations<a name="TDE.Perf"></a>

Using Transparent Data Encryption can affect the performance of a SQL Server DB instance\.

Performance for unencrypted databases can also be degraded if the databases are on a DB instance that has at least one encrypted database\. As a result, we recommend that you keep encrypted and unencrypted databases on separate DB instances\.

## Encrypting data on RDS for SQL Server<a name="TDE.Encrypting"></a>

When the TDE option is added to an option group, Amazon RDS generates a certificate that's used in the encryption process\. You can then use the certificate to run SQL statements that encrypt data in a database on the DB instance\.

The following example uses the RDS\-created certificate called `RDSTDECertificateName` to encrypt a database called `myDatabase`\.

```
 1. ---------- Turning on TDE -------------
 2. 
 3. -- Find an RDS TDE certificate to use
 4. USE [master]
 5. GO
 6. SELECT name FROM sys.certificates WHERE name LIKE 'RDSTDECertificate%'
 7. GO
 8. 
 9. USE [myDatabase]
10. GO
11. -- Create a database encryption key (DEK) using one of the certificates from the previous step
12. CREATE DATABASE ENCRYPTION KEY WITH ALGORITHM = AES_256
13. ENCRYPTION BY SERVER CERTIFICATE [RDSTDECertificateName]
14. GO
15. 
16. -- Turn on encryption for the database
17. ALTER DATABASE [myDatabase] SET ENCRYPTION ON
18. GO
19. 
20. -- Verify that the database is encrypted
21. USE [master]
22. GO
23. SELECT name FROM sys.databases WHERE is_encrypted = 1
24. GO
25. SELECT db_name(database_id) as DatabaseName, * FROM sys.dm_database_encryption_keys
26. GO
```

The time that it takes to encrypt a SQL Server database using TDE depends on several factors\. These include the size of the DB instance, whether the instance uses Provisioned IOPS storage, the amount of data, and other factors\.

## Backing up and restoring TDE certificates on RDS for SQL Server<a name="TDE.BackupRestoreRDS"></a>

RDS for SQL Server provides stored procedures for backing up, restoring, and dropping TDE certificates\. RDS for SQL Server also provides a function for viewing restored user TDE certificates\.

User TDE certificates are used to restore databases to RDS for SQL Server that are on\-premises and have TDE turned on\. These certificates have the prefix `UserTDECertificate_`\. After restoring databases, and before making them available to use, RDS modifies the databases that have TDE turned on to use RDS\-generated TDE certificates\. These certificates have the prefix `RDSTDECertificate`\.

User TDE certificates remain on the RDS for SQL Server DB instance, unless you drop them using the `rds_drop_tde_certificate` stored procedure\. For more information, see [Dropping restored TDE certificates](#TDE.BackupRestoreRDS.Drop)\.

You can use a user TDE certificate to restore other databases from the source DB instance\. The databases to restore must use the same TDE certificate and have TDE turned on\. You don't have to import \(restore\) the same certificate again\.  

**Topics**
+ [Prerequisites](#TDE.BackupRestoreRDS.Prereqs)
+ [Limitations](#TDE.Limitations)
+ [Backing up a TDE certificate](#TDE.BackupRestoreRDS.Backup)
+ [Restoring a TDE certificate](#TDE.BackupRestoreRDS.Restore)
+ [Viewing restored TDE certificates](#TDE.BackupRestoreRDS.Show)
+ [Dropping restored TDE certificates](#TDE.BackupRestoreRDS.Drop)

### Prerequisites<a name="TDE.BackupRestoreRDS.Prereqs"></a>

Before you can back up or restore TDE certificates on RDS for SQL Server, make sure to perform the following tasks\. The first three are described in [Setting up for native backup and restore](SQLServer.Procedural.Importing.md#SQLServer.Procedural.Importing.Native.Enabling)\.

1. Create Amazon S3 buckets for storing files to back up and restore\.

   We recommend that you use separate buckets for database backups and for TDE certificate backups\.

1. Create an IAM role for backing up and restoring files\.

   The IAM role must be both a user and an administrator for the AWS KMS key\.

   In addition to the permissions required for SQL Server native backup and restore, the IAM role also requires the following permissions:
   + `s3:GetBucketACL`, `s3:GetBucketLocation`, and `s3:ListBucket` on the S3 bucket resource
   + `s3:ListAllMyBuckets` on the `*` resource

1. Add the `SQLSERVER_BACKUP_RESTORE` option to an option group on your DB instance\.

   This is in addition to the `TRANSPARENT_DATA_ENCRYPTION` \(`TDE`\) option\.

1. Make sure that you have a symmetric encryption KMS key\. You have the following options:
   + If you have an existing KMS key in your account, you can use it\. No further action is necessary\.
   + If you don't have an existing symmetric encryption KMS key in your account, create a KMS key by following the instructions in [Creating keys](https://docs.aws.amazon.com/kms/latest/developerguide/create-keys.html#create-symmetric-cmk) in the *AWS Key Management Service Developer Guide*\.

### Limitations<a name="TDE.Limitations"></a>

Using stored procedures to back up and restore TDE certificates has the following limitations:
+ Both the `SQLSERVER_BACKUP_RESTORE` and `TRANSPARENT_DATA_ENCRYPTION` \(`TDE`\) options must be added to the option group that you associated with your DB instance\.
+ TDE certificate backup and restore aren't supported on Multi\-AZ DB instances\.
+ Canceling TDE certificate backup and restore tasks isn't supported\.
+ You can't use a user TDE certificate for TDE encryption of any other database on your RDS for SQL Server DB instance\. You can use it to restore only other databases from the source DB instance that have TDE turned on and that use the same TDE certificate\.
+ You can drop only user TDE certificates\.
+ The maximum number of user TDE certificates supported on RDS is 10\. If the number exceeds 10, drop unused TDE certificates and try again\.
+ The certificate name can't be empty or null\.
+ When restoring a certificate, the certificate name can't include the keyword `RDSTDECERTIFICATE`, and must start with the `UserTDECertificate_` prefix\.
+ The `@certificate_name` parameter can include only the following characters: a\-z, 0\-9, @, $, \#, and underscore \(\_\)\.
+ The file extension for `@certificate_file_s3_arn` must be \.cer \(case\-insensitive\)\.
+ The file extension for `@private_key_file_s3_arn` must be \.pvk \(case\-insensitive\)\.
+ The S3 metadata for the private key file must include the `x-amz-meta-rds-tde-pwd` tag\. For more information, see [Backing up and restoring TDE certificates for on\-premises databases](#TDE.BackupRestoreOnPrem)\.

### Backing up a TDE certificate<a name="TDE.BackupRestoreRDS.Backup"></a>

To back up TDE certificates, use the `rds_backup_tde_certificate` stored procedure\. It has the following syntax\.

```
EXECUTE msdb.dbo.rds_backup_tde_certificate
    @certificate_name='UserTDECertificate_certificate_name | RDSTDECertificatetimestamp',
    @certificate_file_s3_arn='arn:aws:s3:::bucket_name/certificate_file_name.cer',
    @private_key_file_s3_arn='arn:aws:s3:::bucket_name/key_file_name.pvk',
    @kms_password_key_arn='arn:aws:kms:region:account-id:key/key-id',
    [@overwrite_s3_files=0|1];
```

The following parameters are required:
+ `@certificate_name` – The name of the TDE certificate to back up\.
+ `@certificate_file_s3_arn` – The destination Amazon Resource Name \(ARN\) for the certificate backup file in Amazon S3\.
+ `@private_key_file_s3_arn` – The destination S3 ARN of the private key file that secures the TDE certificate\.
+ `@kms_password_key_arn` – The ARN of the symmetric KMS key used to encrypt the private key password\.

The following parameter is optional:
+ `@overwrite_s3_files` – Indicates whether to overwrite the existing certificate and private key files in S3:
  + `0` – Doesn't overwrite the existing files\. This value is the default\.

    Setting `@overwrite_s3_files` to 0 returns an error if a file already exists\.
  + `1` – Overwrites an existing file that has the specified name, even if it isn't a backup file\.

**Example of backing up a TDE certificate**  

```
EXECUTE msdb.dbo.rds_backup_tde_certificate
    @certificate_name='RDSTDECertificate20211115T185333',
    @certificate_file_s3_arn='arn:aws:s3:::TDE_certs/mycertfile.cer',
    @private_key_file_s3_arn='arn:aws:s3:::TDE_certs/mykeyfile.pvk',
    @kms_password_key_arn='arn:aws:kms:us-west-2:123456789012:key/AKIAIOSFODNN7EXAMPLE',
    @overwrite_s3_files=1;
```

### Restoring a TDE certificate<a name="TDE.BackupRestoreRDS.Restore"></a>

You use the `rds_restore_tde_certificate` stored procedure to restore \(import\) user TDE certificates\. It has the following syntax\.

```
EXECUTE msdb.dbo.rds_restore_tde_certificate
    @certificate_name='UserTDECertificate_certificate_name',
    @certificate_file_s3_arn='arn:aws:s3:::bucket_name/certificate_file_name.cer',
    @private_key_file_s3_arn='arn:aws:s3:::bucket_name/key_file_name.pvk',
    @kms_password_key_arn='arn:aws:kms:region:account-id:key/key-id';
```

The following parameters are required:
+ `@certificate_name` – The name of the TDE certificate to restore\. The name must start with the `UserTDECertificate_` prefix\.
+ `@certificate_file_s3_arn` – The S3 ARN of the backup file used to restore the TDE certificate\.
+ `@private_key_file_s3_arn` – The S3 ARN of the private key backup file of the TDE certificate to be restored\.
+ `@kms_password_key_arn` – The ARN of the symmetric KMS key used to encrypt the private key password\.

**Example of restoring a TDE certificate**  

```
EXECUTE msdb.dbo.rds_restore_tde_certificate
    @certificate_name='UserTDECertificate_myTDEcertificate',
    @certificate_file_s3_arn='arn:aws:s3:::TDE_certs/mycertfile.cer',
    @private_key_file_s3_arn='arn:aws:s3:::TDE_certs/mykeyfile.pvk',
    @kms_password_key_arn='arn:aws:kms:us-west-2:123456789012:key/AKIAIOSFODNN7EXAMPLE';
```

### Viewing restored TDE certificates<a name="TDE.BackupRestoreRDS.Show"></a>

You use the `rds_fn_list_user_tde_certificates` function to view restored \(imported\) user TDE certificates\. It has the following syntax\.

```
SELECT * FROM msdb.dbo.rds_fn_list_user_tde_certificates();
```

The output resembles the following\. Not all columns are shown here\.


|  |  |  |  |  |  |  |  |  |  |  | 
| --- |--- |--- |--- |--- |--- |--- |--- |--- |--- |--- |
| name | certificate\_id | principal\_id | pvt\_key\_encryption\_type\_desc | issuer\_name | cert\_serial\_number | thumbprint | subject | start\_date | expiry\_date | pvt\_key\_last\_backup\_date | 
| UserTDECertificate\_tde\_cert | 343 | 1 | ENCRYPTED\_BY\_MASTER\_KEY | AnyCompany Shipping | 79 3e 57 a3 69 fd 1d 9e 47 2c 32 67 1d 9c ca af | 0x6BB218B34110388680B FE1BA2D86C695096485B5 | AnyCompany Shipping | 2022\-04\-05 19:49:45\.0000000 | 2023\-04\-05 19:49:45\.0000000 | NULL | 

### Dropping restored TDE certificates<a name="TDE.BackupRestoreRDS.Drop"></a>

To drop restored \(imported\) user TDE certificates that you aren't using, use the `rds_drop_tde_certificate` stored procedure\. It has the following syntax\.

```
EXECUTE msdb.dbo.rds_drop_tde_certificate @certificate_name='UserTDECertificate_certificate_name';
```

The following parameter is required:
+ `@certificate_name` – The name of the TDE certificate to drop\.

You can drop only restored \(imported\) TDE certificates\. You can't drop RDS\-created certificates\.

**Example of dropping a TDE certificate**  

```
EXECUTE msdb.dbo.rds_drop_tde_certificate @certificate_name='UserTDECertificate_myTDEcertificate';
```

## Backing up and restoring TDE certificates for on\-premises databases<a name="TDE.BackupRestoreOnPrem"></a>

You can back up TDE certificates for on\-premises databases, then later restore them to RDS for SQL Server\. You can also restore an RDS for SQL Server TDE certificate to an on\-premises DB instance\.

The following procedure backs up a TDE certificate and private key\. The private key is encrypted using a data key generated from your symmetric encryption KMS key\.

**To back up an on\-premises TDE certificate**

1. Generate the data key using the AWS CLI [generate\-data\-key](https://docs.aws.amazon.com/cli/latest/reference/kms/generate-data-key.html) command\.

   ```
   aws kms generate-data-key \
       --key-id my_KMS_key_ID \
       --key-spec AES_256
   ```

   The output resembles the following\.

   ```
   {
   "CiphertextBlob": "AQIDAHimL2NEoAlOY6Bn7LJfnxi/OZe9kTQo/XQXduug1rmerwGiL7g5ux4av9GfZLxYTDATAAAAfjB8BgkqhkiG9w0B
   BwagbzBtAgEAMGgGCSqGSIb3DQEHATAeBglghkgBZQMEAS4wEQQMyCxLMi7GRZgKqD65AgEQgDtjvZLJo2cQ31Vetngzm2ybHDc3d2vI74SRUzZ
   2RezQy3sAS6ZHrCjfnfn0c65bFdhsXxjSMnudIY7AKw==",
   "Plaintext": "U/fpGtmzGCYBi8A2+0/9qcRQRK2zmG/aOn939ZnKi/0=",
   "KeyId": "arn:aws:kms:us-west-2:123456789012:key/1234abcd-00ee-99ff-88dd-aa11bb22cc33"
   }
   ```

   You use the plain text output in the next step as the private key password\.

1. Back up your TDE certificate as shown in the following example\.

   ```
   BACKUP CERTIFICATE myOnPremTDEcertificate TO FILE = 'D:\tde-cert-backup.cer'
   WITH PRIVATE KEY (
   FILE = 'C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER\MSSQL\DATA\cert-backup-key.pvk',
   ENCRYPTION BY PASSWORD = 'U/fpGtmzGCYBi8A2+0/9qcRQRK2zmG/aOn939ZnKi/0=');
   ```

1. Save the certificate backup file to your Amazon S3 certificate bucket\.

1. Save the private key backup file to your S3 certificate bucket, with the following tag in the file's metadata:
   + Key – `x-amz-meta-rds-tde-pwd`
   + Value – The `CiphertextBlob` value from generating the data key, as in the following example\.

     ```
     AQIDAHimL2NEoAlOY6Bn7LJfnxi/OZe9kTQo/XQXduug1rmerwGiL7g5ux4av9GfZLxYTDATAAAAfjB8BgkqhkiG9w0B
     BwagbzBtAgEAMGgGCSqGSIb3DQEHATAeBglghkgBZQMEAS4wEQQMyCxLMi7GRZgKqD65AgEQgDtjvZLJo2cQ31Vetngzm2ybHDc3d2vI74SRUzZ
     2RezQy3sAS6ZHrCjfnfn0c65bFdhsXxjSMnudIY7AKw==
     ```

The following procedure restores an RDS for SQL Server TDE certificate to an on\-premises DB instance\. You copy and restore the TDE certificate on your destination DB instance using the certificate backup, corresponding private key file, and data key\. The restored certificate is encrypted by the database master key of the new server\.   

**To restore a TDE certificate**

1. Copy the TDE certificate backup file and private key file from Amazon S3 to the destination instance\.

1. Use your KMS key to decrypt the output cipher text to retrieve the plain text of the data key\. The cipher text is located in the S3 metadata of the private key backup file\.

   ```
   aws kms decrypt \
       --key-id my_KMS_key_ID \
       --ciphertext-blob fileb://exampleCiphertextFile | base64 -d \
       --output text \
       --query Plaintext
   ```

   You use the plain text output in the next step as the private key password\.

1. Use the following SQL command to restore your TDE certificate\.

   ```
   CREATE CERTIFICATE myOnPremTDEcertificate FROM FILE='D:\tde-cert-backup.cer'
   WITH PRIVATE KEY (FILE = N'D:\tde-cert-key.pvk',
   DECRYPTION BY PASSWORD = 'plain_text_output');
   ```

For more information on KMS decryption, see [decrypt](https://docs.aws.amazon.com/cli/latest/reference/kms/decrypt.html) in the KMS section of the *AWS CLI Command Reference*\.

After the TDE certificate is restored on the destination DB instance, you can restore encrypted databases with that certificate\.

**Note**  
You can use the same TDE certificate to encrypt multiple SQL Server databases on the source DB instance\. To migrate multiple databases to a destination instance, copy the TDE certificate associated with them to the destination instance only once\.

## Turning off TDE for RDS for SQL Server<a name="TDE.Disabling"></a>

To turn off TDE for an RDS for SQL Server DB instance, first make sure that there are no encrypted objects left on the DB instance\. To do so, either decrypt the objects or drop them\. If any encrypted objects exist on the DB instance, you can't turn off TDE for the DB instance\. When you use the console to remove the TDE option from an option group, the console indicates that it's processing\. In addition, an error event is created if the option group is associated with an encrypted DB instance or DB snapshot\.

The following example removes the TDE encryption from a database called `customerDatabase`\. 

```
 1. ------------- Removing TDE ----------------
 2. 
 3. USE [customerDatabase]
 4. GO
 5. 
 6. -- Turn off encryption of the database
 7. ALTER DATABASE [customerDatabase]
 8. SET ENCRYPTION OFF
 9. GO
10. 
11. -- Wait until the encryption state of the database becomes 1. The state is 5 (Decryption in progress) for a while
12. SELECT db_name(database_id) as DatabaseName, * FROM sys.dm_database_encryption_keys
13. GO
14. 
15. -- Drop the DEK used for encryption
16. DROP DATABASE ENCRYPTION KEY
17. GO
18. 
19. -- Alter to SIMPLE Recovery mode so that your encrypted log gets truncated
20. USE [master]
21. GO
22. ALTER DATABASE [customerDatabase] SET RECOVERY SIMPLE
23. GO
```

When all objects are decrypted, you have two options:

1. You can modify the DB instance to be associated with an option group without the TDE option\.

1. You can remove the TDE option from the option group\.