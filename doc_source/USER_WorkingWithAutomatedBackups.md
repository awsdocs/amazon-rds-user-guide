# Working With Backups<a name="USER_WorkingWithAutomatedBackups"></a>

Amazon RDS creates and saves automated backups of your DB instance\. Amazon RDS creates a storage volume snapshot of your DB instance, backing up the entire DB instance and not just individual databases\. 

Amazon RDS creates automated backups of your DB instance during the backup window of your DB instance\. Amazon RDS saves the automated backups of your DB instance according to the backup retention period that you specify\. If necessary, you can recover your database to any point in time during the backup retention period\. 

Automated backups follow these rules:
+ Your DB instance must be in the `ACTIVE` state for automated backups to occur\. Automated backups don't occur while your DB instance is in a state other than `ACTIVE`, for example `STORAGE_FULL`\.
+ Automated backups and automated snapshots don't occur while a copy is executing in the same region for the same DB instance\.

You can also backup your DB instance manually, by manually creating a DB snapshot\. For more information about creating a DB snapshot, see [Creating a DB Snapshot](USER_CreateSnapshot.md)\. 

The first snapshot of a DB instance contains the data for the full DB instance\. Subsequent snapshots of the same DB instance are incremental, which means that only the data that has changed after your most recent snapshot is saved\.

You can copy both automatic and manual DB snapshots, and share manual DB snapshots\. For more information about copying a DB snapshot, see [Copying a Snapshot](USER_CopySnapshot.md)\. For more information about sharing a DB snapshot, see [Sharing a DB Snapshot](USER_ShareSnapshot.md)\.

## Backup Storage<a name="USER_WorkingWithAutomatedBackups.BackupStorage"></a>

Your Amazon RDS backup storage for each region is composed of the automated backups and manual DB snapshots for that region\. Your backup storage is equivalent to the sum of the database storage for all instances in that region\. Moving a DB snapshot to another region increases the backup storage in the destination region\. 

For more information about backup storage costs, see [Amazon RDS Pricing](https://aws.amazon.com/rds/pricing/)\. 

All automated backups are deleted when you delete a DB instance\. After you delete a DB instance, the automated backups can't be recovered\. If you choose to have Amazon RDS create a final DB snapshot before it deletes your DB instance, you can use that to recover your DB instance\. 

Manual snapshots are not deleted\. 

## The Backup Window<a name="USER_WorkingWithAutomatedBackups.BackupWindow"></a>

Automated backups occur daily during the preferred backup window\. If the backup requires more time than allotted to the backup window, the backup continues after the window ends, until it finishes\. The backup window can't overlap with the weekly maintenance window for the DB instance\. 

During the automatic backup window, storage I/O might be suspended briefly while the backup process initializes \(typically under a few seconds\)\. You may experience elevated latencies for a few minutes during backups for Multi\-AZ deployments\.  For MariaDB, MySQL, Oracle, and PostgreSQL, I/O activity is not suspended on your primary during backup for Multi\-AZ deployments, because the backup is taken from the standby\. For SQL Server, I/O activity is suspended briefly during backup for Multi\-AZ deployments\. 

If you don't specify a preferred backup window when you create the DB instance, Amazon RDS assigns a default 30\-minute backup window which is selected at random from an 8\-hour block of time per region\. The following table lists the time blocks for each region from which the default backups windows are assigned\. 


****  
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithAutomatedBackups.html)

## The Backup Retention Period<a name="USER_WorkingWithAutomatedBackups.BackupRetention"></a>

You can set the backup retention period when you create a DB instance\. If you don't set the backup retention period, the default backup retention period is one day if you create the DB instance using the Amazon RDS API or the AWS CLI, or seven days if you create the DB instance using the AWS Console\. After you create a DB instance, you can modify the backup retention period\. You can set the backup retention period to between 0 and 35 days\. Setting the backup retention period to 0 disables automated backups\. Manual snapshot limits \(100 per region\) do not apply to automated backups\. 

**Important**  
An outage occurs if you change the backup retention period from 0 to a non\-zero value or from a non\-zero value to 0\. 

## Disabling Automated Backups<a name="USER_WorkingWithAutomatedBackups.Disabling"></a>

You may want to temporarily disable automated backups in certain situations; for example, while loading large amounts of data\. 

**Important**  
We highly discourage disabling automated backups because it disables point\-in\-time recovery\. Disabling automatic backups for a DB instance deletes all existing automated backups for the instance\. If you disable and then re\-enable automated backups, you are only able to restore starting from the time you re\-enabled automated backups\. 

