# IO:BufFileRead and IO:BufFileWrite<a name="wait-event.iobuffile"></a>

The `IO:BufFileRead` and `IO:BufFileWrite` events occur when RDS for PostgreSQL creates temporary files\. When operations require more memory than the working memory parameters currently define, they write temporary data to persistent storage\. This operation is sometimes called "spilling to disk\."

**Topics**
+ [Supported engine versions](#wait-event.iobuffile.context.supported)
+ [Context](#wait-event.iobuffile.context)
+ [Likely causes of increased waits](#wait-event.iobuffile.causes)
+ [Actions](#wait-event.iobuffile.actions)

## Supported engine versions<a name="wait-event.iobuffile.context.supported"></a>

This wait event information is supported for all versions of RDS for PostgreSQL\.

## Context<a name="wait-event.iobuffile.context"></a>

`IO:BufFileRead` and `IO:BufFileWrite` relate to the work memory area and maintenance work memory area\. For more information about these local memory areas, see [Resource Consumption](https://www.postgresql.org/docs/current/runtime-config-resource.html) in the PostgreSQL documentation\.

The default value for `work_mem` is 4 MB\. If one session performs operations in parallel, each worker handling the parallelism uses 4 MB of memory\. For this reason, set `work_mem` carefully\. If you increase the value too much, a database running many sessions might consume too much memory\. If you set the value too low, RDS for PostgreSQL creates temporary files in local storage\. The disk I/O for these temporary files can reduce performance\.

If you observe the following sequence of events, your database might be generating temporary files:

1. Sudden and sharp decreases in availability

1. Fast recovery for the free space

You might also see a "chainsaw" pattern\. This pattern can indicate that your database is creating small files constantly\.

## Likely causes of increased waits<a name="wait-event.iobuffile.causes"></a>

In general, these wait events are caused by operations that consume more memory than the `work_mem` or `maintenance_work_mem` parameters allocate\. To compensate, the operations write to temporary files\. Common causes for the `IO:BufFileRead` and `IO:BufFileWrite` events include the following:

**Queries that need more memory than exists in the work memory area**  
Queries with the following characteristics use the work memory area:  
+ Hash joins
+ `ORDER BY` clause
+ `GROUP BY` clause
+ `DISTINCT`
+ Window functions
+ `CREATE TABLE AS SELECT`
+ Materialized view refresh

**Statements that need more memory than exists in the maintenance work memory area**  
The following statements use the maintenance work memory area:  
+ `CREATE INDEX`
+ `CLUSTER`

## Actions<a name="wait-event.iobuffile.actions"></a>

We recommend different actions depending on the causes of your wait event\.

**Topics**
+ [Identify the problem](#wait-event.iobuffile.actions.problem)
+ [Examine your join queries](#wait-event.iobuffile.actions.joins)
+ [Examine your ORDER BY and GROUP BY queries](#wait-event.iobuffile.actions.order-by)
+ [Avoid using the DISTINCT operation](#wait-event.iobuffile.actions.distinct)
+ [Consider using window functions instead of GROUP BY functions](#wait-event.iobuffile.actions.window)
+ [Investigate materialized views and CTAS statements](#wait-event.iobuffile.actions.mv-refresh)
+ [Use pg\_repack when you rebuild indexes](#wait-event.iobuffile.actions.pg_repack)
+ [Increase maintenance\_work\_mem when you cluster tables](#wait-event.iobuffile.actions.cluster)
+ [Tune memory to prevent IO:BufFileRead and IO:BufFileWrite](#wait-event.iobuffile.actions.tuning-memory)

### Identify the problem<a name="wait-event.iobuffile.actions.problem"></a>

Assume a situation in which Performance Insights isn't turned on and you suspect that `IO:BufFileRead` and `IO:BufFileWrite` are occurring more frequently than is normal\.  To identify the source of the problem, you can set the `log_temp_files` parameter to log all queries that generate more than your specified threshold KB of temporary files\. By default, `log_temp_files` is set to `-1`, which turns off this logging feature\. If you set this parameter to `0`, RDS for PostgreSQL logs all temporary files\. If you set it to is `1024`, RDS for PostgreSQL logs all queries that produce temporary files larger than 1 MB\. For more information about `log_temp_files`, see [Error Reporting and Logging](https://www.postgresql.org/docs/current/runtime-config-logging.html) in the PostgreSQL documentation\.

### Examine your join queries<a name="wait-event.iobuffile.actions.joins"></a>

It's likely that your query uses joins\. For example, the following query joins four tables\.

```
SELECT * 
       FROM "order" 
 INNER JOIN order_item 
       ON (order.id = order_item.order_id)
 INNER JOIN customer 
       ON (customer.id = order.customer_id)
 INNER JOIN customer_address 
       ON (customer_address.customer_id = customer.id AND 
           order.customer_address_id = customer_address.id)
 WHERE customer.id = 1234567890;
```

A possible cause of spikes in temporary file usage is a problem in the query itself\. For example, a broken clause might not filter the joins properly\. Consider the second inner join in the following example\.

```
SELECT * 
       FROM "order"
 INNER JOIN order_item 
       ON (order.id = order_item.order_id)
 INNER JOIN customer 
       ON (customer.id = customer.id)
 INNER JOIN customer_address 
       ON (customer_address.customer_id = customer.id AND 
           order.customer_address_id = customer_address.id)
 WHERE customer.id = 1234567890;
```

The preceding query mistakenly joins `customer.id` to `customer.id`, generating a Cartesian product between every customer and every order\. This type of accidental join generates large temporary files\. Depending on the size of the tables, a Cartesian query can even fill up storage\. Your application might have Cartesian joins when the following conditions are met:
+ You see large, sharp decreases in storage availability, followed by fast recovery\.
+ No indexes are being created\.
+ No `CREATE TABLE FROM SELECT` statements are being issued\.
+ No materialized views are being refreshed\.

To see whether the tables are being joined using the proper keys, inspect your query and object\-relational mapping directives\. Bear in mind that certain queries of your application are not called all the time, and some queries are dynamically generated\.

### Examine your ORDER BY and GROUP BY queries<a name="wait-event.iobuffile.actions.order-by"></a>

In some cases, an `ORDER BY` clause can result in excessive temporary files\. Consider the following guidelines:
+ Only include columns in an `ORDER BY` clause when they need to be ordered\. This guideline is especially important for queries that return thousands of rows and specify many columns in the `ORDER BY` clause\.
+ Considering creating indexes to accelerate `ORDER BY` clauses when they match columns that have the same ascending or descending order\. Partial indexes are preferable because they are smaller\. Smaller indexes are read and traversed more quickly\.
+ If you create indexes for columns that can accept null values, consider whether you want the null values stored at the end or at the beginning of the indexes\.

  If possible, reduce the number of rows that need to be ordered by filtering the result set\. If you use `WITH` clause statements or subqueries, remember that an inner query generates a result set and passes it to the outside query\. The more rows that a query can filter out, the less ordering the query needs to do\.
+ If you don't need to obtain the full result set, use the `LIMIT` clause\. For example, if you only want the top five rows, a query using the `LIMIT` clause doesn't keep generating results\. In this way, the query requires less memory and temporary files\.

A query that uses a `GROUP BY` clause can also require temporary files\. `GROUP BY` queries summarize values by using functions such as the following:
+ `COUNT`
+ `AVG`
+ `MIN`
+ `MAX`
+ `SUM`
+ `STDDEV`

To tune `GROUP BY` queries, follow the recommendations for `ORDER BY` queries\.

### Avoid using the DISTINCT operation<a name="wait-event.iobuffile.actions.distinct"></a>

If possible, avoid using the `DISTINCT` operation to remove duplicated rows\. The more unnecessary and duplicated rows that your query returns, the more expensive the `DISTINCT` operation becomes\. If possible, add filters in the `WHERE` clause even if you use the same filters for different tables\. Filtering the query and joining correctly improves your performance and reduces resource use\. It also prevents incorrect reports and results\.

If you need to use `DISTINCT` for multiple rows of a same table, consider creating a composite index\. Grouping multiple columns in an index can improve the time to evaluate distinct rows\. Also, if you use RDS for PostgreSQL version 10 or higher, you can correlate statistics among multiple columns by using the `CREATE STATISTICS` command\.

### Consider using window functions instead of GROUP BY functions<a name="wait-event.iobuffile.actions.window"></a>

Using `GROUP BY`, you change the result set, and then retrieve the aggregated result\. Using window functions, you aggregate data without changing the result set\. A window function uses the `OVER` clause to perform calculations across the sets defined by the query, correlating one row with another\. You can use all the `GROUP BY` functions in window functions, but also use functions such as the following:
+ `RANK`
+ `ARRAY_AGG`
+ `ROW_NUMBER`
+ `LAG`
+ `LEAD`

To minimize the number of temporary files generated by a window function, remove duplications for the same result set when you need two distinct aggregations\. Consider the following query\.

```
SELECT sum(salary) OVER (PARTITION BY dept ORDER BY salary DESC) as sum_salary
     , avg(salary) OVER (PARTITION BY dept ORDER BY salary ASC) as avg_salary
  FROM empsalary;
```

You can rewrite the query with the `WINDOW` clause as follows\.

```
SELECT sum(salary) OVER w as sum_salary
         , avg(salary) OVER w as_avg_salary
    FROM empsalary
  WINDOW w AS (PARTITION BY dept ORDER BY salary DESC);
```

By default, the RDS for PostgreSQL execution planner consolidates similar nodes so that it doesn't duplicate operations\. However, by using an explicit declaration for the window block, you can maintain the query more easily\. You might also improve performance by preventing duplication\.

### Investigate materialized views and CTAS statements<a name="wait-event.iobuffile.actions.mv-refresh"></a>

When a materialized view refreshes, it runs a query\. This query can contain an operation such as `GROUP BY`, `ORDER BY`, or `DISTINCT`\. During a refresh, you might observe large numbers of temporary files and the wait events `IO:BufFileWrite` and `IO:BufFileRead`\. Similarly, when you create a table based on a `SELECT` statement, the `CREATE TABLE` statement runs a query\. To reduce the temporary files needed, optimize the query\.

### Use pg\_repack when you rebuild indexes<a name="wait-event.iobuffile.actions.pg_repack"></a>

When you create an index, the engine orders the result set\. As tables grow in size, and as values in the indexed column become more diverse, the temporary files require more space\. In most cases, you can't prevent the creation of temporary files for large tables without modifying the maintenance work memory area\. For more information about `maintenance_work_mem`, see [https://www.postgresql.org/docs/current/runtime-config-resource.html](https://www.postgresql.org/docs/current/runtime-config-resource.html) in the PostgreSQL documentation\. 

A possible workaround when recreating a large index is to use the pg\_repack extension\. For more information, see [Reorganize tables in PostgreSQL databases with minimal locks](https://reorg.github.io/pg_repack/) in the pg\_repack documentation\. For information about setting up the extension in your RDS for PostgreSQL DB instance, see [Reducing bloat in tables and indexes with the pg\_repack extension](Appendix.PostgreSQL.CommonDBATasks.Extensions.md#Appendix.PostgreSQL.CommonDBATasks.pg_repack)\. 

### Increase maintenance\_work\_mem when you cluster tables<a name="wait-event.iobuffile.actions.cluster"></a>

The `CLUSTER` command clusters the table specified by *table\_name* based on an existing index specified by *index\_name*\. RDS for PostgreSQL physically recreates the table to match the order of a given index\.

When magnetic storage was prevalent, clustering was common because storage throughput was limited\. Now that SSD\-based storage is common, clustering is less popular\. However, if you cluster tables, you can still increase performance slightly depending on the table size, index, query, and so on\. 

If you run the `CLUSTER` command and observe the wait events `IO:BufFileWrite` and `IO:BufFileRead`, tune `maintenance_work_mem`\. Increase the memory size to a fairly large amount\. A high value means that the engine can use more memory for the clustering operation\.

### Tune memory to prevent IO:BufFileRead and IO:BufFileWrite<a name="wait-event.iobuffile.actions.tuning-memory"></a>

In some situations, you need to tune memory\. Your goal is to balance memory across the following areas of consumption using the appropriate parameters, as follows\.
+ The `work_mem` value 
+ The memory remaining after discounting the `shared_buffers` value
+ The maximum connections opened and in use, which is limited by `max_connections`

For more information about tuning memory, see [Resource Consumption](https://www.postgresql.org/docs/current/runtime-config-resource.html) in the PostgreSQL documentation\. 

#### Increase the size of the work memory area<a name="wait-event.iobuffile.actions.tuning-memory.work-mem"></a>

In some situations, your only option is to increase the memory used by your session\. If your queries are correctly written and are using the correct keys for joins, consider increasing the `work_mem` value\. 

To find out how many temporary files a query generates, set `log_temp_files` to `0`\. If you increase the `work_mem` value to the maximum value identified in the logs, you prevent the query from generating temporary files\. However, `work_mem` sets the maximum per plan node for each connection or parallel worker\. If the database has 5,000 connections, and if each one uses 256 MiB memory, the engine needs 1\.2 TiB of RAM\. Thus, your instance might run out of memory\.

#### Reserve sufficient memory for the shared buffer pool<a name="wait-event.iobuffile.actions.tuning-memory.shared-pool"></a>

Your database uses memory areas such as the shared buffer pool, not just the work memory area\. Consider the requirements of these additional memory areas before you increase `work_mem`\.

For example, assume that your RDS for PostgreSQL instance class is db\.r5\.2xlarge\. This class has 64 GiB of memory\. By default, 25 percent of the memory is reserved for the shared buffer pool\. After you subtract the amount allocated to the shared memory area, 16,384 MB remains\. Don't allocate the remaining memory exclusively to the work memory area because the operating system and the engine also require memory\.

The memory that you can allocate to `work_mem` depends on the instance class\. If you use a larger instance class, more memory is available\. However, in the preceding example, you can't use more than 16 GiB\. Otherwise, your instance becomes unavailable when it runs out of memory\. To recover the instance from the unavailable state, the RDS for PostgreSQL automation services automatically restart\.

#### Manage the number of connections<a name="wait-event.iobuffile.actions.tuning-memory.connections"></a>

Suppose that your database instance has 5,000 simultaneous connections\. Each connection uses at least 4 MiB of `work_mem`\. The high memory consumption of the connections is likely to degrade performance\. In response, you have the following options:
+ Upgrade to a larger instance class\.
+ Decrease the number of simultaneous database connections by using a connection proxy or pooler\.

For proxies, consider Amazon RDS Proxy, pgBouncer, or a connection pooler based on your application\. This solution alleviates the CPU load\. It also reduces the risk when all connections require the work memory area\. When fewer database connections exist, you can increase the value of `work_mem`\. In this way, you reduce the occurrence of the `IO:BufFileRead` and `IO:BufFileWrite` wait events\. Also, the queries waiting for the work memory area speed up significantly\.