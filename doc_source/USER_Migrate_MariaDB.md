# Migrating data from a MySQL DB snapshot to a MariaDB DB instance<a name="USER_Migrate_MariaDB"></a>

You can migrate an RDS for MySQL DB snapshot to a new DB instance running MariaDB 10\.1 using the AWS CLI or Amazon RDS API\. You must create the DB snapshot from an Amazon RDS DB instance running MySQL 5\.6\. To learn how to create an RDS for MySQL DB snapshot, see [Creating a DB snapshot](USER_CreateSnapshot.md)\.

After you migrate from MySQL to MariaDB, the MariaDB DB instance will be associated with the default DB parameter group and option group\. After you restore the DB snapshot, you can associate a custom DB parameter group for the new DB instance\. However, a MariaDB parameter group has a different set of configurable system variables\. For information about the differences between MySQL and MariaDB system variables, see [ System variable differences between MariaDB 10\.1 and MySQL 5\.6](https://mariadb.com/kb/en/mariadb/system-variable-differences-between-mariadb-101-and-mysql-56/)\. To learn about DB parameter groups, see [Working with DB parameter groups](USER_WorkingWithParamGroups.md)\. To learn about option groups, see [Working with option groups](USER_WorkingWithOptionGroups.md)\. 

## Incompatibilities between MariaDB and MySQL<a name="USER_Migrate_MariaDB.Incompatibilities"></a>

Incompatibilities between MySQL and MariaDB include the following:
+ You can't migrate a DB snapshot created with MySQL 5\.5 to MariaDB 10\.1\.
+ You can't migrate a DB snapshot created with MySQL 5\.7 to MariaDB\.
+ You can't migrate a DB snapshot created with MySQL 8\.0 to MariaDB\.
+ You can't migrate an encrypted snapshot\.
+ If the source MySQL database uses a SHA256 password hash, you need to reset user passwords that are SHA256 hashed before you can connect to the MariaDB database\. The following code shows how to reset a password that is SHA256 hashed:

  ```
  SET old_passwords = 0;
  UPDATE mysql.user SET plugin = 'mysql_native_password',
  Password = PASSWORD('new_password')
  WHERE (User, Host) = ('master_user_name', %);
  FLUSH PRIVILEGES;
  ```
+ If your RDS master user account uses the SHA\-256 password hash, you must reset the password using the the AWS Management Console, the [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) AWS CLI command, or the [ModifyDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) RDS API operation\. For information about modifying a DB instance, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\. 
+ MariaDB doesn't support the Memcached plugin; however, the data used by the Memcached plugin is stored as InnoDB tables\. After you migrate a MySQL DB snapshot, you can access the data used by the Memcached plugin using SQL\. For more information about the innodb\_memcache database, see [InnoDB memcached plugin internals](https://dev.mysql.com/doc/refman/5.6/en/innodb-memcached-internals.html)\.

## Performing the migration<a name="USER_Migrate_MariaDB.Migrating"></a>

You can migrate an RDS for MySQL DB snapshot to a new MariaDB DB instance using the AWS CLI or the RDS API\.

**Note**  
You can't use the AWS Management Console to migrate an RDS for MySQL DB snapshot to a new MariaDB DB instance\.

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

To migrate data from a MySQL DB snapshot to a MariaDB DB instance, call the Amazon RDS API operation [ `RestoreDBInstanceFromDBSnapshot`](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBInstanceFromDBSnapshot.html)\.