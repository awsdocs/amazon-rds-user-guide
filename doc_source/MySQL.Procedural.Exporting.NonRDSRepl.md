# Exporting Data from a MySQL DB Instance by Using Replication<a name="MySQL.Procedural.Exporting.NonRDSRepl"></a>

To export data from a MySQL 5\.6 or later DB instance to a MySQL instance running external to Amazon RDS, you can use replication\. In this scenario, the Amazon RDS MySQL DB instance is the *source MySQL DB instance*, and the MySQL instance running external to Amazon RDS is the *external MySQL database*\.

The source MySQL DB instance must be running version 5\.6\.13 or later\. The external MySQL database can run either on\-premises in your data center, or on an Amazon EC2 instance\. The external MySQL database must run the same version as the source MySQL DB instance, or a later version\.

Replication to an external MySQL database is only supported during the time it takes to export a database from the source MySQL DB instance\. The replication should be terminated when the data has been exported and applications can start accessing the external MySQL instance\.

The following list shows the steps to take\. Each step is discussed in more detail in later sections\.

1. Prepare an external MySQL DB instance\.

1. Prepare the source MySQL DB instance for replication\.

1. Use the mysqldump utility to transfer the database from the source MySQL DB instance to the external MySQL database\.

1. Start replication to the external MySQL database\.

1. After the export completes, stop replication\.

## Prepare an External MySQL Database<a name="MySQL.Procedural.Exporting.NonRDSRepl.PrepareRDS"></a>

Perform the following steps to prepare the external MySQL database\.

**To prepare the external MySQL database**

1. Install the external MySQL database\.

1. Connect to the external MySQL database as the master user\. Then create the users required to support the administrators, applications, and services that access the database\.

