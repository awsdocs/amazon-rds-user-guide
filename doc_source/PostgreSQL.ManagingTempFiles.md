# Managing temporary files with PostgreSQL<a name="PostgreSQL.ManagingTempFiles"></a>

In PostgreSQL, the query performing sort and hash operations use the instance memory to store results up to the value speciﬁed in the [https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-WORK-MEM](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-WORK-MEM) parameter\. When the instance memory is not sufficient, temporary files are created to store the results\. These are written to disk to complete the query execution\. Later, these files are automatically removed after the query completes\. In RDS for PostgreSQL, these files are stored in Amazon EBS on the data volume\. For more information, see [Amazon RDS DB instance storage](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Storage.html)\.

You can use the following parameters and functions to manage the temporary files in your instance\.
+ **[https://www.postgresql.org/docs/current/runtime-config-resource.html#RUNTIME-CONFIG-RESOURCE-DISK](https://www.postgresql.org/docs/current/runtime-config-resource.html#RUNTIME-CONFIG-RESOURCE-DISK)** – This parameter cancels any query exceeding the size of temp\_files in KB\. This limit prevents any query from running endlessly and consuming disk space with temporary files\. You can estimate the value using the results from the `log_temp_files` parameter\. As a best practice, examine the workload behavior and set the limit according to the estimation\. The following example shows how a query is canceled when it exceeds the limit\.

  ```
  postgres=> select * from pgbench_accounts, pg_class, big_table;
  ```

  ```
  ERROR: temporary file size exceeds temp_file_limit (64kB)
  ```
+ **[https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-TEMP-FILES](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-TEMP-FILES)** – This parameter sends messages to the postgresql\.log when the temporary files of a session are removed\. This parameter produces logs after a query successfully completes\. Therefore, it might not help in troubleshooting active, long\-running queries\. 

  The following example shows that when the query successfully completes, the entries are logged in the postgresql\.log file while the temporary files are cleaned up\.

  ```
                      
  2023-02-06 23:48:35 UTC:205.251.233.182(12456):adminuser@postgres:[31236]:LOG:  temporary file: path "base/pgsql_tmp/pgsql_tmp31236.5", size 140353536
  2023-02-06 23:48:35 UTC:205.251.233.182(12456):adminuser@postgres:[31236]:STATEMENT:  select a.aid from pgbench_accounts a, pgbench_accounts b where a.bid=b.bid order by a.bid limit 10;
  2023-02-06 23:48:35 UTC:205.251.233.182(12456):adminuser@postgres:[31236]:LOG:  temporary file: path "base/pgsql_tmp/pgsql_tmp31236.4", size 180428800
  2023-02-06 23:48:35 UTC:205.251.233.182(12456):adminuser@postgres:[31236]:STATEMENT:  select a.aid from pgbench_accounts a, pgbench_accounts b where a.bid=b.bid order by a.bid limit 10;
  ```
+ **[https://www.postgresql.org/docs/current/functions-admin.html#FUNCTIONS-ADMIN-GENFILE](https://www.postgresql.org/docs/current/functions-admin.html#FUNCTIONS-ADMIN-GENFILE)** – This function provides visibility into the current temporary file usage\. The completed query doesn't appear in the results of the function\. In the following example, you can view the results of this function\.

  ```
  postgres=> select * from pg_ls_tmpdir();
  ```

  ```
        name       |    size    |      modification
  -----------------+------------+------------------------
   pgsql_tmp8355.1 | 1072250880 | 2023-02-06 22:54:56+00
   pgsql_tmp8351.0 | 1072250880 | 2023-02-06 22:54:43+00
   pgsql_tmp8327.0 | 1072250880 | 2023-02-06 22:54:56+00
   pgsql_tmp8351.1 |  703168512 | 2023-02-06 22:54:56+00
   pgsql_tmp8355.0 | 1072250880 | 2023-02-06 22:54:00+00
   pgsql_tmp8328.1 |  835031040 | 2023-02-06 22:54:56+00
   pgsql_tmp8328.0 | 1072250880 | 2023-02-06 22:54:40+00
  (7 rows)
  ```

  ```
  postgres=> select query from pg_stat_activity where pid = 8355;
                  
  query
  ----------------------------------------------------------------------------------------
  select a.aid from pgbench_accounts a, pgbench_accounts b where a.bid=b.bid order by a.bid
  (1 row)
  ```

  The file name includes the processing ID \(PID\) of the session that generated the temporary file\. A more advanced query, such as in the following example, performs a sum of the temporary files for each PID\.

  ```
  postgres=> select replace(left(name, strpos(name, '.')-1),'pgsql_tmp','') as pid, count(*), sum(size) from pg_ls_tmpdir() group by pid;
  ```

  ```
   pid  | count |   sum
  ------+-------------------
   8355 |     2 | 2144501760
   8351 |     2 | 2090770432
   8327 |     1 | 1072250880
   8328 |     2 | 2144501760
  (4 rows)
  ```
+ **`[ pg\_stat\_statements](https://www.postgresql.org/docs/current/pgstatstatements.html)`** – If you activate the pg\_stat\_statements parameter, then you can view the average temporary file usage per call\. You can identify the query\_id of the query and use it to examine the temporary file usage as shown in the following example\.

  ```
  postgres=> select queryid from pg_stat_statements where query like 'select a.aid from pgbench%';
  ```

  ```
         queryid
  ----------------------
   -7170349228837045701
  (1 row)
  ```

  ```
  postgres=> select queryid, substr(query,1,25), calls, temp_blks_read/calls temp_blks_read_per_call, temp_blks_written/calls temp_blks_written_per_call from pg_stat_statements where queryid = -7170349228837045701;
  ```

  ```
         queryid        |          substr           | calls | temp_blks_read_per_call | temp_blks_written_per_call
  ----------------------+---------------------------+-------+-------------------------+----------------------------
   -7170349228837045701 | select a.aid from pgbench |    50 |                  239226 |                     388678
  (1 row)
  ```
+ **`[Performance Insights](https://aws.amazon.com/rds/performance-insights/)`** – In the Performance Insights dashboard, you can view temporary file usage by turning on the metrics **temp\_bytes** and **temp\_files**\. Then, you can see the average of both of these metrics and see how they correspond to the query workload\. The view within Performance Insights doesn't show specifically the queries that are generating the temporary files\. However, when you combine Performance Insights with the query shown for `pg_ls_tmpdir`, you can troubleshoot, analyze, and determine the changes in your query workload\. 

  For more information about how to analyze metrics and queries with Performance Insights, see [Analyzing metrics with the Performance Insights dashboard](USER_PerfInsights.UsingDashboard.md)

**To view the temporary file usage with Performance Insights**

  1. In the Performance Insights dashboard, choose **Manage Metrics**\.

  1. Select the **temp\_bytes** and **temp\_files** **database metrics** as shown in the following screenshot\.  
![\[Metrics displayed in the graph.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/rpg_mantempfiles_metrics.png)

  1. In the **Top SQL** tab, choose the **Preferences** icon\.

  1. In the **Preferences** window, turn on the following statistics to appear in the **Top SQL**tab and choose **Continue**\.
     + Temp writes/sec
     + Temp reads/sec
     + Tmp blk write/call
     + Tmp blk read/call

  1. The temporary file is broken out when combined with the query shown for `pg_ls_tmpdir`, as shown in the following example\.  
![\[Query that displays the temporary file usage.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/rpg_mantempfiles_query.png)

**Note**  
The [https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-WORK-MEM](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-WORK-MEM) parameter controls when the sort operation runs out of memory and results are written into temporary files\. We recommend that you don't change the setting of this parameter higher than the default value because it would permit every database session to consume more memory\. Also, a single session that performs complex joins and sorts can perform parallel operations in which each operation consumes memory\.   
As a best practice, when you have a large report with multiple joins and sorts, set this parameter at the session level by using the `SET work_mem` command\. Then the change is only applied to the current session and doesn't change the value globally\.