# Exporting Data from a MySQL DB Instance by Using Replication<a name="MySQL.Procedural.Exporting.NonRDSRepl"></a>

You can use replication to export data from a MySQL 5\.6 or later DB instance to a MySQL instance running external to Amazon RDS\. The MySQL instance external to Amazon RDS can be running either on\-premises in your data center, or on an Amazon EC2 instance\. The MySQL DB instance must be running version 5\.6\.13 or later\. The MySQL instance external to Amazon RDS must be running the same version as the Amazon RDS instance, or a later version\.

Replication to an instance of MySQL running external to Amazon RDS is only supported during the time it takes to export a database from a MySQL DB instance\. The replication should be terminated when the data has been exported and applications can start accessing the external instance\.

The following list shows the steps to take\. Each step is discussed in more detail in later sections\.

1. Prepare an instance of MySQL running external to Amazon RDS\.

1. Configure the MySQL DB instance to be the replication source\.

1. Use `mysqldump` to transfer the database from the Amazon RDS instance to the instance external to Amazon RDS\.

1. Start replication to the instance running external to Amazon RDS\.

1. After the export completes, stop replication\.

## Prepare an Instance of MySQL External to Amazon RDS<a name="MySQL.Procedural.Exporting.NonRDSRepl.PrepareRDS"></a>

Install an instance of MySQL external to Amazon RDS\.

Connect to the instance as the master user, and create the users required to support the administrators, applications, and services that access the instance\.

