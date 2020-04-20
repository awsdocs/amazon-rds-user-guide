# Support for Native Backup and Restore in SQL Server<a name="Appendix.SQLServer.Options.BackupRestore"></a>

By using native backup and restore for SQL Server databases, you can create a differential or full backup of your on\-premises database and store the backup files on Amazon S3\. You can then restore to an existing Amazon RDS DB instance running SQL Server\. You can also back up an RDS SQL Server database, store it on Amazon S3, and restore it in other locations\. In addition, you can restore the backup to an on\-premises server, or a different Amazon RDS DB instance running SQL Server\. For more information, see [Importing and Exporting SQL Server Databases](SQLServer.Procedural.Importing.md)\.

Amazon RDS supports native backup and restore for Microsoft SQL Server databases by using differential and full backup files \(\.bak files\)\.

## Adding the Native Backup and Restore Option<a name="Appendix.SQLServer.Options.BackupRestore.Add"></a>

The general process for adding the native backup and restore option to a DB instance is the following:

1. Create a new option group, or copy or modify an existing option group\.

1. Add the `SQLSERVER_BACKUP_RESTORE` option to the option group\.

1. Associate an AWS Identity and Access Management \(IAM\) role with the option\. The IAM role must have access to an S3 bucket to store the database backups\.

   That is, it must have as its option setting a valid Amazon Resource Name \(ARN\) in the format `arn:aws:iam::account-id:role/role-name`\. For more information, see [Amazon Resource Names \(ARNs\)](https://docs.aws.amazon.com/general/latest/gr/aws-arns-and-namespaces.html#arn-syntax-iam) in the *AWS General Reference\.*

1. Associate the option group with the DB instance\.

After you add the native backup and restore option, you don't need to restart your DB instance\. As soon as the option group is active, you can begin backing up and restoring immediately\.

### Console<a name="Add.Native.Backup.Restore.Console"></a>

**To add the native backup and restore option**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Option groups**\.

1. Create a new option group or use an existing option group\. For information on how to create a custom DB option group, see [Creating an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.Create)\.

   To use an existing option group, skip to the next step\.

1. Add the **SQLSERVER\_BACKUP\_RESTORE** option to the option group\. For more information about adding options, see [Adding an Option to an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.AddOption)\.

1. Do one of the following:
   + To use an existing IAM role and Amazon S3 settings, choose an existing IAM role for **IAM Role**\. If you use an existing IAM role, RDS uses the Amazon S3 settings configured for this role\.
   + To create a new role and configure new Amazon S3 settings, do the following: 

     1. For **IAM Role**, choose **Create a New Role**\.

     1. For **Select S3 Bucket**, either create an S3 bucket or use an existing one\. To create a new bucket, choose **Create a New S3 Bucket**\. To use an existing bucket, choose it from the list\.

     1. For **S3 folder path prefix \(optional\)**, specify a prefix to use for the files stored in your Amazon S3 bucket\. 

        This prefix can include a file path but doesn't have to\. If you provide a prefix, RDS attaches that prefix to all backup files\. RDS then uses the prefix during a restore to identify related files and ignore irrelevant files\. For example, you might use the S3 bucket for purposes besides holding backup files\. In this case, you can use the prefix to have RDS perform native backup and restore only on a particular folder and its subfolders\.

        If you leave the prefix blank, then RDS doesn't use a prefix to identify backup files or files to restore\. As a result, during a multiple\-file restore, RDS attempts to restore every file in every folder of the S3 bucket\.

     1. For **Enable Encryption**, choose **Yes** to encrypt the backup file\. Choose **No** to leave the backup file unencrypted\.

        If you choose **Yes**, choose an encryption key for **Master Key**\. For more information about encryption keys, see [Getting Started](https://docs.aws.amazon.com/kms/latest/developerguide/getting-started.html) in the *AWS Key Management Service Developer Guide\.*

1. Choose **Add option**\.

1. Apply the option group to a new or existing DB instance:
   + For a new DB instance, apply the option group when you launch the instance\. For more information, see [Creating an Amazon RDS DB Instance](USER_CreateDBInstance.md)\. 
   + For an existing DB instance, apply the option group by modifying the instance and attaching the new option group\. For more information, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\. 

### CLI<a name="Add.Native.Backup.Restore.CLI"></a>

This procedure makes the following assumptions:
+ You're adding the SQLSERVER\_BACKUP\_RESTORE option to an option group that already exists\. For more information about adding options, see [Adding an Option to an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.AddOption)\.
+ You're associating the option with an IAM role that already exists and has access to an S3 bucket to store the backups\.
+ You're applying the option group to a DB instance that already exists\. For more information, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\. 

**To add the native backup and restore option**

1. Add the `SQLSERVER_BACKUP_RESTORE` option to the option group\.  
**Example**  

   For Linux, macOS, or Unix:

   ```
   aws rds add-option-to-option-group \
   	--apply-immediately \
   	--option-group-name mybackupgroup \
   	--options "OptionName=SQLSERVER_BACKUP_RESTORE, \
   	  OptionSettings=[{Name=IAM_ROLE_ARN,Value=arn:aws:iam::account-id:role/role-name}]"
   ```

   For Windows:

   ```
   aws rds add-option-to-option-group ^
   	--option-group-name mybackupgroup ^
   	--options "[{\"OptionName\": \"SQLSERVER_BACKUP_RESTORE\", ^
   	\"OptionSettings\": [{\"Name\": \"IAM_ROLE_ARN\", ^
   	\"Value\": \"arn:aws:iam::account-id:role/role-name"}]}]" ^
   	--apply-immediately
   ```
**Note**  
When using the Windows command prompt, you must escape double quotes \("\) in JSON code by prefixing them with a backslash \(\\\)\.

1. Apply the option group to the DB instance\.  
**Example**  

   For Linux, macOS, or Unix:

   ```
   aws rds modify-db-instance \
   	--db-instance-identifier mydbinstance \
   	--option-group-name mybackupgroup \
   	--apply-immediately
   ```

   For Windows:

   ```
   aws rds modify-db-instance ^
   	--db-instance-identifier mydbinstance ^
   	--option-group-name mybackupgroup ^
   	--apply-immediately
   ```

## Modifying Native Backup and Restore Option Settings<a name="Appendix.SQLServer.Options.BackupRestore.ModifySettings"></a>

After you enable the native backup and restore option, you can modify the settings for the option\. For more information about how to modify option settings, see [Modifying an Option Setting](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.ModifyOption)\.

## Removing the Native Backup and Restore Option<a name="Appendix.SQLServer.Options.BackupRestore.Remove"></a>

You can turn off native backup and restore by removing the option from your DB instance\. After you remove the native backup and restore option, you don't need to restart your DB instance\. 

To remove the native backup and restore option from a DB instance, do one of the following: 
+ Remove the option from the option group it belongs to\. This change affects all DB instances that use the option group\. For more information, see [Removing an Option from an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.RemoveOption)\. 
+ Modify the DB instance and specify a different option group that doesn't include the native backup and restore option\. This change affects a single DB instance\. You can specify the default \(empty\) option group, or a different custom option group\. For more information, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\. 