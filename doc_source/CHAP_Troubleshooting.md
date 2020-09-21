# Troubleshooting for Amazon RDS<a name="CHAP_Troubleshooting"></a>

Use the following sections to help troubleshoot problems you have with DB instances in Amazon RDS and Aurora\.

**Topics**
+ [Can't Connect to Amazon RDS DB Instance](#CHAP_Troubleshooting.Connecting)
+ [Amazon RDS Security Issues](#CHAP_Troubleshooting.Security)
+ [Resetting the DB Instance Owner Password](#CHAP_Troubleshooting.ResetPassword)
+ [Amazon RDS DB Instance Outage or Reboot](#CHAP_Troubleshooting.Reboots)
+ [Amazon RDS DB Parameter Changes Not Taking Effect](#CHAP_Troubleshooting.Parameters)
+ [Amazon RDS DB Instance Running Out of Storage](#CHAP_Troubleshooting.Storage)
+ [Amazon RDS Insufficient DB Instance Capacity](#CHAP_Troubleshooting.Capacity)
+ [Amazon RDS MySQL and MariaDB Issues](#CHAP_Troubleshooting.MySQL)
+ [Can't Set Backup Retention Period to 0](#CHAP_Troubleshooting.Backup.Retention)

 For information about debugging problems using the Amazon RDS API, see [Troubleshooting Applications on Amazon RDS](APITroubleshooting.md)\. 

## Can't Connect to Amazon RDS DB Instance<a name="CHAP_Troubleshooting.Connecting"></a>

When you can't connect to a DB instance, the following are common causes:
+ **Inbound rules** – The access rules enforced by your local firewall and the IP addresses authorized to access your DB instance might not match\. The problem is most likely the inbound rules in your security group\.

  By default, DB instances don't allow access\. Access is granted through a security group associated with the VPC that allows traffic into and out of the DB instance\. If necessary, add inbound and outbound rules for your particular situation to the security group\. You can specify an IP address, a range of IP addresses, or another VPC security group\.
**Note**  
When adding a new inbound rule, you can choose **My IP** for **Source** to allow access to the DB instance from the IP address detected in your browser\.

  For more information about setting up security groups, see [Provide Access to Your DB Instance in Your VPC by Creating a Security Group](CHAP_SettingUp.md#CHAP_SettingUp.SecurityGroup)\.
**Note**  
Client connections from IP addresses within the range 169\.254\.0\.0/16 aren't permitted\. This is the Automatic Private IP Addressing Range \(APIPA\), which is used for local\-link addressing\.
+ **Public accessibility** – To connect to your DB instance from outside of the VPC, such as by using a client application, the instance must have a public IP address assigned to it\.

  To make the instance publicly accessible, modify it and choose **Yes** under **Public accessibility**\. For more information, see [Hiding a DB Instance in a VPC from the Internet](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.Hiding)\.
+ **Port** – The port that you specified when you created the DB instance can't be used to send or receive communications due to your local firewall restrictions\. To determine if your network allows the specified port to be used for inbound and outbound communication, check with your network administrator\.
+ **Availability** – For a newly created DB instance, the DB instance has a status of `creating` until the DB instance is ready to use\. When the state changes to `available`, you can connect to the DB instance\. Depending on the size of your DB instance, it can take up to 20 minutes before an instance is available\.
+ **Internet gateway** – For a DB instance to be publicly accessible, the subnets in its DB subnet group must have an internet gateway\.

**To configure an internet gateway for a subnet**

  1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

  1. In the navigation pane, choose **Databases**, and then choose the name of the DB instance\.

  1. In the **Connectivity & security** tab, write down the values of the VPC ID under **VPC** and the subnet ID under **Subnets**\.

  1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

  1. In the navigation pane, choose **Internet Gateways**\. Verify that there is an internet gateway attached to your VPC\. Otherwise, choose **Create Internet Gateway** to create an internet gateway\. Select the internet gateway, and then choose **Attach to VPC** and follow the directions to attach it to your VPC\.

  1. In the navigation pane, choose **Subnets**, and then select your subnet\.

  1. On the **Route Table** tab, verify that there is a route with `0.0.0.0/0` as the destination and the internet gateway for your VPC as the target\. If you're connecting to your instance using its IPv6 address, verify that there is a route for all IPv6 traffic \(`::/0`\) that points to the internet gateway\. Otherwise, do the following:

     1. Choose the ID of the route table \(rtb\-*xxxxxxxx*\) to navigate to the route table\.

     1. On the **Routes** tab, choose **Edit routes**\. Choose **Add route**, use `0.0.0.0/0` as the destination and the internet gateway as the target\. For IPv6, choose **Add route**, use `::/0` as the destination and the internet gateway as the target\.

     1. Choose **Save routes**\.

  For more information, see [Working with a DB Instance in a VPC](USER_VPC.WorkingWithRDSInstanceinaVPC.md)\.

For engine\-specific connection issues, see the following topics:
+  [Troubleshooting Connections to Your SQL Server DB Instance](USER_ConnectToMicrosoftSQLServerInstance.md#USER_ConnectToMicrosoftSQLServerInstance.Troubleshooting)
+ [Troubleshooting Connections to Your Oracle DB Instance](USER_ConnectToOracleInstance.md#USER_ConnectToOracleInstance.Troubleshooting)
+ [Troubleshooting Connections to Your PostgreSQL Instance](USER_ConnectToPostgreSQLInstance.md#USER_ConnectToPostgreSQLInstance.Troubleshooting)
+ [Maximum MySQL and MariaDB Connections](#USER_ConnectToInstance.max_connections)

### Testing a Connection to a DB Instance<a name="CHAP_Troubleshooting.Connecting.Test"></a>

You can test your connection to a DB instance using common Linux or Microsoft Windows tools\. 

From a Linux or Unix terminal, you can test the connection by entering the following \(replace `DB-instance-endpoint` with the endpoint and `port` with the port of your DB instance\)\.

```
nc -zv DB-instance-endpoint port 
```

For example, the following shows a sample command and the return value\.

```
nc -zv postgresql1.c6c8mn7fake0.us-west-2.rds.amazonaws.com 8299

  Connection to postgresql1.c6c8mn7fake0.us-west-2.rds.amazonaws.com 8299 port [tcp/vvr-data] succeeded!
```

Windows users can use Telnet to test the connection to a DB instance\. Telnet actions aren't supported other than for testing the connection\. If a connection is successful, the action returns no message\. If a connection isn't successful, you receive an error message such as the following\.

```
C:\>telnet sg-postgresql1.c6c8mntfake0.us-west-2.rds.amazonaws.com 819

  Connecting To sg-postgresql1.c6c8mntfake0.us-west-2.rds.amazonaws.com...Could not open
  connection to the host, on port 819: Connect failed
```

If Telnet actions return success, your security group is properly configured\.

**Note**  
Amazon RDS doesn't accept internet control message protocol \(ICMP\) traffic, including ping\.

### Troubleshooting Connection Authentication<a name="CHAP_Troubleshooting.Connecting.Authorization"></a>

If you can connect to your DB instance but you get authentication errors, you might want to reset the master user password for the DB instance\. You can do this by modifying the RDS instance\. 

For more information about modifying a DB instance, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\.

## Amazon RDS Security Issues<a name="CHAP_Troubleshooting.Security"></a>

To avoid security issues, never use your master AWS user name and password for a user account\. Best practice is to use your master AWS account to create AWS Identity and Access Management \(IAM\) users and assign those to DB user accounts\. You can also use your master account to create other user accounts, if necessary\.

For more information on creating IAM users, see [Create an IAM User](CHAP_SettingUp.md#CHAP_SettingUp.IAM)\.

### Error Message "Failed to retrieve account attributes, certain console functions may be impaired\."<a name="CHAP_Troubleshooting.Security.AccountAttributes"></a>

You can get this error for several reasons\. It might be because your account is missing permissions, or your account hasn't been properly set up\. If your account is new, you might not have waited for the account to be ready\. If this is an existing account, you might lack permissions in your access policies to perform certain actions such as creating a DB instance\. To fix the issue, your IAM administrator needs to provide the necessary roles to your account\. For more information, see [the IAM documentation](https://docs.aws.amazon.com/IAM/latest/UserGuide/)\.

## Resetting the DB Instance Owner Password<a name="CHAP_Troubleshooting.ResetPassword"></a>

If you get locked out of your DB instance, you can log in as the master user\. Then you can reset the credentials for other administrative users or roles\. If you can't log in as the master user, the AWS account owner can reset the master user password\. For details of which administrative accounts or roles you might need to reset, see [Master User Account Privileges](UsingWithRDS.MasterAccounts.md)\.

You can change the DB instance password by using the Amazon RDS console, the AWS CLI command [modify\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html), or by using the [ModifyDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) API operation\. For more information about modifying a DB instance, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\.

## Amazon RDS DB Instance Outage or Reboot<a name="CHAP_Troubleshooting.Reboots"></a>

A DB instance outage can occur when a DB instance is rebooted\. It can also occur when the DB instance is put into a state that prevents access to it, and when the database is restarted\. A reboot can occur when you either manually reboot your DB instance or change a DB instance setting that requires a reboot before it can take effect\.

 A DB instance reboot occurs when you change a setting that requires a reboot, or when you manually cause a reboot\. A reboot can occur immediately if you change a setting and request that the change take effect immediately or it can occur during the DB instance's maintenance window\.

 A DB instance reboot occurs immediately when one of the following occurs:
+ You change the backup retention period for a DB instance from 0 to a nonzero value or from a nonzero value to 0 and set **Apply Immediately** to `true`\. 
+ You change the DB instance class, and **Apply Immediately** is set to `true`\. 
+ You change the storage type from **Magnetic \(Standard\)** to **General Purpose \(SSD**\) or **Provisioned IOPS \(SSD\)**, or from **Provisioned IOPS \(SSD\)** or **General Purpose \(SSD\)** to **Magnetic \(Standard\)**\.

A DB instance reboot occurs during the maintenance window when one of the following occurs:
+ You change the backup retention period for a DB instance from 0 to a nonzero value or from a nonzero value to 0, and **Apply Immediately** is set to `false`\. 
+ You change the DB instance class, and **Apply Immediately** is set to `false`\.

When you change a static parameter in a DB parameter group, the change doesn't take effect until the DB instance associated with the parameter group is rebooted\. The change requires a manual reboot\. The DB instance isn't automatically rebooted during the maintenance window\.

To see a table that shows DB instance actions and the effect that setting the **Apply Immediately** value has, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\.

## Amazon RDS DB Parameter Changes Not Taking Effect<a name="CHAP_Troubleshooting.Parameters"></a>

In some cases, you might change a parameter in a DB parameter group but don't see the changes take effect\. If so, you likely need to reboot the DB instance associated with the DB parameter group\. When you change a dynamic parameter, the change takes effect immediately\. When you change a static parameter, the change doesn't take effect until you reboot the DB instance associated with the parameter group\.

You can reboot a DB instance using the RDS console or explicitly calling the [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RebootDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RebootDBInstance.html) API operation \(without failover, if the DB instance is in a Multi\-AZ deployment\)\. The requirement to reboot the associated DB instance after a static parameter change helps mitigate the risk of a parameter misconfiguration affecting an API call\. An example of this might be calling `ModifyDBInstance` to change the DB instance class\. For more information, see [Modifying Parameters in a DB Parameter Group](USER_WorkingWithParamGroups.md#USER_WorkingWithParamGroups.Modifying)\.

## Amazon RDS DB Instance Running Out of Storage<a name="CHAP_Troubleshooting.Storage"></a>

If your DB instance runs out of storage space, it might no longer be available\. We highly recommend that you constantly monitor the `FreeStorageSpace` metric published in CloudWatch to make sure that your DB instance has enough free storage space\.

If your database instance runs out of storage, its status changes to `storage-full`\. For example, a call to the `DescribeDBInstances` API operation for a DB instance that has used up its storage outputs the following\.

```
aws rds describe-db-instances --db-instance-identifier mydbinstance

DBINSTANCE  mydbinstance  2009-12-22T23:06:11.915Z  db.m5.large  mysql8.0  50  sa
storage-full  mydbinstance.clla4j4jgyph.us-east-1.rds.amazonaws.com  3306
us-east-1b  3
	SECGROUP  default  active
	PARAMGRP  default.mysql8.0  in-sync
```

To recover from this scenario, add more storage space to your instance using the `ModifyDBInstance` API operation or the following AWS CLI command\.

For Linux, macOS, or Unix:

```
aws rds modify-db-instance \
    --db-instance-identifier mydbinstance \
    --allocated-storage 60 \
    --apply-immediately
```

For Windows:

```
aws rds modify-db-instance ^
    --db-instance-identifier mydbinstance ^
    --allocated-storage 60 ^
    --apply-immediately
```

```
DBINSTANCE  mydbinstance  2009-12-22T23:06:11.915Z  db.m5.large  mysql8.0  50  sa
storage-full  mydbinstance.clla4j4jgyph.us-east-1.rds.amazonaws.com  3306
us-east-1b  3  60
	SECGROUP  default  active
	PARAMGRP  default.mysql8.0  in-sync
```

Now, when you describe your DB instance, you see that your DB instance has `modifying` status, which indicates the storage is being scaled\.

```
1. aws rds describe-db-instances --db-instance-identifier mydbinstance 
```

```
DBINSTANCE  mydbinstance  2009-12-22T23:06:11.915Z  db.m5.large  mysql8.0  50  sa
modifying  mydbinstance.clla4j4jgyph.us-east-1.rds.amazonaws.com
3306  us-east-1b  3  60
	SECGROUP  default  active
	PARAMGRP  default.mysql8.0  in-sync
```

After storage scaling is complete, your DB instance status changes to `available`\.

```
aws rds describe-db-instances --db-instance-identifier mydbinstance 
```

```
DBINSTANCE  mydbinstance  2009-12-22T23:06:11.915Z  db.m5.large  mysql8.0  60  sa
available  mydbinstance.clla4j4jgyph.us-east-1.rds.amazonaws.com  3306
us-east-1b  3
	SECGROUP  default  active
	PARAMGRP  default.mysql8.0  in-sync
```

You can receive notifications when your storage space is exhausted using the `DescribeEvents` operation\. For example, in this scenario, if you make a `DescribeEvents` call after these operations you see the following output\.

```
aws rds describe-events --source-type db-instance --source-identifier mydbinstance 
```

```
2009-12-22T23:44:14.374Z  mydbinstance  Allocated storage has been exhausted db-instance
2009-12-23T00:14:02.737Z  mydbinstance  Applying modification to allocated storage db-instance
2009-12-23T00:31:54.764Z  mydbinstance  Finished applying modification to allocated storage
```

## Amazon RDS Insufficient DB Instance Capacity<a name="CHAP_Troubleshooting.Capacity"></a>

The `InsufficientDBInstanceCapacity` error can be returned when you try to create or modify a DB instance, or when you try to restore a DB instance from a DB snapshot\. When this error is returned, the following are common causes:
+ The specific DB instance class isn't available in the requested Availability Zone\. You can try one of the following to solve the problem:
  + Retry the request with a different DB instance class\.
  + Retry the request with a different Availability Zone\.
  + Retry the request without specifying an explicit Availability Zone\.

  For information about troubleshooting instance capacity issues for Amazon EC2, see [Insufficient Instance Capacity](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/troubleshooting-launch.html#troubleshooting-launch-capacity) in the *Amazon Elastic Compute Cloud User Guide*\.
+ The DB instance is on the EC2\-Classic platform and therefore isn't in a VPC\. Some DB instance classes require a VPC\. For example, if you're on the EC2\-Classic platform and try to increase capacity by switching to a DB instance class that requires a VPC, this error results\. For information about Amazon EC2 instance types that are only available in a VPC, see [Instance Types Available in EC2\-Classic](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-classic-platform.html#ec2-classic-instance-types) in the *Amazon Elastic Compute Cloud User Guide*\. To correct the problem, you can move the DB instance into a VPC\. For more information, see [Moving a DB Instance Not in a VPC into a VPC](USER_VPC.md#USER_VPC.Non-VPC2VPC)\.

For information about modifying a DB instance, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\.

## Amazon RDS MySQL and MariaDB Issues<a name="CHAP_Troubleshooting.MySQL"></a>

You can diagnose and correct issues with MySQL and MariaDB DB instances\.

**Topics**
+ [Maximum MySQL and MariaDB Connections](#USER_ConnectToInstance.max_connections)
+ [Diagnosing and Resolving Lag Between Read Replicas](#CHAP_Troubleshooting.MySQL.ReplicaLag)
+ [Diagnosing and Resolving a MySQL or MariaDB Read Replication Failure](#CHAP_Troubleshooting.MySQL.RR)
+ [Creating Triggers with Binary Logging Enabled Requires SUPER Privilege](#CHAP_Troubleshooting.MySQL.CreatingTriggers)
+ [Diagnosing and Resolving Point\-In\-Time Restore Failures](#CHAP_Troubleshooting.MySQL.PITR)
+ [Replication Stopped Error](#CHAP_Troubleshooting.MySQL.ReplicationStopped)
+ [Read Replica Create Fails or Replication Breaks With Fatal Error 1236](#CHAP_Troubleshooting.MySQL.ReadReplicas)

### Maximum MySQL and MariaDB Connections<a name="USER_ConnectToInstance.max_connections"></a>

The maximum number of connections allowed to an RDS MySQL or MariaDB DB instance is based on the amount of memory available for its DB instance class\. A DB instance class with more memory available results in a larger number of connections available\. For more information on DB instance classes, see [DB Instance Classes](Concepts.DBInstanceClass.md)\.

The connection limit for a DB instance is set by default to the maximum for the DB instance class\. You can limit the number of concurrent connections to any value up to the maximum number of connections allowed\. Use the `max_connections` parameter in the parameter group for the DB instance\. For more information, see [Maximum Number of Database Connections](CHAP_Limits.md#RDS_Limits.MaxConnections) and [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)\.

You can retrieve the maximum number of connections allowed for a MySQL or MariaDB DB instance by running the following query\.

```
SELECT @@max_connections;
```

You can retrieve the number of active connections to a MySQL or MariaDB DB instance by running the following query\.

```
SHOW STATUS WHERE `variable_name` = 'Threads_connected';
```

### Diagnosing and Resolving Lag Between Read Replicas<a name="CHAP_Troubleshooting.MySQL.ReplicaLag"></a>

After you create a MySQL or MariaDB read replica and the replica is available, Amazon RDS first replicates the changes made to the source DB instance from the time the read replica create operation started\. During this phase, the replication lag time for the read replica is greater than 0\. You can monitor this lag time in Amazon CloudWatch by viewing the Amazon RDS `ReplicaLag` metric\.

The `ReplicaLag` metric reports the value of the `Seconds_Behind_Master` field of the MySQL or MariaDB `SHOW SLAVE STATUS` command\. For more information, see [SHOW SLAVE STATUS](https://dev.mysql.com/doc/refman/8.0/en/show-slave-status.html)\. When the `ReplicaLag` metric reaches 0, the replica has caught up to the source DB instance\. If the `ReplicaLag` metric returns \-1, replication might not be active\. To troubleshoot a replication error, see [Diagnosing and Resolving a MySQL or MariaDB Read Replication Failure](#CHAP_Troubleshooting.MySQL.RR)\. A `ReplicaLag` value of \-1 can also mean that the `Seconds_Behind_Master` value can't be determined or is `NULL`\.

The `ReplicaLag` metric returns \-1 during a network outage or when a patch is applied during the maintenance window\. In this case, wait for network connectivity to be restored or for the maintenance window to end before you check the `ReplicaLag` metric again\.

The MySQL and MariaDB read replication technology is asynchronous\. Thus, you can expect occasional increases for the `BinLogDiskUsage` metric on the source DB instance and for the `ReplicaLag` metric on the read replica\. For example, consider a situation where a high volume of write operations to the source DB instance occur in parallel\. At the same time, write operations to the read replica are serialized using a single I/O thread\. Such a situation can lead to a lag between the source instance and read replica\. 

For more information about read replicas and MySQL, see [Replication Implementation Details](https://dev.mysql.com/doc/refman/8.0/en/replication-implementation-details.html) in the MySQL documentation\. For more information about read replicas and MariaDB, see [Replication Overview](http://mariadb.com/kb/en/mariadb/replication-overview/) in the MariaDB documentation\.

You can reduce the lag between updates to a source DB instance and the subsequent updates to the read replica by doing the following:
+ Set the DB instance class of the read replica to have a storage size comparable to that of the source DB instance\.
+ Make sure that parameter settings in the DB parameter groups used by the source DB instance and the read replica are compatible\. For more information and an example, see the discussion of the `max_allowed_packet` parameter in the next section\.
+ Disable the query cache\. For tables that are modified often, using the query cache can increase replica lag because the cache is locked and refreshed often\. If this is the case, you might see less replica lag if you disable the query cache\. You can disable the query cache by setting the `query_cache_type parameter` to 0 in the DB parameter group for the DB instance\. For more information on the query cache, see [Query Cache Configuration](https://dev.mysql.com/doc/refman/5.7/en/query-cache-configuration.html)\.
+ Warm the buffer pool on the read replica for InnoDB for MySQL, InnoDB for MariaDB 10\.2 or higher, or XtraDB for MariaDB 10\.1 or lower\. For example, suppose that you have a small set of tables that are being updated often and you're using the InnoDB or XtraDB table schema\. In this case, dump those tables on the read replica\. Doing this causes the database engine to scan through the rows of those tables from the disk and then cache them in the buffer pool\. This approach can reduce replica lag\. The following shows an example\.

  For Linux, macOS, or Unix:

  ```
  PROMPT> mysqldump \
      -h <endpoint> \
      --port=<port> \
      -u=<username> \
      -p <password> \
      database_name table1 table2 > /dev/null
  ```

  For Windows:

  ```
  PROMPT> mysqldump ^
      -h <endpoint> ^
      --port=<port> ^
      -u=<username> ^
      -p <password> ^
      database_name table1 table2 > /dev/null
  ```

### Diagnosing and Resolving a MySQL or MariaDB Read Replication Failure<a name="CHAP_Troubleshooting.MySQL.RR"></a>

Amazon RDS monitors the replication status of your read replicas and updates the **Replication State** field of the read replica instance to `Error` if replication stops for any reason\. You can review the details of the associated error thrown by the MySQL or MariaDB engines by viewing the **Replication Error** field\. Events that indicate the status of the read replica are also generated, including [RDS-EVENT-0045](USER_Events.md#RDS-EVENT-0045), [RDS-EVENT-0046](USER_Events.md#RDS-EVENT-0046), and [RDS-EVENT-0047](USER_Events.md#RDS-EVENT-0047)\. For more information about events and subscribing to events, see [Using Amazon RDS Event Notification](USER_Events.md)\. If a MySQL error message is returned, check the error in the [MySQL error message documentation](https://dev.mysql.com/doc/refman/8.0/en/server-error-reference.html)\. If a MariaDB error message is returned, check the error in the [MariaDB error message documentation](http://mariadb.com/kb/en/mariadb/mariadb-error-codes/)\.

Common situations that can cause replication errors include the following:
+ The value for the `max_allowed_packet` parameter for a read replica is less than the `max_allowed_packet` parameter for the source DB instance\. 

  The `max_allowed_packet` parameter is a custom parameter that you can set in a DB parameter group\. The `max_allowed_packet` parameter is used to specify the maximum size of data manipulation language \(DML\) that can be run on the database\. If the `max_allowed_packet` value for the source DB instance is smaller than the `max_allowed_packet` value for the read replica, the replication process can throw an error and stop replication\. The most common error is `packet bigger than 'max_allowed_packet' bytes`\. You can fix the error by having the source and read replica use DB parameter groups with the same `max_allowed_packet` parameter values\.
+ Writing to tables on a read replica\. If you're creating indexes on a read replica, you need to have the `read_only` parameter set to *0* to create the indexes\. If you're writing to tables on the read replica, it can break replication\.
+ Using a nontransactional storage engine such as MyISAM\. Read replicas require a transactional storage engine\. Replication is only supported for the following storage engines: InnoDB for MySQL, InnoDB for MariaDB 10\.2 or higher, or XtraDB for MariaDB 10\.1 or lower\.

  You can convert a MyISAM table to InnoDB with the following command:

  `alter table <schema>.<table_name> engine=innodb;`
+ Using unsafe nondeterministic queries such as `SYSDATE()`\. For more information, see [Determination of Safe and Unsafe Statements in Binary Logging](https://dev.mysql.com/doc/refman/8.0/en/replication-rbr-safe-unsafe.html) in the MySQL documentation\. 

The following steps can help resolve your replication error: 
+ If you encounter a logical error and you can safely skip the error, follow the steps described in [Skipping the Current Replication Error](Appendix.MySQL.CommonDBATasks.md#Appendix.MySQL.CommonDBATasks.SkipError)\. Your MySQL or MariaDB DB instance must be running a version that includes the `mysql_rds_skip_repl_error` procedure\. For more information, see [mysql\.rds\_skip\_repl\_error](mysql_rds_skip_repl_error.md)\.
+ If you encounter a binary log \(binlog\) position issue, you can change the replica replay position with the `mysql_rds_next_master_log` command\. Your MySQL or MariaDB DB instance must be running a version that supports the `mysql_rds_next_master_log` command to change the replica replay position\. For version information, see [mysql\.rds\_next\_master\_log](mysql_rds_next_master_log.md)\.
+ If you encounter a temporary performance issue due to high DML load, you can set the `innodb_flush_log_at_trx_commit` parameter to 2 in the DB parameter group on the read replica\. Doing this can help the read replica catch up, though it temporarily reduces atomicity, consistency, isolation, and durability \(ACID\)\.
+ You can delete the read replica and create an instance using the same DB instance identifier\. If you do this, the endpoint remains the same as that of your old read replica\.

If a replication error is fixed, the **Replication State** changes to **replicating**\. For more information, see [Troubleshooting a MySQL Read Replica Problem](USER_MySQL.Replication.ReadReplicas.md#USER_ReadRepl.Troubleshooting)\.

### Creating Triggers with Binary Logging Enabled Requires SUPER Privilege<a name="CHAP_Troubleshooting.MySQL.CreatingTriggers"></a>

When trying to create triggers in an RDS MySQL or MariaDB DB instance, you might receive the following error\.

```
"You do not have the SUPER privilege and binary logging is enabled" 
```

To use triggers when binary logging is enabled requires the SUPER privilege, which is restricted for RDS MySQL and MariaDB DB instances\. You can create triggers when binary logging is enabled without the SUPER privilege by setting the `log_bin_trust_function_creators` parameter to true\. To set the `log_bin_trust_function_creators` to true, create a new DB parameter group or modify an existing DB parameter group\.

To create a new DB parameter group that allows you to create triggers in your RDS MySQL or MariaDB DB instance with binary logging enabled, use the following CLI commands\. To modify an existing parameter group, start with step 2\.

**To create a new parameter group to allow triggers with binary logging enabled using the CLI**

1. Create a new parameter group\.

   For Linux, macOS, or Unix:

   ```
   aws rds create-db-parameter-group \
       --db-parameter-group-name allow-triggers \
       --db-parameter-group-family mysql8.0 \
       --description "parameter group allowing triggers"
   ```

   For Windows:

   ```
   aws rds create-db-parameter-group ^
       --db-parameter-group-name allow-triggers ^
       --db-parameter-group-family mysql8.0 ^
       --description "parameter group allowing triggers"
   ```

1. Modify the DB parameter group to allow triggers\.

   For Linux, macOS, or Unix:

   ```
   aws rds modify-db-parameter-group \
       --db-parameter-group-name allow-triggers \
       --parameters "ParameterName=log_bin_trust_function_creators, ParameterValue=true, ApplyMethod=pending-reboot"
   ```

   For Windows:

   ```
   aws rds modify-db-parameter-group ^
       --db-parameter-group-name allow-triggers ^
       --parameters "ParameterName=log_bin_trust_function_creators, ParameterValue=true, ApplyMethod=pending-reboot"
   ```

1. Modify your DB instance to use the new DB parameter group\.

   For Linux, macOS, or Unix:

   ```
   aws rds modify-db-instance \
       --db-instance-identifier mydbinstance \
       --db-parameter-group-name allow-triggers \
       --apply-immediately
   ```

   For Windows:

   ```
   aws rds modify-db-instance ^
       --db-instance-identifier mydbinstance ^
       --db-parameter-group-name allow-triggers ^
       --apply-immediately
   ```

1. For the changes to take effect, manually reboot the DB instance\.

   ```
   aws rds reboot-db-instance --db-instance-identifier mydbinstance
   ```

### Diagnosing and Resolving Point\-In\-Time Restore Failures<a name="CHAP_Troubleshooting.MySQL.PITR"></a>

**Restoring a DB Instance That Includes Temporary Tables**

When attempting a point\-in\-time restore \(PITR\) of your MySQL or MariaDB DB instance, you might encounter the following error\.

```
Database instance could not be restored because there has been incompatible database activity for restore
functionality. Common examples of incompatible activity include using temporary tables, in-memory tables,
or using MyISAM tables. In this case, use of Temporary table was detected.
```

PITR relies on both backup snapshots and binary logs \(binlogs\) from MySQL or MariaDB to restore your DB instance to a particular time\. Temporary table information can be unreliable in binlogs and can cause a PITR failure\. If you use temporary tables in your MySQL or MariaDB DB instance, you can minimize the possibility of a PITR failure by performing more frequent backups\. A PITR failure is most probable in the time between a temporary table's creation and the next backup snapshot\.

**Restoring a DB Instance That Includes In\-Memory Tables**

You might encounter a problem when restoring a database that has in\-memory tables\. In\-memory tables are purged during a restart\. As a result, your in\-memory tables might be empty after a reboot\. We recommend that when you use in\-memory tables, you architect your solution to handle empty tables in the event of a restart\. If you're using in\-memory tables with replicated DB instances, you might need to recreate the read replicas after a restart\. This might be necessary if a read replica reboots and can't restore data from an empty in\-memory table\.

For more information about backups and PITR, see [Working With Backups](USER_WorkingWithAutomatedBackups.md) and [Restoring a DB Instance to a Specified Time](USER_PIT.md)\.

### Replication Stopped Error<a name="CHAP_Troubleshooting.MySQL.ReplicationStopped"></a>

When you call the `mysql.rds_skip_repl_error` command, you might receive the following error message: `Slave is down or disabled.`

This error message appears because replication is stopped and can't be restarted\.

If you need to skip a large number of errors, the replication lag can increase beyond the default retention period for binary log files\. In this case, you might encounter a fatal error due to binary log files being purged before they have been replayed on the replica\. This purge causes replication to stop, and you can no longer call the `mysql.rds_skip_repl_error` command to skip replication errors\. 

You can mitigate this issue by increasing the number of hours that binary log files are retained on your replication source\. After you have increased the binlog retention time, you can restart replication and call the `mysql.rds_skip_repl_error` command as needed\.

To set the binlog retention time, use the [mysql\.rds\_set\_configuration](mysql_rds_set_configuration.md) procedure\. Specify a configuration parameter of 'binlog retention hours' along with the number of hours to retain binlog files on the DB cluster, up to 720 \(30 days\)\. The following example sets the retention period for binlog files to 48 hours\.

```
CALL mysql.rds_set_configuration('binlog retention hours', 48);
```

### Read Replica Create Fails or Replication Breaks With Fatal Error 1236<a name="CHAP_Troubleshooting.MySQL.ReadReplicas"></a>

After changing default parameter values for a MySQL or MariaDB DB instance, you might encounter one of the following problems:
+ You can't create a read replica for the DB instance\.
+ Replication fails with `fatal error 1236`\.

Some default parameter values for MySQL and MariaDB DB instances help to make sure that the database is ACID compliant and read replicas are crash\-safe\. They do this by making sure that each commit is fully synchronized by writing the transaction to the binary log before it's committed\. Changing these parameters from their default values to improve performance can cause replication to fail when a transaction hasn't been written to the binary log\.

To resolve this issue, set the following parameter values:
+ `sync-binlog = 1`
+ `innodb_support_xa = 1`
+ `innodb_flush_log_at_trx_commit = 1`

## Can't Set Backup Retention Period to 0<a name="CHAP_Troubleshooting.Backup.Retention"></a>

There are several reasons why you might need to set the backup retention period to 0\. For example, you can disable automatic backups immediately by setting the retention period to 0\. 

In some cases, you might set the value to 0 and receive a message saying that the retention period must be between 1 and 35\. In these cases, check to make sure that you haven't set up a read replica for the instance\. Read replicas require backups for managing read replica logs, and therefore you can't set a retention period of 0\.