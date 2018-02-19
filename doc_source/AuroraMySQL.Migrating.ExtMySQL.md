# Migrating Data from an External MySQL Database to an Amazon Aurora MySQL DB Cluster<a name="AuroraMySQL.Migrating.ExtMySQL"></a>

If your database supports the InnoDB or MyISAM tablespaces, you have these options for migrating your data to an Amazon Aurora MySQL DB cluster: 

+ You can create a dump of your data using the `mysqldump` utility, and then import that data into an existing Amazon Aurora MySQL DB cluster\. For more information, see [Migrating from MySQL to Amazon Aurora by Using mysqldump](#AuroraMySQL.Migrating.ExtMySQL.mysqldump)\.

+ You can copy the full and incremental backup files from your database to an Amazon S3 bucket, and then restore an Amazon Aurora MySQL DB cluster from those files\. This option can be considerably faster than migrating data using `mysqldump`\. For more information, see [Migrating Data from MySQL by Using an Amazon S3 Bucket](#AuroraMySQL.Migrating.ExtMySQL.S3)\.

## Migrating Data from MySQL by Using an Amazon S3 Bucket<a name="AuroraMySQL.Migrating.ExtMySQL.S3"></a>

You can copy the full and incremental backup files from your source MySQL version 5\.5 or 5\.6 database to an Amazon S3 bucket, and then restore an Amazon Aurora MySQL DB cluster from those files\.

This option can be considerably faster than migrating data using `mysqldump`, because using `mysqldump` replays all of the commands to recreate the schema and data from your source database in your new Aurora MySQL DB cluster\. By copying your source MySQL data files, Aurora MySQL can immediately use those files as the data for an Aurora MySQL DB cluster\.

**Note**  
The Amazon S3 bucket and the Amazon Aurora MySQL DB cluster must be in the same region\.

Aurora MySQL doesn't restore everything from your database\. You should save the database schema and values for the following items from your source MySQL database and add them to your restored Aurora MySQL DB cluster after it has been created\.

+ User accounts

+ Functions

+ Stored procedures

+ Time zone information\. Time zone information is loaded from the local operating system of your Amazon Aurora MySQL DB cluster\. For more information, see [Local Time Zone for Amazon Aurora DB Clusters](Aurora.Overview.md#Aurora.Overview.LocalTimeZone)\.

### Before You Begin<a name="AuroraMySQL.Migrating.ExtMySQL.S3.Prereqs"></a>

Before you can copy your data to an Amazon S3 bucket and restore a DB cluster from those files, you must do the following:

+ Install Percona XtraBackup on your local server\.

+ Permit Aurora MySQL to access your Amazon S3 bucket on your behalf\.

#### Installing Percona XtraBackup<a name="AuroraMySQL.Migrating.ExtMySQL.S3.Prereqs.XtraBackup"></a>

Amazon Aurora can restore a DB cluster from files that were created using Percona XtraBackup\. You can install Percona XtraBackup from [the Percona website](https://www.percona.com/software/mysql-database/percona-xtrabackup)\.

**Note**  
You must use Percona XtraBackup version 2\.3 or later\. Aurora MySQL is not compatible with earlier versions of Percona XtraBackup\.

#### Required Permissions<a name="AuroraMySQL.Migrating.ExtMySQL.S3.Prereqs.Permitting"></a>

To migrate your MySQL data to an Amazon Aurora MySQL DB cluster, several permissions are required:

+ The user that is requesting that Amazon RDS create a new cluster from an Amazon S3 bucket must have permission to list the buckets for your AWS account\. You grant the user this permission using an AWS Identity and Access Management \(IAM\) policy\.

+ Amazon RDS requires permission to act on your behalf to access the Amazon S3 bucket where you store the files used to create your Amazon Aurora MySQL DB cluster\. You grant Amazon RDS the required permissions using an IAM service role\. 

+ The user making the request must also have permission to list the IAM roles for your AWS account\.

+ If the user making the request will create the IAM service role, or will request that Amazon RDS create the IAM service role \(by using the console\), then the user must have permission to create an IAM role for your AWS account\.

For example, the following IAM policy grants a user the minimum required permissions to use the console to list IAM roles, create an IAM role, and list the Amazon S3 buckets for your account\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "iam:ListRoles",
                "iam:CreateRole",
                "iam:CreatePolicy",
                "iam:AttachRolePolicy",
                "s3:ListBucket",
                "s3:ListObjects"
            ],
            "Resource": "*"
        }
    ]
}
```

Additionally, for a user to associate an IAM role with an Amazon S3 bucket, the IAM user must have the `iam:PassRole` permission for that IAM role\. This permission allows an administrator to restrict which IAM roles a user can associate with Amazon S3 buckets\. 

For example, the following IAM policy allows a user to associate the role named `S3Access` with an Amazon S3 bucket\.

```
{
    "Version":"2012-10-17",
    "Statement":[
        {
            "Sid":"AllowS3AccessRole",
            "Effect":"Allow",
            "Action":"iam:PassRole",
            "Resource":"arn:aws:iam::123456789012:role/S3Access"
        }
    ]
}
```

For more information on IAM user permissions, see [Using Identity\-Based Policies \(IAM Policies\) for Amazon RDS](UsingWithRDS.IAM.AccessControl.IdentityBased.md)\.

#### Creating the IAM Service Role<a name="AuroraMySQL.Migrating.ExtMySQL.S3.Prereqs.CreateRole"></a>

You can have the AWS Management Console create a role for you by choosing the **Create a New Role** option \(shown later in this topic\)\. If you select this option and specify a name for the new role, then Amazon RDS creates the IAM service role required for Amazon RDS to access your Amazon S3 bucket with the name that you supply\.

As an alternative, you can manually create the role using the following procedure\.

**To create an IAM role for Amazon RDS to access Amazon S3**

1. Complete the steps in [Creating an IAM Policy to Access Amazon S3 Resources](AuroraMySQL.Integrating.Authorizing.IAM.S3CreatePolicy.md)\.

1. Complete the steps in [Creating an IAM Role to Allow Amazon Aurora to Access AWS Services](AuroraMySQL.Integrating.Authorizing.IAM.CreateRole.md)\.

1. Complete the steps in [Associating an IAM Role with an Amazon Aurora MySQL DB Cluster](AuroraMySQL.Integrating.Authorizing.IAM.AddRoleToDBCluster.md)\.

### Backing Up Files to be Restored as an Amazon Aurora MySQL DB Cluster<a name="AuroraMySQL.Migrating.ExtMySQL.S3.Backup"></a>

You can create a full backup of your MySQL database files using Percona XtraBackup and upload the backup files to an Amazon S3 bucket\. Alternatively, if you already use Percona XtraBackup to back up your MySQL database files, you can upload your existing full and incremental backup directories and files to an Amazon S3 bucket\.

#### Creating a Full Backup With Percona XtraBackup<a name="AuroraMySQL.Migrating.ExtMySQL.S3.Backup.Full"></a>

To create a full backup of your MySQL database files that can be restored from Amazon S3 to create an Amazon Aurora MySQL DB cluster, use the Percona XtraBackup utility \(`innobackupex`\) to back up your database\. 

For example, the following command creates a backup of a MySQL database and stores the files in the `/s3-restore/backup` folder\.

```
innobackupex --user=myuser --password=<password> --no-timestamp /s3-restore/backup
```

If you want to compress your backup into a single file \(which can be split, if needed\), you can use the `--stream` option to save your backup in one of the following formats:

+ Gzip \(\.gz\)

+ tar \(\.tar\)

+ Percona xbstream \(\.xbstream\)

The following command creates a backup of your MySQL database split into multiple Gzip files\.

```
innobackupex --user=myuser --password=<password> --stream=tar \
   /mydata/s3-restore/backup | gzip - | split -d --bytes=500MB \
   - /mydata/s3-restore/backup/backup.tar.gz
