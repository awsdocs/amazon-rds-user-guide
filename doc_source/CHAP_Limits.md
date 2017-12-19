# Limits for Amazon RDS<a name="CHAP_Limits"></a>

This topic describes the resource limits and naming constraints for Amazon RDS\.


+ [Limits in Amazon RDS](#RDS_Limits.Limits)
+ [Naming Constraints in Amazon RDS](#RDS_Limits.Constraints)
+ [File Size Limits in Amazon RDS](#RDS_Limits.FileSize)

## Limits in Amazon RDS<a name="RDS_Limits.Limits"></a>

Each AWS account has limits, per region, on the number of Amazon RDS resources that can be created\. Once a limit for a resource has been reached, additional calls to create that resource will fail with an exception\.

The following table lists the resources and their limits per region\.


| Resource | Default Limit | 
| --- | --- | 
| Clusters | 40 | 
| Cluster parameter groups | 50 | 
| Cross\-region snapshots copy requests | 5 | 
| DB Instances | 40 | 
| Event subscriptions | 20 | 
| Manual snapshots | 100 | 
| Manual cluster snapshots | 100 | 
| Option groups | 20 | 
| Parameter groups | 50 | 
| Read replicas per master | 5 | 
| Reserved instances | 40 | 
| Rules per security group | 20 | 
| Security groups | 25 | 
| Security groups \(VPC\) | 5 | 
| Subnet groups | 50 | 
| Subnets per subnet group | 20 | 
| Tags per resource | 50 | 
| Total storage for all DB instances | 100 TB | 

## Naming Constraints in Amazon RDS<a name="RDS_Limits.Constraints"></a>

The following table describes naming constraints in Amazon RDS\. 


****  

|  |  | 
| --- |--- |
| DB instance identifier |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Limits.html)  | 
|  Database name  |  Database name constraints differ for each database engine\. **MySQL, Amazon Aurora, and MariaDB** [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Limits.html) **PostgreSQL** [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Limits.html) **Oracle** [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Limits.html) **SQL Server** [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Limits.html)  | 
|  Master user name  |  Master user name constraints differ for each database engine\. **MySQL and Amazon Aurora** [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Limits.html) **Oracle** [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Limits.html) **SQL Server** [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Limits.html) **PostgreSQL** [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Limits.html) **MariaDB** [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Limits.html)  | 
|  Master password  |  The password for the master database user can be any printable ASCII character except "/", """, or "@"\. Master password constraints differ for each database engine\. **MySQL, Amazon Aurora, and MariaDB** [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Limits.html) **Oracle** [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Limits.html) **SQL Server** [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Limits.html) **PostgreSQL** [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Limits.html)  | 
| DB parameter group name |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Limits.html)  | 

## File Size Limits in Amazon RDS<a name="RDS_Limits.FileSize"></a>

### Aurora File Size Limits in Amazon RDS<a name="RDS_Limits.FileSize.Aurora"></a>

With Amazon Aurora, the table size limit is only constrained by the size of the Aurora cluster volume, which has a maximum of 64 terabytes \(TB\)\. As a result, the maximum table size for a table in an Aurora database is 64 TB\.

### MySQL File Size Limits in Amazon RDS<a name="RDS_Limits.FileSize.MySQL"></a>

For Amazon RDS MySQL DB instances, the maximum provisioned storage limit constrains the size of a table to a maximum size of 16 TB when using InnoDB file\-per\-table tablespaces\. This limit also constrains the system tablespace to a maximum size of 16 TB\. InnoDB file\-per\-table tablespaces \(with tables each in their own tablespace\) is set by default for Amazon RDS MySQL DB instances\. For more information, see [Storage for Amazon RDS](CHAP_Storage.md)\. 

**Note**  
Some existing DB instances have a lower limit\. For example, MySQL DB instances created prior to April 2014 have a file and table size limit of 2 TB\. This 2 TB file size limit also applies to DB instances or Read Replicas created from DB snapshots taken prior to April 2014, regardless of when the DB instance was created\. 

There are advantages and disadvantages to using InnoDB file\-per\-table tablespaces, depending on your application\. To determine the best approach for your application, go to [InnoDB File\-Per\-Table Mode](http://dev.mysql.com/doc/refman/5.6/en/innodb-multiple-tablespaces.html) in the MySQL documentation\.

We don't recommend allowing tables to grow to the maximum file size\. In general, a better practice is to partition data into smaller tables, which can improve performance and recovery times\.

One option that you can use for breaking a large table up into smaller tables is partitioning\. Partitioning distributes portions of your large table into separate files based on rules that you specify\. For example, if you store transactions by date, you can create partitioning rules that distribute older transactions into separate files using partitioning\. Then periodically, you can archive the historical transaction data that doesn't need to be readily available to your application\. For more information, see [Partitioning](https://dev.mysql.com/doc/refman/5.6/en/partitioning.html) in the MySQL documentation\.

**To determine the file size of a table**

Use the following SQL command to determine if any of your tables are too large and are candidates for partitioning\. To update table statistics, issue an `ANALYZE TABLE` command on each table\. For more information, see [ANALYZE TABLE](https://dev.mysql.com/doc/refman/5.6/en/analyze-table.html) in the MySQL documentation\.

```
1. SELECT TABLE_SCHEMA, TABLE_NAME, 
2.     round(((DATA_LENGTH + INDEX_LENGTH) / 1024 / 1024), 2) As "Approximate size (MB)", DATA_FREE 
3.     FROM information_schema.TABLES
4.     WHERE TABLE_SCHEMA NOT IN ('mysql', 'information_schema', 'performance_schema');
```

**To enable InnoDB file\-per\-table tablespaces**

+ To enable InnoDB file\-per\-table tablespaces, set the *innodb\_file\_per\_table* parameter to `1` in the parameter group for the DB instance\.

**To disable InnoDB file\-per\-table tablespaces**

+ To disable InnoDB file\-per\-table tablespaces, set the *innodb\_file\_per\_table* parameter to `0` in the parameter group for the DB instance\.

For information on updating a parameter group, see [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)\.

When you have enabled or disabled InnoDB file\-per\-table tablespaces, you can issue an `ALTER TABLE` command to move a table from the global tablespace to its own tablespace, or from its own tablespace to the global tablespace as shown in the following example:

```
1. ALTER TABLE table_name ENGINE=InnoDB, ALGORITHM=COPY; 
```

### MariaDB File Size Limits in Amazon RDS<a name="RDS_Limits.FileSize.MariaDB"></a>

For Amazon RDS MariaDB DB instances, the maximum provisioned storage limit constrains the size of a table to a maximum size of 16 TB when using InnoDB file\-per\-table tablespaces\. This limit also constrains the system tablespace to a maximum size of 16 TB\. InnoDB file\-per\-table tablespaces \(with tables each in their own tablespace\) is set by default for Amazon RDS MariaDB DB instances\. For more information, see [Storage for Amazon RDS](CHAP_Storage.md)\. 

There are advantages and disadvantages to using InnoDB file\-per\-table tablespaces, depending on your application\. To determine the best approach for your application, go to [InnoDB File\-Per\-Table Mode](http://dev.mysql.com/doc/refman/5.6/en/innodb-multiple-tablespaces.html) in the MySQL documentation\.

We don't recommend allowing tables to grow to the maximum file size\. In general, a better practice is to partition data into smaller tables, which can improve performance and recovery times\.

One option that you can use for breaking a large table up into smaller tables is partitioning\. Partitioning distributes portions of your large table into separate files based on rules that you specify\. For example, if you store transactions by date, you can create partitioning rules that distribute older transactions into separate files using partitioning\. Then periodically, you can archive the historical transaction data that doesn't need to be readily available to your application\. For more information, go to [https://dev\.mysql\.com/doc/refman/5\.6/en/partitioning\.html](https://dev.mysql.com/doc/refman/5.6/en/partitioning.html) in the MySQL documentation\.

**To determine the file size of a table**

Use the following SQL command to determine if any of your tables are too large and are candidates for partitioning\. To update table statistics, issue an `ANALYZE TABLE` command on each table\. For more information, see [ANALYZE TABLE](https://dev.mysql.com/doc/refman/5.6/en/analyze-table.html) in the MySQL documentation\.

```
1. SELECT TABLE_SCHEMA, TABLE_NAME, 
2.     round(((DATA_LENGTH + INDEX_LENGTH) / 1024 / 1024), 2) As "Approximate size (MB)", DATA_FREE 
3.     FROM information_schema.TABLES
4.     WHERE TABLE_SCHEMA NOT IN ('mysql', 'information_schema', 'performance_schema');
```

**To enable InnoDB file\-per\-table tablespaces**

+ To enable InnoDB file\-per\-table tablespaces, set the *innodb\_file\_per\_table* parameter to `1` in the parameter group for the DB instance\.

**To disable InnoDB file\-per\-table tablespaces**

+ To disable InnoDB file\-per\-table tablespaces, set the *innodb\_file\_per\_table* parameter to `0` in the parameter group for the DB instance\.

For information on updating a parameter group, see [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)\.

When you have enabled or disabled InnoDB file\-per\-table tablespaces, you can issue an `ALTER TABLE` command to move a table from the global tablespace to its own tablespace, or from its own tablespace to the global tablespace as shown in the following example:

```
1. ALTER TABLE table_name ENGINE=InnoDB, ALGORITHM=COPY; 
```