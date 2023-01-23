# Improving write performance with Amazon RDS Optimized Writes<a name="rds-optimized-writes"></a>

You can improve the performance of write transactions with Amazon RDS Optimized Writes\. When your RDS for MySQL database uses RDS Optimized Writes, it can achieve up to two times higher write transaction throughput\.

**Topics**
+ [Overview of RDS Optimized Writes](#rds-optimized-writes-overview)
+ [Using RDS Optimized Writes](#rds-optimized-writes-using)
+ [Limitations for RDS Optimized Writes](#rds-optimized-writes-limitations)

## Overview of RDS Optimized Writes<a name="rds-optimized-writes-overview"></a>

When you turn on Amazon RDS Optimized Writes, your RDS for MySQL databases write only once when flushing data to durable storage without the need for the doublewrite buffer\. The databases continue to provide ACID property protections for reliable database transactions, along with improved performance\.

Relational databases, like MySQL, provide the *ACID properties* of atomicity, consistency, isolation, and durability for reliable database transactions\. To help provide these properties, MySQL uses a data storage area called the *doublewrite buffer* that prevents partial page write errors\. These errors occur when there is a hardware failure while the database is updating a page, such as in the case of a power outage\. A MySQL database can detect partial page writes and recover with a copy of the page in the doublewrite buffer\. While this technique provides protection, it also results in extra write operations\. For more information about the MySQL doublewrite buffer, see [Doublewrite Buffer](https://dev.mysql.com/doc/refman/8.0/en/innodb-doublewrite-buffer.html) in the MySQL documentation\.

With RDS Optimized Writes turned on, RDS for MySQL databases write only once when flushing data to durable storage without using the doublewrite buffer\. RDS Optimized Writes is useful if you run write\-heavy workloads on your RDS for MySQL databases\. Examples of databases with write\-heavy workloads include ones that support digital payments, financial trading, and gaming applications\.

These databases run on DB instance classes that use the AWS Nitro System\. Because of the hardware configuration in these systems, the database can write 16 KiB pages directly to data files reliably and durably in one step\. The AWS Nitro System makes RDS Optimized Writes possible\.

You can set the new database parameter `rds.optimized_writes` to control the RDS Optimized Writes feature for RDS for MySQL databases\. Access this parameter in the DB parameter groups of RDS for MySQL version 8\.0\. Set the parameter using the following values:
+ `AUTO` – Turn on RDS Optimized Writes if the database supports it\. Turn off RDS Optimized Writes if the database doesn't support it\. This setting is the default\.
+ `OFF` – Turn off RDS Optimized Writes even if the database supports it\.

If you migrate an RDS for MySQL database that is configured to use RDS Optimized Writes to a DB instance class that doesn't support the feature, RDS automatically turns off RDS Optimized Writes for the database\.

When RDS Optimized Writes is turned off, the database uses the MySQL doublewrite buffer\.

To determine whether an RDS for MySQL database is using RDS Optimized Writes, view the current value of the `innodb_doublewrite` parameter for the database\. If the database is using RDS Optimized Writes, this parameter is set to `FALSE` \(`0`\)\.

## Using RDS Optimized Writes<a name="rds-optimized-writes-using"></a>

You can turn on RDS Optimized Writes when you create an RDS for MySQL database with the RDS console, the AWS CLI, or the RDS API\. RDS Optimized Writes is turned on automatically when both of the following conditions apply during database creation:
+ You specify a DB engine version and DB instance class that support RDS Optimized Writes\. For information about the DB engine versions and DB instance classes that support RDS Optimized Writes, see [Limitations for RDS Optimized Writes](#rds-optimized-writes-limitations)\.
+ In the parameter group associated with the database, the `rds.optimized_writes` parameter is set to `AUTO`\. In default parameter groups, this parameter is always set to `AUTO`\.

You might want to use a DB engine version and DB instance class that support RDS Optimized Writes, but you don't want to use this feature\. In this case, specify a custom parameter group that has the `rds.optimized_writes` parameter set to `OFF` when you create the database\. If you want the database to use RDS Optimized Writes later, you can set the parameter to `AUTO` to turn it on\. For information about creating custom parameter groups and setting parameters, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\.

For information about creating a DB instance, see [Creating an Amazon RDS DB instance](USER_CreateDBInstance.md)\.

### Console<a name="rds-optimized-writes-using-console"></a>

When you use the RDS console to create an RDS for MySQL database, you can filter for the DB engine versions and DB instance classes that support RDS Optimized Writes\. After you turn on the filters, you can choose from the available DB engine versions and DB instance classes\.

To choose a DB engine version that supports RDS Optimized Writes, filter for the RDS for MySQL DB engine versions that support it in **Engine version**, and then choose a version\.

![\[DB engine version filter for RDS Optimized Writes\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/rds-optimized-writes-version-filter.png)

In the **Instance configuration** section, filter for the DB instance classes that support RDS Optimized Writes, and then choose a DB instance class\.

![\[DB instance class filter for RDS Optimized Writes\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/rds-optimized-writes-class-filter.png)

After you make these selections, you can choose other settings that meet your requirements and finish creating the RDS for MySQL database with the console\.

### AWS CLI<a name="rds-optimized-writes-using-cli"></a>

To create a DB instance by using the AWS CLI, use the [create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) command\. Make sure the `--engine-version` and `--db-instance-class` values support RDS Optimized Writes\. In addition, make sure the parameter group associated with the DB instance has the `rds.optimized_writes` parameter set to `AUTO`\. This example associates the default parameter group with the DB instance\.

**Example Creating a DB instance that uses RDS Optimized Writes**  
For Linux, macOS, or Unix:  

```
1. aws rds create-db-instance \
2.     --db-instance-identifier mydbinstance \
3.     --engine mysql \
4.     --engine-version 8.0.30 \
5.     --db-instance-class db.r5b.large \
6.     --master-user-password password \
7.     --master-username admin \
8.     --allocated-storage 200
```
For Windows:  

```
1. aws rds create-db-instance ^
2.     --db-instance-identifier mydbinstance ^
3.     --engine mysql ^
4.     --engine-version 8.0.30 ^
5.     --db-instance-class db.r5b.large ^
6.     --master-user-password password ^
7.     --master-username admin ^
8.     --allocated-storage 200
```

### RDS API<a name="rds-optimized-writes-using-api"></a>

You can create a DB instance using the [ CreateDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html) operation\. When you use this operation, make sure the `EngineVersion` and `DBInstanceClass` values support RDS Optimized Writes\. In addition, make sure the parameter group associated with the DB instance has the `rds.optimized_writes` parameter set to `AUTO`\. 

## Limitations for RDS Optimized Writes<a name="rds-optimized-writes-limitations"></a>

The following limitations apply to RDS Optimized Writes:
+ RDS Optimized Writes is supported for RDS for MySQL version 8\.0\.30 and higher\. For information about RDS for MySQL versions, see [MySQL on Amazon RDS versions](MySQL.Concepts.VersionMgmt.md)\.
+ RDS Optimized Writes is supported for RDS for MySQL databases that use the following DB instance classes:
  + db\.x2idn
  + db\.x2iedn
  + db\.r6g
  + db\.r6gd
  + db\.r6i
  + db\.r5b

  For information about DB instance classes, see [DB instance classes](Concepts.DBInstanceClass.md)\.
+ You can only modify a database to turn on RDS Optimized Writes if the database was created with a DB engine version and DB instance class that support the feature\. In this case, if RDS Optimized Writes is turned off for the database, you can turn it on by setting the `rds.optimized_writes` parameter to `AUTO`\.
+ When you are restoring an RDS for MySQL database from a snapshot, you can only turn on RDS Optimized Writes for the database if all of the following conditions apply:
  + The snapshot was created from a database that supports RDS Optimized Writes\.
  + The snapshot is restored to a database that supports RDS Optimized Writes\.
  + The restored database is associated with a parameter group with the `rds.optimized_writes` parameter set to `AUTO`\.