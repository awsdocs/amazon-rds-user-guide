# Importing data to an Amazon RDS MariaDB or MySQL DB instance with reduced downtime<a name="MySQL.Procedural.Importing.NonRDSReplMariaDB"></a>

In some cases, you might need to import data from an external MariaDB or MySQL database that supports a live application to a MariaDB DB instance, a MySQL DB instance, or a MySQL Multi\-AZ DB cluster\. Use the following procedure to minimize the impact on availability of applications\. This procedure can also help if you are working with a very large database\. Using this procedure, you can reduce the cost of the import by reducing the amount of data that is passed across the network to AWS\. 

In this procedure, you transfer a copy of your database data to an Amazon EC2 instance and import the data into a new Amazon RDS database\. You then use replication to bring the Amazon RDS database up\-to\-date with your live external instance, before redirecting your application to the Amazon RDS database\. Configure MariaDB replication based on global transaction identifiers \(GTIDs\) if the external instance is MariaDB 10\.0\.24 or higher and the target instance is RDS for MariaDB\. Otherwise, configure replication based on binary log coordinates\. We recommend GTID\-based replication if your external database supports it because GTID\-based replication is a more reliable method\. For more information, see [Global transaction ID](http://mariadb.com/kb/en/mariadb/global-transaction-id/) in the MariaDB documentation\.

**Note**  
If you want to import data into a MySQL DB instance and your scenario supports it, we recommend moving data in and out of Amazon RDS by using backup files and Amazon S3\. For more information, see [Restoring a backup into a MySQL DB instance](MySQL.Procedural.Importing.md)\. 

![\[Import an external MySQL database to a MySQL database on RDS\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/MigrateMySQLToRDS_1.png)

**Note**  
We don't recommend that you use this procedure with source MySQL databases from MySQL versions earlier than version 5\.5 because of potential replication issues\. For more information, see [Replication compatibility between MySQL versions](https://dev.mysql.com/doc/refman/8.0/en/replication-compatibility.html) in the MySQL documentation\.

## Create a copy of your existing database<a name="MySQL.Procedural.Importing.Copy.Database"></a>

The first step in the process of migrating a large amount of data to an RDS for MariaDB or RDS for MySQL database with minimal downtime is to create a copy of the source data\. 

![\[Create a backup of the MySQL database\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/MigrateMySQLToRDS_2.png)

You can use the mysqldump utility to create a database backup in either SQL or delimited\-text format\. We recommend that you do a test run with each format in a non\-production environment to see which method minimizes the amount of time that mysqldump runs\.

We also recommend that you weigh mysqldump performance against the benefit offered by using the delimited\-text format for loading\. A backup using delimited\-text format creates a tab\-separated text file for each table being dumped\. To reduce the amount of time required to import your database, you can load these files in parallel using the `LOAD DATA LOCAL INFILE` command\. For more information about choosing a mysqldump format and then loading the data, see [ Using mysqldump for backups](https://dev.mysql.com/doc/mysql-backup-excerpt/8.0/en/using-mysqldump.html) in the MySQL documentation\.

Before you start the backup operation, make sure to set the replication options on the MariaDB or MySQL database that you are copying to Amazon RDS\. The replication options include turning on binary logging and setting a unique server ID\. Setting these options causes your server to start logging database transactions and prepares it to be a source replication instance later in this process\.

**Note**  
Use the `--single-transaction` option with mysqldump because it dumps a consistent state of the database\. To ensure a valid dump file, don't run data definition language \(DDL\) statements while mysqldump is running\. You can schedule a maintenance window for these operations\.  
Exclude the following schemas from the dump file: `sys`, `performance_schema`, and `information_schema`\. The mysqldump utility excludes these schemas by default\.  
To migrate users and privileges, consider using a tool that generates the data control language \(DCL\) for recreating them, such as the [pt\-show\-grants](https://www.percona.com/doc/percona-toolkit/LATEST/pt-show-grants.html) utility\.

### To set replication options<a name="MySQL.Procedural.Importing.Repl.Setup.Procedure"></a>

1. Edit the `my.cnf` file \(this file is usually under `/etc`\)\.

   ```
   sudo vi /etc/my.cnf
   ```

   Add the `log_bin` and `server_id` options to the `[mysqld]` section\. The `log_bin` option provides a file name identifier for binary log files\. The `server_id` option provides a unique identifier for the server in source\-replica relationships\.

   The following example shows the updated `[mysqld]` section of a `my.cnf` file\.

   ```
   [mysqld]
   log-bin=mysql-bin
   server-id=1
   ```

   For more information, see [the MySQL documentation](https://dev.mysql.com/doc/refman/8.0/en/replication-howto-masterbaseconfig.html)\.

1. For replication with a Multi\-AZ DB cluster, set the `ENFORCE_GTID_CONSISTENCY` and the `GTID_MODE` parameter to `ON`\.

   ```
   mysql> SET @@GLOBAL.ENFORCE_GTID_CONSISTENCY = ON;
   ```

   ```
   mysql> SET @@GLOBAL.GTID_MODE = ON;
   ```

   These settings aren't required for replication with a DB instance\.

1. Restart the `mysql` service\.

   ```
   sudo service mysqld restart
   ```

### To create a backup copy of your existing database<a name="MySQL.Procedural.Importing.Database.Backup.Procedure"></a>

1. Create a backup of your data using the mysqldump utility, specifying either SQL or delimited\-text format\.

   Specify `--master-data=2` to create a backup file that can be used to start replication between servers\. For more information, see the [ mysqldump](https://dev.mysql.com/doc/refman/8.0/en/mysqldump.html#option_mysqldump_master-data) documentation\.

   To improve performance and ensure data integrity, use the `--order-by-primary` and `--single-transaction` options of mysqldump\.

   To avoid including the MySQL system database in the backup, do not use the `--all-databases` option with mysqldump\. For more information, see [ Creating a data snapshot using mysqldump](https://dev.mysql.com/doc/mysql-replication-excerpt/8.0/en/replication-howto-mysqldump.html) in the MySQL documentation\.

   Use `chmod` if necessary to make sure that the directory where the backup file is being created is writeable\.
**Important**  
On Windows, run the command window as an administrator\.
   + To produce SQL output, use the following command\.

     For Linux, macOS, or Unix:

     ```
     sudo mysqldump \
         --databases database_name \
         --master-data=2  \
         --single-transaction \
         --order-by-primary \
         -r backup.sql \
         -u local_user \
         -p password
     ```

     For Windows:

     ```
     mysqldump ^
         --databases database_name ^
         --master-data=2  ^
         --single-transaction ^
         --order-by-primary ^
         -r backup.sql ^
         -u local_user ^
         -p password
     ```
   + To produce delimited\-text output, use the following command\.

     For Linux, macOS, or Unix:

     ```
     sudo mysqldump \
         --tab=target_directory \
         --fields-terminated-by ',' \
         --fields-enclosed-by '"' \
         --lines-terminated-by 0x0d0a \
         database_name \
         --master-data=2 \
         --single-transaction \
         --order-by-primary \
         -p password
     ```

     For Windows:

     ```
     mysqldump ^
         --tab=target_directory ^
         --fields-terminated-by "," ^
         --fields-enclosed-by """ ^
         --lines-terminated-by 0x0d0a ^
         database_name ^
         --master-data=2 ^
         --single-transaction ^
         --order-by-primary ^
         -p password
     ```
**Note**  
Make sure to create any stored procedures, triggers, functions, or events manually in your Amazon RDS database\. If you have any of these objects in the database that you are copying, exclude them when you run mysqldump\. To do so, include the following arguments with your mysqldump command: `--routines=0 --triggers=0 --events=0`\.

     When using the delimited\-text format, a `CHANGE MASTER TO` comment is returned when you run mysqldump\. This comment contains the master log file name and position\. If the external instance is other than MariaDB version 10\.0\.24 or higher, note the values for `MASTER_LOG_FILE` and `MASTER_LOG_POS`\. You need these values when setting up replication\.

     ```
     -- Position to start replication or point-in-time recovery from
     --
     -- CHANGE MASTER TO MASTER_LOG_FILE='mysql-bin-changelog.000031', MASTER_LOG_POS=107;
     ```

     If you are using SQL format, you can get the master log file name and position in the `CHANGE MASTER TO` comment in the backup file\. If the external instance is MariaDB version 10\.0\.24 or higher, you can get the GTID in the next step\.

1. If the external instance you are using is MariaDB version 10\.0\.24 or higher, you use GTID\-based replication\. Run `SHOW MASTER STATUS` on the external MariaDB instance to get the binary log file name and position, then convert them to a GTID by running `BINLOG_GTID_POS` on the external MariaDB instance\.

   ```
   SELECT BINLOG_GTID_POS('binary log file name', binary log file position);
   ```

   Note the GTID returned; you need it to configure replication\.

1. Compress the copied data to reduce the amount of network resources needed to copy your data to the Amazon RDS database\. Note the size of the backup file\. You need this information when determining how large an Amazon EC2 instance to create\. When you are done, compress the backup file using GZIP or your preferred compression utility\. 
   + To compress SQL output, use the following command\.

     ```
     gzip backup.sql
     ```
   + To compress delimited\-text output, use the following command\.

     ```
     tar -zcvf backup.tar.gz target_directory
     ```

## Create an Amazon EC2 instance and copy the compressed database<a name="MySQL.Procedural.Importing.Import.Database"></a>

Copying your compressed database backup file to an Amazon EC2 instance takes fewer network resources than doing a direct copy of uncompressed data between database instances\. After your data is in Amazon EC2, you can copy it from there directly to your MariaDB or MySQL database\. For you to save on the cost of network resources, your Amazon EC2 instance must be in the same AWS Region as your Amazon RDS DB instance\. Having the Amazon EC2 instance in the same AWS Region as your Amazon RDS database also reduces network latency during the import\.

![\[Copy the database backup to an EC2 instance\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/MigrateMySQLToRDS_3.png)

### To create an Amazon EC2 instance and copy your data<a name="MySQL.Procedural.Importing.Create.EC2"></a>

1. In the AWS Region where you plan to create the RDS database, create a virtual private cloud \(VPC\), a VPC security group, and a VPC subnet\. Ensure that the inbound rules for your VPC security group allow the IP addresses required for your application to connect to AWS\. You can specify a range of IP addresses \(for example, `203.0.113.0/24`\), or another VPC security group\. You can use the [Amazon VPC Management Console](https://console.aws.amazon.com/vpc) to create and manage VPCs, subnets, and security groups\. For more information, see [Getting started with Amazon VPC](https://docs.aws.amazon.com/AmazonVPC/latest/GettingStartedGuide/GetStarted.html) in the *Amazon Virtual Private Cloud Getting Started Guide*\.

1. Open the [Amazon EC2 Management Console](https://console.aws.amazon.com/ec2) and choose the AWS Region to contain both your Amazon EC2 instance and your Amazon RDS database\. Launch an Amazon EC2 instance using the VPC, subnet, and security group that you created in Step 1\. Ensure that you select an instance type with enough storage for your database backup file when it is uncompressed\. For details on Amazon EC2 instances, see [Getting started with Amazon EC2 Linux instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EC2_GetStarted.html) in the *Amazon Elastic Compute Cloud User Guide for Linux*\.

1.  To connect to your Amazon RDS database from your Amazon EC2 instance, edit your VPC security group\. Add an inbound rule specifying the private IP address of your EC2 instance\. You can find the private IP address on the **Details** tab of the **Instance** pane in the EC2 console window\. To edit the VPC security group and add an inbound rule, choose **Security Groups** in the EC2 console navigation pane, choose your security group, and then add an inbound rule for MySQL or Aurora specifying the private IP address of your EC2 instance\. To learn how to add an inbound rule to a VPC security group, see [Adding and removing rules](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html#AddRemoveRules) in the *Amazon VPC User Guide*\.

1. Copy your compressed database backup file from your local system to your Amazon EC2 instance\. Use `chmod` if necessary to make sure that you have write permission for the target directory of the Amazon EC2 instance\. You can use `scp` or a Secure Shell \(SSH\) client to copy the file\. The following is an example\.

   ```
   $ scp -r -i key pair.pem backup.sql.gz ec2-user@EC2 DNS:/target_directory/backup.sql.gz
   ```
**Important**  
Be sure to copy sensitive data using a secure network transfer protocol\.

1. Connect to your Amazon EC2 instance and install the latest updates and the MySQL client tools using the following commands\.

   ```
   sudo yum update -y
   sudo yum install mysql -y
   ```

   For more information, see [Connect to your instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-connect-to-instance-linux.html) in the *Amazon Elastic Compute Cloud User Guide for Linux*\.
**Important**  
This example installs the MySQL client on an Amazon Machine Image \(AMI\) for an Amazon Linux distribution\. To install the MySQL client on a different distribution, such as Ubuntu or Red Hat Enterprise Linux, this example doesn't work\. For information about installing MySQL, see [Installing and Upgrading MySQL](https://dev.mysql.com/doc/refman/8.0/en/installing.html) in the MySQL documentation\.

1. While connected to your Amazon EC2 instance, decompress your database backup file\. The following are examples\.
   + To decompress SQL output, use the following command\.

     ```
     gzip backup.sql.gz -d
     ```
   + To decompress delimited\-text output, use the following command\.

     ```
     tar xzvf backup.tar.gz
     ```

## Create a MySQL or MariaDB database and import data from your Amazon EC2 instance<a name="MySQL.Procedural.Importing.Create.RDS.Database"></a>

By creating a MariaDB DB instance, a MySQL DB instance, or a MySQL Multi\-AZ DB cluster in the same AWS Region as your Amazon EC2 instance, you can import the database backup file from EC2 faster than over the internet\.

![\[Import the backup from the EC2 instance to the MySQL database\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/MigrateMySQLToRDS_4.png)

### To create a MariaDB or MySQL database and import your data<a name="MySQL.Procedural.Importing.Create.RDS.Database.Procedure"></a>

1. Determine which DB instance class and what amount of storage space is required to support the expected workload for this Amazon RDS database\. As part of this process, decide what is sufficient space and processing capacity for your data load procedures\. Also decide what is required to handle the production workload\. You can estimate this based on the size and resources of the source MariaDB or MySQL database\. For more information, see [DB instance classes](Concepts.DBInstanceClass.md)\.

1. Create a DB instance or Multi\-AZ DB cluster in the AWS Region that contains your Amazon EC2 instance\.

   To create a MySQL Multi\-AZ DB cluster, follow the instructions in [Creating a Multi\-AZ DB cluster](create-multi-az-db-cluster.md)\.

   To create a MariaDB or MySQL DB instance, follow the instructions in [Creating an Amazon RDS DB instance](USER_CreateDBInstance.md) and use the following guidelines:
   + Specify a DB engine version that is compatible with your source DB instance, as follows:
     + If your source instance is MySQL 5\.5\.x, the Amazon RDS DB instance must be MySQL\.
     + If your source instance is MySQL 5\.6\.x or 5\.7\.x, the Amazon RDS DB instance must be MySQL or MariaDB\.
     + If your source instance is MySQL 8\.0\.x, the Amazon RDS DB instance must be MySQL 8\.0\.x\.
     + If your source instance is MariaDB 5\.5 or higher, the Amazon RDS DB instance must be MariaDB\.
   + Specify the same virtual private cloud \(VPC\) and VPC security group as for your Amazon EC2 instance\. This approach ensures that your Amazon EC2 instance and your Amazon RDS instance are visible to each other over the network\. Make sure your DB instance is publicly accessible\. To set up replication with your source database as described later, your DB instance must be publicly accessible\.
   + Don't configure multiple Availability Zones, backup retention, or read replicas until after you have imported the database backup\. When that import is completed, you can configure Multi\-AZ and backup retention for the production instance\.

1. Review the default configuration options for the Amazon RDS database\. If the default parameter group for the database doesn't have the configuration options that you want, find a different one that does or create a new parameter group\. For more information on creating a parameter group, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\. 

1. Connect to the new Amazon RDS database as the master user\. Create the users required to support the administrators, applications, and services that need to access the instance\. The hostname for the Amazon RDS database is the **Endpoint** value for this instance without including the port number\. An example is `mysampledb.123456789012.us-west-2.rds.amazonaws.com`\. You can find the endpoint value in the database details in the Amazon RDS Management Console\.

1. Connect to your Amazon EC2 instance\. For more information, see [Connect to your instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-connect-to-instance-linux.html) in the *Amazon Elastic Compute Cloud User Guide for Linux*\.

1. Connect to your Amazon RDS database as a remote host from your Amazon EC2 instance using the `mysql` command\. The following is an example\.

   ```
   mysql -h host_name -P 3306 -u db_master_user -p
   ```

   The hostname is the Amazon RDS database endpoint\.

1. At the `mysql` prompt, run the `source` command and pass it the name of your database dump file to load the data into the Amazon RDS DB instance:
   + For SQL format, use the following command\.

     ```
     mysql> source backup.sql;
     ```
   + For delimited\-text format, first create the database, if it isn't the default database you created when setting up the Amazon RDS database\.

     ```
     mysql> create database database_name;
     $ mysql> use database_name;
     ```

     Then create the tables\.

     ```
     mysql> source table1.sql
     $ mysql> source table2.sql
     etc...
     ```

     Then import the data\.

     ```
     mysql> LOAD DATA LOCAL INFILE 'table1.txt' INTO TABLE table1 FIELDS TERMINATED BY ',' ENCLOSED BY '"' LINES TERMINATED BY '0x0d0a';
     $ mysql> LOAD DATA LOCAL INFILE 'table2.txt' INTO TABLE table2 FIELDS TERMINATED BY ',' ENCLOSED BY '"' LINES TERMINATED BY '0x0d0a';
     etc...
     ```

     To improve performance, you can perform these operations in parallel from multiple connections so that all of your tables are created and then loaded at the same time\.
**Note**  
If you used any data\-formatting options with mysqldump when you initially dumped the table, make sure to use the same options with `mysqlimport` or LOAD DATA LOCAL INFILE to ensure proper interpretation of the data file contents\.

1. Run a simple SELECT query against one or two of the tables in the imported database to verify that the import was successful\.

If you no longer need the Amazon EC2 instance used in this procedure, terminate the EC2 instance to reduce your AWS resource usage\. To terminate an EC2 instance, see [Terminating an instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/terminating-instances.html#terminating-instances-console) in the *Amazon EC2 User Guide*\.

## Replicate between your external database and new Amazon RDS database<a name="MySQL.Procedural.Importing.Start.Repl"></a>

Your source database was likely updated during the time that it took to copy and transfer the data to the MariaDB or MySQL database\. Thus, you can use replication to bring the copied database up\-to\-date with the source database\.

![\[Replicate data from the external MySQL database to the database on RDS\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/MigrateMySQLToRDS_5.png)

The permissions required to start replication on an Amazon RDS database are restricted and not available to your Amazon RDS master user\. Because of this, make sure to use either the Amazon RDS [mysql\.rds\_set\_external\_master](mysql_rds_set_external_master.md) command or the [mysql\.rds\_set\_external\_master\_gtid](mysql_rds_set_external_master_gtid.md) command to configure replication, and the [mysql\.rds\_start\_replication](mysql_rds_start_replication.md) command to start replication between your live database and your Amazon RDS database\.

### To start replication<a name="MySQL.Procedural.Importing.Start.Repl.Procedure"></a>

Earlier, you turned on binary logging and set a unique server ID for your source database\. Now you can set up your Amazon RDS database as a replica with your live database as the source replication instance\.

1. In the Amazon RDS Management Console, add the IP address of the server that hosts the source database to the VPC security group for the Amazon RDS database\. For more information on modifying a VPC security group, see [Security groups for your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html) in the *Amazon Virtual Private Cloud User Guide*\. 

   You might also need to configure your local network to permit connections from the IP address of your Amazon RDS database, so that it can communicate with your source instance\. To find the IP address of the Amazon RDS database, use the `host` command\.

   ```
   host rds_db_endpoint
   ```

   The hostname is the DNS name from the Amazon RDS database endpoint, for example `myinstance.123456789012.us-east-1.rds.amazonaws.com`\. You can find the endpoint value in the instance details in the Amazon RDS Management Console\.

1. Using the client of your choice, connect to the source instance and create a user to be used for replication\. This account is used solely for replication and must be restricted to your domain to improve security\. The following is an example\.

   **MySQL 5\.5, 5\.6, and 5\.7**

   ```
   CREATE USER 'repl_user'@'mydomain.com' IDENTIFIED BY 'password';
   ```

   **MySQL 8\.0**

   ```
   CREATE USER 'repl_user'@'mydomain.com' IDENTIFIED WITH mysql_native_password BY 'password';
   ```

1. For the source instance, grant `REPLICATION CLIENT` and `REPLICATION SLAVE` privileges to your replication user\. For example, to grant the `REPLICATION CLIENT` and `REPLICATION SLAVE` privileges on all databases for the '`repl_user`' user for your domain, issue the following command\.

   **MySQL 5\.5, 5\.6, and 5\.7**

   ```
   GRANT REPLICATION CLIENT, REPLICATION SLAVE ON *.* TO 'repl_user'@'mydomain.com' IDENTIFIED BY 'password';
   ```

   **MySQL 8\.0**

   ```
   GRANT REPLICATION CLIENT, REPLICATION SLAVE ON *.* TO 'repl_user'@'mydomain.com';
   ```

1. If you used SQL format to create your backup file and the external instance is not MariaDB 10\.0\.24 or higher, look at the contents of that file\.

   ```
   cat backup.sql
   ```

   The file includes a `CHANGE MASTER TO` comment that contains the master log file name and position\. This comment is included in the backup file when you use the `--master-data` option with mysqldump\. Note the values for `MASTER_LOG_FILE` and `MASTER_LOG_POS`\.

   ```
   --
   -- Position to start replication or point-in-time recovery from
   --
   
   -- CHANGE MASTER TO MASTER_LOG_FILE='mysql-bin-changelog.000031', MASTER_LOG_POS=107;
   ```

   If you used delimited text format to create your backup file and the external instance isn't MariaDB 10\.0\.24 or higher, you should already have binary log coordinates from step 1 of the procedure at "To create a backup copy of your existing database" in this topic\.

   If the external instance is MariaDB 10\.0\.24 or higher, you should already have the GTID from which to start replication from step 2 of the procedure at "To create a backup copy of your existing database" in this topic\.

1. Make the Amazon RDS database the replica\. If the external instance isn't MariaDB 10\.0\.24 or higher, connect to the Amazon RDS database as the master user and identify the source database as the source replication instance by using the [mysql\.rds\_set\_external\_master](mysql_rds_set_external_master.md) command\. Use the master log file name and master log position that you determined in the previous step if you have a SQL format backup file\. Or use the name and position that you determined when creating the backup files if you used delimited\-text format\. The following is an example\.

   ```
   CALL mysql.rds_set_external_master ('myserver.mydomain.com', 3306,
       'repl_user', 'password', 'mysql-bin-changelog.000031', 107, 0);
   ```

   If the external instance is MariaDB 10\.0\.24 or higher, connect to the Amazon RDS database as the master user and identify the source database as the source replication instance by using the [mysql\.rds\_set\_external\_master\_gtid](mysql_rds_set_external_master_gtid.md) command\. Use the GTID that you determined in step 2 of the procedure at "To create a backup copy of your existing database" in this topic\.\. The following is an example\.

   ```
   CALL mysql.rds_set_external_master_gtid ('source_server_ip_address', 3306, 'ReplicationUser', 'password', 'GTID', 0); 
   ```

   The `source_server_ip_address` is the IP address of source replication instance\. An EC2 private DNS address is currently not supported\.

1. On the Amazon RDS database, issue the [mysql\.rds\_start\_replication](mysql_rds_start_replication.md) command to start replication\.

   ```
   CALL mysql.rds_start_replication;
   ```

1. On the Amazon RDS database, run the [SHOW REPLICA STATUS](https://dev.mysql.com/doc/refman/8.0/en/show-replica-status.html) command to determine when the replica is up\-to\-date with the source replication instance\. The results of the `SHOW REPLICA STATUS` command include the `Seconds_Behind_Master` field\. When the `Seconds_Behind_Master` field returns 0, then the replica is up\-to\-date with the source replication instance\.
**Note**  
Previous versions of MySQL used `SHOW SLAVE STATUS` instead of `SHOW REPLICA STATUS`\. If you are using a MySQL version before 8\.0\.23, then use `SHOW SLAVE STATUS`\. 

   For a MariaDB 10\.5 or 10\.6 DB instance, run the [mysql\.rds\_replica\_status](mysql_rds_replica_status.md) procedure instead of the MySQL command\.

1. After the Amazon RDS database is up\-to\-date, turn on automated backups so you can restore that database if needed\. You can turn on or modify automated backups for your Amazon RDS database using the [Amazon RDS Management Console](https://console.aws.amazon.com/rds/)\. For more information, see [Working with backups](USER_WorkingWithAutomatedBackups.md)\.

## Redirect your live application to your Amazon RDS instance<a name="MySQL.Procedural.Importing.Redirect.App"></a>

After the MariaDB or MySQL database is up\-to\-date with the source replication instance, you can now update your live application to use the Amazon RDS instance\.

![\[Stop replication and direct the live application to the database on RDS\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/MigrateMySQLToRDS_6.png)

### To redirect your live application to your MariaDB or MySQL database and stop replication<a name="MySQL.Procedural.Importing.Redirect.App.Procedure"></a>

1. To add the VPC security group for the Amazon RDS database, add the IP address of the server that hosts the application\. For more information on modifying a VPC security group, see [Security groups for your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html) in the *Amazon Virtual Private Cloud User Guide*\. 

1. Verify that the `Seconds_Behind_Master` field in the [SHOW REPLICA STATUS](https://dev.mysql.com/doc/refman/8.0/en/show-replica-status.html) command results is 0, which indicates that the replica is up\-to\-date with the source replication instance\.

   ```
   SHOW REPLICA STATUS;
   ```
**Note**  
Previous versions of MySQL used `SHOW SLAVE STATUS` instead of `SHOW REPLICA STATUS`\. If you are using a MySQL version before 8\.0\.23, then use `SHOW SLAVE STATUS`\. 

   For a MariaDB 10\.5 or 10\.6 DB instance, run the [mysql\.rds\_replica\_status](mysql_rds_replica_status.md) procedure instead of the MySQL command\.

1. Close all connections to the source when their transactions complete\.

1. Update your application to use the Amazon RDS database\. This update typically involves changing the connection settings to identify the hostname and port of the Amazon RDS database, the user account and password to connect with, and the database to use\.

1. Connect to the DB instance\.

   For a Multi\-AZ DB cluster, connect to the writer DB instance\.

1. Stop replication for the Amazon RDS instance using the [mysql\.rds\_stop\_replication](mysql_rds_stop_replication.md) command\.

   ```
   CALL mysql.rds_stop_replication;
   ```

1. Run the [mysql\.rds\_reset\_external\_master](mysql_rds_reset_external_master.md) command on your Amazon RDS database to reset the replication configuration so this instance is no longer identified as a replica\.

   ```
   CALL mysql.rds_reset_external_master;
   ```

1. Turn on additional Amazon RDS features such as Multi\-AZ support and read replicas\. For more information, see [Multi\-AZ deployments for high availability](Concepts.MultiAZ.md) and [Working with read replicas](USER_ReadRepl.md)\.