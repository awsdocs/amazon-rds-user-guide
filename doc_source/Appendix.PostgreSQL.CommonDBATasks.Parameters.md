# Working with parameters on your RDS for PostgreSQL DB instance<a name="Appendix.PostgreSQL.CommonDBATasks.Parameters"></a>

In some cases, you might create an RDS for PostgreSQL DB instance without specifying a custom parameter group\. If so, your DB instance is created using the default parameter group for the version of PostgreSQL that you choose\. For example, suppose that you create an RDS for PostgreSQL DB instance using PostgreSQL 13\.3\. In this case, the DB instance is created using the values in the parameter group for PostgreSQL 13 releases, `default.postgres13`\. 

You can also create your own custom DB parameter group\. You need to do this if you want to modify any settings for the RDS for PostgreSQL DB instance from their default values\. To learn how, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\. 

You can track the settings on your RDS for PostgreSQL DB instance in several different ways\. You can use the AWS Management Console, the AWS CLI, or the Amazon RDS API\. You can also query the values from the PostgreSQL `pg_settings` table of your instance, as shown following\. 

```
SELECT name, setting, boot_val, reset_val, unit
 FROM pg_settings
 ORDER BY name;
```

To learn more about the values returned from this query, see [https://www.postgresql.org/docs/current/view-pg-settings.html](https://www.postgresql.org/docs/current/view-pg-settings.html) in the PostgreSQL documentation\.

Be especially careful when changing the settings for `max_connections` and `shared_buffers` on your RDS for PostgreSQL DB instance\. For example, suppose that you modify settings for `max_connections` or `shared_buffers` and you use values that are too high for your actual workload\. In this case, your RDS for PostgreSQL DB instance won't start\. If this happens, you see an error such as the following in the `postgres.log`\.

```
2018-09-18 21:13:15 UTC::@:[8097]:FATAL:  could not map anonymous shared memory: Cannot allocate memory
2018-09-18 21:13:15 UTC::@:[8097]:HINT:  This error usually means that PostgreSQL's request for a shared memory segment
exceeded available memory or swap space. To reduce the request size (currently 3514134274048 bytes), reduce 
PostgreSQL's shared memory usage, perhaps by reducing shared_buffers or max_connections.
```

However, you can't change any values of the settings contained in the default RDS for PostgreSQL DB parameter groups\. To change settings for any parameters, first create a custom DB parameter group\. Then change the settings in that custom group, and then apply the custom parameter group to your RDS for PostgreSQL DB instance\. To learn more, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\. 

There are two types of RDS for PostgreSQL DB parameters\.
+ **Static parameters** – Static parameters require that the RDS for PostgreSQL DB instance be rebooted after a change so that the new value can take effect\.
+ **Dynamic parameters** – Dynamic parameters don't require a reboot after changing their settings\.

**Note**  
If your RDS for PostgreSQL DB instance is using your own custom DB parameter group, you can change the values of dynamic parameters on the running DB instance\. You can do this by using the AWS Management Console, the AWS CLI, or the Amazon RDS API\. 

If you have privileges to do so, you can also change parameter values by using the `ALTER DATABASE`, `ALTER ROLE`, and `SET` commands\. 

## RDS for PostgreSQL DB instance parameter list<a name="Appendix.PostgreSQL.CommonDBATasks.Parameters.parameters-list"></a>

The following table lists some \(but not all\) parameters available in an RDS for PostgreSQL DB instance\. To view all available parameters, you use the [describe\-db\-parameters](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-parameters.html) AWS CLI command\. For example, to get the list of all parameters available in the default parameter group for RDS for PostgreSQL version 13, run the following\.

```
aws rds describe-db-parameters --db-parameter-group-name default.postgres13
```

You can also use the Console\. Choose **Parameter groups** from the Amazon RDS menu, and then choose the parameter group from those available in your AWS Region\.


|  Parameter name  |  Apply\_Type  |  Description  | 
| --- | --- | --- | 
|  `application_name`  | Dynamic | Sets the application name to be reported in statistics and logs\. | 
|  `archive_command`  | Dynamic | Sets the shell command that will be called to archive a WAL file\. | 
|  `array_nulls`  | Dynamic | Enables input of NULL elements in arrays\. | 
|  `authentication_timeout`  | Dynamic | Sets the maximum allowed time to complete client authentication\. | 
|  `autovacuum`  | Dynamic | Starts the autovacuum subprocess\. | 
|  `autovacuum_analyze_scale_factor`  | Dynamic | Number of tuple inserts, updates, or deletes before analyze as a fraction of reltuples\. | 
|  `autovacuum_analyze_threshold`  | Dynamic | Minimum number of tuple inserts, updates, or deletes before analyze\. | 
|  `autovacuum_freeze_max_age`  | Static | Age at which to autovacuum a table to prevent transaction ID wraparound\.  | 
|  `autovacuum_naptime`  | Dynamic | Time to sleep between autovacuum runs\. | 
|  `autovacuum_max_workers`  | Static | Sets the maximum number of simultaneously running autovacuum worker processes\. | 
|  `autovacuum_vacuum_cost_delay`  | Dynamic | Vacuum cost delay, in milliseconds, for autovacuum\. | 
|  `autovacuum_vacuum_cost_limit`  | Dynamic | Vacuum cost amount available before napping, for autovacuum\. | 
|  `autovacuum_vacuum_scale_factor`  | Dynamic | Number of tuple updates or deletes before vacuum as a fraction of reltuples\. | 
|  `autovacuum_vacuum_threshold`  | Dynamic | Minimum number of tuple updates or deletes before vacuum\. | 
|  `backslash_quote`  | Dynamic | Sets whether a backslash \(\\\) is allowed in string literals\. | 
|  `bgwriter_delay`  | Dynamic | Background writer sleep time between rounds\. | 
|  `bgwriter_lru_maxpages`  | Dynamic | Background writer maximum number of LRU pages to flush per round\. | 
|  `bgwriter_lru_multiplier`  | Dynamic | Multiple of the average buffer usage to free per round\. | 
|  `bytea_output`  | Dynamic | Sets the output format for bytes\. | 
|  `check_function_bodies`  | Dynamic | Checks function bodies during CREATE FUNCTION\. | 
|  `checkpoint_completion_target`  | Dynamic | Time spent flushing dirty buffers during checkpoint, as a fraction of the checkpoint interval\. | 
|  `checkpoint_segments`  | Dynamic | Sets the maximum distance in log segments between automatic write\-ahead log \(WAL\) checkpoints\. | 
|  `checkpoint_timeout`  | Dynamic | Sets the maximum time between automatic WAL checkpoints\. | 
|  `checkpoint_warning`  | Dynamic | Enables warnings if checkpoint segments are filled more frequently than this\. | 
|  `client_encoding`  | Dynamic | Sets the client's character set encoding\. | 
|  `client_min_messages`  | Dynamic | Sets the message levels that are sent to the client\. | 
|  `commit_delay`  | Dynamic | Sets the delay in microseconds between transaction commit and flushing WAL to disk\. | 
|  `commit_siblings`  | Dynamic | Sets the minimum concurrent open transactions before performing commit\_delay\. | 
|  `constraint_exclusion`  | Dynamic | Enables the planner to use constraints to optimize queries\. | 
|  `cpu_index_tuple_cost`  | Dynamic | Sets the planner's estimate of the cost of processing each index entry during an index scan\. | 
|  `cpu_operator_cost`  | Dynamic | Sets the planner's estimate of the cost of processing each operator or function call\. | 
|  `cpu_tuple_cost`  | Dynamic | Sets the planner's estimate of the cost of processing each tuple \(row\)\. | 
|  `cursor_tuple_fraction`  | Dynamic | Sets the planner's estimate of the fraction of a cursor's rows that will be retrieved\. | 
|  `datestyle`  | Dynamic | Sets the display format for date and time values\. | 
|  `deadlock_timeout`  | Dynamic | Sets the time to wait on a lock before checking for deadlock\. | 
|  `debug_pretty_print`  | Dynamic | Indents parse and plan tree displays\. | 
|  `debug_print_parse`  | Dynamic | Logs each query's parse tree\. | 
|  `debug_print_plan`  | Dynamic | Logs each query's execution plan\. | 
|  `debug_print_rewritten`  | Dynamic | Logs each query's rewritten parse tree\. | 
|  `default_statistics_target`  | Dynamic | Sets the default statistics target\. | 
|  `default_tablespace`  | Dynamic | Sets the default tablespace to create tables and indexes in\. | 
|  `default_transaction_deferrable`  | Dynamic | Sets the default deferrable status of new transactions\. | 
|  `default_transaction_isolation`  | Dynamic | Sets the transaction isolation level of each new transaction\. | 
|  `default_transaction_read_only`  | Dynamic | Sets the default read\-only status of new transactions\. | 
|  `default_with_oids`  | Dynamic | Creates new tables with object IDs \(OIDs\) by default\. | 
|  `effective_cache_size`  | Dynamic | Sets the planner's assumption about the size of the disk cache\. | 
|  `effective_io_concurrency`  | Dynamic | Number of simultaneous requests that can be handled efficiently by the disk subsystem\. | 
|  `enable_bitmapscan`  | Dynamic | Enables the planner's use of bitmap\-scan plans\. | 
|  `enable_hashagg`  | Dynamic | Enables the planner's use of hashed aggregation plans\. | 
|  `enable_hashjoin`  | Dynamic | Enables the planner's use of hash join plans\. | 
|  `enable_indexscan`  | Dynamic | Enables the planner's use of index\-scan plans\. | 
|  `enable_material`  | Dynamic | Enables the planner's use of materialization\. | 
|  `enable_mergejoin`  | Dynamic | Enables the planner's use of merge join plans\. | 
|  `enable_nestloop`  | Dynamic | Enables the planner's use of nested\-loop join plans\. | 
|  `enable_seqscan`  | Dynamic | Enables the planner's use of sequential\-scan plans\. | 
|  `enable_sort`  | Dynamic | Enables the planner's use of explicit sort steps\. | 
|  `enable_tidscan`  | Dynamic | Enables the planner's use of TID scan plans\. | 
|  `escape_string_warning`  | Dynamic | Warns about backslash \(\\\) escapes in ordinary string literals\. | 
|  `extra_float_digits`  | Dynamic | Sets the number of digits displayed for floating\-point values\. | 
|  `from_collapse_limit`  | Dynamic | Sets the FROM\-list size beyond which subqueries are not collapsed\. | 
|  `fsync`  | Dynamic | Forces synchronization of updates to disk\. | 
|  `full_page_writes`  | Dynamic | Writes full pages to WAL when first modified after a checkpoint\. | 
|  `geqo`  | Dynamic | Enables genetic query optimization\. | 
|  `geqo_effort`  | Dynamic | GEQO: effort is used to set the default for other GEQO parameters\. | 
|  `geqo_generations`  | Dynamic | GEQO: number of iterations of the algorithm\. | 
|  `geqo_pool_size`  | Dynamic | GEQO: number of individuals in the population\. | 
|  `geqo_seed`  | Dynamic | GEQO: seed for random path selection\. | 
|  `geqo_selection_bias`  | Dynamic | GEQO: selective pressure within the population\. | 
|  `geqo_threshold`  | Dynamic | Sets the threshold of FROM items beyond which GEQO is used\. | 
|  `gin_fuzzy_search_limit`  | Dynamic | Sets the maximum allowed result for exact search by GIN\. | 
|  `hot_standby_feedback`  | Dynamic | Determines whether a hot standby sends feedback messages to the primary or upstream standby\. | 
|  `intervalstyle`  | Dynamic | Sets the display format for interval values\. | 
|  `join_collapse_limit`  | Dynamic | Sets the FROM\-list size beyond which JOIN constructs are not flattened\. | 
|  `lc_messages`  | Dynamic | Sets the language in which messages are displayed\. | 
|  `lc_monetary`  | Dynamic | Sets the locale for formatting monetary amounts\. | 
|  `lc_numeric`  | Dynamic | Sets the locale for formatting numbers\. | 
|  `lc_time`  | Dynamic | Sets the locale for formatting date and time values\. | 
|  `log_autovacuum_min_duration`  | Dynamic | Sets the minimum running time above which autovacuum actions will be logged\. | 
|  `log_checkpoints`  | Dynamic | Logs each checkpoint\. | 
|  `log_connections`  | Dynamic | Logs each successful connection\. | 
|  `log_disconnections`  | Dynamic | Logs end of a session, including duration\. | 
|  `log_duration`  | Dynamic | Logs the duration of each completed SQL statement\. | 
|  `log_error_verbosity`  | Dynamic | Sets the verbosity of logged messages\. | 
|  `log_executor_stats`  | Dynamic | Writes executor performance statistics to the server log\. | 
|  `log_filename`  | Dynamic | Sets the file name pattern for log files\. | 
|  `log_file_mode`  | Dynamic | Sets file permissions for log files\. Default value is 0644\. | 
|  `log_hostname`  | Dynamic | Logs the host name in the connection logs\. As of PostgreSQL 12 and later versions, this parameter is 'off' by default\. When turned on, the connection uses DNS reverse\-lookup to get the hostname that gets captured to the connection logs\. If you turn on this parameter, you should monitor the impact that it has on the time it takes to establish connections\.  | 
|  `log_line_prefix `  | Dynamic | Controls information prefixed to each log line\. | 
|  `log_lock_waits`  | Dynamic | Logs long lock waits\. | 
|  `log_min_duration_statement`  | Dynamic | Sets the minimum running time above which statements will be logged\. | 
|  `log_min_error_statement`  | Dynamic | Causes all statements generating an error at or above this level to be logged\. | 
|  `log_min_messages`  | Dynamic | Sets the message levels that are logged\. | 
|  `log_parser_stats`  | Dynamic | Writes parser performance statistics to the server log\. | 
|  `log_planner_stats`  | Dynamic | Writes planner performance statistics to the server log\. | 
|  `log_rotation_age`  | Dynamic | Automatic log file rotation will occur after N minutes\. | 
|  `log_rotation_size`  | Dynamic | Automatic log file rotation will occur after N kilobytes\. | 
|  `log_statement`  | Dynamic | Sets the type of statements logged\. | 
|  `log_statement_stats`  | Dynamic | Writes cumulative performance statistics to the server log\. | 
|  `log_temp_files`  | Dynamic | Logs the use of temporary files larger than this number of kilobytes\. | 
|  `log_timezone`  | Dynamic | Sets the time zone to use in log messages\. | 
|  `log_truncate_on_rotation`  | Dynamic | Truncate existing log files of same name during log rotation\. | 
|  `logging_collector`  | Static | Start a subprocess to capture stderr output and/or csvlogs into log files\. | 
|  `maintenance_work_mem`  | Dynamic | Sets the maximum memory to be used for maintenance operations\. | 
|  `max_connections`  | Static | Sets the maximum number of concurrent connections\. | 
|  `max_files_per_process`  | Static | Sets the maximum number of simultaneously open files for each server process\. | 
|  `max_locks_per_transaction`  | Static | Sets the maximum number of locks per transaction\. | 
|  `max_pred_locks_per_transaction`  | Static | Sets the maximum number of predicate locks per transaction\. | 
|  `max_prepared_transactions`  | Static | Sets the maximum number of simultaneously prepared transactions\. | 
|  `max_stack_depth`  | Dynamic | Sets the maximum stack depth, in kilobytes\. | 
|  `max_standby_archive_delay`  | Dynamic | Sets the maximum delay before canceling queries when a hot standby server is processing archived WAL data\. | 
|  `max_standby_streaming_delay`  | Dynamic | Sets the maximum delay before canceling queries when a hot standby server is processing streamed WAL data\. | 
| max\_wal\_size | Dynamic | Sets the WAL size \(MB\) that triggers a checkpoint\. For all versions after RDS for PostgreSQL 10, the default is at least 1 GB \(1024 MB\)\. For example, max\_wal\_size setting for RDS for PostgreSQL 14 is 2 GB \(2048 MB\)\. Use the SHOW max\_wal\_size; command on your RDS for PostgreSQL DB instance to see its current value\. | 
| min\_wal\_size | Dynamic | Sets the minimum size to shrink the WAL to\. For PostgreSQL version 9\.6 and earlier, min\_wal\_size is in units of 16 MB\. For PostgreSQL version 10 and later, min\_wal\_size is in units of 1 MB\.  | 
|  `quote_all_identifiers`  | Dynamic | Adds quotes \("\) to all identifiers when generating SQL fragments\. | 
|  `random_page_cost`  | Dynamic | Sets the planner's estimate of the cost of a non\-sequentially fetched disk page\. This parameter has no value unless query plan management \(QPM\) is turned on\. When QPM is on, the default value for this parameter 4\.  | 
| rds\.adaptive\_autovacuum | Dynamic | Automatically tunes the autovacuum parameters whenever the transaction ID thresholds are exceeded\. | 
| rds\.force\_ssl | Dynamic | Requires the use of SSL connections\. Default is 0 \(false\), so connections aren't required \(forced\) to use SSL\. | 
|  `rds.log_retention_period`  | Dynamic | Sets log retention such that Amazon RDS deletes PostgreSQL logs that are older than n minutes\. | 
| rds\.restrict\_password\_commands | Static | Restricts who can manage passwords to users with the rds\_password role\. Set this parameter to 1 to enable password restriction\. The default is 0\.  | 
|  `search_path`  | Dynamic | Sets the schema search order for names that are not schema\-qualified\. | 
|  `seq_page_cost`  | Dynamic | Sets the planner's estimate of the cost of a sequentially fetched disk page\. | 
|  `session_replication_role`  | Dynamic | Sets the sessions behavior for triggers and rewrite rules\. | 
|  `shared_buffers`  | Static | Sets the number of shared memory buffers used by the server\. | 
|  `shared_preload_libraries `  | Static | Lists the shared libraries to preload into the RDS for PostgreSQL DB instance\. Supported values include auto\_explain, orafce, pgaudit, pglogical, pg\_bigm, pg\_cron, pg\_hint\_plan, pg\_prewarm, pg\_similarity, pg\_stat\_statements, pg\_transport, and plprofiler\. | 
|  `ssl`  | Dynamic | Enables SSL connections\. | 
|  `sql_inheritance`  | Dynamic | Causes subtables to be included by default in various commands\. | 
|  `ssl_renegotiation_limit`  | Dynamic | Sets the amount of traffic to send and receive before renegotiating the encryption keys\. | 
|  `standard_conforming_strings`  | Dynamic | Causes \.\.\. strings to treat backslashes literally\. | 
|  `statement_timeout`  | Dynamic | Sets the maximum allowed duration of any statement\. | 
|  `synchronize_seqscans`  | Dynamic | Enables synchronized sequential scans\. | 
|  `synchronous_commit`  | Dynamic | Sets the current transactions synchronization level\. | 
|  `tcp_keepalives_count`  | Dynamic | Maximum number of TCP keepalive retransmits\. | 
|  `tcp_keepalives_idle`  | Dynamic | Time between issuing TCP keepalives\. | 
|  `tcp_keepalives_interval`  | Dynamic | Time between TCP keepalive retransmits\. | 
|  `temp_buffers`  | Dynamic | Sets the maximum number of temporary buffers used by each session\. | 
| temp\_file\_limit | Dynamic | Sets the maximum size in KB to which the temporary files can grow\. | 
|  `temp_tablespaces`  | Dynamic | Sets the tablespaces to use for temporary tables and sort files\. | 
|  `timezone`  | Dynamic | Sets the time zone for displaying and interpreting time stamps\. | 
|  `track_activities`  | Dynamic | Collects information about running commands\. | 
|  `track_activity_query_size`  | Static | Sets the size reserved for pg\_stat\_activity\.current\_query, in bytes\. | 
|  `track_counts`  | Dynamic | Collects statistics on database activity\. | 
|  `track_functions`  | Dynamic | Collects function\-level statistics on database activity\. | 
|  `track_io_timing`  | Dynamic | Collects timing statistics on database I/O activity\. | 
|  `transaction_deferrable`  | Dynamic | Indicates whether to defer a read\-only serializable transaction until it can be started with no possible serialization failures\. | 
|  `transaction_isolation`  | Dynamic | Sets the current transactions isolation level\. | 
|  `transaction_read_only`  | Dynamic | Sets the current transactions read\-only status\. | 
|  `transform_null_equals`  | Dynamic | Treats expr=NULL as expr IS NULL\. | 
|  `update_process_title`  | Dynamic | Updates the process title to show the active SQL command\. | 
|  `vacuum_cost_delay`  | Dynamic | Vacuum cost delay in milliseconds\. | 
|  `vacuum_cost_limit`  | Dynamic | Vacuum cost amount available before napping\. | 
|  `vacuum_cost_page_dirty`  | Dynamic | Vacuum cost for a page dirtied by vacuum\. | 
|  `vacuum_cost_page_hit`  | Dynamic | Vacuum cost for a page found in the buffer cache\. | 
|  `vacuum_cost_page_miss`  | Dynamic | Vacuum cost for a page not found in the buffer cache\. | 
|  `vacuum_defer_cleanup_age`  | Dynamic | Number of transactions by which vacuum and hot cleanup should be deferred, if any\. | 
|  `vacuum_freeze_min_age`  | Dynamic | Minimum age at which vacuum should freeze a table row\. | 
|  `vacuum_freeze_table_age`  | Dynamic | Age at which vacuum should scan a whole table to freeze tuples\. | 
|  `wal_buffers`  | Static | Sets the number of disk\-page buffers in shared memory for WAL\. | 
|  `wal_writer_delay`  | Dynamic | WAL writer sleep time between WAL flushes\. | 
|  `work_mem`  | Dynamic | Sets the maximum memory to be used for query workspaces\. | 
|  `xmlbinary`  | Dynamic | Sets how binary values are to be encoded in XML\. | 
|  `xmloption`  | Dynamic | Sets whether XML data in implicit parsing and serialization operations is to be considered as documents or content fragments\. | 

Amazon RDS uses the default PostgreSQL units for all parameters\. The following table shows the PostgreSQL default unit and value for each parameter\.


|  Parameter name  |  Unit  | 
| --- | --- | 
| `archive_timeout` | s | 
| `authentication_timeout` | s | 
| `autovacuum_naptime` | s | 
| `autovacuum_vacuum_cost_delay` | ms | 
| `bgwriter_delay` | ms | 
| `checkpoint_timeout` | s | 
| `checkpoint_warning` | s | 
| `deadlock_timeout` | ms | 
| `effective_cache_size` | 8 KB | 
| `lock_timeout` | ms | 
| `log_autovacuum_min_duration` | ms | 
| `log_min_duration_statement` | ms | 
| `log_rotation_age` | minutes | 
| `log_rotation_size` | KB | 
| `log_temp_files` | KB | 
| `maintenance_work_mem` | KB | 
| `max_stack_depth` | KB | 
| `max_standby_archive_delay` | ms | 
| `max_standby_streaming_delay` | ms | 
| `post_auth_delay` | s | 
| `pre_auth_delay` | s | 
| `segment_size` | 8 KB | 
| `shared_buffers` | 8 KB | 
| `statement_timeout` | ms | 
| `ssl_renegotiation_limit` | KB | 
| `tcp_keepalives_idle` | s | 
| `tcp_keepalives_interval` | s | 
| `temp_file_limit` | KB | 
| `work_mem` | KB | 
| `temp_buffers` | 8 KB | 
| `vacuum_cost_delay` | ms | 
| `wal_buffers` | 8 KB | 
| `wal_receiver_timeout` | ms | 
| `wal_segment_size` | 8 KB | 
| `wal_sender_timeout` | ms | 
| `wal_writer_delay` | ms | 
| `wal_receiver_status_interval` | s | 