# Replication with a MySQL or MariaDB Instance Running External to Amazon RDS<a name="MySQL.Procedural.Importing.External.Repl"></a>

You can set up replication between an Amazon RDS MySQL or MariaDB DB instance and a MySQL or MariaDB instance that is external to Amazon RDS\. Use the procedure in this topic to configure replication in all cases except when the external instance is MariaDB version 10\.0\.2 or greater and the Amazon RDS instance is MariaDB\. In that case, use the procedure at [Configuring GTID\-Based Replication into an Amazon RDS MariaDB DB instance](MariaDB.Procedural.Replication.GTID.md) to set up GTID\-based replication\.

 Be sure to follow these guidelines when you set up an external replication master and a replica on Amazon RDS: 
+ Monitor failover events for the Amazon RDS DB instance that is your replica\. If a failover occurs, then the DB instance that is your replica might be recreated on a new host with a different network address\. For information on how to monitor failover events, see [Using Amazon RDS Event Notification](USER_Events.md)\.
+ Maintain the binlogs on your master instance until you have verified that they have been applied to the replica\. This maintenance ensures that you can restore your master instance in the event of a failure\.
+ Turn on automated backups on your Amazon RDS DB instance\. Turning on automated backups ensures that you can restore your replica to a particular point in time if you need to re\-synchronize your master and replica\. For information on backups and point\-in\-time restore, see [Backing Up and Restoring Amazon RDS DB Instances](CHAP_CommonTasks.BackupRestore.md)\.

**Note**  
The permissions required to start replication on an Amazon RDS DB instance are restricted and not available to your Amazon RDS master user\. Because of this, you must use the Amazon RDS [mysql\.rds\_set\_external\_master](mysql_rds_set_external_master.md) and [mysql\.rds\_start\_replication](mysql_rds_start_replication.md) commands to set up replication between your live database and your Amazon RDS database\.

## Start replication between an external master instance and a DB instance on Amazon RDS<a name="MySQL.Procedural.Importing.External.Repl.Procedure"></a>

1. Make the source MySQL or MariaDB instance read\-only:

   ```
   mysql> FLUSH TABLES WITH READ LOCK;
   mysql> SET GLOBAL read_only = ON;
   ```

1. Run the `SHOW MASTER STATUS` command on the source MySQLor MariaDB instance to determine the binlog location\. You will receive output similar to the following example: 

   ```
   File                        Position  
   ------------------------------------
    mysql-bin-changelog.000031      107   
   ------------------------------------
   ```

1. Copy the database from the external instance to the Amazon RDS DB instance using `mysqldump`\. For very large databases, you might want to use the procedure in [Importing Data to an Amazon RDS MySQL or MariaDB DB Instance with Reduced Downtime](MySQL.Procedural.Importing.NonRDSRepl.md)\. 

   For Linux, OS X, or Unix:

   ```
   mysqldump --databases <database_name> \
       --single-transaction \
       --compress \
       --order-by-primary \
       -u <local_user> \
       -p<local_password> | mysql \
           --host=hostname \
           --port=3306 \
           -u <RDS_user_name> \
           -p<RDS_password>
   ```

   For Windows:

   ```
   mysqldump --databases <database_name> ^
       --single-transaction ^
       --compress ^
       --order-by-primary ^
       -u <local_user> ^
       -p<local_password> | mysql ^
           --host=hostname ^
           --port=3306 ^
           -u <RDS_user_name> ^
           -p<RDS_password>
   ```
**Note**  
Make sure there is not a space between the `-p` option and the entered password\. 

   Use the `--host`, `--user (-u)`, `--port` and `-p` options in the `mysql` command to specify the hostname, username, port, and password to connect to your Amazon RDS DB instance\. The host name is the DNS name from the Amazon RDS DB instance endpoint, for example, `myinstance.123456789012.us-east-1.rds.amazonaws.com`\. You can find the endpoint value in the instance details in the Amazon RDS Management Console\.

1. Make the source MySQL or MariaDB instance writeable again:

   ```
   mysql> SET GLOBAL read_only = OFF;
   mysql> UNLOCK TABLES;
   ```

   For more information on making backups for use with replication, see [Backing Up a Master or Slave by Making It Read Only](http://dev.mysql.com/doc/refman/5.6/en/replication-solutions-backups-read-only.html) in the MySQL documentation\.

1. In the Amazon RDS Management Console, add the IP address of the server that hosts the external database to the VPC security group for the Amazon RDS DB instance\. For more information on modifying a VPC security group, see [Security Groups for Your VPC](http://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html) in the *Amazon Virtual Private Cloud User Guide*\. 

   You might also need to configure your local network to permit connections from the IP address of your Amazon RDS DB instance, so that it can communicate with your external MySQL or MariaDB instance\. To find the IP address of the Amazon RDS DB instance, use the `host` command:

   ```
   host <RDS_MySQL_DB_host_name>
   ```

   The host name is the DNS name from the Amazon RDS DB instance endpoint\.

1. Using the client of your choice, connect to the external instance and create a user that will be used for replication\. This account is used solely for replication and must be restricted to your domain to improve security\. The following is an example: 

   ```
   CREATE USER 'repl_user'@'mydomain.com' IDENTIFIED BY '<password>';
   ```

1. For the external instance, grant `REPLICATION CLIENT` and `REPLICATION SLAVE` privileges to your replication user\. For example, to grant the `REPLICATION CLIENT` and `REPLICATION SLAVE` privileges on all databases for the '`repl_user`' user for your domain, issue the following command:

   ```
   GRANT REPLICATION CLIENT, REPLICATION SLAVE ON *.* TO 'repl_user'@'mydomain.com' IDENTIFIED BY '<password>'; 
   ```

1. Make the Amazon RDS DB instance the replica\. Connect to the Amazon RDS DB instance as the master user and identify the external MySQL or MariaDB database as the replication master by using the [mysql\.rds\_set\_external\_master](mysql_rds_set_external_master.md) command\. Use the master log file name and master log position that you determined in Step 2\. The following is an example: 

   ```
   CALL mysql.rds_set_external_master ('mymasterserver.mydomain.com', 3306,
       'repl_user', '<password>', 'mysql-bin-changelog.000031', 107, 0);
   ```
**Note**  
On Amazon RDS MySQL, you can choose to use delayed replication by running the [mysql\.rds\_set\_external\_master\_with\_delay](mysql_rds_set_external_master_with_delay.md) stored procedure instead\. One reason to use delayed replication is to enable disaster recovery with the [mysql\.rds\_start\_replication\_until](mysql_rds_start_replication_until.md) stored procedure\. Currently, delayed replication is not supported on Amazon RDS MariaDB\.

1. On the Amazon RDS DB instance, issue the [mysql\.rds\_start\_replication](mysql_rds_start_replication.md) command to start replication:

   ```
   CALL mysql.rds_start_replication; 
   ```