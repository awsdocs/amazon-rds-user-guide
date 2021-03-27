# Instance store support for the tempdb database on Amazon RDS for SQL Server<a name="SQLServer.InstanceStore"></a>

An *instance store* provides temporary block\-level storage for your DB instance\. This storage is located on disks that are physically attached to the host computer\. These disks have Non\-Volatile Memory Express \(NVMe\) instance storage that is based on solid\-state drives \(SSDs\)\. This storage is optimized for low latency, very high random I/O performance, and high sequential read throughput\.

By placing `tempdb` data files and `tempdb` log files on the instance store, you can achieve lower read and write latencies compared to standard storage based on Amazon EBS\.

**Note**  
SQL Server database files and database log files aren't placed on the instance store\.

## Enabling the instance store<a name="SQLServer.InstanceStore.Enable"></a>

When RDS provisions DB instances with one of the following instance classes, the `tempdb` database is automatically placed onto the instance store:
+ db\.m5d
+ db\.r5d

To enable the instance store, do one of the following:
+ Create a SQL Server DB instance using one of these instance types\. For more information, see [Creating an Amazon RDS DB instance](USER_CreateDBInstance.md)\.
+ Modify an existing SQL Server DB instance to use one of them\. For more information, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\.

The instance store is available in all AWS Regions where one or more of these instance types are supported\. For more information on the `db.m5d` and `db.r5d` instance classes, see [DB instance classes](Concepts.DBInstanceClass.md)\. For more information on the instance classes supported by Amazon RDS for SQL Server, see [DB instance class support for Microsoft SQL Server](CHAP_SQLServer.md#SQLServer.Concepts.General.InstanceClasses)\.

## File location and size considerations<a name="SQLServer.InstanceStore.Files"></a>

On instances without an instance store, RDS stores the `tempdb` data and log files in the `D:\rdsdbdata\DATA` directory\. Both files start at 8 MB by default\.

On instances with an instance store, RDS stores the `tempdb` data and log files in the `T:\rdsdbdata\DATA` directory\.

When `tempdb` has only one data file \(`tempdb.mdf`\) and one log file \(`templog.ldf`\), `templog.ldf` starts at 8 MB by default and `tempdb.mdf` starts at 80% or more of the instance's storage capacity\. Twenty percent of the storage capacity or 200 GB, whichever is less, is kept free to start\. Multiple `tempdb` data files split the 80% disk space evenly, while log files always have an 8\-MB initial size\.

For example, if you modify your DB instance class from `db.m5.2xlarge` to `db.m5d.2xlarge`, the size of `tempdb` data files increases from 8 MB each to 234 GB in total\.

**Note**  
Besides the `tempdb` data and log files on the instance store \(`T:\rdsdbdata\DATA`\), you can still create extra `tempdb` data and log files on the data volume \(`D:\rdsdbdata\DATA`\)\. Those files always have an 8 MB initial size\.

## Backup considerations<a name="SQLServer.InstanceStore.Backups"></a>

You might need to retain backups for long periods, incurring costs over time\. The `tempdb` data and log blocks can change very often depending on the workload\. This can greatly increase the DB snapshot size\.

When `tempdb` is on the instance store, snapshots don't include temporary files\. This means that snapshot sizes are smaller and consume less of the free backup allocation compared to EBS\-only storage\.

## Disk full errors<a name="SQLServer.InstanceStore.DiskFull"></a>

If you use all of the available space in the instance store, you might receive errors such as the following:
+ The transaction log for database 'tempdb' is full due to 'ACTIVE\_TRANSACTION'\.
+ Could not allocate space for object 'dbo\.SORT temporary run storage: 140738941419520' in database 'tempdb' because the 'PRIMARY' filegroup is full\. Create disk space by deleting unneeded files, dropping objects in the filegroup, adding additional files to the filegroup, or setting autogrowth on for existing files in the filegroup\.

You can do one or more of the following when the instance store is full:
+ Adjust your workload or the way you use `tempdb`\.
+ Scale up to use a DB instance class with more NVMe storage\.
+ Stop using the instance store, and use an instance class with only EBS storage\.
+ Use a mixed mode by adding secondary data or log files for `tempdb` on the EBS volume\.

## Removing the instance store<a name="SQLServer.InstanceStore.Disable"></a>

To remove the instance store, modify your SQL Server DB instance to use an instance type that doesn't support instance store, such as `db.m5` or `db.r5`\.

**Note**  
When you remove the instance store, the temporary files are moved to the `D:\rdsdbdata\DATA` directory and reduced in size to 8 MB\.