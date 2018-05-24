# Importing Data into a MariaDB DB Instance<a name="MariaDB.Procedural.Importing"></a>

Following, you can find information about  methods to import your MariaDB data to an Amazon RDS DB instance running MariaDB\. 

To do an initial data import into a MariaDB DB instance, you can use the procedures documented in [Importing Data into an Amazon RDS MySQL DB Instance](MySQL.Procedural.Importing.md), as follows: 
+ To move data from an Amazon RDS MySQL DB instance, a MariaDB or MySQL instance in Amazon Elastic Compute Cloud \(Amazon EC2\) in the same VPC as your Amazon RDS MariaDB DB instance, or a small on\-premises instance of MariaDB or MySQL, you can use the procedure documented in [Importing Data from a MySQL or MariaDB DB to an Amazon RDS MySQL or MariaDB DB Instance](MySQL.Procedural.Importing.SmallExisting.md)\.
+ To move data from a large or production on\-premises instance of MariaDB or MySQL, you can use the procedure documented in [Importing Data to an Amazon RDS MySQL or MariaDB DB Instance with Reduced Downtime](MySQL.Procedural.Importing.NonRDSRepl.md)\.
+ To move data from an instance of MariaDB or MySQL that is in EC2 in a different VPC than your Amazon RDS MariaDB DB instance, or to move data from any data source that can output delimited text files, you can use the procedure documented in [Importing Data From Any Source to a MySQL or MariaDB DB Instance](MySQL.Procedural.Importing.AnySource.md)\.

You can also use AWS Database Migration Service \(AWS DMS\) to import data into an Amazon RDS DB instance\. AWS DMS can migrate databases without downtime and, for many database engines, continue ongoing replication until you are ready to switch over to the target database\. You can migrate to MariaDB from either the same database engine or a different database engine using AWS DMS\. If you are migrating from a different database engine, you can use the AWS Schema Conversion Tool to migrate schema objects that are not migrated by AWS DMS\. For more information about AWS DMS, see see [ What is AWS Database Migration Service](http://docs.aws.amazon.com/dms/latest/userguide/Welcome.html)\. 

You can configure replication into an Amazon RDS MariaDB DB instance using MariaDB global transaction identifiers \(GTIDs\) when the external instance is MariaDB version 10\.0\.24 or greater, or using binary log coordinates for MySQL instances or MariaDB instances on earlier versions than 10\.0\.24\. Note that MariaDB GTIDs are implemented differently than MySQL GTIDs, which are not supported by Amazon RDS\. 

To configure replication into a MariaDB DB instance, you can use the following procedures: 
+ To configure replication into a MariaDB DB instance from an external MySQL instance or an external MariaDB instance running a version prior to 10\.0\.24, you can use the procedure documented in [Replication with a MySQL or MariaDB Instance Running External to Amazon RDS](MySQL.Procedural.Importing.External.Repl.md)\.
+ To configure replication into a MariaDB DB instance from an external MariaDB instance running version 10\.0\.24 or greater, you can use the procedure documented in [Configuring GTID\-Based Replication into an Amazon RDS MariaDB DB instance](#MariaDB.Procedural.Replication.GTID)\.

**Note**  
The mysql system database contains authentication and authorization information required to log into your DB instance and access your data\. Dropping, altering, renaming, or truncating tables, data, or other contents of the mysql database in your DB instance can result in errors and might render the DB instance and your data inaccessible\. If this occurs, the DB instance can be restored from a snapshot using the AWS CLI [http://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-from-db-snapshot.html](http://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-from-db-snapshot.html) or recovered using [http://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-to-point-in-time.html](http://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-to-point-in-time.html) commands\. 

## Configuring GTID\-Based Replication into an Amazon RDS MariaDB DB instance<a name="MariaDB.Procedural.Replication.GTID"></a>

You can set up GTID\-based replication from an external MariaDB instance of version 10\.0\.24 or greater into an Amazon RDS MariaDB DB instance\. Be sure to follow these guidelines when you set up an external replication master and a replica on Amazon RDS:
+ Monitor failover events for the Amazon RDS MariaDB DB instance that is your replica\. If a failover occurs, then the DB instance that is your replica might be recreated on a new host with a different network address\. For information on how to monitor failover events, see [Using Amazon RDS Event Notification](USER_Events.md)\.
+ Maintain the binlogs on your master instance until you have verified that they have been applied to the replica\. This maintenance ensures that you can restore your master instance in the event of a failure\.
+ Turn on automated backups on your MariaDB DB instance on Amazon RDS\. Turning on automated backups ensures that you can restore your replica to a particular point in time if you need to re\-synchronize your master and replica\. For information on backups and Point\-In\-Time Restore, see [Backing Up and Restoring Amazon RDS DB Instances](CHAP_CommonTasks.BackupRestore.md)\.

**Note**  
The permissions required to start replication on an Amazon RDS MariaDB DB instance are restricted and not available to your Amazon RDS master user\. Because of this, you must use the Amazon RDS [mysql\.rds\_set\_external\_master\_gtid](mysql_rds_set_external_master_gtid.md) and [mysql\.rds\_start\_replication](mysql_rds_start_replication.md) commands to set up replication between your live database and your Amazon RDS MariaDB database\. 

To start replication between an external master instance and a MariaDB DB instance on Amazon RDS, use the following procedure\. <a name="MariaDB.Procedural.Importing.External.Repl.Procedure"></a>

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

   For Linux, OS X, or Unix:

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

1. In the Amazon RDS Management Console, add the IP address of the server that hosts the external MariaDB database to the VPC security group for the Amazon RDS MariaDB DB instance\. For more information on modifying a VPC security group, go to [Security Groups for Your VPC](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_SecurityGroups.html) in the *Amazon Virtual Private Cloud User Guide*\. 

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

1. Make the Amazon RDS MariaDB DB instance the replica\. Connect to the Amazon RDS MariaDB DB instance as the master user and identify the external MariaDB database as the replication master by using the [mysql\.rds\_set\_external\_master\_gtid](mysql_rds_set_external_master_gtid.md) command\. Use the GTID that you determined in Step 2\. The following is an example: 

   ```
   CALL mysql.rds_set_external_master_gtid ('master_server_ip_address', 3306,
       'repl_user', '<password>', '<GTID>', 0);
   ```
   The `master_server_ip_address` is the IP address of master MariaDB instance\. Note that EC2 Private DNS address is currently not supported\.

1. On the Amazon RDS MariaDB DB instance, issue the [mysql\.rds\_start\_replication](mysql_rds_start_replication.md) command to start replication: 

   ```
   CALL mysql.rds_start_replication; 
   ```