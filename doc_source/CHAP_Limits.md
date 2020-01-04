# Quotas for Amazon RDS<a name="CHAP_Limits"></a>

Following, you can find a description of the resource quotas and naming constraints for Amazon RDS\.

**Topics**
+ [Quotas in Amazon RDS](#RDS_Limits.Limits)
+ [Naming Constraints in Amazon RDS](#RDS_Limits.Constraints)
+ [File Size Limits in Amazon RDS](#RDS_Limits.FileSize)

## Quotas in Amazon RDS<a name="RDS_Limits.Limits"></a>

Each AWS account has quotas, for each AWS Region, on the number of Amazon RDS resources that can be created\. After a quota for a resource has been reached, additional calls to create that resource fail with an exception\.

The following table lists the resources and their quotas per AWS Region\.


| Resource | Default Quota | 
| --- | --- | 
| Authorizations per DB security group | 20 | 
| Cross\-region snapshots copy requests | 5 | 
| DB instances | 40 | 
| DB security groups | 25 | 
| DB subnet groups | 50 | 
| Event subscriptions | 20 | 
| IAM roles per DB instance | 5 | 
| Manual snapshots | 100 | 
| Option groups | 20 | 
| Parameter groups | 50 | 
| Read replicas per master | 5 | 
| Reserved DB instances | 40 | 
| Rules per security group | 20 | 
| Rules per virtual private cloud \(VPC\) security group | 50 inbound, 50 outbound | 
| Subnets per subnet group | 20 | 
| Tags per resource | 50 | 
| Total storage for all DB instances | 100 TB | 
| VPC security groups | 5 | 

**Note**  
By default, you can have up to a total of 40 DB instances\. RDS DB instances, Aurora DB instances, Amazon Neptune instances, and Amazon DocumentDB instances apply to this quota\.Of those 40 DB instances, you can have up to 10 instances of each SQL Server DB edition \(Enterprise, Standard, Web, and Express\) under the "license\-included" model\. All 40 can be MySQL, MariaDB, or PostgreSQL\. All 40 can be Oracle under the "bring\-your\-own\-license" \(BYOL\) model\.  If your application requires more DB instances, you can request additional DB instances by opening the [Service Quotas console](https://console.aws.amazon.com/servicequotas/home?region=us-east-1#!/dashboard)\. In the navigation pane, choose **AWS services**\. Choose **Amazon Relational Database Service \(Amazon RDS\)**, choose a quota, and follow the directions to request a quota increase\. For more information, see [Requesting a Quota Increase](https://docs.aws.amazon.com/servicequotas/latest/userguide/request-increase.html) in the *Service Quotas User Guide*\.  
Backups managed by AWS Backup are considered manual snapshots for the manual snapshot quota\. For information about AWS Backup, see the [https://docs.aws.amazon.com/aws-backup/latest/devguide](https://docs.aws.amazon.com/aws-backup/latest/devguide)\.

## Naming Constraints in Amazon RDS<a name="RDS_Limits.Constraints"></a>

The following table describes naming constraints in Amazon RDS\. 


****  

|  |  | 
| --- |--- |
| DB instance identifier |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Limits.html)  | 
|  Database name  |  Database name constraints differ for each database engine\.  **MySQL and MariaDB**  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Limits.html)  **Oracle**  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Limits.html)  **PostgreSQL**  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Limits.html)  **SQL Server** [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Limits.html)    | 
|  Master user name  |  Master user name constraints differ for each database engine\.  **MariaDB**  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Limits.html)  **MySQL**  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Limits.html)  **Oracle**  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Limits.html)  **PostgreSQL**  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Limits.html)  **SQL Server**  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Limits.html)    | 
|  Master password  |  The password for the master database user can be any printable ASCII character except "/", """, or "@"\. Master password constraints differ for each database engine\.  **MySQL and MariaDB**  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Limits.html)  **Oracle**  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Limits.html)  **PostgreSQL**  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Limits.html)  **SQL Server**  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Limits.html)    | 
| DB parameter group name |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Limits.html)  | 
|  DB subnet group name  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Limits.html)  | 

## File Size Limits in Amazon RDS<a name="RDS_Limits.FileSize"></a>

File size limits apply to Amazon RDS DB instances\.

### MySQL File Size Limits in Amazon RDS<a name="RDS_Limits.FileSize.MySQL"></a>

For Amazon RDS MySQL DB instances, the maximum provisioned storage limit constrains the size of a table to a maximum size of 16 TB when using InnoDB file\-per\-table tablespaces\. This limit also constrains the system tablespace to a maximum size of 16 TB\. InnoDB file\-per\-table tablespaces \(with tables each in their own tablespace\) are set by default for Amazon RDS MySQL DB instances\. For more information, see [Amazon RDS DB Instance Storage](CHAP_Storage.md)\. 

**Note**  
Some existing DB instances have a lower limit\. For example, MySQL DB instances created prior to April 2014 have a file and table size limit of 2 TB\. This 2\-TB file size limit also applies to DB instances or Read Replicas created from DB snapshots taken before April 2014, regardless of when the DB instance was created\. 

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

When you have enabled or disabled InnoDB file\-per\-table tablespaces, you can issue an `ALTER TABLE` command\. You can use this command to move a table from the global tablespace to its own tablespace, or from its own tablespace to the global tablespace as shown in the following example\.

```
1. ALTER TABLE table_name ENGINE=InnoDB, ALGORITHM=COPY; 
```

### MariaDB File Size Limits in Amazon RDS<a name="RDS_Limits.FileSize.MariaDB"></a>

For Amazon RDS MariaDB DB instances, the maximum provisioned storage limit constrains the size of a table to a maximum size of 16 TB when using InnoDB file\-per\-table tablespaces\. This limit also constrains the system tablespace to a maximum size of 16 TB\. InnoDB file\-per\-table tablespaces \(with tables each in their own tablespace\) is set by default for Amazon RDS MariaDB DB instances\. For more information, see [Amazon RDS DB Instance Storage](CHAP_Storage.md)\. 

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

When you have enabled or disabled InnoDB file\-per\-table tablespaces, you can issue an `ALTER TABLE` command\. You can use this command to move a table from the global tablespace to its own tablespace, or from its own tablespace to the global tablespace as shown in the following example\.

```
1. ALTER TABLE table_name ENGINE=InnoDB, ALGORITHM=COPY; 
```