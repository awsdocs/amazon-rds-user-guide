# Microsoft SQL Server Native Backup and Restore Support<a name="Appendix.SQLServer.Options.BackupRestore"></a>

Amazon RDS supports native backup and restore for Microsoft SQL Server databases using full backup files \(\.bak files\)\. You can import and export SQL Server databases in a single, easily portable file\. You can create a full backup of your on\-premises database, store it on Amazon Simple Storage Service \(Amazon S3\), and then restore the backup file onto an existing Amazon RDS DB instance running SQL Server\. You can back up an Amazon RDS SQL Server database, store it on Amazon S3, and then restore the backup file onto an on\-premises server, or a different Amazon RDS DB instance running SQL Server\. For more information, see [Importing and Exporting SQL Server Databases](SQLServer.Procedural.Importing.md)\. 

## Native Backup and Restore Option Settings<a name="Appendix.SQLServer.Options.BackupRestore.Options"></a>

Amazon RDS supports the following settings for the Native Backup and Restore option\. 


****  

| Option Setting | Valid Values | Description | 
| --- | --- | --- | 
| `IAM_ROLE_ARN` |  A valid Amazon Resource Name \(ARN\) in the format `arn:aws:iam::account-id:role/role-name`\.   |  The ARN for an AWS Identity and Access Management \(IAM\) role to access the Amazon S3 bucket that contains your backup files\. For more information, see [ AWS Identity and Access Management \(IAM\) ](https://docs.aws.amazon.com/general/latest/gr/aws-arns-and-namespaces.html#arn-syntax-iam)\.   | 

## Adding the Native Backup and Restore Option<a name="Appendix.SQLServer.Options.BackupRestore.Add"></a>

The general process for adding the Native Backup and Restore option to a DB instance is the following: 

1. Create a new option group, or copy or modify an existing option group\.

1. Add the option to the option group\.

1. Associate the option group with the DB instance\.

After you add the Native Backup and Restore option, you don't need to restart your DB instance\. As soon as the option group is active, you can begin backing up and restoring immediately\. 

**To add the Native Backup and Restore option**

1. Determine the option group you want to use\. You can create a new option group or use an existing option group\. If you want to use an existing option group, skip to the next step\. Otherwise, create a custom DB option group\. For more information, see [Creating an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.Create)\. 

1. Add the **SQLSERVER\_BACKUP\_RESTORE** option to the option group, and configure the option settings\. For more information about adding options, see [Adding an Option to an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.AddOption)\. 

   1. For **IAM Role**, select an existing IAM role\. Alternatively, you can choose to have a new IAM role created for you by choosing **Create a New Role**\. 

   1. For **Select S3 Bucket**, select an existing bucket\. Alternatively, you can choose to have a new Amazon S3 bucket created for you by choosing **Create a New S3 Bucket**\. 

   1. For **Enable Encryption**, choose **Yes** to encrypt the backup file\. If you choose **Yes**, for **Master Key** you must also choose an encryption key\. For more information about encryption keys, see [ Getting Started ](https://docs.aws.amazon.com/kms/latest/developerguide/getting-started.html) in the AWS Key Management Service \(AWS KMS\) documentation\. 

1. Apply the option group to a new or existing DB instance\. 
   + For a new DB instance, you apply the option group when you launch the instance\. For more information, see [Creating a DB Instance Running the Microsoft SQL Server Database Engine](USER_CreateMicrosoftSQLServerInstance.md)\. 
   + For an existing DB instance, you apply the option group by modifying the instance and attaching the new option group\. For more information, see [Modifying a DB Instance Running the Microsoft SQL Server Database Engine](USER_ModifyInstance.SQLServer.md)\. 

## Modifying Native Backup and Restore Option Settings<a name="Appendix.SQLServer.Options.BackupRestore.ModifySettings"></a>

After you enable the Native Backup and Restore option, you can modify the settings for the option\. For more information about how to modify option settings, see [Modifying an Option Setting](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.ModifyOption)\. For more information about each setting, see [Native Backup and Restore Option Settings](#Appendix.SQLServer.Options.BackupRestore.Options)\. 

## Removing the Native Backup and Restore Option<a name="Appendix.SQLServer.Options.BackupRestore.Remove"></a>

You can turn off the native backup and restore feature by removing the option from your DB instance\. After you remove the Native Backup and Restore option, you don't need to restart your DB instance\. 

To remove the Native Backup and Restore option from a DB instance, do one of the following: 
+ Remove the Native Backup and Restore option from the option group it belongs to\. This change affects all DB instances that use the option group\. For more information, see [Removing an Option from an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.RemoveOption) 
+ Modify the DB instance and specify a different option group that doesn't include the Native Backup and Restore option\. This change affects a single DB instance\. You can specify the default \(empty\) option group, or a different custom option group\. For more information, see [Modifying a DB Instance Running the Microsoft SQL Server Database Engine](USER_ModifyInstance.SQLServer.md)\. 