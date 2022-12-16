# Importing data into Oracle on Amazon RDS<a name="Oracle.Procedural.Importing"></a>

How you import data into an Amazon RDS for Oracle DB instance depends on the following: 
+ The amount of data you have
+ The number of database objects in your database
+ The variety of database objects in your database

For example, you can use the following tools, depending on your requirements:
+ Oracle SQL Developer – Import a simple, 20 MB database\.
+ Oracle Data Pump – Import complex databases, or databases that are several hundred megabytes or several terabytes in size\. You can use Amazon S3 in this task\. For example, download Data Pump files from Amazon S3 to the DB instance\. For more information, see [Amazon S3 integration](oracle-s3-integration.md)\.
+ AWS Database Migration Service \(AWS DMS\) – Migrate databases without downtime\. For more information about AWS DMS, see [ What is AWS Database Migration Service](https://docs.aws.amazon.com/dms/latest/userguide/Welcome.html) and the blog post [Migrating Oracle databases with near\-zero downtime using AWS DMS](http://aws.amazon.com/blogs/database/migrating-oracle-databases-with-near-zero-downtime-using-aws-dms/)\.

**Important**  
Before you use the preceding migration techniques, we recommend that you back up your database\. After you import the data, you can back up your RDS for Oracle DB instances by creating snapshots\. Later, you can restore the snapshots\. For more information, see [Backing up and restoring an Amazon RDS DB instance](CHAP_CommonTasks.BackupRestore.md)\.

For many database engines, ongoing replication can continue until you are ready to switch over to the target database\. You can use AWS DMS to migrate to RDS for Oracle from either the same database engine or a different engine\. If you migrate from a different database engine, you can use the AWS Schema Conversion Tool to migrate schema objects that AWS DMS doesn't migrate\.

**Topics**
+ [Importing using Oracle SQL Developer](Oracle.Procedural.Importing.SQLDeveloper.md)
+ [Importing using Oracle Data Pump](Oracle.Procedural.Importing.DataPump.md)
+ [Importing using Oracle Export/Import](Oracle.Procedural.Importing.ExportImport.md)
+ [Importing using Oracle SQL\*Loader](Oracle.Procedural.Importing.SQLLoader.md)
+ [Migrating with Oracle materialized views](Oracle.Procedural.Importing.Materialized.md)