Follow the directions in the MySQL documentation to prepare the instance of MySQL running external to Amazon RDS as a replica\. For more information, see [Setting the Replication Slave Configuration](http://dev.mysql.com/doc/refman/5.6/en/replication-howto-slavebaseconfig.html)\.

Configure an egress rule for the external instance to operate as a Read Replica during the export\. The egress rule will allow the MySQL Read Replica to connect to the MySQL DB instance during replication\. Specify an egress rule that allows TCP connections to the port and IP address of the source Amazon RDS MySQL DB instance\.

If the Read Replica is running in an Amazon EC2 instance in an Amazon VPC, specify the egress rules in a VPC security group\. If the Read Replica is running in an Amazon EC2 instance that is not in a VPC, specify the egress rule in an Amazon EC2 security group\. If the Read Replica is installed on\-premises, specify the egress rule in a firewall\.

If the Read Replica is running in a VPC, configure VPC ACL rules in addition to the security group egress rule\. For more information about Amazon VPC network ACLs, see [Network ACLs](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_ACLs.html)\.
+ ACL ingress rule allowing TCP traffic to ports 1024\-65535 from the IP address of the source MySQL DB instance\.
+ ACL egress rule: allowing outbound TCP traffic to the port and IP address of the source MySQL DB instance\.

## Prepare the Replication Source<a name="MySQL.Procedural.Exporting.NonRDSRepl.PrepareSource"></a>

Prepare the MySQL DB instance as the replication source\.

Ensure your client computer has enough disk space available to save the binary logs while setting up replication\.

Create a replication account by following the directions in [Creating a User For Replication](http://dev.mysql.com/doc/refman/5.6/en/replication-howto-repuser.html)\.

Configure ingress rules on the system running the replication source MySQL DB instance that will allow the external MySQL Read Replica to connect during replication\. Specify an ingress rule that allows TCP connections to the port used by the Amazon RDS instance from the IP address of the MySQL Read Replica running external to Amazon RDS\.

If the Amazon RDS instance is running in a VPC, specify the ingress rules in a VPC security group\. If the Amazon RDS instance is not running in an in a VPC, specify the ingress rules in a database security group\.

If the Amazon RDS instance is running in a VPC, configure VPC ACL rules in addition to the security group ingress rule\. For more information about Amazon VPC network ACLs, see [Network ACLs](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_ACLs.html)\.
+ ACL ingress rule: allow TCP connections to the port used by the Amazon RDS instance from the IP address of the external MySQL Read Replica\.
+ ACL egress rule: allow TCP connections from ports 1024\-65535 to the IP address of the external MySQL Read Replica\.

Ensure that the backup retention period is set long enough that no binary logs are purged during the export\. If any of the logs are purged before the export is complete, you must restart replication from the beginning\. For more information about setting the backup retention period, see [Working With Backups](USER_WorkingWithAutomatedBackups.md)\.

Use the `mysql.rds_set_configuration` stored procedure to set the binary log retention period long enough that the binary logs are not purged during the export\. For more information, see [Accessing MySQL Binary Logs](USER_LogAccess.Concepts.MySQL.md#USER_LogAccess.MySQL.Binarylog)\.

To further ensure that the binary logs of the source instance are not purged, create an Amazon RDS Read Replica from the source instance\. For more information, see [Creating a Read Replica](USER_ReadRepl.md#USER_ReadRepl.Create)\. After the Amazon RDS Read Replica has been created, call the `mysql.rds_stop_replication` stored procedure to stop the replication process\. The source instance will no longer purge its binary log files, so they will be available for the replication process\.

## Copy the Database<a name="MySQL.Procedural.Exporting.NonRDSRepl.CopyData"></a>

Run the MySQL `SHOW SLAVE STATUS` statement on the RDS read replica, and note the values for the following:
+  `master_host`
+ `master_port`
+ `master_log_file`
+ `exec_master_log_pos`

Use the `mysqldump` utility to create a snapshot, which copies the data from Amazon RDS to your local client computer\. Then run another utility to load the data into the MySQL instance running external to RDS\. Ensure your client computer has enough space to hold the `mysqldump` files from the databases to be replicated\. This process can take several hours for very large databases\. Follow the directions in [Creating a Dump Snapshot Using mysqldump](http://dev.mysql.com/doc/refman/5.6/en/replication-howto-mysqldump.html)\.

The following example shows how to run `mysqldump` on a client, and then pipe the dump into the `mysql` client utility, which loads the data into the external MySQL instance\.

For Linux, OS X, or Unix:

```
mysqldump -h RDS instance endpoint \
    -u user \
    -p password \
    --port=3306 \
    --single-transaction \
    --routines \
    --triggers \
    --databases  database database2 \
    --compress  \
    --compact | mysql \
        -h MySQL host \
        -u master user \
        -p password \
        --port 3306
```

For Windows:

```
mysqldump -h RDS instance endpoint ^
    -u user ^
    -p password ^
    --port=3306 ^
    --single-transaction ^
    --routines ^
    --triggers ^
    --databases  database database2 ^
    --compress  ^
    --compact | mysql ^
        -h MySQL host ^
        -u master user ^
        -p password ^
        --port 3306
```

The following example shows how to run `mysqldump` on a client and write the dump to a file\.

For Linux, OS X, or Unix:

```
mysqldump -h RDS instance endpoint \
    -u user \
    -p password \
    --port=3306 \
    --single-transaction \
    --routines \
    --triggers \
    --databases  database database2 > path/rds-dump.sql
```

For Windows:

```
mysqldump -h RDS instance endpoint ^
    -u user ^
    -p password ^
    --port=3306 ^
    --single-transaction ^
    --routines ^
    --triggers ^
    --databases  database database2 > path\rds-dump.sql
```

## Complete the Export<a name="MySQL.Procedural.Exporting.NonRDSRepl.CompleteExp"></a>

After you have loaded the `mysqldump` files to create the databases on the MySQL instance running external to Amazon RDS, start replication from the source MySQL DB instance to export all source changes that have occurred after you stopped replication from the Amazon RDS Read Replica\.

Use the MySQL `CHANGE MASTER` statement to configure the external MySQL instance\. Specify the ID and password of the user granted REPLICATION SLAVE permissions\. Specify the `master_host`, `master_port`, `relay_master_log_file` and `exec_master_log_pos` values you got from the Mysql `SHOW SLAVE STATUS` statement you ran on the RDS Read Replica\. For more information, see [Setting the Master Configuration on the Slave](http://dev.mysql.com/doc/refman/5.6/en/replication-howto-slaveinit.html)\.

Use the MySQL `START SLAVE` command to initiate replication from the source MySQL DB instance and the MySQL replica\.

Run the MySQL `SHOW SLAVE STATUS` command on the Amazon RDS instance to verify that it is operating as a Read Replica\. For more information about interpreting the results, see [SHOW SLAVE STATUS Syntax](http://dev.mysql.com/doc/refman/5.6/en/show-slave-status.html)\.

After replication on the MySQL instance has caught up with the Amazon RDS source, use the MySQL `STOP SLAVE` command to terminate replication from the source MySQL DB instance\.

On the Amazon RDS Read Replica, call the `mysql.rds_start_replication` stored procedure\. This will allow Amazon RDS to start purging the binary log files from the source MySQL DB instance\.

## Related Topics<a name="MySQL.Procedural.Exporting.Related"></a>
+ [Restoring a Backup into an Amazon RDS MySQL DB Instance](MySQL.Procedural.Importing.md)
+ [Backing Up and Restoring Amazon RDS DB Instances](CHAP_CommonTasks.BackupRestore.md)