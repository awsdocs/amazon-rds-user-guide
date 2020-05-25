# Restoring from a DB Snapshot<a name="USER_RestoreFromSnapshot"></a>

Amazon RDS creates a storage volume snapshot of your DB instance, backing up the entire DB instance and not just individual databases\. You can create a DB instance by restoring from this DB snapshot\. When you restore the DB instance, you provide the name of the DB snapshot to restore from, and then provide a name for the new DB instance that is created from the restore\. You can't restore from a DB snapshot to an existing DB instance; a new DB instance is created when you restore\. 

You can restore a DB instance and use a different storage type than the source DB snapshot\. In this case, the restoration process is slower because of the additional work required to migrate the data to the new storage type\. If you restore to or from magnetic storage, the migration process is the slowest\. That's because magnetic storage doesn't have the IOPS capability of Provisioned IOPS or General Purpose \(SSD\) storage\.

**Note**  
You can't restore a DB instance from a DB snapshot that is both shared and encrypted\. Instead, you can make a copy of the DB snapshot and restore the DB instance from the copy\.

## Parameter Group Considerations<a name="USER_RestoreFromSnapshot.Parameters"></a>

We recommend that you retain the parameter group for any DB snapshots you create, so that you can associate your restored DB instance with the correct parameter group\. You can specify the parameter group when you restore the DB instance\. 

## Security Group Considerations<a name="USER_RestoreFromSnapshot.Security"></a>

When you restore a DB instance, the default security group is associated with the restored instance by default\.

**Note**  
If you're using the Amazon RDS console, you can specify a custom security group to associate with the instance or create a new VPC security group\.
If you're using the AWS CLI, you can specify a custom security group to associate with the instance by including the `--vpc-security-group-ids` option in the `restore-db-instance-from-db-snapshot` command\.
If you're using the Amazon RDS API, you can include the `VpcSecurityGroupIds.VpcSecurityGroupId.N` parameter in the `RestoreDBInstanceFromDBSnapshot` action\.

As soon as the restore is complete and your new DB instance is available, you can associate any custom security groups used by the snapshot you restored from\. You must apply these changes by modifying the DB instance with the RDS console, the AWS CLI `modify-db-instance` command, or the `ModifyDBInstance` Amazon RDS API operation\. For more information, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\.

## Option Group Considerations<a name="USER_RestoreFromSnapshot.Options"></a>

When you restore a DB instance, the option group associated with the DB snapshot is associated with the restored DB instance after it is created\. For example, if the DB snapshot you are restoring from uses Oracle Transparent Data Encryption, the restored DB instance will use the same option group\. 

When you assign an option group to a DB instance, the option group is also linked to the supported platform the DB instance is on, either VPC or EC2\-Classic \(non\-VPC\)\. If a DB instance is in a VPC, the option group associated with the DB instance is linked to that VPC\. This means that you can't use the option group assigned to a DB instance if you attempt to restore the instance into a different VPC or onto a different platform\. If you restore a DB instance into a different VPC or onto a different platform, you must either assign the default option group to the instance, assign an option group that is linked to that VPC or platform, or create a new option group and assign it to the DB instance\. For persistent or permanent options, when restoring a DB instance into a different VPC you must create a new option group that includes the persistent or permanent option\. 

## Microsoft SQL Server Considerations<a name="USER_RestoreFromSnapshot.MSSQL"></a>

When you restore a Microsoft SQL Server DB snapshot to a new instance, you can always restore to the same edition as your snapshot\. In some cases, you can also change the edition of the DB instance\. The following are the limitations when you change editions: 
+ The DB snapshot must have enough storage allocated for the new edition\. 
+ Only the following edition changes are supported: 
  + From Standard Edition to Enterprise Edition 
  + From Web Edition to Standard Edition or Enterprise Edition 
  + From Express Edition to Web Edition, Standard Edition or Enterprise Edition 

If you want to change from one edition to a new edition that is not supported by restoring a snapshot, you can try using the native backup and restore feature\. SQL Server verifies whether or not your database is compatible with the new edition based on what SQL Server features you have enabled on the database\. For more information, see [Importing and Exporting SQL Server Databases](SQLServer.Procedural.Importing.md)\. 

## Oracle Considerations<a name="USER_RestoreFromSnapshot.Oracle"></a>

If you use Oracle GoldenGate, always retain the parameter group with the `compatible` parameter\. When you restore a DB instance from a DB snapshot, you must specify the parameter group that has a matching or greater `compatible` parameter value\. 

You can upgrade a DB snapshot while it is still a DB snapshot, before you restore it\. For more information, see [Upgrading an Oracle DB Snapshot](USER_UpgradeDBSnapshot.Oracle.md)\. 

## Restoring from a Snapshot<a name="USER_RestoreFromSnapshot.Restoring"></a>

You can restore a DB instance from a DB snapshot using the AWS Management Console, the AWS CLI, or the RDS API\.

### Console<a name="USER_RestoreFromSnapshot.CON"></a>

**To restore a DB instance from a DB snapshot**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Snapshots**\.

1. Choose the DB snapshot that you want to restore from\. 

1. For **Actions**, choose **Restore Snapshot**\. 

1. On the **Restore DB Instance** page, for **DB Instance Identifier**, enter the name for your restored DB instance\. 

1. Choose **Restore DB Instance**\. 

### AWS CLI<a name="USER_RestoreFromSnapshot.CLI"></a>

To restore a DB instance from a DB snapshot, use the AWS CLI command [restore\-db\-instance\-from\-db\-snapshot](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-from-db-snapshot.html)\. 

In this example, you restore from a previously created DB snapshot named *mydbsnapshot*\. You restore to a new DB instance named *mynewdbinstance*\. 

**Example**  
For Linux, macOS, or Unix:  

```
1. aws rds restore-db-instance-from-db-snapshot \
2.     --db-instance-identifier mynewdbinstance \
3.     --db-snapshot-identifier mydbsnapshot
```
For Windows:  

```
1. aws rds restore-db-instance-from-db-snapshot ^
2.     --db-instance-identifier mynewdbinstance ^
3.     --db-snapshot-identifier mydbsnapshot
```
This command returns output similar to the following:  

```
1. DBINSTANCE  mynewdbinstance  db.m3.large  MySQL     50       sa              creating  3  n  5.6.40  general-public-license
```

### RDS API<a name="USER_RestoreFromSnapshot.API"></a>

To restore a DB instance from a DB snapshot, call the Amazon RDS API function [RestoreDBInstanceFromDBSnapshot](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBInstanceFromDBSnapshot.html) with the following parameters: 
+ `DBInstanceIdentifier` 
+ `DBSnapshotIdentifier` 