# Importing data into PostgreSQL on Amazon RDS<a name="PostgreSQL.Procedural.Importing"></a>

Suppose that you have an existing PostgreSQL deployment that you want to move to Amazon RDS\. The complexity of your task depends on the size of your database and the types of database objects that you're transferring\. For example, consider a database that contains datasets on the order of gigabytes, along with stored procedures and triggers\. Such a database is going to be more complicated than a simple database with only a few megabytes of test data and no triggers or stored procedures\. 

We recommend that you use native PostgreSQL database migration tools under the following conditions:
+ You have a homogeneous migration, where you are migrating from a database with the same database engine as the target database\.
+ You are migrating an entire database\.
+ The native tools allow you to migrate your system with minimal downtime\.

In most other cases, performing a database migration using AWS Database Migration Service \(AWS DMS\) is the best approach\. AWS DMS can migrate databases without downtime and, for many database engines, continue ongoing replication until you are ready to switch over to the target database\. You can migrate to either the same database engine or a different database engine using AWS DMS\. If you are migrating to a different database engine than your source database, you can use the AWS Schema Conversion Tool \(AWS SCT\)\. You use AWS SCT to migrate schema objects that are not migrated by AWS DMS\. For more information about AWS DMS, see [ What is AWS Database Migration Service?](https://docs.aws.amazon.com/dms/latest/userguide/Welcome.html)

Modify your DB parameter group to include the following settings *for your import only*\. You should test the parameter settings to find the most efficient settings for your DB instance size\. You also need to revert back to production values for these parameters after your import completes\.

Modify your DB instance settings to the following:
+ Disable DB instance backups \(set backup\_retention to 0\)\.
+ Disable Multi\-AZ\.

Modify your DB parameter group to include the following settings\. You should only use these settings when importing data\. You should test the parameter settings to find the most efficient settings for your DB instance size\. You also need to revert back to production values for these parameters after your import completes\.


| Parameter | Recommended value when importing | Description | 
| --- | --- | --- | 
|  `maintenance_work_mem`  |  524288, 1048576, 2097152, or 4194304 \(in KB\)\. These settings are comparable to 512 MB, 1 GB, 2 GB, and 4 GB\.  |  The value for this setting depends on the size of your host\. This parameter is used during CREATE INDEX statements and each parallel command can use this much memory\. Calculate the best value so that you don't set this value so high that you run out of memory\.  | 
|  `max_wal_size`  |  256 \(for version 9\.6\), 4096 \(for versions 10 and higher\)  |  Maximum size to let the WAL grow during automatic checkpoints\. Increasing this parameter can increase the amount of time needed for crash recovery\. This parameter replaces `checkpoint_segments` for PostgreSQL 9\.6 and later\. For PostgreSQL version 9\.6, this value is in 16 MB units\. For later versions, the value is in 1 MB units\. For example, in version 9\.6, 128 means 128 chunks that are each 16 MB in size\. In version 12\.4, 2048 means 2048 chunks that are each 1 MB in size\.  | 
|  `checkpoint_timeout`  |  1800  |  The value for this setting allows for less frequent WAL rotation\.  | 
|  `synchronous_commit`  |  Off  |  Disable this setting to speed up writes\. Turning this parameter off can increase the risk of data loss in the event of a server crash \(do not turn off FSYNC\)\.  | 
|  `wal_buffers`  |   8192  |  This is value is in 8 KB units\. This again helps your WAL generation speed  | 
|  `autovacuum`  |  0  |  Disable the PostgreSQL auto vacuum parameter while you are loading data so that it doesn't use resources  | 

Use the `pg_dump -Fc` \(compressed\) or `pg_restore -j` \(parallel\) commands with these settings\.

**Note**  
The PostgreSQL command `pg_dumpall` requires super\_user permissions that are not granted when you create a DB instance, so it cannot be used for importing data\.

**Topics**
+ [Importing a PostgreSQL database from an Amazon EC2 instance](PostgreSQL.Procedural.Importing.EC2.md)
+ [Using the \\copy command to import data to a table on a PostgreSQL DB instance](PostgreSQL.Procedural.Importing.Copy.md)
+ [Importing data from Amazon S3 into an RDS for PostgreSQL DB instance](USER_PostgreSQL.S3Import.md)
+ [Transporting PostgreSQL databases between DB instances](PostgreSQL.TransportableDB.md)