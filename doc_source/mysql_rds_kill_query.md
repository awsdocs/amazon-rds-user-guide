# mysql\.rds\_kill\_query<a name="mysql_rds_kill_query"></a>

Terminates a query running against the MySQL server\.

## Syntax<a name="mysql_rds_kill_query-syntax"></a>

```
CALL mysql.rds_kill_query(queryID);
```

## Parameters<a name="mysql_rds_kill_query-parameters"></a>

 *queryID*   
The identity of the query that will be terminated\.

## Usage Notes<a name="mysql_rds_kill_query-usage-notes"></a>

To terminate a query running against the MySQL server, use the `mysql_rds_kill_query` procedure and pass in the ID of that query\. To obtain the query ID, use the MySQL [INFORMATION\_SCHEMA PROCESSLIST](http://dev.mysql.com/doc/refman/5.6/en/processlist-table.html) command\. The connection to the MySQL server is retained\. 

The `mysql_rds_kill_query` procedure is available in these versions of Amazon RDS MySQL:
+ MySQL 5\.5
+ MySQL 5\.6
+ MySQL 5\.7

## Related Topics<a name="mysql_rds_kill_query.related"></a>
+ [mysql\.rds\_kill](mysql_rds_kill.md)

## Examples<a name="mysql_rds_kill_query-examples"></a>

The following example terminates a query with a thread ID of 230040:

```
call mysql.rds_kill_query(230040);               
```