# mysql\.rds\_replica\_status<a name="mysql_rds_replica_status"></a>

Shows the replication status of a MariaDB read replica\.

Call this procedure on the read replica to show status information on essential parameters of the replica threads\.

## Syntax<a name="mysql_rds_replica_status-syntax"></a>

```
CALL mysql.rds_replica_status;
```

## Usage notes<a name="mysql_rds_replica_status-usage-notes"></a>

This procedure is only supported for MariaDB DB instances running MariaDB version 10\.5 and higher\.

This procedure is the equivalent of the `SHOW REPLICA STATUS` command\. This command isn't supported for MariaDB version 10\.5 and higher DB instances\.

In prior versions of MariaDB, the equivalent `SHOW SLAVE STATUS` command required the `REPLICATION SLAVE` privilege\. In MariaDB version 10\.5 and higher, it requires the `REPLICATION REPLICA ADMIN` privilege\. To protect the RDS management of MariaDB 10\.5 and higher DB instances, this new privilege isn't granted to the RDS master user\.

## Examples<a name="mysql_rds_replica_status-examples"></a>

The following example shows the status of a MariaDB read replica:

```
call mysql.rds_replica_status;
```

The response is similar to the following:

```
*************************** 1. row ***************************
                Replica_IO_State: Waiting for master to send event
                     Source_Host: XX.XX.XX.XXX
                     Source_User: rdsrepladmin
                     Source_Port: 3306
                   Connect_Retry: 60
                 Source_Log_File: mysql-bin-changelog.003988
             Read_Source_Log_Pos: 405
                  Relay_Log_File: relaylog.011024
                   Relay_Log_Pos: 657
           Relay_Source_Log_File: mysql-bin-changelog.003988
              Replica_IO_Running: Yes
             Replica_SQL_Running: Yes
                 Replicate_Do_DB:
             Replicate_Ignore_DB:
              Replicate_Do_Table:
          Replicate_Ignore_Table: mysql.rds_sysinfo,mysql.rds_history,mysql.rds_replication_status
         Replicate_Wild_Do_Table:
     Replicate_Wild_Ignore_Table:
                      Last_Errno: 0
                      Last_Error:
                    Skip_Counter: 0
             Exec_Source_Log_Pos: 405
                 Relay_Log_Space: 1016
                 Until_Condition: None
                  Until_Log_File:
                   Until_Log_Pos: 0
              Source_SSL_Allowed: No
              Source_SSL_CA_File:
              Source_SSL_CA_Path:
                 Source_SSL_Cert:
               Source_SSL_Cipher:
                  Source_SSL_Key:
           Seconds_Behind_Master: 0
   Source_SSL_Verify_Server_Cert: No
                   Last_IO_Errno: 0
                   Last_IO_Error:
                  Last_SQL_Errno: 0
                  Last_SQL_Error:
     Replicate_Ignore_Server_Ids:
                Source_Server_Id: 807509301
                  Source_SSL_Crl:
              Source_SSL_Crlpath:
                      Using_Gtid: Slave_Pos
                     Gtid_IO_Pos: 0-807509301-3980
         Replicate_Do_Domain_Ids:
     Replicate_Ignore_Domain_Ids:
                   Parallel_Mode: optimistic
                       SQL_Delay: 0
             SQL_Remaining_Delay: NULL
       Replica_SQL_Running_State: Reading event from the relay log
              Replica_DDL_Groups: 15
Replica_Non_Transactional_Groups: 0
    Replica_Transactional_Groups: 3658
1 row in set (0.000 sec)

Query OK, 0 rows affected (0.000 sec)
```