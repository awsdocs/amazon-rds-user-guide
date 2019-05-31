# Importing and Exporting SQL Server Databases<a name="SQLServer.Procedural.Importing"></a>

Amazon RDS supports native backup and restore for Microsoft SQL Server databases using full backup files \(\.bak files\)\. When you use RDS, you access files stored in Amazon S3 rather than using the local file system on the database server\. 

For example, you can create a full backup from your local server, store it on S3, and then restore it onto an existing Amazon RDS DB instance\. You can also make backups from RDS, store them on S3, and then restore them wherever you wish\. 

Native backup and restore is available in all AWS Regions, and for both Single\-AZ and Multi\-AZ DB instances\. Native backup and restore is available for all editions of Microsoft SQL Server supported on Amazon RDS\. 

The following diagram shows the supported scenarios\.

![\[Native Backup and Restore Architecture\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/SQL-bak-files.png)

Using native \.bak files to back up and restore databases is usually the fastest way to backup and restore databases\. There are many additional advantages to using native backup and restore\. For example, you can do the following: 
+ Migrate databases to or from Amazon RDS\.
+ Move databases between Amazon RDS SQL Server DB instances\.
+ Migrate data, schemas, stored procedures, triggers and other database code inside \.bak files\.
+ Backup and restore single databases, instead of entire DB instances\.
+ Create copies of databases for development, testing, training, and demonstrations\.
+ Store and transfer backup files with Amazon S3, for an added layer of protection for disaster recovery\. 

## Limitations and Recommendations<a name="SQLServer.Procedural.Importing.Native.Limitations"></a>

The following are some limitations to using native backup and restore: 
+ You can't back up to, or restore from, an Amazon S3 bucket in a different AWS Region than your Amazon RDS DB instance\. 
+ We strongly recommend that you don't restore backups from one time zone to a different time zone\. If you restore backups from one time zone to a different time zone, you must audit your queries and applications for the effects of the time zone change\.   
+ Native backups of databases larger than 1 TB are not supported\. 
+ RDS supports native restores of databases up to 5 TB\. Native restores of databases on SQL Server Express are limited by the MSSQL edition to 10 GB or less\. 
+ You can't do a native backup during the maintenance window, or any time Amazon RDS is in the process of taking a snapshot of the database\. 
+ On Multi\-AZ DB instances, you can only natively restore databases that are backed up in full recovery model\.
+ Calling the RDS procedures for native backup and restore within a transaction is not supported\.
+ Native backup files are encrypted with the specified AWS Key Management Service key using the "Encryption\-Only" crypto mode\. When you are restoring encrypted backup files, be aware that they were encrypted with the "Encryption\-Only" crypto mode\.

If your database can be offline while the backup file is created, copied, and restored, we recommend that you use native backup and restore to migrate it to RDS\. If your on\-premises database can't be offline, we recommend that you use the AWS Database Migration Service to migrate your database to Amazon RDS\. For more information, see [ What Is AWS Database Migration Service?](https://docs.aws.amazon.com/dms/latest/userguide/Welcome.html) 

Native backup and restore isn't intended to replace the data recovery capabilities of the cross\-region snapshot copy feature\. We recommend that you use snapshot copy to copy your database snapshot to another region for cross\-region disaster recovery in Amazon RDS\. For more information, see [Copying a Snapshot](USER_CopySnapshot.md)\. 

## Setting Up for Native Backup and Restore<a name="SQLServer.Procedural.Importing.Native.Enabling"></a>

To set up for native backup and restore, you need three components: 

1. An Amazon S3 bucket to store your backup files\. 

   You need to have an S3 bucket to use for your backup files and then upload backups you want to migrate to RDS\. If you already have an Amazon S3 bucket, you can use that\. If you don't, you can [ create a bucket](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/CreatingaBucket.html)\. Alternatively, you can choose to have a new bucket created for you when you add the `SQLSERVER_BACKUP_RESTORE` option by using the AWS Management Console\. 

   For information on using S3, see the *Amazon Simple Storage Service Getting Started Guide *for a simple introduction\. For more depth, see the *Amazon Simple Storage Service Console User Guide*\.

1. An AWS Identity and Access Management \(IAM\) role to access the bucket\.

   If you already have an IAM role, you can use that\. If you don't have an IAM role, you can create a new one manually\. Alternatively, you can choose to have a new IAM role created for you when you add the `SQLSERVER_BACKUP_RESTORE` option by using the AWS Management Console\. 

   If you want to create a new IAM role manually, take the approach discussed in the next section\. Also, if you want to attach trust and permissions policies to an existing IAM role, take the approach discussed in the next section\. 

1. The `SQLSERVER_BACKUP_RESTORE` option added to an option group on your DB instance\.

   To enable native backup and restore on your DB instance, you add the `SQLSERVER_BACKUP_RESTORE` option to an option group on your DB instance\. For more information and instructions, see [Support for Native Backup and Restore in Microsoft SQL Server](Appendix.SQLServer.Options.BackupRestore.md)\. 

### Manually Creating an IAM Role for Native Backup and Restore<a name="SQLServer.Procedural.Importing.Native.Enabling.IAM"></a>

If you want to manually create a new IAM role to use with native backup and restore, you create a role to delegate permissions from the Amazon RDS service to your Amazon S3 bucket\. When you create an IAM role, you attach trust and permissions policies\. The trust policy allows RDS to assume this role\. The permission policy defines the actions this Role can do\. For more information about creating the role, see [ Creating a Role to Delegate Permissions to an AWS Service](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-service.html)\. 

For the native backup and restore feature, use trust and permissions policies similar to the examples in this section\. In following example, we use the service principle name `rds.amazon.aws.com` as an alias for all service accounts\. In the other examples, we specify an Amazon Resource Name \(ARN\) to identify another account, user, or role that we're granting access to in the trust policy\. 

**Example Trust Policy for Native Backup and Restore**  

```
1. {
2.     "Version": "2012-10-17",
3.     "Statement":
4.     [{
5.         "Effect": "Allow",
6.         "Principal": {"Service":  "rds.amazonaws.com"},
7.         "Action": "sts:AssumeRole"
8.     }]
9. }
```

The following example uses an ARN to specify a resource\. For more information on using ARNs, see [ Amazon Resource Names \(ARNs\)](https://docs.aws.amazon.com/general/latest/gr/aws-arns-and-namespaces.html)\. 

**Example Permissions Policy for Native Backup and Restore Without Encryption Support**  

```
 1. {
 2.     "Version": "2012-10-17",
 3.     "Statement":
 4.     [
 5.         {
 6.         "Effect": "Allow",
 7.         "Action":
 8.             [
 9.                 "s3:ListBucket",
10.                 "s3:GetBucketLocation"
11.             ],
12.         "Resource": "arn:aws:s3:::bucket_name"
13.         },
14.         {
15.         "Effect": "Allow",
16.         "Action":
17.             [
18.                 "s3:GetObject",
19.                 "s3:PutObject",
20.                 "s3:ListMultipartUploadParts",
21.                 "s3:AbortMultipartUpload"
22.             ],
23.         "Resource": "arn:aws:s3:::bucket_name/*"
24.         }
25.     ]
26. }
```

**Example Permissions Policy for Native Backup and Restore with Encryption Support**  
If you want to encrypt your backup files, include an encryption key in your permissions policy\. For more information about encryption keys, see [ Getting Started](https://docs.aws.amazon.com/kms/latest/developerguide/getting-started.html) in the AWS Key Management Service \(AWS KMS\) documentation\.   

```
 1. {
 2.     "Version": "2012-10-17",
 3.     "Statement":
 4.     [
 5.         {
 6.         "Effect": "Allow",
 7.         "Action":
 8.             [
 9.                 "kms:DescribeKey",
10.                 "kms:GenerateDataKey",
11.                 "kms:Encrypt",
12.                 "kms:Decrypt"
13.             ],
14.         "Resource": "arn:aws:kms:region:account-id:key/key-id"
15.         },
16.         {
17.         "Effect": "Allow",
18.         "Action":
19.             [
20.                 "s3:ListBucket",
21.                 "s3:GetBucketLocation"
22.             ],
23.         "Resource": "arn:aws:s3:::bucket_name"
24.         },
25.         {
26.         "Effect": "Allow",
27.         "Action":
28.             [
29.                 "s3:GetObject",
30.                 "s3:PutObject",
31.                 "s3:ListMultipartUploadParts",
32.                 "s3:AbortMultipartUpload"
33.             ],
34.         "Resource": "arn:aws:s3:::bucket_name/*"
35.         }
36.     ]
37. }
```

## Using Native Backup and Restore<a name="SQLServer.Procedural.Importing.Native.Using"></a>

After you have enabled and configured native backup and restore, you can start using it\. First you connect to your Microsoft SQL Server database, and then call an Amazon RDS stored procedure to do the work\. For instructions on connecting to your database, see [Connecting to a DB Instance Running the Microsoft SQL Server Database Engine](USER_ConnectToMicrosoftSQLServerInstance.md)\. 

Some of the stored procedures require that you provide an Amazon Resource Name \(ARN\) to your Amazon S3 bucket and file\. The format for your ARN is `arn:aws:s3:::bucket_name/file_name`\. Amazon S3 doesn't require an account number or region in ARNs\. If you also provide an optional AWS KMS encryption key, the format your ARN is `arn:aws:kms:region:account-id:key/key-id`\. For more information, see [ Amazon Resource Names \(ARNs\) and AWS Service Namespaces](https://docs.aws.amazon.com/general/latest/gr/aws-arns-and-namespaces.html)\. 

There are stored procedures for backing up your database, restoring your database, canceling tasks that are in progress, and tracking the status of the backup and restore tasks\. For instructions on how to call each stored procedure, see the following subsections: 
+ [Backing Up a Database](#SQLServer.Procedural.Importing.Native.Using.Backup)
+ [Restoring a Database](#SQLServer.Procedural.Importing.Native.Using.Restore)
+ [Canceling a Task](#SQLServer.Procedural.Importing.Native.Using.Cancel)
+ [Tracking the Status of Tasks](#SQLServer.Procedural.Importing.Native.Using.Poll)

### Backing Up a Database<a name="SQLServer.Procedural.Importing.Native.Using.Backup"></a>

To back up your database, you call the `rds_backup_database` stored procedure\. 

**Note**  
You can't back up a database during the maintenance window, or when Amazon RDS is taking a snapshot\. 

The following parameters are required: 
+ `@source_db_name` – The name of the database to back up
+ `@s3_arn_to_backup_to` – The bucket to use for the backup, plus the name of the file \(Amazon S3 bucket \+ key ARN\)\. 

  The file can have any extension, but `.bak` is traditional\. 

**The following parameters are optional: **
+ `@kms_master_key_arn` – The key to encrypt the backup \(KMS customer master key ARN\)\. If you don't specify an AWS KMS key identifier, then Amazon RDS uses your default encryption key for your new DB instance\. AWS KMS creates your default encryption key for Amazon RDS for your AWS account\. Your AWS account has a different default encryption key for each AWS Region\. 

  For more information, see [Encrypting Amazon RDS Resources](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Overview.Encryption.html)\. 
+ `@overwrite_S3_backup_file` – Defaults to `0`
  + `0` – Don't overwrite the existing file\. Return an error instead if the file already exists\. 
  + `1` – Overwrite an existing file that has the specified name, even if it isn't a backup file\. 
+ `@type` – Defaults to `FULL`, not case sensitive
  + `differential` – Take a differential backup\.
  + `full` – Take a full backup\.

**Example Differential Backup without Encryption**  

```
1. exec msdb.dbo.rds_backup_database 
2.         @source_db_name='database_name', 
3.         @s3_arn_to_backup_to='arn:aws:s3:::bucket_name/file_name_and_extension',
4.         @overwrite_S3_backup_file=1,
5.         @type='differential';
```

**Example Full Backup with Encryption**  

```
1. exec msdb.dbo.rds_backup_database 
2.         @source_db_name='database_name',
3.         @s3_arn_to_backup_to='arn:aws:s3:::bucket_name/file_name_and_extension',
4.         @kms_master_key_arn='arn:aws:kms:region:account-id:key/key-id',
5.         @overwrite_S3_backup_file=1,
6.         @type='FULL';
```

The differential backup is based on the last full backup\. For differential backups to work, you can't take a snapshot between the last full backup and the differential backup\. If you want to take a differential backup, and a snapshot exists, make another full backup before proceeding with the differential\. 

You can look for the last full backup or snapshot using the following sample SQL: 

```
select top 1 
database_name
, 	backup_start_date
, 	backup_finish_date 
from    msdb.dbo.backupset 
where   database_name='name-of-database'
and     type = 'D' 
order by backup_start_date desc;
```

### Restoring a Database<a name="SQLServer.Procedural.Importing.Native.Using.Restore"></a>

To restore your database, you call the `rds_restore_database` stored procedure\. 

The following parameters are required: 
+ `@restore_db_name` – The name of the database to restore\. 
+ `@s3_arn_to_restore_from` – The Amazon S3 ARN prefix of the backup files used to restore database from\. For a single file backup, provide the entire name\. To restore from a multiple file backup, provide the prefix the files have in common, then suffix that with an asterisk \(`*`\)\. Following are examples\.

  The following example shows single file restore\.  
**Example**  

  ```
  exec msdb.dbo.rds_restore_database
  @restore_db_name='database_name',
  @s3_arn_to_restore_from='arn:aws:s3:::bucket_name/bakup_file'
  ```

  To avoid errors while restoring multiple files, make sure that all the backup files have the same prefix, and that no other files use that prefix\. 

  The following example shows multiple\-file restore\.  
**Example**  

  ```
  exec msdb.dbo.rds_restore_database
  @restore_db_name='database_name',
  @s3_arn_to_restore_from='arn:aws:s3:::bucket_name/bakup_file_*'
  ```

  If `@s3_arn_to_restore_from` is empty, the following error is returned: "S3 ARN prefix cannot be empty"\.

The following parameter is optional\. 
+ `@kms_master_key_arn` – If you encrypted the backup file, the key to use to decrypt the file\. 

The following example shows restore without encryption\.

**Example**  

```
exec msdb.dbo.rds_restore_database 
        @restore_db_name='database_name', 
        @s3_arn_to_restore_from='arn:aws:s3:::bucket_name/file_name_and_extension';
```

The following example shows restore with encryption\.

**Example**  

```
exec msdb.dbo.rds_restore_database 
        @restore_db_name='database_name', 
        @s3_arn_to_restore_from='arn:aws:s3:::bucket_name/file_name_and_extension',
        @kms_master_key_arn='arn:aws:kms:region:account-id:key/key-id';
```

### Canceling a Task<a name="SQLServer.Procedural.Importing.Native.Using.Cancel"></a>

To cancel a backup or restore task, you call the `rds_cancel_task` stored procedure\. 

The following parameters are optional: 
+ `@db_name` – The name of the database to cancel the task for\. 
+ `@task_id` – The ID of the task to cancel\. You can get the task ID by calling `rds_task_status`\. 

**Example**  

```
exec msdb.dbo.rds_cancel_task @task_id=1234;
```

### Tracking the Status of Tasks<a name="SQLServer.Procedural.Importing.Native.Using.Poll"></a>

To track the status of your backup and restore tasks, you call the `rds_task_status` stored procedure\. If you don't provide any parameters, the stored procedure returns the status of all tasks\. The status for tasks is updated approximately every 2 minutes\. 

The following parameters are optional: 
+ `@db_name` – The name of the database to show the task status for\. 
+ `@task_id` – The ID of the task to show the task status for\. 

**Example**  

```
exec msdb.dbo.rds_task_status @db_name='database_name';
```

The `rds_task_status` stored procedure returns the following columns\. 


****  

| Column | Description | 
| --- | --- | 
| `task_id` |  The ID of the task\.   | 
| `task_type` |  Either `BACKUP_DB` for a backup task, or `RESTORE_DB` for a restore task\.   | 
| `database_name` |  The name of the database that the task is associated with\.   | 
| `% complete` |  The progress of the task as a percentage\.   | 
| `duration (mins)` |  The amount of time spent on the task, in minutes\.   | 
| `lifecycle` |  The status of the task\. The possible statuses for a task are the following:  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/SQLServer.Procedural.Importing.html)  | 
| `task_info` |  Additional information about the task\.  If an error occurs while backing up or restoring a database, this column contains information about the error\. For a list of possible errors, and mitigation strategies, see [Troubleshooting](#SQLServer.Procedural.Importing.Native.Troubleshooting)\.   | 
| `last_updated` |  The date and time that the task status was last updated\. The status is updated after every 5% of progress\.   | 
| `created_at` |  The date and time that the task was created\.   | 
| `overwrite_S3_backup_file` |  The value of the `@overwrite_S3_backup_file` parameter specified when calling a backup task\. For more information, see [Backing Up a Database](#SQLServer.Procedural.Importing.Native.Using.Backup)\.   | 

## Compressing Backup Files<a name="SQLServer.Procedural.Importing.Native.Compression"></a>

To save space in your Amazon S3 bucket, you can compress your backup files\. For more information about compressing backup files, see [Backup Compression](https://msdn.microsoft.com/en-us/library/bb964719.aspx) in the Microsoft documentation\. 

Compressing your backup files is supported for the following database editions: 
+ Microsoft SQL Server Enterprise Edition 
+ Microsoft SQL Server Standard Edition 

To turn on compression for your backup files, run the following code: 

```
1. exec rdsadmin..rds_set_configuration 'S3 backup compression', 'true'; 
```

To turn off compression for your backup files, run the following code: 

```
1. exec rdsadmin..rds_set_configuration 'S3 backup compression', 'false'; 
```

## Troubleshooting<a name="SQLServer.Procedural.Importing.Native.Troubleshooting"></a>

The following are issues you might encounter when you use native backup and restore\. 


****  

| Issue | Troubleshooting Suggestions | 
| --- | --- | 
|  `Access Denied`   |  The backup or restore process is unable to access the backup file\. This is usually caused by issues like the following:  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/SQLServer.Procedural.Importing.html)  | 
|  `BACKUP DATABASE WITH COMPRESSION is not supported on <edition_name> Edition`   |  Compressing your backup files is only supported for Microsoft SQL Server Enterprise Edition and Standard Edition\.  For more information, see [Compressing Backup Files](#SQLServer.Procedural.Importing.Native.Compression)\.   | 
|  `Key <ARN> does not exist`   |  You attempted to restore an encrypted backup, but didn't provide a valid encryption key\. Check your encryption key and retry\.  For more information, see [Restoring a Database](#SQLServer.Procedural.Importing.Native.Using.Restore)\.   | 
|  `Please reissue task with correct type and overwrite property`   |  If you attempt to back up your database and provide the name of a file that already exists, but set the overwrite property to false, the save operation fails\. To fix this error, either provide the name of a file that doesn't already exist, or set the overwrite property to true\.  For more information, see [Backing Up a Database](#SQLServer.Procedural.Importing.Native.Using.Backup)\.  It's also possible that you intended to restore your database, but called the `rds_backup_database` stored procedure accidentally\. In that case, call the `rds_restore_database` stored procedure instead\.  For more information, see [Restoring a Database](#SQLServer.Procedural.Importing.Native.Using.Restore)\.  If you intended to restore your database and called the `rds_restore_database` stored procedure, make sure that you provided the name of a valid backup file\.  For more information, see [Using Native Backup and Restore](#SQLServer.Procedural.Importing.Native.Using)\.   | 
|  `Please specify a bucket that is in the same region as RDS instance`  |  You can't back up to, or restore from, an Amazon S3 bucket in a different AWS Region than your Amazon RDS DB instance\. You can use Amazon S3 replication to copy the backup file to the correct region\.  For more information, see [Cross\-Region Replication](https://docs.aws.amazon.com/AmazonS3/latest/dev/crr.html) in the Amazon S3 documentation\.   | 
|  `The specified bucket does not exist`   |  Verify that you have provided the correct ARN for your bucket and file, in the correct format\.  For more information, see [Using Native Backup and Restore](#SQLServer.Procedural.Importing.Native.Using)\.   | 
|  `User <ARN> is not authorized to perform <kms action> on resource <ARN>`   |  You requested an encrypted operation, but didn't provide correct AWS KMS permissions\. Verify that you have the correct permissions, or add them\.  For more information, see [Setting Up for Native Backup and Restore](#SQLServer.Procedural.Importing.Native.Enabling)\.   | 
| The Restore task is unable to restore from more than n backup file\(s\)\. Please reduce the number of files matched and try again\. | Reduce the number of files you're trying to restore from\. You can make each individual file larger if necessary\.  | 

## Related Topics<a name="SQLServer.Procedural.Importing.Native.Related"></a>
+ [Importing and Exporting SQL Server Data Using Other Methods](SQLServer.Procedural.Importing.Snapshots.md)
+ [Backing Up and Restoring Amazon RDS DB Instances](CHAP_CommonTasks.BackupRestore.md)