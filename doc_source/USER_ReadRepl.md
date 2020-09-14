# Working with Read Replicas<a name="USER_ReadRepl"></a>

Amazon RDS uses the MariaDB, MySQL, Oracle, PostgreSQL, and Microsoft SQL Server DB engines' built\-in replication functionality to create a special type of DB instance called a read replica from a source DB instance\. The source DB instance becomes the primary DB instance\. Updates made to the primary DB instance are asynchronously copied to the read replica\. You can reduce the load on your primary DB instance by routing read queries from your applications to the read replica\. Using read replicas, you can elastically scale out beyond the capacity constraints of a single DB instance for read\-heavy database workloads\.

**Note**  
The information following applies to creating Amazon RDS read replicas either in the same AWS Region as the source DB instance, or in a separate AWS Region\. The information following doesn't apply to setting up replication with an instance that is running on an Amazon EC2 instance or that is on\-premises\.

When you create a read replica, you first specify an existing DB instance as the source\. Then Amazon RDS takes a snapshot of the source instance and creates a read\-only instance from the snapshot\. Amazon RDS then uses the asynchronous replication method for the DB engine to update the read replica whenever there is a change to the primary DB instance\. The read replica operates as a DB instance that allows only read\-only connections\. Applications connect to a read replica the same way they do to any DB instance\. Amazon RDS replicates all databases in the source DB instance\.

**Note**  
The Oracle DB engine supports replica databases in mounted mode\. A mounted replica doesn't accept user connections and so can't serve a read\-only workload\. The primary use for mounted replicas is cross\-Region disaster recovery\. For more information, see [Working with Oracle Replicas for Amazon RDS](oracle-read-replicas.md)\.

In some cases, a read replica resides in a different AWS Region from its primary DB instance\. In these cases, Amazon RDS sets up a secure communications channel between the primary DB instance and the read replica\. Amazon RDS establishes any AWS security configurations needed to enable the secure channel, such as adding security group entries\.

Read replicas are supported by the MariaDB, MySQL, Oracle, PostgreSQL, and Microsoft SQL Server engines\. In this section, you can find general information about using read replicas with all of these engines\. For information about using read replicas with a specific engine, see the following sections:
+ [Working with MariaDB Read Replicas](USER_MariaDB.Replication.ReadReplicas.md)
+ [Working with Read Replicas for Microsoft SQL Server in Amazon RDS](SQLServer.ReadReplicas.md)
+ [Working with MySQL Read Replicas](USER_MySQL.Replication.ReadReplicas.md)
+ [Working with Oracle Replicas for Amazon RDS](oracle-read-replicas.md)
+ [Working with PostgreSQL Read Replicas in Amazon RDS](USER_PostgreSQL.Replication.ReadReplicas.md)

## Overview of Amazon RDS Read Replicas<a name="USER_ReadRepl.Overview"></a>

Deploying one or more read replicas for a given source DB instance might make sense in a variety of scenarios, including the following: 
+ Scaling beyond the compute or I/O capacity of a single DB instance for read\-heavy database workloads\. You can direct this excess read traffic to one or more read replicas\.
+ Serving read traffic while the source DB instance is unavailable\. In some cases, your source DB instance might not be able to take I/O requests, for example due to I/O suspension for backups or scheduled maintenance\. In these cases, you can direct read traffic to your read replicas\. For this use case, keep in mind that the data on the read replica might be "stale" because the source DB instance is unavailable\.
+ Business reporting or data warehousing scenarios where you might want business reporting queries to run against a read replica, rather than your production DB instance\. 
+ Implementing disaster recovery\. You can promote a read replica to a standalone instance as a disaster recovery solution if the primary DB instance fails\.

By default, a read replica is created with the same storage type as the source DB instance\. However, you can create a read replica that has a different storage type from the source DB instance based on the options listed in the following table\.

<a name="rds-read-replica-storage-reference"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ReadRepl.html)

Amazon RDS doesn't support circular replication\. You can't configure a DB instance to serve as a replication source for an existing DB instance\. You can only create a new read replica from an existing DB instance\. For example, if **MyDBInstance** replicates to **ReadReplica1**, you can't configure **ReadReplica1** to replicate back to **MyDBInstance**\. For MariaDB and MySQL you can create a read replica from an existing read replica\. For example, from **ReadReplica1**, you can create a new read replica, such as **ReadReplica2**\. For Oracle, PostgreSQL, and SQL Server, you can't create a read replica from an existing read replica\.

