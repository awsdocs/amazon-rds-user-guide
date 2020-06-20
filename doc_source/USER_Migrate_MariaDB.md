# Migrating Data from a MySQL DB Snapshot to a MariaDB DB Instance<a name="USER_Migrate_MariaDB"></a>

You can migrate an Amazon RDS MySQL DB snapshot to a new DB instance running MariaDB 10\.1 using the AWS Management Console, AWS CLI, or Amazon RDS API\. You must create the DB snapshot from an Amazon RDS DB instance running MySQL 5\.6\. To learn how to create an RDS MySQL DB snapshot, see [Creating a DB Snapshot](USER_CreateSnapshot.md)\.

After you migrate from MySQL to MariaDB, the MariaDB DB instance will be associated with the default DB parameter group and option group\. After you restore the DB snapshot, you can associate a custom DB parameter group for the new DB instance\. However, a MariaDB parameter group has a different set of configurable system variables\. For information about the differences between MySQL and MariaDB system variables, see [ System Variable Differences Between MariaDB 10\.1 and MySQL 5\.6](https://mariadb.com/kb/en/mariadb/system-variable-differences-between-mariadb-101-and-mysql-56/)\. To learn about DB parameter groups, see [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)\. To learn about option groups, see [Working with Option Groups](USER_WorkingWithOptionGroups.md)\. 

## Incompatibilities Between MariaDB and MySQL<a name="USER_Migrate_MariaDB.Incompatibilities"></a>

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
+ If your RDS master user account uses the SHA\-256 password hash, the password has to be reset using the RDS [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) AWS CLI command, [ ModifyDBInstance ](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) API operation, or the AWS Management Console\. For information about modifying a DB instance, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\. 
+ MariaDB doesn't support the Memcached plugin; however, the data used by the Memcached plugin is stored as InnoDB tables\. After you migrate a MySQL DB snapshot, you can access the data used by the Memcached plugin using SQL\. For more information about the innodb\_memcache database, see [InnoDB memcached Plugin Internals](https://dev.mysql.com/doc/refman/5.6/en/innodb-memcached-internals.html)\.

## Console<a name="USER_Migrate_MariaDB.CON"></a>

**To migrate a MySQL DB snapshot to a MariaDB DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Snapshots**, and then select the MySQL DB snapshot you want to migrate\. 

1. For **Actions**, choose **Migrate Snapshot**\. The **Migrate Database** page appears\.

1. For **Migrate to DB Engine**, choose **mariadb**\.

1. On the **Migrate Database** page, provide additional information that RDS needs to launch the MariaDB DB instance\.
   + **DB Engine Version**: Choose the version of the MariaDB database engine that you want to use\. For more information, see [Upgrading the MariaDB DB Engine](USER_UpgradeDBInstance.MariaDB.md)\. 
   + **DB Instance Class**: Choose a DB instance class that has the required storage and capacity for your database, for example db\.r3\.large\. For any production application that requires fast and consistent I/O performance, we recommend Provisioned IOPS storage\. For more information, see [Provisioned IOPS SSD Storage](CHAP_Storage.md#USER_PIOPS)\. MariaDB 10\.1 does not support previous\-generation DB instance classes\. For more information, see [DB Instance Classes](Concepts.DBInstanceClass.md)\. 
   + **Multi\-AZ Deployment**: Choose **Yes** to deploy your DB instance in multiple Availability Zones; otherwise, **No**\. For more information, see [ Regions, Availability Zones, and Local Zones ](Concepts.RegionsAndAvailabilityZones.md)\. 
   + **DB Snapshot ID**: Type a name for the DB snapshot identifier\. 

     The DB snapshot identifier has the following constraints:
     + It must contain from 1 to 255 alphanumeric characters or hyphens\.
     + The character must be a letter\.
     + It cannot end with a hyphen or contain two consecutive hyphens\.

     If you are restoring from a shared manual DB snapshot, the DB snapshot identifier must be the Amazon Resource Name \(ARN\) of the shared DB snapshot\.
   + **DB Instance Identifier**: Type a name for the DB instance that is unique for your account in the AWS Region where the DB instance will reside\. This identifier is used in the endpoint addresses for the instances in your DB instance\. 

     The DB instance identifier has the following constraints:
     + It must contain from 1 to 63 alphanumeric characters or hyphens\.
     + Its first character must be a letter\.
     + It cannot end with a hyphen or contain two consecutive hyphens\.
     + It must be unique for all DB instances for your AWS account, within an AWS Region\.
   + **Virtual Private Cloud \(VPC\)**: If you have an existing VPC, then you can use that VPC with your MariaDB DB instance by selecting your VPC identifier, for example `vpc-a464d1c1`\. For more information about VPC, see [Amazon Virtual Private Cloud VPCs and Amazon RDS](USER_VPC.md) \.

     Otherwise, you can choose to have Amazon RDS create a VPC for you by selecting Create a new VPC\. 

     You cannot create MariaDB instances in the EC2 Classic Network\.
   + **Subnet group**: If you have an existing subnet group, then you can use that subnet group with your MariaDB DB instance by selecting your subnet group identifier, for example `gs-subnet-group1`\.

     Otherwise, you can choose to have Amazon RDS create a subnet group for you by selecting Create a new subnet group\. 
   + **Public accessibility**: Choose **No** to specify that instances in your DB instance can only be accessed by resources inside your VPC\. Choose **Yes** to specify that instances in your DB instance can be accessed by resources on the public network\. The default is **Yes**\.
   + **Availability zone**: Choose the **Availability Zone** to host the primary instance for your MariaDB DB instance\. To have Amazon RDS choose an **Availability Zone** for you, choose **No Preference**\.
   + **Database Port**: Type the default port to be used when connecting to instances in the DB instance\. The default is `3306`\.

     You might be behind a corporate firewall that doesn't allow access to default ports such as the MySQL default port `3306`\. In this case, provide a port value that your corporate firewall allows\.
   + **Option Group**: Choose the option group that you want associated with the DB instance\. For more information, see [Working with Option Groups](USER_WorkingWithOptionGroups.md)\.
   + **Encryption**: Choose **Enable Encryption** for your new MariaDB DB instance to be encrypted "at rest\." If you choose **Enable Encryption**, you will be required to choose an AWS KMS encryption key as the **Master Key** value\.
   +  **Auto minor version upgrade**: Choose **Enable auto minor version upgrade** to enable your DB instance to receive preferred minor DB engine version upgrades automatically when they become available\. Amazon RDS performs automatic minor version upgrades in the maintenance window\. The **Auto minor version upgrade** option only applies to upgrades to MySQL minor engine versions for your MariaDB DB instance\. It doesn't apply to regular patches applied to maintain system stability\.   
![\[Migrate to MariaDB from MySQL\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/MigrateMariaDB.png)

1. Choose **Migrate**\.

## AWS CLI<a name="USER_Migrate_MariaDB.CLI"></a>

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
1. aws rds restore-db-instance-from-db-snapshot \
2.     --db-instance-identifier newmariadbinstance ^
3.     --db-snapshot-identifier mysqlsnapshot ^
4.     --engine mariadb
```

## API<a name="USER_Migrate_MariaDB.API"></a>

To migrate data from a MySQL DB snapshot to a MariaDB DB instance, call the Amazon RDS API operation [ `RestoreDBInstanceFromDBSnapshot`](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBInstanceFromDBSnapshot.html)\.

**Example**  

```
 1. https://rds.us-west-2.amazonaws.com/
 2.    ?Action=RestoreDBInstanceFromDBSnapshot
 3.    &DBInstanceIdentifier= newmariadbinstance
 4.    &DBSnapshotIdentifier= mysqlsnapshot
 5.    &Engine= mariadb
 6.    &SignatureMethod=HmacSHA256
 7.    &SignatureVersion=4
 8.    &Version=2013-09-09
 9.    &X-Amz-Algorithm=AWS4-HMAC-SHA256
10.    &X-Amz-Credential=AKIADQKE4SARGYLE/20140428/us-west-2/rds/aws4_request
11.    &X-Amz-Date=20140428T232655Z
12.    &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
13.    &X-Amz-Signature=78ac761e8c8f54a8c0727f4e67ad0a766fbb0024510b9aa34ea6d1f7df52fe92
```