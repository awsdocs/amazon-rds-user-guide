# RDS for MariaDB limitations<a name="CHAP_MariaDB.Limitations"></a>

Following are important limitations of using RDS for MariaDB\.

**Note**  
This list is not exhaustive\.

**Topics**
+ [MariaDB file size limits in Amazon RDS](#RDS_Limits.FileSize.MariaDB)
+ [InnoDB reserved word](#MariaDB.Concepts.InnodbDatabaseName)

## MariaDB file size limits in Amazon RDS<a name="RDS_Limits.FileSize.MariaDB"></a>

For MariaDB DB instances, the maximum size of a table is 16 TB when using InnoDB file\-per\-table tablespaces\. This limit also constrains the system tablespace to a maximum size of 16 TB\. InnoDB file\-per\-table tablespaces \(with tables each in their own tablespace\) are set by default for MariaDB DB instances\. This limit isn't related to the maximum storage limit for MariaDB DB instances\. For more information about the storage limit, see [Amazon RDS DB instance storage](CHAP_Storage.md)\.

There are advantages and disadvantages to using InnoDB file\-per\-table tablespaces, depending on your application\. To determine the best approach for your application, see [File\-per\-table tablespaces](https://dev.mysql.com/doc/refman/5.7/en/innodb-file-per-table-tablespaces.html) in the MySQL documentation\.

We don't recommend allowing tables to grow to the maximum file size\. In general, a better practice is to partition data into smaller tables, which can improve performance and recovery times\.

One option that you can use for breaking a large table up into smaller tables is partitioning\. *Partitioning* distributes portions of your large table into separate files based on rules that you specify\. For example, if you store transactions by date, you can create partitioning rules that distribute older transactions into separate files using partitioning\. Then periodically, you can archive the historical transaction data that doesn't need to be readily available to your application\. For more information, see [Partitioning](https://dev.mysql.com/doc/refman/5.7/en/partitioning.html) in the MySQL documentation\.

**To determine the file size of a table**

Use the following SQL command to determine if any of your tables are too large and are candidates for partitioning\. To update table statistics, issue an `ANALYZE TABLE` command on each table\. For more information, see [ANALYZE TABLE statement](https://dev.mysql.com/doc/refman/5.7/en/analyze-table.html) in the MySQL documentation\.

```
1. SELECT TABLE_SCHEMA, TABLE_NAME, 
2.     round(((DATA_LENGTH + INDEX_LENGTH) / 1024 / 1024), 2) As "Approximate size (MB)", DATA_FREE 
3.     FROM information_schema.TABLES
4.     WHERE TABLE_SCHEMA NOT IN ('mysql', 'information_schema', 'performance_schema');
```

**To enable InnoDB file\-per\-table tablespaces**
+ To enable InnoDB file\-per\-table tablespaces, set the `innodb_file_per_table` parameter to `1` in the parameter group for the DB instance\.

**To disable InnoDB file\-per\-table tablespaces**
+ To disable InnoDB file\-per\-table tablespaces, set the `innodb_file_per_table` parameter to `0` in the parameter group for the DB instance\.

For information on updating a parameter group, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\.

When you have enabled or disabled InnoDB file\-per\-table tablespaces, you can issue an `ALTER TABLE` command\. You can use this command to move a table from the global tablespace to its own tablespace\. Or you can move a table from its own tablespace to the global tablespace\. Following is an example\.

```
1. ALTER TABLE table_name ENGINE=InnoDB, ALGORITHM=COPY; 
```

## InnoDB reserved word<a name="MariaDB.Concepts.InnodbDatabaseName"></a>

`InnoDB` is a reserved word for RDS for MariaDB\. You can't use this name for a MariaDB database\.