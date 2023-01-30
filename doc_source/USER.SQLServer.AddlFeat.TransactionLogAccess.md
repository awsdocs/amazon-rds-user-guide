# Access to transaction log backups with RDS for SQL Server<a name="USER.SQLServer.AddlFeat.TransactionLogAccess"></a>

With access to transaction log backups for RDS for SQL Server, you can list the transaction log backup files for a database and copy them to a target Amazon S3 bucket\. By copying transaction log backups in an Amazon S3 bucket, you can use them in combination with full and differential database backups to perform point in time database restores\. You use RDS stored procedures to set up access to transaction log backups, list available transaction log backups, and copy them to your Amazon S3 bucket\.

Access to transaction log backups provides the following capabilities and benefits:
+ List and view the metadata of available transaction log backups for a database on an RDS for SQL Server DB instance\.
+ Copy available transaction log backups from RDS for SQL Server to a target Amazon S3 bucket\.
+ Perform point\-in\-time restores of databases without the need to restore an entire DB instance\. For more information on restoring a DB instance to a point in time, see [Restoring a DB instance to a specified time](USER_PIT.md)\.

## Availability and support<a name="USER.SQLServer.AddlFeat.TransactionLogAccess.Availability"></a>

Access to transaction log backups is supported in all AWS Regions\. Access to transaction log backups is available for all editions and versions of Microsoft SQL Server supported on Amazon RDS\.  

## Requirements<a name="USER.SQLServer.AddlFeat.TransactionLogAccess.Requirements"></a>

