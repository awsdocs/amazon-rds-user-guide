# Migrating data from a MySQL DB snapshot to a MariaDB DB instance<a name="USER_Migrate_MariaDB"></a>

You can migrate an RDS for MySQL DB snapshot to a new DB instance running MariaDB using the AWS Management Console, the AWS CLI, or Amazon RDS API\. You must use a DB snapshot that was created from an Amazon RDS DB instance running MySQL 5\.6 or 5\.7\. To learn how to create an RDS for MySQL DB snapshot, see [Creating a DB snapshot](USER_CreateSnapshot.md)\.

Migrating the snapshot doesn't affect the original DB instance from which the snapshot was taken\. You can test and validate the new DB instance before diverting traffic to it as a replacement for the original DB instance\.

After you migrate from MySQL to MariaDB, the MariaDB DB instance is associated with the default DB parameter group and option group\. After you restore the DB snapshot, you can associate a custom DB parameter group with the new DB instance\. However, a MariaDB parameter group has a different set of configurable system variables\. For information about the differences between MySQL and MariaDB system variables, see [ System Variable Differences between MariaDB and MySQL](https://mariadb.com/kb/en/system-variable-differences-between-mariadb-and-mysql/)\. To learn about DB parameter groups, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\. To learn about option groups, see [Working with option groups](USER_WorkingWithOptionGroups.md)\. 

## Performing the migration<a name="USER_Migrate_MariaDB.Migrating"></a>

You can migrate an RDS for MySQL DB snapshot to a new MariaDB DB instance using the AWS Management Console, the AWS CLI, or the RDS API\.

### Console<a name="USER_Migrate_MariaDB.CON"></a>

**To migrate a MySQL DB snapshot to a MariaDB DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Snapshots**, and then select the MySQL DB snapshot you want to migrate\. 

1. For **Actions**, choose **Migrate snapshot**\. The **Migrate database** page appears\.

1. For **Migrate to DB Engine**, choose **mariadb**\.

   Amazon RDS selects the **DB engine version** automatically\. You can't change the DB engine version\.  
![\[Migrate to MariaDB from MySQL\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/MigrateMariaDB.png)

1. For the remaining sections, specify your DB instance settings\. For information about each setting, see [Settings for DB instances](USER_CreateDBInstance.md#USER_CreateDBInstance.Settings)\. 

1. Choose **Migrate**\.

### AWS CLI<a name="USER_Migrate_MariaDB.CLI"></a>

To migrate data from a MySQL DB snapshot to a MariaDB DB instance, use the AWS CLI [https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-from-db-snapshot.html](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-from-db-snapshot.html) command with the following parameters:
+ \-\-db\-instance\-identifier – Name of the DB instance to create from the DB snapshot\.
+ \-\-db\-snapshot\-identifier – The identifier for the DB snapshot to restore from\.
+ \-\-engine – The database engine to use for the new instance\.

**Example**  
For Linux, macOS, or Unix:  

```
1. aws rds restore-db-instance-from-db-snapshot \
2.     --db-instance-identifier newmariadbinstance \
3.     --db-snapshot-identifier mysqlsnapshot \
4.     --engine mariadb
```
For Windows:  

```
1. aws rds restore-db-instance-from-db-snapshot ^
2.     --db-instance-identifier newmariadbinstance ^
3.     --db-snapshot-identifier mysqlsnapshot ^
4.     --engine mariadb
```

### API<a name="USER_Migrate_MariaDB.API"></a>

To migrate data from a MySQL DB snapshot to a MariaDB DB instance, call the Amazon RDS API operation [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBInstanceFromDBSnapshot.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBInstanceFromDBSnapshot.html)\.

## Incompatibilities between MariaDB and MySQL<a name="USER_Migrate_MariaDB.Incompatibilities"></a>

Incompatibilities between MySQL and MariaDB include the following:
+ You can't migrate a DB snapshot created with MySQL 8\.0 to MariaDB\.
+ If the source MySQL database uses a SHA256 password hash, make sure to reset user passwords that are SHA256 hashed before you connect to the MariaDB database\. The following code shows how to reset a password that is SHA256 hashed\.

  ```
  SET old_passwords = 0;
  UPDATE mysql.user SET plugin = 'mysql_native_password',
  Password = PASSWORD('new_password')
  WHERE (User, Host) = ('master_user_name', %);
  FLUSH PRIVILEGES;
  ```
+ If your RDS master user account uses the SHA\-256 password hash, make sure to reset the password using the AWS Management Console, the [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) AWS CLI command, or the [ModifyDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) RDS API operation\. For information about modifying a DB instance, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\. 
+ MariaDB doesn't support the Memcached plugin\. However, the data used by the Memcached plugin is stored as InnoDB tables\. After you migrate a MySQL DB snapshot, you can access the data used by the Memcached plugin using SQL\. For more information about the innodb\_memcache database, see [InnoDB memcached Plugin Internals](https://dev.mysql.com/doc/refman/8.0/en/innodb-memcached-internals.html)\.