If you no longer need read replicas, you can explicitly delete them using the same mechanisms for deleting a DB instance\. If you delete a source DB instance without deleting its read replicas in the same AWS Region, each read replica is promoted to a standalone DB instance\. For information about deleting a DB instance, see [Deleting a DB Instance](USER_DeleteInstance.md)\. For information about read replica promotion, see [Promoting a Read Replica to Be a Standalone DB Instance](#USER_ReadRepl.Promote)\. If you have cross\-Region read replicas, see [Cross\-Region Replication Considerations](#USER_ReadRepl.XRgn.Cnsdr) for considerations related to deleting the source for a cross\-Region read replica\.

### Differences Between Read Replicas for Different DB Engines<a name="USER_ReadRepl.Overview.Differences"></a>

Because Amazon RDS DB engines implement replication differently, there are several significant differences you should know about, as shown in the following table\.


| Feature or Behavior | MySQL and MariaDB | Oracle | PostgreSQL | SQL Server | 
| --- | --- | --- | --- | --- | 
|  What is the replication method?   |  Logical replication\.  |  Physical replication\.  |  Physical replication\.  |  Physical replication\.  | 
|  How are transaction logs purged?  |  RDS MySQL and RDS MariaDB keep any binary logs that haven't been applied\.  |  If a primary DB instance has no cross\-Region read replicas, Amazon RDS for Oracle keeps a minimum of two hours of transaction logs on the source DB instance\. Logs are purged from the source DB instance after two hours or after the archive log retention hours setting has passed, whichever is longer\. Logs are purged from the read replica after the archive log retention hours setting has passed only if they have been successfully applied to the database\.  In some cases, a primary DB instance might have one or more cross\-Region read replicas\. If so, Amazon RDS for Oracle keeps the transaction logs on the source DB instance until they have been transmitted and applied to all cross\-Region read replicas\. For information about setting archive log retention hours, see [Retaining Archived Redo Logs](Appendix.Oracle.CommonDBATasks.Log.md#Appendix.Oracle.CommonDBATasks.RetainRedoLogs)\.  |  PostgreSQL has the parameter `wal_keep_segments` that dictates how many write ahead log \(WAL\) files are kept to provide data to the read replicas\. The parameter value specifies the number of logs to keep\.   |  The Virtual Log File \(VLF\) of the transaction log file on the primary replica can be truncated after it is no longer required for the secondary replicas\.  The VLF can only be marked as inactive when the log records have been hardened in the replicas\. Regardless of how fast the disk subsystems are in the primary replica, the transaction log will keep the VLFs until the slowest replica has hardened it\.  | 
|  Can a replica be made writable?  |  Yes\. You can enable the MySQL or MariaDB read replica to be writable\.   |  No\. An Oracle read replica is a physical copy, and Oracle doesn't allow for writes in a read replica\. You can promote the read replica to make it writable\. The promoted read replica has the replicated data to the point when the request was made to promote it\.   |  No\. A PostgreSQL read replica is a physical copy, and PostgreSQL doesn't allow for a read replica to be made writable\.   |  No\. A SQL Server read replica is a physical copy and also doesn't allow for writes\. You can promote the read replica to make it writable\. The promoted read replica has the replicated data up to the point when the request was made to promote it\.  | 
|  Can backups be performed on the replica?  |  Yes\. You can enable automatic backups on a MySQL or MariaDB read replica\.   |  No\. You can't create manual snapshots of Amazon RDS for Oracle read replicas or enable automatic backups for them\.   |  Yes, you can create a manual snapshot of a PostgreSQL read replica, but you can't enable automatic backups\.   |  No\. You can't create manual snapshots of Amazon RDS for SQL Server read replicas or enable automatic backups for them\.  | 
|  Can you use parallel replication?  |  Yes\. MySQL version 5\.6 and later and all supported MariaDB versions allow for parallel replication threads\.   |  Yes\. Redo log data is always transmitted in parallel from the primary database to all of its read replicas\.  |  No\. PostgreSQL has a single process handling replication\.  |  Yes\. Redo log data is always transmitted in parallel from the primary database to all of its read replicas\.  | 
|  Can you maintain a replica in a mounted rather than a read\-only state?  |  No\.  |  Yes\. The primary use for mounted replicas is cross\-Region disaster recovery\. An Active Data Guard license isn't required for mounted replicas\. For more information, see [Working with Oracle Replicas for Amazon RDS](oracle-read-replicas.md)\.  |  No\.  |  No\.  | 

## Creating a Read Replica<a name="USER_ReadRepl.Create"></a>

You can create a read replica from an existing MySQL, MariaDB, Oracle, PostgreSQL DB, or SQL Server instance using the AWS Management Console, AWS CLI, or RDS API\. You create a read replica by specifying `SourceDBInstanceIdentifier`, which is the DB instance identifier of the source DB instance that you want to replicate from\.

When you create a read replica, Amazon RDS takes a DB snapshot of your source DB instance and begins replication\. As a result, you experience a brief I/O suspension on your source DB instance while the DB snapshot occurs\. The I/O suspension typically lasts about one minute\. You can avoid the I/O suspension if the source DB instance is a Multi\-AZ deployment, because in that case the snapshot is taken from the secondary DB instance\. An active, long\-running transaction can slow the process of creating the read replica\. We recommend that you wait for long\-running transactions to complete before creating a read replica\. If you create multiple read replicas in parallel from the same source DB instance, Amazon RDS takes only one snapshot at the start of the first create action\.

When creating a read replica, there are a few things to consider\. First, you must enable automatic backups on the source DB instance by setting the backup retention period to a value other than 0\. This requirement also applies to a read replica that is the source DB instance for another read replica\. For MySQL DB instances, automatic backups are supported only for read replicas running MySQL 5\.6 and later, but not for MySQL versions 5\.5\. To enable automatic backups on an Amazon RDS MySQL version 5\.6 and later read replica, first create the read replica, then modify the read replica to enable automatic backups\.

**Note**  
Within an AWS Region, all read replicas must be created in the same virtual private cloud \(VPC\) based on Amazon VPC as the source DB instance\. This requirement applies even if VPC peering is configured in the AWS Region\.  
You can't create a read replica in a different AWS account from the source DB instance\.

### Console<a name="USER_ReadRepl.Create.Console"></a>

**To create a read replica from a source MySQL, MariaDB, Oracle, PostgreSQL, or SQL Server DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the MySQL, MariaDB, Oracle, PostgreSQL, or SQL Server DB instance that you want to use as the source for a read replica\.

1. For **Actions**, choose **Create read replica**\. 

1. Choose your instance specifications\. We recommend that you use the same DB instance class and storage type as the source DB instance for the read replica\. For **Multi\-AZ deployment**, choose **Yes** to create a standby of your replica in another Availability Zone for failover support for the replica\. Creating your read replica as a Multi\-AZ DB instance is independent of whether the source database is a Multi\-AZ DB instance\.

1. Choose the settings that you want to use\. For **DB instance identifier**, enter a name for the read replica\. Adjust other settings as needed\.

1. Choose the other settings that you want to use\.

1. Choose **Create read replica**\.

### AWS CLI<a name="USER_ReadRepl.Create.CLI"></a>

To create a read replica from a source MySQL, MariaDB, Oracle, PostgreSQL, or SQL Server DB instance, use the AWS CLI command [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance-read-replica.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance-read-replica.html)\.

**Example**  
For Linux, macOS, or Unix:  

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

### RDS API<a name="USER_ReadRepl.Create.API"></a>

To create a read replica from a source MySQL, MariaDB, Oracle, PostgreSQL, or SQL Server DB instance, call the Amazon RDS API function [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstanceReadReplica.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstanceReadReplica.html)\.

```
https://rds.amazonaws.com/
	?Action=CreateDBInstanceReadReplica
	&DBInstanceIdentifier=myreadreplica
	&SourceDBInstanceIdentifier=mydbinstance
	&Version=2012-01-15						
	&SignatureVersion=2
	&SignatureMethod=HmacSHA256
	&Timestamp=2012-01-20T22%3A06%3A23.624Z
	&AWSAccessKeyId=<AWS Access Key ID>
	&Signature=<Signature>
```

## Promoting a Read Replica to Be a Standalone DB Instance<a name="USER_ReadRepl.Promote"></a>

You can promote a MySQL, MariaDB, Oracle, PostgreSQL, or SQL Server read replica into a standalone DB instance\. When you promote a read replica, the DB instance is rebooted before it becomes available\.

There are several reasons you might want to promote a read replica to a standalone DB instance:
+ **Performing DDL operations \(MySQL and MariaDB only\)** – DDL operations, such as creating or rebuilding indexes, can take time and impose a significant performance penalty on your DB instance\. You can perform these operations on a MySQL or MariaDB read replica once the read replica is in sync with its primary DB instance\. Then you can promote the read replica and direct your applications to use the promoted instance\.
+ **Sharding** – Sharding embodies the "share\-nothing" architecture and essentially involves breaking a large database into several smaller databases\. One common way to split a database is splitting tables that are not joined in the same query onto different hosts\. Another method is duplicating a table across multiple hosts and then using a hashing algorithm to determine which host receives a given update\. You can create read replicas corresponding to each of your shards \(smaller databases\) and promote them when you decide to convert them into standalone shards\. You can then carve out the key space \(if you are splitting rows\) or distribution of tables for each of the shards depending on your requirements\.
+ **Implementing failure recovery** – You can use read replica promotion as a data recovery scheme if the primary DB instance fails\. This approach complements synchronous replication, automatic failure detection, and failover\.

  If you are aware of the ramifications and limitations of asynchronous replication and you still want to use read replica promotion for data recovery, you can\. To do this, first create a read replica and then monitor the primary DB instance for failures\. In the event of a failure, do the following:

  1. Promote the read replica\.

  1. Direct database traffic to the promoted DB instance\.

  1. Create a replacement read replica with the promoted DB instance as its source\.

When you promote a read replica, the new DB instance that is created retains the backup retention period, the backup window, the option group, and the parameter group of the former read replica source\. The promotion process can take several minutes or longer to complete, depending on the size of the read replica\. After you promote the read replica to a new DB instance, it's just like any other DB instance\. For example, you can create read replicas from the new DB instance and perform point\-in\-time restore operations\. Because the promoted DB instance is no longer a read replica, you can't use it as a replication target\. If a source DB instance has several read replicas, promoting one of the read replicas to a DB instance has no effect on the other replicas\.

Backup duration is a function of the number of changes to the database since the previous backup\. If you plan to promote a read replica to a standalone instance, we recommend that you enable backups and complete at least one backup prior to promotion\. In addition, you can't promote a read replica to a standalone instance when it has the `backing-up` status\. If you have enabled backups on your read replica, configure the automated backup window so that daily backups don't interfere with read replica promotion\.

The following steps show the general process for promoting a read replica to a DB instance:

1. Stop any transactions from being written to the primary DB instance, and then wait for all updates to be made to the read replica\. Database updates occur on the read replica after they have occurred on the primary DB instance, and this replication lag can vary significantly\. Use the [Replica Lag](http://aws.amazon.com/rds/faqs/#105) metric to determine when all updates have been made to the read replica\.

1. For MySQL and MariaDB only: If you need to make changes to the MySQL or MariaDB read replica, you must set the `read_only` parameter to `0` in the DB parameter group for the read replica\. You can then perform all needed DDL operations, such as creating indexes, on the read replica\. Actions taken on the read replica don't affect the performance of the primary DB instance\.

1. Promote the read replica by using the **Promote** option on the Amazon RDS console, the AWS CLI command [https://docs.aws.amazon.com/cli/latest/reference/rds/promote-read-replica.html](https://docs.aws.amazon.com/cli/latest/reference/rds/promote-read-replica.html), or the [ `PromoteReadReplica`](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_PromoteReadReplica.html) Amazon RDS API operation\.
**Note**  
The promotion process takes a few minutes to complete\. When you promote a read replica, replication is stopped and the read replica is rebooted\. When the reboot is complete, the read replica is available as a new DB instance\.

1. \(Optional\) Modify the new DB instance to be a Multi\-AZ deployment\. For more information, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md) and [High Availability \(Multi\-AZ\) for Amazon RDS](Concepts.MultiAZ.md)\. 

### Console<a name="USER_ReadRepl.Promote.Console"></a>

**To promote a read replica to a DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the Amazon RDS console, choose **Databases**\.

   The **Databases** pane appears\. Each read replica shows **Replica** in the **Role** column\.

1. Choose the read replica that you want to promote\.

1. For **Actions**, choose **Promote read replica**\.

1. On the **Promote Read Replica** page, enter the backup retention period and the backup window for the newly promoted DB instance\.

1. When the settings are as you want them, choose **Continue**\.

1. On the acknowledgment page, choose **Promote Read Replica**\.

### AWS CLI<a name="USER_ReadRepl.Promote.CLI"></a>

To promote a read replica to a DB instance, use the AWS CLI [https://docs.aws.amazon.com/cli/latest/reference/rds/promote-read-replica.html](https://docs.aws.amazon.com/cli/latest/reference/rds/promote-read-replica.html) command\.

**Example**  
For Linux, macOS, or Unix:  

```
aws rds promote-read-replica \
    --db-instance-identifier myreadreplica
```
For Windows:  

```
aws rds promote-read-replica ^
    --db-instance-identifier myreadreplica
```

### RDS API<a name="USER_ReadRepl.Promote.API"></a>

To promote a read replica to a DB instance, call [ `PromoteReadReplica`](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_PromoteReadReplica.html)\.

```
https://rds.amazonaws.com/
	?Action=PromoteReadReplica
	&DBInstanceIdentifier=myreadreplica
	&Version=2012-01-15						
	&SignatureVersion=2
	&SignatureMethod=HmacSHA256
	&Timestamp=2012-01-20T22%3A06%3A23.624Z
	&AWSAccessKeyId=<AWS Access Key ID>
	&Signature=<Signature>
```

## Creating a Read Replica in a Different AWS Region<a name="USER_ReadRepl.XRgn"></a>

With Amazon RDS, you can create a MariaDB, MySQL, Oracle, or PostgreSQL read replica in a different AWS Region from the source DB instance\. Creating a cross\-Region read replica isn't supported for SQL Server on Amazon RDS\.

You create a read replica in a different AWS Region to do the following:
+ Improve your disaster recovery capabilities\.
+ Scale read operations into an AWS Region closer to your users\.
+ Make it easier to migrate from a data center in one AWS Region to a data center in another AWS Region\.

Creating a read replica in a different AWS Region from the source instance is similar to creating a replica in the same AWS Region\. You can use the AWS Management Console, run the [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance-read-replica.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance-read-replica.html) command, or call the [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstanceReadReplica.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstanceReadReplica.html) API operation\.

To create an encrypted read replica in a different AWS Region from the source DB instance, the source DB instance must be encrypted\.

Cross\-Region read replicas aren't supported in the following opt\-in AWS Regions:
+ Africa \(Cape Town\)
+ Asia Pacific \(Hong Kong\)
+ Europe \(Milan\)
+ Middle East \(Bahrain\)

The following procedures show how to create a read replica from a source MariaDB, MySQL, Oracle, or PostgreSQL DB instance in a different AWS Region\.

### Console<a name="USER_ReadRepl.XRgn.CON"></a>

You can create a read replica across AWS Regions using the AWS Management Console\.

**To create a read replica across AWS Regions with the console**

1.  Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the MariaDB, MySQL, Oracle, or PostgreSQL DB instance that you want to use as the source for a read replica\. For **Actions**, choose **Create read replica**\. To create an encrypted read replica, the source DB instance must be encrypted\. To learn more about encrypting the source DB instance, see [Encrypting Amazon RDS Resources](Overview.Encryption.md)\.

1. Choose the instance specifications you want to use\. We recommend that you use the same DB instance class and storage type for the read replica\.

1. Choose the other settings that you want to use:
   + For **DB instance identifier**, enter a name for the read replica\.
   + In the **Network & Security** section, choose a value for **Destination region** and **Destination DB subnet group**\.
   + To create an encrypted read replica in another AWS Region, choose **Enable Encryption**, and then choose the **Master key**\. For the **Master key**, choose the AWS Key Management Service \(AWS KMS\) key identifier of the customer master key \(CMK\) of the destination AWS Region\.
   + Choose the other settings that you want to use\.

1. Choose **Create read replica**\.

### AWS CLI<a name="USER_ReadRepl.XRgn.CLI"></a>

 To create a read replica from a source MySQL, MariaDB, Oracle, or PostgreSQL DB instance in a different AWS Region, you can use the [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance-read-replica.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance-read-replica.html) command\. In this case, you use [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance-read-replica.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance-read-replica.html) from the AWS Region where you want the read replica and specify the Amazon Resource Name \(ARN\) for the source DB instance\. An ARN uniquely identifies a resource created in Amazon Web Services\.

For example, if your source DB instance is in the US East \(N\. Virginia\) Region, the ARN looks similar to this example:

`arn:aws:rds:us-east-1:123456789012:db:my-mysql-instance`

For information about ARNs, see [Working with Amazon Resource Names \(ARNs\) in Amazon RDS](USER_Tagging.ARN.md)\.

 To create an encrypted read replica in a different AWS Region than the source DB instance, you can use the AWS CLI [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance-read-replica.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance-read-replica.html) command from the destination AWS Region\. The following parameters are used to create an encrypted read replica in another AWS Region:
+  `--source-region` — The AWS Region that the encrypted read replica is created in\. If `source-region` is not specified, you must specify a `pre-signed-url` value\. A `pre-signed-url` is an URL that contains a Signature Version 4 signed request for the `CreateDBInstanceReadReplica` operation that is called in the source AWS Region that the read replica is created from\. To learn more about `pre-signed-url`, see [ `CreateDBInstanceReadReplica`](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstanceReadReplica.html)\.
+  `--source-db-instance-identifier` — The DB instance identifier for the encrypted read replica that is created\. This identifier must be in the ARN format for the source AWS Region\. The AWS Region specified in `source-db-instance-identifier` must match the AWS Region specified as `source-region`\.
+  `--db-instance-identifier` — The identifier for the encrypted read replica in the destination AWS Region\.
+  `--kms-key-id` — The AWS KMS key identifier for the customer master key \(CMK\) to use to encrypt the read replica in the destination AWS Region\.

The following code creates a read replica in the `us-west-2` Region\.

**Example**  
For Linux, macOS, or Unix:  

```
aws rds create-db-instance-read-replica \
    --db-instance-identifier DBInstanceIdentifier \
    --region us-west-2 \
    --source-db-instance-identifier arn:aws:rds:us-east-1:123456789012:db:my-mysql-instance
```
For Windows:  

```
aws rds create-db-instance-read-replica ^
    --db-instance-identifier DBInstanceIdentifier ^
    --region us-west-2 ^
    --source-db-instance-identifier arn:aws:rds:us-east-1:123456789012:db:my-mysql-instance
```

The following code creates a read replica in a different AWS Region than the source DB instance\. The AWS Region where you call the `create-db-instance-read-replica` command is the destination AWS Region for the encrypted read replica\.

**Example**  
For Linux, macOS, or Unix:  

```
aws rds create-db-instance-read-replica \
    --db-instance-identifier DBInstanceIdentifier \
    --region us-west-2 \
    --source-db-instance-identifier arn:aws:rds:us-east-1:123456789012:db:my-mysql-instance \
    --source-region us-east-1 \
    --kms-key-id my-us-east-1-key
```
For Windows:  

```
aws rds create-db-instance-read-replica ^
    --db-instance-identifier DBInstanceIdentifier ^
    --region us-west-2 ^
    --source-db-instance-identifier arn:aws:rds:us-east-1:123456789012:db:my-mysql-instance ^
    --source-region us-east-1 ^	
    --kms-key-id my-us-east-1-key
```

### RDS API<a name="USER_ReadRepl.XRgn.API"></a>

 To create a read replica from a source MySQL, MariaDB, Oracle, or PostgreSQL DB instance in a different AWS Region, you can call the Amazon RDS API function [CreateDBInstanceReadReplica](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstanceReadReplica.html)\. In this case, you call [CreateDBInstanceReadReplica](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstanceReadReplica.html) from the AWS Region where you want the read replica and specify the Amazon Resource Name \(ARN\) for the source DB instance\. An ARN uniquely identifies a resource created in Amazon Web Services\.

 To create an encrypted read replica in a different AWS Region than the source DB instance, you can use the Amazon RDS API [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstanceReadReplica.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstanceReadReplica.html) operation from the destination AWS Region\. To create an encrypted read replica in another AWS Region, you must specify a value for `PreSignedURL`\. `PreSignedURL` should contain a request for the [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstanceReadReplica.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstanceReadReplica.html) operation to call in the source AWS Region where the read replica is created in\. To learn more about `PreSignedUrl`, see [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstanceReadReplica.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstanceReadReplica.html)\.

For example, if your source DB instance is in the US East \(N\. Virginia\) Region, the ARN looks similar to the following\.

`arn:aws:rds:us-east-1:123456789012:db:my-mysql-instance`

For information about ARNs, see [Working with Amazon Resource Names \(ARNs\) in Amazon RDS](USER_Tagging.ARN.md)\.

**Example**  

```
https://us-west-2.rds.amazonaws.com/
    ?Action=CreateDBInstanceReadReplica
    &KmsKeyId=my-us-east-1-key
    &PreSignedUrl=https%253A%252F%252Frds.us-west-2.amazonaws.com%252F
         %253FAction%253D CreateDBInstanceReadReplica
         %2526DestinationRegion%253Dus-east-1
         %2526KmsKeyId%253Dmy-us-east-1-key
         %2526SourceDBInstanceIdentifier%253Darn%25253Aaws%25253Ards%25253Aus-west-2%1234567890
12%25253Adb%25253Amy-mysql-instance
         %2526SignatureMethod%253DHmacSHA256
         %2526SignatureVersion%253D4%2526SourceDBInstanceIdentifier%253Darn%25253Aaws%25253Ards%25253Aus-west-2%25253A123456789012%25253Ainstance%25253Amysql-instance1-instance-20161115
         %2526Version%253D2014-10-31
         %2526X-Amz-Algorithm%253DAWS4-HMAC-SHA256
         %2526X-Amz-Credential%253DAKIADQKE4SARGYLE%252F20161117%252Fus-west-2%252Frds%252Faws4_request
         %2526X-Amz-Date%253D20161117T215409Z
         %2526X-Amz-Expires%253D3600
         %2526X-Amz-SignedHeaders%253Dcontent-type%253Bhost%253Buser-agent%253Bx-amz-content-sha256%253Bx-amz-date
         %2526X-Amz-Signature%253D255a0f17b4e717d3b67fad163c3ec26573b882c03a65523522cf890a67fca613
    &DBInstanceIdentifier=myreadreplica
    &SourceDBInstanceIdentifier=arn:aws:rds:us-east-1:123456789012:db:my-mysql-instance
    &Version=2012-01-15						
    &SignatureVersion=2
    &SignatureMethod=HmacSHA256
    &Timestamp=2012-01-20T22%3A06%3A23.624Z
    &AWSAccessKeyId=<AWS Access Key ID>
    &Signature=<Signature>
```

### Cross\-Region Replication Considerations<a name="USER_ReadRepl.XRgn.Cnsdr"></a>

All of the considerations for performing replication within an AWS Region apply to cross\-Region replication\. The following extra considerations apply when replicating between AWS Regions:
+ You can only replicate between AWS Regions when using the following Amazon RDS DB instances:
  + MariaDB \(all versions\)\.
  + MySQL version 5\.6 and later\.
  + Oracle Enterprise Edition \(EE\) engine version 12\.1\.0\.2\.v10 and higher 12\.1 versions, all versions of 12\.2, and all versions of 18\.0\.

    An Active Data Guard license is required\. For information about limitations for Oracle cross\-Region read replicas, see [Replica Requirements for Oracle](oracle-read-replicas.md#oracle-read-replicas.limitations)\.
  + PostgreSQL \(all versions\)\.
+ A source DB instance can have cross\-Region read replicas in multiple AWS Regions\.
+ You can only create a cross\-Region Amazon RDS read replica from a source Amazon RDS DB instance that is not a read replica of another Amazon RDS DB instance\.
+ You can't set up a replication channel into or out of the AWS GovCloud \(US\-West\) Region\.
+ You can expect to see a higher level of lag time for any read replica that is in a different AWS Region than the source instance\. This lag time comes from the longer network channels between regional data centers\.
+ Within an AWS Region, all cross\-Region read replicas created from the same source DB instance must either be in the same VPC or be outside of a VPC\. For cross\-Region read replicas, any of the create read replica commands that specify the `--db-subnet-group-name` parameter must specify a DB subnet group from the same VPC\.
+ You can create a cross\-Region read replica in a VPC from a source DB instance that is in a VPC in another AWS Region\. You can also create a cross\-Region read replica in a VPC from a source DB instance that is not in a VPC\. You can also create a cross\-Region read replica that is not in a VPC from a source DB instance that is in a VPC\.
+ Due to the limit on the number of access control list \(ACL\) entries for a VPC, we can't guarantee more than five cross\-Region read replica instances\. 
+ The read replica uses the default DB parameter group for the specified DB engine\.
+ The read replica uses the default security group\.
+ For MariaDB, MySQL, and Oracle DB instances, when the source for a cross\-Region read replica is deleted, the read replica is promoted\.
+ For PostgreSQL DB instances, when the source for a cross\-Region read replica is deleted, the replication status of the read replica is set to `terminated`\. The read replica isn't promoted\.

### Cross\-Region Replication Costs<a name="USER_ReadRepl.XRgn.Costs"></a>

The data transferred for cross\-Region replication incurs Amazon RDS data transfer charges\. These cross\-Region replication actions generate charges for the data transferred out of the source AWS Region:
+ When you create a read replica, Amazon RDS takes a snapshot of the source instance and transfers the snapshot to the read replica AWS Region\.
+ For each data modification made in the source databases, Amazon RDS transfers data from the source AWS Region to the read replica AWS Region\.

For more information about data transfer pricing, see [Amazon RDS Pricing](https://aws.amazon.com/rds/pricing/)\.

For MySQL and MariaDB instances, you can reduce your data transfer costs by reducing the number of cross\-Region read replicas that you create\. For example, suppose that you have a source DB instance in one AWS Region and want to have three read replicas in another AWS Region\. In this case, you create only one of the read replicas from the source DB instance\. You create the other two replicas from the first read replica instead of the source DB instance\.

For example, if you have `source-instance-1` in one AWS Region, you can do the following:
+ Create `read-replica-1` in the new AWS Region, specifying `source-instance-1` as the source\.
+ Create `read-replica-2` from `read-replica-1`\.
+ Create `read-replica-3` from `read-replica-1`\.

In this example, you are only charged for the data transferred from `source-instance-1` to `read-replica-1`\. You aren't charged for the data transferred from `read-replica-1` to the other two replicas because they are all in the same AWS Region\. If you create all three replicas directly from `source-instance-1`, you are charged for the data transfers to all three replicas\.

### How Amazon RDS Does Cross\-Region Replication<a name="USER_ReadRepl.XRgn.Process"></a>

Amazon RDS uses the following process to create a cross\-Region read replica\. Depending on the AWS Regions involved and the amount of data in the databases, this process can take hours to complete\. You can use this information to determine how far the process has proceeded when you create a cross\-Region read replica:

1. Amazon RDS begins configuring the source DB instance as a replication source and sets the status to *modifying*\.

1. Amazon RDS begins setting up the specified read replica in the destination AWS Region and sets the status to *creating*\.

1. Amazon RDS creates an automated DB snapshot of the source DB instance in the source AWS Region\. The format of the DB snapshot name is `rds:<InstanceID>-<timestamp>`, where `<InstanceID>` is the identifier of the source instance, and `<timestamp>` is the date and time the copy started\. For example, `rds:mysourceinstance-2013-11-14-09-24` was created from the instance `mysourceinstance` at `2013-11-14-09-24`\. During the creation of an automated DB snapshot, the source DB instance status remains *modifying*, the read replica status remains *creating*, and the DB snapshot status is *creating*\. The progress column of the DB snapshot page in the console reports how far the DB snapshot creation has progressed\. When the DB snapshot is complete, the status of both the DB snapshot and source DB instance are set to *available*\.

1. Amazon RDS begins a cross\-Region snapshot copy for the initial data transfer\. The snapshot copy is listed as an automated snapshot in the destination AWS Region with a status of *creating*\. It has the same name as the source DB snapshot\. The progress column of the DB snapshot display indicates how far the copy has progressed\. When the copy is complete, the status of the DB snapshot copy is set to *available*\.

1. Amazon RDS then uses the copied DB snapshot for the initial data load on the read replica\. During this phase, the read replica is in the list of DB instances in the destination, with a status of *creating*\. When the load is complete, the read replica status is set to *available*, and the DB snapshot copy is deleted\.

1. When the read replica reaches the available status, Amazon RDS starts by replicating the changes made to the source instance since the start of the create read replica operation\. During this phase, the replication lag time for the read replica is greater than 0\.

   For information about replication lag time, see [Monitoring Read Replication](#USER_ReadRepl.Monitoring)\.

### Cross\-Region Replication Examples<a name="USER_ReadRepl.XRgn.Examples"></a>

**Example Create a Cross\-Region Read Replica Outside of Any VPC**  
The following example creates a read replica in us\-west\-2 from a source DB instance in us\-east\-1\. The read replica is created outside of a VPC:  
For Linux, macOS, or Unix:  

```
aws rds create-db-instance-read-replica \
    --db-instance-identifier SimCoProd01Replica01 \
    --region us-west-2
    --source-db-instance-identifier arn:aws:rds:us-east-1:123456789012:db:SimcoProd01
```
For Windows:  

```
aws rds create-db-instance-read-replica ^
    --db-instance-identifier SimCoProd01Replica01 ^
    --region us-west-2
    --source-db-instance-identifier arn:aws:rds:us-east-1:123456789012:db:SimcoProd01
```

**Example Create Cross\-Region Read Replica in a VPC**  
This example creates a read replica in us\-west\-2 from a source DB instance in us\-east\-1\. The read replica is created in the VPC associated with the specified DB subnet group:  
For Linux, macOS, or Unix:  

```
aws rds create-db-instance-read-replica \
    --db-instance-identifier SimCoProd01Replica01 \
    --region us-west-2
    --db-subnet-group-name my-us-west-2-subnet 
    --source-db-instance-identifier arn:aws:rds:us-east-1:123456789012:db:SimcoProd01
```
For Windows:  

```
aws rds create-db-instance-read-replica ^
    --db-instance-identifier SimCoProd01Replica01 ^
    --region us-west-2
    --db-subnet-group-name my-us-west-2-subnet 
    --source-db-instance-identifier arn:aws:rds:us-east-1:123456789012:db:SimcoProd01
```

## Monitoring Read Replication<a name="USER_ReadRepl.Monitoring"></a>

You can monitor the status of a read replica in several ways\. The Amazon RDS console shows the status of a read replica in the **Availability and durability** section of the read replica details\. To view the details for a read replica, choose the name of the read replica in the list of instances in the Amazon RDS console\.

![\[Read replica status\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/ReadReplicaStatus.png)

You can also see the status of a read replica using the AWS CLI `describe-db-instances` command or the Amazon RDS API `DescribeDBInstances` operation\.

The status of a read replica can be one of the following:
+ ****replicating**** – The read replica is replicating successfully\.
+ ****replication degraded** \(SQL Server only\) – **Replicas are receiving data from the primary instance, but one or more databases might be not getting updates\. This can occur, for example, when a replica is in the process of setting up newly created databases\.

  The status doesn't transition from `replication degraded` to `error`, unless an error occurs during the degraded state\.
+ ****error**** – An error has occurred with the replication\. Check the **Replication Error** field in the Amazon RDS console or the event log to determine the exact error\. For more information about troubleshooting a replication error, see [Troubleshooting a MySQL Read Replica Problem](USER_MySQL.Replication.ReadReplicas.md#USER_ReadRepl.Troubleshooting)\.
+ ****terminated** \(MariaDB, MySQL, or PostgreSQL only\)** – Replication is terminated\. This occurs if replication is stopped for more than 30 consecutive days, either manually or due to a replication error\. In this case, Amazon RDS terminates replication between the primary DB instance and all read replicas\. Amazon RDS does this to prevent increased storage requirements on the source DB instance and long failover times\.

  Broken replication can affect storage because the logs can grow in size and number due to the high volume of errors messages being written to the log\. Broken replication can also affect failure recovery due to the time Amazon RDS requires to maintain and process the large number of logs during recovery\.
+ ****stopped** \(MariaDB or MySQL only\)** – Replication has stopped because of a customer\-initiated request\.
+ ****replication stop point set** \(MySQL only\)** – A customer\-initiated stop point was set using the [mysql\.rds\_start\_replication\_until](mysql_rds_start_replication_until.md) stored procedure and the replication is in progress\.
+ ****replication stop point reached** \(MySQL only\)** – A customer\-initiated stop point was set using the [mysql\.rds\_start\_replication\_until](mysql_rds_start_replication_until.md) stored procedure and replication is stopped because the stop point was reached\.

### Monitoring Replication Lag<a name="USER_ReadRepl.Monitoring.Lag"></a>

You can monitor replication lag in Amazon CloudWatch by viewing the Amazon RDS `ReplicaLag` metric\.

For MySQL and MariaDB, the `ReplicaLag` metric reports the value of the `Seconds_Behind_Master` field of the `SHOW SLAVE STATUS` command\. Common causes for replication lag for MySQL and MariaDB are the following:
+ A network outage\.
+ Writing to tables with indexes on a read replica\. If the `read_only` parameter is not set to 0 on the read replica, it can break replication\.
+ Using a nontransactional storage engine such as MyISAM\. Replication is only supported for the InnoDB storage engine on MySQL and the XtraDB storage engine on MariaDB\.

When the `ReplicaLag` metric reaches 0, the replica has caught up to the primary DB instance\. If the `ReplicaLag` metric returns `-1`, then replication is currently not active\. `ReplicaLag = -1` is equivalent to `Seconds_Behind_Master = NULL`\.

For Oracle, the `ReplicaLag` metric is the sum of the `Apply Lag` value and the difference between the current time and the apply lag's `DATUM_TIME` value\. The `DATUM_TIME` value is the last time the read replica received data from its source DB instance\. For more information, see [ V$DATAGUARD\_STATS](https://docs.oracle.com/database/121/REFRN/GUID-B346DD88-3F5E-4F16-9DEE-2FDE62B1ABF7.htm#REFRN30413) in the Oracle documentation\.

For SQL Server, the `ReplicaLag` metric is the maximum lag of databases that have fallen behind, in seconds\. For example, if you have two databases that lag 5 seconds and 10 seconds, respectively, then `ReplicaLag` is 10 seconds\. The `ReplicaLag` metric returns the value of the following query\.

```
select ag.name name, MAX(hdrs.secondary_lag_seconds) max_lag from sys.dm_hadr_database_replica_state
```

For more information, see [secondary\_lag\_seconds](https://docs.microsoft.com/en-us/sql/relational-databases/system-dynamic-management-views/sys-dm-hadr-database-replica-states-transact-sql) in the Microsoft documentation\.

`ReplicaLag` returns `-1` if RDS can't determine the lag, such as during replica setup, or when the read replica is in the `error` state\.

**Note**  
New databases aren't included in the lag calculation until they are accessible on the read replica\.

For PostgreSQL, the `ReplicaLag` metric returns the value of the following query\.

```
SELECT extract(epoch from now() - pg_last_xact_replay_timestamp()) AS slave_lag
```

PostgreSQL versions 9\.5\.2 and later use physical replication slots to manage write ahead log \(WAL\) retention on the source instance\. For each cross\-Region read replica instance, Amazon RDS creates a physical replication slot and associates it with the instance\. Two Amazon CloudWatch metrics, `Oldest Replication Slot Lag` and `Transaction Logs Disk Usage`, show how far behind the most lagging replica is in terms of WAL data received and how much storage is being used for WAL data\. The `Transaction Logs Disk Usage` value can substantially increase when a cross\-Region read replica is lagging significantly\.

For more information about monitoring a DB instance with CloudWatch, see [Monitoring with Amazon CloudWatch](MonitoringOverview.md#monitoring-cloudwatch)\.