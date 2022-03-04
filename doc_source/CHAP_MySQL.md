# MySQL on Amazon RDS<a name="CHAP_MySQL"></a>

Amazon RDS supports DB instances running several versions of MySQL\. You can use the following major versions:
+ MySQL 8\.0
+ MySQL 5\.7
+ MySQL 5\.6 \(Deprecation scheduled for February 1, 2022\)

For more information about minor version support, see [MySQL on Amazon RDS versions](#MySQL.Concepts.VersionMgmt)\.

You first use the Amazon RDS management tools or interfaces to create an Amazon RDS for MySQL DB instance\. You can then resize the DB instance, authorize connections to the DB instance, create and restore from backups or snapshots, create Multi\-AZ secondaries, create read replicas, and monitor the performance of the DB instance\. You use standard MySQL utilities and applications to store and access the data in the DB instance\.

Amazon RDS for MySQL is compliant with many industry standards\. For example, you can use RDS for MySQL databases to build HIPAA\-compliant applications and to store healthcare related information, including protected health information \(PHI\) under a Business Associate Agreement \(BAA\) with AWS\. Amazon RDS for MySQL also meets Federal Risk and Authorization Management Program \(FedRAMP\) security requirements and has received a FedRAMP Joint Authorization Board \(JAB\) Provisional Authority to Operate \(P\-ATO\) at the FedRAMP HIGH Baseline within the AWS GovCloud \(US\) Regions\. For more information on supported compliance standards, see [AWS cloud compliance](http://aws.amazon.com/compliance/)\.

For information about the features in each version of MySQL, see [The main features of MySQL](https://dev.mysql.com/doc/refman/8.0/en/features.html) in the MySQL documentation\.

## Common management tasks for Amazon RDS for MySQL<a name="MySQL.Concepts.General"></a>

The following are the common management tasks you perform with an RDS for MySQL DB instance, with links to relevant documentation for each task\.


****  

| Task area | Relevant documentation | 
| --- | --- | 
|  **Understanding Amazon RDS** Understand key Amazon RDS components, including DB instances, AWS Regions, Availability Zones, security groups, parameter groups, and option groups\.  |  [What is Amazon Relational Database Service \(Amazon RDS\)?](Welcome.md)  | 
|  **Setting up Amazon RDS for first time use** Set up Amazon RDS so that you can create MySQL DB instances in Amazon Web Services \(AWS\)\.  |  [Setting up for Amazon RDS](CHAP_SettingUp.md)   | 
|  **Understanding Amazon RDS DB instances** Create virtual MySQL server instances that run in AWS\. Because DB instances are the building blocks of Amazon RDS, we recommend that you understand their principles\.  |  [Amazon RDS DB instances](Overview.DBInstance.md)  | 
|  **Creating a DB instance for production** Create a DB instance for production purposes\. Creating an instance includes choosing a DB instance class with appropriate processing power and memory capacity and choosing a storage type that supports the way you expect to use your database\.   |   [DB instance classes](Concepts.DBInstanceClass.md)   [Amazon RDS storage types](CHAP_Storage.md#Concepts.Storage)   [Creating an Amazon RDS DB instance](USER_CreateDBInstance.md)   | 
|  **Managing security for your DB instance** By default, DB instances are created with a firewall that prevents access to them\. You must create a security group with the correct IP addresses and network configuration to access the DB instance\. You can also use AWS Identity and Access Management \(IAM\) policies to assign permissions that determine who is allowed to manage RDS resources\.  |   [Security in Amazon RDS](UsingWithRDS.md)   [Managing access using policies](UsingWithRDS.IAM.md#security_iam_access-manage)   [Controlling access with security groups](Overview.RDSSecurityGroups.md)   [Determining whether you are using the EC2\-VPC or EC2\-Classic platform](USER_VPC.FindDefaultVPC.md)   | 
|  **Connecting to your DB instance** Connect to your DB instance using a standard SQL client application such as the MySQL command line utility or MySQL Workbench\.  |   [Connecting to a DB instance running the MySQL database engine](USER_ConnectToInstance.md)   | 
|  **Configuring high availability for a production DB instance ** Provide high availability with synchronous standby replication in a different Availability Zone, automatic failover, fault tolerance for DB instances using Multi\-AZ deployments, and read replicas\.  |   [Multi\-AZ deployments for high availability](Concepts.MultiAZ.md)   | 
|  **Configuring a DB instance in an Amazon Virtual Private Cloud** Configure a virtual private cloud \(VPC\) in the Amazon VPC service\. An Amazon VPC is a virtual network logically isolated from other virtual networks in AWS\.  |   [Determining whether you are using the EC2\-VPC or EC2\-Classic platform](USER_VPC.FindDefaultVPC.md)   [Working with a DB instance in a VPC](USER_VPC.WorkingWithRDSInstanceinaVPC.md)   | 
|  **Configuring specific MySQL database parameters and features** Configure specific MySQL database parameters with a parameter group that can be associated with many DB instances\. You can also configure specific MySQL database features with an option group that can be associated with many DB instances\.  |   [Working with parameter groups](USER_WorkingWithParamGroups.md)   [Working with option groups](USER_WorkingWithOptionGroups.md)   [Options for MySQL DB instances](Appendix.MySQL.Options.md)   | 
|  **Modifying a DB instance running the MySQL database engine** Change the settings of a DB instance to accomplish tasks such as adding additional storage or changing the DB instance class\.  |   [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)   | 
|  **Configuring database backup and restore** Configure your DB instance to take automated backups\. You can also back up and restore your databases manually by using full backup files\.   |   [Working with backups](USER_WorkingWithAutomatedBackups.md)   [Backing up and restoring an Amazon RDS DB instance](CHAP_CommonTasks.BackupRestore.md)   | 
|  **Importing and exporting data** Import data from other MySQL DB instances, MySQL instances running external to Amazon RDS, and other types of data sources, and export data to MySQL instances running external to Amazon RDS\.  |   [Restoring a backup into a MySQL DB instance](MySQL.Procedural.Importing.md)   | 
|  **Monitoring a MySQL DB instance** Monitor your MySQL DB instance by using Amazon CloudWatch RDS metrics, events, and Enhanced Monitoring\. View log files for your MySQL DB instance\.  |   [Monitoring metrics in an Amazon RDS instance](CHAP_Monitoring.md)   [Viewing metrics in the Amazon RDS console](USER_Monitoring.md)   [Viewing Amazon RDS events](USER_ListEvents.md)   [Monitoring Amazon RDS log files](USER_LogAccess.md)   [MySQL database log files](USER_LogAccess.Concepts.MySQL.md)   | 
|  **Replicating your data** Create a MySQL read replica, in the same AWS Region or a different one\. You can use read replicas for load balancing, disaster recovery, and processing read\-heavy database workloads, such as for analysis and reporting\.   |   [Working with read replicas](USER_ReadRepl.md)   [Replication with a MariaDB or MySQL instance running external to Amazon RDS](MySQL.Procedural.Importing.External.Repl.md)   | 

There are also several sections with useful information about working with MySQL DB instances: 
+ [Common DBA tasks for MySQL DB instances](Appendix.MySQL.CommonDBATasks.md)
+ [Options for MySQL DB instances](Appendix.MySQL.Options.md)
+ [MySQL on Amazon RDS SQL reference](Appendix.MySQL.SQLRef.md)

## MySQL on Amazon RDS versions<a name="MySQL.Concepts.VersionMgmt"></a>

For MySQL, version numbers are organized as version = X\.Y\.Z\. In Amazon RDS terminology, X\.Y denotes the major version, and Z is the minor version number\. For Amazon RDS implementations, a version change is considered major if the major version number changes—for example, going from version 5\.7 to 8\.0\. A version change is considered minor if only the minor version number changes—for example, going from version 5\.7\.16 to 5\.7\.21\. 

Amazon RDS currently supports the following versions of MySQL: 


****  

| Major version | Minor version | 
| --- | --- | 
| MySQL 8\.0 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_MySQL.html)  | 
| MySQL 5\.7 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_MySQL.html)  | 
| MySQL 5\.6 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_MySQL.html)  | 

You can specify any currently supported MySQL version when creating a new DB instance\. You can specify the major version \(such as MySQL 5\.7\), and any supported minor version for the specified major version\. If no version is specified, Amazon RDS defaults to a supported version, typically the most recent version\. If a major version is specified but a minor version is not, Amazon RDS defaults to a recent release of the major version you have specified\. To see a list of supported versions, as well as defaults for newly created DB instances, use the [https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-engine-versions.html](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-engine-versions.html) AWS CLI command\.

The default MySQL version might vary by AWS Region\. To create a DB instance with a specific minor version, specify the minor version during DB instance creation\. You can determine the default minor version for an AWS Region using the following AWS CLI command:

```
aws rds describe-db-engine-versions --default-only --engine mysql --engine-version major-engine-version --region region --query "*[].{Engine:Engine,EngineVersion:EngineVersion}" --output text
```

Replace *major\-engine\-version* with the major engine version, and replace *region* with the AWS Region\. For example, the following AWS CLI command returns the default MySQL minor engine version for the 5\.7 major version and the US West \(Oregon\) AWS Region \(us\-west\-2\):

```
aws rds describe-db-engine-versions --default-only --engine mysql --engine-version 5.7 --region us-west-2 --query '*[].{Engine:Engine,EngineVersion:EngineVersion}' --output text
```

With Amazon RDS, you control when to upgrade your MySQL instance to a new major version supported by Amazon RDS\. You can maintain compatibility with specific MySQL versions, test new versions with your application before deploying in production, and perform major version upgrades at times that best fit your schedule\.

When automatic minor version upgrade is enabled, your DB instance will be upgraded automatically to new MySQL minor versions as they are supported by Amazon RDS\. This patching occurs during your scheduled maintenance window\. You can modify a DB instance to enable or disable automatic minor version upgrades\. 

If you opt out of automatically scheduled upgrades, you can manually upgrade to a supported minor version release by following the same procedure as you would for a major version update\. For information, see [Upgrading a DB instance engine version](USER_UpgradeDBInstance.Upgrading.md)\. 

Amazon RDS currently supports the major version upgrades from MySQL version 5\.6 to version 5\.7, and from MySQL version 5\.7 to version 8\.0\. Because major version upgrades involve some compatibility risk, they do not occur automatically; you must make a request to modify the DB instance\. You should thoroughly test any upgrade before upgrading your production instances\. For information about upgrading a MySQL DB instance, see [Upgrading the MySQL DB engine](USER_UpgradeDBInstance.MySQL.md)\. 

You can test a DB instance against a new version before upgrading by creating a DB snapshot of your existing DB instance, restoring from the DB snapshot to create a new DB instance, and then initiating a version upgrade for the new DB instance\. You can then experiment safely on the upgraded clone of your DB instance before deciding whether or not to upgrade your original DB instance\. 

### Deprecation of MySQL version 5\.6<a name="MySQL.Concepts.VersionMgmt.Deprecation56"></a>

On February 1, 2022, Amazon RDS plans to deprecate support for MySQL 5\.6 using the following schedule, which includes upgrade recommendations\. We recommend that you upgrade all MySQL 5\.6 DB instances to MySQL 5\.7 or higher as soon as possible\. For more information, see [Upgrading the MySQL DB engine](USER_UpgradeDBInstance.MySQL.md)\.


| Action or recommendation | Dates | 
| --- | --- | 
|  We recommend that you upgrade MySQL 5\.6 DB instances manually to the version of your choice\.   |  Now–February 1, 2022  | 
|  We recommend that you upgrade MySQL 5\.6 snapshots manually to the version of your choice\.  |  Now–February 1, 2022  | 
|  You can no longer create new MySQL 5\.6 DB instances\. You can still create read replicas of existing MySQL 5\.6 DB instances and change them from Single\-AZ deployments to Multi\-AZ deployments\.  |  February 1, 2022  | 
|  Amazon RDS starts automatic upgrades of your MySQL 5\.6 DB instances to version 5\.7\.  |  March 1, 2022  | 
|  Amazon RDS starts automatic upgrades to version 5\.7 for any MySQL 5\.6 DB instances restored from snapshots\.  |  March 1, 2022  | 

For more information about Amazon RDS for MySQL 5\.6 deprecation, see [ Announcement: Extending end\-of\-life process for Amazon RDS for MySQL 5\.6](http://forums.aws.amazon.com/ann.jspa?annID=8790)\.

## MySQL features not supported by Amazon RDS<a name="MySQL.Concepts.Features"></a>

Amazon RDS doesn't currently support the following MySQL features: 
+ Authentication Plugin
+ Error Logging to the System Log
+ Group Replication Plugin
+ InnoDB Tablespace Encryption
+ Password Strength Plugin
+ Persisted system variables
+ Semisynchronous replication
+ Transportable tablespace
+ X Plugin

**Note**  
Global transaction IDs are supported for RDS for MySQL 5\.7\.23 and higher 5\.7 versions, and for MySQL 8\.0\.26 and higher 8\.0 versions\. Global transaction IDs are not supported for RDS for MySQL 5\.6\.

To deliver a managed service experience, Amazon RDS doesn't provide shell access to DB instances\. It also restricts access to certain system procedures and tables that require advanced privileges\. Amazon RDS supports access to databases on a DB instance using any standard SQL client application\. Amazon RDS doesn't allow direct host access to a DB instance by using Telnet, Secure Shell \(SSH\), or Windows Remote Desktop Connection\. When you create a DB instance, you are assigned to the *db\_owner* role for all databases on that instance, and you have all database\-level permissions except for those used for backups\. Amazon RDS manages backups for you\. 

## Supported storage engines for RDS for MySQL<a name="MySQL.Concepts.Storage"></a>

While MySQL supports multiple storage engines with varying capabilities, not all of them are optimized for recovery and data durability\. Amazon RDS fully supports the InnoDB storage engine for MySQL DB instances\. Amazon RDS features such as Point\-In\-Time restore and snapshot restore require a recoverable storage engine and are supported for the InnoDB storage engine only\. You must be running an instance of MySQL 5\.6 or later to use the InnoDB `memcached` interface\. For more information, see [MySQL memcached support](Appendix.MySQL.Options.memcached.md)\. 

The Federated Storage Engine is currently not supported by Amazon RDS for MySQL\. 

For user\-created schemas, the MyISAM storage engine does not support reliable recovery and can result in lost or corrupt data when MySQL is restarted after a recovery, preventing Point\-In\-Time restore or snapshot restore from working as intended\. However, if you still choose to use MyISAM with Amazon RDS, snapshots can be helpful under some conditions\. 

**Note**  
System tables in the `mysql` schema can be in MyISAM storage\.

If you want to convert existing MyISAM tables to InnoDB tables, you can use the `ALTER TABLE` command \(for example, `alter table TABLE_NAME engine=innodb;`\)\. Bear in mind that MyISAM and InnoDB have different strengths and weaknesses, so you should fully evaluate the impact of making this switch on your applications before doing so\. 

MySQL 5\.1 and 5\.5 are no longer supported in Amazon RDS\. However, you can restore existing MySQL 5\.1 and 5\.5 snapshots\. When you restore a MySQL 5\.1 or 5\.5 snapshot, the DB instance is automatically upgraded to MySQL 5\.6\. 

## Storage\-full behavior for Amazon RDS for MySQL<a name="MySQL.Concepts.StorageFullBehavior"></a>

When storage becomes full for a MySQL DB instance, there can be metadata inconsistencies, dictionary mismatches, and orphan tables\. To prevent these issues, Amazon RDS automatically stops a DB instance that reaches the `storage-full` state\.

A MySQL DB instance reaches the `storage-full` state in the following cases:
+ The DB instance has less than 20,000 MiB of storage, and available storage reaches 200 MiB or less\.
+ The DB instance has more than 102,400 MiB of storage, and available storage reaches 1024 MiB or less\.
+ The DB instance has between 20,000 MiB and 102,400 MiB of storage, and has less than 1% of storage available\.

After Amazon RDS stops a DB instance automatically because it reached the `storage-full` state, you can still modify it\. To restart the DB instance, complete at least one of the following:
+ Modify the DB instance to enable storage autoscaling\.

  For more information about storage autoscaling, see [Managing capacity automatically with Amazon RDS storage autoscaling](USER_PIOPS.StorageTypes.md#USER_PIOPS.Autoscaling)\.
+ Modify the DB instance to increase its storage capacity\.

  For more information about increasing storage capacity, see [Increasing DB instance storage capacity](USER_PIOPS.StorageTypes.md#USER_PIOPS.ModifyingExisting)\.

After you make one of these changes, the DB instance is restarted automatically\. For information about modifying a DB instance, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\.

## MySQL security on Amazon RDS<a name="MySQL.Concepts.UsersAndPrivileges"></a>

Security for MySQL DB instances is managed at three levels:
+ AWS Identity and Access Management controls who can perform Amazon RDS management actions on DB instances\. When you connect to AWS using IAM credentials, your IAM account must have IAM policies that grant the permissions required to perform Amazon RDS management operations\. For more information, see [Identity and access management in Amazon RDS](UsingWithRDS.IAM.md)\. 
+ When you create a DB instance, you use either a VPC security group or a DB security group to control which devices and Amazon EC2 instances can open connections to the endpoint and port of the DB instance\. These connections can be made using Secure Sockets Layer \(SSL\)\. In addition, firewall rules at your company can control whether devices running at your company can open connections to the DB instance\. 
+ To authenticate login and permissions for a MySQL DB instance, you can take either of the following approaches, or a combination of them\. 

  You can take the same approach as with a stand\-alone instance of MySQL\. Commands such as `CREATE USER`, `RENAME USER`, `GRANT`, `REVOKE`, and `SET PASSWORD` work just as they do in on\-premises databases, as does directly modifying database schema tables\. For information, see [ Access control and account management](https://dev.mysql.com/doc/refman/8.0/en/access-control.html) in the MySQL documentation\. 

  You can also use IAM database authentication\. With IAM database authentication, you authenticate to your DB instance by using an IAM user or IAM role and an authentication token\. An *authentication token* is a unique value that is generated using the Signature Version 4 signing process\. By using IAM database authentication, you can use the same credentials to control access to your AWS resources and your databases\. For more information, see [IAM database authentication for MariaDB, MySQL, and PostgreSQL](UsingWithRDS.IAMDBAuth.md)\. 

 When you create an Amazon RDS DB instance, the master user has the following default privileges: 
+ `alter`
+ `alter routine`
+ `create`
+ `create routine`
+ `create temporary tables`
+ `create user`
+ `create view`
+ `delete`
+ `drop`
+ `event`
+ `execute`
+ `grant option`
+ `index`
+ `insert`
+ `lock tables`
+ `process`
+ `references`
+ `replication client`
+ `replication slave (MySQL 5.6 and later) `
+ `select`
+ `show databases`
+ `show view`
+ `trigger`
+ `update`

**Note**  
Although it is possible to delete the master user on the DB instance, it is not recommended\. To recreate the master user, use the [ModifyDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) RDS API operation or the [modify\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) AWS CLI command and specify a new master user password with the appropriate parameter\. If the master user does not exist in the instance, the master user is created with the specified password\. 

To provide management services for each DB instance, the `rdsadmin` user is created when the DB instance is created\. Attempting to drop, rename, change the password, or change privileges for the `rdsadmin` account will result in an error\. 

To allow management of the DB instance, the standard `kill` and `kill_query` commands have been restricted\. The Amazon RDS commands `rds_kill` and `rds_kill_query` are provided to allow you to end user sessions or queries on DB instances\. 

## Using the Password Validation Plugin<a name="MySQL.Concepts.PasswordValidationPlugin"></a>

MySQL provides the `validate_password` plugin for improved security\. The plugin enforces password policies using parameters in the DB parameter group for your MySQL DB instance\. The plugin is supported for DB instances running MySQL version 5\.6, 5\.7, and 8\.0\. For more information about the `validate_password` plugin, see [The Password Validation Plugin](https://dev.mysql.com/doc/refman/8.0/en/validate-password.html) in the MySQL documentation\.

**To enable the validate\_password plugin for a MySQL DB instance**

1. Connect to your MySQL DB instance and run the following command\.

   ```
   INSTALL PLUGIN validate_password SONAME 'validate_password.so';                    
   ```

1. Configure the parameters for the plugin in the DB parameter group used by the DB instance\.

   For more information about the parameters, see [Password Validation Plugin options and variables](https://dev.mysql.com/doc/refman/8.0/en/validate-password-options-variables.html) in the MySQL documentation\.

   For more information about modifying DB instance parameters, see [Modifying parameters in a DB parameter group](USER_WorkingWithDBInstanceParamGroups.md#USER_WorkingWithParamGroups.Modifying)\.

After installing and enabling the `password_validate` plugin, reset existing passwords to comply with your new validation policies\.

Amazon RDS doesn't validate passwords\. The MySQL DB instance performs password validation\. If you set a user password with the AWS Management Console, the `modify-db-instance` AWS CLI command, or the `ModifyDBInstance` RDS API operation, the change can succeed even if the new password doesn't satisfy your password policies\. However, a new password is set in the MySQL DB instance only if it satisfies the password policies\. In this case, Amazon RDS records the following event\.

```
"RDS-EVENT-0067" - An attempt to reset the master password for the DB instance has failed.            
```

For more information about Amazon RDS events, see [Using Amazon RDS event notification](USER_Events.md)\.

## Using SSL with a MySQL DB instance<a name="MySQL.Concepts.SSLSupport"></a>

Amazon RDS supports Secure Sockets Layer \(SSL\) connections with DB instances running the MySQL database engine\. 

Amazon RDS creates an SSL certificate and installs the certificate on the DB instance when Amazon RDS provisions the instance\. These certificates are signed by a certificate authority\. The SSL certificate includes the DB instance endpoint as the Common Name \(CN\) for the SSL certificate to guard against spoofing attacks\. 

An SSL certificate created by Amazon RDS is the trusted root entity and should work in most cases but might fail if your application does not accept certificate chains\. If your application does not accept certificate chains, you might need to use an intermediate certificate to connect to your AWS Region\. For example, you must use an intermediate certificate to connect to the AWS GovCloud \(US\) Regions using SSL\.

For information about downloading certificates, see [Using SSL/TLS to encrypt a connection to a DB instance](UsingWithRDS.SSL.md)\. For more information about using SSL with MySQL, see [Updating applications to connect to MySQL DB instances using new SSL/TLS certificates](ssl-certificate-rotation-mysql.md)\.

MySQL uses yaSSL for secure connections in the following versions:
+ MySQL version 5\.7\.19 and earlier 5\.7 versions
+ MySQL version 5\.6\.37 and earlier 5\.6 versions

MySQL uses OpenSSL for secure connections in the following versions:
+ MySQL version 8\.0
+ MySQL version 5\.7\.21 and later 5\.7 versions
+ MySQL version 5\.6\.39 and later 5\.6 versions

Amazon RDS for MySQL supports Transport Layer Security \(TLS\) versions 1\.0, 1\.1, and 1\.2\. The following table shows the TLS support for MySQL versions\. 


****  

| MySQL version | TLS 1\.0 | TLS 1\.1 | TLS 1\.2 | 
| --- | --- | --- | --- | 
|  MySQL 8\.0  |  Supported  |  Supported  |  Supported  | 
|  MySQL 5\.7  |  Supported  |  Supported  |  Supported for MySQL 5\.7\.21 and later  | 
|  MySQL 5\.6  |  Supported  |  Supported for MySQL 5\.6\.46 and later  |  Supported for MySQL 5\.6\.46 and later  | 

To encrypt connections using the default `mysql` client, launch the mysql client using the `--ssl-ca` parameter to reference the public key, as shown in the examples following\. 

The following example shows how to launch the client using the `--ssl-ca` parameter for MySQL 5\.7 and later\.

```
mysql -h myinstance.c9akciq32.rds-us-east-1.amazonaws.com
--ssl-ca=[full path]rds-combined-ca-bundle.pem --ssl-mode=VERIFY_IDENTITY
```

The following example shows how to launch the client using the `--ssl-ca` parameter for MySQL 5\.6 and earlier\.

```
mysql -h myinstance.c9akciq32.rds-us-east-1.amazonaws.com
--ssl-ca=[full path]rds-combined-ca-bundle.pem --ssl-verify-server-cert
```

For information about downloading certificate bundles, see [Using SSL/TLS to encrypt a connection to a DB instance](UsingWithRDS.SSL.md)\.

You can require SSL connections for specific users accounts\. For example, you can use one of the following statements, depending on your MySQL version, to require SSL connections on the user account `encrypted_user`\.

For MySQL 5\.7 and later, use the following statement\.

```
ALTER USER 'encrypted_user'@'%' REQUIRE SSL;            
```

For MySQL 5\.6 and earlier, use the following statement\.

```
GRANT USAGE ON *.* TO 'encrypted_user'@'%' REQUIRE SSL;            
```

For more information on SSL connections with MySQL, see the [ Using encrypted connections](https://dev.mysql.com/doc/refman/8.0/en/encrypted-connections.html) in the MySQL documentation\.

## Using memcached and other options with MySQL<a name="MySQL.Concepts.General.Options"></a>

Most Amazon RDS DB engines support option groups that allow you to select additional features for your DB instance\. DB instances on MySQL version 5\.6 and later support the `memcached` option, a simple, key\-based cache\. For more information about `memcached` and other options, see [Options for MySQL DB instances](Appendix.MySQL.Options.md)\. For more information about working with option groups, see [Working with option groups](USER_WorkingWithOptionGroups.md)\. 

## InnoDB cache warming<a name="MySQL.Concepts.InnoDBCacheWarming"></a>

InnoDB cache warming can provide performance gains for your MySQL DB instance by saving the current state of the buffer pool when the DB instance is shut down, and then reloading the buffer pool from the saved information when the DB instance starts up\. This bypasses the need for the buffer pool to "warm up" from normal database use and instead preloads the buffer pool with the pages for known common queries\. The file that stores the saved buffer pool information only stores metadata for the pages that are in the buffer pool, and not the pages themselves\. As a result, the file does not require much storage space\. The file size is about 0\.2 percent of the cache size\. For example, for a 64 GiB cache, the cache warming file size is 128 MiB\. For more information on InnoDB cache warming, see [Saving and restoring the buffer pool state](https://dev.mysql.com/doc/refman/8.0/en/innodb-preload-buffer-pool.html) in the MySQL documentation\. 

MySQL on Amazon RDS supports InnoDB cache warming for MySQL version 5\.6 and later\. To enable InnoDB cache warming, set the `innodb_buffer_pool_dump_at_shutdown` and `innodb_buffer_pool_load_at_startup` parameters to 1 in the parameter group for your DB instance\. Changing these parameter values in a parameter group will affect all MySQL DB instances that use that parameter group\. To enable InnoDB cache warming for specific MySQL DB instances, you might need to create a new parameter group for those instances\. For information on parameter groups, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\. 

InnoDB cache warming primarily provides a performance benefit for DB instances that use standard storage\. If you use PIOPS storage, you do not commonly see a significant performance benefit\. 

**Important**  
If your MySQL DB instance does not shut down normally, such as during a failover, then the buffer pool state will not be saved to disk\. In this case, MySQL loads whatever buffer pool file is available when the DB instance is restarted\. No harm is done, but the restored buffer pool might not reflect the most recent state of the buffer pool prior to the restart\. To ensure that you have a recent state of the buffer pool available to warm the InnoDB cache on startup, we recommend that you periodically dump the buffer pool "on demand\." You can dump or load the buffer pool on demand if your DB instance is running MySQL version 5\.6\.19 or later\.  
You can create an event to dump the buffer pool automatically and on a regular interval\. For example, the following statement creates an event named `periodic_buffer_pool_dump` that dumps the buffer pool every hour\.   

```
1. CREATE EVENT periodic_buffer_pool_dump 
2. ON SCHEDULE EVERY 1 HOUR 
3. DO CALL mysql.rds_innodb_buffer_pool_dump_now();
```
For more information on MySQL events, see [Event syntax](https://dev.mysql.com/doc/refman/8.0/en/events-syntax.html) in the MySQL documentation\. 

### Dumping and loading the buffer pool on demand<a name="MySQL.Concepts.InnoDBCacheWarming.OnDemand"></a>

For MySQL version 5\.6\.19 and later, you can save and load the InnoDB cache "on demand\."
+ To dump the current state of the buffer pool to disk, call the [mysql\.rds\_innodb\_buffer\_pool\_dump\_now](mysql_rds_innodb_buffer_pool_dump_now.md) stored procedure\.
+ To load the saved state of the buffer pool from disk, call the [mysql\.rds\_innodb\_buffer\_pool\_load\_now](mysql_rds_innodb_buffer_pool_load_now.md) stored procedure\.
+ To cancel a load operation in progress, call the [mysql\.rds\_innodb\_buffer\_pool\_load\_abort](mysql_rds_innodb_buffer_pool_load_abort.md) stored procedure\.

## Local time zone for MySQL DB instances<a name="MySQL.Concepts.LocalTimeZone"></a>

By default, the time zone for a MySQL DB instance is Universal Time Coordinated \(UTC\)\. You can set the time zone for your DB instance to the local time zone for your application instead\. 

To set the local time zone for a DB instance, set the `time_zone` parameter in the parameter group for your DB instance to one of the supported values listed later in this section\. When you set the `time_zone` parameter for a parameter group, all DB instances and read replicas that are using that parameter group change to use the new local time zone\. For information on setting parameters in a parameter group, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\. 

After you set the local time zone, all new connections to the database reflect the change\. If you have any open connections to your database when you change the local time zone, you won't see the local time zone update until after you close the connection and open a new connection\. 

You can set a different local time zone for a DB instance and one or more of its read replicas\. To do this, use a different parameter group for the DB instance and the replica or replicas and set the `time_zone` parameter in each parameter group to a different local time zone\. 

If you are replicating across AWS Regions, then the source DB instance and the read replica use different parameter groups \(parameter groups are unique to an AWS Region\)\. To use the same local time zone for each instance, you must set the `time_zone` parameter in the instance's and read replica's parameter groups\. 

When you restore a DB instance from a DB snapshot, the local time zone is set to UTC\. You can update the time zone to your local time zone after the restore is complete\. If you restore a DB instance to a point in time, then the local time zone for the restored DB instance is the time zone setting from the parameter group of the restored DB instance\. 

You can set your local time zone to one of the following values\. 


****  

|  |  | 
| --- |--- |
| `Africa/Cairo` | `Asia/Riyadh` | 
| `Africa/Casablanca` | `Asia/Seoul` | 
| `Africa/Harare` | `Asia/Shanghai` | 
| `Africa/Monrovia` | `Asia/Singapore` | 
| `Africa/Nairobi` | `Asia/Taipei` | 
| `Africa/Tripoli` | `Asia/Tehran` | 
| `Africa/Windhoek` | `Asia/Tokyo` | 
| `America/Araguaina` | `Asia/Ulaanbaatar` | 
| `America/Asuncion` | `Asia/Vladivostok` | 
| `America/Bogota` | `Asia/Yakutsk` | 
| `America/Buenos_Aires` | `Asia/Yerevan` | 
| `America/Caracas` | `Atlantic/Azores` | 
| `America/Chihuahua` | `Australia/Adelaide` | 
| `America/Cuiaba` | `Australia/Brisbane` | 
| `America/Denver` | `Australia/Darwin` | 
| `America/Fortaleza` | `Australia/Hobart` | 
| `America/Guatemala` | `Australia/Perth` | 
| `America/Halifax` | `Australia/Sydney` | 
| `America/Manaus` | `Brazil/East` | 
| `America/Matamoros` | `Canada/Newfoundland` | 
| `America/Monterrey` | `Canada/Saskatchewan` | 
| `America/Montevideo` | `Canada/Yukon` | 
| `America/Phoenix` | `Europe/Amsterdam` | 
| `America/Santiago` | `Europe/Athens` | 
| `America/Tijuana` | `Europe/Dublin` | 
| `Asia/Amman` | `Europe/Helsinki` | 
| `Asia/Ashgabat` | `Europe/Istanbul` | 
| `Asia/Baghdad` | `Europe/Kaliningrad` | 
| `Asia/Baku` | `Europe/Moscow` | 
| `Asia/Bangkok` | `Europe/Paris` | 
| `Asia/Beirut` | `Europe/Prague` | 
| `Asia/Calcutta` | `Europe/Sarajevo` | 
| `Asia/Damascus` | `Pacific/Auckland` | 
| `Asia/Dhaka` | `Pacific/Fiji` | 
| `Asia/Irkutsk` | `Pacific/Guam` | 
| `Asia/Jerusalem` | `Pacific/Honolulu` | 
| `Asia/Kabul` | `Pacific/Samoa` | 
| `Asia/Karachi` | `US/Alaska` | 
| `Asia/Kathmandu` | `US/Central` | 
| `Asia/Krasnoyarsk` | `US/Eastern` | 
| `Asia/Magadan` | `US/East-Indiana` | 
| `Asia/Muscat` | `US/Pacific` | 
| `Asia/Novosibirsk` | `UTC` | 

## Known issues and limitations for Amazon RDS for MySQL<a name="MySQL.Concepts.KnownIssuesAndLimitations"></a>

There are some known issues and limitations for working with MySQL on Amazon RDS for MySQL\. For more information, see [Known issues and limitations for Amazon RDS for MySQL](MySQL.KnownIssuesAndLimitations.md)\. 

## Deprecated versions for Amazon RDS for MySQL<a name="MySQL.Concepts.DeprecatedVersions"></a>

Amazon RDS for MySQL version 5\.1 and 5\.5 are deprecated\.

For more information about Amazon RDS for MySQL 5\.5 deprecation, see [ Announcement: Extending end\-of\-life process for Amazon RDS for MySQL 5\.5](http://forums.aws.amazon.com/ann.jspa?annID=8570)\.

For information about the Amazon RDS deprecation policy for MySQL, see [Amazon RDS FAQs](http://aws.amazon.com/rds/faqs/)\.