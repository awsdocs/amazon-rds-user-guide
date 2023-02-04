# Restoring from a DB snapshot<a name="USER_RestoreFromSnapshot"></a><a name="restore_snapshot"></a>

Amazon RDS creates a storage volume snapshot of your DB instance, backing up the entire DB instance and not just individual databases\. You can create a new DB instance by restoring from a DB snapshot\. You provide the name of the DB snapshot to restore from, and then provide a name for the new DB instance that is created from the restore\. You can't restore from a DB snapshot to an existing DB instance; a new DB instance is created when you restore\. 

You can use the restored DB instance as soon as its status is `available`\. The DB instance continues to load data in the background\. This is known as *lazy loading*\.

If you access data that hasn't been loaded yet, the DB instance immediately downloads the requested data from Amazon S3, and then continues loading the rest of the data in the background\. For more information, see [Amazon EBS snapshots](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSSnapshots.html)\.

To help mitigate the effects of lazy loading on tables to which you require quick access, you can perform operations that involve full\-table scans, such as `SELECT *`\. This allows Amazon RDS to download all of the backed\-up table data from S3\.

You can restore a DB instance and use a different storage type than the source DB snapshot\. In this case, the restoration process is slower because of the additional work required to migrate the data to the new storage type\. If you restore to or from magnetic storage, the migration process is the slowest\. That's because magnetic storage doesn't have the IOPS capability of Provisioned IOPS or General Purpose \(SSD\) storage\.

You can use AWS CloudFormation to restore a DB instance from a DB instance snapshot\. For more information, see [AWS::RDS::DBInstance](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-rds-dbinstance.html) in the *AWS CloudFormation User Guide*\.

**Note**  
You can't restore a DB instance from a DB snapshot that is both shared and encrypted\. Instead, you can make a copy of the DB snapshot and restore the DB instance from the copy\. For more information, see [Copying a DB snapshot](USER_CopySnapshot.md)\.

## Parameter group considerations<a name="USER_RestoreFromSnapshot.Parameters"></a>

We recommend that you retain the DB parameter group for any DB snapshots you create, so that you can associate your restored DB instance with the correct parameter group\.

The default DB parameter group is associated with the restored instance, unless you choose a different one\. No custom parameter settings are available in the default parameter group\.

You can specify the parameter group when you restore the DB instance\.

For more information about DB parameter groups, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\.

## Security group considerations<a name="USER_RestoreFromSnapshot.Security"></a>

When you restore a DB instance, the default virtual private cloud \(VPC\), DB subnet group, and VPC security group are associated with the restored instance, unless you choose different ones\.
+ If you're using the Amazon RDS console, you can specify a custom VPC security group to associate with the instance or create a new VPC security group\.
+ If you're using the AWS CLI, you can specify a custom VPC security group to associate with the instance by including the `--vpc-security-group-ids` option in the `restore-db-instance-from-db-snapshot` command\.
+ If you're using the Amazon RDS API, you can include the `VpcSecurityGroupIds.VpcSecurityGroupId.N` parameter in the `RestoreDBInstanceFromDBSnapshot` action\.

As soon as the restore is complete and your new DB instance is available, you can also change the VPC settings by modifying the DB instance\. For more information, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\.

## Option group considerations<a name="USER_RestoreFromSnapshot.Options"></a>

When you restore a DB instance, the default DB option group is associated with the restored DB instance in most cases\.

The exception is when the source DB instance is associated with an option group that contains a persistent or permanent option\. For example, if the source DB instance uses Oracle Transparent Data Encryption \(TDE\), the restored DB instance must use an option group that has the TDE option\.

If you restore a DB instance into a different VPC, you must do one of the following to assign a DB option group:
+ Assign the default option group for that VPC group to the instance\.
+ Assign another option group that is linked to that VPC\.
+ Create a new option group and assign it to the DB instance\. With persistent or permanent options, such as Oracle TDE, you must create a new option group that includes the persistent or permanent option\.

For more information about DB option groups, see [Working with option groups](USER_WorkingWithOptionGroups.md)\.

## Resource tagging considerations<a name="restore-from-snapshot.tagging"></a>

When you restore a DB instance from a DB snapshot, RDS checks whether you specify new tags\. If yes, the new tags are added to the restored DB instance\. If there are no new tags, RDS adds the tags from the source DB instance at the time of snapshot creation to the restored DB instance\.

