# Configuring GTID\-Based Replication into an Amazon RDS MariaDB DB instance<a name="MariaDB.Procedural.Replication.GTID"></a>

You can set up GTID\-based replication from an external MariaDB instance of version 10\.0\.24 or greater into an Amazon RDS MariaDB DB instance\. Be sure to follow these guidelines when you set up an external source instance and a replica on Amazon RDS:
+ Monitor failover events for the Amazon RDS MariaDB DB instance that is your replica\. If a failover occurs, then the DB instance that is your replica might be recreated on a new host with a different network address\. For information on how to monitor failover events, see [Using Amazon RDS Event Notification](USER_Events.md)\.
+ Maintain the binlogs on your source instance until you have verified that they have been applied to the replica\. This maintenance ensures that you can restore your source instance in the event of a failure\.
+ Turn on automated backups on your MariaDB DB instance on Amazon RDS\. Turning on automated backups ensures that you can restore your replica to a particular point in time if you need to re\-synchronize your source instance and replica\. For information on backups and Point\-In\-Time Restore, see [Backing Up and Restoring an Amazon RDS DB Instance](CHAP_CommonTasks.BackupRestore.md)\.

**Note**  
The permissions required to start replication on an Amazon RDS MariaDB DB instance are restricted and not available to your Amazon RDS master user\. Because of this, you must use the Amazon RDS [mysql\.rds\_set\_external\_master\_gtid](mysql_rds_set_external_master_gtid.md) and [mysql\.rds\_start\_replication](mysql_rds_start_replication.md) commands to set up replication between your live database and your Amazon RDS MariaDB database\. 

To start replication between an external source instance and a MariaDB DB instance on Amazon RDS, use the following procedure\. <a name="MariaDB.Procedural.Importing.External.Repl.Procedure"></a>

**To Start Replication**

1. Make the source MariaDB instance read\-only:

   ```
   mysql> FLUSH TABLES WITH READ LOCK;
   mysql> SET GLOBAL read_only = ON;
   ```

1. Get the current GTID of the external MariaDB instance\. You can do this by using `mysql` or the query editor of your choice to run `SELECT @@gtid_current_pos;`\. 

   The GTID is formatted as `<domain-id>-<server-id>-<sequence-id>`\. A typical GTID looks something like **0\-1234510749\-1728**\. For more information about GTIDs and their component parts, see [Global Transaction ID](http://mariadb.com/kb/en/mariadb/global-transaction-id/) in the MariaDB documentation\. 

1. Copy the database from the external MariaDB instance to the Amazon RDS MariaDB DB instance using `mysqldump`\. For very large databases, you might want to use the procedure in [Importing Data to an Amazon RDS MySQL or MariaDB DB Instance with Reduced Downtime](MySQL.Procedural.Importing.NonRDSRepl.md)\. 
**Note**  
Make sure there is not a space between the `-p` option and the entered password\. 

   For Linux, macOS, or Unix:

   ```
   mysqldump \
       --databases <database_name> \
       --single-transaction \
       --compress \
       --order-by-primary \
       -u <local_user> \
       -p<local_password> | mysql \
           --host=hostname \
           --port=3306 \
           -u <RDS_user_name> \
           -p <RDS_password>
   ```

   For Windows:

   ```
   mysqldump ^
       --databases <database_name> ^
       --single-transaction ^
       --compress ^
       --order-by-primary \
       -u <local_user> \
       -p<local_password> | mysql ^
           --host=hostname ^
           --port=3306 ^
           -u <RDS_user_name> ^
           -p <RDS_password>
   ```

   Use the `--host`, `--user (-u)`, `--port` and `-p` options in the `mysql` command to specify the host name, user name, port, and password to connect to your Amazon RDS MariaDB DB instance\. The host name is the DNS name from the Amazon RDS MariaDB DB instance endpoint, for example `myinstance.123456789012.us-east-1.rds.amazonaws.com`\. You can find the endpoint value in the instance details in the Amazon RDS Management Console\. 

1. Make the source MariaDB instance writeable again:

   ```
   mysql> SET GLOBAL read_only = OFF;
   mysql> UNLOCK TABLES;
   ```

1. In the Amazon RDS Management Console, add the IP address of the server that hosts the external MariaDB database to the VPC security group for the Amazon RDS MariaDB DB instance\. For more information on modifying a VPC security group, go to [Security Groups for Your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html) in the *Amazon Virtual Private Cloud User Guide*\. 

   The IP address can change when the following conditions are met:
   + You are using a public IP address for communication between the external source instance and the DB instance\.
   + The external source instance was stopped and restarted\.

   If these conditions are met, verify the IP address before adding it\.

   You might also need to configure your local network to permit connections from the IP address of your Amazon RDS MariaDB DB instance, so that it can communicate with your external MariaDB instance\. To find the IP address of the Amazon RDS MariaDB DB instance, use the `host` command: 

   ```
   host <RDS_MariaDB_DB_host_name>
   ```

   The host name is the DNS name from the Amazon RDS MariaDB DB instance endpoint\. 

1. Using the client of your choice, connect to the external MariaDB instance and create a MariaDB user to be used for replication\. This account is used solely for replication and must be restricted to your domain to improve security\. The following is an example: 

   ```
   CREATE USER 'repl_user'@'mydomain.com' IDENTIFIED BY '<password>';
   ```

1. For the external MariaDB instance, grant `REPLICATION CLIENT` and `REPLICATION SLAVE` privileges to your replication user\. For example, to grant the `REPLICATION CLIENT` and `REPLICATION SLAVE` privileges on all databases for the '`repl_user`' user for your domain, issue the following command: 

   ```
   GRANT REPLICATION CLIENT, REPLICATION SLAVE ON *.* 
      TO 'repl_user'@'mydomain.com' 
      IDENTIFIED BY '<password>';
   ```

1. Make the Amazon RDS MariaDB DB instance the replica\. Connect to the Amazon RDS MariaDB DB instance as the master user and identify the external MariaDB database as the replication source instance by using the [mysql\.rds\_set\_external\_master\_gtid](mysql_rds_set_external_master_gtid.md) command\. Use the GTID that you determined in Step 2\. The following is an example: 

   ```
   CALL mysql.rds_set_external_master_gtid ('mymasterserver.mydomain.com', 3306,
       'repl_user', '<password>', '<GTID>', 0);
   ```

1. On the Amazon RDS MariaDB DB instance, issue the [mysql\.rds\_start\_replication](mysql_rds_start_replication.md) command to start replication: 

   ```
   CALL mysql.rds_start_replication; 
   ```