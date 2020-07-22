# Importing Data into a MySQL DB Instance<a name="MySQL.Procedural.Importing.Other"></a>

You can use several different techniques to import data into an Amazon RDS for MySQL DB instance\. The best approach depends on the source of the data, the amount of data, and whether the import is done one time or is ongoing\. If you are migrating an application along with the data, also consider the amount of downtime that you are willing to experience\.

## Overview<a name="MySQL.Procedural.Importing.Overview"></a>

Find techniques to import data into an Amazon RDS for MySQL DB instance in the following table\.


****  

| Source | Amount of Data | One Time or Ongoing | Application Downtime | Technique | More Information | 
| --- | --- | --- | --- | --- | --- | 
| Existing MySQL database on premises or on Amazon EC2 | Any | One time | Some | Create a backup of your on\-premises database, store it on Amazon S3, and then restore the backup file to a new Amazon RDS DB instance running MySQL\. | [Restoring a Backup into an Amazon RDS MySQL DB Instance](MySQL.Procedural.Importing.md) | 
| Any existing database | Any | One time or ongoing | Minimal | Use AWS Database Migration Service to migrate the database with minimal downtime and, for many database DB engines, continue ongoing replication\. | [ What is AWS Database Migration Service](https://docs.aws.amazon.com/dms/latest/userguide/Welcome.html) in the AWS Database Migration Service User Guide | 
| Existing Amazon RDS MySQL DB instance | Any | One time or ongoing | Minimal | Create a read replica for ongoing replication\. Promote the read replica for one\-time creation of a new DB instance\. | [Working with Read Replicas](USER_ReadRepl.md) | 
| Existing MySQL or MariaDB database | Small | One time | Some | Copy the data directly to your Amazon RDS MySQL DB instance using a command\-line utility\. | [Importing Data from a MySQL or MariaDB DB to an Amazon RDS MySQL or MariaDB DB Instance](MySQL.Procedural.Importing.SmallExisting.md) | 
| Data not stored in an existing database | Medium | One time | Some | Create flat files and import them using the mysqlimport utility\. | [Importing Data From Any Source to a MySQL or MariaDB DB Instance](MySQL.Procedural.Importing.AnySource.md) | 
| Existing MySQL or MariaDB database on premises or on Amazon EC2 | Any | Ongoing | Minimal | Configure replication with an existing MySQL database as the replication source\. | [Replication with a MySQL or MariaDB Instance Running External to Amazon RDS](MySQL.Procedural.Importing.External.Repl.md) or [Importing Data to an Amazon RDS MySQL or MariaDB DB Instance with Reduced Downtime](MySQL.Procedural.Importing.NonRDSRepl.md) | 

**Note**  
The `'mysql'` system database contains authentication and authorization information required to log in to your DB instance and access your data\. Dropping, altering, renaming, or truncating tables, data, or other contents of the `'mysql'` database in your DB instance can result in error and might render the DB instance and your data inaccessible\. If this occurs, you can restore the DB instance from a snapshot using the AWS CLI `restore-db-instance-from-db-snapshot` command\. You can recover the DB instance using the AWS CLI `restore-db-instance-to-point-in-time` command\. 

## Importing Data Considerations<a name="MySQL.Procedural.Importing.Advanced"></a>

Following, you can find additional technical information related to loading data into MySQL\. This information is intended for advanced users who are familiar with the MySQL server architecture\. All comments related to LOAD DATA LOCAL INFILE also apply to `mysqlimport`\. 

### Binary Log<a name="MySQL.Procedural.Importing.Advanced.Log"></a>

Data loads incur a performance penalty and require additional free disk space \(up to four times more\) when binary logging is enabled versus loading the same data with binary logging turned off\. The severity of the performance penalty and the amount of free disk space required is directly proportional to the size of the transactions used to load the data\. 

### Transaction Size<a name="MySQL.Procedural.Importing.Advanced.Size"></a>

Transaction size plays an important role in MySQL data loads\. It has a major influence on resource consumption, disk space utilization, resume process, time to recover, and input format \(flat files or SQL\)\. This section describes how transaction size affects binary logging and makes the case for disabling binary logging during large data loads\. As noted earlier, binary logging is enabled and disabled by setting the Amazon RDS automated backup retention period\. Non\-zero values enable binary logging, and zero disables it\. We also describe the impact of large transactions on InnoDB and why it's important to keep transaction sizes small\. 

#### Small Transactions<a name="MySQL.Procedural.Importing.Advanced.Log.Small"></a>

For small transactions, binary logging doubles the number of disk writes required to load the data\. This effect can severely degrade performance for other database sessions and increase the time required to load the data\. The degradation experienced depends in part upon the upload rate, other database activity taking place during the load, and the capacity of your Amazon RDS DB instance\.

The binary logs also consume disk space roughly equal to the amount of data loaded until they are backed up and removed\. Fortunately, Amazon RDS minimizes this by backing up and removing binary logs on a frequent basis\. 

#### Large Transactions<a name="MySQL.Procedural.Importing.Advanced.Log.Large"></a>

Large transactions incur a 3X penalty for IOPS and disk consumption with binary logging enabled\. This is due to the binary log cache spilling to disk, consuming disk space and incurring additional IO for each write\. The cache cannot be written to the binlog until the transaction commits or rolls back, so it consumes disk space in proportion to the amount of data loaded\. When the transaction commits, the cache must be copied to the binlog, creating a third copy of the data on disk\. 

Because of this, there must be at least three times as much free disk space available to load the data compared to loading with binary logging disabled\. For example, 10 GiB of data loaded as a single transaction consumes at least 30 GiB disk space during the load\. It consumes 10 GiB for the table \+ 10 GiB for the binary log cache \+ 10 GiB for the binary log itself\. The cache file remains on disk until the session that created it terminates or the session fills its binary log cache again during another transaction\. The binary log must remain on disk until backed up, so it might be some time before the extra 20 GiB is freed\. 

If the data was loaded using LOAD DATA LOCAL INFILE, yet another copy of the data is created if the database has to be recovered from a backup made before the load\. During recovery, MySQL extracts the data from the binary log into a flat file\. MySQL then runs LOAD DATA LOCAL INFILE, just as in the original transaction\. However, this time the input file is local to the database server\. Continuing with the example preceding, recovery fails unless there is at least 40 GiB free disk space available\.

#### Disable Binary Logging<a name="MySQL.Procedural.Importing.AnySource.Advanced.Disable"></a>

Whenever possible, disable binary logging during large data loads to avoid the resource overhead and addition disk space requirements\. In Amazon RDS, disabling binary logging is as simple as setting the backup retention period to zero\. If you do this, we recommend that you take a DB snapshot of the database instance immediately before the load\. By doing this, you can quickly and easily undo changes made during loading if you need to\. 

After the load, set the backup retention period back to an appropriate \(no zero\) value\. 

You can't set the backup retention period to zero if the DB instance is a source DB instance for read replicas\.

### InnoDB<a name="MySQL.Procedural.Importing.Advanced.InnoDB"></a>

The information in this section provides a strong argument for keeping transaction sizes small when using InnoDB\.

#### Undo<a name="MySQL.Procedural.Importing.Advanced.InnoDB.Undo"></a>

InnoDB generates undo to support features such as transaction rollback and MVCC\. Undo is stored in the InnoDB system tablespace \(usually ibdata1\) and is retained until removed by the purge thread\. The purge thread cannot advance beyond the undo of the oldest active transaction, so it is effectively blocked until the transaction commits or completes a rollback\. If the database is processing other transactions during the load, their undo also accumulates in the system tablespace and cannot be removed even if they commit and no other transaction needs the undo for MVCC\. In this situation, all transactions \(including read\-only transactions\) that access any of the rows changed by any transaction \(not just the load transaction\) slow down\. The slowdown occurs because transactions scan through undo that could have been purged if not for the long\-running load transaction\. 

Undo is stored in the system tablespace, and the system tablespace never shrinks in size\. Thus, large data load transactions can cause the system tablespace to become quite large, consuming disk space that you can't reclaim without recreating the database from scratch\. 

#### Rollback<a name="MySQL.Procedural.Importing.Advanced.InnoDB.Rollback"></a>

InnoDB is optimized for commits\. Rolling back a large transaction can take a very, very long time\. In some cases, it might be faster to perform a point\-in\-time recovery or restore a DB snapshot\. 

### Input Data Format<a name="MySQL.Procedural.Importing.Advanced.InputFormat"></a>

MySQL can accept incoming data in one of two forms: flat files and SQL\. This section points out some key advantages and disadvantages of each\.

#### Flat Files<a name="MySQL.Procedural.Importing.Advanced.InputFormat.FlatFiles"></a>

Loading flat files with LOAD DATA LOCAL INFILE can be the fastest and least costly method of loading data as long as transactions are kept relatively small\. Compared to loading the same data with SQL, flat files usually require less network traffic, lowering transmission costs and load much faster due to the reduced overhead in the database\. 

#### One Big Transaction<a name="MySQL.Procedural.Importing.Advanced.InputFormat.BigTransaction"></a>

LOAD DATA LOCAL INFILE loads the entire flat file as one transaction\. This isn't necessarily a bad thing\. If the size of the individual files can be kept small, this has a number of advantages:
+ Resume capability – Keeping track of which files have been loaded is easy\. If a problem arises during the load, you can pick up where you left off with little effort\. Some data might have to be retransmitted to Amazon RDS, but with small files, the amount retransmitted is minimal\.
+ Load data in parallel – If you've got IOPS and network bandwidth to spare with a single file load, loading in parallel might save time\.
+ Throttle the load rate – Data load having a negative impact on other processes? Throttle the load by increasing the interval between files\. 

#### Be Careful<a name="MySQL.Procedural.Importing.Advanced.InputFormat.Careful"></a>

The advantages of LOAD DATA LOCAL INFILE diminish rapidly as transaction size increases\. If breaking up a large set of data into smaller ones isn't an option, SQL might be the better choice\. 

#### SQL<a name="MySQL.Procedural.Importing.Advanced.InputFormat.SQL"></a>

SQL has one main advantage over flat files: it's easy to keep transaction sizes small\. However, SQL can take significantly longer to load than flat files and it can be difficult to determine where to resume the load after a failure\. For example, mysqldump files are not restartable\. If a failure occurs while loading a mysqldump file, the file requires modification or replacement before the load can resume\. The alternative is to restore to the point in time before the load and replay the file after the cause of the failure has been corrected\. 

### Take Checkpoints Using Amazon RDS Snapshots<a name="MySQL.Procedural.Importing.Advanced.Checkpoints"></a>

If you have a load that's going to take several hours or even days, loading without binary logging isn't a very attractive prospect unless you can take periodic checkpoints\. This is where the Amazon RDS DB snapshot feature comes in very handy\. A DB snapshot creates a point\-in\-time consistent copy of your database instance which can be used restore the database to that point in time after a crash or other mishap\. 

To create a checkpoint, simply take a DB snapshot\. Any previous DB snapshots taken for checkpoints can be removed without affecting durability or restore time\. 

Snapshots are fast too, so frequent checkpointing doesn't add significantly to load time\. 

### Decreasing Load Time<a name="MySQL.Procedural.Importing.Advanced.LoadTime"></a>

Here are some additional tips to reduce load times: 
+ Create all secondary indexes before loading\. This is counter\-intuitive for those familiar with other databases\. Adding or modifying a secondary index causes MySQL to create a new table with the index changes, copy the data from the existing table to the new table, and drop the original table\. 
+ Load data in PK order\. This is particularly helpful for InnoDB tables, where load times can be reduced by 75–80 percent and data file size cut in half\. 
+ Disable foreign key constraints foreign\_key\_checks=0\. For flat files loaded with LOAD DATA LOCAL INFILE, this is required in many cases\. For any load, disabling FK checks provides significant performance gains\. Just be sure to enable the constraints and verify the data after the load\. 
+ Load in parallel unless already near a resource limit\. Use partitioned tables when appropriate\. 
+ Use multi\-value inserts when loading with SQL to minimize overhead when running statements\. When using mysqldump, this is done automatically\.
+ Reduce InnoDB log IO innodb\_flush\_log\_at\_trx\_commit=0 
+ If you are loading data into a DB instance that does not have read replicas, set the sync\_binlog parameter to 0 while loading data\. When data loading is complete, set the sync\_binlog parameter to back to 1\.
+ Load data before converting the DB instance to a Multi\-AZ deployment\. However, if the DB instance already uses a Multi\-AZ deployment, switching to a Single\-AZ deployment for data loading is not recommended, because doing so only provides marginal improvements\.

**Note**  
Using innodb\_flush\_log\_at\_trx\_commit=0 causes InnoDB to flush its logs every second instead of at each commit\. This provides a significant speed advantage, but can lead to data loss during a crash\. Use with caution\. 

**Topics**
+ [Overview](#MySQL.Procedural.Importing.Overview)
+ [Importing Data Considerations](#MySQL.Procedural.Importing.Advanced)
+ [Restoring a Backup into an Amazon RDS MySQL DB Instance](MySQL.Procedural.Importing.md)
+ [Importing Data from a MySQL or MariaDB DB to an Amazon RDS MySQL or MariaDB DB Instance](MySQL.Procedural.Importing.SmallExisting.md)
+ [Importing Data to an Amazon RDS MySQL or MariaDB DB Instance with Reduced Downtime](MySQL.Procedural.Importing.NonRDSRepl.md)
+ [Importing Data From Any Source to a MySQL or MariaDB DB Instance](MySQL.Procedural.Importing.AnySource.md)