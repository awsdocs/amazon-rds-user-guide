# mysql\.rds\_kill\_query\_id<a name="mysql_rds_kill_query_id"></a>

Ends a query running against the MariaDB server\.

## Syntax<a name="mysql_rds_kill_query_id-syntax"></a>

```
CALL mysql.rds_kill_query_id(queryID);
```

## Parameters<a name="mysql_rds_kill_query_id-parameters"></a>

 *queryID*   
Integer\. The identity of the query to be ended\.

## Usage Notes<a name="mysql_rds_kill_query_id-usage-notes"></a>

To stop a query running against the MariaDB server, use the `mysql.rds_kill_query_id` procedure and pass in the ID of that query\. To obtain the query ID, query the MariaDB [Information Schema PROCESSLIST Table](http://mariadb.com/kb/en/mariadb/information-schema-processlist-table/), as shown following:

```
SELECT USER, HOST, COMMAND, TIME, STATE, INFO, QUERY_ID FROM 
                INFORMATION_SCHEMA.PROCESSLIST WHERE USER = '<user name>';
```

The connection to the MariaDB server is retained\.

## Related Topics<a name="mysql_rds_kill_query_id.related"></a>
+ [mysql\.rds\_kill](mysql_rds_kill.md)
+ [mysql\.rds\_kill\_query](mysql_rds_kill_query.md)

## Examples<a name="mysql_rds_kill_query_id-examples"></a>

The following example ends a query with a query ID of 230040:

```
call mysql.rds_kill_query_id(230040); 
```