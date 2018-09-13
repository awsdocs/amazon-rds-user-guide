# Importing and Exporting SQL Server Databases<a name="SQLServer.Procedural.Importing"></a>

Amazon RDS supports native backup and restore for Microsoft SQL Server databases using full backup files \(\.bak files\)\. You can import and export SQL Server databases in a single, easily portable file\. You can create a full backup of your on\-premises database, store it on Amazon Simple Storage Service \(Amazon S3\), and then restore the backup file onto an existing Amazon RDS DB instance running SQL Server\. You can back up an Amazon RDS SQL Server database, store it on Amazon S3, and then restore the backup file onto an on\-premises server, or a different Amazon RDS DB instance running SQL Server\. 

The following diagram shows the supported scenarios\.

![\[Native Backup and Restore Architecture\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/SQL-bak-file.png)

Using \.bak files to back up and restore databases is heavily optimized, and is usually the fastest way to backup and restore databases\. There are many additional advantages to using native backup and restore\. You can do the following: 
+ Migrate databases to Amazon RDS\.
+ Move databases between Amazon RDS SQL Server DB instances\.
+ Import and export data\.
+ Migrate schemas, stored procedures, triggers and other database code\.
+ Backup and restore single databases, instead of entire DB instances\.
+ Create copies of databases for testing, training, and demonstrations\.
+ Store and transfer backup files into and out of Amazon RDS through Amazon S3, giving you an added layer of protection for disaster recovery\. 

Native backup and restore is available in all AWS Regions, and for both Single\-AZ and Multi\-AZ DB instances\. Native backup and restore is available for all editions of Microsoft SQL Server supported on Amazon RDS\. 

The following are some limitations to using native backup and restore: 
+ You can't back up to, or restore from, an Amazon S3 bucket in a different AWS Region than your Amazon RDS DB instance\. 
+ We strongly recommend that you don't restore a backup file from one time zone to a different time zone\. If you restore a backup file from one time zone to a different time zone, you must audit your queries and applications for the effects of the time zone change\.   
+ You can't restore a backup file to the same DB instance that was used to create the backup file\. Instead, restore the backup file to a new DB instance\. Renaming the database is not a workaround for this limitation\. 
+ You can't restore the same backup file to a DB instance multiple times\. That is, you can't restore a backup file to a DB instance that already contains the database that you are restoring\. Renaming the database is not a workaround for this limitation\. 
+ You can't back up databases larger than 1 TB in size\. 
+ You can't restore databases larger than 4 TB in size\. 
+ You can't back up a database during the maintenance window, or any time Amazon RDS is in the process of taking a snapshot of the database\. 
+ When you restore a backup file to a Multi\-AZ DB instance, mirroring is terminated and then reestablished\. Mirroring is terminated and reestablished for all databases on the DB instance, not just the one you are restoring\. While RDS reestablishes mirroring, your DB instance can't failover\. It can take 30 minutes or more to reestablish mirroring, depending on the size of the restore\. For more information, see [Multi\-AZ Deployments for Microsoft SQL Server with Database Mirroring](USER_SQLServerMultiAZ.md)\. 

We recommend that you use native backup and restore to migrate your database to Amazon RDS if your database can be offline while the backup file is created, copied, and restored\. If your on\-premises database can't be offline, we recommend that you use the AWS Database Migration Service to migrate your database to Amazon RDS\. For more information, see [ What Is AWS Database Migration Service? ](http://docs.aws.amazon.com/dms/latest/userguide/Welcome.html) 

Native backup and restore is not intended to replace the data recovery capabilities of the cross\-region snapshot copy feature\. We recommend that you use snapshot copy to copy your database snapshot to another region for cross\-region disaster recovery in Amazon RDS\. For more information, see [Copying a Snapshot](USER_CopySnapshot.md)\. 

## Setting Up for Native Backup and Restore<a name="SQLServer.Procedural.Importing.Native.Enabling"></a>

There are three components you'll need to set up for native backup and restore: 
+ An Amazon S3 bucket to store your backup files\.
+ An AWS Identity and Access Management \(IAM\) role to access the bucket\.
+ The `SQLSERVER_BACKUP_RESTORE` option added to an option group on your DB instance\.

If you already have an Amazon S3 bucket, you can use that\. If you don't have an Amazon S3 bucket, you can create a new one manually\. Alternatively, you can choose to have a new bucket created for you when you add the `SQLSERVER_BACKUP_RESTORE` option by using the AWS Management Console\. If you want to create a new bucket manually, see [ Creating a Bucket](http://docs.aws.amazon.com/AmazonS3/latest/user-guide/CreatingaBucket.html)\. 

If you already have an IAM role, you can use that\. If you don't have an IAM role, you can create a new one manually\. Alternatively, you can choose to have a new IAM role created for you when you add the `SQLSERVER_BACKUP_RESTORE` option by using the AWS Management Console\. If you want to create a new IAM role manually, or attach trust and permissions policies to an existing IAM role, take the approach discussed in the next section\. 

To enable native backup and restore on your DB instance, you add the `SQLSERVER_BACKUP_RESTORE` option to an option group on your DB instance\. For more information and instructions, see [Microsoft SQL Server Native Backup and Restore Support](Appendix.SQLServer.Options.BackupRestore.md)\. 

### Manually Creating an IAM Role for Native Backup and Restore<a name="SQLServer.Procedural.Importing.Native.Enabling.IAM"></a>

If you want to manually create a new IAM role to use with native backup and restore, you create a role to delegate permissions from the Amazon RDS service to your Amazon S3 bucket\. When you create an IAM role, you attach trust and permissions policies\. For the native backup and restore feature, use trust and permissions policies similar to the examples following\. For more information about creating the role, see [ Creating a Role to Delegate Permissions to an AWS Service](http://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-service.html)\. 

To use the trust and permissions policies, you provide an Amazon Resource Name \(ARN\)\. For more information about ARN formatting, see [ Amazon Resource Names \(ARNs\) and AWS Service Namespaces](http://docs.aws.amazon.com/general/latest/gr/aws-arns-and-namespaces.html)\. 

In the first example following, we use the service principle name `rds.amazon.aws.com` as an alias for all service accounts\. In the other examples, we specify an ARN to identify another account, user, or role that we're granting access to in the trust policy\. 

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
18.                 "s3:GetObjectMetaData",
19.                 "s3:GetObject",
20.                 "s3:PutObject",
21.                 "s3:ListMultipartUploadParts",
22.                 "s3:AbortMultipartUpload"
23.             ],
24.         "Resource": "arn:aws:s3:::bucket_name/*"
25.         }
26.     ]
27. }
```

**Example Permissions Policy for Native Backup and Restore with Encryption Support**  
If you want to encrypt your backup files, include an encryption key in your permissions policy\. For more information about encryption keys, see [ Getting Started](http://docs.aws.amazon.com/kms/latest/developerguide/getting-started.html) in the AWS Key Management Service \(AWS KMS\) documentation\.   

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
29.                 "s3:GetObjectMetaData",
30.                 "s3:GetObject",
31.                 "s3:PutObject",
32.                 "s3:ListMultipartUploadParts",
33.                 "s3:AbortMultipartUpload"
34.             ],
35.         "Resource": "arn:aws:s3:::bucket_name/*"
36.         }
37.     ]
38. }
```

