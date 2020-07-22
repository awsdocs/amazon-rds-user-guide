# mysql\.rds\_kill<a name="mysql_rds_kill"></a>

Ends a connection to the MySQL server\.

## Syntax<a name="mysql_rds_kill-syntax"></a>

```
CALL mysql.rds_kill(processID);
```

## Parameters<a name="mysql_rds_kill-parameters"></a>

 *processID*   
The identity of the connection thread to be ended\.

## Usage Notes<a name="mysql_rds_kill-usage-notes"></a>

Each connection to the MySQL server runs in a separate thread\. To end a connection, use the `mysql.rds_kill` procedure and pass in the thread ID of that connection\. To obtain the thread ID, use the MySQL [SHOW PROCESSLIST](https://dev.mysql.com/doc/refman/8.0/en/show-processlist.html) command\.

## Examples<a name="mysql_rds_kill-examples"></a>

The following example ends a connection with a thread ID of 4243:

```
call mysql.rds_kill(4243);               
```