# Migrating Data to an Amazon Aurora MySQL DB Cluster<a name="AuroraMySQL.Migrating"></a>

You have several options for migrating data from your existing database to an Amazon Aurora MySQL DB cluster\. Your migration options also depend on the database that you are migrating from and the size of the data that you are migrating\.

There are two different types of migration: physical and logical\. Physical migration means that physical copies of database files are used to migrate the database\. Logical migration means that the migration is accomplished by applying logical database changes, such as inserts, updates, and deletes\.

Physical migration has the following advantages:
+ Physical migration is faster than logical migration, especially for large databases\.
+ Database performance does not suffer when a backup is taken for physical migration\.
+ Physical migration can migrate everything in the source database, including complex database components\.

Physical migration has the following limitations:
+ The `innodb_page_size` parameter must be set to its default value \(`16KB`\)\.
+ The `innodb_data_file_path` must be configured with the default data file name `"ibdata1"`\. The following are examples of file names that are not allowed: `"innodb_data_file_path=ibdata1:50M; ibdata2:50M:autoextend"` and `"innodb_data_file_path=ibdata01:50M:autoextend"`\.
+ The `innodb_log_files_in_group` parameter must be set to its default value \(`2`\)\.

Logical migration has the following advantages:
+ You can migrate subsets of the database, such as specific tables or parts of a table\.
+ The data can be migrated regardless of the physical storage structure\.

Logical migration has the following limitations:
+ Logical migration is usually slower than physical migration\.
+ Complex database components can slow down the logical migration process\. In some cases, complex database components can even block logical migration\.

The following table describes your options and the type of migration for each option\.


| Migrating From | Migration Type | Solution | 
| --- | --- | --- | 
| An Amazon RDS MySQL DB instance | Physical |  You can migrate from an RDS MySQL DB instance by first creating an Aurora MySQL Read Replica of a MySQL DB instance\. When the replica lag between the MySQL DB instance and the Aurora MySQL Read Replica is 0, you can direct your client applications to read from the Aurora Read Replica and then stop replication to make the Aurora MySQL Read Replica a standalone Aurora MySQL DB cluster for reading and writing\. For details, see [Migrating Data from a MySQL DB Instance to an Amazon Aurora MySQL DB Cluster by Using an Aurora Read Replica](AuroraMySQL.Migrating.RDSMySQL.Replica.md)\.  | 
| An RDS MySQL DB instance | Physical |  You can migrate data directly from an Amazon RDS MySQL DB snapshot to an Amazon Aurora MySQL DB cluster\. For details, see [Migrating Data from a MySQL DB Instance to an Amazon Aurora MySQL DB Cluster by Using a DB Snapshot](AuroraMySQL.Migrating.RDSMySQL.md)\.  | 
| A MySQL database external to Amazon RDS | Logical |  You can create a dump of your data using the `mysqldump` utility, and then import that data into an existing Amazon Aurora MySQL DB cluster\. For details, see [Migrating from MySQL to Amazon Aurora by Using mysqldump](AuroraMySQL.Migrating.ExtMySQL.md#AuroraMySQL.Migrating.ExtMySQL.mysqldump)\.  | 
| A MySQL database external to Amazon RDS | Physical |  You can copy the backup files from your database to an Amazon Simple Storage Service \(Amazon S3\) bucket, and then restore an Amazon Aurora MySQL DB cluster from those files\. This option can be considerably faster than migrating data using `mysqldump`\. For details, see [Migrating Data from MySQL by Using an Amazon S3 Bucket](AuroraMySQL.Migrating.ExtMySQL.md#AuroraMySQL.Migrating.ExtMySQL.S3)\.  | 
| A MySQL database external to Amazon RDS | Logical |  You can save data from your database as text files and copy those files to an Amazon S3 bucket\. You can then load that data into an existing Aurora MySQL DB cluster using the `LOAD DATA FROM S3` MySQL command\. For more information, see [Loading Data into an Amazon Aurora MySQL DB Cluster from Text Files in an Amazon S3 Bucket](AuroraMySQL.Integrating.LoadFromS3.md)\.  | 
| A database that is not MySQL\-compatible | Logical |  You can use AWS Database Migration Service \(AWS DMS\) to migrate data from a database that is not MySQL\-compatible\. For more information on AWS DMS, see [What Is AWS Database Migration Service?](http://docs.aws.amazon.com/dms/latest/userguide/Welcome.html) | 

**Note**  
If you are migrating a MySQL database external to Amazon RDS, the migration options described in the table are supported only if your database supports the InnoDB or MyISAM tablespaces\.
If the MySQL database you are migrating to Aurora MySQL uses `memcached`, remove `memcached` before migrating it\.
