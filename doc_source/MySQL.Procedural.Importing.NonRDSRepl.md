# Importing Data to an Amazon RDS MySQL or MariaDB DB Instance with Reduced Downtime<a name="MySQL.Procedural.Importing.NonRDSRepl"></a>

If your scenario supports it, it is easier to move data in and out of Amazon RDS by using backup files and Amazon S3\. For more information, see [Restoring a Backup into an Amazon RDS MySQL DB Instance](MySQL.Procedural.Importing.md)\. 

In some cases, you might need to import data from an external MySQL or MariaDB database that supports a live application to an Amazon RDS MySQL or MariaDB DB instance\. In these cases, you can use the following procedure to minimize the impact on application availability\. This procedure can also help if you are working with a very large database\. Here, the procedure helps because you can reduce the cost of the import by reducing the amount of data that is passed across the network to AWS\.

In this procedure, you transfer a copy of your database data to an Amazon EC2 instance and import the data into a new Amazon RDS DB instance\. You then use replication to bring the Amazon RDS DB instance up\-to\-date with your live external instance, before redirecting your application to the Amazon RDS DB instance\. You configure MariaDB replication based on global transaction identifiers \(GTIDs\) if the external instance is MariaDB 10\.0\.2 or greater and the target instance is Amazon RDS MariaDB; otherwise, you configure replication based on binary log coordinates\. We recommend GTID\-based replication if your external database supports it due to its enhanced crash\-safety features\. For more information, see [Global Transaction ID](http://mariadb.com/kb/en/mariadb/global-transaction-id/) in the MariaDB documentation\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/MigrateMySQLToRDS_1.png)

**Note**  
We don't recommend that you use this procedure with source MySQL databases from MySQL versions earlier than version 5\.1, due to potential replication issues\. For more information, see [Replication Compatibility Between MySQL Versions](https://dev.mysql.com/doc/refman/8.0/en/replication-compatibility.html) in the MySQL documentation\.

## Create a Copy of Your Existing Database<a name="MySQL.Procedural.Importing.Copy.Database"></a>

The first step in the process of migrating a large amount of data to an Amazon RDS MySQL or MariaDB DB instance with minimal downtime is to create a copy of the source data\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/MigrateMySQLToRDS_2.png)

You can use the `mysqldump` utility to create a database backup in either SQL or delimited\-text format\. You should do a test run with each format in a nonproduction environment to see which method minimizes the amount of time that `mysqldump` runs\.

