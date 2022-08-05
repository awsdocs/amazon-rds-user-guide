# MySQL feature support on Amazon RDS<a name="MySQL.Concepts.FeatureSupport"></a>

RDS for MySQL supports most of the features and capabilities of MySQL\. Some features might have limited support or restricted privileges\.

You can filter new Amazon RDS features on the [What's New with Database?](http://aws.amazon.com/about-aws/whats-new/database/) page\. For **Products**, choose **Amazon RDS**\. Then search using keywords such as **MySQL 2022**\.

**Note**  
The following lists are not exhaustive\.

**Topics**
+ [Supported storage engines for RDS for MySQL](#MySQL.Concepts.Storage)
+ [Using memcached and other options with MySQL on Amazon RDS](#MySQL.Concepts.General.Options)
+ [InnoDB cache warming for MySQL on Amazon RDS](#MySQL.Concepts.InnoDBCacheWarming)
+ [MySQL features not supported by Amazon RDS](#MySQL.Concepts.Features)

## Supported storage engines for RDS for MySQL<a name="MySQL.Concepts.Storage"></a>

While MySQL supports multiple storage engines with varying capabilities, not all of them are optimized for recovery and data durability\. Amazon RDS fully supports the InnoDB storage engine for MySQL DB instances\. Amazon RDS features such as Point\-In\-Time restore and snapshot restore require a recoverable storage engine and are supported for the InnoDB storage engine only\. For more information, see [MySQL memcached support](Appendix.MySQL.Options.memcached.md)\. 

The Federated Storage Engine is currently not supported by Amazon RDS for MySQL\. 

For user\-created schemas, the MyISAM storage engine does not support reliable recovery and can result in lost or corrupt data when MySQL is restarted after a recovery, preventing Point\-In\-Time restore or snapshot restore from working as intended\. However, if you still choose to use MyISAM with Amazon RDS, snapshots can be helpful under some conditions\. 

**Note**  
System tables in the `mysql` schema can be in MyISAM storage\.

If you want to convert existing MyISAM tables to InnoDB tables, you can use the `ALTER TABLE` command \(for example, `alter table TABLE_NAME engine=innodb;`\)\. Bear in mind that MyISAM and InnoDB have different strengths and weaknesses, so you should fully evaluate the impact of making this switch on your applications before doing so\. 

MySQL 5\.1, 5\.5, and 5\.6 are no longer supported in Amazon RDS\. However, you can restore existing MySQL 5\.1, 5\.5, and 5\.6 snapshots\. When you restore a MySQL 5\.1, 5\.5, or 5\.6 snapshot, the DB instance is automatically upgraded to MySQL 5\.7\. 

## Using memcached and other options with MySQL on Amazon RDS<a name="MySQL.Concepts.General.Options"></a>

Most Amazon RDS DB engines support option groups that allow you to select additional features for your DB instance\. RDS for MySQL DB instances support the `memcached` option, a simple, key\-based cache\. For more information about `memcached` and other options, see [Options for MySQL DB instances](Appendix.MySQL.Options.md)\. For more information about working with option groups, see [Working with option groups](USER_WorkingWithOptionGroups.md)\. 

## InnoDB cache warming for MySQL on Amazon RDS<a name="MySQL.Concepts.InnoDBCacheWarming"></a>

InnoDB cache warming can provide performance gains for your MySQL DB instance by saving the current state of the buffer pool when the DB instance is shut down, and then reloading the buffer pool from the saved information when the DB instance starts up\. This bypasses the need for the buffer pool to "warm up" from normal database use and instead preloads the buffer pool with the pages for known common queries\. The file that stores the saved buffer pool information only stores metadata for the pages that are in the buffer pool, and not the pages themselves\. As a result, the file does not require much storage space\. The file size is about 0\.2 percent of the cache size\. For example, for a 64 GiB cache, the cache warming file size is 128 MiB\. For more information on InnoDB cache warming, see [Saving and restoring the buffer pool state](https://dev.mysql.com/doc/refman/8.0/en/innodb-preload-buffer-pool.html) in the MySQL documentation\. 

RDS for MySQL DB instances support InnoDB cache warming\. To enable InnoDB cache warming, set the `innodb_buffer_pool_dump_at_shutdown` and `innodb_buffer_pool_load_at_startup` parameters to 1 in the parameter group for your DB instance\. Changing these parameter values in a parameter group will affect all MySQL DB instances that use that parameter group\. To enable InnoDB cache warming for specific MySQL DB instances, you might need to create a new parameter group for those instances\. For information on parameter groups, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\. 

InnoDB cache warming primarily provides a performance benefit for DB instances that use standard storage\. If you use PIOPS storage, you do not commonly see a significant performance benefit\. 

**Important**  
If your MySQL DB instance does not shut down normally, such as during a failover, then the buffer pool state will not be saved to disk\. In this case, MySQL loads whatever buffer pool file is available when the DB instance is restarted\. No harm is done, but the restored buffer pool might not reflect the most recent state of the buffer pool prior to the restart\. To ensure that you have a recent state of the buffer pool available to warm the InnoDB cache on startup, we recommend that you periodically dump the buffer pool "on demand\."  
You can create an event to dump the buffer pool automatically and on a regular interval\. For example, the following statement creates an event named `periodic_buffer_pool_dump` that dumps the buffer pool every hour\.   

```
1. CREATE EVENT periodic_buffer_pool_dump 
2. ON SCHEDULE EVERY 1 HOUR 
3. DO CALL mysql.rds_innodb_buffer_pool_dump_now();
```
For more information on MySQL events, see [Event syntax](https://dev.mysql.com/doc/refman/8.0/en/events-syntax.html) in the MySQL documentation\. 

### Dumping and loading the buffer pool on demand<a name="MySQL.Concepts.InnoDBCacheWarming.OnDemand"></a>

You can save and load the InnoDB cache "on demand\."
+ To dump the current state of the buffer pool to disk, call the [mysql\.rds\_innodb\_buffer\_pool\_dump\_now](mysql_rds_innodb_buffer_pool_dump_now.md) stored procedure\.
+ To load the saved state of the buffer pool from disk, call the [mysql\.rds\_innodb\_buffer\_pool\_load\_now](mysql_rds_innodb_buffer_pool_load_now.md) stored procedure\.
+ To cancel a load operation in progress, call the [mysql\.rds\_innodb\_buffer\_pool\_load\_abort](mysql_rds_innodb_buffer_pool_load_abort.md) stored procedure\.

## MySQL features not supported by Amazon RDS<a name="MySQL.Concepts.Features"></a>

Amazon RDS doesn't currently support the following MySQL features: 
+ Authentication Plugin
+ Error Logging to the System Log
+ Group Replication Plugin
+ InnoDB Tablespace Encryption
+ Password Strength Plugin
+ Persisted system variables
+ Rewriter Query Rewrite Plugin
+ Semisynchronous replication
+ Transportable tablespace
+ X Plugin

**Note**  
Global transaction IDs are supported for all RDS for MySQL 5\.7 versions, and for RDS for MySQL 8\.0\.26 and higher 8\.0 versions\.

To deliver a managed service experience, Amazon RDS doesn't provide shell access to DB instances\. It also restricts access to certain system procedures and tables that require advanced privileges\. Amazon RDS supports access to databases on a DB instance using any standard SQL client application\. Amazon RDS doesn't allow direct host access to a DB instance by using Telnet, Secure Shell \(SSH\), or Windows Remote Desktop Connection\. When you create a DB instance, you are assigned to the *db\_owner* role for all databases on that instance, and you have all database\-level permissions except for those used for backups\. Amazon RDS manages backups for you\. 