1. Follow the directions in the MySQL documentation to prepare the external MySQL database as a replica\. For more information, see [the MySQL documentation](https://dev.mysql.com/doc/refman/8.0/en/replication-howto-slavebaseconfig.html)\.

1. Configure an egress rule for the external MySQL database to operate as a read replica during the export\. The egress rule allows the external MySQL database to connect to the source MySQL DB instance during replication\. Specify an egress rule that allows Transmission Control Protocol \(TCP\) connections to the port and IP address of the source MySQL DB instance\.

   Specify the appropriate egress rules for your environment:
   + If the external MySQL database is running in an Amazon EC2 instance in a virtual private cloud \(VPC\) based on the Amazon VPC service, specify the egress rules in a VPC security group\. For more information, see [Controlling Access with Security Groups](Overview.RDSSecurityGroups.md)\.
   + If the external MySQL database is running in an Amazon EC2 instance that is not in a VPC, specify the egress rules in an EC2\-Classic security group\.
   + If the external MySQL database is installed on\-premises, specify the egress rules in a firewall\.

1. If the external MySQL database is running in a VPC, configure rules for the VPC access control list \(ACL\) rules in addition to the security group egress rule: 
   + Configure an ACL ingress rule allowing TCP traffic to ports 1024–65535 from the IP address of the source MySQL DB instance\.
   + Configure an ACL egress rule allowing outbound TCP traffic to the port and IP address of the source MySQL DB instance\.

   For more information about Amazon VPC network ACLs, see [Network ACLs](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-network-acls.html) in *Amazon VPC User Guide\.*

1. \(Optional\) Set the `max_allowed_packet` parameter to the maximum size to avoid replication errors\. We recommend this setting\.

## Prepare the Source MySQL DB Instance<a name="MySQL.Procedural.Exporting.NonRDSRepl.PrepareSource"></a>

Perform the following steps to prepare the source MySQL DB instance as the replication source\.

**To prepare the source MySQL DB instance**

1. Ensure that your client computer has enough disk space available to save the binary logs while setting up replication\.

1. Connect to the source MySQL DB instance, and create a replication account by following the directions in [Creating a User for Replication](http://dev.mysql.com/doc/refman/8.0/en/replication-howto-repuser.html) in the MySQL documentation\.

1. Configure ingress rules on the system running the source MySQL DB instance to allow the external MySQL database to connect during replication\. Specify an ingress rule that allows TCP connections to the port used by the source MySQL DB instance from the IP address of the external MySQL database\.

1. Specify the egress rules:
   + If the source MySQL DB instance is running in a VPC, specify the ingress rules in a VPC security group\. For more information, see [Controlling Access with Security Groups](Overview.RDSSecurityGroups.md)\.
   + If the source MySQL DB instance isn't running in a VPC, specify the ingress rules in a DB security group\. For more information, see [Authorizing Network Access to a DB Security Group from an IP Range](USER_WorkingWithSecurityGroups.md#USER_WorkingWithSecurityGroups.Authorizing)\.

1. If source MySQL DB instance is running in a VPC, configure VPC ACL rules in addition to the security group ingress rule:
   + Configure an ACL ingress rule to allow TCP connections to the port used by the Amazon RDS instance from the IP address of the external MySQL database\.
   + Configure an ACL egress rule to allow TCP connections from ports 1024–65535 to the IP address of the external MySQL database\.

   For more information about Amazon VPC network ACLs, see [Network ACLs](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-network-acls.html) in the *Amazon VPC User Guide\.*

1. Ensure that the backup retention period is set long enough that no binary logs are purged during the export\. If any of the logs are purged before the export has completed, you must restart replication from the beginning\. For more information about setting the backup retention period, see [Working With Backups](USER_WorkingWithAutomatedBackups.md)\.

1. Use the `mysql.rds_set_configuration` stored procedure to set the binary log retention period long enough that the binary logs aren't purged during the export\. For more information, see [Accessing MySQL Binary Logs](USER_LogAccess.Concepts.MySQL.md#USER_LogAccess.MySQL.Binarylog)\.

1. Create an Amazon RDS read replica from the source MySQL DB instance to further ensure that the binary logs of the source MySQL DB instance are not purged\. For more information, see [Creating a Read Replica](USER_ReadRepl.md#USER_ReadRepl.Create)\.

1. After the Amazon RDS read replica has been created, call the `mysql.rds_stop_replication` stored procedure to stop the replication process\. The source MySQL DB instance no longer purges its binary log files, so they are available for the replication process\.

1. \(Optional\) Set both the `max_allowed_packet` parameter and the `slave_max_allowed_packet` parameter to the maximum size to avoid replication errors\. The maximum size for both parameters is 1 GB\. We recommend this setting for both parameters\. For information about setting parameters, see [Modifying Parameters in a DB Parameter Group](USER_WorkingWithParamGroups.md#USER_WorkingWithParamGroups.Modifying)\.

## Copy the Database<a name="MySQL.Procedural.Exporting.NonRDSRepl.CopyData"></a>

Perform the following steps to copy the database\.

**To copy the database**

1. Connect to the RDS read replica of the source MySQL DB instance, and run the MySQL `SHOW SLAVE STATUS\G` statement\. Note the values for the following:
   + `Master_Host`
   + `Master_Port`
   + `Master_Log_File`
   + `Exec_Master_Log_Pos`

1. Use the mysqldump utility to create a snapshot, which copies the data from Amazon RDS to your local client computer\. Then run another utility to load the data into the external MySQL database\. Ensure that your client computer has enough space to hold the `mysqldump` files from the databases to be replicated\. This process can take several hours for very large databases\. Follow the directions in [Creating a Data Snapshot Using mysqldump](https://dev.mysql.com/doc/mysql-replication-excerpt/8.0/en/replication-howto-mysqldump.html) in the MySQL documentation\.

   The following example runs `mysqldump` on a client, and then pipes the dump into the `mysql` client utility, which loads the data into the external MySQL database\.

   For Linux, macOS, or Unix:

   ```
   mysqldump -h source_MySQL_DB_instance_endpoint \
       -u user \
       -ppassword \
       --port=3306 \
       --single-transaction \
       --routines \
       --triggers \
       --databases  database database2 \
       --compress  \
       --port 3306
   ```

   For Windows:

   ```
   mysqldump -h source_MySQL_DB_instance_endpoint ^
       -u user ^
       -ppassword ^
       --port=3306 ^
       --single-transaction ^
       --routines ^
       --triggers ^
       --databases  database database2 ^
       --compress  ^
       --port 3306
   ```

   The following example runs `mysqldump` on a client and writes the dump to a file\.

   For Linux, macOS, or Unix:

   ```
   mysqldump -h source_MySQL_DB_instance_endpoint \
       -u user \
       -ppassword \
       --port=3306 \
       --single-transaction \
       --routines \
       --triggers \
       --databases  database database2 > path/rds-dump.sql
   ```

   For Windows:

   ```
   mysqldump -h source_MySQL_DB_instance_endpoint ^
       -u user ^
       -ppassword ^
       --port=3306 ^
       --single-transaction ^
       --routines ^
       --triggers ^
       --databases  database database2 > path\rds-dump.sql
   ```

## Complete the Export<a name="MySQL.Procedural.Exporting.NonRDSRepl.CompleteExp"></a>

Perform the following steps to complete the export\.

**To complete the export**

1. Load the mysqldump files to create the databases on the external MySQL database\.

1. On the Amazon RDS read replica, call the `mysql.rds_start_replication` stored procedure\. Doing this starts replication from the source MySQL DB instance and exports all source changes that have occurred after you stopped replication from the Amazon RDS read replica\.

1. Use the MySQL `CHANGE MASTER` statement to configure the external MySQL database\. Specify the ID and password of the user granted `REPLICATION SLAVE` permissions\. Specify the `Master_Host`, `Master_Port`, `Relay_Master_Log_File`, and `Exec_Master_Log_Pos` values that you got from the MySQL `SHOW SLAVE STATUS\G` statement that you ran on the RDS read replica\. For more information, see [the MySQL documentation](https://dev.mysql.com/doc/refman/8.0/en/change-master-to.html)\.

1. Use the MySQL `START SLAVE` command to initiate replication from the source MySQL DB instance to the external MySQL database\.

1. Run the MySQL `SHOW SLAVE STATUS\G` command on the external MySQL database to verify that it is operating as a read replica\. For more information about interpreting the results, see [the MySQL documentation](https://dev.mysql.com/doc/refman/8.0/en/show-slave-status.html)\.

1. After replication on the external MySQL database has caught up with the source MySQL DB instance, use the MySQL `STOP SLAVE` command to stop replication from the source MySQL DB instance\.

1. On the Amazon RDS read replica, call the `mysql.rds_start_replication` stored procedure\. Doing this allows Amazon RDS to start purging the binary log files from the source MySQL DB instance\.