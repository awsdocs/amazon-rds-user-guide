# Configuring binary log file position replication with an external source instance<a name="MySQL.Procedural.Importing.External.ReplMariaDB"></a>

You can set up replication between an RDS for MySQL or MariaDB DB instance and a MySQL or MariaDB instance that is external to Amazon RDS using binary log file replication\.

**Topics**
+ [Before you begin](#MySQL.Procedural.Importing.External.Repl.BeforeYouBegin)
+ [Configuring binary log file position replication with an external source instance](#MySQL.Procedural.Importing.External.Repl.Procedure)

## Before you begin<a name="MySQL.Procedural.Importing.External.Repl.BeforeYouBegin"></a>

You can configure replication using the binary log file position of replicated transactions\.

The permissions required to start replication on an Amazon RDS DB instance are restricted and not available to your Amazon RDS master user\. Because of this, make sure that you use the Amazon RDS [mysql\.rds\_set\_external\_master](mysql_rds_set_external_master.md) and [mysql\.rds\_start\_replication](mysql_rds_start_replication.md) commands to set up replication between your live database and your Amazon RDS database\.

To set the binary logging format for a MySQL or MariaDB database, update the `binlog_format` parameter\. If your DB instance uses the default DB instance parameter group, create a new DB parameter group to modify `binlog_format` settings\. We recommend that you use the default setting for `binlog_format`, which is `MIXED`\. However, you can also set `binlog_format` to `ROW` or `STATEMENT` if you need a specific binary log \(binlog\) format\. Reboot your DB instance for the change to take effect\.

For information about setting the `binlog_format` parameter, see [Configuring MySQL binary logging](USER_LogAccess.MySQL.BinaryFormat.md)\. For information about the implications of different MySQL replication types, see [Advantages and disadvantages of statement\-based and row\-based replication](https://dev.mysql.com/doc/refman/8.0/en/replication-sbr-rbr.html) in the MySQL documentation\.

## Configuring binary log file position replication with an external source instance<a name="MySQL.Procedural.Importing.External.Repl.Procedure"></a>

Follow these guidelines when you set up an external source instance and a replica on Amazon RDS: 
+ Monitor failover events for the Amazon RDS DB instance that is your replica\. If a failover occurs, then the DB instance that is your replica might be recreated on a new host with a different network address\. For information on how to monitor failover events, see [Working with Amazon RDS event notification](USER_Events.md)\.
+ Maintain the binlogs on your source instance until you have verified that they have been applied to the replica\. This maintenance makes sure that you can restore your source instance in the event of a failure\.
+ Turn on automated backups on your Amazon RDS DB instance\. Turning on automated backups makes sure that you can restore your replica to a particular point in time if you need to re\-synchronize your source instance and replica\. For information on backups and point\-in\-time restore, see [Backing up and restoring an Amazon RDS DB instance](CHAP_CommonTasks.BackupRestore.md)\.

**To configure binary log file replication with an external source instance**

1. Make the source MySQL or MariaDB instance read\-only\.

   ```
   mysql> FLUSH TABLES WITH READ LOCK;
   mysql> SET GLOBAL read_only = ON;
   ```

1. Run the `SHOW MASTER STATUS` command on the source MySQL or MariaDB instance to determine the binlog location\.

   You receive output similar to the following example\.

   ```
   File                        Position  
   ------------------------------------
    mysql-bin-changelog.000031      107   
   ------------------------------------
   ```

1. Copy the database from the external instance to the Amazon RDS DB instance using `mysqldump`\. For very large databases, you might want to use the procedure in [Importing data to an Amazon RDS MariaDB or MySQL database with reduced downtime](MySQL.Procedural.Importing.NonRDSRepl.md)\. 

   For Linux, macOS, or Unix:

   ```
   mysqldump --databases database_name \
       --single-transaction \
       --compress \
       --order-by-primary \
       -u local_user \
       -plocal_password | mysql \
           --host=hostname \
           --port=3306 \
           -u RDS_user_name \
           -pRDS_password
   ```

   For Windows:

   ```
   mysqldump --databases database_name ^
       --single-transaction ^
       --compress ^
       --order-by-primary ^
       -u local_user ^
       -plocal_password | mysql ^
           --host=hostname ^
           --port=3306 ^
           -u RDS_user_name ^
           -pRDS_password
   ```
**Note**  
Make sure that there isn't a space between the `-p` option and the entered password\. 

   To specify the host name, user name, port, and password to connect to your Amazon RDS DB instance, use the `--host`, `--user (-u)`, `--port` and `-p` options in the `mysql` command\. The host name is the Domain Name Service \(DNS\) name from the Amazon RDS DB instance endpoint, for example `myinstance.123456789012.us-east-1.rds.amazonaws.com`\. You can find the endpoint value in the instance details in the AWS Management Console\.

1. Make the source MySQL or MariaDB instance writeable again\.

   ```
   mysql> SET GLOBAL read_only = OFF;
   mysql> UNLOCK TABLES;
   ```

   For more information on making backups for use with replication, see [the MySQL documentation](https://dev.mysql.com/doc/refman/8.0/en/replication-solutions-backups-read-only.html)\.

1. In the AWS Management Console, add the IP address of the server that hosts the external database to the virtual private cloud \(VPC\) security group for the Amazon RDS DB instance\. For more information on modifying a VPC security group, see [Security groups for your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html) in the *Amazon Virtual Private Cloud User Guide*\. 

   The IP address can change when the following conditions are met:
   + You are using a public IP address for communication between the external source instance and the DB instance\.
   + The external source instance was stopped and restarted\.

   If these conditions are met, verify the IP address before adding it\.

   You might also need to configure your local network to permit connections from the IP address of your Amazon RDS DB instance\. You do this so that your local network can communicate with your external MySQL or MariaDB instance\. To find the IP address of the Amazon RDS DB instance, use the `host` command\.

   ```
   host db_instance_endpoint
   ```

   The host name is the DNS name from the Amazon RDS DB instance endpoint\.

1. Using the client of your choice, connect to the external instance and create a user to use for replication\. Use this account solely for replication and restrict it to your domain to improve security\. The following is an example\. 

   ```
   CREATE USER 'repl_user'@'mydomain.com' IDENTIFIED BY 'password';
   ```

1. For the external instance, grant `REPLICATION CLIENT` and `REPLICATION SLAVE` privileges to your replication user\. For example, to grant the `REPLICATION CLIENT` and `REPLICATION SLAVE` privileges on all databases for the '`repl_user`' user for your domain, issue the following command\.

   ```
   GRANT REPLICATION CLIENT, REPLICATION SLAVE ON *.* TO 'repl_user'@'mydomain.com';
   ```

1. Make the Amazon RDS DB instance the replica\. To do so, first connect to the Amazon RDS DB instance as the master user\. Then identify the external MySQL or MariaDB database as the source instance by using the [mysql\.rds\_set\_external\_master](mysql_rds_set_external_master.md) command\. Use the master log file name and master log position that you determined in step 2\. The following is an example\. 

   ```
   CALL mysql.rds_set_external_master ('mymasterserver.mydomain.com', 3306, 'repl_user', 'password', 'mysql-bin-changelog.000031', 107, 0);
   ```
**Note**  
On RDS for MySQL, you can choose to use delayed replication by running the [mysql\.rds\_set\_external\_master\_with\_delay](mysql_rds_set_external_master_with_delay.md) stored procedure instead\. On RDS for MySQL, one reason to use delayed replication is to turn on disaster recovery with the [mysql\.rds\_start\_replication\_until](mysql_rds_start_replication_until.md) stored procedure\. Currently, RDS for MariaDB supports delayed replication but doesn't support the `mysql.rds_start_replication_until` procedure\.

1. On the Amazon RDS DB instance, issue the [mysql\.rds\_start\_replication](mysql_rds_start_replication.md) command to start replication\.

   ```
   CALL mysql.rds_start_replication;
   ```