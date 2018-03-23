# mysql\.rds\_kill<a name="mysql_rds_kill"></a>

Terminates a connection to the MySQL server\.

## Syntax<a name="mysql_rds_kill-syntax"></a>

```
CALL mysql.rds_kill(processID);
```

## Parameters<a name="mysql_rds_kill-parameters"></a>

 *processID*   
The identity of the connection thread that will be terminated\.

## Usage Notes<a name="mysql_rds_kill-usage-notes"></a>

Each connection to the MySQL server runs in a separate thread\. To terminate a connection, use the `mysql.rds_kill` procedure and pass in the thread ID of that connection\. To obtain the thread ID, use the MySQL [SHOW PROCESSLIST](http://dev.mysql.com/doc/refman/5.6/en/show-processlist.html) command\.

The `mysql.rds_kill` procedure is available in these versions of Amazon RDS MySQL:
+ MySQL 5\.5
+ MySQL 5\.6
+ MySQL 5\.7

## Related Topics<a name="mysql_rds_kill.related"></a>
+ [mysql\.rds\_kill\_query](mysql_rds_kill_query.md)

## Examples<a name="mysql_rds_kill-examples"></a>

The following example terminates a connection with a thread ID of 4243:

```
call mysql.rds_kill(4243);               
```