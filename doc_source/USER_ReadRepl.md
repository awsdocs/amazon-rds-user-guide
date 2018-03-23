# Working with Read Replicas of MariaDB, MySQL, and PostgreSQL DB Instances<a name="USER_ReadRepl"></a>

Amazon RDS uses the MariaDB, MySQL, and PostgreSQL \(version 9\.3\.5 and later\) DB engines' built\-in replication functionality to create a special type of DB instance called a Read Replica from a source DB instance\. Updates made to the source DB instance are asynchronously copied to the Read Replica\. You can reduce the load on your source DB instance by routing read queries from your applications to the Read Replica\. Using Read Replicas, you can elastically scale out beyond the capacity constraints of a single DB instance for read\-heavy database workloads\. 

**Note**  
The information following applies to creating Amazon RDS Read Replicas either in the same AWS Region as the source DB instance, or in a separate AWS Region\. The information following doesn't apply to setting up replication with an instance that is running on an Amazon EC2 instance or that is on\-premises\. 

When you create a Read Replica, you first specify an existing DB instance as the source\. Then Amazon RDS takes a snapshot of the source instance and creates a read\-only instance from the snapshot\. Amazon RDS then uses the asynchronous replication method for the DB engine to update the Read Replica whenever there is a change to the source DB instance\. The Read Replica operates as a DB instance that allows only read\-only connections\. Applications connect to a Read Replica the same way they do to any DB instance\. Amazon RDS replicates all databases in the source DB instance\. 

Amazon RDS sets up a secure communications channel between the source DB instance and a Read Replica if that Read Replica is in a different AWS Region from the DB instance\. Amazon RDS establishes any AWS security configurations needed to enable the secure channel, such as adding security group entries\. PostgreSQL DB instances use a secure connection that you can encrypt by setting the `ssl` parameter to `1` for both the source and the replica instances\. 

## Overview of Amazon RDS Read Replicas<a name="USER_ReadRepl.Overview"></a>

Deploying one or more Read Replicas for a given source DB instance might make sense in a variety of scenarios, including the following: 
+ Scaling beyond the compute or I/O capacity of a single DB instance for read\-heavy database workloads\. You can direct this excess read traffic to one or more Read Replicas\. 
+ Serving read traffic while the source DB instance is unavailable\. If your source DB instance can't take I/O requests \(for example, due to I/O suspension for backups or scheduled maintenance\), you can direct read traffic to your Read Replicas\. For this use case, keep in mind that the data on the Read Replica might be "stale" because the source DB instance is unavailable\. 
+ Business reporting or data warehousing scenarios where you might want business reporting queries to run against a Read Replica, rather than your primary, production DB instance\. 

By default, a Read Replica is created with the same storage type as the source DB instance\. However, you can create a Read Replica that has a different storage type from the source DB instance based on the options listed in the following table\. 

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ReadRepl.html)

Amazon RDS doesn't support circular replication\. You can't configure a DB instance to serve as a replication source for an existing DB instance; you can only create a new Read Replica from an existing DB instance\. For example, if MyDBInstance replicates to ReadReplica1, you can't configure ReadReplica1 to replicate back to MyDBInstance\. From ReadReplica1, you can only create a new Read Replica, such as ReadReplica2\. 

For MySQL, MariaDB, and PostgreSQL Read Replicas, you can monitor replication lag in Amazon CloudWatch by viewing the Amazon RDS `ReplicaLag` metric\. For MySQL and MariaDB, the `ReplicaLag` metric reports the value of the `Seconds_Behind_Master` field of the `SHOW SLAVE STATUS` command\. For PostgreSQL, the `ReplicaLag` metric reports the value of `SELECT extract(epoch from now() - pg_last_xact_replay_timestamp()) AS slave_lag`\. 

Common causes for replication lag for MySQL and MariaDB are the following: 
+ A network outage\.
+ Writing to tables with indexes on a Read Replica\. If the `read_only` parameter is not set to 0 on the Read Replica, it can break replication\.
+ Using a nontransactional storage engine such as MyISAM\. Replication is only supported for the InnoDB storage engine on MySQL and the XtraDB storage engine on MariaDB\.

When the `ReplicaLag` metric reaches 0, the replica has caught up to the source DB instance\. If the `ReplicaLag` metric returns \-1, then replication is currently not active\. `ReplicaLag` = \-1 is equivalent to `Seconds_Behind_Master` = `NULL`\. 

### Differences Between PostgreSQL and MySQL or MariaDB Read Replicas<a name="USER_ReadRepl.Overview.Differences"></a>

Because the PostgreSQL DB engine implements replication differently than the MySQL and MariaDB DB engines, there are several significant differences you should know about, as shown in the following table\. 


| Feature or Behavior | PostgreSQL | MySQL and MariaDB | 
| --- | --- | --- | 
|  What is the replication method?   |  Physical replication\.  |  Logical replication\.  | 
|  How are transaction logs purged?  |  PostgreSQL has a parameter, `wal_keep_segments`, that dictates how many write ahead log \(WAL\) files are kept to provide data to the Read Replicas\. The parameter value specifies the number of logs to keep\.   |  Amazon RDS keeps any binary logs that haven't been applied\.  | 
|  Can a replica be made writable?  |  No\. A PostgreSQL Read Replica is a physical copy, and PostgreSQL doesn't allow for a Read Replica to be made writable\.   |  Yes\. You can enable the MySQL or MariaDB Read Replica to be writable\.   | 
|  Can backups be performed on the replica?  |  Yes, you can create a manual snapshot of a PostgreSQL Read Replica, but you can't enable automatic backups\.   |  Yes\. You can enable automatic backups on a MySQL or MariaDB Read Replica\.   | 
|  Can you use parallel replication?  |  No\. PostgreSQL has a single process handling replication\.  |  Yes\. MySQL version 5\.6 and later and all supported MariaDB versions allow for parallel replication threads\.   | 

## PostgreSQL Read Replicas \(Version 9\.3\.5 and Later\)<a name="USER_ReadRepl.PostgreSQL"></a>

Amazon RDS PostgreSQL 9\.3\.5 and later uses PostgreSQL native streaming replication to create a read\-only copy of a source \(a "master" in PostgreSQL terms\) DB instance\. This Read Replica \(a "standby" in PostgreSQL terms\) DB instance is an asynchronously created physical replication of the master DB instance\. It's created by a special connection that transmits write ahead log \(WAL\) data between the source DB instance and the Read Replica where PostgreSQL asynchronously streams database changes as they are made\. 

PostgreSQL uses a "replication" role to perform streaming replication\. The role is privileged, but can't be used to modify any data\. PostgreSQL uses a single process for handling replication\. 

Creating a PostgreSQL Read Replica doesn't require an outage for the master DB instance\. Amazon RDS sets the necessary parameters and permissions for the source DB instance and the Read Replica without any service interruption\. A snapshot is taken of the source DB instance, and this snapshot becomes the Read Replica\. No outage occurs when you delete a Read Replica\. 

You can create up to five Read Replicas from one source DB instance\. For replication to operate effectively, each Read Replica should have the same amount of compute and storage resources as the source DB instance\. If you scale the source DB instance, you should also scale the Read Replicas\. 

