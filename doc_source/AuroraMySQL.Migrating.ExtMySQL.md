# Migrating Data from an External MySQL Database to an Amazon Aurora MySQL DB Cluster<a name="AuroraMySQL.Migrating.ExtMySQL"></a>

If your database supports the InnoDB or MyISAM tablespaces, you have these options for migrating your data to an Amazon Aurora MySQL DB cluster: 
+ You can create a dump of your data using the `mysqldump` utility, and then import that data into an existing Amazon Aurora MySQL DB cluster\. For more information, see [Migrating from MySQL to Amazon Aurora by Using mysqldump](#AuroraMySQL.Migrating.ExtMySQL.mysqldump)\.
+ You can copy the full and incremental backup files from your database to an Amazon S3 bucket, and then restore an Amazon Aurora MySQL DB cluster from those files\. This option can be considerably faster than migrating data using `mysqldump`\. For more information, see [Migrating Data from MySQL by Using an Amazon S3 Bucket](#AuroraMySQL.Migrating.ExtMySQL.S3)\.

## Migrating Data from MySQL by Using an Amazon S3 Bucket<a name="AuroraMySQL.Migrating.ExtMySQL.S3"></a>

You can copy the full and incremental backup files from your source MySQL version 5\.5 or 5\.6 database to an Amazon S3 bucket, and then restore an Amazon Aurora MySQL DB cluster from those files\.

This option can be considerably faster than migrating data using `mysqldump`, because using `mysqldump` replays all of the commands to recreate the schema and data from your source database in your new Aurora MySQL DB cluster\. By copying your source MySQL data files, Aurora MySQL can immediately use those files as the data for an Aurora MySQL DB cluster\.

**Note**  
The Amazon S3 bucket and the Amazon Aurora MySQL DB cluster must be in the same AWS Region\.

Aurora MySQL doesn't restore everything from your database\. You should save the database schema and values for the following items from your source MySQL database and add them to your restored Aurora MySQL DB cluster after it has been created:
+ User accounts
+ Functions
+ Stored procedures
+ Time zone information\. Time zone information is loaded from the local operating system of your Amazon Aurora MySQL DB cluster\. For more information, see [Local Time Zone for Amazon Aurora DB Clusters](Aurora.Overview.md#Aurora.Overview.LocalTimeZone)\.

You can encrypt the data being migrated, or leave the data unencrypted during the migration process\.

Also, decide whether you want to minimize downtime by using binary log replication during the migration process\. If you use binary log replication, the external MySQL database remains open to transactions while the data is being migrated to the Aurora MySQL DB cluster\. After the Aurora MySQL DB cluster has been created, you use binary log replication to synchronize the Aurora MySQL DB cluster with the transactions that happened after the backup\. When the Aurora MySQL DB cluster is caught up with the MySQL database, you finish the migration by completely switching to the Aurora MySQL DB cluster for new transactions\.

**Topics**
+ [Before You Begin](#AuroraMySQL.Migrating.ExtMySQL.S3.Prereqs)
+ [Backing Up Files to be Restored as an Amazon Aurora MySQL DB Cluster](#AuroraMySQL.Migrating.ExtMySQL.S3.Backup)
+ [Restoring an Amazon Aurora MySQL DB Cluster from an Amazon S3 Bucket](#AuroraMySQL.Migrating.ExtMySQL.S3.Restore)
+ [Synchronizing the Amazon Aurora MySQL DB Cluster with the MySQL Database Using Replication](#AuroraMySQL.Migrating.ExtMySQL.S3.RepSync)

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
+ If the user making the request is to create the IAM service role or request that Amazon RDS create the IAM service role \(by using the console\), then the user must have permission to create an IAM role for your AWS account\.
+ If you plan to encrypt the data during the migration process, update the IAM policy of the user who will perform the migration to grant RDS access to the KMS keys used for encrypting the backups\. For instructions, see [Creating an IAM Policy to Access AWS KMS Resources](AuroraMySQL.Integrating.Authorizing.IAM.KMSCreatePolicy.md)\.

For example, the following IAM policy grants a user the minimum required permissions to use the console to list IAM roles, create an IAM role, list the Amazon S3 buckets for your account, and list the KMS keys\.

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
                "kms:ListKeys"
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

Once you have backed up your MySQL database using the Percona XtraBackup utility, you can copy your backup directories and files to an Amazon S3 bucket\.

For information on creating and uploading a file to an Amazon S3 bucket, see [Getting Started with Amazon Simple Storage Service](http://docs.aws.amazon.com/AmazonS3/latest/gsg/GetStartedWithS3.html) in the *Amazon S3 Getting Started Guide*\.

#### Using Incremental Backups With Percona XtraBackup<a name="AuroraMySQL.Migrating.ExtMySQL.S3.Backup.Incr"></a>

Amazon Aurora MySQL supports both full and incremental backups created using Percona XtraBackup\. If you already use Percona XtraBackup to perform full and incremental backups of your MySQL database files, you don't need to create a full backup and upload the backup files to Amazon S3\. Instead, you can save a significant amount of time by copying your existing backup directories and files for your full and incremental backups to an Amazon S3 bucket\. For more information about creating incremental backups using Percona XtraBackup, see [Incremental Backups with innobackupex](https://www.percona.com/doc/percona-xtrabackup/2.1/innobackupex/incremental_backups_innobackupex.html)\.

When copying your existing full and incremental backup files to an Amazon S3 bucket, you must recursively copy the contents of the base directory\. Those contents include the full backup and also all incremental backup directories and files\. This copy must preserve the directory structure in the Amazon S3 bucket\. Aurora iterates through all files and directories\. Aurora uses the `xtrabackup-checkpoints` file included with each incremental backup to identify the base directory and to order incremental backups by log sequence number \(LSN\) range\.

For information on creating and uploading a file to an Amazon S3 bucket, see [Getting Started with Amazon Simple Storage Service](http://docs.aws.amazon.com/AmazonS3/latest/gsg/GetStartedWithS3.html) in the *Amazon S3 Getting Started Guide*\.

#### Backup Considerations<a name="AuroraMySQL.Migrating.ExtMySQL.S3.Backup.Considerations"></a>

When you upload a file to an Amazon S3 bucket, you can use server\-side encryption to encrypt the data\. You can then restore an Amazon Aurora MySQL DB cluster from those encrypted files\. Amazon Aurora MySQL can restore a DB cluster with files encrypted using the following types of server\-side encryption:
+ Server\-side encryption with Amazon S3–managed keys \(SSE\-S3\) – Each object is encrypted with a unique key employing strong multifactor encryption\.
+ Server\-side encryption with AWS KMS–managed keys \(SSE\-KMS\) – Similar to SSE\-S3, but you have the option to create and manage encryption keys yourself, and also other differences\.

For information about using server\-side encryption when uploading files to an Amazon S3 bucket, see [Protecting Data Using Server\-Side Encryption](http://docs.aws.amazon.com/AmazonS3/latest/dev/serv-side-encryption.html) in the *Amazon S3 Developer Guide*\.

Amazon S3 limits the size of a file uploaded to an Amazon S3 bucket to 5 terabytes \(TB\)\. If the backup data for your database exceeds 5 TB, use the `split` command to split the backup files into multiple files that are each less than 5 TB\.

Amazon RDS limits the number of source files uploaded to an Amazon S3 bucket to 1 million files\. In some cases, backup data for your database, including all full and incremental backups, can come to a large number of files\. In these cases, use a tarball \(\.tar\.gz\) file to store full and incremental backup files in the Amazon S3 bucket\.

Aurora consumes your backup files based on the file name\. Be sure to name your backup files with the appropriate file extension based on the file format—for example, `.xbstream` for files stored using the Percona xbstream format\.

Aurora consumes your backup files in alphabetical order and also in natural number order\. Always use the `split` option when you issue the `innobackupex` command to ensure that your backup files are written and named in the proper order\.

Aurora doesn't support partial backups created using Percona XtraBackup\. You can't use the `--include`, `--tables-file`, or `--databases` options to create a partial backup when you backup the source files for your database\.

Aurora supports incremental backups created using Percona XtraBackup, with or without the `--no-timestamp` option\. We recommend that you use the `--no-timestamp` option, to reduce the depth of the directory structure for your incremental backup\. 

For more information, see [The innobackupex Script](https://www.percona.com/doc/percona-xtrabackup/2.1/innobackupex/innobackupex_script.html) on the Percona website

### Restoring an Amazon Aurora MySQL DB Cluster from an Amazon S3 Bucket<a name="AuroraMySQL.Migrating.ExtMySQL.S3.Restore"></a>

You can restore your backup files from your Amazon S3 bucket to create a new Amazon Aurora MySQL DB cluster by using the Amazon RDS console\. 

**To restore an Amazon Aurora MySQL DB cluster from files on an Amazon S3 bucket**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Instances**, and then choose **Restore from S3**\.

1. On the **Select engine** page, choose Amazon Aurora, choose the MySQL\-compatible edition, and then choose **Next**\.

1. In **Specify source backup details**, specify the following\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/AuroraMySQL.Migrating.ExtMySQL.html)

   A typical **Specify source backup details** page for AWS\-KMS encrypted backup files looks like the following\.  
![\[Amazon Aurora migration from an Amazon S3 bucket\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/AuroraMigrateS3_01.png)

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

To view the newly created cluster, choose the **Clusters** view in the Amazon RDS console and choose the DB cluster\. For more information, see [Viewing an Amazon Aurora DB Cluster](Aurora.Viewing.md)\.

![\[Amazon Aurora DB Instances List\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/AuroraLaunch04.png)

Note the port and the endpoint of the DB cluster\. Use the endpoint and port of the DB cluster in your JDBC and ODBC connection strings for any application that performs write or read operations\.

### Synchronizing the Amazon Aurora MySQL DB Cluster with the MySQL Database Using Replication<a name="AuroraMySQL.Migrating.ExtMySQL.S3.RepSync"></a>

To achieve little or no downtime during the migration, you can replicate transactions that were committed on your MySQL database to your Aurora MySQL DB cluster\. Replication enables the DB cluster to catch up with the transactions on the MySQL database that happened during the migration\. When the DB cluster is completely caught up, you can stop the replication and finish the migration to Aurora MySQL\.

**Topics**
+ [Configuring Your External MySQL Database and Your Aurora MySQL DB Cluster for Encrypted Replication](#AuroraMySQL.Migrating.ExtMySQL.S3.RepSync.ConfigureEncryption)
+ [Synchronizing the Amazon Aurora MySQL DB Cluster with the External MySQL Database](#AuroraMySQL.Migrating.ExtMySQL.S3.RepSync.Synchronizing)

#### Configuring Your External MySQL Database and Your Aurora MySQL DB Cluster for Encrypted Replication<a name="AuroraMySQL.Migrating.ExtMySQL.S3.RepSync.ConfigureEncryption"></a>

To replicate data securely, you can use encrypted replication\.

**Note**  
If you don't need to use encrypted replication, you can skip these steps and move on to the instructions in [Synchronizing the Amazon Aurora MySQL DB Cluster with the External MySQL Database](#AuroraMySQL.Migrating.ExtMySQL.S3.RepSync.Synchronizing)\.

The following are prerequisites for using encrypted replication:
+ Secure Sockets Layer \(SSL\) must be enabled on the external MySQL master database\.
+ A client key and client certificate must be prepared for the Aurora MySQL DB cluster\.

During encrypted replication, the Aurora MySQL DB cluster acts a client to the MySQL database server\. The certificates and keys for the Aurora MySQL client are in files in \.pem format\.

**To configure your external MySQL database and your Aurora MySQL DB cluster for encrypted replication**

1. Ensure that you are prepared for encrypted replication:
   + If you don't have SSL enabled on the external MySQL master database and don't have a client key and client certificate prepared, enable SSL on the MySQL database server and generate the required client key and client certificate\.
   + If SSL is enabled on the external master, supply a client key and certificate for the Aurora MySQL DB cluster\. If you don't have these, generate a new key and certificate for the Aurora MySQL DB cluster\. To sign the client certificate, you must have the certificate authority key that you used to configure SSL on the external MySQL master database\.

   For more information, see [ Creating SSL Certificates and Keys Using openssl](https://dev.mysql.com/doc/refman/5.6/en/creating-ssl-files-using-openssl.html) in the MySQL documentation\.

   You need the certificate authority certificate, the client key, and the client certificate\.

1. Connect to the Aurora MySQL DB cluster as the master user using SSL\.

   For information about connecting to an Aurora MySQL DB cluster with SSL, see [Using SSL with Aurora MySQL DB Clusters](AuroraMySQL.Security.md#AuroraMySQL.Security.SSL)\.

1. Run the `mysql.rds_import_binlog_ssl_material` stored procedure to import the SSL information into the Aurora MySQL DB cluster\.

   For the `ssl_material_value` parameter, insert the information from the \.pem format files for the Aurora MySQL DB cluster in the correct JSON payload\.

   The following example imports SSL information into an Aurora MySQL DB cluster\. In \.pem format files, the body code typically is longer than the body code shown in the example\.

   ```
   call mysql.rds_import_binlog_ssl_material(
   '{"ssl_ca":"-----BEGIN CERTIFICATE-----
   AAAAB3NzaC1yc2EAAAADAQABAAABAQClKsfkNkuSevGj3eYhCe53pcjqP3maAhDFcvBS7O6V
   hz2ItxCih+PnDSUaw+WNQn/mZphTk/a/gU8jEzoOWbkM4yxyb/wB96xbiFveSFJuOp/d6RJhJOI0iBXr
   lsLnBItntckiJ7FbtxJMXLvvwJryDUilBMTjYtwB+QhYXUMOzce5Pjz5/i8SeJtjnV3iAoG/cQk+0FzZ
   qaeJAAHco+CY/5WrUBkrHmFJr6HcXkvJdWPkYQS3xqC0+FmUZofz221CBt5IMucxXPkX4rWi+z7wB3Rb
   BQoQzd8v7yeb7OzlPnWOyN0qFU0XA246RA8QFYiCNYwI3f05p6KLxEXAMPLE
   -----END CERTIFICATE-----\n","ssl_cert":"-----BEGIN CERTIFICATE-----
   AAAAB3NzaC1yc2EAAAADAQABAAABAQClKsfkNkuSevGj3eYhCe53pcjqP3maAhDFcvBS7O6V
   hz2ItxCih+PnDSUaw+WNQn/mZphTk/a/gU8jEzoOWbkM4yxyb/wB96xbiFveSFJuOp/d6RJhJOI0iBXr
   lsLnBItntckiJ7FbtxJMXLvvwJryDUilBMTjYtwB+QhYXUMOzce5Pjz5/i8SeJtjnV3iAoG/cQk+0FzZ
   qaeJAAHco+CY/5WrUBkrHmFJr6HcXkvJdWPkYQS3xqC0+FmUZofz221CBt5IMucxXPkX4rWi+z7wB3Rb
   BQoQzd8v7yeb7OzlPnWOyN0qFU0XA246RA8QFYiCNYwI3f05p6KLxEXAMPLE
   -----END CERTIFICATE-----\n","ssl_key":"-----BEGIN RSA PRIVATE KEY-----
   AAAAB3NzaC1yc2EAAAADAQABAAABAQClKsfkNkuSevGj3eYhCe53pcjqP3maAhDFcvBS7O6V
   hz2ItxCih+PnDSUaw+WNQn/mZphTk/a/gU8jEzoOWbkM4yxyb/wB96xbiFveSFJuOp/d6RJhJOI0iBXr
   lsLnBItntckiJ7FbtxJMXLvvwJryDUilBMTjYtwB+QhYXUMOzce5Pjz5/i8SeJtjnV3iAoG/cQk+0FzZ
   qaeJAAHco+CY/5WrUBkrHmFJr6HcXkvJdWPkYQS3xqC0+FmUZofz221CBt5IMucxXPkX4rWi+z7wB3Rb
   BQoQzd8v7yeb7OzlPnWOyN0qFU0XA246RA8QFYiCNYwI3f05p6KLxEXAMPLE
   -----END RSA PRIVATE KEY-----\n"}');
   ```

   For more information, see [mysql\.rds\_import\_binlog\_ssl\_material](mysql_rds_import_binlog_ssl_material.md)\.
**Note**  
After running the procedure, the secrets are stored in files\. To erase the files later, you can run the [mysql\.rds\_remove\_binlog\_ssl\_material](mysql_rds_remove_binlog_ssl_material.md) stored procedure\.

#### Synchronizing the Amazon Aurora MySQL DB Cluster with the External MySQL Database<a name="AuroraMySQL.Migrating.ExtMySQL.S3.RepSync.Synchronizing"></a>

You can synchronize your Amazon Aurora MySQL DB cluster with the MySQL database using replication\.

**To synchronize your Aurora MySQL DB cluster with the MySQL database using replication**

1. Ensure that the /etc/my\.cnf file for the external MySQL database has the relevant entries\.

   If encrypted replication is not required, ensure that the external MySQL database is started with binary logs \(binlogs\) enabled and SSL disabled\. The following are the relevant entries in the /etc/my\.cnf file for unencrypted data\.

   ```
   log-bin=mysql-bin
   server-id=2133421
   innodb_flush_log_at_trx_commit=1
   sync_binlog=1
   ```

   If encrypted replication is required, ensure that the external MySQL database is started with SSL and binlogs enabled\. The entries in the /etc/my\.cnf file include the \.pem file locations for the MySQL database server\.

   ```
   log-bin=mysql-bin
   server-id=2133421
   innodb_flush_log_at_trx_commit=1
   sync_binlog=1
                               
   # Setup SSL.
   ssl-ca=/home/sslcerts/ca.pem
   ssl-cert=/home/sslcerts/server-cert.pem
   ssl-key=/home/sslcerts/server-key.pem
   ```

   You can verify that SSL is enabled with the following command\.

   ```
   mysql> show variables like 'have_ssl';                                                            
   ```

   Your output should be similar the following\.

   ```
                            
   +~-~-~-~-~-~-~-~-~-~-~-~-~-~--+~-~-~-~-~-~--+
   | Variable_name | Value |
   +~-~-~-~-~-~-~-~-~-~-~-~-~-~--+~-~-~-~-~-~--+
   | have_ssl      | YES   |
   +~-~-~-~-~-~-~-~-~-~-~-~-~-~--+~-~-~-~-~-~--+
   1 row in set (0.00 sec)
   ```

1. Determine the starting binary log position for replication:

   1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

   1. In the navigation pane, choose **Events**\.

   1. In the **Events** list, note the position in the **Recovered from Binary log filename** event\.  
![\[View MySQL master\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/aurora-mysql-rep-binary-log-position.png)

      You specify the position to start replication in a later step\.

1. While connected to the external MySQL database, create a user to be used for replication\. This account is used solely for replication and must be restricted to your domain to improve security\. The following is an example\.

   ```
   mysql> CREATE USER '<user_name>'@'<domain_name>' IDENTIFIED BY '<password>';
   ```

   The user requires the `REPLICATION CLIENT` and `REPLICATION SLAVE` privileges\. Grant these privileges to the user\.

   ```
   GRANT REPLICATION CLIENT, REPLICATION SLAVE ON *.* TO '<user_name>'@'<domain_name>';
   ```

   If you need to use encrypted replication, require SSL connections for the replication user\. For example, you can use the following statement to require SSL connections on the user account `<user_name>`\.

   ```
   GRANT USAGE ON *.* TO '<user_name>'@'<domain_name>' REQUIRE SSL;            
   ```
**Note**  
If `REQUIRE SSL` is not included, the replication connection might silently fall back to an unencrypted connection\.

1. In the Amazon RDS Management Console, add the IP address of the server that hosts the external MySQL database to the VPC security group for the Aurora MySQL DB cluster\. For more information on modifying a VPC security group, see [Security Groups for Your VPC](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_SecurityGroups.html) in the *Amazon Virtual Private Cloud User Guide*\. 

   You might also need to configure your local network to permit connections from the IP address of your Aurora MySQL DB cluster, so that it can communicate with your external MySQL database\. To find the IP address of the Aurora MySQL DB cluster, use the `host` command\.

   ```
   host RDS_MySQL_DB_<host_name>
   ```

   The host name is the DNS name from the Aurora MySQL DB cluster endpoint\.

1. Enable binary log replication by running the `mysql.rds_set_external_master` stored procedure\. This stored procedure has the following syntax\.

   ```
   CALL mysql.rds_set_external_master (
     host_name
     , host_port
     , replication_user_name
     , replication_user_password
     , mysql_binary_log_file_name
     , mysql_binary_log_file_location
     , ssl_encryption
   );
   ```

   For information about the parameters, see [mysql\.rds\_set\_external\_master](mysql_rds_set_external_master.md)\.

   For `mysql_binary_log_file_name` and `mysql_binary_log_file_location`, use the position in the **Recovered from Binary log filename** event you noted earlier\.

   If the data in the Aurora MySQL DB cluster is not encrypted, the `ssl_encryption` parameter must be set to `0`\. If the data is encrypted, the `ssl_encryption` parameter must be set to `1`\.

   The following example runs the procedure for an Aurora MySQL DB cluster that has encrypted data\.

   ```
   CALL mysql.rds_set_external_master(
     'Externaldb.some.com',
     3306,
     'repl_user'@'mydomain.com',
     'password',
     'mysql-bin.000010',
     120,
     1);
   ```

   This stored procedure sets the parameters that the Aurora MySQL DB cluster uses for connecting to the external MySQL database and reading its binary log\. If the data is encrypted, it also downloads the SSL certificate authority certificate, client certificate, and client key to the local disk\. 

1. Start binary log replication by running the `mysql.rds_start_replication` stored procedure\.

   ```
   CALL mysql.rds_start_replication;                            
   ```

1. Monitor how far the Aurora MySQL DB cluster is behind the MySQL replication master database\. To do so, connect to the Aurora MySQL DB cluster and run the following command\.

   ```
   SHOW SLAVE STATUS;                            
   ```

   In the command output, the `Seconds Behind Master` field shows how far the Aurora MySQL DB cluster is behind the MySQL master\. When this value is `0` \(zero\), the Aurora MySQL DB cluster has caught up to the master, and you can move on to the next step to stop replication\.

1. Connect to the MySQL replication master database and stop replication\. To do so, run the following command\.

   ```
   CALL mysql.rds_stop_replication;                            
   ```

## Migrating from MySQL to Amazon Aurora by Using mysqldump<a name="AuroraMySQL.Migrating.ExtMySQL.mysqldump"></a>

Because Amazon Aurora MySQL is a MySQL\-compatible database, you can use the `mysqldump` utility to copy data from your MySQL or MariaDB database to an existing Aurora MySQL DB cluster\. For a discussion of how to do so with MySQL databases that are very large, see [Importing Data to an Amazon RDS MySQL or MariaDB DB Instance with Reduced Downtime](MySQL.Procedural.Importing.NonRDSRepl.md)\. For MySQL databases that have smaller amounts of data, see [Importing Data from a MySQL or MariaDB DB to an Amazon RDS MySQL or MariaDB DB Instance](MySQL.Procedural.Importing.SmallExisting.md)\.

## <a name="AuroraMySQL.Migrating.ExtMySQL.RelatedTopics"></a>