# mysql\.rds\_next\_master\_log<a name="mysql_rds_next_master_log"></a>

Changes the source database instance log position to the start of the next binary log on the source database instance\. Use this procedure only if you are receiving replication I/O error 1236 on a read replica\.

## Syntax<a name="mysql_rds_next_master_log-syntax"></a>

 

```
CALL mysql.rds_next_master_log(
curr_master_log
);
```

## Parameters<a name="mysql_rds_next_master_log-parameters"></a>

 *curr\_master\_log*   
The index of the current master log file\. For example, if the current file is named `mysql-bin-changelog.012345`, then the index is 12345\. To determine the current master log file name, run the `SHOW REPLICA STATUS` command and view the `Master_Log_File` field\.  
Previous versions of MySQL used `SHOW SLAVE STATUS` instead of `SHOW REPLICA STATUS`\. If you are using a MySQL version before 8\.0\.23, then use `SHOW SLAVE STATUS`\. 

## Usage notes<a name="mysql_rds_next_master_log-usage-notes"></a>

The master user must run the `mysql.rds_next_master_log` procedure\. 

**Warning**  
Call `mysql.rds_next_master_log` only if replication fails after a failover of a Multi\-AZ DB instance that is the replication source, and the `Last_IO_Errno` field of `SHOW REPLICA STATUS` reports I/O error 1236\.  
Calling `mysql.rds_next_master_log` may result in data loss in the read replica if transactions in the source instance were not written to the binary log on disk before the failover event occurred\. You can reduce the chance of this happening by configuring the source instance parameters sync\_binlog = 1 and innodb\_support\_xa = 1, although this may reduce performance\. For more information, see [Working with read replicas](USER_ReadRepl.md)\.

## Examples<a name="mysql_rds_next_master_log-examples"></a>

Assume replication fails on an Amazon RDS read replica\. Running `SHOW REPLICA STATUS\G` on the read replica returns the following result:

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