You should also weigh `mysqldump` performance against the benefit offered by using the delimited\-text format for loading\. A backup using delimited\-text format creates a tab\-separated text file for each table being dumped\. You can load these files in parallel using the `LOAD DATA LOCAL INFILE` command to reduce the amount of time required to import your database\. For more information about choosing a `mysqldump` format and then loading the data, see [ Using mysqldump For Backups](https://dev.mysql.com/doc/mysql-backup-excerpt/8.0/en/using-mysqldump.html) in the MySQL documentation\.

Before you start the backup operation, you must set the replication options on the MySQL or MariaDB database that you are copying to Amazon RDS\. The replication options include enabling binary logging and setting a unique server ID\. Setting these options causes your server to start logging database transactions and prepares it to be a source replication instance later in this process\.

**Note**  
Use the `--single-transaction` option with `mysqldump` because it dumps a consistent state of the database\. To ensure a valid dump file, don't run data definition language \(DDL\) statements while `mysqldump` is running\. You can schedule a maintenance window for these operations\.
Exclude the following schemas from the dump file: `sys`, `performance_schema`, and `information_schema`\. The `mysqldump` utility excludes these schemas by default\.
If you need to migrate users and privileges, consider using a tool that generates the data control language \(DCL\) for recreating them, such as the [pt\-show\-grants](https://www.percona.com/doc/percona-toolkit/LATEST/pt-show-grants.html) utility\.

### To Set Replication Options<a name="MySQL.Procedural.Importing.Repl.Setup.Procedure"></a>

1. Edit the my\.cnf file \(this file is usually under `/etc`\)\.

   ```
   sudo vi /etc/my.cnf
   ```

   Add the `log_bin` and `server_id` options to the `[mysqld]` section\. The `log_bin` option provides a file name identifier for binary log files\. The `server_id` option provides a unique identifier for the server in source\-replica relationships\.

   The following example shows the updated `[mysqld]` section of a my\.cnf file:

   ```
   [mysqld]
   log-bin=mysql-bin
   server-id=1
   ```

   For more information, see [the MySQL documentation](https://dev.mysql.com/doc/refman/8.0/en/replication-howto-masterbaseconfig.html)\.

1. Restart the `mysql` service\.

   ```
   sudo service mysqld restart
   ```

### To Create a Backup Copy of Your Existing Database<a name="MySQL.Procedural.Importing.Database.Backup.Procedure"></a>

1. Create a backup of your data using the `mysqldump` utility, specifying either SQL or delimited\-text format\.

   You must specify `--master-data=2` in order to create a backup file that can be used to start replication between servers\. For more information, see the [ mysqldump](https://dev.mysql.com/doc/refman/8.0/en/mysqldump.html#option_mysqldump_master-data) documentation\.

   To improve performance and ensure data integrity, use the `--order-by-primary` and `--single-transaction` options of `mysqldump`\.

   To avoid including the MySQL system database in the backup, do not use the `--all-databases` option with `mysqldump`\. For more information, see [ Creating a Data Snapshot Using mysqldump](https://dev.mysql.com/doc/mysql-replication-excerpt/8.0/en/replication-howto-mysqldump.html) in the MySQL documentation\.

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
         --fields-terminated-by ',' ^
         --fields-enclosed-by '"' ^
         --lines-terminated-by 0x0d0a ^
         database_name ^
         --master-data=2 ^
         --single-transaction ^
         --order-by-primary ^
         -p password
     ```
**Note**  
You must create any stored procedures, triggers, functions, or events manually in your Amazon RDS database\. If you have any of these objects in the database that you are copying, exclude them when you run `mysqldump` by including the following arguments with your `mysqldump` command: `--routines=0 --triggers=0 --events=0`\.

     When using the delimited\-text format, a `CHANGE MASTER TO` comment is returned when you run `mysqldump`\. This comment contains the master log file name and position\. If the external instance is other than MariaDB version 10\.0\.2 or greater, note the values for `MASTER_LOG_FILE` and `MASTER_LOG_POS`\. You need these values when setting up replication\.

     ```
     -- Position to start replication or point-in-time recovery from
     --
     -- CHANGE MASTER TO MASTER_LOG_FILE='mysql-bin-changelog.000031', MASTER_LOG_POS=107;
     ```

     If you are using SQL format, you can get the master log file name and position in step 4 of the procedure at [Replicate Between Your External Database and New Amazon RDS DB Instance](#MySQL.Procedural.Importing.Start.Repl)\. If the external instance is MariaDB version 10\.0\.2 or greater, you can get the GTID in the next step\.

1. If the external instance you are using is MariaDB version 10\.0\.2 or greater, you use GTID\-based replication\. Run `SHOW MASTER STATUS` on the external MariaDB instance to get the binary log file name and position, then convert them to a GTID by running `BINLOG_GTID_POS` on the external MariaDB instance\.

   ```
   SELECT BINLOG_GTID_POS('binary log file name', binary log file position);
   ```

   Note the GTID returned; you need it to configure replication\.

1. Compress the copied data to reduce the amount of network resources needed to copy your data to the Amazon RDS DB instance\. Take note of the size of the backup file; you need this information when determining how large an Amazon EC2 instance to create\. When you are done, compress the backup file using GZIP or your preferred compression utility\. 
   + To compress SQL output, use the following command\.

     ```
     gzip backup.sql
     ```
   + To compress delimited\-text output, use the following command\.

     ```
     tar -zcvf backup.tar.gz target_directory
     ```

## Create an Amazon EC2 Instance and Copy the Compressed Database<a name="MySQL.Procedural.Importing.Import.Database"></a>

Copying your compressed database backup file to an Amazon EC2 instance takes fewer network resources than doing a direct copy of uncompressed data between database instances\. After your data is in Amazon EC2, you can copy it from there directly to your Amazon RDS MySQL or MariaDB DB instance\. For you to save on the cost of network resources, your Amazon EC2 instance must be in the same AWS Region as your Amazon RDS DB instance\. Having the Amazon EC2 instance in the same AWS Region as your Amazon RDS DB instance also reduces network latency during the import\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/MigrateMySQLToRDS_3.png)

### To Create an Amazon EC2 Instance and Copy Your Data<a name="MySQL.Procedural.Importing.Create.EC2"></a>

1. In the AWS Region where you plan to create the RDS DB instance to run your MySQL database engine, create a VPC, a VPC security group, and a VPC subnet\. Ensure that the inbound rules for your VPC security group allow the IP addresses required for your application to connect to AWS\. This can be a range of IP addresses \(for example, `203.0.113.0/24`\), or another VPC security group\. You can use the [Amazon VPC Management Console](https://console.aws.amazon.com/vpc) to create and manage VPCs, subnets, and security groups\. For more information, see [Getting Started with Amazon VPC](https://docs.aws.amazon.com/AmazonVPC/latest/GettingStartedGuide/GetStarted.html) in the *Amazon Virtual Private Cloud Getting Started Guide*\.
**Note**  
Older AWS accounts can also launch instances in Amazon EC2\-Classic mode\. In this case, make sure that the inbound rules in the DB security group for your Amazon RDS instance allow access for your EC2\-Classic instance using the Amazon EC2 private IP address\. For more information, see [Working with DB Security Groups \(EC2\-Classic Platform\)](USER_WorkingWithSecurityGroups.md)\.

1. Open the [Amazon EC2 Management Console](https://console.aws.amazon.com/ec2) and select the AWS Region to contain both your Amazon EC2 instance and your Amazon RDS DB instance\. Launch an Amazon EC2 instance using the VPC, subnet, and security group that you created in Step 1\. Ensure that you select an instance type with enough storage for your database backup file when it is uncompressed\. For details on Amazon EC2 instances, see [Getting Started with Amazon EC2 Linux Instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EC2_GetStarted.html) in the *Amazon Elastic Compute Cloud User Guide for Linux*\.

1.  To connect to your Amazon RDS DB instance from your Amazon EC2 instance, you need to edit your VPC security group, and add an inbound rule specifying the private IP address of your EC2 instance\. You can find the private IP address on the **Details** tab of the **Instance** pane in the EC2 console window\. To edit the VPC security group and add an inbound rule, choose **Security Groups** in the EC2 console navigation pane, choose your security group, and then add an inbound rule for MySQL/Aurora specifying the private IP address of your EC2 instance\. To learn how to add an inbound rule to a VPC security group, see [Adding and Removing Rules](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html#AddRemoveRules)\.

1. Copy your compressed database backup file from your local system to your Amazon EC2 instance\. Use `chmod` if necessary to make sure you have write permission for the target directory of the Amazon EC2 instance\. You can use `scp` or an SSH client to copy the file\. The following is an example:

   ```
   $ scp -r -i key pair.pem backup.sql.gz ec2-user@EC2 DNS:/target_directory/backup.sql.gz
   ```
**Important**  
Be sure to copy sensitive data using a secure network transfer protocol\.

1. Connect to your Amazon EC2 instance and install the latest updates and the MySQL client tools using the following commands:

   ```
   sudo yum update -y
   sudo yum install mysql -y
   ```

   For more information, see [Connect to Your Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-connect-to-instance-linux.html) in the *Amazon Elastic Compute Cloud User Guide for Linux*\.

1. While connected to your Amazon EC2 instance, decompress your database backup file\. For example:
   + To decompress SQL output, use the following command:

     ```
     gzip backup.sql.gz -d
     ```
   + To decompress delimited\-text output, use the following command:

     ```
     tar xzvf backup.tar.gz
     ```

## Create an Amazon RDS MySQL or MariaDB DB instance and Import Data from Your Amazon EC2 Instance<a name="MySQL.Procedural.Importing.Create.RDS.Database"></a>

By creating an Amazon RDS MySQL or MariaDB DB instance in the same AWS Region as your Amazon EC2 instance, you can import the database backup file from EC2 faster than over the internet\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/MigrateMySQLToRDS_4.png)

### To Create an Amazon RDS MySQL or MariaDB DB Instance and Import Your Data<a name="MySQL.Procedural.Importing.Create.RDS.Database.Procedure"></a>

****

1. Determine which DB instance class and what amount of storage space is required to support the expected workload for this Amazon RDS DB instance\. As part of this process, decide what is sufficient space and processing capacity for your data load procedures, and also what is required to handle the production workload\. You can estimate this based on the size and resources of the source MySQL or MariaDB database\. For more information, see [DB Instance Classes](Concepts.DBInstanceClass.md)\.

1. Determine if Amazon RDS provisioned input/output operations per second \(IOPS\) is required to support the workloads\. Provisioned IOPS storage delivers fast throughput for online transaction processing \(OLTP\) workloads, which are I/O intensive\. For more information, see [Provisioned IOPS SSD Storage](CHAP_Storage.md#USER_PIOPS)\.

1. Open the [Amazon RDS console](https://console.aws.amazon.com/rds/)\. In the upper\-right corner, choose the AWS Region that contains your Amazon EC2 instance\.

1. In the navigation pane, choose **Databases**\.

1. Choose **Create database**, and then go through the steps to choose options for your DB instance:

   1. Make sure that **Standard Create** is chosen\.

   1. In the **Engine options** section, choose **MySQL** or **MariaDB**, as appropriate\.

   1. For **Version**, choose the version that is compatible with your source MySQL instance, as follows:
      + If your source instance is MySQL 5\.1\.x, the Amazon RDS DB instance must be MySQL 5\.5\.x\.
      + If your source instance is MySQL 5\.5\.x, the Amazon RDS DB instance must be MySQL 5\.5\.x or greater\.
      + If your source instance is MySQL 5\.6\.x, the Amazon RDS DB instance must be MySQL 5\.6\.x or MariaDB\.
      + If your source instance is MySQL 5\.7\.x, the Amazon RDS DB instance must be MySQL 5\.7\.x, 5\.6\.x, or MariaDB\.
      + If your source instance is MySQL 8\.0\.x, the Amazon RDS DB instance must be MySQL 8\.0\.x\.
      + If your source instance is MariaDB 5\.1, 5\.2, or 5\.3, the Amazon RDS DB instance must be MySQL 5\.1\.x\.
      + If your source instance is MariaDB 5\.5 or greater, the Amazon RDS DB instance must be MariaDB\.

   1. In the **Templates** section, choose **Dev/Test** to skip configuring Multi\-AZ deployment and provisioned IOPS storage\.

   1. In the **Settings** section, specify the requested **DB instance identifier** and user information\.

   1. In the **DB instance class** and **Storage** sections, specify the DB instance class and allocated storage size that you want\.

   1. In the **Availability & durability** section, choose **Do not create a standby instance** for **Multi\-AZ deployment**\.

   1. In the **Connectivity** section, choose the same virtual private cloud \(VPC\) and VPC security group as for your Amazon EC2 instance\. This approach ensures that your Amazon EC2 instance and your Amazon RDS instance are visible to each other over the network\. Set **Publicly accessible** to **Yes**\. To set up replication with your source database as described later, your DB instance must be publicly accessible\.

      Use the default values for the other settings in this section\.

      In the **Backup** section, set the backup retention period to **0 days**\. 

      Use the default values for the other settings in this section\.

   1. Open the **Additional configuration** section, and specify an **Initial database name**\.

      Set the **Backup retention period** to **0 days**

      Use the default values for the other settings in this section\.

   1. Choose **Create database**\.

      Your new DB instance appears in the **Databases** list with the status **Creating**\. Wait for the **Status** of your new DB instance to show as **Available**\.

   Don't configure multiple Availability Zones, backup retention, or read replicas until after you have imported the database backup\. When that import is done, you can set Multi\-AZ and backup retention the way you want them for the production instance\. For a detailed walkthrough of creating a DB instance, see [Creating an Amazon RDS DB Instance](USER_CreateDBInstance.md)\.

1. Review the default configuration options for the Amazon RDS DB instance\. In the RDS console navigation pane, choose **Parameter groups**, and then choose the magnifying glass icon next to the **default\.mysqlx\.x** or **default\.mariadbx\.x** parameter group\. If this parameter group doesn't have the configuration options that you want, find a different one that does or create a new parameter group\. For more information on creating a parameter group, see [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)\. 

   If you decide to use a different parameter group than the default, associate it with your Amazon RDS DB instance\. For more information, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\.

1. Connect to the new Amazon RDS DB instance as the master user, and create the users required to support the administrators, applications, and services that need to access the instance\. The host name for the Amazon RDS DB instance is the **Endpoint** value for this instance without including the port number, for example `mysampledb.claxc2oy9ak1.us-west-2.rds.amazonaws.com`\. You can find the endpoint value in the instance details in the Amazon RDS Management Console\.

1. Connect to your Amazon EC2 instance\. For more information, see [Connect to Your Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-connect-to-instance-linux.html) in the *Amazon Elastic Compute Cloud User Guide for Linux*\.

1. Connect to your Amazon RDS DB instance as a remote host from your Amazon EC2 instance using the `mysql` command\. The following is an example\.

   ```
   mysql -h host_name -P 3306 -u db_master_user -p
   ```

   The host name is the DNS name from the Amazon RDS DB instance endpoint\.

1. At the `mysql` prompt, run the `source` command and pass it the name of your database dump file to load the data into the Amazon RDS DB instance:
   + For SQL format, use the following command\.

     ```
     mysql> source backup.sql;
     ```
   + For delimited\-text format, first create the database \(if it isn't the default database you created when setting up the Amazon RDS DB instance\)\.

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

     To improve performance, you can perform these operations in parallel from multiple connections so that all of your tables get created and then loaded at the same time\.
**Note**  
If you used any data\-formatting options with `mysqldump` when you initially dumped the table, you must use the same options with `mysqlimport` or LOAD DATA LOCAL INFILE to ensure proper interpretation of the data file contents\.

1. Run a simple SELECT query against one or two of the tables in the imported database to verify that the import was successful\.

**Note**  
 If you no longer need the Amazon EC2 instance used in this procedure, terminate the EC2 instance to reduce your AWS resource usage\. To terminate an EC2 instance, see [Terminating an Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/terminating-instances.html#terminating-instances-console)\. 

## Replicate Between Your External Database and New Amazon RDS DB Instance<a name="MySQL.Procedural.Importing.Start.Repl"></a>

Your source database was likely updated during the time that it took to copy and transfer the data to the Amazon RDS MySQL or MariaDB DB instance\. That being the case, you can use replication to bring the copied database up\-to\-date with the source database\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/MigrateMySQLToRDS_5.png)

**Note**  
The permissions required to start replication on an Amazon RDS DB instance are restricted and not available to your Amazon RDS master user\. Because of this, you must use either the Amazon RDS [mysql\.rds\_set\_external\_master](mysql_rds_set_external_master.md) command or the [mysql\.rds\_set\_external\_master\_gtid](mysql_rds_set_external_master_gtid.md) command to configure replication, and the [mysql\.rds\_start\_replication](mysql_rds_start_replication.md) command to start replication between your live database and your Amazon RDS database\.

### To Start Replication<a name="MySQL.Procedural.Importing.Start.Repl.Procedure"></a>

Earlier, you enabled binary logging and set a unique server ID for your source database\. Now you can set up your Amazon RDS DB instance as a replica with your live database as the source replication instance\.

1. In the Amazon RDS Management Console, add the IP address of the server that hosts the source database to the VPC security group for the Amazon RDS DB instance\. For more information on modifying a VPC security group, see [Security Groups for Your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html) in the *Amazon Virtual Private Cloud User Guide*\. 

   You might also need to configure your local network to permit connections from the IP address of your Amazon RDS DB instance, so that it can communicate with your source instance\. To find the IP address of the Amazon RDS DB instance, use the `host` command\.

   ```
   host db_instance_endpoint
   ```

   The host name is the DNS name from the Amazon RDS DB instance endpoint, for example `myinstance.123456789012.us-east-1.rds.amazonaws.com`\. You can find the endpoint value in the instance details in the Amazon RDS Management Console\.

1. Using the client of your choice, connect to the source instance and create a user to be used for replication\. This account is used solely for replication and must be restricted to your domain to improve security\. The following is an example\.

   ```
   CREATE USER 'repl_user'@'mydomain.com' IDENTIFIED BY 'password';
   ```

1. For the source instance, grant `REPLICATION CLIENT` and `REPLICATION SLAVE` privileges to your replication user\. For example, to grant the `REPLICATION CLIENT` and `REPLICATION SLAVE` privileges on all databases for the '`repl_user`' user for your domain, issue the following command\.

   ```
   GRANT REPLICATION CLIENT, REPLICATION SLAVE ON *.* TO 'repl_user'@'mydomain.com' IDENTIFIED BY 'password';
   ```

1. If you used SQL format to create your backup file and the external instance is not MariaDB 10\.0\.2 or greater, look at the contents of that file\.

   ```
   cat backup.sql
   ```

   The file includes a `CHANGE MASTER TO` comment that contains the master log file name and position\. This comment is included in the backup file when you use the `--master-data` option with `mysqldump`\. Note the values for `MASTER_LOG_FILE` and `MASTER_LOG_POS`\.

   ```
   --
   -- Position to start replication or point-in-time recovery from
   --
   
   -- CHANGE MASTER TO MASTER_LOG_FILE='mysql-bin-changelog.000031', MASTER_LOG_POS=107;
   ```

   If you used delimited text format to create your backup file and the external instance is not MariaDB 10\.0\.2 or greater, you should already have binary log coordinates from step 1 of the procedure at [To Create a Backup Copy of Your Existing Database](#MySQL.Procedural.Importing.Database.Backup.Procedure)\.

   If the external instance is MariaDB 10\.0\.2 or greater, you should already have the GTID from which to start replication from step 2 of the procedure at [To Create a Backup Copy of Your Existing Database](#MySQL.Procedural.Importing.Database.Backup.Procedure)\.

1. Make the Amazon RDS DB instance the replica\. If the external instance is not MariaDB 10\.0\.2 or greater, connect to the Amazon RDS DB instance as the master user and identify the source database as the source replication instance by using the [mysql\.rds\_set\_external\_master](mysql_rds_set_external_master.md) command\. Use the master log file name and master log position that you determined in the previous step if you have a SQL format backup file\. Alternatively, use the name and position that you determined when creating the backup files if you used delimited\-text format\. The following is an example\.

   ```
   CALL mysql.rds_set_external_master ('myserver.mydomain.com', 3306,
       'repl_user', 'password', 'mysql-bin-changelog.000031', 107, 0);
   ```

   If the external instance is MariaDB 10\.0\.2 or greater, connect to the Amazon RDS DB instance as the master user and identify the source database as the source replication instance by using the [mysql\.rds\_set\_external\_master\_gtid](mysql_rds_set_external_master_gtid.md) command\. Use the GTID that you determined in step 2 of the procedure at [To Create a Backup Copy of Your Existing Database](#MySQL.Procedural.Importing.Database.Backup.Procedure)\. The following is an example\.

   ```
   CALL mysql.rds_set_external_master_gtid ('source_server_ip_address', 3306, 'ReplicationUser', 'password', 'GTID', 0); 
   ```

   The `source_server_ip_address` is the IP address of source replication instance\. An EC2 private DNS address is currently not supported\.

1. On the Amazon RDS DB instance, issue the [mysql\.rds\_start\_replication](mysql_rds_start_replication.md) command to start replication\.

   ```
   CALL mysql.rds_start_replication;
   ```

1. On the Amazon RDS DB instance, run the [SHOW SLAVE STATUS](https://dev.mysql.com/doc/refman/8.0/en/show-slave-status.html) command to determine when the replica is up\-to\-date with the source replication instance\. The results of the `SHOW SLAVE STATUS` command include the `Seconds_Behind_Master` field\. When the `Seconds_Behind_Master` field returns 0, then the replica is up\-to\-date with the source replication instance\.

1. After the Amazon RDS DB instance is up\-to\-date, enable automated backups so you can restore that database if needed\. You can enable or modify automated backups for your Amazon RDS DB instance using the [Amazon RDS Management Console](https://console.aws.amazon.com/rds/)\. For more information, see [Working With Backups](USER_WorkingWithAutomatedBackups.md)\.

## Redirect Your Live Application to Your Amazon RDS Instance<a name="MySQL.Procedural.Importing.Redirect.App"></a>

After the Amazon RDS MySQL or MariaDB DB instance is up\-to\-date with the source replication instance, you can now update your live application to use the Amazon RDS instance\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/MigrateMySQLToRDS_6.png)

### To Redirect Your Live Application to Your Amazon RDS MySQL or MariaDB DB Instance and Stop Replication<a name="MySQL.Procedural.Importing.Redirect.App.Procedure"></a>

1. To add the VPC security group for the Amazon RDS DB instance, add the IP address of the server that hosts the application\. For more information on modifying a VPC security group, see [Security Groups for Your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html) in the *Amazon Virtual Private Cloud User Guide*\. 

1. Verify that the `Seconds_Behind_Master` field in the [SHOW SLAVE STATUS](https://dev.mysql.com/doc/refman/8.0/en/show-slave-status.html) command results is 0, which indicates that the replica is up\-to\-date with the source replication instance\.

   ```
   SHOW SLAVE STATUS;
   ```

1. Close all connections to the source when their transactions complete\.

1. Update your application to use the Amazon RDS DB instance\. This update typically involves changing the connection settings to identify the host name and port of the Amazon RDS DB instance, the user account and password to connect with, and the database to use\.

1. Stop replication for the Amazon RDS instance using the [mysql\.rds\_stop\_replication](mysql_rds_stop_replication.md) command\.

   ```
   CALL mysql.rds_stop_replication;
   ```

1. Run the [mysql\.rds\_reset\_external\_master](mysql_rds_reset_external_master.md) command on your Amazon RDS DB instance to reset the replication configuration so this instance is no longer identified as a replica\.

   ```
   CALL mysql.rds_reset_external_master;
   ```

1. Enable additional Amazon RDS features such as Multi\-AZ support and read replicas\. For more information, see [High Availability \(Multi\-AZ\) for Amazon RDS](Concepts.MultiAZ.md) and [Working with Read Replicas](USER_ReadRepl.md)\.

**Note**  
 If you no longer need the Amazon RDS instance used in this procedure, you should delete the RDS instance to reduce your Amazon AWS resource usage\. To delete an RDS instance, see [Deleting a DB Instance](USER_DeleteInstance.md)\. 