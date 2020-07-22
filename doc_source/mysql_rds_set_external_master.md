# mysql\.rds\_set\_external\_master<a name="mysql_rds_set_external_master"></a>

Configures a MySQL DB instance to be a read replica of an instance of MySQL running external to Amazon RDS\.

**Important**  
To run this procedure, `autocommit` must be enabled\. To enable it, set the `autocommit` parameter to `1`\. For information about modifying parameters, see [Modifying Parameters in a DB Parameter Group](USER_WorkingWithParamGroups.md#USER_WorkingWithParamGroups.Modifying)\.

**Note**  
You can use the [mysql\.rds\_set\_external\_master\_with\_delay](mysql_rds_set_external_master_with_delay.md) stored procedure to configure an external source database instance and delayed replication\.

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
The host name or IP address of the MySQL instance running external to Amazon RDS to become the source database instance\.

 *host\_port*   
The port used by the MySQL instance running external to Amazon RDS to be configured as the source database instance\. If your network configuration includes Secure Shell \(SSH\) port replication that converts the port number, specify the port number that is exposed by SSH\.

 *replication\_user\_name*   
The ID of a user with `REPLICATION CLIENT` and `REPLICATION SLAVE` permissions on the MySQL instance running external to Amazon RDS\. We recommend that you provide an account that is used solely for replication with the external instance\.

 *replication\_user\_password*   
The password of the user ID specified in `replication_user_name`\.

 *mysql\_binary\_log\_file\_name*   
The name of the binary log on the source database instance that contains the replication information\.

 *mysql\_binary\_log\_file\_location*   
The location in the `mysql_binary_log_file_name` binary log at which replication starts reading the replication information\.

 *ssl\_encryption*   
A value that specifies whether Secure Socket Layer \(SSL\) encryption is used on the replication connection\. 1 specifies to use SSL encryption, 0 specifies to not use encryption\. The default is 0\.  
The `MASTER_SSL_VERIFY_SERVER_CERT` option isn't supported\. This option is set to 0, which means that the connection is encrypted, but the certificates aren't verified\.

## Usage Notes<a name="mysql_rds_set_external_master-usage-notes"></a>

 The master user must run the `mysql.rds_set_external_master` procedure\. This procedure must be run on the MySQL DB instance to be configured as the read replica of a MySQL instance running external to Amazon RDS\. 

 Before you run `mysql.rds_set_external_master`, you must configure the instance of MySQL running external to Amazon RDS to be a source database instance\. To connect to the MySQL instance running external to Amazon RDS, you must specify `replication_user_name` and `replication_user_password` values that indicate a replication user that has `REPLICATION CLIENT` and `REPLICATION SLAVE` permissions on the external instance of MySQL\. 

**To configure an external instance of MySQL as a source database instance**

1. Using the MySQL client of your choice, connect to the external instance of MySQL and create a user account to be used for replication\. The following is an example\.

   ```
   CREATE USER 'repl_user'@'mydomain.com' IDENTIFIED BY 'password'
   ```

1. On the external instance of MySQL, grant `REPLICATION CLIENT` and `REPLICATION SLAVE` privileges to your replication user\. The following example grants `REPLICATION CLIENT` and `REPLICATION SLAVE` privileges on all databases for the 'repl\_user' user for your domain\.

   ```
   GRANT REPLICATION CLIENT, REPLICATION SLAVE ON *.* TO 'repl_user'@'mydomain.com' 
   IDENTIFIED BY 'password'
   ```

To use encrypted replication, configure source database instance to use SSL connections\. Also, import the certificate authority certificate, client certificate, and client key into the DB instance or DB cluster using the [mysql\.rds\_import\_binlog\_ssl\_material](mysql_rds_import_binlog_ssl_material.md) procedure\.

**Note**  
We recommend that you use read replicas to manage replication between two Amazon RDS DB instances when possible\. When you do so, we recommend that you use only this and other replication\-related stored procedures\. These practices enable more complex replication topologies between Amazon RDS DB instances\. We offer these stored procedures primarily to enable replication with MySQL instances running external to Amazon RDS\. For information about managing replication between Amazon RDS DB instances, see [Working with Read Replicas](USER_ReadRepl.md)\.

After calling `mysql.rds_set_external_master` to configure an Amazon RDS DB instance as a read replica, you can call [mysql\.rds\_start\_replication](mysql_rds_start_replication.md) on the read replica to start the replication process\. You can call [mysql\.rds\_reset\_external\_master](mysql_rds_reset_external_master.md) to remove the read replica configuration\.

When `mysql.rds_set_external_master` is called, Amazon RDS records the time, user, and an action of `set master` in the `mysql.rds_history` and `mysql.rds_replication_status` tables\.

## Examples<a name="mysql_rds_set_external_master-examples"></a>

When run on a MySQL DB instance, the following example configures the DB instance to be a read replica of an instance of MySQL running external to Amazon RDS\.

```
call mysql.rds_set_external_master(
  'Externaldb.some.com',
  3306,
  'repl_user',
  'password',
  'mysql-bin-changelog.0777',
  120,
  0);
```