Amazon RDS overrides any incompatible parameters on a Read Replica if it prevents the Read Replica from starting\. For example, suppose that the `max_connections` parameter value is higher on the source DB instance than on the Read Replica\. In that case, Amazon RDS updates the parameter on the Read Replica to be the same value as that on the source DB instance\. 

Here are some important facts about PostgreSQL Read Replicas: 
+ PostgreSQL Read Replicas are read\-only and can't be made writable\.
+ You can't create a Read Replica from another Read Replica \(that is, you can't create cascading Read Replicas\)\. 
+ You can promote a PostgreSQL Read Replica to be a new source DB instance\. However, the Read Replica doesn't become the new source DB instance automatically\. The Read Replica, when promoted, stops receiving WAL communications and is no longer a read\-only instance\. You must set up any replication you intend to have going forward because the promoted Read Replica is now a new source DB instance\. 
+ A PostgreSQL Read Replica reports a replication lag of up to five minutes if there are no user transactions occurring on the source DB instance\. 
+ Before a DB instance can serve as a source DB instance, you must enable automatic backups on the source DB instance by setting the backup retention period to a value other than 0\. 

In several situations, a PostgreSQL source DB instance can unintentionally break replication with a Read Replica\. These situations include the following: 
+ The `max_wal_senders` parameter is set too low to provide enough data to the number of Read Replicas\. This situation causes replication to stop\. 
+ The PostgreSQL parameter `wal_keep_segments` dictates how many WAL files are kept to provide data to the Read Replicas\. The parameter value specifies the number of logs to keep\. If you set the parameter value too low, you can cause a Read Replica to fall so far behind that streaming replication stops\. In this case, Amazon RDS reports a replication error and begins recovery on the Read Replica by replaying the source DB instance's archived WAL logs\. This recovery process continues until the Read Replica has caught up enough to continue streaming replication\. For more information, see [Troubleshooting a PostgreSQL Read Replica Problem](#USER_ReadRepl.TroubleshootingPostgreSQL)\. 
+ A PostgreSQL Read Replica requires a reboot if the source DB instance endpoint changes\. 

When the WAL stream that provides data to a Read Replica is broken, PostgreSQL switches into recovery mode to restore the Read Replica by using archived WAL files\. When this process is complete, PostgreSQL attempts to re\-establish streaming replication\. 

## MySQL and MariaDB Read Replicas<a name="USER_ReadRepl.MySQL"></a>

Before a MySQL or MariaDB DB instance can serve as a replication source, you must enable automatic backups on the source DB instance by setting the backup retention period to a value other than 0\. This requirement also applies to a Read Replica that is the source DB instance for another Read Replica\. Automatic backups are supported only for Read Replicas running any version of MariaDB or MySQL 5\.6 and later\. 

