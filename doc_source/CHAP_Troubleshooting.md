# Troubleshooting<a name="CHAP_Troubleshooting"></a>

Use the following sections to help troubleshoot problems you have with Amazon RDS\. 

**Topics**
+ [Cannot Connect to Amazon RDS DB Instance](#CHAP_Troubleshooting.Connecting)
+ [Amazon RDS Security Issues](#CHAP_Troubleshooting.Security)
+ [Resetting the DB Instance Owner Role Password](#CHAP_Troubleshooting.ResetPassword)
+ [Amazon RDS DB Instance Outage or Reboot](#CHAP_Troubleshooting.Reboots)
+ [Amazon RDS DB Parameter Changes Not Taking Effect](#CHAP_Troubleshooting.Parameters)
+ [Amazon RDS DB Instance Running Out of Storage](#CHAP_Troubleshooting.Storage)
+ [Amazon RDS Insufficient DB Instance Capacity](#CHAP_Troubleshooting.Capacity)
+ [Amazon RDS MySQL and MariaDB Issues](#CHAP_Troubleshooting.MySQL)
+ [Amazon Aurora Issues](#CHAP_Troubleshooting.Aurora)
+ [Amazon RDS Oracle GoldenGate Issues](#CHAP_Troubleshooting.Oracle.GoldenGate)
+ [Cannot Connect to Amazon RDS SQL Server DB Instance](#CHAP_Troubleshooting.SQLServer.Connect)
+ [Cannot Connect to Amazon RDS PostgreSQL DB Instance](#CHAP_Troubleshooting.PostgreSQL.Connect)
+ [Cannot Set Backup Retention Period to 0](#CHAP_Troubleshooting.Backup.Retention)

## Cannot Connect to Amazon RDS DB Instance<a name="CHAP_Troubleshooting.Connecting"></a>

When you cannot connect to a DB instance, the following are common causes:
+ The access rules enforced by your local firewall and the ingress IP addresses that you authorized to access your DB instance in the instance's security group are not in sync\. The problem is most likely the ingress rules in your security group\. By default, DB instances do not allow access; access is granted through a security group\. To grant access, you must create your own security group with specific ingress and egress rules for your situation\. For more information about setting up a security group, see [Provide Access to Your DB Instance in Your VPC by Creating a Security Group](CHAP_SettingUp.md#CHAP_SettingUp.SecurityGroup)\.
+ The port you specified when you created the DB instance cannot be used to send or receive communications due to your local firewall restrictions\. In this case, check with your network administrator to determine if your network allows the specified port to be used for inbound and outbound communication\.
+ Your DB instance is still being created and is not yet available\. Depending on the size of your DB instance, it can take up to 20 minutes before an instance is available\. 

### Testing a Connection to an Amazon RDS DB Instance<a name="CHAP_Troubleshooting.Connecting.Test"></a>

You can test your connection to a DB instance using common Linux or Windows tools\. 

From a Linux or Unix terminal, you can test the connection by typing the following \(replace `<DB-instance-endpoint>` with the endpoint and `<port>` with the port of your DB instance\):

```
1.   $nc -zv <DB-instance-endpoint> <port> 
```

For example, the following shows a sample command and the return value:

```
1.   $nc -zv postgresql1.c6c8mn7tsdgv0.us-west-2.rds.amazonaws.com 8299
2. 
3.   Connection to postgresql1.c6c8mn7tsdgv0.us-west-2.rds.amazonaws.com 8299 port [tcp/vvr-data] succeeded!
```

Windows users can use Telnet to test the connection to a DB instance\. Note that Telnet actions are not supported other than for testing the connection\. If a connection is successful, the action returns no message\. If a connection is not successful, you receive an error message such as the following:

```
1.   C:\>telnet sg-postgresql1.c6c8mntzhgv0.us-west-2.rds.amazonaws.com 819
2.   
3.   Connecting To sg-postgresql1.c6c8mntzhgv0.us-west-2.rds.amazonaws.com...Could not open 
4.   connection to the host, on port 819: Connect failed
```

If Telnet actions return success, your security group is properly configured\.

### Troubleshooting Connection Authentication<a name="CHAP_Troubleshooting.Connecting.Authorization"></a>

If you can connect to your DB instance but you get authentication errors, you might want to reset the master user password for the DB instance\. You can do this by modifying the RDS instance; for more information, see one of the following topics:
+ [Modifying a DB Instance Running the MySQL Database Engine](USER_ModifyInstance.MySQL.md)
+ [Modifying a DB Instance Running the Oracle Database Engine](USER_ModifyInstance.Oracle.md)
+ [Modifying a DB Instance Running the Microsoft SQL Server Database Engine](USER_ModifyInstance.SQLServer.md)
+ [Modifying a DB Instance Running the PostgreSQL Database Engine](USER_ModifyPostgreSQLInstance.md)

## Amazon RDS Security Issues<a name="CHAP_Troubleshooting.Security"></a>

To avoid security issues, never use your master AWS user name and password for a user account\. Best practice is to use your master AWS account to create IAM users and assign those to DB user accounts\. You can also use your master account to create other user accounts, if necessary\. For more information on creating IAM users, see [Create an IAM User](CHAP_SettingUp.md#CHAP_SettingUp.IAM)\. 

### Error Message "Failed to retrieve account attributes, certain console functions may be impaired\."<a name="CHAP_Troubleshooting.Security.AccountAttributes"></a>

There are several reasons you would get this error; it could be because your account is missing permissions, or your account has not been properly set up\. If your account is new, you may not have waited for the account to be ready\. If this is an existing account, you could lack permissions in your access policies to perform certain actions such as creating a DB instance\. To fix the issue, your IAM administrator needs to provide the necessary roles to your account\. For more information, see the IAM documentation\.

## Resetting the DB Instance Owner Role Password<a name="CHAP_Troubleshooting.ResetPassword"></a>

You can reset the assigned permissions for your DB instance by resetting the master password\. For example, if you lock yourself out of the `db_owner` role on your SQL Server database, you can reset the `db_owner` role password by modifying the DB instance master password\. By changing the DB instance password, you can regain access to the DB instance, access databases using the modified password for the `db_owner`, and restore privileges for the `db_owner` role that may have been accidentally revoked\. You can change the DB instance password by using the Amazon RDS console, the AWS CLI command [modify\-db\-instance](http://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html), or by using the [ModifyDBInstance](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference//API_ModifyDBInstance.html) action\. For more information about modifying a SQL Server DB instance, see [Modifying a DB Instance Running the Microsoft SQL Server Database Engine](USER_ModifyInstance.SQLServer.md)\.

## Amazon RDS DB Instance Outage or Reboot<a name="CHAP_Troubleshooting.Reboots"></a>

 A DB instance outage can occur when a DB instance is rebooted, when the DB instance is put into a state that prevents access to it, and when the database is restarted\. A reboot can occur when you manually reboot your DB instance or when you change a DB instance setting that requires a reboot before it can take effect\.   

When you modify a setting for a DB instance, you can determine when the change is applied by using the **Apply Immediately** setting\. To see a table that shows DB instance actions and the effect that setting the **Apply Immediately** value has, see [Modifying an Amazon RDS DB Instance and Using the Apply Immediately Parameter](Overview.DBInstance.Modifying.md)\.  

 A DB instance reboot only occurs when you change a setting that requires a reboot, or when you manually cause a reboot\. A reboot can occur immediately if you change a setting and request that the change take effect immediately or it can occur during the DB instance's maintenance window\. 

 A DB instance reboot occurs immediately when one of the following occurs: 
+ You change the backup retention period for a DB instance from 0 to a nonzero value or from a nonzero value to 0 and set **Apply Immediately** to *true*\. 
+ You change the DB instance class, and **Apply Immediately** is set to *true*\. 
+ You change the storage type from **Magnetic \(Standard\)** to **General Purpose \(SSD**\) or **Provisioned IOPS \(SSD\)**, or from **Provisioned IOPS \(SSD\)** or **General Purpose \(SSD\)** to **Magnetic \(Standard\)**\. from standard to PIOPS\. 

A DB instance reboot occurs during the maintenance window when one of the following occurs:
+ You change the backup retention period for a DB instance from 0 to a nonzero value or from a nonzero value to 0, and **Apply Immediately** is set to *false*\. 
+ You change the DB instance class, and **Apply Immediately** is set to *false*\.

When you change a static parameter in a DB parameter group, the change will not take effect until the DB instance associated with the parameter group is rebooted\. The change requires a manual reboot; the DB instance will not automatically be rebooted during the maintenance window\.

## Amazon RDS DB Parameter Changes Not Taking Effect<a name="CHAP_Troubleshooting.Parameters"></a>

If you change a parameter in a DB parameter group but you don't see the changes take effect, you most likely need to reboot the DB instance associated with the DB parameter group\. When you change a dynamic parameter, the change takes effect immediately; when you change a static parameter, the change won't take effect until you reboot the DB instance associated with the parameter group\. 

You can reboot a DB instance using the RDS console or explicitly calling the `RebootDbInstance` API action \(without failover, if the DB instance is in a Multi\-AZ deployment\)\. The requirement to reboot the associated DB instance after a static parameter change helps mitigate the risk of a parameter misconfiguration affecting an API call, such as calling `ModifyDBInstance` to change DB instance class or scale storage\. For more information, see [Modifying Parameters in a DB Parameter Group](USER_WorkingWithParamGroups.md#USER_WorkingWithParamGroups.Modifying)\.

## Amazon RDS DB Instance Running Out of Storage<a name="CHAP_Troubleshooting.Storage"></a>

If your DB instance runs out of storage space, it might no longer be available\. We highly recommend that you constantly monitor the `FreeStorageSpace` metric published in CloudWatch to ensure that your DB instance has enough free storage space\.

If your database instance runs out of storage, its status will change to *storage\-full*\. For example, a call to the `DescribeDBInstances` action for a DB instance that has used up its storage will output the following:

```
1. aws rds describe-db-instances --db-instance-identifier mydbinstance
2. 				
3. DBINSTANCE  mydbinstance  2009-12-22T23:06:11.915Z  db.m3.large  mysql5.6  50  sa  
4. storage-full  mydbinstance.clla4j4jgyph.us-east-1.rds.amazonaws.com  3306  
5. us-east-1b  3
6. 	SECGROUP  default  active
7. 	PARAMGRP  default.mysql5.6  in-sync
```

To recover from this scenario, add more storage space to your instance using the `ModifyDBInstance` action or the following AWS CLI command:

For Linux, OS X, or Unix:

```
1. aws rds modify-db-instance \
2.     --db-instance-identifier mydbinstance \
3.     --allocated-storage 60 \
4.     --apply-immediately
```

For Windows:

```
1. aws rds modify-db-instance ^
2.     --db-instance-identifier mydbinstance ^
3.     --allocated-storage 60 ^
4.     --apply-immediately
```

```
1. DBINSTANCE  mydbinstance  2009-12-22T23:06:11.915Z  db.m3.large  mysql5.6  50  sa  
2. storage-full  mydbinstance.clla4j4jgyph.us-east-1.rds.amazonaws.com  3306  
3. us-east-1b  3  60
4. 	SECGROUP  default  active
5. 	PARAMGRP  default.mysql5.6  in-sync
```

Now, when you describe your DB instance, you will see that your DB instance will have *modifying* status, which indicates the storage is being scaled\.

```
1. aws rds describe-db-instances --db-instance-identifier mydbinstance 
```

```
DBINSTANCE  mydbinstance  2009-12-22T23:06:11.915Z  db.m3.large  mysql5.6  50  sa  
modifying  mydbinstance.clla4j4jgyph.us-east-1.rds.amazonaws.com  
3306  us-east-1b  3  60
	SECGROUP  default  active
	PARAMGRP  default.mysql5.6  in-sync
```

Once storage scaling is complete, your DB instance status will change to *available*\.

```
1. aws rds describe-db-instances --db-instance-identifier mydbinstance 
```

```
DBINSTANCE  mydbinstance  2009-12-22T23:06:11.915Z  db.m3.large  mysql5.6  60  sa  
available  mydbinstance.clla4j4jgyph.us-east-1.rds.amazonaws.com  3306  
us-east-1b  3
	SECGROUP  default  active
	PARAMGRP  default.mysql5.6  in-sync
```

Note that you can receive notifications when your storage space is exhausted using the `DescribeEvents` action\. For example, in this scenario, if you do a `DescribeEvents` call after these operations you will see the following output:

```
1. aws rds describe-events --source-type db-instance --source-identifier mydbinstance 
```

```
2009-12-22T23:44:14.374Z  mydbinstance  Allocated storage has been exhausted db-instance  
2009-12-23T00:14:02.737Z  mydbinstance  Applying modification to allocated storage db-instance  
2009-12-23T00:31:54.764Z  mydbinstance  Finished applying modification to allocated storage
```

## Amazon RDS Insufficient DB Instance Capacity<a name="CHAP_Troubleshooting.Capacity"></a>

If you get an `InsufficientDBInstanceCapacity` error when you try to modify a DB instance class, it might be because the DB instance is on the EC2\-Classic platform and is therefore not in a VPC\. Some DB instance classes require a VPC\. For example, if you are on the EC2\-Classic platform and try to increase capacity by switching to a DB instance class that requires a VPC, this error results\. For information about Amazon Elastic Compute Cloud instance types that are only available in a VPC, see [Instance Types Available Only in a VPC](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-vpc.html#vpc-only-instance-types) in the *Amazon Elastic Compute Cloud User Guide*\.

To correct the problem, you can move the DB instance into a VPC\. For more information, see [Moving a DB Instance Not in a VPC into a VPC](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.Non-VPC2VPC)\.

For information about modifying a DB instance, see [Modifying an Amazon RDS DB Instance and Using the Apply Immediately Parameter](Overview.DBInstance.Modifying.md)\. For information about troubleshooting instance capacity issues for Amazon EC2, see [Troubleshooting Instance Capacity](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-capacity.html) in the *Amazon Elastic Compute Cloud User Guide*\.

## Amazon RDS MySQL and MariaDB Issues<a name="CHAP_Troubleshooting.MySQL"></a>

### Index Merge Optimization Returns Wrong Results<a name="CHAP_Troubleshooting.MySQL.IndexMergeOptimization"></a>

This issue applies only to MySQL DB instances\.

Queries that use index merge optimization might return wrong results due to a bug in the MySQL query optimizer that was introduced in MySQL 5\.5\.37\. When you issue a query against a table with multiple indexes the optimizer scans ranges of rows based on the multiple indexes, but does not merge the results together correctly\. For more information on the query optimizer bug, go to [http://bugs\.mysql\.com/bug\.php?id=72745](http://bugs.mysql.com/bug.php?id=72745) and [http://bugs\.mysql\.com/bug\.php?id=68194](http://bugs.mysql.com/bug.php?id=68194) in the MySQL bug database\.

For example, consider a query on a table with two indexes where the search arguments reference the indexed columns\.

```
1. SELECT * FROM table1 
2.   WHERE indexed_col1 = 'value1' AND indexed_col2 = 'value2';
```

In this case, the search engine searches both indexes\. However, due to the bug, the merged results are incorrect\.

To resolve this issue, you can do one of the following: 
+ Set the `optimizer_switch` parameter to `index_merge=off` in the DB parameter group for your MySQL DB instance\. For information on setting DB parameter group parameters, see [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)\.
+ Upgrade your MySQL DB instance to MySQL version 5\.6 or 5\.7\. For more information, see [Upgrading a MySQL DB Snapshot](USER_UpgradeDBSnapshot.MySQL.md)\. 
+ If you cannot upgrade your instance or change the `optimizer_switch` parameter, you can work around the bug by explicitly identifying an index for the query, for example: 

  ```
  1. SELECT * FROM table1 
  2.   USE INDEX covering_index 
  3.   WHERE indexed_col1 = 'value1' AND indexed_col2 = 'value2';
  ```

For more information, go to [Index Merge Optimization](http://dev.mysql.com/doc/refman/5.6/en/index-merge-optimization.html)\.

### Diagnosing and Resolving Lag Between Read Replicas<a name="CHAP_Troubleshooting.MySQL.ReplicaLag"></a>

After you create a MySQL or MariaDB Read Replica and the Read Replica is available, Amazon RDS first replicates the changes made to the source DB instance from the time the create Read Replica operation was initiated\. During this phase, the replication lag time for the Read Replica will be greater than 0\. You can monitor this lag time in Amazon CloudWatch by viewing the Amazon RDS `ReplicaLag` metric\.

The `ReplicaLag` metric reports the value of the `Seconds_Behind_Master` field of the MySQL or MariaDB `SHOW SLAVE STATUS` command\. For more information, see [SHOW SLAVE STATUS](http://dev.mysql.com/doc/refman/5.6/en/show-slave-status.html)\. When the `ReplicaLag` metric reaches 0, the replica has caught up to the source DB instance\. If the `ReplicaLag` metric returns \-1, replication might not be active\. To troubleshoot a replication error, see [Diagnosing and Resolving a MySQL or MariaDB Read Replication Failure](#CHAP_Troubleshooting.MySQL.RR)\. A `ReplicaLag` value of \-1 can also mean that the `Seconds_Behind_Master` value cannot be determined or is `NULL`\.

The `ReplicaLag` metric returns \-1 during a network outage or when a patch is applied during the maintenance window\. In this case, wait for network connectivity to be restored or for the maintenance window to end before you check the `ReplicaLag` metric again\.

Because the MySQL and MariaDB read replication technology is asynchronous, you can expect occasional increases for the `BinLogDiskUsage` metric on the source DB instance and for the `ReplicaLag` metric on the Read Replica\. For example, a high volume of write operations to the source DB instance can occur in parallel, while write operations to the Read Replica are serialized using a single I/O thread, can lead to a lag between the source instance and Read Replica\. For more information about Read Replicas and MySQL, go to [Replication Implementation Details](http://dev.mysql.com/doc/refman/5.5/en/replication-implementation-details.html) in the MySQL documentation\. For more information about Read Replicas and MariaDB, go to [Replication Overview](http://mariadb.com/kb/en/mariadb/replication-overview/) in the MariaDB documentation\.

You can reduce the lag between updates to a source DB instance and the subsequent updates to the Read Replica by doing the following:
+ Set the DB instance class of the Read Replica to have a storage size comparable to that of the source DB instance\.
+ Ensure that parameter settings in the DB parameter groups used by the source DB instance and the Read Replica are compatible\. For more information and an example, see the discussion of the `max_allowed_packet` parameter in the next section\.
+ Disable the query cache\. For tables that are modified often, using the query cache can increase replica lag because the cache is locked and refreshed often\. If this is the case, you might see less replica lag if you disable the query cache\. You can disable the query cache by setting the `query_cache_type parameter` to 0 in the DB parameter group for the DB instance\. For more information on the query cache, see [Query Cache Configuration](http://dev.mysql.com/doc/refman/5.6/en/query-cache-configuration.html)\.
+ Warm the buffer pool on the Read Replica for InnoDB for MySQL, InnoDB for MariaDB 10\.2 or higher, or XtraDB for MariaDB 10\.1 or lower\. If you have a small set of tables that are being updated often, and you are using the InnoDB or XtraDB table schema, then dump those tables on the Read Replica\. Doing this causes the database engine to scan through the rows of those tables from the disk and then cache them in the buffer pool, which can reduce replica lag\. The following shows an example\.

  For Linux, OS X, or Unix:

  ```
  1. PROMPT> mysqldump \
  2.     –h <endpoint> \
  3.     --port=<port> \
  4.     -u=<username> \
  5.     -p <password> \
  6.     database_name table1 table2 > /dev/null
  ```

  For Windows:

  ```
  1. PROMPT> mysqldump ^
  2.     –h <endpoint> ^
  3.     --port=<port> ^
  4.     -u=<username> ^
  5.     -p <password> ^
  6.     database_name table1 table2 > /dev/null
  ```

### Diagnosing and Resolving a MySQL or MariaDB Read Replication Failure<a name="CHAP_Troubleshooting.MySQL.RR"></a>

Amazon RDS monitors the replication status of your Read Replicas and updates the **Replication State** field of the Read Replica instance to **Error** if replication stops for any reason\. You can review the details of the associated error thrown by the MySQL or MariaDB engines by viewing the **Replication Error** field\. Events that indicate the status of the Read Replica are also generated, including [RDS-EVENT-0045](USER_Events.md#RDS-EVENT-0045), [RDS-EVENT-0046](USER_Events.md#RDS-EVENT-0046), and [RDS-EVENT-0047](USER_Events.md#RDS-EVENT-0047)\. For more information about events and subscribing to events, see [Using Amazon RDS Event Notification](USER_Events.md)\. If a MySQL error message is returned, review the error in the [MySQL error message documentation](http://dev.mysql.com/doc/refman/5.5/en/error-messages-server.html)\. If a MariaDB error message is returned, review the error in the [MariaDB error message documentation](http://mariadb.com/kb/en/mariadb/mariadb-error-codes/)\.

Common situations that can cause replication errors include the following:
+ The value for the `max_allowed_packet` parameter for a Read Replica is less than the `max_allowed_packet` parameter for the source DB instance\. 

  The `max_allowed_packet` parameter is a custom parameter that you can set in a DB parameter group that is used to specify the maximum size of data manipulation language \(DML\) that can be executed on the database\. If the `max_allowed_packet` parameter value for the source DB instance is smaller than the `max_allowed_packet` parameter value for the Read Replica, the replication process can throw an error and stop replication\. The most common error is `packet bigger than 'max_allowed_packet' bytes`\. You can fix the error by having the source and Read Replica use DB parameter groups with the same `max_allowed_packet` parameter values\.
+ Writing to tables on a Read Replica\. If you are creating indexes on a Read Replica, you need to have the `read_only` parameter set to *0* to create the indexes\. If you are writing to tables on the Read Replica, it can break replication\.
+ Using a non\-transactional storage engine such as MyISAM\. Read replicas require a transactional storage engine\. Replication is only supported for the following storage engines: InnoDB for MySQL, InnoDB for MariaDB 10\.2 or higher, or XtraDB for MariaDB 10\.1 or lower\.

  You can convert a MyISAM table to InnoDB with the following command:

  `alter table <schema>.<table_name> engine=innodb;`
+ Using unsafe non\-deterministic queries such as `SYSDATE()`\. For more information, see [Determination of Safe and Unsafe Statements in Binary Logging](http://dev.mysql.com/doc/refman/5.5/en/replication-rbr-safe-unsafe.html)\. 

The following steps can help resolve your replication error: 
+ If you encounter a logical error and you can safely skip the error, follow the steps described in [Skipping the Current Replication Error](Appendix.MySQL.CommonDBATasks.md#Appendix.MySQL.CommonDBATasks.SkipError)\. Your MySQL or MariaDB DB instance must be running a version that includes the `mysql_rds_skip_repl_error` procedure\. For more information, see [mysql\.rds\_skip\_repl\_error](mysql_rds_skip_repl_error.md)\.
+ If you encounter a binlog position issue, you can change the slave replay position with the `mysql_rds_next_master_log` command\. Your MySQL or MariaDB DB instance must be running a version that supports the `mysql_rds_next_master_log` command in order to change the slave replay position\. For version information, see [mysql\.rds\_next\_master\_log](mysql_rds_next_master_log.md)\.
+ If you encounter a temporary performance issue due to high DML load, you can set the `innodb_flush_log_at_trx_commit` parameter to 2 in the DB parameter group on the Read Replica\. Doing this can help the Read Replica catch up, though it temporarily reduces atomicity, consistency, isolation, and durability \(ACID\)\.
+ You can delete the Read Replica and create an instance using the same DB instance identifier so that the endpoint remains the same as that of your old Read Replica\.

If a replication error is fixed, the **Replication State** changes to **replicating**\. For more information, see [Troubleshooting a MySQL or MariaDB Read Replica Problem](USER_ReadRepl.md#USER_ReadRepl.Troubleshooting)\.

### Creating Triggers with Binary Logging Enabled Requires SUPER Privilege<a name="CHAP_Troubleshooting.MySQL.CreatingTriggers"></a>

When trying to create triggers in an RDS MySQL or MariaDB DB instance, you might receive the following error:

```
1. "You do not have the SUPER privilege and binary logging is enabled" 
```

To use triggers when binary logging is enabled requires the SUPER privilege, which is restricted for RDS MySQL and MariaDB DB instances\. You can create triggers when binary logging is enabled without the SUPER privilege by setting the `log_bin_trust_function_creators` parameter to true\. To set the `log_bin_trust_function_creators` to true, create a new DB parameter group or modify an existing DB parameter group\.

To create a new DB parameter group that allows you to create triggers in your RDS MySQL or MariaDB DB instance with binary logging enabled, use the following CLI commands\. To modify an existing parameter group, start with step 2\.

**To create a new parameter group to allow triggers with binary logging enabled using the CLI**

1. Create a new parameter group\.

   For Linux, OS X, or Unix:

   ```
   1. aws rds create-db-parameter-group \
   2.     --db-parameter-group-name allow-triggers \
   3.     --db-parameter-group-family mysql15.5 \
   4.     --description "parameter group allowing triggers"
   ```

   For Windows:

   ```
   1. aws rds create-db-parameter-group ^
   2.     --db-parameter-group-name allow-triggers ^
   3.     --db-parameter-group-family mysql15.5 ^
   4.     --description "parameter group allowing triggers"
   ```

1. Modify the DB parameter group to allow triggers\.

   For Linux, OS X, or Unix:

   ```
   1. aws rds modify-db-parameter-group \ 
   2.     --db-parameter-group-name allow-triggers \
   3.     --parameters "name=log_bin_trust_function_creators,value=true, method=pending-reboot"
   ```

   For Windows:

   ```
   1. aws rds modify-db-parameter-group ^ 
   2.     --db-parameter-group-name allow-triggers ^
   3.     --parameters "name=log_bin_trust_function_creators,value=true, method=pending-reboot"
   ```

1. Modify your DB instance to use the new DB parameter group\.

   For Linux, OS X, or Unix:

   ```
   1. aws rds modify-db-instance \
   2.     --db-instance-identifier mydbinstance \
   3.     --db-parameter-group-name allow-triggers \
   4.     --apply-immediately
   ```

   For Windows:

   ```
   1. aws rds modify-db-instance ^
   2.     --db-instance-identifier mydbinstance ^
   3.     --db-parameter-group-name allow-triggers ^
   4.     --apply-immediately
   ```

1. In order for the changes to take effect, manually reboot the DB instance\.

   ```
   1. aws rds reboot-db-instance mydbinstance 
   ```

### Diagnosing and Resolving Point\-In\-Time Restore Failures<a name="CHAP_Troubleshooting.MySQL.PITR"></a>

**Restoring a DB Instance That Includes Temporary Tables**

When attempting a Point\-In\-Time Restore \(PITR\) of your MySQL or MariaDB DB instance, you might encounter the following error:

```
1. Database instance could not be restored because there has been incompatible database activity for restore 
2. functionality. Common examples of incompatible activity include using temporary tables, in-memory tables, 
3. or using MyISAM tables. In this case, use of Temporary table was detected.
```

PITR relies on both backup snapshots and binlogs from MySQL or MariaDB to restore your DB instance to a particular time\. Temporary table information can be unreliable in binlogs and can cause a PITR failure\. If you use temporary tables in your MySQL or MariaDB DB instance, you can minimize the possibility of a PITR failure by performing more frequent backups\. A PITR failure is most probable in the time between a temporary table's creation and the next backup snapshot\.

**Restoring a DB Instance That Includes In\-Memory Tables**

You might encounter a problem when restoring a database that has in\-memory tables\. In\-memory tables are purged during a restart\. As a result, your in\-memory tables might be empty after a reboot\. We recommend that when you use in\-memory tables, you architect your solution to handle empty tables in the event of a restart\. If you are using in\-memory tables with replicated DB instances, you might need to recreate the Read Replicas after a restart if a Read Replica reboots and is unable to restore data from an empty in\-memory table\.

For more information about backups and PITR, see [Working With Backups](USER_WorkingWithAutomatedBackups.md) and [Restoring a DB Instance to a Specified Time](USER_PIT.md)\.

### Slave Down or Disabled Error<a name="CHAP_Troubleshooting.MySQL.SlaveDown"></a>

When you call the `mysql.rds_skip_repl_error` command, you might receive the following error message: `Slave is down or disabled.`

This error message appears because replication has stopped and could not be restarted\.

If you need to skip a large number of errors, the replication lag can increase beyond the default retention period for binary log files\. In this case, you might encounter a fatal error due to binary log files being purged before they have been replayed on the replica\. This purge causes replication to stop, and you can no longer call the `mysql.rds_skip_repl_error` command to skip replication errors\. 

You can mitigate this issue by increasing the number of hours that binary log files are retained on your replication master\. After you have increased the binlog retention time, you can restart replication and call the `mysql.rds_skip_repl_error` command as needed\.

To set the binlog retention time, use the [mysql\.rds\_set\_configuration](mysql_rds_set_configuration.md) procedure and specify a configuration parameter of 'binlog retention hours' along with the number of hours to retain binlog files on the DB cluster, up to 720 \(30 days\)\. The following example sets the retention period for binlog files to 48 hours:

```
CALL mysql.rds_set_configuration('binlog retention hours', 48);
```

### Read Replica Create Fails or Replication Breaks With Fatal Error 1236<a name="CHAP_Troubleshooting.MySQL.ReadReplicas"></a>

After changing default parameter values for a MySQL or MariaDB DB instance, you might encounter one of the following problems:
+ You are unable to create a Read Replica for the DB instance\.
+ Replication fails with `fatal error 1236`\.

Some default parameter values for MySQL or MariaDB DB instances help to ensure the database is ACID compliant and Read Replicas are crash\-safe by making sure that each commit is fully synchronized by writing the transaction to the binary log before it is committed\. Changing these parameters from their default values to improve performance can cause replication to fail when a transaction has not been written to the binary log\.

To resolve this issue, set the following parameter values:
+ `sync-binlog = 1`
+ `innodb_support_xa = 1`
+ `innodb_flush_log_at_trx_commit = 1`

## Amazon Aurora Issues<a name="CHAP_Troubleshooting.Aurora"></a>

### No Space Left on Device Error<a name="CHAP_Troubleshooting.Aurora.NoSpaceLeft"></a>

You might encounter the following error message from Amazon Aurora:

```
ERROR 3 (HY000): Error writing file '/rdsdbdata/tmp/XXXXXXXX' (Errcode: 28 - No space left on device)
```

Each DB instance in an Amazon Aurora DB cluster uses local SSD storage to store temporary tables for a session\. This local storage for temporary tables does not autogrow like the Aurora cluster volume\. Instead, the amount of local storage is limited\. The limit is based on the DB instance class for DB instances in your DB cluster\. To find the amount of local SSD storage for memory optimized instance types, go to [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/#memory-optimized)\.

If your workload cannot be modified to reduce the amount temporary storage required, then you can scale your DB instances up to use a DB instance class that has more local SSD storage\. 

## Amazon RDS Oracle GoldenGate Issues<a name="CHAP_Troubleshooting.Oracle.GoldenGate"></a>

### Retaining Logs for Sufficient Time<a name="CHAP_Troubleshooting.Oracle.GoldenGate.Logs"></a>

The source database must retain archived redo logs\. The duration for log retention is specified in hours\. The duration should exceed any potential downtime of the source instance or any potential period of communication or networking issues for the source instance, so that Oracle GoldenGate can recover logs from the source instance as needed\. The absolute minimum value required is one \(1\) hour of logs retained\. If you don't have log retention enabled, or if the retention value is too small, you will receive the following message:

```
1. 2014-03-06 06:17:27  ERROR   OGG-00446  error 2 (No such file or directory) 
2. opening redo log /rdsdbdata/db/GGTEST3_A/onlinelog/o1_mf_2_9k4bp1n6_.log 
3. for sequence 1306Not able to establish initial position for begin time 2014-03-06 06:16:55.
```

## Cannot Connect to Amazon RDS SQL Server DB Instance<a name="CHAP_Troubleshooting.SQLServer.Connect"></a>

When you have problems connecting to a DB instance using SQL Server Management Studio, the following are some common causes: 
+ The access rules enforced by your local firewall and the IP addresses you authorized to access your DB instance in the instance's security group are not in sync\. If you use your DB instance’s endpoint and port with Microsoft SQL Server Management Studio and cannot connect, the problem is most likely the egress or ingress rules on your firewall\. To grant access, you must create your own security group with specific ingress and egress rules for your situation\. For more information about security groups, see [Amazon RDS Security Groups](Overview.RDSSecurityGroups.md)\.
+ The port you specified when you created the DB instance cannot be used to send or receive communications due to your local firewall restrictions\. In this case, check with your network administrator to determine if your network allows the specified port to be used for inbound and outbound communication\.
+ Your DB instance is still being created and is not yet available\. Depending on the size of your DB instance, it can take up to 20 minutes before an instance is available\.

If you can send and receive communications through the port you specified, check for the following SQL Server errors:
+ **Could not open a connection to SQL Server \- Microsoft SQL Server, Error: 53** – You must include the port number when you specify the server name when using Microsoft SQL Server Management Studio\. For example, the server name for a DB instance \(including the port number\) might be: **sqlsvr\-pdz\.c6c8mdfntzgv0\.region\.rds\.amazonaws\.com,1433**\.
+ **No connection could be made because the target machine actively refused it \- Microsoft SQL Server, Error: 10061** – In this case, you reached the DB instance but the connection was refused\. This error is often caused by an incorrect user name or password\.

## Cannot Connect to Amazon RDS PostgreSQL DB Instance<a name="CHAP_Troubleshooting.PostgreSQL.Connect"></a>

The most common problem when attempting to connect to a PostgreSQL DB instance is that the security group assigned to the DB instance has incorrect access rules\. By default, DB instances do not allow access; access is granted through a security group\. To grant access, you must create your own security group with specific ingress and egress rules for your situation\. For more information about creating a security group for your DB instance, see [Provide Access to Your DB Instance in Your VPC by Creating a Security Group](CHAP_SettingUp.md#CHAP_SettingUp.SecurityGroup)\. 

The most common error is `could not connect to server: Connection timed out`\. If you receive this error, check that the host name is the DB instance endpoint and that the port number is correct\. Check that the security group assigned to the DB instance has the necessary rules to allow access through your local firewall\.

## Cannot Set Backup Retention Period to 0<a name="CHAP_Troubleshooting.Backup.Retention"></a>

 There are several reasons why you may need to set the backup retention period to 0\. For example, you can disable automatic backups immediately by setting the retention period to 0\. If you set the value to 0 and receive a message saying that the retention period must be between 1 and 35, check to make sure you haven't setup a read replica for the instance\. Read replicas require backups for managing read replica logs, thus, you can't set the retention period of 0\. 