In this example, you disable automated backups for a DB instance named *mydbinstance* by setting the backup retention parameter to 0\. 

### AWS Management Console<a name="USER_WorkingWithAutomatedBackups.Disabling.CON"></a>

**To disable automated backups immediately**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **DB Instances**, and then select the DB instance that you want to modify\. 

1. Choose **Instance Actions**, and then choose **Modify**\. The **Modify DB Instance** window appears\.

1. For **Backup Retention Period**, choose **0**\. 

1. Select **Apply Immediately**\.

1. Choose **Continue**\. 

1. On the confirmation page, choose **Modify DB Instance** to save your changes and disable automated backups\. 

### CLI<a name="USER_WorkingWithAutomatedBackups.Disabling.CLI"></a>

To disable automated backups immediately, use the [modify\-db\-instance](http://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) command and set the backup retention period to 0 with `--apply-immediately`\. 

**Example**  
The following example immediately disabled automatic backups\.  
For Linux, OS X, or Unix:  

```
1. aws rds modify-db-instance \
2.     --db-instance-identifier mydbinstance \
3.     --backup-retention-period 0 \
4.     --apply-immediately
```
For Windows:  

```
1. aws rds modify-db-instance ^
2.     --db-instance-identifier mydbinstance ^
3.     --backup-retention-period 0 ^
4.     --apply-immediately
```

To know when the modification is in effect, call `describe-db-instances` for the DB instance until the value for backup retention period is 0 and mydbinstance status is available\.

```
1. aws rds describe-db-instances --db-instance-identifier mydbinstance
```

### API<a name="USER_WorkingWithAutomatedBackups.Disabling.API"></a>

To disable automated backups immediately, call the [ModifyDBInstance](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) action with the following parameters: 
+ `DBInstanceIdentifier = mydbinstance`
+ `BackupRetentionPeriod = 0`

**Example**  

```
1. https://rds.amazonaws.com/
2.     ?Action=ModifyDBInstance
3.     &DBInstanceIdentifier=mydbinstance
4.     &BackupRetentionPeriod=0
5.     &SignatureVersion=2
6.     &SignatureMethod=HmacSHA256
7.     &Timestamp=2009-10-14T17%3A48%3A21.746Z
8.     &AWSAccessKeyId=<AWS Access Key ID>
9.     &Signature=<Signature>
```

## Enabling Automated Backups<a name="USER_WorkingWithAutomatedBackups.Enabling"></a>

If your DB instance doesn't have automated backups enabled, you can enable them at any time\. You enable automated backups by setting the backup retention period to a positive non\-zero value\. When automated backups are enabled, your RDS instance and database is taken offline and a backup is immediately created\. 

In this example, you enable automated backups for a DB instance named *mydbinstance* by setting the backup retention period to a positive non\-zero value \(in this case, 3\)\. 

### AWS Management Console<a name="USER_WorkingWithAutomatedBackups.Enabling.CON"></a>

**To enable automated backups immediately**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **DB Instances**, and then select the DB instance that you want to modify\. 

1. Choose **Instance Actions**, and then choose **Modify**\. The **Modify DB Instance** page appears\.

1. For **Backup Retention Period**, choose a positive non\-zero value, for example 3\. 

1. Select **Apply Immediately**\.

1. Choose **Continue**\. 

1. On the confirmation page, choose **Modify DB Instance** to save your changes and enable automated backups\. 

### CLI<a name="USER_WorkingWithAutomatedBackups.Enabling.CLI"></a>

To enable automated backups immediately, use the AWS CLI [http://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html](http://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) command\.

In this example, we will enable automated backups by setting the backup retention period to 3 days\.

Include the following parameters:
+ `--db-instance-identifier`
+ `--backup-retention-period`
+ `--apply-immediately` or `--no-apply-immediately`

**Example**  
For Linux, OS X, or Unix:  

```
1. aws rds modify-db-instance \
2.     --db-instance-identifier mydbinstance  \
3.     --backup-retention-period 3 \
4.     --apply-immediately
```
For Windows:  

```
1. aws rds modify-db-instance ^
2.     --db-instance-identifier mydbinstance  ^
3.     --backup-retention-period 3 ^
4.     --apply-immediately
```

### API<a name="USER_WorkingWithAutomatedBackups.Enabling.API"></a>

To enable automated backups immediately, use the AWS CLI [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) command\.

In this example, we will enable automated backups by setting the backup retention period to 3 days\.

Include the following parameters:
+ `DBInstanceIdentifier`
+ `BackupRetentionPeriod`
+ `ApplyImmediately = true`

**Example**  

```
 1. https://rds.amazonaws.com/
 2.  ?Action=ModifyDBInstance
 3.  &DBInstanceIdentifier=mydbinstance
 4.  &BackupRetentionPeriod=3
 5.  &ApplyImmediately=true
 6.  &SignatureVersion=2
 7.  &SignatureMethod=HmacSHA256
 8.  &Timestamp=2009-10-14T17%3A48%3A21.746Z
 9.  &AWSAccessKeyId=<AWS Access Key ID>
10.  &Signature=<Signature>
```

## Automated Backups with Unsupported MySQL Storage Engines<a name="Overview.BackupDeviceRestrictions"></a>

For the MySQL DB engine, automated backups are only supported for the InnoDB storage engine; use of these features with other MySQL storage engines, including MyISAM, may lead to unreliable behavior while restoring from backups\. Specifically, since storage engines like MyISAM do not support reliable crash recovery, your tables can be corrupted in the event of a crash\. For this reason, we encourage you to use the InnoDB storage engine\. 
+ To convert existing MyISAM tables to InnoDB tables, you can use alter table command\. For example: `ALTER TABLE table_name ENGINE=innodb, ALGORITHM=COPY;` 
+ If you choose to use MyISAM, you can attempt to manually repair tables that become damaged after a crash by using the REPAIR command \(see: [http://dev\.mysql\.com/doc/refman/5\.5/en/repair\-table\.html](http://dev.mysql.com/doc/refman/5.5/en/repair-table.html)\)\. However, as noted in the MySQL documentation, there is a good chance that you will not be able to recover all your data\. 
+ If you want to take a snapshot of your MyISAM tables prior to restoring, follow these steps: 

  1. Stop all activity to your MyISAM tables \(that is, close all sessions\)\. 

     You can close all sessions by calling the [mysql\.rds\_kill](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Appendix.MySQL.CommonDBATasks.html) command for each process that is returned from the `SHOW FULL PROCESSLIST` command\. 

  1. Lock and flush each of your MyISAM tables\. For example, the following commands lock and flush two tables named `myisam_table1` and `myisam_table2`: 

     ```
     mysql> FLUSH TABLES myisam_table, myisam_table2 WITH READ LOCK;
     ```

  1. Create a snapshot of your DB instance\. When the snapshot has completed, release the locks and resume activity on the MyISAM tables\. You can release the locks on your tables using the following command: 

     ```
     mysql> UNLOCK TABLES;
     ```

  These steps force MyISAM to flush data stored in memory to disk thereby ensuring a clean start when you restore from a DB snapshot\. For more information on creating a DB snapshot, see [Creating a DB Snapshot](USER_CreateSnapshot.md)\. 

## Automated Backups with Unsupported MariaDB Storage Engines<a name="Overview.BackupDeviceRestrictionsMariaDB"></a>

For the MariaDB DB engine, automated backups are only supported with the InnoDB storage engine \(version 10\.2 and later\) and XtraDB storage engine \(versions 10\.0 and 10\.1\)\. Use of these features with other MariaDB storage engines, including Aria, might lead to unreliable behavior while restoring from backups\. Even though Aria is a crash\-resistant alternative to MyISAM, your tables can still be corrupted in the event of a crash\. For this reason, we encourage you to use the XtraDB storage engine\. 
+ To convert existing Aria tables to InnoDB tables, you can use ALTER TABLE command\. For example: `ALTER TABLE table_name ENGINE=innodb, ALGORITHM=COPY;` 
+ To convert existing Aria tables to XtraDB tables, you can use ALTER TABLE command\. For example: `ALTER TABLE table_name ENGINE=xtradb, ALGORITHM=COPY;` 
+ If you choose to use Aria, you can attempt to manually repair tables that become damaged after a crash by using the REPAIR TABLE command\. For more information, see [http://mariadb\.com/kb/en/mariadb/repair\-table/](http://mariadb.com/kb/en/mariadb/repair-table/)\. 
+ If you want to take a snapshot of your Aria tables prior to restoring, follow these steps: 

  1. Stop all activity to your Aria tables \(that is, close all sessions\)\.

  1. Lock and flush each of your Aria tables\.

  1. Create a snapshot of your DB instance\. When the snapshot has completed, release the locks and resume activity on the Aria tables\. These steps force Aria to flush data stored in memory to disk, thereby ensuring a clean start when you restore from a DB snapshot\. 

## Related Topics<a name="USER_WorkingWithAutomatedBackups.related"></a>
+  [Restoring a DB Instance to a Specified Time](USER_PIT.md) 