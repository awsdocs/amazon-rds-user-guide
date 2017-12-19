# mysql\.rds\_set\_external\_master<a name="mysql_rds_set_external_master"></a>

Configures a MySQL DB instance to be a Read Replica of an instance of MySQL running external to Amazon RDS\.

## Syntax<a name="mysql_rds_set_external_master-syntax"></a>

```
CALL mysql.rds_set_external_master (
  host_name
  , host_port
  , replication_user_name
  , replication_user_password
  , mysql_binary_log_file_name
  , mysql_binary_log_file_location
  , ssl_encryption
);
```

## Parameters<a name="mysql_rds_set_external_master-parameters"></a>

 *host\_name*   
The host name or IP address of the MySQL instance running external to Amazon RDS that will become the replication master\.

 *host\_port*   
The port used by the MySQL instance running external to Amazon RDS to be configured as the replication master\. If your network configuration includes SSH port replication that converts the port number, specify the port number that is exposed by SSH\.

 *replication\_user\_name*   
The ID of a user with REPLICATION CLIENT and REPLICATION SLAVE permissions on the MySQL instance running external to Amazon RDS\. We recommend that you provide an account that is used solely for replication with the external instance\.

 *replication\_user\_password*   
The password of the user ID specified in `replication_user_name`\.

 *mysql\_binary\_log\_file\_name*   
The name of the binary log on the replication master contains the replication information\.

 *mysql\_binary\_log\_file\_location*   
The location in the `mysql_binary_log_file_name` binary log at which replication will start reading the replication information\.

 *ssl\_encryption*   
This option is not currently implemented\.  The default is 0\.

## Usage Notes<a name="mysql_rds_set_external_master-usage-notes"></a>

 The `mysql.rds_set_external_master` procedure must be run by the master user\. It must be run on the MySQL DB instance to be configured as the Read Replica of a MySQL instance running external to Amazon RDS\. 

 Before you run `mysql.rds_set_external_master`, you must configure the instance of MySQL running external to Amazon RDS to be a replication master\. To connect to the MySQL instance running external to Amazon RDS, you must specify `replication_user_name` and `replication_user_password` values that indicate a replication user that has `REPLICATION CLIENT` and `REPLICATION SLAVE` permissions on the external instance of MySQL\. 

**To configure an external instance of MySQL as a replication master**

1. Using the MySQL client of your choice, connect to the external instance of MySQL and create a user account to be used for replication\. The following is an example: 

   ```
   CREATE USER 'repl_user'@'mydomain.com' IDENTIFIED BY 'SomePassW0rd'
   ```

1. On the external instance of MySQL, grant `REPLICATION CLIENT` and `REPLICATION SLAVE` privileges to your replication user\. The following example grants `REPLICATION CLIENT` and `REPLICATION SLAVE` privileges on all databases for the 'repl\_user' user for your domain:

   ```
   GRANT REPLICATION CLIENT, REPLICATION SLAVE ON *.* TO 'repl_user'@'mydomain.com' 
   IDENTIFIED BY 'SomePassW0rd'
   ```

For more information, see [Replication with a MySQL or MariaDB Instance Running External to Amazon RDS](MySQL.Procedural.Importing.External.Repl.md)\.

**Note**  
We recommend that you use Read Replicas to manage replication between two Amazon RDS DB instances when possible, and only use this and other replication\-related stored procedures to enable more complex replication topologies between Amazon RDS DB instances\. These stored procedures are primarily offered to enable replication with MySQL instances running external to Amazon RDS\. For information about managing replication between Amazon RDS DB instances, see [Working with Read Replicas of MariaDB, MySQL, and PostgreSQL DB Instances](USER_ReadRepl.md)\.

After calling `mysql.rds_set_external_master` to configure an Amazon RDS DB instance as a Read Replica, you can call [mysql\.rds\_start\_replication](mysql_rds_start_replication.md) on the replica to start the replication process\. You can call [mysql\.rds\_reset\_external\_master](mysql_rds_reset_external_master.md) to remove the Read Replica configuration\.

When `mysql.rds_set_external_master` is called, Amazon RDS records the time, user, and an action of "set master" in the `mysql.rds_history` and `mysql.rds_replication_status` tables\.

The `mysql.rds_set_external_master` procedure is available in these versions of Amazon RDS MySQL:

+ MySQL 5\.5

+ MySQL 5\.6

+ MySQL 5\.7

## Examples<a name="mysql_rds_set_external_master-examples"></a>

When run on a MySQL DB instance, the following example configures the DB instance to be a Read Replica of an instance of MySQL running external to Amazon RDS\.

```
call mysql.rds_set_external_master(
  'Externaldb.some.com',
  3306,
  'repl_user'@'mydomain.com',
  'SomePassW0rd',
  'mysql-bin-changelog.0777',
  120,
  0);
```

## Related Topics<a name="mysql_rds_set_external_master.related"></a>

+ [mysql\.rds\_reset\_external\_master](mysql_rds_reset_external_master.md)

+ [mysql\.rds\_start\_replication](mysql_rds_start_replication.md)

+ [mysql\.rds\_stop\_replication](mysql_rds_stop_replication.md)