```

The following command creates a backup of your MySQL database split into multiple tar files\.

```
innobackupex --user=myuser --password=<password> --stream=tar \
   /mydata/s3-restore/backup | split -d --bytes=500MB \
   - /mydata/s3-restore/backup/backup.tar
```

The following command creates a backup of your MySQL database split into multiple xbstream files\.

```
innobackupex --stream=xbstream  \
   /mydata/s3-restore/backup | split -d --bytes=500MB \
   - /mydata/s3-restore/backup/backup.xbstream
```

Once you have backed up your MySQL database using the Percona XtraBackup utility, then you can copy your backup directories and files to an Amazon S3 bucket\.

For information on creating and uploading a file to an Amazon S3 bucket, see [Getting Started with Amazon Simple Storage Service](http://docs.aws.amazon.com/AmazonS3/latest/gsg/GetStartedWithS3.html) in the *Amazon S3 Getting Started Guide*\.

#### Using Incremental Backups With Percona XtraBackup<a name="AuroraMySQL.Migrating.ExtMySQL.S3.Backup.Incr"></a>

Amazon Aurora MySQL supports both full and incremental backups created using Percona XtraBackup\. If you already use Percona XtraBackup to perform full and incremental backups of your MySQL database files, you don't need to create a full backup and upload the backup files to Amazon S3\. Instead, you can save a significant amount of time by copying your existing backup directories and files for your full and incremental backups to an Amazon S3 bucket\. For more information about creating incremental backups using Percona XtraBackup, see [Incremental Backups with innobackupex](https://www.percona.com/doc/percona-xtrabackup/2.1/innobackupex/incremental_backups_innobackupex.html)

When copying your existing full and incremental backup files to an Amazon S3 bucket, you must recursively copy the contents of the base directory\. Those contents include the full backup and also all incremental backup directories and files\. This copy must preserve the directory structure in the Amazon S3 bucket\. Aurora iterates through all files and directories\. Aurora uses the `xtrabackup-checkpoints` file included with each incremental backup to identify the base directory and to order incremental backups by log sequence number \(LSN\) range\.

For information on creating and uploading a file to an Amazon S3 bucket, see [Getting Started with Amazon Simple Storage Service](http://docs.aws.amazon.com/AmazonS3/latest/gsg/GetStartedWithS3.html) in the *Amazon S3 Getting Started Guide*\.

#### Backup Considerations<a name="AuroraMySQL.Migrating.ExtMySQL.S3.Backup.Considerations"></a>

Amazon S3 limits the size of a file uploaded to an Amazon S3 bucket to 5 terabytes \(TB\)\. If the backup data for your database exceeds 5 TB, then you must use the `split` command to split the backup files into multiple files that are each less than 5 TB\.

Amazon RDS limits the number of source files uploaded to an Amazon S3 bucket to 1 million files\. If the backup data for your database, including all full and incremental backups, include a large number of files, use a tarball \(\.tar\.gz\) file to store full and incremental backup files in the Amazon S3 bucket\.

Aurora consumes your backup files based on the file name\. Be sure to name your backup files with the appropriate file extension based on the file format—for example, `.xbstream` for files stored using the Percona xbstream format\.

Aurora consumes your backup files in alphabetical order and also in natural number order\. Always use the `split` option when you issue the `innobackupex` command to ensure that your backup files are written and named in the proper order\.

Aurora doesn't support partial backups created using Percona XtraBackup\. You cannot use the `--include`, `--tables-file`, or `--databases` options to create a partial backup when you backup the source files for your database\.

Aurora supports incremental backups created using Percona XtraBackup, with or without the `--no-timestamp` option\. We recommend that you use the `--no-timestamp` option, to reduce the depth of the directory structure for your incremental backup\. 

For more information, see [The innobackupex Script](https://www.percona.com/doc/percona-xtrabackup/2.1/innobackupex/innobackupex_script.html)\.

### Restoring an Amazon Aurora MySQL DB Cluster from an Amazon S3 Bucket<a name="AuroraMySQL.Migrating.ExtMySQL.S3.Restore"></a>

You can restore your backup files from your Amazon S3 bucket to create a new Amazon Aurora MySQL DB cluster by using the Amazon RDS console\. 

**To restore an Amazon Aurora MySQL DB cluster from files on an Amazon S3 bucket**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the RDS dashboard, choose **Restore from S3** under **Create instance**\.

1. On the **Select engine** page, choose Amazon Aurora and choose the MySQL\-compatible edition, and choose **Next**\.

1. In the **Specify source backup details**, specify the following:    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/AuroraMySQL.Migrating.ExtMySQL.html)

   A typical **Specify source backup details** page looks like the following\.  
![\[Amazon Aurora Migrate from an Amazon S3 bucket\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/AuroraMigrateS3_01.png)

1. Choose **Next**\.

1. On the **Specify DB details** page, specify your DB cluster information\. The following table shows settings for a DB instance\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/AuroraMySQL.Migrating.ExtMySQL.html)

   A typical **Specify DB details** page looks like the following\.   
![\[Amazon Aurora Launch DB Instance Wizard DB Instance Details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/AuroraLaunch02.png)

1. Confirm your master password, and then choose **Next**\.

1. On the **Configure advanced settings** page, you can customize additional settings for your Aurora MySQL DB cluster\. The following table shows the advanced settings for a DB cluster\.     
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/AuroraMySQL.Migrating.ExtMySQL.html)

1. Choose **Launch DB instance** to launch your Aurora DB instance, and then choose **Close** to close the wizard\. 

On the Amazon RDS console, the new DB instance appears in the list of DB instances\. The DB instance has a status of **creating** until the DB instance is created and ready for use\. When the state changes to **available**, you can connect to the primary instance for your DB cluster\. Depending on the DB instance class and store allocated, it can take several minutes for the new instance to be available\.

To view the newly created cluster, choose the **Clusters** view in the Amazon RDS console and choose the cluster\. For more information, see [Viewing an Amazon Aurora DB Cluster](Aurora.Viewing.md)\.

![\[Amazon Aurora DB Instances List\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/AuroraLaunch04.png)

Note the port and the endpoint of the cluster\. Use the endpoint and port of the cluster in your JDBC and ODBC connection strings for any application that performs write or read operations\.

## Migrating from MySQL to Amazon Aurora by Using mysqldump<a name="AuroraMySQL.Migrating.ExtMySQL.mysqldump"></a>

Because Amazon Aurora MySQL is a MySQL\-compatible database, you can use the `mysqldump` utility to copy data from your MySQL or MariaDB database to an existing Aurora MySQL DB cluster\. For a discussion of how to do so with MySQL databases that are very large, see [Importing Data to an Amazon RDS MySQL or MariaDB DB Instance with Reduced Downtime](MySQL.Procedural.Importing.NonRDSRepl.md)\. For MySQL databases that have smaller amounts of data, see [Importing Data from a MySQL or MariaDB DB to an Amazon RDS MySQL or MariaDB DB Instance](MySQL.Procedural.Importing.SmallExisting.md)\.

## Related Topics<a name="AuroraMySQL.Migrating.ExtMySQL.RelatedTopics"></a>

+ [Amazon Aurora on Amazon RDS](CHAP_Aurora.md)

+ [Migrating Data to an Amazon Aurora DB Cluster](Aurora.Migrate.md)