You can configure replication based on binary log coordinates for both MySQL and MariaDB instance\. For MariaDB instances, you can also configure replication based on global transaction IDs \(GTIDs\), which provides better crash safety\. For more information, see [Configuring GTID\-Based Replication into an Amazon RDS MariaDB DB instance](MariaDB.Procedural.Importing.md#MariaDB.Procedural.Replication.GTID)\. 

You can create up to five Read Replicas from one DB instance\. For replication to operate effectively, each Read Replica should have as the same amount of compute and storage resources as the source DB instance\. If you scale the source DB instance, you should also scale the Read Replicas\. 

If a Read Replica is running any version of MariaDB or MySQL 5\.6 and later, you can specify it as the source DB instance for another Read Replica\. For example, you can create ReadReplica1 from MyDBInstance, and then create ReadReplica2 from ReadReplica1\. Updates made to MyDBInstance are replicated to ReadReplica1 and then replicated from ReadReplica1 to ReadReplica2\. You can't have more than four instances involved in a replication chain\. For example, you can create ReadReplica1 from MySourceDBInstance, and then create ReadReplica2 from ReadReplica1, and then create ReadReplica3 from ReadReplica2, but you can't create a ReadReplica4 from ReadReplica3\. 

To enable automatic backups on a Read Replica for Amazon RDS MariaDB or MySQL version 5\.6 and later, first create the Read Replica, then modify the Read Replica to enable automatic backups\. 

Read Replicas are designed to support read queries, but you might need occasional updates\. For example, you might need to add an index to speed the specific types of queries accessing the replica\. You can enable updates by setting the `read_only` parameter to **0** in the DB parameter group for the Read Replica\. 

You can run multiple concurrent Read Replica create or delete actions that reference the same source DB instance, as long as you stay within the limit of five Read Replicas for the source instance\. 

You can create a Read Replica from either single\-AZ or Multi\-AZ DB instance deployments\. You use Multi\-AZ deployments to improve the durability and availability of critical data, but you can't use the Multi\-AZ secondary to serve read\-only queries\. Instead, you can create Read Replicas from high\-traffic Multi\-AZ DB instances to offload read\-only queries\. If the source instance of a Multi\-AZ deployment fails over to the secondary, any associated Read Replicas automatically switch to use the secondary \(now primary\) as their replication source\. For more information, see [High Availability \(Multi\-AZ\)](Concepts.MultiAZ.md)\. 

You can create a Read Replica as a Multi\-AZ DB instance\. Amazon RDS creates a standby of your replica in another Availability Zone for failover support for the replica\. Creating your Read Replica as a Multi\-AZ DB instance is independent of whether the source database is a Multi\-AZ DB instance\. 

**Note**  
Currently PostgreSQL Read Replicas can only be created as single\-AZ DB instances\.

For MySQL and MariaDB DB instances, in some cases Read Replicas can't be switched to the secondary if some binlog events aren't flushed during the failure\. In these cases, you must manually delete and recreate the Read Replicas\. You can reduce the chance of this happening in MySQL 5\.5 by setting the `sync_binlog=1` and `innodb_support_xa=1` dynamic variables\. These settings might reduce performance, so test their impact before implementing the changes to a production environment\. These problems are less likely to occur if you use MySQL 5\.6 and later or MariaDB\. For instances running MySQL 5\.6 and later or MariaDB, the parameters are set by default to `sync_binlog=1` and `innodb_support_xa=1`\. 

You usually configure replication between Amazon RDS DB instances, but you can configure replication to import databases from instances of MySQL or MariaDB running outside of Amazon RDS, or to export databases to such instances\. For more information, see [Importing Data to an Amazon RDS MySQL or MariaDB DB Instance with Reduced Downtime](MySQL.Procedural.Importing.NonRDSRepl.md) and [Exporting Data from a MySQL DB Instance by Using Replication](MySQL.Procedural.Exporting.NonRDSRepl.md)\. 

You can stop and restart the replication process on an Amazon RDS DB instance by calling the system stored procedures [mysql\.rds\_stop\_replication](mysql_rds_stop_replication.md) and [mysql\.rds\_start\_replication](mysql_rds_start_replication.md)\. You can do this when replicating between two Amazon RDS instances for long\-running operations such as creating large indexes\. You also need to stop and start replication when importing or exporting databases\. For more information, see [Importing Data to an Amazon RDS MySQL or MariaDB DB Instance with Reduced Downtime](MySQL.Procedural.Importing.NonRDSRepl.md) and [Exporting Data from a MySQL DB Instance by Using Replication](MySQL.Procedural.Exporting.NonRDSRepl.md)\. 

You must explicitly delete Read Replicas, using the same mechanisms for deleting a DB instance\. If you delete the source DB instance without deleting the replicas, each replica is promoted to a standalone DB instance\. 

If you promote a MySQL or MariaDB Read Replica that is in turn replicating to other Read Replicas, those Read Replicas remain active\. Consider an example where MyDBInstance1 replicates to MyDBInstance2, and MyDBInstance2 replicates to MyDBInstance3\. If you promote MyDBInstance2, replication from MyDBInstance1 to MyDBInstance2 no longer occurs, but MyDBInstance2 still replicates to MyDBInstance3\. 

If replication is stopped for more than 30 consecutive days, either manually or due to a replication error, Amazon RDS terminates replication between the master DB instance and all Read Replicas\. It does so to prevent increased storage requirements on the master DB instance and long failover times\. The Read Replica DB instance is still available\. However, replication can't be resumed because the binary logs required by the Read Replica are deleted from the master DB instance after replication is terminated\. You can create a new Read Replica for the master DB instance to reestablish replication\. 

## Creating a Read Replica<a name="USER_ReadRepl.Create"></a>

 You can create a Read Replica from an existing MySQL, MariaDB, or PostgreSQL DB instance using the AWS Management Console, AWS CLI, or AWS API\. You create a Read Replica by specifying the `SourceDBInstanceIdentifier`, which is the DB instance identifier of the source DB instance from which you wish to replicate\. 

When you create a Read Replica, Amazon RDS takes a DB snapshot of your source DB instance and begins replication\. As a result, you experience a brief I/O suspension on your source DB instance while the DB snapshot occurs\. The I/O suspension typically lasts about one minute\. You can avoid the I/O suspension if the source DB instance is a Multi\-AZ deployment, because in that case the snapshot is taken from the secondary DB instance\. An active, long\-running transaction can slow the process of creating the Read Replica\. We recommend that you wait for long\-running transactions to complete before creating a Read Replica\. If you create multiple Read Replicas in parallel from the same source DB instance, Amazon RDS takes only one snapshot at the start of the first create action\. 

When creating a Read Replica, there are a few things to consider\. First, you must enable automatic backups on the source DB instance by setting the backup retention period to a value other than 0\. This requirement also applies to a Read Replica that is the source DB instance for another Read Replica\. For MySQL DB instances, automatic backups are supported only for Read Replicas running MySQL 5\.6 and later, but not for MySQL versions 5\.5\. To enable automatic backups on an Amazon RDS MySQL version 5\.6 and later Read Replica, first create the Read Replica, then modify the Read Replica to enable automatic backups\. 

### Preparing MySQL DB Instances That Use MyISAM<a name="w3ab1c15c49c19b9"></a>

If your MySQL DB instance uses a nontransactional engine such as MyISAM, you need to perform the following steps to successfully set up your Read Replica\. These steps are required to ensure that the Read Replica has a consistent copy of your data\. These steps are not required if all of your tables use a transactional engine such as InnoDB\. 

1. Stop all data manipulation language \(DML\) and data definition language \(DDL\) operations on non\-transactional tables in the source DB instance and wait for them to complete\. SELECT statements can continue running\. 

1. Flush and lock the tables in the source DB instance\.

1. Create the Read Replica using one of the methods in the following sections\.

1. Check the progress of the Read Replica creation using, for example, the `DescribeDBInstances` API operation\. Once the Read Replica is available, unlock the tables of the source DB instance and resume normal database operations\. 

### AWS Management Console<a name="USER_ReadRepl.Create.Console"></a>

**To create a Read Replica from a source MySQL, MariaDB, or PostgreSQL DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Instances**\. 

1. In the **Instances** pane, select the MySQL, MariaDB, or PostgreSQL DB instance that you want to use as the source for a Read Replica\. 

1. For **Instance actions**, choose **Create read replica**\. 

1. Choose the instance specifications you want to use\. We recommend that you use the same DB instance class and storage type as the source DB instance for the Read Replica\. For **Multi\-AZ deployment**, choose **Yes** to create a standby of your replica in another Availability Zone for failover support for the replica\. Creating your Read Replica as a Multi\-AZ DB instance is independent of whether the source database is a Multi\-AZ DB instance\. 

1. Choose the settings you want to use\. For **DB instance identifier**, type a name for the Read Replica\. Adjust other settings as needed\. 

1. Choose the other settings you want to use\. 

1. Choose **Create read replica**\.

### CLI<a name="USER_ReadRepl.Create.CLI"></a>

To create a Read Replica from a source MySQL, MariaDB, or PostgreSQL DB instance, use the AWS CLI command [http://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance-read-replica.html](http://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance-read-replica.html)\. 

**Example**  
For Linux, OS X, or Unix:  

```
aws rds create-db-instance-read-replica \
    --db-instance-identifier myreadreplica \
    --source-db-instance-identifier mydbinstance
```
For Windows:  

```
aws rds create-db-instance-read-replica ^
    --db-instance-identifier myreadreplica ^
    --source-db-instance-identifier mydbinstance
```

### API<a name="USER_ReadRepl.Create.API"></a>

To create a Read Replica from a source MySQL, MariaDB, or PostgreSQL DB instance, call the Amazon RDS API function [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstanceReadReplica.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstanceReadReplica.html)\. 

```
 1. https://rds.amazonaws.com/
 2. 	?Action=CreateDBInstanceReadReplica
 3. 	&DBInstanceIdentifier=myreadreplica
 4. 	&SourceDBInstanceIdentifier=mydbinstance
 5. 	&Version=2012-01-15						
 6. 	&SignatureVersion=2
 7. 	&SignatureMethod=HmacSHA256
 8. 	&Timestamp=2012-01-20T22%3A06%3A23.624Z
 9. 	&AWSAccessKeyId=<AWS Access Key ID>
10. 	&Signature=<Signature>
```

## Promoting a Read Replica to Be a DB Instance<a name="USER_ReadRepl.Promote"></a>

You can promote a MySQL, MariaDB, or PostgreSQL Read Replica into a standalone DB instance\. When you promote a Read Replica, the DB instance is rebooted before it becomes available\. 

There are several reasons you might want to promote a Read Replica to a standalone DB instance: 
+ **Performing DDL operations \(MySQL and MariaDB only\)** – DDL operations, such as creating or rebuilding indexes, can take time and impose a significant performance penalty on your DB instance\. You can perform these operations on a MySQL or MariaDB Read Replica once the Read Replica is in sync with its source DB instance\. Then you can promote the Read Replica and direct your applications to use the promoted instance\. 
+ **Sharding** – Sharding embodies the "share\-nothing" architecture and essentially involves breaking a large database into several smaller databases\. One common way to split a database is splitting tables that are not joined in the same query onto different hosts\. Another method is duplicating a table across multiple hosts and then using a hashing algorithm to determine which host receives a given update\. You can create Read Replicas corresponding to each of your shards \(smaller databases\) and promote them when you decide to convert them into standalone shards\. You can then carve out the key space \(if you are splitting rows\) or distribution of tables for each of the shards depending on your requirements\. 
+ **Implementing failure recovery** – You can use Read Replica promotion as a data recovery scheme if the source DB instance fails\. However, if you require synchronous replication, automatic failure detection, and failover, we recommend that you run your DB instance as a Multi\-AZ deployment instead\. For more information, see [High Availability \(Multi\-AZ\)](Concepts.MultiAZ.md)\. 

  If you are aware of the ramifications and limitations of asynchronous replication and you still want to use Read Replica promotion for data recovery, you can do so\. To do this, first create a Read Replica and then monitor the source DB instance for failures\. In the event of a failure, do the following: 

  1. Promote the Read Replica\. 

  1. Direct database traffic to the promoted DB instance\. 

  1. Create a replacement Read Replica with the promoted DB instance as its source\. 

When you promote a Read Replica, the new DB instance that is created retains the backup retention period, the backup window, and the parameter group of the former Read Replica source\. The promotion process can take several minutes or longer to complete, depending on the size of the Read Replica\. Once you promote the Read Replica to a new DB instance, it's just like any other DB instance\. For example, you can convert the new DB instance into a Multi\-AZ DB instance, create Read Replicas from it, and perform point\-in\-time restore operations\. Because the promoted DB instance is no longer a Read Replica, you can't use it as a replication target\. If a source DB instance has several Read Replicas, promoting one of the Read Replicas to a DB instance has no effect on the other replicas\. 

 Backup duration is a function of the amount of changes to the database since the previous backup\. If you plan to promote a Read Replica to a standalone instance, we recommend that you enable backups and complete at least one backup prior to promotion\. In addition, a Read Replica cannot be promoted to a standalone instance when it is in the `backing-up` status\. If you have enabled backups on your Read Replica, configure the automated backup window so that daily backups do not interfere with Read Replica promotion\. 

The following steps show the general process for promoting a Read Replica to a DB instance: 

1. Stop any transactions from being written to the Read Replica source DB instance, and then wait for all updates to be made to the Read Replica\. Database updates occur on the Read Replica after they have occurred on the source DB instance, and this replication lag can vary significantly\. Use the [Replica Lag](http://aws.amazon.com/rds/faqs/#105) metric to determine when all updates have been made to the Read Replica\.

1. For MySQL and MariaDB only: If you need to make changes to the MySQL or MariaDB Read Replica, you must the set the `read_only` parameter to `0` in the DB parameter group for the Read Replica\. You can then perform all needed DDL operations, such as creating indexes, on the Read Replica\. Actions taken on the Read Replica don't affect the performance of the source DB instance\.

1. Promote the Read Replica by using the **Promote Read Replica** option on the Amazon RDS console, the AWS CLI command [http://docs.aws.amazon.com/cli/latest/reference/rds/promote-read-replica.html](http://docs.aws.amazon.com/cli/latest/reference/rds/promote-read-replica.html), or the [ `PromoteReadReplica`](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference//API_PromoteReadReplica.html) Amazon RDS API operation\.
**Note**  
The promotion process takes a few minutes to complete\. When you promote a Read Replica, replication is stopped and the Read Replica is rebooted\. When the reboot is complete, the Read Replica is available as a new DB instance\.

1. \(Optional\) Modify the new DB instance to be a Multi\-AZ deployment\. For more information, see [Modifying an Amazon RDS DB Instance and Using the Apply Immediately Parameter](Overview.DBInstance.Modifying.md) and [High Availability \(Multi\-AZ\)](Concepts.MultiAZ.md)\. 

### AWS Management Console<a name="USER_ReadRepl.Promote.Console"></a>

**To promote a Read Replica to a DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the Amazon RDS console, choose **Instances**\. 

   The **Instance** pane appears\. Each Read Replica shows **replica** in the **Replication role** column\. 

1. In the **Instances** pane, select the Read Replica that you want to promote\. 

1. Choose **Instance actions**, and then choose **Promote read replica**\. 

1. On the **Promote Read Replica** page, enter the backup retention period and the backup window for the new promoted DB instance\. 

1. When the settings are as you want them, choose **Continue**\. 

1. On the acknowledgment page, choose **Promote Read Replica**\. 

### CLI<a name="USER_ReadRepl.Promote.CLI"></a>

To promote a Read Replica to a DB instance, use the AWS CLI [http://docs.aws.amazon.com/cli/latest/reference/rds/promote-read-replica.html](http://docs.aws.amazon.com/cli/latest/reference/rds/promote-read-replica.html) command\. 

**Example**  
For Linux, OS X, or Unix:  

```
aws rds promote-read-replica \
    --db-instance-identifier myreadreplica
```
For Windows:  

```
aws rds promote-read-replica ^
    --db-instance-identifier myreadreplica
```

### API<a name="USER_ReadRepl.Promote.API"></a>

To promote a Read Replica to a DB instance, call [ `PromoteReadReplica`](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_PromoteReadReplica.html)\. 

```
1. https://rds.amazonaws.com/
2. 	?Action=PromoteReadReplica
3. 	&DBInstanceIdentifier=myreadreplica
4. 	&Version=2012-01-15						
5. 	&SignatureVersion=2
6. 	&SignatureMethod=HmacSHA256
7. 	&Timestamp=2012-01-20T22%3A06%3A23.624Z
8. 	&AWSAccessKeyId=<AWS Access Key ID>
9. 	&Signature=<Signature>
```

## Creating a Read Replica in a Different AWS Region<a name="USER_ReadRepl.XRgn"></a>

With Amazon Relational Database Service \(Amazon RDS\), you can create a MySQL, PostgreSQL, or MariaDB Read Replica in a different AWS Region than the source DB instance\. You create a Read Replica to do the following:
+ Improve your disaster recovery capabilities\.
+ Scale read operations into an AWS Region closer to your users\.
+ Make it easier to migrate from a data center in one AWS Region to a data center in another AWS Region\.

Creating a MySQL, PostgreSQL, or MariaDB Read Replica in a different AWS Region than the source instance is similar to creating a replica in the same AWS Region\. To create a Read Replica across regions, you can use the AWS Management Console, run the [http://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance-read-replica.html](http://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance-read-replica.html) command, or call the [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstanceReadReplica.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstanceReadReplica.html) API action\.

 To create an encrypted Read Replica in a different AWS Region than the source DB instance, the source DB instance must be encrypted\. 

**Note**  
You can also create a replica of an Amazon Aurora MySQL DB cluster in a different AWS Region\. For more information, see [Replicating Amazon Aurora MySQL DB Clusters Across AWS Regions](AuroraMySQL.Replication.CrossRegion.md)\.

Following, you can find information on how to create a Read Replica from a source MySQL, MariaDB, or PostgreSQL DB instance in a different AWS Region\.

### AWS Management Console<a name="USER_ReadRepl.XRgn.CON"></a>

You can create a Read Replica across regions using the AWS Management Console\.

**To create a Read Replica across regions with the console**

1.  Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\. 

1. In the navigation pane, choose **Instances**\.

1. In the **Instances** pane, choose the MySQL, MariaDB, or PostgreSQL DB instance that you want to use as the source for a Read Replica, and then choose **Create read replica** from **Instance actions**\. To create an encrypted Read Replica, the source DB instance must be encrypted\. To learn more about encrypting the source DB instance, see [Encrypting Amazon RDS Resources](Overview.Encryption.md)\.

1. Choose the instance specifications you want to use\. We recommend that you use the same DB instance class and storage type for the Read Replica\. 

1. Choose the other settings you want to use: 
   + For **DB instance identifier**, type a name for the Read Replica\.
   + In the **Network & Security** section, choose a value for **Designation region** and **Designation DB subnet group**\.
   +  To create an encrypted Read Replica in another AWS Region, choose **Enable Encryption**, and then choose the **Master key**\. For the **Master key**, choose the KMS key identifier of the destination AWS Region\. 
   + Choose the other settings you want to use\. 

1. Choose **Create read replica**\.

### CLI<a name="USER_ReadRepl.XRgn.CLI"></a>

 To create a Read Replica from a source MySQL, MariaDB, or PostgreSQL DB instance in a different AWS Region, you can use the [http://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance-read-replica.html](http://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance-read-replica.html) command\. In this case, you use [http://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance-read-replica.html](http://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance-read-replica.html) from the AWS Region where you want the Read Replica and specify the Amazon Resource Name \(ARN\) for the source DB instance\. An ARN uniquely identifies a resource created in Amazon Web Services\. 

For example, if your source DB instance is in the US East \(N\. Virginia\) region, the ARN looks similar to the following\.

`arn:aws:rds:us-east-1:123456789012:db:my-mysql-instance`

For information about ARNs, see [Working with Amazon Resource Names \(ARNs\) in Amazon RDS](USER_Tagging.ARN.md)\.

 To create an encrypted Read Replica in a different AWS Region than the source DB instance, you can use the AWS CLI [http://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance-read-replica.html](http://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance-read-replica.html) command from the destination AWS Region\. The following parameters are used to create an encrypted Read Replica in another AWS Region: 
+  `--source-region` — The AWS Region that the encrypted Read Replica is created in\. If `source-region` is not specified, you must specify a `pre-signed-url`\. A `pre-signed-url` is a URL that contains a Signature Version 4 signed request for the `CreateDBInstanceReadReplica` action that is called in the source AWS Region where the Read Replica is created from\. To learn more about the `pre-signed-url`, see [ `CreateDBInstanceReadReplica`](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstanceReadReplica.html)\. 
+  `--source-db-instance-identifier` — The DB instance identifier for the encrypted Read Replica that is created\. This identifier must be in the ARN format for the source AWS Region\. The AWS Region specified in `source-db-instance-identifier` must match the AWS Region specified as the `source-region`\. 
+  `--db-instance-identifier` — The identifier for the encrypted Read Replica in the destination AWS Region\. 
+  `--kms-key-id` — The AWS KMS key identifier for the key to use to encrypt the Read Replica in the destination AWS Region\. 

The following code creates a Read Replica in the `us-west-2` region\.

**Example**  
For Linux, OS X, or Unix:  

```
1. aws rds create-db-instance-read-replica \
2.     --db-instance-identifier DBInstanceIdentifier \
3.     --region us-west-2 \
4.     --source-db-instance-identifier arn:aws:rds:us-east-1:123456789012:db:my-mysql-instance
```
For Windows:  

```
1. aws rds create-db-instance-read-replica ^
2.     --db-instance-identifier DBInstanceIdentifier ^
3.     --region us-west-2 ^
4.     --source-db-instance-identifier arn:aws:rds:us-east-1:123456789012:db:my-mysql-instance
```

The following code creates a Read Replica in a different AWS Region than the source DB instance\. The AWS Region where you call the `create-db-instance-read-replica` command is the destination AWS Region for the encrypted Read Replica\.

**Example**  
For Linux, OS X, or Unix:  

```
1. aws rds create-db-instance-read-replica \
2.     --db-instance-identifier DBInstanceIdentifier \
3.     --region us-west-2 \
4.     --source-db-instance-identifier arn:aws:rds:us-east-1:123456789012:db:my-mysql-instance \
5.     --source-region us-east-1 \
6.     --kms-key-id my-us-east-1-key
```
For Windows:  

```
1. aws rds create-db-instance-read-replica ^
2.     --db-instance-identifier DBInstanceIdentifier ^
3.     --region us-west-2 ^
4.     --source-db-instance-identifier arn:aws:rds:us-east-1:123456789012:db:my-mysql-instance ^
5.     --source-region us-east-1 ^	
6.     --kms-key-id my-us-east-1-key
```

### API<a name="USER_ReadRepl.XRgn.API"></a>

 To create a Read Replica from a source MySQL, MariaDB, or PostgreSQL DB instance in a different AWS Region, you can call the Amazon RDS API function [CreateDBInstanceReadReplica](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstanceReadReplica.html)\. In this case, you call [CreateDBInstanceReadReplica](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstanceReadReplica.html) from the AWS Region where you want the Read Replica and specify the Amazon Resource Name \(ARN\) for the source DB instance\. An ARN uniquely identifies a resource created in Amazon Web Services\. 

 To create an encrypted Read Replica in a different AWS Region than the source DB instance, you can use the Amazon RDS API [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstanceReadReplica.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstanceReadReplica.html) action from the destination AWS Region\. To create an encrypted Read Replica in another AWS Region, you must specify a value for `PreSignedURL`\. `PreSignedURL` should contain a request for the [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstanceReadReplica.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstanceReadReplica.html) action to call in the source AWS Region where the Read Replica is created in\. To learn more about `PreSignedUrl`, see [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstanceReadReplica.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstanceReadReplica.html)\. 

For example, if your source DB instance is in the US East \(N\. Virginia\) region, the ARN looks similar to the following\.

`arn:aws:rds:us-east-1:123456789012:db:my-mysql-instance`

For information about ARNs, see [Working with Amazon Resource Names \(ARNs\) in Amazon RDS](USER_Tagging.ARN.md)\.

**Example**  

```
 1. https://us-west-2.rds.amazonaws.com/
 2.     ?Action=CreateDBInstanceReadReplica
 3.     &KmsKeyId=my-us-east-1-key
 4.     &PreSignedUrl=https%253A%252F%252Frds.us-west-2.amazonaws.com%252F
 5.          %253FAction%253D CreateDBInstanceReadReplica
 6.          %2526DestinationRegion%253Dus-east-1
 7.          %2526KmsKeyId%253Dmy-us-east-1-key
 8.          %2526SourceDBInstanceIdentifier%253Darn%25253Aaws%25253Ards%25253Aus-west-2%1234567890
 9. 12%25253Adb%25253Amy-mysql-instance
10.          %2526SignatureMethod%253DHmacSHA256
11.          %2526SignatureVersion%253D4%2526SourceDBInstanceIdentifier%253Darn%25253Aaws%25253Ards%25253Aus-west-2%25253A123456789012%25253Ainstance%25253Amysql-instance1-instance-20161115
12.          %2526Version%253D2014-10-31
13.          %2526X-Amz-Algorithm%253DAWS4-HMAC-SHA256
14.          %2526X-Amz-Credential%253DAKIADQKE4SARGYLE%252F20161117%252Fus-west-2%252Frds%252Faws4_request
15.          %2526X-Amz-Date%253D20161117T215409Z
16.          %2526X-Amz-Expires%253D3600
17.          %2526X-Amz-SignedHeaders%253Dcontent-type%253Bhost%253Buser-agent%253Bx-amz-content-sha256%253Bx-amz-date
18.          %2526X-Amz-Signature%253D255a0f17b4e717d3b67fad163c3ec26573b882c03a65523522cf890a67fca613
19.     &DBInstanceIdentifier=myreadreplica
20.     &SourceDBInstanceIdentifier=arn:aws:rds:us-east-1:123456789012:db:my-mysql-instance
21.     &Version=2012-01-15						
22.     &SignatureVersion=2
23.     &SignatureMethod=HmacSHA256
24.     &Timestamp=2012-01-20T22%3A06%3A23.624Z
25.     &AWSAccessKeyId=<AWS Access Key ID>
26.     &Signature=<Signature>
```

### Cross\-Region Replication Considerations<a name="USER_ReadRepl.XRgn.Cnsdr"></a>

All of the considerations for performing replication within an AWS Region apply to cross\-region replication\. The following extra considerations apply when replicating between regions: 
+ You can only replicate between regions when using Amazon RDS DB instances of MariaDB, PostgreSQL \(versions 9\.4\.7 and 9\.5\.2 and later\), or MySQL 5\.6 and later\.
+ A source DB instance can have cross\-region Read Replicas in multiple regions\. 
+ You can only create a cross\-region Amazon RDS Read Replica from a source Amazon RDS DB instance that is not a Read Replica of another Amazon RDS DB instance\.
+ You can't set up a replication channel into or out of the AWS GovCloud \(US\) region\.
+ You can expect to see a higher level of lag time for any Read Replica that is in a different AWS Region than the source instance, due to the longer network channels between regional data centers\.
+ Within an AWS Region, all cross\-region Read Replicas created from the same source DB instance must either be in the same Amazon VPC or be outside of a VPC\. For cross\-region Read Replicas, any of the create Read Replica commands that specify the `--db-subnet-group-name` parameter must specify a DB subnet group from the same VPC\. 
+ You can create a cross\-region Read Replica in a VPC from a source DB instance that is not in a VPC\. You can also create a cross\-region Read Replica that is not in a VPC from a source DB instance that is in a VPC\.
+ Due to the limit on the number of access control list \(ACL\) entries for a VPC, we can't guarantee more than five cross\-region Read Replica instances\. 

### Cross\-Region Replication Costs<a name="USER_ReadRepl.XRgn.Costs"></a>

The data transferred for cross\-region replication incurs Amazon RDS data transfer charges\. These cross\-region replication actions generate charges for the data transferred out of the source AWS Region:
+ When you create a Read Replica, Amazon RDS takes a snapshot of the source instance and transfers the snapshot to the Read Replica region\.
+ For each data modification made in the source databases, Amazon RDS transfers data from the source AWS Region to the Read Replica region\.

For more information about data transfer pricing, see [Amazon RDS Pricing](https://aws.amazon.com/rds/pricing/)\. 

For MySQL and MariaDB instances, you can reduce your data transfer costs by reducing the number of cross\-region Read Replicas that you create\. For example, suppose that you have a source DB instance in one AWS Region and want to have three Read Replicas in another AWS Region\. In this case, you create only one of the Read Replicas from the source DB instance\. You create the other two replicas from the first Read Replica instead of the source DB instance\. 

For example, if you have `source-instance-1` in one AWS Region, you can do the following:
+ Create `read-replica-1` in the new AWS Region, specifying `source-instance-1` as the source\.
+ Create `read-replica-2` from `read-replica-1`\.
+ Create `read-replica-3` from `read-replica-1`\.

In this example, you are only charged for the data transferred from `source-instance-1` to `read-replica-1`\. You are not charged for the data transferred from `read-replica-1` to the other two replicas because they are all in the same AWS Region\. If you create all three replicas directly from `source-instance-1`, you are charged for the data transfers to all three replicas\.

### How Amazon RDS Does Cross\-Region Replication<a name="USER_ReadRepl.XRgn.Process"></a>

Amazon RDS uses the following process to create a cross\-region Read Replica\. Depending on the regions involved and the amount of data in the databases, this process can take hours to complete\. You can use this information to determine how far the process has proceeded when you create a cross\-region Read Replica:

1. Amazon RDS begins configuring the source DB instance as a replication source and sets the status to *modifying*\.

1. Amazon RDS begins setting up the specified Read Replica in the destination AWS Region and sets the status to *creating*\.

1. Amazon RDS creates an automated DB snapshot of the source DB instance in the source AWS Region\. The format of the DB snapshot name is `rds:<InstanceID>-<timestamp>`, where `<InstanceID>` is the identifier of the source instance, and `<timestamp>` is the date and time the copy started\. For example, `rds:mysourceinstance-2013-11-14-09-24` was created from the instance `mysourceinstance` at `2013-11-14-09-24`\. During the creation of an automated DB snapshot, the source DB instance status remains *modifying*, the Read Replica status remains *creating*, and the DB snapshot status is *creating*\. The progress column of the DB snapshot page in the console reports how far the DB snapshot creation has progressed\. When the DB snapshot is complete, the status of both the DB snapshot and source DB instance are set to *available*\.

1. Amazon RDS begins a cross\-region snapshot copy for the initial data transfer\. The snapshot copy is listed as an automated snapshot in the destination AWS Region with a status of *creating*\. It has the same name as the source DB snapshot\. The progress column of the DB snapshot display indicates how far the copy has progressed\. When the copy is complete, the status of the DB snapshot copy is set to *available*\.

1. Amazon RDS then uses the copied DB snapshot for the initial data load on the Read Replica\. During this phase, the Read Replica is in the list of DB instances in the destination, with a status of *creating*\. When the load is complete, the Read Replica status is set to *available*, and the DB snapshot copy is deleted\.

1. When the Read Replica reaches the available status, Amazon RDS starts by replicating the changes made to the source instance since the start of the create Read Replica operation\. During this phase, the replication lag time for the Read Replica is greater than 0\. 

   For MySQL, MariaDB, and PostgreSQL Read Replicas, you can monitor replication lag in Amazon CloudWatch by viewing the Amazon RDS `ReplicaLag` metric\. For MySQL and MariaDB, the `ReplicaLag` metric reports the value of the `Seconds_Behind_Master` field of the `SHOW SLAVE STATUS` command\. For PostgreSQL, the `ReplicaLag` metric reports the value of `SELECT extract(epoch from now() - pg_last_xact_replay_timestamp()) AS slave_lag`\.

   Common causes for replication lag for MySQL and MariaDB are the following: 
   + A network outage\.
   + Writing to tables with indexes on a Read Replica\. If the `read_only` parameter is not set to 0 on the Read Replica, it can break replication\.
   + Using a non\-transactional storage engine such as MyISAM\. Replication is only supported for the InnoDB storage engine on MySQL and the XtraDB storage engine on MariaDB\.

   When the `ReplicaLag` metric reaches 0, the replica has caught up to the source DB instance\. If the `ReplicaLag` metric returns \-1, then replication is currently not active\. `ReplicaLag` = \-1 is equivalent to `Seconds_Behind_Master` = `NULL`\. 

   PostgreSQL \(versions 9\.4\.7 and 9\.5\.2 and later\) uses physical replication slots to manage Write Ahead Log \(WAL\) retention on the source instance\. For each cross\-region Read Replica instance, Amazon RDS creates a physical replication slot and associates it with the instance\. Two Amazon CloudWatch metrics, `Oldest Replication Slot Lag` and `Transaction Logs Disk Usage`, show how far behind the most lagging replica is in terms of WAL data received and how much storage is being used for WAL data\. The `Transaction Logs Disk Usage` value can substantially increase when a cross\-region Read Replica is lagging significantly\.

### Cross\-Region Replication Examples<a name="USER_ReadRepl.XRgn.Examples"></a>

**Example Create a Cross\-Region Read Replica Outside of Any VPC**  
The following example creates a Read Replica in us\-west\-2 from a source DB instance in us\-east\-1\. The Read Replica is created outside of a VPC:  
For Linux, OS X, or Unix:  

```
1. aws rds create-db-instance-read-replica \
2.     --db-instance-identifier SimCoProd01Replica01 \
3.     --region us-west-2
4.     --source-db-instance-identifier arn:aws:rds:us-east-1:123456789012:db:SimcoProd01
```
For Windows:  

```
1. aws rds create-db-instance-read-replica ^
2.     --db-instance-identifier SimCoProd01Replica01 ^
3.     --region us-west-2
4.     --source-db-instance-identifier arn:aws:rds:us-east-1:123456789012:db:SimcoProd01
```

**Example Create Cross\-Region Read Replica in a VPC**  
This example creates a Read Replica in us\-west\-2 from a source DB instance in us\-east\-1\. The Read Replica is created in the VPC associated with the specified DB subnet group:  
For Linux, OS X, or Unix:  

```
1. aws rds create-db-instance-read-replica \
2.     --db-instance-identifier SimCoProd01Replica01 \
3.     --region us-west-2
4.     --db-subnet-group-name my-us-west-2-subnet 
5.     --source-db-instance-identifier arn:aws:rds:us-east-1:123456789012:db:SimcoProd01
```
For Windows:  

```
1. aws rds create-db-instance-read-replica ^
2.     --db-instance-identifier SimCoProd01Replica01 ^
3.     --region us-west-2
4.     --db-subnet-group-name my-us-west-2-subnet 
5.     --source-db-instance-identifier arn:aws:rds:us-east-1:123456789012:db:SimcoProd01
```

## Monitoring Read Replication<a name="USER_ReadRepl.Monitoring"></a>

You can monitor the status of a Read Replica in several ways\. The Amazon RDS console shows the status of a Read Replica in the **Availability and durability** section of the Read Replica details\. To view the details for a Read Replica, select the Read Replica in the list of instances in the Amazon RDS console, and choose **See details** from **Instance actions**\. 

![\[Read Replica status\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/ReadReplicaStatus.png)

You can also see the status of a Read Replica using the AWS CLI `describe-db-instances` command or the Amazon RDS API `DescribeDBInstances` action\.

The status of a Read Replica can be one of the following: 
+ ****Replicating**—**The Read Replica is replicating successfully\.
+ ****Error**—**An error has occurred with the replication\. Check the **Replication Error** field in the Amazon RDS console or the event log to determine the exact error\. For more information about troubleshooting a replication error, see [Troubleshooting a MySQL or MariaDB Read Replica Problem](#USER_ReadRepl.Troubleshooting)\.
+ ****Stopped**—**\(MySQL or MariaDB only\) Replication has stopped because of a customer initiated request\.
+ ****Terminated**—**Replication is terminated\. This occurs if replication is stopped for more than thirty consecutive days, either manually or due to a replication error\. In this case, Amazon RDS terminates replication between the master DB instance and all Read Replicas in order to prevent increased storage requirements on the master DB instance and long failover times\. 

  Broken replication can affect storage because the logs can grow in size and number due to the high volume of errors messages being written to the log\. Broken replication can also affect failure recovery due to the time Amazon RDS requires to maintain and process the large number of logs during recovery\.

You can monitor how far a MySQL or MariaDB Read Replica is lagging the source DB instance by viewing the **Seconds\_Behind\_Master** data returned by the MySQL or MariaDB `Show Slave Status` command, or the CloudWatch **Replica Lag** statistic\. If a replica lags too far behind for your environment, consider deleting and recreating the Read Replica\. Also consider increasing the scale of the Read Replica to speed replication\. 

## Troubleshooting a MySQL or MariaDB Read Replica Problem<a name="USER_ReadRepl.Troubleshooting"></a>

The replication technologies for MySQL and MariaDB are asynchronous\. Because they are asynchronous, occasional `BinLogDiskUsage` increases on the source DB instance and `ReplicaLag` on the Read Replica are to be expected\. For example, a high volume of write operations to the source DB instance can occur in parallel\. In contrast, write operations to the Read Replica are serialized using a single I/O thread, which can lead to a lag between the source instance and Read Replica\. For more information about read\-only replicas in the MySQL documentation, see [Replication Implementation Details](http://dev.mysql.com/doc/refman/5.5/en/replication-implementation-details.html)\. For more information about read\-only replicas in the MariaDB documentation, go to [Replication Overview](http://mariadb.com/kb/en/mariadb/replication-overview/)\.

You can do several things to reduce the lag between updates to a source DB instance and the subsequent updates to the Read Replica, such as the following:
+ Sizing a Read Replica to have a storage size and DB instance class comparable to the source DB instance\.
+ Ensuring that parameter settings in the DB parameter groups used by the source DB instance and the Read Replica are compatible\. For more information and an example, see the discussion of the `max_allowed_packet` parameter later in this section\.

Amazon RDS monitors the replication status of your Read Replicas and updates the `Replication State` field of the Read Replica instance to `Error` if replication stops for any reason\. An example might be if DML queries run on your Read Replica conflict with the updates made on the source DB instance\. 

You can review the details of the associated error thrown by the MySQL or MariaDB engines by viewing the `Replication Error` field\. Events that indicate the status of the Read Replica are also generated, including [RDS-EVENT-0045](USER_Events.md#RDS-EVENT-0045), [RDS-EVENT-0046](USER_Events.md#RDS-EVENT-0046), and [RDS-EVENT-0047](USER_Events.md#RDS-EVENT-0047)\. For more information about events and subscribing to events, see [Using Amazon RDS Event Notification](USER_Events.md)\. If a MySQL error message is returned, review the error number in the [ MySQL error message documentation](http://dev.mysql.com/doc/refman/5.5/en/error-messages-server.html)\. If a MariaDB error message is returned, review the error in the [MariaDB error message documentation](http://mariadb.com/kb/en/mariadb/mariadb-error-codes/)\.

One common issue that can cause replication errors is when the value for the `max_allowed_packet` parameter for a Read Replica is less than the `max_allowed_packet` parameter for the source DB instance\. The `max_allowed_packet` parameter is a custom parameter that you can set in a DB parameter group that is used to specify the maximum size of DML code that can be executed on the database\. In some cases, the `max_allowed_packet` parameter value in the DB parameter group associated with a source DB instance is smaller than the `max_allowed_packet` parameter value in the DB parameter group associated with the source's Read Replica\. In these cases, the replication process can throw an error \(Packet bigger than 'max\_allowed\_packet' bytes\) and stop replication\. You can fix the error by having the source and Read Replica use DB parameter groups with the same `max_allowed_packet` parameter values\. 

Other common situations that can cause replication errors include the following:
+ Writing to tables on a Read Replica\. If you are creating indexes on a Read Replica, you need to have the `read_only` parameter set to **0** to create the indexes\. If you are writing to tables on the Read Replica, it might break replication\. 
+  Using a non\-transactional storage engine such as MyISAM\. Read replicas require a transactional storage engine\. Replication is only supported for the InnoDB storage engine on MySQL and the XtraDB storage engine on MariaDB\.
+  Using unsafe nondeterministic queries such as `SYSDATE()`\. For more information, see [Determination of Safe and Unsafe Statements in Binary Logging](http://dev.mysql.com/doc/refman/5.5/en/replication-rbr-safe-unsafe.html)\. 

If you decide that you can safely skip an error, you can follow the steps described in the section [Skipping the Current Replication Error](Appendix.MySQL.CommonDBATasks.md#Appendix.MySQL.CommonDBATasks.SkipError)\. Otherwise, you can delete the Read Replica and create an instance using the same DB instance identifier so that the endpoint remains the same as that of your old Read Replica\. If a replication error is fixed, the `Replication State` changes to *replicating*\.

## Troubleshooting a PostgreSQL Read Replica Problem<a name="USER_ReadRepl.TroubleshootingPostgreSQL"></a>

PostgreSQL uses replication slots for cross\-region replication, so the process for troubleshooting same\-region replication problems and cross\-region replication problems is different\.

### Troubleshooting PostgreSQL Read Replica Problems Within an AWS Region<a name="USER_ReadRepl.TroubleshootingPostgreSQL.WithinARegion"></a>

The PostgreSQL parameter, `wal_keep_segments`, dictates how many Write Ahead Log \(WAL\) files are kept to provide data to the Read Replicas\. The parameter value specifies the number of logs to keep\. If you set the parameter value too low, you can cause a Read Replica to fall so far behind that streaming replication stops\. In this case, Amazon RDS reports a replication error and begins recovery on the Read Replica by replaying the source DB instance's archived WAL logs\. This recovery process continues until the Read Replica has caught up enough to continue streaming replication\. 

 The PostgreSQL log on the Read Replica shows when Amazon RDS is recovering a Read Replica that is this state by replaying archived WAL files\. 

```
2014-11-07 19:01:10 UTC::@:[23180]:DEBUG: switched WAL source from archive to stream after failure
2014-11-07 19:01:10 UTC::@:[11575]:LOG: started streaming WAL from primary at 1A/D3000000 on timeline 1
2014-11-07 19:01:10 UTC::@:[11575]:FATAL: could not receive data from WAL stream: ERROR: requested WAL segment 000000010000001A000000D3 has already been removed
2014-11-07 19:01:10 UTC::@:[23180]:DEBUG: could not restore file "00000002.history" from archive: return code 0
2014-11-07 19:01:15 UTC::@:[23180]:DEBUG: switched WAL source from stream to archive after failure recovering 000000010000001A000000D3
2014-11-07 19:01:16 UTC::@:[23180]:LOG: restored log file "000000010000001A000000D3" from archive
```

After a certain amount of time, Amazon RDS replays enough archived WAL files on the replica to catch up and allow the Read Replica to begin streaming again\. At this point, PostgreSQL resumes streaming and writes a similar line to the following to the log file\.

```
  2014-11-07 19:41:36 UTC::@:[24714]:LOG:  started streaming WAL from primary at 1B/B6000000
      on timeline 1
```

You can determine how many WAL files you should keep by looking at the checkpoint information in the log\. The PostgreSQL log shows the following information at each checkpoint\. By looking at the "\# recycled" transaction log files of these log statements, you can understand how many transaction files will be recycled during a time range and use this information to tune the `wal_keep_segments` parameter\. 

```
  2014-11-07 19:59:35 UTC::@:[26820]:LOG:  checkpoint complete: wrote 376 buffers (0.2%); 0
      transaction log file(s) added, 0 removed, 1 recycled; write=35.681 s, sync=0.013 s,
      total=35.703 s; sync files=10, longest=0.013 s, average=0.001 s
```

For example, suppose that the PostgreSQL log shows that 35 files are recycled from the "checkpoint completed" log statements within a 5\-minute time frame\. In that case, we know that with this usage pattern a Read Replica relies on 35 transaction files in five minutes\. A Read Replica can't survive five minutes in a nonstreaming state if the source DB instance is set to the default `wal_keep_segments` parameter value of 32\.

### Troubleshooting PostgreSQL Read Replica Problems Across AWS Regions<a name="USER_ReadRepl.TroubleshootingPostgreSQL.AcrossRegions"></a>

PostgreSQL \(versions 9\.4\.7 and 9\.5\.2 exclusively\) uses physical replication slots to manage Write Ahead Log \(WAL\) retention on the source DB instance\. For each cross\-region Read Replica instance, Amazon RDS creates and associates a physical replication slot\. You can use two Amazon CloudWatch metrics, `Oldest Replication Slot Lag` and `Transaction Logs Disk Usage`, to see how far behind the most lagging replica is in terms of WAL data received and to see how much storage is being used for WAL data\. The `Transaction Logs Disk Usage` value can substantially increase when a cross\-region Read Replica is lagging significantly\.

If the workload on your DB instance generates a large amount of WAL data, you might need to change the DB instance class of your source DB instance and Read Replica\. In that case, you change it to one with high \(10 Gbps\) network performance for the replica to keep up\. The Amazon CloudWatch metric `Transaction Logs Generation` can help you understand the rate at which your workload is generating WAL data\. 

 To determine the status of a cross\-region Read Replica, you can query `pg_replication_slots` on the source instance, as in the following example: 

```
postgres=# select * from pg_replication_slots;

                  slot_name                  | plugin | slot_type | datoid | database | active | active_pid | xmin | catalog_xmin | restart_lsn
_______________________________________________________________________________________________________________________________________________
 rds_us_east_1_db_uzwlholddgpblksce6hgw4nkte |        | physical  |        |          | t      |      12598 |      |              | 4E/95000060
(1 row)
```