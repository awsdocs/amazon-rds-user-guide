# Replicating<a name="mysql-stored-proc-replicating"></a>

The following stored procedures control how transactions are replicated from an external database into RDS for MySQL, or from RDS for MySQL to an external database\. To learn how to use replication based on global transaction identifiers \(GTIDs\) with RDS for MySQL, see [Using GTID\-based replication for Amazon RDS for MySQL](mysql-replication-gtid.md)\.

**Topics**
+ [mysql\.rds\_import\_binlog\_ssl\_material](#mysql_rds_import_binlog_ssl_material)
+ [mysql\.rds\_next\_master\_log](#mysql_rds_next_master_log)
+ [mysql\.rds\_remove\_binlog\_ssl\_material](#mysql_rds_remove_binlog_ssl_material)
+ [mysql\.rds\_reset\_external\_master](#mysql_rds_reset_external_master)
+ [mysql\.rds\_set\_external\_master](#mysql_rds_set_external_master)
+ [mysql\.rds\_set\_external\_master\_with\_auto\_position](#mysql_rds_set_external_master_with_auto_position)
+ [mysql\.rds\_set\_external\_master\_with\_delay](#mysql_rds_set_external_master_with_delay)
+ [mysql\.rds\_set\_master\_auto\_position](#mysql_rds_set_master_auto_position)
+ [mysql\.rds\_set\_source\_delay](#mysql_rds_set_source_delay)
+ [mysql\.rds\_skip\_transaction\_with\_gtid](#mysql_rds_skip_transaction_with_gtid)
+ [mysql\.rds\_skip\_repl\_error](#mysql_rds_skip_repl_error)
+ [mysql\.rds\_start\_replication](#mysql_rds_start_replication)
+ [mysql\.rds\_start\_replication\_until](#mysql_rds_start_replication_until)
+ [mysql\.rds\_start\_replication\_until\_gtid](#mysql_rds_start_replication_until_gtid)
+ [mysql\.rds\_stop\_replication](#mysql_rds_stop_replication)

## mysql\.rds\_import\_binlog\_ssl\_material<a name="mysql_rds_import_binlog_ssl_material"></a>

Imports the certificate authority certificate, client certificate, and client key into an RDS for MySQL DB instance\. The information is required for SSL communication and encrypted replication\.

### Syntax<a name="mysql_rds_import_binlog_ssl_material-syntax"></a>

 

```
CALL mysql.rds_import_binlog_ssl_material (
  ssl_material
);
```

### Parameters<a name="mysql_rds_import_binlog_ssl_material-parameters"></a>

 *ssl\_material*   
JSON payload that contains the contents of the following \.pem format files for a MySQL client:  
+ "ssl\_ca":"*Certificate authority certificate*"
+ "ssl\_cert":"*Client certificate*"
+ "ssl\_key":"*Client key*"

### Usage notes<a name="mysql_rds_import_binlog_ssl_material-usage-notes"></a>

Prepare for encrypted replication before you run this procedure:
+ If you don't have SSL enabled on the external MySQL source database instance and don't have a client key and client certificate prepared, enable SSL on the MySQL database server and generate the required client key and client certificate\.
+ If SSL is enabled on the external source database instance, supply a client key and certificate for the RDS for MySQL DB instance\. If you don't have these, generate a new key and certificate for the RDS for MySQL DB instance\. To sign the client certificate, you must have the certificate authority key you used to configure SSL on the external MySQL source database instance\.

For more information, see [ Creating SSL certificates and keys using openssl](https://dev.mysql.com/doc/refman/8.0/en/creating-ssl-files-using-openssl.html) in the MySQL documentation\.

**Important**  
After you prepare for encrypted replication, use an SSL connection to run this procedure\. The client key must not be transferred across an insecure connection\. 

This procedure imports SSL information from an external MySQL database into an RDS for MySQL DB instance\. The SSL information is in \.pem format files that contain the SSL information for the RDS for MySQL DB instance\. During encrypted replication, the RDS for MySQL DB instance acts a client to the MySQL database server\. The certificates and keys for the RDS for MySQL client are in files in \.pem format\.

You can copy the information from these files into the `ssl_material` parameter in the correct JSON payload\. To support encrypted replication, import this SSL information into the RDS for MySQL DB instance\.

The JSON payload must be in the following format\.

```
'{"ssl_ca":"-----BEGIN CERTIFICATE-----
ssl_ca_pem_body_code
-----END CERTIFICATE-----\n","ssl_cert":"-----BEGIN CERTIFICATE-----
ssl_cert_pem_body_code
-----END CERTIFICATE-----\n","ssl_key":"-----BEGIN RSA PRIVATE KEY-----
ssl_key_pem_body_code
-----END RSA PRIVATE KEY-----\n"}'
```

### Examples<a name="mysql_rds_import_binlog_ssl_material-examples"></a>

The following example imports SSL information into an RDS for MySQL DB instance\. In \.pem format files, the body code typically is longer than the body code shown in the example\.

```
call mysql.rds_import_binlog_ssl_material(
'{"ssl_ca":"-----BEGIN CERTIFICATE-----
AAAAB3NzaC1yc2EAAAADAQABAAABAQClKsfkNkuSevGj3eYhCe53pcjqP3maAhDFcvBS7O6V
hz2ItxCih+PnDSUaw+WNQn/mZphTk/a/gU8jEzoOWbkM4yxyb/wB96xbiFveSFJuOp/d6RJhJOI0iBXr
lsLnBItntckiJ7FbtxJMXLvvwJryDUilBMTjYtwB+QhYXUMOzce5Pjz5/i8SeJtjnV3iAoG/cQk+0FzZ
qaeJAAHco+CY/5WrUBkrHmFJr6HcXkvJdWPkYQS3xqC0+FmUZofz221CBt5IMucxXPkX4rWi+z7wB3Rb
BQoQzd8v7yeb7OzlPnWOyN0qFU0XA246RA8QFYiCNYwI3f05p6KLxEXAMPLE
-----END CERTIFICATE-----\n","ssl_cert":"-----BEGIN CERTIFICATE-----
AAAAB3NzaC1yc2EAAAADAQABAAABAQClKsfkNkuSevGj3eYhCe53pcjqP3maAhDFcvBS7O6V
hz2ItxCih+PnDSUaw+WNQn/mZphTk/a/gU8jEzoOWbkM4yxyb/wB96xbiFveSFJuOp/d6RJhJOI0iBXr
lsLnBItntckiJ7FbtxJMXLvvwJryDUilBMTjYtwB+QhYXUMOzce5Pjz5/i8SeJtjnV3iAoG/cQk+0FzZ
qaeJAAHco+CY/5WrUBkrHmFJr6HcXkvJdWPkYQS3xqC0+FmUZofz221CBt5IMucxXPkX4rWi+z7wB3Rb
BQoQzd8v7yeb7OzlPnWOyN0qFU0XA246RA8QFYiCNYwI3f05p6KLxEXAMPLE
-----END CERTIFICATE-----\n","ssl_key":"-----BEGIN RSA PRIVATE KEY-----
AAAAB3NzaC1yc2EAAAADAQABAAABAQClKsfkNkuSevGj3eYhCe53pcjqP3maAhDFcvBS7O6V
hz2ItxCih+PnDSUaw+WNQn/mZphTk/a/gU8jEzoOWbkM4yxyb/wB96xbiFveSFJuOp/d6RJhJOI0iBXr
lsLnBItntckiJ7FbtxJMXLvvwJryDUilBMTjYtwB+QhYXUMOzce5Pjz5/i8SeJtjnV3iAoG/cQk+0FzZ
qaeJAAHco+CY/5WrUBkrHmFJr6HcXkvJdWPkYQS3xqC0+FmUZofz221CBt5IMucxXPkX4rWi+z7wB3Rb
BQoQzd8v7yeb7OzlPnWOyN0qFU0XA246RA8QFYiCNYwI3f05p6KLxEXAMPLE
-----END RSA PRIVATE KEY-----\n"}');
```

## mysql\.rds\_next\_master\_log<a name="mysql_rds_next_master_log"></a>

Changes the source database instance log position to the start of the next binary log on the source database instance\. Use this procedure only if you are receiving replication I/O error 1236 on a read replica\.

### Syntax<a name="mysql_rds_next_master_log-syntax"></a>

 

```
CALL mysql.rds_next_master_log(
curr_master_log
);
```

### Parameters<a name="mysql_rds_next_master_log-parameters"></a>

 *curr\_master\_log*   
The index of the current master log file\. For example, if the current file is named `mysql-bin-changelog.012345`, then the index is 12345\. To determine the current master log file name, run the `SHOW REPLICA STATUS` command and view the `Master_Log_File` field\.  
Previous versions of MySQL used `SHOW SLAVE STATUS` instead of `SHOW REPLICA STATUS`\. If you are using a MySQL version before 8\.0\.23, then use `SHOW SLAVE STATUS`\. 

### Usage notes<a name="mysql_rds_next_master_log-usage-notes"></a>

The master user must run the `mysql.rds_next_master_log` procedure\. 

**Warning**  
Call `mysql.rds_next_master_log` only if replication fails after a failover of a Multi\-AZ DB instance that is the replication source, and the `Last_IO_Errno` field of `SHOW REPLICA STATUS` reports I/O error 1236\.  
Calling `mysql.rds_next_master_log` can result in data loss in the read replica if transactions in the source instance were not written to the binary log on disk before the failover event occurred\.  
You can reduce the chance of this happening by setting the source instance parameters `sync_binlog` and `innodb_support_xa` to `1`, although this might reduce performance\. For more information, see [Troubleshooting a MySQL read replica problem](USER_MySQL.Replication.ReadReplicas.md#USER_ReadRepl.Troubleshooting)\.

### Examples<a name="mysql_rds_next_master_log-examples"></a>

Assume replication fails on an RDS for MySQL read replica\. Running `SHOW REPLICA STATUS\G` on the read replica returns the following result:

```
*************************** 1. row ***************************
             Replica_IO_State:
                  Source_Host: myhost.XXXXXXXXXXXXXXX.rr-rrrr-1.rds.amazonaws.com
                  Source_User: MasterUser
                  Source_Port: 3306
                Connect_Retry: 10
              Source_Log_File: mysql-bin-changelog.012345
          Read_Source_Log_Pos: 1219393
               Relay_Log_File: relaylog.012340
                Relay_Log_Pos: 30223388
        Relay_Source_Log_File: mysql-bin-changelog.012345
           Replica_IO_Running: No
          Replica_SQL_Running: Yes
              Replicate_Do_DB:
          Replicate_Ignore_DB:
           Replicate_Do_Table:
       Replicate_Ignore_Table:
      Replicate_Wild_Do_Table:
  Replicate_Wild_Ignore_Table:
                   Last_Errno: 0
                   Last_Error:
                 Skip_Counter: 0
          Exec_Source_Log_Pos: 30223232
              Relay_Log_Space: 5248928866
              Until_Condition: None
               Until_Log_File:
                Until_Log_Pos: 0
           Source_SSL_Allowed: No
           Source_SSL_CA_File:
           Source_SSL_CA_Path:
              Source_SSL_Cert:
            Source_SSL_Cipher:
               Source_SSL_Key:
        Seconds_Behind_Master: NULL
Source_SSL_Verify_Server_Cert: No
                Last_IO_Errno: 1236
                Last_IO_Error: Got fatal error 1236 from master when reading data from binary log: 'Client requested master to start replication from impossible position; the first event 'mysql-bin-changelog.013406' at 1219393, the last event read from '/rdsdbdata/log/binlog/mysql-bin-changelog.012345' at 4, the last byte read from '/rdsdbdata/log/binlog/mysql-bin-changelog.012345' at 4.'
               Last_SQL_Errno: 0
               Last_SQL_Error:
  Replicate_Ignore_Server_Ids:
             Source_Server_Id: 67285976
```

The `Last_IO_Errno` field shows that the instance is receiving I/O error 1236\. The `Master_Log_File` field shows that the file name is `mysql-bin-changelog.012345`, which means that the log file index is `12345`\. To resolve the error, you can call `mysql.rds_next_master_log` with the following parameter:

```
CALL mysql.rds_next_master_log(12345);
```

**Note**  
Previous versions of MySQL used `SHOW SLAVE STATUS` instead of `SHOW REPLICA STATUS`\. If you are using a MySQL version before 8\.0\.23, then use `SHOW SLAVE STATUS`\. 

## mysql\.rds\_remove\_binlog\_ssl\_material<a name="mysql_rds_remove_binlog_ssl_material"></a>

Removes the certificate authority certificate, client certificate, and client key for SSL communication and encrypted replication\. This information is imported by using [mysql\.rds\_import\_binlog\_ssl\_material](#mysql_rds_import_binlog_ssl_material)\.

### Syntax<a name="mysql_rds_remove_binlog_ssl_material-syntax"></a>

 

```
CALL mysql.rds_remove_binlog_ssl_material;
```

## mysql\.rds\_reset\_external\_master<a name="mysql_rds_reset_external_master"></a>

Reconfigures an RDS for MySQL DB instance to no longer be a read replica of an instance of MySQL running external to Amazon RDS\.

**Important**  
To run this procedure, `autocommit` must be enabled\. To enable it, set the `autocommit` parameter to `1`\. For information about modifying parameters, see [Modifying parameters in a DB parameter group](USER_WorkingWithDBInstanceParamGroups.md#USER_WorkingWithParamGroups.Modifying)\.

### Syntax<a name="mysql_rds_reset_external_master-syntax"></a>

 

```
CALL mysql.rds_reset_external_master;
```

### Usage notes<a name="mysql_rds_reset_external_master-usage-notes"></a>

The master user must run the `mysql.rds_reset_external_master` procedure\. This procedure must be run on the MySQL DB instance to be removed as a read replica of a MySQL instance running external to Amazon RDS\.

**Note**  
We recommend that you use read replicas to manage replication between two Amazon RDS DB instances when possible\. When you do so, we recommend that you use only this and other replication\-related stored procedures\. These practices enable more complex replication topologies between Amazon RDS DB instances\. We offer these stored procedures primarily to enable replication with MySQL instances running external to Amazon RDS\. For information about managing replication between Amazon RDS DB instances, see [Working with DB instance read replicas](USER_ReadRepl.md)\.

For more information about using replication to import data from an instance of MySQL running external to Amazon RDS, see [Configuring binary log file position replication with an external source instance](MySQL.Procedural.Importing.External.Repl.md)\.

## mysql\.rds\_set\_external\_master<a name="mysql_rds_set_external_master"></a>

Configures an RDS for MySQL DB instance to be a read replica of an instance of MySQL running external to Amazon RDS\.

**Important**  
To run this procedure, `autocommit` must be enabled\. To enable it, set the `autocommit` parameter to `1`\. For information about modifying parameters, see [Modifying parameters in a DB parameter group](USER_WorkingWithDBInstanceParamGroups.md#USER_WorkingWithParamGroups.Modifying)\.

**Note**  
You can use the [mysql\.rds\_set\_external\_master\_with\_delay](#mysql_rds_set_external_master_with_delay) stored procedure to configure an external source database instance and delayed replication\.

### Syntax<a name="mysql_rds_set_external_master-syntax"></a>

 

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

### Parameters<a name="mysql_rds_set_external_master-parameters"></a>

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
You can determine the binlog file name and location by running `SHOW MASTER STATUS` on the source database instance\.

 *ssl\_encryption*   
A value that specifies whether Secure Socket Layer \(SSL\) encryption is used on the replication connection\. 1 specifies to use SSL encryption, 0 specifies to not use encryption\. The default is 0\.  
The `MASTER_SSL_VERIFY_SERVER_CERT` option isn't supported\. This option is set to 0, which means that the connection is encrypted, but the certificates aren't verified\.

### Usage notes<a name="mysql_rds_set_external_master-usage-notes"></a>

 The master user must run the `mysql.rds_set_external_master` procedure\. This procedure must be run on the MySQL DB instance to be configured as the read replica of a MySQL instance running external to Amazon RDS\. 

 Before you run `mysql.rds_set_external_master`, you must configure the instance of MySQL running external to Amazon RDS to be a source database instance\. To connect to the MySQL instance running external to Amazon RDS, you must specify `replication_user_name` and `replication_user_password` values that indicate a replication user that has `REPLICATION CLIENT` and `REPLICATION SLAVE` permissions on the external instance of MySQL\. 

**To configure an external instance of MySQL as a source database instance**

1. Using the MySQL client of your choice, connect to the external instance of MySQL and create a user account to be used for replication\. The following is an example\.

   **MySQL 5\.7**

   ```
   CREATE USER 'repl_user'@'mydomain.com' IDENTIFIED BY 'password';
   ```

   **MySQL 8\.0**

   ```
   CREATE USER 'repl_user'@'mydomain.com' IDENTIFIED WITH mysql_native_password BY 'password';
   ```

1. On the external instance of MySQL, grant `REPLICATION CLIENT` and `REPLICATION SLAVE` privileges to your replication user\. The following example grants `REPLICATION CLIENT` and `REPLICATION SLAVE` privileges on all databases for the 'repl\_user' user for your domain\.

   **MySQL 5\.7**

   ```
   GRANT REPLICATION CLIENT, REPLICATION SLAVE ON *.* TO 'repl_user'@'mydomain.com' IDENTIFIED BY 'password';
   ```

   **MySQL 8\.0**

   ```
   GRANT REPLICATION CLIENT, REPLICATION SLAVE ON *.* TO 'repl_user'@'mydomain.com';
   ```

To use encrypted replication, configure source database instance to use SSL connections\. Also, import the certificate authority certificate, client certificate, and client key into the DB instance or DB cluster using the [mysql\.rds\_import\_binlog\_ssl\_material](#mysql_rds_import_binlog_ssl_material) procedure\.

**Note**  
We recommend that you use read replicas to manage replication between two Amazon RDS DB instances when possible\. When you do so, we recommend that you use only this and other replication\-related stored procedures\. These practices enable more complex replication topologies between Amazon RDS DB instances\. We offer these stored procedures primarily to enable replication with MySQL instances running external to Amazon RDS\. For information about managing replication between Amazon RDS DB instances, see [Working with DB instance read replicas](USER_ReadRepl.md)\.

After calling `mysql.rds_set_external_master` to configure an Amazon RDS DB instance as a read replica, you can call [mysql\.rds\_start\_replication](#mysql_rds_start_replication) on the read replica to start the replication process\. You can call [mysql\.rds\_reset\_external\_master](#mysql_rds_reset_external_master) to remove the read replica configuration\.

When `mysql.rds_set_external_master` is called, Amazon RDS records the time, user, and an action of `set master` in the `mysql.rds_history` and `mysql.rds_replication_status` tables\.

### Examples<a name="mysql_rds_set_external_master-examples"></a>

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

## mysql\.rds\_set\_external\_master\_with\_auto\_position<a name="mysql_rds_set_external_master_with_auto_position"></a>

Configures an RDS for MySQL DB instance to be a read replica of an instance of MySQL running external to Amazon RDS\. This procedure also configures delayed replication and replication based on global transaction identifiers \(GTIDs\)\.

**Important**  
To run this procedure, `autocommit` must be enabled\. To enable it, set the `autocommit` parameter to `1`\. For information about modifying parameters, see [Modifying parameters in a DB parameter group](USER_WorkingWithDBInstanceParamGroups.md#USER_WorkingWithParamGroups.Modifying)\.

### Syntax<a name="mysql_rds_set_external_master_with_auto_position-syntax"></a>

 

```
CALL mysql.rds_set_external_master_with_auto_position (
  host_name
  , host_port
  , replication_user_name
  , replication_user_password
  , ssl_encryption
  , delay
);
```

### Parameters<a name="mysql_rds_set_external_master_with_auto_position-parameters"></a>

 *host\_name*   
The host name or IP address of the MySQL instance running external to Amazon RDS to become the source database instance\.

 *host\_port*   
The port used by the MySQL instance running external to Amazon RDS to be configured as the source database instance\. If your network configuration includes Secure Shell \(SSH\) port replication that converts the port number, specify the port number that is exposed by SSH\.

 *replication\_user\_name*   
The ID of a user with `REPLICATION CLIENT` and `REPLICATION SLAVE` permissions on the MySQL instance running external to Amazon RDS\. We recommend that you provide an account that is used solely for replication with the external instance\.

 *replication\_user\_password*   
The password of the user ID specified in `replication_user_name`\.

 *ssl\_encryption*   
A value that specifies whether Secure Socket Layer \(SSL\) encryption is used on the replication connection\. 1 specifies to use SSL encryption, 0 specifies to not use encryption\. The default is 0\.  
The `MASTER_SSL_VERIFY_SERVER_CERT` option isn't supported\. This option is set to 0, which means that the connection is encrypted, but the certificates aren't verified\.

 *delay*   
The minimum number of seconds to delay replication from source database instance\.  
The limit for this parameter is one day \(86,400 seconds\)\.

### Usage notes<a name="mysql_rds_set_external_master_with_auto_position-usage-notes"></a>

The master user must run the `mysql.rds_set_external_master_with_auto_position` procedure\. This procedure must be run on the MySQL DB instance to be configured as the read replica of a MySQL instance running external to Amazon RDS\. 

This procedure is supported for all RDS for MySQL 5\.7 versions, and RDS for MySQL 8\.0\.26 and higher 8\.0 versions\.

Before you run `mysql.rds_set_external_master_with_auto_position`, you must configure the instance of MySQL running external to Amazon RDS to be a source database instance\. To connect to the MySQL instance running external to Amazon RDS, you must specify values for `replication_user_name` and `replication_user_password`\. These values must indicate a replication user that has `REPLICATION CLIENT` and `REPLICATION SLAVE` permissions on the external instance of MySQL\. 

**To configure an external instance of MySQL as a source database instance**

1. Using the MySQL client of your choice, connect to the external instance of MySQL and create a user account to be used for replication\. The following is an example\.

   ```
   CREATE USER 'repl_user'@'mydomain.com' IDENTIFIED BY 'SomePassW0rd'
   ```

1. On the external instance of MySQL, grant `REPLICATION CLIENT` and `REPLICATION SLAVE` privileges to your replication user\. The following example grants `REPLICATION CLIENT` and `REPLICATION SLAVE` privileges on all databases for the `'repl_user'` user for your domain\.

   ```
   GRANT REPLICATION CLIENT, REPLICATION SLAVE ON *.* TO 'repl_user'@'mydomain.com'
   IDENTIFIED BY 'SomePassW0rd'
   ```

For more information, see [Configuring binary log file position replication with an external source instance](MySQL.Procedural.Importing.External.Repl.md)\.

**Note**  
We recommend that you use read replicas to manage replication between two Amazon RDS DB instances when possible\. When you do so, we recommend that you use only this and other replication\-related stored procedures\. These practices enable more complex replication topologies between Amazon RDS DB instances\. We offer these stored procedures primarily to enable replication with MySQL instances running external to Amazon RDS\. For information about managing replication between Amazon RDS DB instances, see [Working with DB instance read replicas](USER_ReadRepl.md)\.

After calling `mysql.rds_set_external_master_with_auto_position` to configure an Amazon RDS DB instance as a read replica, you can call [mysql\.rds\_start\_replication](#mysql_rds_start_replication) on the read replica to start the replication process\. You can call [mysql\.rds\_reset\_external\_master](#mysql_rds_reset_external_master) to remove the read replica configuration\.

When you call `mysql.rds_set_external_master_with_auto_position`, Amazon RDS records the time, the user, and an action of `set master` in the `mysql.rds_history` and `mysql.rds_replication_status` tables\.

For disaster recovery, you can use this procedure with the [mysql\.rds\_start\_replication\_until](#mysql_rds_start_replication_until) or [mysql\.rds\_start\_replication\_until\_gtid](#mysql_rds_start_replication_until_gtid) stored procedure\. To roll forward changes to a delayed read replica to the time just before a disaster, you can run the `mysql.rds_set_external_master_with_auto_position` procedure\. After the `mysql.rds_start_replication_until_gtid` procedure stops replication, you can promote the read replica to be the new primary DB instance by using the instructions in [Promoting a read replica to be a standalone DB instance](USER_ReadRepl.md#USER_ReadRepl.Promote)\. 

To use the `mysql.rds_rds_start_replication_until_gtid` procedure, GTID\-based replication must be enabled\. To skip a specific GTID\-based transaction that is known to cause disaster, you can use the [mysql\.rds\_skip\_transaction\_with\_gtid](#mysql_rds_skip_transaction_with_gtid) stored procedure\. For more information about working with GTID\-based replication, see [Using GTID\-based replication for Amazon RDS for MySQL](mysql-replication-gtid.md)\.

### Examples<a name="mysql_rds_set_external_master_with_auto_position-examples"></a>

When run on a MySQL DB instance, the following example configures the DB instance to be a read replica of an instance of MySQL running external to Amazon RDS\. It sets the minimum replication delay to one hour \(3,600 seconds\) on the MySQL DB instance\. A change from the MySQL source database instance running external to Amazon RDS isn't applied on the MySQL DB instance read replica for at least one hour\.

```
call mysql.rds_set_external_master_with_auto_position(
  'Externaldb.some.com',
  3306,
  'repl_user',
  'SomePassW0rd',
  0,
  3600);
```

## mysql\.rds\_set\_external\_master\_with\_delay<a name="mysql_rds_set_external_master_with_delay"></a>

Configures an RDS for MySQL DB instance to be a read replica of an instance of MySQL running external to Amazon RDS and configures delayed replication\.

**Important**  
To run this procedure, `autocommit` must be enabled\. To enable it, set the `autocommit` parameter to `1`\. For information about modifying parameters, see [Modifying parameters in a DB parameter group](USER_WorkingWithDBInstanceParamGroups.md#USER_WorkingWithParamGroups.Modifying)\.

### Syntax<a name="mysql_rds_set_external_master_with_delay-syntax"></a>

 

```
CALL mysql.rds_set_external_master_with_delay (
  host_name
  , host_port
  , replication_user_name
  , replication_user_password
  , mysql_binary_log_file_name
  , mysql_binary_log_file_location
  , ssl_encryption
  , delay
);
```

### Parameters<a name="mysql_rds_set_external_master_with_delay-parameters"></a>

 *host\_name*   
The host name or IP address of the MySQL instance running external to Amazon RDS that will become the source database instance\.

 *host\_port*   
The port used by the MySQL instance running external to Amazon RDS to be configured as the source database instance\. If your network configuration includes SSH port replication that converts the port number, specify the port number that is exposed by SSH\.

 *replication\_user\_name*   
The ID of a user with `REPLICATION CLIENT` and `REPLICATION SLAVE` permissions on the MySQL instance running external to Amazon RDS\. We recommend that you provide an account that is used solely for replication with the external instance\.

 *replication\_user\_password*   
The password of the user ID specified in `replication_user_name`\.

 *mysql\_binary\_log\_file\_name*   
The name of the binary log on the source database instance contains the replication information\.

 *mysql\_binary\_log\_file\_location*   
The location in the `mysql_binary_log_file_name` binary log at which replication will start reading the replication information\.  
You can determine the binlog file name and location by running `SHOW MASTER STATUS` on the source database instance\.

 *ssl\_encryption*   
A value that specifies whether Secure Socket Layer \(SSL\) encryption is used on the replication connection\. 1 specifies to use SSL encryption, 0 specifies to not use encryption\. The default is 0\.  
The `MASTER_SSL_VERIFY_SERVER_CERT` option isn't supported\. This option is set to 0, which means that the connection is encrypted, but the certificates aren't verified\.

 *delay*   
The minimum number of seconds to delay replication from source database instance\.  
The limit for this parameter is one day \(86400 seconds\)\.

### Usage notes<a name="mysql_rds_set_external_master_with_delay-usage-notes"></a>

 The master user must run the `mysql.rds_set_external_master_with_delay` procedure\. This procedure must be run on the MySQL DB instance to be configured as the read replica of a MySQL instance running external to Amazon RDS\. 

 Before you run `mysql.rds_set_external_master_with_delay`, you must configure the instance of MySQL running external to Amazon RDS to be a source database instance\. To connect to the MySQL instance running external to Amazon RDS, you must specify values for `replication_user_name` and `replication_user_password`\. These values must indicate a replication user that has `REPLICATION CLIENT` and `REPLICATION SLAVE` permissions on the external instance of MySQL\. 

**To configure an external instance of MySQL as a source database instance**

1. Using the MySQL client of your choice, connect to the external instance of MySQL and create a user account to be used for replication\. The following is an example\.

   ```
   CREATE USER 'repl_user'@'mydomain.com' IDENTIFIED BY 'SomePassW0rd'
   ```

1. On the external instance of MySQL, grant `REPLICATION CLIENT` and `REPLICATION SLAVE` privileges to your replication user\. The following example grants `REPLICATION CLIENT` and `REPLICATION SLAVE` privileges on all databases for the `'repl_user'` user for your domain\.

   ```
   GRANT REPLICATION CLIENT, REPLICATION SLAVE ON *.* TO 'repl_user'@'mydomain.com'
   IDENTIFIED BY 'SomePassW0rd'
   ```

For more information, see [Configuring binary log file position replication with an external source instance](MySQL.Procedural.Importing.External.Repl.md)\.

**Note**  
We recommend that you use read replicas to manage replication between two Amazon RDS DB instances when possible\. When you do so, we recommend that you use only this and other replication\-related stored procedures\. These practices enable more complex replication topologies between Amazon RDS DB instances\. We offer these stored procedures primarily to enable replication with MySQL instances running external to Amazon RDS\. For information about managing replication between Amazon RDS DB instances, see [Working with DB instance read replicas](USER_ReadRepl.md)\.

After calling `mysql.rds_set_external_master_with_delay` to configure an Amazon RDS DB instance as a read replica, you can call [mysql\.rds\_start\_replication](#mysql_rds_start_replication) on the read replica to start the replication process\. You can call [mysql\.rds\_reset\_external\_master](#mysql_rds_reset_external_master) to remove the read replica configuration\.

When you call `mysql.rds_set_external_master_with_delay`, Amazon RDS records the time, the user, and an action of `set master` in the `mysql.rds_history` and `mysql.rds_replication_status` tables\.

For disaster recovery, you can use this procedure with the [mysql\.rds\_start\_replication\_until](#mysql_rds_start_replication_until) or [mysql\.rds\_start\_replication\_until\_gtid](#mysql_rds_start_replication_until_gtid) stored procedure\. To roll forward changes to a delayed read replica to the time just before a disaster, you can run the `mysql.rds_set_external_master_with_delay` procedure\. After the `mysql.rds_start_replication_until` procedure stops replication, you can promote the read replica to be the new primary DB instance by using the instructions in [Promoting a read replica to be a standalone DB instance](USER_ReadRepl.md#USER_ReadRepl.Promote)\. 

To use the `mysql.rds_rds_start_replication_until_gtid` procedure, GTID\-based replication must be enabled\. To skip a specific GTID\-based transaction that is known to cause disaster, you can use the [mysql\.rds\_skip\_transaction\_with\_gtid](#mysql_rds_skip_transaction_with_gtid) stored procedure\. For more information about working with GTID\-based replication, see [Using GTID\-based replication for Amazon RDS for MySQL](mysql-replication-gtid.md)\.

The `mysql.rds_set_external_master_with_delay` procedure is available in these versions of RDS for MySQL:
+ MySQL 8\.0\.26 and higher 8\.0 versions
+ All 5\.7 versions

### Examples<a name="mysql_rds_set_external_master_with_delay-examples"></a>

When run on a MySQL DB instance, the following example configures the DB instance to be a read replica of an instance of MySQL running external to Amazon RDS\. It sets the minimum replication delay to one hour \(3,600 seconds\) on the MySQL DB instance\. A change from the MySQL source database instance running external to Amazon RDS isn't applied on the MySQL DB instance read replica for at least one hour\.

```
call mysql.rds_set_external_master_with_delay(
  'Externaldb.some.com',
  3306,
  'repl_user',
  'SomePassW0rd',
  'mysql-bin-changelog.000777',
  120,
  0,
  3600);
```

## mysql\.rds\_set\_master\_auto\_position<a name="mysql_rds_set_master_auto_position"></a>

Sets the replication mode to be based on either binary log file positions or on global transaction identifiers \(GTIDs\)\.

### Syntax<a name="mysql_rds_set_master_auto_position-syntax"></a>

 

```
CALL mysql.rds_set_master_auto_position (
auto_position_mode
);
```

### Parameters<a name="mysql_rds_set_master_auto_position-parameters"></a>

 *auto\_position\_mode*   
A value that indicates whether to use log file position replication or GTID\-based replication:  
+ `0` – Use the replication method based on binary log file position\. The default is `0`\.
+ `1` – Use the GTID\-based replication method\.

### Usage notes<a name="mysql_rds_set_master_auto_position-usage-notes"></a>

The master user must run the `mysql.rds_set_master_auto_position` procedure\.

This procedure is supported for all RDS for MySQL 5\.7 versions, and RDS for MySQL 8\.0\.26 and higher 8\.0 versions\.

## mysql\.rds\_set\_source\_delay<a name="mysql_rds_set_source_delay"></a>

Sets the minimum number of seconds to delay replication from source database instance to the current read replica\. Use this procedure when you are connected to a read replica to delay replication from its source database instance\.

### Syntax<a name="mysql_rds_set_source_delay-syntax"></a>

```
CALL mysql.rds_set_source_delay(
delay
);
```

### Parameters<a name="mysql_rds_set_source_delay-parameters"></a>

 *delay*   
The minimum number of seconds to delay replication from the source database instance\.  
The limit for this parameter is one day \(86400 seconds\)\.

### Usage notes<a name="mysql_rds_set_source_delay-usage-notes"></a>

The master user must run the `mysql.rds_set_source_delay` procedure\.

For disaster recovery, you can use this procedure with the [mysql\.rds\_start\_replication\_until](#mysql_rds_start_replication_until) stored procedure or the [mysql\.rds\_start\_replication\_until\_gtid](#mysql_rds_start_replication_until_gtid) stored procedure\. To roll forward changes to a delayed read replica to the time just before a disaster, you can run the `mysql.rds_set_source_delay` procedure\. After the `mysql.rds_start_replication_until` or `mysql.rds_start_replication_until_gtid` procedure stops replication, you can promote the read replica to be the new primary DB instance by using the instructions in [Promoting a read replica to be a standalone DB instance](USER_ReadRepl.md#USER_ReadRepl.Promote)\.

To use the `mysql.rds_rds_start_replication_until_gtid` procedure, GTID\-based replication must be enabled\. To skip a specific GTID\-based transaction that is known to cause disaster, you can use the [mysql\.rds\_skip\_transaction\_with\_gtid](#mysql_rds_skip_transaction_with_gtid) stored procedure\. For more information on GTID\-based replication, see [Using GTID\-based replication for Amazon RDS for MySQL](mysql-replication-gtid.md)\.

The `mysql.rds_set_source_delay` procedure is available in these versions of RDS for MySQL:
+ MySQL 8\.0\.26 and higher 8\.0 versions
+ All 5\.7 versions

### Examples<a name="mysql_rds_set_source_delay-examples"></a>

To delay replication from source database instance to the current read replica for at least one hour \(3,600 seconds\), you can call `mysql.rds_set_source_delay` with the following parameter:

```
CALL mysql.rds_set_source_delay(3600);
```

## mysql\.rds\_skip\_transaction\_with\_gtid<a name="mysql_rds_skip_transaction_with_gtid"></a>

Skips replication of a transaction with the specified global transaction identifier \(GTID\) on a MySQL DB instance\.

You can use this procedure for disaster recovery when a specific GTID transaction is known to cause a problem\. Use this stored procedure to skip the problematic transaction\. Examples of problematic transactions include transactions that disable replication, delete important data, or cause the DB instance to become unavailable\.

### Syntax<a name="mysql_rds_skip_transaction_with_gtid-syntax"></a>

 

```
CALL mysql.rds_skip_transaction_with_gtid (
gtid_to_skip
);
```

### Parameters<a name="mysql_rds_skip_transaction_with_gtid-parameters"></a>

 *gtid\_to\_skip*   
The GTID of the replication transaction to skip\.

### Usage notes<a name="mysql_rds_skip_transaction_with_gtid-usage-notes"></a>

The master user must run the `mysql.rds_skip_transaction_with_gtid` procedure\.

This procedure is supported for all RDS for MySQL 5\.7 versions, and RDS for MySQL 8\.0\.26 and higher 8\.0 versions\.

### Examples<a name="mysql_rds_skip_transaction_with_gtid-examples"></a>

The following example skips replication of the transaction with the GTID `3E11FA47-71CA-11E1-9E33-C80AA9429562:23`\.

```
call mysql.rds_skip_transaction_with_gtid('3E11FA47-71CA-11E1-9E33-C80AA9429562:23');
```

## mysql\.rds\_skip\_repl\_error<a name="mysql_rds_skip_repl_error"></a>

Skips and deletes a replication error on a MySQL DB read replica\.

### Syntax<a name="mysql_rds_skip_repl_error-syntax"></a>

 

```
CALL mysql.rds_skip_repl_error;
```

### Usage notes<a name="mysql_rds_skip_repl_error-usage-notes"></a>

The master user must run the `mysql.rds_skip_repl_error` procedure on a read replica\. For more information about this procedure, see [Calling the mysql\.rds\_skip\_repl\_error procedure](Appendix.MySQL.CommonDBATasks.md#Appendix.MySQL.CommonDBATasks.SkipError.procedure)\.

To determine if there are errors, run the MySQL `SHOW REPLICA STATUS\G` command\. If a replication error isn't critical, you can run `mysql.rds_skip_repl_error` to skip the error\. If there are multiple errors, `mysql.rds_skip_repl_error` deletes the first error, then warns that others are present\. You can then use `SHOW REPLICA STATUS\G` to determine the correct course of action for the next error\. For information about the values returned, see [SHOW REPLICA STATUS statement](https://dev.mysql.com/doc/refman/8.0/en/show-replica-status.html) in the MySQL documentation\.

**Note**  
Previous versions of MySQL used `SHOW SLAVE STATUS` instead of `SHOW REPLICA STATUS`\. If you are using a MySQL version before 8\.0\.23, then use `SHOW SLAVE STATUS`\.

For more information about addressing replication errors with Amazon RDS, see [Troubleshooting a MySQL read replica problem](USER_MySQL.Replication.ReadReplicas.md#USER_ReadRepl.Troubleshooting)\.

#### Replication stopped error<a name="skip_repl_error.stopped-error"></a>

When you call the `mysql.rds_skip_repl_error` procedure, you might receive an error message stating that the replica is down or disabled\.

This error message appears if you run the procedure on the primary instance instead of the read replica\. You must run this procedure on the read replica for the procedure to work\.

This error message might also appear if you run the procedure on the read replica, but replication can't be restarted successfully\.

If you need to skip a large number of errors, the replication lag can increase beyond the default retention period for binary log \(binlog\) files\. In this case, you might encounter a fatal error due to binlog files being purged before they have been replayed on the read replica\. This purge causes replication to stop, and you can no longer call the `mysql.rds_skip_repl_error` command to skip replication errors\.

You can mitigate this issue by increasing the number of hours that binlog files are retained on your source database instance\. After you have increased the binlog retention time, you can restart replication and call the `mysql.rds_skip_repl_error` command as needed\.

To set the binlog retention time, use the [mysql\.rds\_set\_configuration](mysql-stored-proc-configuring.md#mysql_rds_set_configuration) procedure and specify a configuration parameter of `'binlog retention hours'` along with the number of hours to retain binlog files on the DB cluster\. The following example sets the retention period for binlog files to 48 hours\.

```
CALL mysql.rds_set_configuration('binlog retention hours', 48);
```

## mysql\.rds\_start\_replication<a name="mysql_rds_start_replication"></a>

Initiates replication from an RDS for MySQL DB instance\.

**Note**  
You can use the [mysql\.rds\_start\_replication\_until](#mysql_rds_start_replication_until) or [mysql\.rds\_start\_replication\_until\_gtid](#mysql_rds_start_replication_until_gtid) stored procedure to initiate replication from an RDS for MySQL DB instance and stop replication at the specified binary log file location\.

### Syntax<a name="mysql_rds_start_replication-syntax"></a>

 

```
CALL mysql.rds_start_replication;
```

### Usage notes<a name="mysql_rds_start_replication-usage-notes"></a>

The master user must run the `mysql.rds_start_replication` procedure\.

To import data from an instance of MySQL external to Amazon RDS, call `mysql.rds_start_replication` on the read replica to start the replication process after you call `mysql.rds_set_external_master` to build the replication configuration\. For more information, see [Restoring a backup into a MySQL DB instance](MySQL.Procedural.Importing.md)\.

To export data to an instance of MySQL external to Amazon RDS, call `mysql.rds_start_replication` and `mysql.rds_stop_replication` on the read replica to control some replication actions, such as purging binary logs\. For more information, see [Exporting data from a MySQL DB instance by using replication](MySQL.Procedural.Exporting.NonRDSRepl.md)\.

You can also call `mysql.rds_start_replication` on the read replica to restart any replication process that you previously stopped by calling `mysql.rds_stop_replication`\. For more information, see [Working with DB instance read replicas](USER_ReadRepl.md)\.

## mysql\.rds\_start\_replication\_until<a name="mysql_rds_start_replication_until"></a>

Initiates replication from an RDS for MySQL DB instance and stops replication at the specified binary log file location\.

### Syntax<a name="mysql_rds_start_replication_until-syntax"></a>

 

```
CALL mysql.rds_start_replication_until (
replication_log_file
  , replication_stop_point
);
```

### Parameters<a name="mysql_rds_start_replication_until-parameters"></a>

 *replication\_log\_file*   
The name of the binary log on the source database instance contains the replication information\.

 *replication\_stop\_point *   
The location in the `replication_log_file` binary log at which replication will stop\.

### Usage notes<a name="mysql_rds_start_replication_until-usage-notes"></a>

The master user must run the `mysql.rds_start_replication_until` procedure\.

The `mysql.rds_start_replication_until` procedure is available in these versions of RDS for MySQL:
+ MySQL 8\.0\.26 and higher 8\.0 versions
+ All 5\.7 versions

You can use this procedure with delayed replication for disaster recovery\. If you have delayed replication configured, you can use this procedure to roll forward changes to a delayed read replica to the time just before a disaster\. After this procedure stops replication, you can promote the read replica to be the new primary DB instance by using the instructions in [Promoting a read replica to be a standalone DB instance](USER_ReadRepl.md#USER_ReadRepl.Promote)\.

You can configure delayed replication using the following stored procedures:
+ [mysql\.rds\_set\_configuration](mysql-stored-proc-configuring.md#mysql_rds_set_configuration)
+ [mysql\.rds\_set\_external\_master\_with\_delay](#mysql_rds_set_external_master_with_delay)
+ [mysql\.rds\_set\_source\_delay](#mysql_rds_set_source_delay)

The file name specified for the `replication_log_file` parameter must match the source database instance binlog file name\.

When the `replication_stop_point ` parameter specifies a stop location that is in the past, replication is stopped immediately\.

### Examples<a name="mysql_rds_start_replication_until-examples"></a>

The following example initiates replication and replicates changes until it reaches location `120` in the `mysql-bin-changelog.000777` binary log file\.

```
call mysql.rds_start_replication_until(
  'mysql-bin-changelog.000777',
  120);
```

## mysql\.rds\_start\_replication\_until\_gtid<a name="mysql_rds_start_replication_until_gtid"></a>

Initiates replication from an RDS for MySQL DB instance and stops replication immediately after the specified global transaction identifier \(GTID\)\.

### Syntax<a name="mysql_rds_start_replication_until_gtid-syntax"></a>

 

```
CALL mysql.rds_start_replication_until_gtid(gtid);
```

### Parameters<a name="mysql_rds_start_replication_until_gtid-parameters"></a>

 *gtid*   
The GTID after which replication is to stop\.

### Usage notes<a name="mysql_rds_start_replication_until_gtid-usage-notes"></a>

The master user must run the `mysql.rds_start_replication_until_gtid` procedure\.

This procedure is supported for all RDS for MySQL 5\.7 versions, and RDS for MySQL 8\.0\.26 and higher 8\.0 versions\.

You can use this procedure with delayed replication for disaster recovery\. If you have delayed replication configured, you can use this procedure to roll forward changes to a delayed read replica to the time just before a disaster\. After this procedure stops replication, you can promote the read replica to be the new primary DB instance by using the instructions in [Promoting a read replica to be a standalone DB instance](USER_ReadRepl.md#USER_ReadRepl.Promote)\.

You can configure delayed replication using the following stored procedures:
+ [mysql\.rds\_set\_configuration](mysql-stored-proc-configuring.md#mysql_rds_set_configuration)
+ [mysql\.rds\_set\_external\_master\_with\_delay](#mysql_rds_set_external_master_with_delay)
+ [mysql\.rds\_set\_source\_delay](#mysql_rds_set_source_delay)

When the `gtid` parameter specifies a transaction that has already been run by the replica, replication is stopped immediately\.

### Examples<a name="mysql_rds_start_replication_until_gtid-examples"></a>

The following example initiates replication and replicates changes until it reaches GTID `3E11FA47-71CA-11E1-9E33-C80AA9429562:23`\.

```
call mysql.rds_start_replication_until_gtid('3E11FA47-71CA-11E1-9E33-C80AA9429562:23');
```

## mysql\.rds\_stop\_replication<a name="mysql_rds_stop_replication"></a>

Stops replication from a MySQL DB instance\.

### Syntax<a name="mysql_rds_stop_replication-syntax"></a>

 

```
CALL mysql.rds_stop_replication;
```

### Usage notes<a name="mysql_rds_stop_replication-usage-notes"></a>

The master user must run the `mysql.rds_stop_replication` procedure\. 

If you are configuring replication to import data from an instance of MySQL running external to Amazon RDS, you call `mysql.rds_stop_replication` on the read replica to stop the replication process after the import has completed\. For more information, see [Restoring a backup into a MySQL DB instance](MySQL.Procedural.Importing.md)\.

If you are configuring replication to export data to an instance of MySQL external to Amazon RDS, you call `mysql.rds_start_replication` and `mysql.rds_stop_replication` on the read replica to control some replication actions, such as purging binary logs\. For more information, see [Exporting data from a MySQL DB instance by using replication](MySQL.Procedural.Exporting.NonRDSRepl.md)\.

You can also use `mysql.rds_stop_replication` to stop replication between two Amazon RDS DB instances\. You typically stop replication to perform a long running operation on the read replica, such as creating a large index on the read replica\. You can restart any replication process that you stopped by calling [mysql\.rds\_start\_replication](#mysql_rds_start_replication) on the read replica\. For more information, see [Working with DB instance read replicas](USER_ReadRepl.md)\.