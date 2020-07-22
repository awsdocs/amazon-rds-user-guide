# Known Issues and Limitations for MySQL on Amazon RDS<a name="MySQL.KnownIssuesAndLimitations"></a>

Known issues and limitations for working with MySQL on Amazon RDS are as follows\.

**Topics**
+ [Inconsistent InnoDB Buffer Pool Size](#MySQL.Concepts.KnownIssuesAndLimitations.InnodbBufferPoolSize)
+ [Index Merge Optimization Returns Wrong Results](#MySQL.Concepts.KnownIssuesAndLimitations.IndexMergeOptimization)
+ [Log File Size](#MySQL.Concepts.KnownIssuesAndLimitations.LogFileSize)
+ [MySQL Parameter Exceptions for Amazon RDS DB Instances](#MySQL.Concepts.ParameterNotes)
+ [MySQL File Size Limits in Amazon RDS](#MySQL.Concepts.Limits.FileSize)
+ [MySQL Keyring Plugin Not Supported](#MySQL.Concepts.Limits.KeyRing)

## Inconsistent InnoDB Buffer Pool Size<a name="MySQL.Concepts.KnownIssuesAndLimitations.InnodbBufferPoolSize"></a>

For MySQL 5\.7, there is currently a bug in the way that the InnoDB buffer pool size is managed\. MySQL 5\.7 might adjust the value of the `innodb_buffer_pool_size` parameter to a large value that can result in the InnoDB buffer pool growing too large and using up too much memory\. This effect can cause the MySQL database engine to stop running or can prevent the MySQL database engine from starting\. This issue is more common for DB instance classes that have less memory available\.

To resolve this issue, set the value of the `innodb_buffer_pool_size` parameter to a multiple of the product of the `innodb_buffer_pool_instances` parameter value and the `innodb_buffer_pool_chunk_size` parameter value\. For example, you might set the `innodb_buffer_pool_size` parameter value to a multiple of eight times the product of the `innodb_buffer_pool_instances` and `innodb_buffer_pool_chunk_size` parameter values, as shown in the following example\.

```
innodb_buffer_pool_chunk_size = 536870912
innodb_buffer_pool_instances = 4
innodb_buffer_pool_size = (536870912 * 4) * 8 = 17179869184
```

For details on this MySQL 5\.7 bug, go to [https://bugs\.mysql\.com/bug\.php?id=79379](https://bugs.mysql.com/bug.php?id=79379) in the MySQL documentation\. 

## Index Merge Optimization Returns Wrong Results<a name="MySQL.Concepts.KnownIssuesAndLimitations.IndexMergeOptimization"></a>

Queries that use index merge optimization might return wrong results due to a bug in the MySQL query optimizer that was introduced in MySQL 5\.5\.37\. When you issue a query against a table with multiple indexes the optimizer scans ranges of rows based on the multiple indexes, but does not merge the results together correctly\. For more information on the query optimizer bug, go to [http://bugs\.mysql\.com/bug\.php?id=72745](https://bugs.mysql.com/bug.php?id=72745) and [http://bugs\.mysql\.com/bug\.php?id=68194](https://bugs.mysql.com/bug.php?id=68194) in the MySQL bug database\. 

For example, consider a query on a table with two indexes where the search arguments reference the indexed columns\. 

```
1. SELECT * FROM table1
2. WHERE indexed_col1 = 'value1' AND indexed_col2 = 'value2';
```

In this case, the search engine will search both indexes\. However, due to the bug, the merged results are incorrect\. 

To resolve this issue, you can do one of the following: 
+ Set the `optimizer_switch` parameter to `index_merge=off` in the DB parameter group for your MySQL DB instance\. For information on setting DB parameter group parameters, see [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)\.
+ Upgrade your MySQL DB instance to MySQL version 5\.6, 5\.7, or 8\.0\. For more information, see [Upgrading a MySQL DB Snapshot](USER_UpgradeDBSnapshot.MySQL.md)\. 
+ If you cannot upgrade your instance or change the `optimizer_switch` parameter, you can work around the bug by explicitly identifying an index for the query, for example: 

  ```
  1. SELECT * FROM table1
  2. USE INDEX covering_index
  3. WHERE indexed_col1 = 'value1' AND indexed_col2 = 'value2';
  ```

For more information, go to [Index Merge Optimization](https://dev.mysql.com/doc/refman/8.0/en/index-merge-optimization.html)\. 

## Log File Size<a name="MySQL.Concepts.KnownIssuesAndLimitations.LogFileSize"></a>

For MySQL, there is a size limit on BLOBs written to the redo log\. To account for this limit, ensure that the `innodb_log_file_size` parameter for your MySQL DB instance is 10 times larger than the largest BLOB data size found in your tables, plus the length of other variable length fields \(`VARCHAR`, `VARBINARY`, `TEXT`\) in the same tables\. For information on how to set parameter values, see [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)\. For information on the redo log BLOB size limit, go to [Changes in MySQL 5\.6\.20](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-20.html)\. 

## MySQL Parameter Exceptions for Amazon RDS DB Instances<a name="MySQL.Concepts.ParameterNotes"></a>

Some MySQL parameters require special considerations when used with an Amazon RDS DB instance\.

### lower\_case\_table\_names<a name="MySQL.Concepts.ParameterNotes.lower-case-table-names"></a>

Because Amazon RDS uses a case\-sensitive file system, setting the value of the `lower_case_table_names` server parameter to 2 \("names stored as given but compared in lowercase"\) is not supported\. The following are the supported values for Amazon RDS for MySQL DB instances:
+ 0 \("names stored as given and comparisons are case\-sensitive"\) is supported for all Amazon RDS for MySQL versions\.
+ 1 \("names stored in lowercase and comparisons are not case\-sensitive"\) is supported for Amazon RDS for MySQL version 5\.5, version 5\.6, version 5\.7, and version 8\.0\.19 and higher 8\.0 versions\.

The `lower_case_table_names` parameter should be set as part of a custom DB parameter group before creating a DB instance\. You should avoid changing the `lower_case_table_names` parameter for existing database instances because doing so could cause inconsistencies with point\-in\-time recovery backups and read replica DB instances\. 

Read replicas should always use the same `lower_case_table_names` parameter value as the source DB instance\. 

### long\_query\_time<a name="MySQL.Concepts.ParameterNotes.long_query_time"></a>

You can set the `long_query_time` parameter to a floating point value which allows you to log slow queries to the MySQL slow query log with microsecond resolution\. You can set a value such as 0\.1 seconds, which would be 100 milliseconds, to help when debugging slow transactions that take less than one second\. 

## MySQL File Size Limits in Amazon RDS<a name="MySQL.Concepts.Limits.FileSize"></a>

For Amazon RDS MySQL DB instances, the maximum provisioned storage limit constrains the size of a table to a maximum size of 16 TB when using InnoDB file\-per\-table tablespaces\. This limit also constrains the system tablespace to a maximum size of 16 TB\. InnoDB file\-per\-table tablespaces \(with tables each in their own tablespace\) is set by default for Amazon RDS MySQL DB instances\. 

**Note**  
Some existing DB instances have a lower limit\. For example, MySQL DB instances created before April 2014 have a file and table size limit of 2 TB\. This 2 TB file size limit also applies to DB instances or read replicas created from DB snapshots taken before April 2014, regardless of when the DB instance was created\.

There are advantages and disadvantages to using InnoDB file\-per\-table tablespaces, depending on your application\. To determine the best approach for your application, go to [File\-Per\-Table Tablespaces](https://dev.mysql.com/doc/refman/8.0/en/innodb-file-per-table-tablespaces.html) in the MySQL documentation\. 

We don't recommend allowing tables to grow to the maximum file size\. In general, a better practice is to partition data into smaller tables, which can improve performance and recovery times\. 

One option that you can use for breaking a large table up into smaller tables is partitioning\. Partitioning distributes portions of your large table into separate files based on rules that you specify\. For example, if you store transactions by date, you can create partitioning rules that distribute older transactions into separate files using partitioning\. Then periodically, you can archive the historical transaction data that doesn't need to be readily available to your application\. For more information, go to [Partitioning](https://dev.mysql.com/doc/refman/8.0/en/partitioning.html) in the MySQL documentation\. 

**To determine the file size of a table**
+ Use the following SQL command to determine if any of your tables are too large and are candidates for partitioning\.

  ```
  1. SELECT TABLE_SCHEMA, TABLE_NAME,
  2. round(((DATA_LENGTH + INDEX_LENGTH) / 1024 / 1024), 2) As "Approximate size (MB)"
  3. FROM information_schema.TABLES
  4. WHERE TABLE_SCHEMA NOT IN ('mysql', 'information_schema', 'performance_schema');
  ```

**To enable InnoDB file\-per\-table tablespaces**
+ To enable InnoDB file\-per\-table tablespaces, set the *innodb\_file\_per\_table* parameter to `1` in the parameter group for the DB instance\. 

**To disable InnoDB file\-per\-table tablespaces**
+ To disable InnoDB file\-per\-table tablespaces, set the *innodb\_file\_per\_table* parameter to `0` in the parameter group for the DB instance\.

For information on updating a parameter group, see [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)\. 

When you have enabled or disabled InnoDB file\-per\-table tablespaces, you can issue an `ALTER TABLE` command to move a table from the global tablespace to its own tablespace, or from its own tablespace to the global tablespace as shown in the following example: 

```
ALTER TABLE table_name ENGINE=InnoDB;
```

## MySQL Keyring Plugin Not Supported<a name="MySQL.Concepts.Limits.KeyRing"></a>

Currently, Amazon RDS for MySQL does not support the MySQL `keyring_aws` Amazon Web Services Keyring Plugin\.