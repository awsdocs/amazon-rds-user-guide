# Migrating an on\-premises database to Amazon RDS Custom for SQL Server<a name="custom-migrating"></a>

You can use the following process to migrate an on\-premises Microsoft SQL Server database to Amazon RDS Custom for SQL Server using native backup and restore:

1. Take a full backup of the database on the on\-premises DB instance\.

1. Upload the backup file to Amazon S3\.

1. Download the backup file from S3 to your RDS Custom for SQL Server DB instance\.

1. Restore a database using the downloaded backup file on the RDS Custom for SQL Server DB instance\.

This process explains the migration of a database from on\-premises to RDS Custom for SQL Server, using native full backup and restore\. To reduce the cutover time during the migration process, you might also consider using differential or log backups\.

For general information about native backup and restore for RDS for SQL Server, see [Importing and exporting SQL Server databases using native backup and restore](SQLServer.Procedural.Importing.md)\.

**Topics**
+ [Prerequisites](#custom-migrating.prereqs)
+ [Backing up the on\-premises database](#custom-migrating.backup)
+ [Uploading the backup file to Amazon S3](#custom-migrating.upload)
+ [Downloading the backup file from Amazon S3](#custom-migrating.upload)
+ [Restoring the backup file to the RDS Custom for SQL Server DB instance](#custom-migrating.restore)

## Prerequisites<a name="custom-migrating.prereqs"></a>

Perform the following tasks before migrating the database:

1. Configure Remote Desktop Connection \(RDP\) for your RDS Custom for SQL Server DB instance\. For more information, see [Connecting to your RDS Custom DB instance using RDP](custom-creating-sqlserver.md#custom-creating-sqlserver.rdp)\.

1. Configure access to Amazon S3 so you can upload and download the database backup file\. For more information, see [Integrating an Amazon RDS for SQL Server DB instance with Amazon S3](User.SQLServer.Options.S3-integration.md)\.

## Backing up the on\-premises database<a name="custom-migrating.backup"></a>

You use SQL Server native backup to take a full backup of the database on the on\-premises DB instance\.

The following example shows a backup of a database called `mydatabase`, with the `COMPRESSION` option specified to reduce the backup file size\.

**To back up the on\-premises database**

1. Using SQL Server Management Studio \(SSMS\), connect to the on\-premises SQL Server instance\.

1. Run the following T\-SQL command\.

   ```
   backup database mydatabase to
   disk ='C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\MSSQL\Backup\mydb-full-compressed.bak'
   with compression;
   ```

## Uploading the backup file to Amazon S3<a name="custom-migrating.upload"></a>

You use the AWS Management Console to upload the backup file `mydb-full-compressed.bak` to Amazon S3\.

**To upload the backup file to S3**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. For **Buckets**, choose the name of the bucket to which you want to upload your backup file\.

1. Choose **Upload**\.

1. In the **Upload** window, do one of the following:
   + Drag and drop `mydb-full-compressed.bak` to the **Upload** window\.
   + Choose **Add file**, choose `mydb-full-compressed.bak`, and then choose **Open**\.

   Amazon S3 uploads your backup file as an S3 object\. When the upload completes, you can see a success message on the **Upload: status** page\.

## Downloading the backup file from Amazon S3<a name="custom-migrating.upload"></a>

You use the console to download the backup file from S3 to the RDS Custom for SQL Server DB instance\.

**To download the backup file from S3**

1. Using RDP, connect to your RDS Custom for SQL Server DB instance\.

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** list, choose the name of the bucket that contains your backup file\.

1. Choose the backup file `mydb-full-compressed.bak`\.

1. For **Actions**, choose **Download as**\.

1. Open the context \(right\-click\) menu for the link provided, then choose **Save As**\.

1. Save `mydb-full-compressed.bak` to the `D:\rdsdbdata\BACKUP` directory\.

## Restoring the backup file to the RDS Custom for SQL Server DB instance<a name="custom-migrating.restore"></a>

You use SQL Server native restore to restore the backup file to your RDS Custom for SQL Server DB instance\.

In this example, the `MOVE` option is specified because the data and log file directories are different from the on\-premises DB instance\.

**To restore the backup file**

1. Using SSMS, connect to your RDS Custom for SQL Server DB instance\.

1. Run the following T\-SQL command\.

   ```
   restore database mydatabase from disk='D:\rdsdbdata\BACKUP\mydb-full-compressed.bak'
   with move 'mydatabase' to 'D:\rdsdbdata\DATA\mydatabase.mdf',
   move 'mydatabase_log' to 'D:\rdsdbdata\DATA\mydatabase_log.ldf';
   ```