The following requirements must be met before enabling access to transaction log backups: 
+  Automated backups must be enabled on the DB instance and the backup retention must be set to a value of one or more days\. For more information on enabling automated backups and configuring a retention policy, see [Enabling automated backups](USER_WorkingWithAutomatedBackups.md#USER_WorkingWithAutomatedBackups.Enabling)\. 
+ An Amazon S3 bucket must exist in the same account and Region as the source DB instance\. Before enabling access to transaction log backups, choose an existing Amazon S3 bucket or [create a new bucket](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/CreatingaBucket.html) to use for your transaction log backup files\.
+ An Amazon S3 bucket permissions policy must be configured as follows to allow Amazon RDS to copy transaction log files into it:

  1. Set the object account ownership property on the bucket to **Bucket Owner Preferred**\.

  1. Add the following policy\. There will be no policy by default, so use the bucket Access Control Lists \(ACL\) to edit the bucket policy and add it\.

  

  The following example uses an ARN to specify a resource\. We recommend using the `SourceArn` and `SourceAccount` global condition context keys in resource\-based trust relationships to limit the service's permissions to a specific resource\. For more information on working with ARNs, see [ Amazon resource names \(ARNs\)](https://docs.aws.amazon.com/general/latest/gr/aws-arns-and-namespaces.html) and [Working with Amazon Resource Names \(ARNs\) in Amazon RDS](USER_Tagging.ARN.md)\.

    
**Example of an Amazon S3 permissions policy for access to transaction log backups**  

  ```
   1.     {
   2.     "Version": "2012-10-17",
   3.     "Statement": [
   4.         {
   5.             "Sid": "Only allow writes to my bucket with bucket owner full control",
   6.             "Effect": "Allow",
   7.             "Principal": {
   8.                 "Service": "backups.rds.amazonaws.com"
   9.             },
  10.             "Action": "s3:PutObject",
  11.             "Resource": "arn:aws:s3:::{customer_bucket}/{customer_path}/*",
  12.             "Condition": {
  13.                 "StringEquals": {
  14.                     "s3:x-amz-acl": "bucket-owner-full-control",
  15.                     "aws:sourceAccount": "{customer_account}",
  16.                     "aws:sourceArn": "{db_instance_arn}"
  17.                 }
  18.             }
  19.         }
  20.     ]
  21. }
  ```
+ An AWS Identity and Access Management \(IAM\) role to access the Amazon S3 bucket\. If you already have an IAM role, you can use that\. You can choose to have a new IAM role created for you when you add the `SQLSERVER_BACKUP_RESTORE` option by using the AWS Management Console\. Alternatively, you can create a new one manually\. For more information on creating and configuring an IAM role with `SQLSERVER_BACKUP_RESTORE`, see [Manually creating an IAM role for native backup and restore](SQLServer.Procedural.Importing.md#SQLServer.Procedural.Importing.Native.Enabling.IAM)\.
+ The `SQLSERVER_BACKUP_RESTORE` option must be added to an option group on your DB instance\. For more information on adding the `SQLSERVER_BACKUP_RESTORE` option, see [Support for native backup and restore in SQL Server](Appendix.SQLServer.Options.BackupRestore.md)\.
**Note**  
If your DB instance has storage encryption enabled , the AWS KMS \(KMS\) actions and key must be provided in the IAM role provided in the native backup and restore option group\.

  Optionally, if you intend to use the `rds_restore_log` stored procedure to perform point in time database restores, we recommend using the same Amazon S3 path for the native backup and restore option group and access to transaction log backups\. This method ensures that when Amazon RDS assumes the role from the option group to perform the restore log functions, it has access to retrieve transaction log backups from the same Amazon S3 path\.

## Limitations and recommendations<a name="USER.SQLServer.AddlFeat.TransactionLogAccess.Limitations"></a>

Access to transaction log backups has the following limitations and recommendations:
+  You can list and copy up to the last seven days of transaction log backups for any DB instance that has backup retention configured between one to 35 days\. 
+  The Amazon S3 bucket used for access to transaction log backups must exist in the same account and Region as the source DB instance\. Cross\-account and cross\-region copy is not supported\. 
+  Only one Amazon S3 bucket can be configured as a target to copy transaction log backups into\. You can choose a new target Amazon S3 bucket with the `rds_tlog_copy_setup` stored procedure\. For more information on choosing a new target Amazon S3 bucket, see [Setting up access to transaction log backups](#USER.SQLServer.AddlFeat.TransactionLogAccess.Enabling)\.
+  You cannot specify the KMS key when using the `rds_tlog_backup_copy_to_S3` stored procedure if your RDS instance is not enabled for storage encryption\. 
+  Multi\-account copying is not supported\. The IAM role used for copying will only permit write access to Amazon S3 buckets within the owner account of the DB instance\. 
+  Only two concurrent tasks of any type may be run on an RDS for SQL Server DB instance\. 
+  Only one copy task can run for a single database at a given time\. If you want to copy transaction log backups for multiple databases on the DB instance, use a separate copy task for each database\. 
+  If you copy a transaction log backup that already exists with the same name in the Amazon S3 bucket, the existing transaction log backup will be overwritten\. 
+  You can only run the stored procedures that are provided with access to transaction log backups on the primary DB instance\. You can’t run these stored procedures on an RDS for SQL Server read replica or on a secondary instance of a Multi\-AZ DB cluster\. 
+  If the RDS for SQL Server DB instance is rebooted while the `rds_tlog_backup_copy_to_S3` stored procedure is running, the task will automatically restart from the beginning when the DB instance is back online\. Any transaction log backups that had been copied to the Amazon S3 bucket while the task was running before the reboot will be overwritten\. 
+ The Microsoft SQL Server system databases and the `RDSAdmin` database cannot be configured for access to transaction log backups\.

## Setting up access to transaction log backups<a name="USER.SQLServer.AddlFeat.TransactionLogAccess.Enabling"></a>

To set up access to transaction log backups, complete the list of requirements in the [Requirements](#USER.SQLServer.AddlFeat.TransactionLogAccess.Requirements) section, and then run the `rds_tlog_copy_setup` stored procedure\. The procedure will enable the access to transaction log backups feature at the DB instance level\. You don't need to run it for each individual database on the DB instance\. 

**Important**  
The database user must be granted the `db_owner` role within SQL Server on each database to configure and use the access to transaction log backups feature\.

**Example usage:**  

```
exec msdb.dbo.rds_tlog_copy_setup
@target_s3_arn='arn:aws:s3:::mybucket/myfolder';
```

The following parameter is required:
+ `@target_s3_arn` – The ARN of the target Amazon S3 bucket to copy transaction log backups files to\.

**Example of setting an Amazon S3 target bucket:**  

```
exec msdb.dbo.rds_tlog_copy_setup @target_s3_arn='arn:aws:s3:::accesstlogs-testbucket/mytestdb1';
```

To validate the configuration, call the `rds_show_configuration` stored procedure\.

**Example of validating the configuration:**  

```
exec rdsadmin.dbo.rds_show_configuration @name='target_s3_arn_for_tlog_copy';
```

To modify access to transaction log backups to point to a different Amazon S3 bucket, you can view the current Amazon S3 bucket value and re\-run the `rds_tlog_copy_setup` stored procedure using a new value for the `@target_s3_arn`\.

**Example of viewing the existing Amazon S3 bucket configured for access to transaction log backups**  

```
exec rdsadmin.dbo.rds_show_configuration @name='target_s3_arn_for_tlog_copy';
```

**Example of updating to a new target Amazon S3 bucket**  

```
exec msdb.dbo.rds_tlog_copy_setup @target_s3_arn='arn:aws:s3:::mynewbucket/mynewfolder';
```

## Listing available transaction log backups<a name="USER.SQLServer.AddlFeat.TransactionLogAccess.Listing"></a>

With RDS for SQL Server, databases configured to use the full recovery model and a DB instance backup retention set to one or more days have transaction log backups automatically enabled\. By enabling access to transaction log backups, up to seven days of those transaction log backups are made available for you to copy into your Amazon S3 bucket\.

After you have enabled access to transaction log backups, you can start using it to list and copy available transaction log backup files\.

**Listing transaction log backups**

To list all transaction log backups available for an individual database, call the `rds_fn_list_tlog_backup_metadata` function\. You can use an `ORDER BY` or a `WHERE` clause when calling the function\.

**Example of listing and filtering available transaction log backup files**  

```
SELECT * from msdb.dbo.rds_fn_list_tlog_backup_metadata('mydatabasename');
SELECT * from msdb.dbo.rds_fn_list_tlog_backup_metadata('mydatabasename') WHERE rds_backup_seq_id = 3507;
SELECT * from msdb.dbo.rds_fn_list_tlog_backup_metadata('mydatabasename') WHERE backup_file_time_utc > '2022-09-15 20:44:01' ORDER BY backup_file_time_utc DESC;
```

![\[Output from rds_fn_list_tlog_backup_metadata\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/sql_accesstransactionlogs_func.png)

The `rds_fn_list_tlog_backup_metadata` function returns the following output:


****  

| Column name | Data type | Description | 
| --- | --- | --- | 
| `db_name` | sysname | The database name provided to list the transaction log backups for\. | 
| `db_id` | int | The internal database identifier for the input parameter `db_name`\. | 
| `family_guid` | uniqueidentifier | The unique ID of the original database at creation\. This value remains the same when the database is restored, even to a different database name\. | 
| `rds_backup_seq_id` | int | The ID that RDS uses internally to maintain a sequence number for each transaction log backup file\. | 
| `backup_file_epoch` | bigint | The epoch time that a transaction backup file was generated\. | 
| `backup_file_time_utc` | datetime | The UTC time\-converted value for the `backup_file_epoch` value\. | 
| `starting_lsn` | numeric\(25,0\) | The log sequence number of the first or oldest log record of a transaction log backup file\. | 
| `ending_lsn` | numeric\(25,0\) | The log sequence number of the last or next log record of a transaction log backup file\. | 
| `is_log_chain_broken` | bit | A boolean value indicating if the log chain is broken between the current transaction log backup file and the previous transaction log backup file\. | 
| `file_size_bytes` | bigint | The size of the transactional backup set in bytes\. | 
| `Error` | varchar\(4000\) | Error message if the `rds_fn_list_tlog_backup_metadata` function throws an exception\. NULL if no exceptions\. | 

## Copying transaction log backups<a name="USER.SQLServer.AddlFeat.TransactionLogAccess.Copying"></a>

To copy a set of available transaction log backups for an individual database to your Amazon S3 bucket, call the `rds_tlog_backup_copy_to_S3` stored procedure\. The `rds_tlog_backup_copy_to_S3` stored procedure will initiate a new task to copy transaction log backups\. 

**Note**  
The `rds_tlog_backup_copy_to_S3` stored procedure will copy the transaction log backups without validating against `is_log_chain_broken` attribute\. For this reason, you should manually confirm an unbroken log chain before running the `rds_tlog_backup_copy_to_S3` stored procedure\. For further explanation, see [Validating the transaction log backup log chain](#USER.SQLServer.AddlFeat.TransactionLogAccess.Copying.LogChain)\.

**Example usage of the `rds_tlog_backup_copy_to_S3` stored procedure**  

```
exec msdb.dbo.rds_tlog_backup_copy_to_S3
	@db_name='mydatabasename',
	[@kms_key_arn='arn:aws:kms:region:account-id:key/key-id'],	
	[@backup_file_start_time='2022-09-01 01:00:15'],
	[@backup_file_end_time=''2022-09-01 21:30:45''],
	[@starting_lsn=149000000112100001],
	[@ending_lsn=149000000120400001],
	[@rds_backup_starting_seq_id=5],
	[@rds_backup_ending_seq_id=10];
```

The following input parameters are available:


****  

| Parameter | Description | 
| --- | --- | 
| `@db_name` | The name of the database to copy transaction log backups for | 
| `@kms_key_arn` | The ARN of the KMS key used to encrypt a storage\-encrypted DB instance\. | 
| `@backup_file_start_time` | The UTC timestamp as provided from the `[backup_file_time_utc]` column of the `rds_fn_list_tlog_backup_metadata` function\. | 
| `@backup_file_end_time` | The UTC timestamp as provided from the `[backup_file_time_utc]` column of the `rds_fn_list_tlog_backup_metadata` function\. | 
| `@starting_lsn` | The log sequence number \(LSN\) as provided from the `[starting_lsn]` column of the `rds_fn_list_tlog_backup_metadata` function | 
| `@ending_lsn` | The log sequence number \(LSN\) as provided from the `[ending_lsn]` column of the `rds_fn_list_tlog_backup_metadata` function\. | 
| `@rds_backup_starting_seq_id` | The sequence ID as provided from the `[rds_backup_seq_id]` column of the `rds_fn_list_tlog_backup_metadata` function\. | 
| `@rds_backup_ending_seq_id` | The sequence ID as provided from the `[rds_backup_seq_id]` column of the `rds_fn_list_tlog_backup_metadata` function\. | 

You can specify a set of either the time, LSN, or sequence ID parameters\. Only one set of parameters are required\.

You can also specify just a single parameter in any of the sets\. For example, by providing a value for only the `backup_file_end_time` parameter, all available transaction log backup files prior to that time within the seven\-day limit will be copied to your Amazon S3 bucket\. 

Following are the valid input parameter combinations for the `rds_tlog_backup_copy_to_S3` stored procedure\.


****  

| Parameters provided | Expected result | 
| --- | --- | 
|  <pre>exec msdb.dbo.rds_tlog_backup_copy_to_S3  <br />	@db_name = 'testdb1',<br />            @backup_file_start_time='2022-08-23 00:00:00',<br />            @backup_file_end_time='2022-08-30 00:00:00';</pre>  | Copies transaction log backups from the last seven days and exist between the provided range of `backup_file_start_time` and `backup_file_end_time`\. In this example, the stored procedure will copy transaction log backups that were generated between '2022\-08\-23 00:00:00' and '2022\-08\-30 00:00:00'\.  | 
|  <pre>exec msdb.dbo.rds_tlog_backup_copy_to_S3<br />           @db_name = 'testdb1',<br />           @backup_file_start_time='2022-08-23 00:00:00';</pre>  | Copies transaction log backups from the last seven days and starting from the provided `backup_file_start_time`\. In this example, the stored procedure will copy transaction log backups from '2022\-08\-23 00:00:00' up to the latest transaction log backup\.  | 
|  <pre>exec msdb.dbo.rds_tlog_backup_copy_to_S3<br />          @db_name = 'testdb1',<br />          @backup_file_end_time='2022-08-30 00:00:00';</pre>  | Copies transaction log backups from the last seven days up to the provided `backup_file_end_time`\. In this example, the stored procedure will copy transaction log backups from '2022\-08\-23 00:00:00 up to '2022\-08\-30 00:00:00'\.  | 
|  <pre>exec msdb.dbo.rds_tlog_backup_copy_to_S3<br />         @db_name='testdb1',<br />         @starting_lsn =1490000000040007,<br />         @ending_lsn =  1490000000050009;</pre>  | Copies transaction log backups that are available from the last seven days and are between the provided range of the `starting_lsn` and `ending_lsn`\. In this example, the stored procedure will copy transaction log backups from the last seven days with an LSN range between 1490000000040007 and 1490000000050009\.   | 
|  <pre>exec msdb.dbo.rds_tlog_backup_copy_to_S3<br />        @db_name='testdb1',<br />        @starting_lsn =1490000000040007;</pre>  |  Copies transaction log backups that are available from the last seven days, beginning from the provided `starting_lsn`\. In this example, the stored procedure will copy transaction log backups from LSN 1490000000040007 up to the latest transaction log backup\.   | 
|  <pre>exec msdb.dbo.rds_tlog_backup_copy_to_S3<br />        @db_name='testdb1',<br />        @ending_lsn  =  1490000000050009;</pre>  |  Copies transaction log backups that are available from the last seven days, up to the provided `ending_lsn`\. In this example, the stored procedure will copy transaction log backups beginning from the last seven days up to lsn 1490000000050009\.   | 
|  <pre>exec msdb.dbo.rds_tlog_backup_copy_to_S3<br />       @db_name='testdb1',<br />       @rds_backup_starting_seq_id= 2000,<br />       @rds_backup_ending_seq_id= 5000;</pre>  |  Copies transaction log backups that are available from the last seven days, and exist between the provided range of `rds_backup_starting_seq_id` and `rds_backup_ending_seq_id`\. In this example, the stored procedure will copy transaction log backups beginning from the last seven days and within the provided rds backup sequence id range, starting from seq\_id 2000 up to seq\_id 5000\.   | 
|  <pre>exec msdb.dbo.rds_tlog_backup_copy_to_S3<br />       @db_name='testdb1',<br />       @rds_backup_starting_seq_id= 2000;</pre>  |  Copies transaction log backups that are available from the last seven days, beginning from the provided `rds_backup_starting_seq_id`\. In this example, the stored procedure will copy transaction log backups beginning from seq\_id 2000, up to the latest transaction log backup\.   | 
|  <pre>exec msdb.dbo.rds_tlog_backup_copy_to_S3<br />      @db_name='testdb1',<br />      @rds_backup_ending_seq_id= 5000;</pre>  |  Copies transaction log backups that are available from the last seven days, up to the provided `rds_backup_ending_seq_id`\. In this example, the stored procedure will copy transaction log backups beginning from the last seven days, up to seq\_id 5000\.   | 
|  <pre>exec msdb.dbo.rds_tlog_backup_copy_to_S3<br />      @db_name='testdb1',<br />      @rds_backup_starting_seq_id= 2000;<br />      @rds_backup_ending_seq_id= 2000;</pre>  |  Copies a single transaction log backup with the provided `rds_backup_starting_seq_id`, if available within the last seven days\. In this example, the stored procedure will copy a single transaction log backup that has a seq\_id of 2000, if it exists within the last seven days\.   | 

### Validating the transaction log backup log chain<a name="USER.SQLServer.AddlFeat.TransactionLogAccess.Copying.LogChain"></a>

 Databases configured for access to transaction log backups must have automated backup retention enabled\. Automated backup retention sets the databases on the DB instance to the `FULL` recovery model\. To support point in time restore for a database, avoid changing the database recovery model, which can result in a broken log chain\. We recommend keeping the database set to the `FULL` recovery model\.

To manually validate the log chain before copying transaction log backups, call the `rds_fn_list_tlog_backup_metadata` function and review the values in the `is_log_chain_broken` column\. A value of "1" indicates the log chain was broken between the current log backup and the previous log backup\.

The following example shows a broken log chain in the output from the `rds_fn_list_tlog_backup_metadata` stored procedure\. 

![\[Output from rds_fn_list_tlog_backup_metadata showing a broken log chain.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/sql_accesstransactionlogs_logchain_error.png)

In a normal log chain, the log sequence number \(LSN\) value for first\_lsn for given rds\_sequence\_id should match the value of last\_lsn in the preceding rds\_sequence\_id\. In the image, the rds\_sequence\_id of 45 has a first\_lsn value 90987, which does not match the last\_lsn value of 90985 for preceeding rds\_sequence\_id 44\.

For more information about SQL Server transaction log architecture and log sequence numbers, see [Transaction Log Logical Architecture](https://learn.microsoft.com/en-us/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide?view=sql-server-ver15#Logical_Arch) in the Microsoft SQL Server documentation\.

## Amazon S3 bucket folder and file structure<a name="USER.SQLServer.AddlFeat.TransactionLogAccess.S3namingConvention"></a>

Transaction log backups have the following standard structure and naming convention within an Amazon S3 bucket:
+ A new folder is created under the `target_s3_arn` path for each database with the naming structure as `{db_id}.{family_guid}`\.
+ Within the folder, transaction log backups have a filename structure as `{db_id}.{family_guid}.{rds_backup_seq_id}.{backup_file_epoch}`\.
+ You can view the details of `family_guid,db_id,rds_backup_seq_id and backup_file_epoch` with the `rds_fn_list_tlog_backup_metadata`function\.

The following example shows the folder and file structure of a set of transaction log backups within an Amazon S3 bucket\.

![\[Amazon S3 bucket structure with access to transaction logs\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/sql_accesstransactionlogs_s3.png)

## Tracking the status of tasks<a name="USER.SQLServer.AddlFeat.TransactionLogAccess.TrackTaskStatus"></a>

 To track the status of your copy tasks, call the `rds_task_status` stored procedure\. If you don't provide any parameters, the stored procedure returns the status of all tasks\. 

**Example usage:**  

```
exec msdb.dbo.rds_task_status
  @db_name='database_name',
  @task_id=ID_number;
```

The following parameters are optional:
+ `@db_name` – The name of the database to show the task status for\.
+ `@task_id` – The ID of the task to show the task status for\.

**Example of listing the status for a specific task ID:**  

```
exec msdb.dbo.rds_task_status @task_id=5;
```

**Example of listing the status for a specific database and task:**  

```
exec msdb.dbo.rds_task_status@db_name='my_database',@task_id=5;
```

**Example of listing all tasks and their status for a specific database:**  

```
exec msdb.dbo.rds_task_status @db_name='my_database';
```

**Example of listing all tasks and their status on the current DB instance:**  

```
exec msdb.dbo.rds_task_status;
```

## Canceling a task<a name="USER.SQLServer.AddlFeat.TransactionLogAccess.CancelTask"></a>

To cancel a running task, call the `rds_cancel_task` stored procedure\.

**Example usage:**  

```
exec msdb.dbo.rds_cancel_task @task_id=ID_number;
```

The following parameter is required:
+ `@task_id` – The ID of the task to cancel\. You can view the task ID by calling the `rds_task_status` stored procedure\.

For more information on viewing and canceling running tasks, see [Importing and exporting SQL Server databases using native backup and restore](SQLServer.Procedural.Importing.md)\.

## Troubleshooting access to transaction log backups<a name="USER.SQLServer.AddlFeat.TransactionLogAccess.Troubleshooting"></a>

The following are issues you might encounter when you use the stored procedures for access to transaction log backups\.


****  

| Stored Procedure | Error Message | Issue | Troubleshooting suggestions | 
| --- | --- | --- | --- | 
| rds\_tlog\_copy\_setup | Backups are disabled on this DB instance\. Enable DB instance backups with a retention of at least "1" and try again\. | Automated backups are not enabled for the DB instance\. |  DB instance backup retention must be enabled with a retention of at least one day\. For more information on enabling automated backups and configuring backup retention, see [Backup retention period](USER_WorkingWithAutomatedBackups.md#USER_WorkingWithAutomatedBackups.BackupRetention)\.  | 
| rds\_tlog\_copy\_setup | Error running the rds\_tlog\_copy\_setup stored procedure\. Reconnect to the RDS endpoint and try again\. | An internal error occurred\. | Reconnect to the RDS endpoint and run the `rds_tlog_copy_setup` stored procedure again\. | 
| rds\_tlog\_copy\_setup | Running the rds\_tlog\_backup\_copy\_setup stored procedure inside a transaction is not supported\. Verify that the session has no open transactions and try again\.  | The stored procedure was attempted within a transaction using `BEGIN` and `END`\. | Avoid using `BEGIN` and `END` when running the `rds_tlog_copy_setup` stored procedure\. | 
| rds\_tlog\_copy\_setup | The S3 bucket name for the input parameter `@target_s3_arn` should contain at least one character other than a space\.  | An incorrect value was provided for the input parameter `@target_s3_arn`\. | Ensure the input parameter `@target_s3_arn` specifies the complete Amazon S3 bucket ARN\. | 
| rds\_tlog\_copy\_setup | The `SQLSERVER_BACKUP_RESTORE` option isn't enabled or is in the process of being enabled\. Enable the option or try again later\.  | The `SQLSERVER_BACKUP_RESTORE` option is not enabled on the DB instance or was just enabled and pending internal activation\. | Enable the `SQLSERVER_BACKUP_RESTORE` option as specified in the Requirements section\. Wait a few minutes and run the `rds_tlog_copy_setup` stored procedure again\. | 
| rds\_tlog\_copy\_setup | The target S3 arn for the input parameter `@target_s3_arn` can't be empty or null\.  | An `NULL` value was provided for the input parameter `@target_s3_arn`, or the value wasn't provided\. | Ensure the input parameter `@target_s3_arn` specifies the complete Amazon S3 bucket ARN\. | 
| rds\_tlog\_copy\_setup | The target S3 arn for the input parameter `@target_s3_arn` must begin with arn:aws\.  | The input parameter `@target_s3_arn` was provide without `arn:aws` on the front\. | Ensure the input parameter `@target_s3_arn` specifies the complete Amazon S3 bucket ARN\. | 
| rds\_tlog\_copy\_setup | The target S3 ARN is already set to the provided value\.  | The `rds_tlog_copy_setup` stored procedure previously ran and was configured with an Amazon S3 bucket ARN\. | To modify the Amazon S3 bucket value for access to transaction log backups, provide a different `target S3 ARN`\. | 
| rds\_tlog\_copy\_setup | Unable to generate credentials for enabling Access to Transaction Log Backups\. Confirm the S3 path ARN provided with `rds_tlog_copy_setup`, and try again later\.  | There was an unspecified error while generating credentials to enable access to transaction log backups\. | Review your setup configuration and try again\.  | 
| rds\_tlog\_copy\_setup | You cannot run the rds\_tlog\_copy\_setup stored procedure while there are pending tasks\. Wait for the pending tasks to complete and try again\.  | Only two tasks may run at any time\. There are pending tasks awaiting completion\. | View pending tasks and wait for them to complete\. For more information on monitoring task status, see [Tracking the status of tasks](#USER.SQLServer.AddlFeat.TransactionLogAccess.TrackTaskStatus)\.  | 
| rds\_tlog\_backup\_copy\_to\_S3 | A T\-log backup file copy task has already been issued for database: %s with task Id: %d, please try again later\.  | Only one copy task may run at any time for a given database\. There is a pending copy task awaiting completion\. | View pending tasks and wait for them to complete\. For more information on monitoring task status, see [Tracking the status of tasks](#USER.SQLServer.AddlFeat.TransactionLogAccess.TrackTaskStatus)\.  | 
| rds\_tlog\_backup\_copy\_to\_S3 | At least one of these three parameter sets must be provided\. SET\-1:\(@backup\_file\_start\_time, @backup\_file\_end\_time\) \| SET\-2:\(@starting\_lsn, @ending\_lsn\) \| SET\-3:\(@rds\_backup\_starting\_seq\_id, @rds\_backup\_ending\_seq\_id\)  | None of the three parameter sets were provided, or a provided parameter set is missing a required parameter\. | You can specify either the time, lsn, or sequence ID parameters\. One set from these three sets of parameters are required\. For more information on required parameters, see [Copying transaction log backups](#USER.SQLServer.AddlFeat.TransactionLogAccess.Copying)\. | 
| rds\_tlog\_backup\_copy\_to\_S3 | Backups are disabled on your instance\. Please enable backups and try again in some time\. | Automated backups are not enabled for the DB instance\. |  For more information on enabling automated backups and configuring backup retention, see [Backup retention period](USER_WorkingWithAutomatedBackups.md#USER_WorkingWithAutomatedBackups.BackupRetention)\.  | 
| rds\_tlog\_backup\_copy\_to\_S3 | Cannot find the given database %s\.  | The value provided for input parameter `@db_name` does not match a database name on the DB instance\. | Use the correct database name\. To list all databases by name, run `SELECT * from sys.databases` | 
| rds\_tlog\_backup\_copy\_to\_S3 | Cannot run the rds\_tlog\_backup\_copy\_to\_S3 stored procedure for SQL Server system databases or the rdsadmin database\.  | The value provided for input parameter `@db_name` matches a SQL Server system database name or the RDSAdmin database\. | The following databases are not allowed to be used with access to transaction log backups: `master, model, msdb, tempdb, RDSAdmin.`  | 
| rds\_tlog\_backup\_copy\_to\_S3 | Database name for the input parameter @db\_name can't be empty or null\.  | The value provided for input parameter `@db_name` was empty or `NULL`\. | Use the correct database name\. To list all databases by name, run `SELECT * from sys.databases` | 
| rds\_tlog\_backup\_copy\_to\_S3 | DB instance backup retention period must be set to at least 1 to run the rds\_tlog\_backup\_copy\_setup stored procedure\.  | Automated backups are not enabled for the DB instance\. | For more information on enabling automated backups and configuring backup retention, see [Backup retention period](USER_WorkingWithAutomatedBackups.md#USER_WorkingWithAutomatedBackups.BackupRetention)\. | 
| rds\_tlog\_backup\_copy\_to\_S3 | Error running the stored procedure rds\_tlog\_backup\_copy\_to\_S3\. Reconnect to the RDS endpoint and try again\.  | An internal error occurred\. | Reconnect to the RDS endpoint and run the `rds_tlog_backup_copy_to_S3` stored procedure again\. | 
| rds\_tlog\_backup\_copy\_to\_S3 | Only one of these three parameter sets can be provided\. SET\-1:\(@backup\_file\_start\_time, @backup\_file\_end\_time\) \| SET\-2:\(@starting\_lsn, @ending\_lsn\) \| SET\-3:\(@rds\_backup\_starting\_seq\_id, @rds\_backup\_ending\_seq\_id\)  | Multiple parameter sets were provided\. | You can specify either the time, lsn, or sequence ID parameters\. One set from these three sets of parameters are required\. For more information on required parameters, see [Copying transaction log backups](#USER.SQLServer.AddlFeat.TransactionLogAccess.Copying)\.  | 
| rds\_tlog\_backup\_copy\_to\_S3 | Running the rds\_tlog\_backup\_copy\_to\_S3 stored procedure inside a transaction is not supported\. Verify that the session has no open transactions and try again\.  | The stored procedure was attempted within a transaction using `BEGIN` and `END`\. | Avoid using `BEGIN` and `END` when running the `rds_tlog_backup_copy_to_S3` stored procedure\. | 
| rds\_tlog\_backup\_copy\_to\_S3 | The provided parameters fall outside of the transaction backup log retention period\. To list of available transaction log backup files, run the rds\_fn\_list\_tlog\_backup\_metadata function\.  | There are no available transactional log backups for the provided input parameters that fit in the copy retention window\. | Try again with a valid set of parameters\. For more information on required parameters, see [Copying transaction log backups](#USER.SQLServer.AddlFeat.TransactionLogAccess.Copying)\. | 
| rds\_tlog\_backup\_copy\_to\_S3 | There was a permissions error in processing the request\. Ensure the bucket is in the same Account and Region as the DB Instance, and confirm the S3 bucket policy permissions against the template in the public documentation\.  | There was an issue detected with the provided S3 bucket or its policy permissions\. | Confirm your setup for access to transaction log backups is correct\. For more information on setup requirements for your S3 bucket, see [Requirements](#USER.SQLServer.AddlFeat.TransactionLogAccess.Requirements)\. | 
| rds\_tlog\_backup\_copy\_to\_S3 | Running the `rds_tlog_backup_copy_to_S3` stored procedure on an RDS read replica instance isn't permitted\.  | The stored procedure was attempted on a RDS read replica instance\. | Connect to the RDS primary DB instance to run the `rds_tlog_backup_copy_to_S3` stored procedure\. | 
| rds\_tlog\_backup\_copy\_to\_S3 | The LSN for the input parameter `@starting_lsn` must be less than `@ending_lsn`\.  | The value provided for input parameter `@starting_lsn` was greater than the value provided for input parameter `@ending_lsn`\. | Ensure the value provided for input parameter `@starting_lsn` is less than the value provided for input parameter `@ending_lsn`\. | 
| rds\_tlog\_backup\_copy\_to\_S3 | The `rds_tlog_backup_copy_to_S3` stored procedure can only be performed by the members of `db_owner` role in the source database\.  | The `db_owner` role has not been granted for the account attempting to run the `rds_tlog_backup_copy_to_S3` stored procedure on the provided `db_name`\. | Ensure the account running the stored procedure is permissioned with the `db_owner` role for the provided `db_name`\. | 
| rds\_tlog\_backup\_copy\_to\_S3 | The sequence ID for the input parameter `@rds_backup_starting_seq_id` must be less than or equal to `@rds_backup_ending_seq_id`\.  | The value provided for input parameter `@rds_backup_starting_seq_id` was greater than the value provided for input parameter `@rds_backup_ending_seq_id`\. | Ensure the value provided for input parameter `@rds_backup_starting_seq_id` is less than the value provided for input parameter `@rds_backup_ending_seq_id`\. | 
| rds\_tlog\_backup\_copy\_to\_S3 | The SQLSERVER\_BACKUP\_RESTORE option isn't enabled or is in the process of being enabled\. Enable the option or try again later\.  | The `SQLSERVER_BACKUP_RESTORE` option is not enabled on the DB instance or was just enabled and pending internal activation\. | Enable the `SQLSERVER_BACKUP_RESTORE` option as specified in the Requirements section\. Wait a few minutes and run the `rds_tlog_backup_copy_to_S3` stored procedure again\. | 
| rds\_tlog\_backup\_copy\_to\_S3 | The start time for the input parameter `@backup_file_start_time` must be less than `@backup_file_end_time`\.  | The value provided for input parameter `@backup_file_start_time` was greater than the value provided for input parameter `@backup_file_end_time`\. | Ensure the value provided for input parameter `@backup_file_start_time` is less than the value provided for input parameter `@backup_file_end_time`\. | 
| rds\_tlog\_backup\_copy\_to\_S3 | We were unable to process the request due to a lack of access\. Please check your setup and permissions for the feature\.  | There may be an issue with the Amazon S3 bucket permissions, or the Amazon S3 bucket provided is in another account or Region\. | Ensure the Amazon S3 bucket policy permissions are permissioned to allow RDS access\. Ensure the Amazon S3 bucket is in the same account and Region as the DB instance\. | 
| rds\_tlog\_backup\_copy\_to\_S3 | You cannot provide a KMS Key ARN as input parameter to the stored procedure for instances that are not storage\-encrypted\.  | When storage encryption is not enabled on the DB instance, the input parameter `@kms_key_arn` should not be provided\. | Do not provide an input parameter for `@kms_key_arn`\. | 
| rds\_tlog\_backup\_copy\_to\_S3 | You must provide a KMS Key ARN as input parameter to the stored procedure for storage encrypted instances\.  | When storage encryption is enabled on the DB instance, the input parameter `@kms_key_arn` must be provided\. | Provide an input parameter for `@kms_key_arn` with a value that matches the ARN of the Amazon S3 bucket to use for transaction log backups\. | 
| rds\_tlog\_backup\_copy\_to\_S3 | You must run the `rds_tlog_copy_setup` stored procedure and set the `@target_s3_arn`, before running the `rds_tlog_backup_copy_to_S3` stored procedure\.  | The access to transaction log backups setup procedure was not completed before attempting to run the `rds_tlog_backup_copy_to_S3` stored procedure\. | Run the `rds_tlog_copy_setup` stored procedure before running the `rds_tlog_backup_copy_to_S3` stored procedure\. For more information on running the setup procedure for access to transaction log backups, see [Setting up access to transaction log backups](#USER.SQLServer.AddlFeat.TransactionLogAccess.Enabling)\.  | 