## Using Native Backup and Restore<a name="SQLServer.Procedural.Importing.Native.Using"></a>

After you have enabled and configured native backup and restore, you can start using it\. First you connect to your Microsoft SQL Server database, and then call an Amazon RDS stored procedure to do the work\. For instructions on connecting to your database, see [Connecting to a DB Instance Running the Microsoft SQL Server Database Engine](USER_ConnectToMicrosoftSQLServerInstance.md)\. 

Some of the stored procedures require that you provide an Amazon Resource Name \(ARN\) to your Amazon S3 bucket and file\. The format for your ARN is `arn:aws:s3:::bucket_name/file_name`\. Amazon S3 doesn't require an account number or region in ARNs\. If you also provide an optional AWS KMS encryption key, the format your ARN is `arn:aws:kms:region:account-id:key/key-id`\. For more information, see [ Amazon Resource Names \(ARNs\) and AWS Service Namespaces](http://docs.aws.amazon.com/general/latest/gr/aws-arns-and-namespaces.html)\. 

There are stored procedures for backing up your database, restoring your database, canceling tasks that are in progress, and tracking the status of the backup and restore tasks\. For instructions on how to call each stored procedure, see the following subsections: 
+ [Backing Up a Database](#SQLServer.Procedural.Importing.Native.Using.Backup)
+ [Restoring a Database](#SQLServer.Procedural.Importing.Native.Using.Restore)
+ [Canceling a Task](#SQLServer.Procedural.Importing.Native.Using.Cancel)
+ [Tracking the Status of Tasks](#SQLServer.Procedural.Importing.Native.Using.Poll)

### Backing Up a Database<a name="SQLServer.Procedural.Importing.Native.Using.Backup"></a>

To back up your database, you call the `rds_backup_database` stored procedure\. 

**Note**  
You can't back up a database during the maintenance window, or when Amazon RDS is taking a snapshot\. 

**The following parameters are required: **
+ **`@source_db_name`** – The name of the database to back up
+ **`@s3_arn_to_backup_to`** – The bucket to use for the backup, plus the name of the file \(Amazon S3 bucket \+ key ARN\)\. 

  The file can have any extension, but `.bak` is traditional\. 

**The following parameters are optional: **
+ **`@kms_master_key_arn`** – The key to encrypt the backup \(KMS customer master key ARN\)\. 

  For more information about encryption keys, see [ Getting Started ](http://docs.aws.amazon.com/kms/latest/developerguide/getting-started.html) in the AWS Key Management Service \(AWS KMS\) documentation\. 
+ **`@overwrite_S3_backup_file`** – Defaults to `0`
  + `0` – Don't overwrite the existing file\. Return an error instead if the file already exists\. 
  + `1` – Overwrite an existing file that has the specified name, even if it isn't a backup file\. 
+ **`@type`** – Defaults to `FULL`, not case sensitive
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
    , backup_start_date
    , backup_finish_date 
    from    msdb.dbo.backupset 
    where   database_name='name-of-database'
    and     type = 'D' 
    order by backup_start_date desc;
```

### Restoring a Database<a name="SQLServer.Procedural.Importing.Native.Using.Restore"></a>

To restore your database, you call the `rds_restore_database` stored procedure\. 

The following parameters are required: 
+ **`@restore_db_name`** – The name of the database to restore\. 
+ **`@s3_arn_to_restore_from`** – The Amazon S3 bucket that contains the backup file, and the name of the file\. 

The following parameters are optional: 
+ **`@kms_master_key_arn`** – If you encrypted the backup file, the key to use to decrypt the file\. 

**Example Without Encryption**  

```
exec msdb.dbo.rds_restore_database 
        @restore_db_name='database_name', 
        @s3_arn_to_restore_from='arn:aws:s3:::bucket_name/file_name_and_extension';
```

**Example With Encryption**  

```
exec msdb.dbo.rds_restore_database 
        @restore_db_name='database_name', 
        @s3_arn_to_restore_from='arn:aws:s3:::bucket_name/file_name_and_extension',
        @kms_master_key_arn='arn:aws:kms:region:account-id:key/key-id';
```

### Canceling a Task<a name="SQLServer.Procedural.Importing.Native.Using.Cancel"></a>

To cancel a backup or restore task, you call the `rds_cancel_task` stored procedure\. 

The following parameters are optional: 
+ **`@db_name`** – The name of the database to cancel the task for\. 
+ **`@task_id`** – The ID of the task to cancel\. You can get the task ID by calling `rds_task_status`\. 

**Example**  

```
exec msdb.dbo.rds_cancel_task @task_id=1234;
```

### Tracking the Status of Tasks<a name="SQLServer.Procedural.Importing.Native.Using.Poll"></a>

To track the status of your backup and restore tasks, you call the `rds_task_status` stored procedure\. If you don't provide any parameters, the stored procedure returns the status of all tasks\. The status for tasks is updated approximately every 2 minutes\. 

The following parameters are optional: 
+ **`@db_name`** – The name of the database to show the task status for\. 
+ **`@task_id`** – The ID of the task to show the task status for\. 

**Example**  

```
exec msdb.dbo.rds_task_status @db_name='database_name';
```

The `rds_task_status` stored procedure returns the following columns\. 


****  

| Column | Description | 
| --- | --- | 
| `task_id` |  The ID of the task\.   | 
| `task_type` |  Either `BACKUP_DB` for a back up task, or `RESTORE_DB` for a restore task\.   | 
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

## Migrating to Amazon RDS by Using Native Backup and Restore<a name="SQLServer.Procedural.Importing.Native.Migrating"></a>

To migrate your database from your corporate data center to Amazon RDS, you follow the procedures in this topic\. However, you can perform the following steps to prepare: 

1. Create an Amazon S3 bucket\. For more information, see [ Creating a Bucket](http://docs.aws.amazon.com/AmazonS3/latest/user-guide/CreatingaBucket.html)\. 

1. Upload your database backup file to your Amazon S3 bucket\. For more information, see [ Uploading Objects into Amazon S3](http://docs.aws.amazon.com/AmazonS3/latest/user-guide/UploadingObjectsintoAmazonS3.html)\. 

## Troubleshooting<a name="SQLServer.Procedural.Importing.Native.Troubleshooting"></a>

The following are issues you might encounter when you use native backup and restore\. 


****  

| Issue | Troubleshooting Suggestions | 
| --- | --- | 
|  `Access Denied`   |  The backup or restore process is unable to access the backup file\. This is usually caused by issues like the following:  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/SQLServer.Procedural.Importing.html)  | 
|  `BACKUP DATABASE WITH COMPRESSION is not supported on <edition_name> Edition`   |  Compressing your backup files is only supported for Microsoft SQL Server Enterprise Edition and Standard Edition\.  For more information, see [Compressing Backup Files](#SQLServer.Procedural.Importing.Native.Compression)\.   | 
|  `Database <database_name> cannot be restored because there is already an existing database with the same family_guid on the instance`   |  You can't restore a backup file to the same DB instance that was used to create the backup file\. Instead, restore the backup file to a new DB instance\.  You also can't restore the same backup file to a DB instance multiple times\. That is, you can't restore a backup file to a DB instance that already contains the database that you are restoring\. Instead, restore the backup file to a new DB instance\.   | 
|  `Key <ARN> does not exist`   |  You attempted to restore an encrypted backup, but didn't provide a valid encryption key\. Check your encryption key and retry\.  For more information, see [Restoring a Database](#SQLServer.Procedural.Importing.Native.Using.Restore)\.   | 
|  `Please reissue task with correct type and overwrite property`   |  If you attempt to back up your database and provide the name of a file that already exists, but set the overwrite property to false, the save operation fails\. To fix this error, either provide the name of a file that doesn't already exist, or set the overwrite property to true\.  For more information, see [Backing Up a Database](#SQLServer.Procedural.Importing.Native.Using.Backup)\.  It's also possible that you intended to restore your database, but called the `rds_backup_database` stored procedure accidentally\. In that case, call the `rds_restore_database` stored procedure instead\.  For more information, see [Restoring a Database](#SQLServer.Procedural.Importing.Native.Using.Restore)\.  If you intended to restore your database and called the `rds_restore_database` stored procedure, make sure that you provided the name of a valid backup file\.  For more information, see [Using Native Backup and Restore](#SQLServer.Procedural.Importing.Native.Using)\.   | 
|  `Please specify a bucket that is in the same region as RDS instance`  |  You can't back up to, or restore from, an Amazon S3 bucket in a different AWS Region than your Amazon RDS DB instance\. You can use Amazon S3 replication to copy the backup file to the correct region\.  For more information, see [Cross\-Region Replication](http://docs.aws.amazon.com/AmazonS3/latest/dev/crr.html) in the Amazon S3 documentation\.   | 
|  `The specified bucket does not exist`   |  Verify that you have provided the correct ARN for your bucket and file, in the correct format\.  For more information, see [Using Native Backup and Restore](#SQLServer.Procedural.Importing.Native.Using)\.   | 
|  `User <ARN> is not authorized to perform <kms action> on resource <ARN>`   |  You requested an encrypted operation, but didn't provide correct AWS KMS permissions\. Verify that you have the correct permissions, or add them\.  For more information, see [Setting Up for Native Backup and Restore](#SQLServer.Procedural.Importing.Native.Enabling)\.   | 

## Related Topics<a name="SQLServer.Procedural.Importing.Native.Related"></a>
+ [Importing and Exporting SQL Server Data Using Other Methods](SQLServer.Procedural.Importing.Snapshots.md)
+ [Backing Up and Restoring Amazon RDS DB Instances](CHAP_CommonTasks.BackupRestore.md)