For more information, see [Copying tags to DB instance snapshots](USER_Tagging.md#USER_Tagging.CopyTags)\.

## Microsoft SQL Server considerations<a name="USER_RestoreFromSnapshot.MSSQL"></a>

When you restore an RDS for Microsoft SQL Server DB snapshot to a new instance, you can always restore to the same edition as your snapshot\. In some cases, you can also change the edition of the DB instance\. The following limitations apply when you change editions:
+ The DB snapshot must have enough storage allocated for the new edition\.
+ Only the following edition changes are supported:
  + From Standard Edition to Enterprise Edition
  + From Web Edition to Standard Edition or Enterprise Edition
  + From Express Edition to Web Edition, Standard Edition, or Enterprise Edition

If you want to change from one edition to a new edition that isn't supported by restoring a snapshot, you can try using the native backup and restore feature\. SQL Server verifies whether your database is compatible with the new edition based on what SQL Server features you have enabled on the database\. For more information, see [Importing and exporting SQL Server databases using native backup and restore](SQLServer.Procedural.Importing.md)\.

## Oracle Database considerations<a name="USER_RestoreFromSnapshot.Oracle"></a>

If you use Oracle GoldenGate, always retain the parameter group with the `compatible` parameter\. When you restore a DB instance from a DB snapshot, specify a parameter group that has a matching or greater `compatible` value\.

If you restore a snapshot of a CDB instance, you can change the PDB name\. You can't change the CDB name, which is always `RDSCDB`\. This CDB name is the same for all RDS instances that use a single\-tenant architecture\. For more information, see [Snapshots in a single\-tenant architecture](Oracle.Concepts.single-tenant.md#Oracle.Concepts.single-tenant.snapshots)\.

Before you restore a DB snapshot, you can upgrade it to a later release\. For more information, see [Upgrading an Oracle DB snapshot](USER_UpgradeDBSnapshot.Oracle.md)\.

## Restoring from a snapshot<a name="USER_RestoreFromSnapshot.Restoring"></a>

You can restore a DB instance from a DB snapshot using the AWS Management Console, the AWS CLI, or the RDS API\.

**Note**  
You can't reduce the amount of storage when you restore a DB instance\. When you increase the allocated storage, it must be by at least 10 percent\. If you try to increase the value by less than 10 percent, you get an error\. You can't increase the allocated storage when restoring RDS for SQL Server DB instances\.

### Console<a name="USER_RestoreFromSnapshot.CON"></a>

**To restore a DB instancefrom a DB snapshot**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Snapshots**\.

1. Choose the DB snapshot that you want to restore from\.

1. For **Actions**, choose **Restore snapshot**\.

1. On the **Restore snapshot** page, for **DB instance identifier**, enter the name for your restored DB instance\.

1. Specify other settings, such as allocated storage size\.

   For information about each setting, see [Settings for DB instances](USER_CreateDBInstance.md#USER_CreateDBInstance.Settings)\.

1. Choose **Restore DB instance**\. 

### AWS CLI<a name="USER_RestoreFromSnapshot.CLI"></a>

To restore a DB instance from a DB snapshot, use the AWS CLI command [restore\-db\-instance\-from\-db\-snapshot](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-from-db-snapshot.html)\. 

In this example, you restore from a previously created DB snapshot named `mydbsnapshot`\. You restore to a new DB instance named `mynewdbinstance`\. This example also sets the allocated storage size\.

You can specify other settings\. For information about each setting, see [Settings for DB instances](USER_CreateDBInstance.md#USER_CreateDBInstance.Settings)\.

**Example**  
For Linux, macOS, or Unix:  
   

```
1. aws rds restore-db-instance-from-db-snapshot \
2.     --db-instance-identifier mynewdbinstance \
3.     --db-snapshot-identifier mydbsnapshot \
4.     --allocated-storage 100
```
For Windows:  
   

```
1. aws rds restore-db-instance-from-db-snapshot ^
2.     --db-instance-identifier mynewdbinstance ^
3.     --db-snapshot-identifier mydbsnapshot ^
4.     --allocated-storage 100
```
This command returns output similar to the following:  

```
1. DBINSTANCE  mynewdbinstance  db.t3.small  MySQL     50       sa              creating  3  n  8.0.28  general-public-license
```

### RDS API<a name="USER_RestoreFromSnapshot.API"></a>

To restore a DB instance from a DB snapshot, call the Amazon RDS API function [RestoreDBInstanceFromDBSnapshot](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBInstanceFromDBSnapshot.html) with the following parameters: 
+ `DBInstanceIdentifier` 
+ `DBSnapshotIdentifier` 