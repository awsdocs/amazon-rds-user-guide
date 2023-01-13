# Configuring GTID\-based replication with an external source instance<a name="MySQL.Procedural.Importing.External.Repl.GTIDProcedure"></a>

You can set up replication based on global transaction identifiers \(GTIDs\) from an external MySQL instance into an RDS for MySQL DB instance\. When you set up an external source instance and a replica on Amazon RDS, monitor failover events for the Amazon RDS DB instance that is your replica\. If a failover occurs, then the DB instance that is your replica might be recreated on a new host with a different network address\. For information on how to monitor failover events, see [Working with Amazon RDS event notification](USER_Events.md)\.

**Important**  
GTID\-based replication is supported only on RDS for MySQL version 5\.7\.23 and higher MySQL 5\.7 versions, and RDS for MySQL 8\.0\.26 and higher 8\.0 versions\.

**To configure GTID\-based replication with an external source instance**

1. Prepare for GTID\-based replication:

   1. Make sure that the external MySQL database has GTID\-based replication enabled\. To do so, make sure that the external database has the following parameters set to the specified values:

      `gtid_mode` – `ON`

      `enforce_gtid_consistency` – `ON`

      For more information, see [ Replication with global transaction identifiers](https://dev.mysql.com/doc/refman/8.0/en/replication-gtids.html) in the MySQL documentation\.

   1. Make sure that the parameter group associated with the DB instance has the following parameter settings:
      + `gtid_mode` – `ON`, `ON_PERMISSIVE`, or `OFF_PERMISSIVE`
      + `enforce_gtid_consistency` – `ON`

      For more information about parameter groups, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\.

   1. If you changed the parameter group of the DB instance, reboot the DB instance\. For more information, see [Rebooting a DB instance](USER_RebootInstance.md)\.

1. Make the source MySQL instance read\-only\.

   ```
   mysql> FLUSH TABLES WITH READ LOCK;
   mysql> SET GLOBAL read_only = ON;
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

   To specify the host name, user name, port, and password to connect to your Amazon RDS DB instance, use the `--host`, `--user (-u)`, `--port` and `-p` options in the `mysql` command\. The host name is the Domain Name System \(DNS\) name from the Amazon RDS DB instance endpoint, for example, `myinstance.123456789012.us-east-1.rds.amazonaws.com`\. You can find the endpoint value in the instance details in the AWS Management Console\.

1. Make the source MySQL instance writeable again\.

   ```
   mysql> SET GLOBAL read_only = OFF;
   mysql> UNLOCK TABLES;
   ```

   For more information on making backups for use with replication, see [the MySQL documentation](https://dev.mysql.com/doc/refman/8.0/en/replication-solutions-backups-read-only.html)\.

1. On the AWS Management Console, add the IP address of the server that hosts the external database to the virtual private cloud \(VPC\) security group for the Amazon RDS DB instance\. For more information on modifying a VPC security group, see [Security groups for your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html) in the *Amazon VPC User Guide*\. 

   The IP address can change when the following conditions are met:
   + You are using a public IP address for communication between the external source instance and the DB instance\.
   + The external source instance was stopped and restarted\.

   If these conditions are met, verify the IP address before adding it\.

   You might also need to configure your local network to permit connections from the IP address of your Amazon RDS DB instance\. You do this so that your local network can communicate with your external MySQL instance\. To find the IP address of the Amazon RDS DB instance, use the `host` command\.

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

1. Make the Amazon RDS DB instance the replica\. To do so, first connect to the Amazon RDS DB instance as the master user\. Then identify the external MySQL database as the replication primary instance by using the [mysql\.rds\_set\_external\_master\_with\_auto\_position](mysql_rds_set_external_master_with_auto_position.md) command\. The following is an example\.

   ```
   CALL mysql.rds_set_external_master_with_auto_position ('mymasterserver.mydomain.com', 3306, 'repl_user', 'password', 0, 0);
   ```
**Note**  
On RDS for MySQL, you can choose to use delayed replication by running the [mysql\.rds\_set\_external\_master\_with\_delay](mysql_rds_set_external_master_with_delay.md) stored procedure instead\. On RDS for MySQL, one reason to use delayed replication is to enable disaster recovery with the [mysql\.rds\_start\_replication\_until\_gtid](mysql_rds_start_replication_until_gtid.md) stored procedure\.

1. On the Amazon RDS DB instance, issue the [mysql\.rds\_start\_replication](mysql_rds_start_replication.md) command to start replication\.

   ```
   CALL mysql.rds_start_replication; 
   ```