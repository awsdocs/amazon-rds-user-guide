# Replication with a MySQL or MariaDB Instance Running External to Amazon RDS<a name="MySQL.Procedural.Importing.External.Repl"></a>

You can set up replication between an Amazon RDS MySQL or MariaDB DB instance and a MySQL or MariaDB instance that is external to Amazon RDS\.

**Topics**
+ [Before You Begin](#MySQL.Procedural.Importing.External.Repl.BeforeYouBegin)
+ [Configuring Binary Log File Position Replication with an External Source Instance](#MySQL.Procedural.Importing.External.Repl.Procedure)
+ [Configuring GTID\-Based Replication with an External Source Instance](#MySQL.Procedural.Importing.External.Repl.GTIDProcedure)

## Before You Begin<a name="MySQL.Procedural.Importing.External.Repl.BeforeYouBegin"></a>

You can configure replication using the binary log file position of replicated transactions\. On Amazon RDS MySQL 5\.7\.23 and later MySQL 5\.7 versions, you can also configure replication using global transaction identifiers \(GTIDs\)\.

The permissions required to start replication on an Amazon RDS DB instance are restricted and not available to your Amazon RDS master user\. Because of this, make sure that you use the Amazon RDS [mysql\.rds\_set\_external\_master](mysql_rds_set_external_master.md) and [mysql\.rds\_start\_replication](mysql_rds_start_replication.md) commands to set up replication between your live database and your Amazon RDS database\.

To set the binary logging format for a MySQL or MariaDB database, update the `binlog_format` parameter\. If your DB instance uses the default DB instance parameter group, create a new DB parameter group to modify `binlog_format` settings\. We recommend that you use the default setting for `binlog_format`, which is `MIXED`\. However, you can also set `binlog_format` to `ROW` or `STATEMENT` if you need a specific binlog format\. Reboot your DB instance for the change to take effect\.

For information about setting the `binlog_format` parameter, see [Binary Logging Format](USER_LogAccess.Concepts.MySQL.md#USER_LogAccess.MySQL.BinaryFormat)\. For information about the implications of different MySQL replication types, see [Advantages and Disadvantages of Statement\-Based and Row\-Based Replication](https://dev.mysql.com/doc/refman/8.0/en/replication-sbr-rbr.html) in the MySQL documentation\.

**Note**  
Use the procedure in this topic to configure replication in all cases except when the external instance is MariaDB version 10\.0\.2 or greater and the Amazon RDS instance is MariaDB\. In that case, use the procedure at [Configuring GTID\-Based Replication into an Amazon RDS MariaDB DB instance](MariaDB.Procedural.Replication.GTID.md) to set up GTID\-based replication\.

## Configuring Binary Log File Position Replication with an External Source Instance<a name="MySQL.Procedural.Importing.External.Repl.Procedure"></a>

Follow these guidelines when you set up an external source instance and a replica on Amazon RDS: 
+ Monitor failover events for the Amazon RDS DB instance that is your replica\. If a failover occurs, then the DB instance that is your replica might be recreated on a new host with a different network address\. For information on how to monitor failover events, see [Using Amazon RDS Event Notification](USER_Events.md)\.
+ Maintain the binary logs \(binlogs\) on your source instance until you have verified that they have been applied to the replica\. This maintenance makes sure that you can restore your source instance in the event of a failure\.
+ Turn on automated backups on your Amazon RDS DB instance\. Turning on automated backups makes sure that you can restore your replica to a particular point in time if you need to re\-synchronize your source instance and replica\. For information on backups and point\-in\-time restore, see [Backing Up and Restoring an Amazon RDS DB Instance](CHAP_CommonTasks.BackupRestore.md)\.

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

1. Copy the database from the external instance to the Amazon RDS DB instance using `mysqldump`\. For very large databases, you might want to use the procedure in [Importing Data to an Amazon RDS MySQL or MariaDB DB Instance with Reduced Downtime](MySQL.Procedural.Importing.NonRDSRepl.md)\. 

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

   To specify the host name, user name, port, and password to connect to your Amazon RDS DB instance, use the `--host`, `--user (-u)`, `--port` and `-p` options in the `mysql` command\. The host name is the Domain Name Service \(DNS\) name from the Amazon RDS DB instance endpoint, for example, `myinstance.123456789012.us-east-1.rds.amazonaws.com`\. You can find the endpoint value in the instance details in the AWS Management Console\.

1. Make the source MySQL or MariaDB instance writeable again\.

   ```
   mysql> SET GLOBAL read_only = OFF;
   mysql> UNLOCK TABLES;
   ```

   For more information on making backups for use with replication, see [the MySQL documentation](https://dev.mysql.com/doc/refman/8.0/en/replication-solutions-backups-read-only.html)\.

1. In the AWS Management Console, add the IP address of the server that hosts the external database to the VPC security group for the Amazon RDS DB instance\. For more information on modifying a VPC security group, see [Security Groups for Your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html) in the *Amazon Virtual Private Cloud User Guide*\. 

   The IP address can change when the following conditions are met:
   + You are using a public IP address for communication between the external source instance and the DB instance\.
   + The external source instance was stopped and restarted\.

   If these conditions are met, verify the IP address before adding it\.

   You might also need to configure your local network to permit connections from the IP address of your Amazon RDS DB instance\. You do this so that your local network can communicate with your external MySQL or MariaDB instance\. To find the IP address of the Amazon RDS DB instance, use the `host` command\.

   ```
   host db_instance_endpoint
   ```

   The host name is the DNS name from the Amazon RDS DB instance endpoint\.

1. Using the client of your choice, connect to the external instance and create a user to use for replication\. Use this account solely for replication\. and restrict it to your domain to improve security\. The following is an example\. 

   ```
   CREATE USER 'repl_user'@'mydomain.com' IDENTIFIED BY 'password';
   ```

1. For the external instance, grant `REPLICATION CLIENT` and `REPLICATION SLAVE` privileges to your replication user\. For example, to grant the `REPLICATION CLIENT` and `REPLICATION SLAVE` privileges on all databases for the '`repl_user`' user for your domain, issue the following command\.

   ```
   GRANT REPLICATION CLIENT, REPLICATION SLAVE ON *.* TO 'repl_user'@'mydomain.com' IDENTIFIED BY 'password'; 
   ```

1. Make the Amazon RDS DB instance the replica\. To do so, first connect to the Amazon RDS DB instance as the master user\. Then identify the external MySQL or MariaDB database as the source instance by using the [mysql\.rds\_set\_external\_master](mysql_rds_set_external_master.md) command\. Use the master log file name and master log position that you determined in step 2\. The following is an example\. 

   ```
   CALL mysql.rds_set_external_master ('mymasterserver.mydomain.com', 3306, 'repl_user', 'password', 'mysql-bin-changelog.000031', 107, 0);
   ```
**Note**  
On Amazon RDS MySQL, you can choose to use delayed replication by running the [mysql\.rds\_set\_external\_master\_with\_delay](mysql_rds_set_external_master_with_delay.md) stored procedure instead\. One reason to use delayed replication is to enable disaster recovery with the [mysql\.rds\_start\_replication\_until](mysql_rds_start_replication_until.md) stored procedure\. Currently, delayed replication is not supported on Amazon RDS MariaDB\.

1. On the Amazon RDS DB instance, issue the [mysql\.rds\_start\_replication](mysql_rds_start_replication.md) command to start replication:

   ```
   CALL mysql.rds_start_replication;
   ```

## Configuring GTID\-Based Replication with an External Source Instance<a name="MySQL.Procedural.Importing.External.Repl.GTIDProcedure"></a>

When you set up an external source instance and a replica on Amazon RDS, monitor failover events for the Amazon RDS DB instance that is your replica\. If a failover occurs, then the DB instance that is your replica might be recreated on a new host with a different network address\. For information on how to monitor failover events, see [Using Amazon RDS Event Notification](USER_Events.md)\.

**Important**  
GTID\-based replication is only supported on Amazon RDS MySQL version 5\.7\.23 and later MySQL 5\.7 versions\. GTID\-based replication is not supported for Amazon RDS MySQL 5\.5, 5\.6, or 8\.0\.

**To configure GTID\-based replication with an external source instance**

1. Prepare for GTID\-based replication:

   1. Make sure that the external MySQL or MariaDB database has GTID\-based replication enabled\. To do so, make sure that the external database has the following parameters set to the specified values:

      `gtid_mode` – `ON`

      `enforce_gtid_consistency` – `ON`

      For more information, see [ Replication with Global Transaction Identifiers](https://dev.mysql.com/doc/refman/8.0/en/replication-gtids.html) in the MySQL documentation or [ Global Transaction ID](https://mariadb.com/kb/en/library/gtid/) in the MariaDB documentation\.

   1. Make sure that the parameter group associated with the DB instance has the following parameter settings:
      + `gtid_mode` – `ON`, `ON_PERMISSIVE`, or `OFF_PERMISSIVE`
      + `enforce_gtid_consistency` – `ON`

      For more information about parameter groups, see [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)\.

   1. If you changed the parameter group of the DB instance, reboot the DB instance\. For more information, see [Rebooting a DB Instance](USER_RebootInstance.md)\.

1. Make the source MySQL or MariaDB instance read\-only\.

   ```
   mysql> FLUSH TABLES WITH READ LOCK;
   mysql> SET GLOBAL read_only = ON;
   ```

1. Copy the database from the external instance to the Amazon RDS DB instance using `mysqldump`\. For very large databases, you might want to use the procedure in [Importing Data to an Amazon RDS MySQL or MariaDB DB Instance with Reduced Downtime](MySQL.Procedural.Importing.NonRDSRepl.md)\. 

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
Make sure that there is not a space between the `-p` option and the entered password\. 

   To specify the host name, user name, port, and password to connect to your Amazon RDS DB instance, use the `--host`, `--user (-u)`, `--port` and `-p` options in the `mysql` command\. The host name is the DNS name from the Amazon RDS DB instance endpoint, for example, `myinstance.123456789012.us-east-1.rds.amazonaws.com`\. You can find the endpoint value in the instance details in the AWS Management Console\.

1. Make the source MySQL or MariaDB instance writeable again\.

   ```
   mysql> SET GLOBAL read_only = OFF;
   mysql> UNLOCK TABLES;
   ```

   For more information on making backups for use with replication, see [the MySQL documentation](https://dev.mysql.com/doc/refman/8.0/en/replication-solutions-backups-read-only.html)\.

1. In the AWS Management Console, add the IP address of the server that hosts the external database to the VPC security group for the Amazon RDS DB instance\. For more information on modifying a VPC security group, see [Security Groups for Your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html) in the *Amazon Virtual Private Cloud User Guide*\. 

   The IP address can change when the following conditions are met:
   + You are using a public IP address for communication between the external source instance and the DB instance\.
   + The external source instance was stopped and restarted\.

   If these conditions are met, verify the IP address before adding it\.

   You might also need to configure your local network to permit connections from the IP address of your Amazon RDS DB instance\. You do this so that your local network can communicate with your external MySQL or MariaDB instance\. To find the IP address of the Amazon RDS DB instance, use the `host` command\.

   ```
   host db_instance_endpoint
   ```

   The host name is the DNS name from the Amazon RDS DB instance endpoint\.

1. Using the client of your choice, connect to the external instance and create a user to use for replication\. Use this account solely for replication\. and restrict it to your domain to improve security\. The following is an example\. 

   ```
   CREATE USER 'repl_user'@'mydomain.com' IDENTIFIED BY 'password';
   ```

1. For the external instance, grant `REPLICATION CLIENT` and `REPLICATION SLAVE` privileges to your replication user\. For example, to grant the `REPLICATION CLIENT` and `REPLICATION SLAVE` privileges on all databases for the '`repl_user`' user for your domain, issue the following command\.

   ```
   GRANT REPLICATION CLIENT, REPLICATION SLAVE ON *.* TO 'repl_user'@'mydomain.com' IDENTIFIED BY 'password'; 
   ```

1. Make the Amazon RDS DB instance the replica\. To do so, first connect to the Amazon RDS DB instance as the master user\. Then identify the external MySQL or MariaDB database as the replication primary instance by using the [mysql\.rds\_set\_external\_master\_with\_auto\_position](mysql_rds_set_external_master_with_auto_position.md) command\. The following is an example\.

   ```
   CALL mysql.rds_set_external_master_with_auto_position ('mymasterserver.mydomain.com', 3306, 'repl_user', 'password', 0, 0);
   ```
**Note**  
On Amazon RDS MySQL, you can choose to use delayed replication by running the [mysql\.rds\_set\_external\_master\_with\_delay](mysql_rds_set_external_master_with_delay.md) stored procedure instead\. One reason to use delayed replication is to enable disaster recovery with the [mysql\.rds\_start\_replication\_until\_gtid](mysql_rds_start_replication_until_gtid.md) stored procedure\. Currently, delayed replication is not supported on Amazon RDS MariaDB\.

1. On the Amazon RDS DB instance, issue the [mysql\.rds\_start\_replication](mysql_rds_start_replication.md) command to start replication\.

   ```
   CALL mysql.rds_start_replication; 
   ```