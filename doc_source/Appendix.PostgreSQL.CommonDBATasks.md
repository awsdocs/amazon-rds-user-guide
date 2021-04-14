# Common DBA tasks for PostgreSQL<a name="Appendix.PostgreSQL.CommonDBATasks"></a>

This section describes the Amazon RDS implementations of some common DBA tasks for DB instances running the PostgreSQL database engine\. To deliver a managed service experience, Amazon RDS doesn't provide shell access to DB instances, and it restricts access to certain system procedures and tables that require advanced privileges\. 

For information about working with PostgreSQL log files on Amazon RDS, see [PostgreSQL database log files](USER_LogAccess.Concepts.PostgreSQL.md)\.

**Topics**
+ [Creating roles](#Appendix.PostgreSQL.CommonDBATasks.Roles)
+ [Managing PostgreSQL database access](#Appendix.PostgreSQL.CommonDBATasks.Access)
+ [Working with PostgreSQL parameters](#Appendix.PostgreSQL.CommonDBATasks.Parameters)
+ [Audit logging for a PostgreSQL DB instance](#Appendix.PostgreSQL.CommonDBATasks.Auditing)
+ [Working with the pgaudit extension](#Appendix.PostgreSQL.CommonDBATasks.pgaudit)
+ [Working with the pg\_repack extension](#Appendix.PostgreSQL.CommonDBATasks.pg_repack)
+ [Using pgBadger for log analysis with PostgreSQL](#Appendix.PostgreSQL.CommonDBATasks.Badger)
+ [Viewing the contents of pg\_config](#Appendix.PostgreSQL.CommonDBATasks.Viewingpgconfig)
+ [Working with the orafce extension](#Appendix.PostgreSQL.CommonDBATasks.orafce)
+ [Accessing external data with the postgres\_fdw extension](#postgresql-commondbatasks-fdw)
+ [Restricting password management](#Appendix.PostgreSQL.CommonDBATasks.RestrictPasswordMgmt)
+ [Working with PostgreSQL autovacuum on Amazon RDS](Appendix.PostgreSQL.CommonDBATasks.Autovacuum.md)
+ [Working with the PostGIS extension](Appendix.PostgreSQL.CommonDBATasks.PostGIS.md)
+ [Using a custom DNS server for outbound network access](Appendix.PostgreSQL.CommonDBATasks.CustomDNS.md)
+ [Scheduling maintenance with the PostgreSQL pg\_cron extension](PostgreSQL_pg_cron.md)
+ [Managing PostgreSQL partitions with the pg\_partman extension](PostgreSQL_Partitions.md)
+ [Invoking an AWS Lambda function from an RDS for PostgreSQL DB instance](PostgreSQL-Lambda.md)

## Creating roles<a name="Appendix.PostgreSQL.CommonDBATasks.Roles"></a>

When you create a DB instance, the master user system account that you create is assigned to the `rds_superuser` role\. The `rds_superuser` role is a predefined Amazon RDS role similar to the PostgreSQL superuser role \(customarily named `postgres` in local instances\), but with some restrictions\. As with the PostgreSQL superuser role, the `rds_superuser` role has the most privileges for your DB instance\. You should not assign this role to users unless they need the most access to the DB instance\.

The `rds_superuser` role can do the following:
+ Add extensions that are available for use with Amazon RDS\. For more information, see [Some supported PostgreSQL features](CHAP_PostgreSQL.md#PostgreSQL.Concepts.General.FeatureSupport) and the [PostgreSQL documentation](http://www.postgresql.org/docs/9.4/static/sql-createextension.html)\.
+ Manage tablespaces, including creating and deleting them\. For more information, see [ Tablespaces for PostgreSQL on Amazon RDS](CHAP_PostgreSQL.md#PostgreSQL.Concepts.General.FeatureSupport.Tablespaces) and the [Tablespaces](http://www.postgresql.org/docs/9.4/static/manage-ag-tablespaces.html) section in the PostgreSQL documentation\.
+ View all users not assigned the `rds_superuser` role using the `pg_stat_activity` command and stop their connections using the `pg_terminate_backend` and `pg_cancel_backend` commands\.
+ Grant and revoke the `rds_replication` role for all roles that are not the `rds_superuser` role\. For more information, see the [GRANT](http://www.postgresql.org/docs/9.4/static/sql-grant.html) section in the PostgreSQL documentation\.

The following example shows how to create a user and then grant the user the `rds_superuser` role\. User\-defined roles, such as `rds_superuser`, have to be granted\.

```
create role testuser with password 'testuser' login;
grant rds_superuser to testuser;
```

## Managing PostgreSQL database access<a name="Appendix.PostgreSQL.CommonDBATasks.Access"></a>

In Amazon RDS for PostgreSQL, you can manage which users have privileges to connect to which databases\. In other PostgreSQL environments, you sometimes perform this kind of management by modifying the `pg_hba.conf` file\. In Amazon RDS, you can use database grants instead\.

New databases in PostgreSQL are always created with a default set of privileges\. The default privileges allow `PUBLIC` \(all users\) to connect to the database and to create temporary tables while connected\. 

To control which users are allowed to connect to a given database in Amazon RDS, first revoke the default `PUBLIC` privileges\. Then grant back the privileges on a more granular basis\. The following example code shows how\.

```
psql> revoke all on database <database-name> from public;
psql> grant connect, temporary on database <database-name> to <user/role name>;
```

For more information about privileges in PostgreSQL databases, see the [https://www.postgresql.org/docs/current/static/sql-grant.html](https://www.postgresql.org/docs/current/static/sql-grant.html) command in the PostgreSQL documentation\.

## Working with PostgreSQL parameters<a name="Appendix.PostgreSQL.CommonDBATasks.Parameters"></a>

PostgreSQL parameters that you set for a local PostgreSQL instance in the *postgresql\.conf* file are maintained in the DB parameter group for your DB instance\. If you create a DB instance using the default parameter group, the parameter settings are in the parameter group called *default\.postgres9\.6*\.

 When you create a DB instance, the parameters in the associated DB parameter group are loaded\. You can modify parameter values by changing values in the parameter group\. You can also change parameter values, if you have the security privileges to do so, by using the ALTER DATABASE, ALTER ROLE, and SET commands\. You can't use the command line `postgres` command or the `env PGOPTIONS` command, because you have no access to the host\. 

Keeping track of PostgreSQL parameter settings can occasionally be difficult\. Use the following command to list current parameter settings and the default value\.

```
select name, setting, boot_val, reset_val, unit
from pg_settings
order by name;
```

For an explanation of the output values, see the [https://www.postgresql.org/docs/current/view-pg-settings.html](https://www.postgresql.org/docs/current/view-pg-settings.html) topic in the PostgreSQL documentation\.

 If you set the memory settings too large for `max_connections` or `shared_buffers`, you will prevent the PostgreSQL instance from starting up\. Some parameters use units that you might not be familiar with; for example, `shared_buffers` sets the number of 8\-KB shared memory buffers used by the server\. 

The following error is written to the *postgres\.log* file when the instance is attempting to start up, but incorrect parameter settings are preventing it from starting\.

```
2013-09-18 21:13:15 UTC::@:[8097]:FATAL:  could not map anonymous shared
memory: Cannot allocate memory
2013-09-18 21:13:15 UTC::@:[8097]:HINT:  This error usually means that 
PostgreSQL's request for a shared memory segment exceeded available memory or 
swap space. To reduce the request size (currently 3514134274048 bytes), reduce 
PostgreSQL's shared memory usage, perhaps by reducing shared_buffers or 
max_connections.
```

There are two types of PostgreSQL parameters, static and dynamic\. Static parameters require that the DB instance be rebooted before they are applied\. Dynamic parameters can be applied immediately\. The following table shows parameters that you can modify for a PostgreSQL DB instance and each parameter's type\. 


|  Parameter name  |  Apply\_Type  |  Description  | 
| --- | --- | --- | 
|  `application_name`  | Dynamic | Sets the application name to be reported in statistics and logs\. | 
|  `array_nulls`  | Dynamic | Enables input of NULL elements in arrays\. | 
|  `authentication_timeout`  | Dynamic | Sets the maximum allowed time to complete client authentication\. | 
|  `autovacuum`  | Dynamic | Starts the autovacuum subprocess\. | 
|  `autovacuum_analyze_scale_factor`  | Dynamic | Number of tuple inserts, updates, or deletes before analyze as a fraction of reltuples\. | 
|  `autovacuum_analyze_threshold`  | Dynamic | Minimum number of tuple inserts, updates, or deletes before analyze\. | 
|  `autovacuum_naptime`  | Dynamic | Time to sleep between autovacuum runs\. | 
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
|  `default_with_oids`  | Dynamic | Creates new tables with OIDs by default\. | 
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
|  `log_hostname`  | Dynamic | Logs the host name in the connection logs\. | 
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
|  `maintenance_work_mem`  | Dynamic | Sets the maximum memory to be used for maintenance operations\. | 
|  `max_stack_depth`  | Dynamic | Sets the maximum stack depth, in kilobytes\. | 
|  `max_standby_archive_delay`  | Dynamic | Sets the maximum delay before canceling queries when a hot standby server is processing archived WAL data\. | 
|  `max_standby_streaming_delay`  | Dynamic | Sets the maximum delay before canceling queries when a hot standby server is processing streamed WAL data\. | 
| max\_wal\_size | Static | Sets the WAL size that triggers the checkpoint\. For PostgreSQL version 9\.6 and earlier, max\_wal\_size is in units of 16 MB\. For PostgreSQL version 10 and later, max\_wal\_size is in units of 1 MB\.  | 
| min\_wal\_size | Static | Sets the minimum size to shrink the WAL to\. For PostgreSQL version 9\.6 and earlier, min\_wal\_size is in units of 16 MB\. For PostgreSQL version 10 and later, min\_wal\_size is in units of 1 MB\.  | 
|  `quote_all_identifiers`  | Dynamic | Adds quotes \("\) to all identifiers when generating SQL fragments\. | 
|  `random_page_cost`  | Dynamic | Sets the planner's estimate of the cost of a non\-sequentially fetched disk page\. | 
| rds\.adaptive\_autovacuum | Dynamic | Automatically tunes the autovacuum parameters whenever the transaction ID thresholds are exceeded\. | 
|  `rds.log_retention_period`  | Dynamic | Sets log retention such that Amazon RDS deletes PostgreSQL logs that are older than N minutes\. | 
| rds\.restrict\_password\_commands | Static | Restricts who can manage passwords to users with the rds\_password role\. Set this parameter to 1 to enable password restriction\. The default is 0\.  | 
|  `search_path`  | Dynamic | Sets the schema search order for names that are not schema\-qualified\. | 
|  `seq_page_cost`  | Dynamic | Sets the planner's estimate of the cost of a sequentially fetched disk page\. | 
|  `session_replication_role`  | Dynamic | Sets the sessions behavior for triggers and rewrite rules\. | 
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
|  `temp_tablespaces`  | Dynamic | Sets the tablespaces to use for temporary tables and sort files\. | 
|  `timezone`  | Dynamic | Sets the time zone for displaying and interpreting time stamps\. | 
|  `track_activities`  | Dynamic | Collects information about running commands\. | 
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
|  `wal_writer_delay`  | Dynamic | WAL writer sleep time between WAL flushes\. | 
|  `work_mem`  | Dynamic | Sets the maximum memory to be used for query workspaces\. | 
|  `xmlbinary`  | Dynamic | Sets how binary values are to be encoded in XML\. | 
|  `xmloption`  | Dynamic | Sets whether XML data in implicit parsing and serialization operations is to be considered as documents or content fragments\. | 
|  `autovacuum_freeze_max_age`  | Static | Age at which to autovacuum a table to prevent transaction ID wraparound\.  | 
|  `autovacuum_max_workers`  | Static | Sets the maximum number of simultaneously running autovacuum worker processes\. | 
|  `max_connections`  | Static | Sets the maximum number of concurrent connections\. | 
|  `max_files_per_process`  | Static | Sets the maximum number of simultaneously open files for each server process\. | 
|  `max_locks_per_transaction`  | Static | Sets the maximum number of locks per transaction\. | 
|  `max_pred_locks_per_transaction`  | Static | Sets the maximum number of predicate locks per transaction\. | 
|  `max_prepared_transactions`  | Static | Sets the maximum number of simultaneously prepared transactions\. | 
|  `shared_buffers`  | Static | Sets the number of shared memory buffers used by the server\. | 
|  `ssl`  | Static | Enables SSL connections\. | 
| temp\_file\_limit | Static | Sets the maximum size in KB to which the temporary files can grow\. | 
|  `track_activity_query_size`  | Static | Sets the size reserved for pg\_stat\_activity\.current\_query, in bytes\. | 
|  `wal_buffers`  | Static | Sets the number of disk\-page buffers in shared memory for WAL\. | 

Amazon RDS uses the default PostgreSQL units for all parameters\. The following table shows the PostgreSQL default unit and value for each parameter\.


|  Parameter name  |  Unit  | 
| --- | --- | 
| `effective_cache_size` | 8 KB | 
| `segment_size` | 8 KB | 
| `shared_buffers` | 8 KB | 
| `temp_buffers` | 8 KB | 
| `wal_buffers` | 8 KB | 
| `wal_segment_size` | 8 KB | 
| `log_rotation_size` | KB | 
| `log_temp_files` | KB | 
| `maintenance_work_mem` | KB | 
| `max_stack_depth` | KB | 
| `ssl_renegotiation_limit` | KB | 
| temp\_file\_limit | KB | 
| `work_mem` | KB | 
| `log_rotation_age` | minutes | 
| `autovacuum_vacuum_cost_delay` | ms | 
| `bgwriter_delay` | ms | 
| `deadlock_timeout` | ms | 
| `lock_timeout` | ms | 
| `log_autovacuum_min_duration` | ms | 
| `log_min_duration_statement` | ms | 
| `max_standby_archive_delay` | ms | 
| `max_standby_streaming_delay` | ms | 
| `statement_timeout` | ms | 
| `vacuum_cost_delay` | ms | 
| `wal_receiver_timeout` | ms | 
| `wal_sender_timeout` | ms | 
| `wal_writer_delay` | ms | 
| `archive_timeout` | s | 
| `authentication_timeout` | s | 
| `autovacuum_naptime` | s | 
| `checkpoint_timeout` | s | 
| `checkpoint_warning` | s | 
| `post_auth_delay` | s | 
| `pre_auth_delay` | s | 
| `tcp_keepalives_idle` | s | 
| `tcp_keepalives_interval` | s | 
| `wal_receiver_status_interval` | s | 

## Audit logging for a PostgreSQL DB instance<a name="Appendix.PostgreSQL.CommonDBATasks.Auditing"></a>

There are several parameters you can set to log activity that occurs on your PostgreSQL DB instance\. These parameters include the following:
+  The `log_statement` parameter can be used to log user activity in your PostgreSQL database\. For more information, see [PostgreSQL database log files](USER_LogAccess.Concepts.PostgreSQL.md)\.
+ The `rds.force_admin_logging_level` parameter logs actions by the RDS internal user \(rdsadmin\) in the databases on the DB instance, and writes the output to the PostgreSQL error log\. Allowed values are disabled, debug5, debug4, debug3, debug2, debug1, info, notice, warning, error, log, fatal, and panic\. The default value is disabled\.
+ The `rds.force_autovacuum_logging_level` parameter logs autovacuum worker operations in all databases on the DB instance, and writes the output to the PostgreSQL error log\. Allowed values are disabled, debug5, debug4, debug3, debug2, debug1, info, notice, warning, error, log, fatal, and panic\. The default value is disabled\. The Amazon RDS recommended setting for rds\.force\_autovacuum\_logging\_level: is LOG\. Set log\_autovacuum\_min\_duration to a value from 1000 or 5000\. Setting this value to 5,000 writes activity to the log that takes more than 5 seconds and shows "vacuum skipped" messages\. For more information on this parameter, see [Best practices for working with PostgreSQL](CHAP_BestPractices.md#CHAP_BestPractices.PostgreSQL)\. 

## Working with the pgaudit extension<a name="Appendix.PostgreSQL.CommonDBATasks.pgaudit"></a>

The `pgaudit` extension provides detailed session and object audit logging for Amazon RDS for PostgreSQL version 9\.6\.3 and later and version 9\.5\.7 version and later\. You can enable session auditing or object auditing using this extension\. 

With session auditing, you can log audit events from various sources and includes the fully qualified command text when available\. For example, you can use session auditing to log all READ statements that connect to a database by setting pgaudit\.log to `READ`\.

With object auditing, you can refine the audit logging to work with specific commands\. For example, you can specify that you want audit logging for READ operations on a specific number of tables\.

**To use object based logging with the `pgaudit` extension**

1. Create a database role called `rds_pgaudit` using the following command\.

   ```
   CREATE ROLE rds_pgaudit;
   ```

1. Modify the parameter group that is associated with your DB instance to do the following:
   + Use the shared preload libraries that contain `pgaudit`\.
   + Set `pgaudit.role` to the role `rds_pgaudit`\.

   The following commands modify a custom parameter group\.

   ```
   aws rds modify-db-parameter-group \
      --db-parameter-group-name rds-parameter-group-96 \
      --parameters "ParameterName=pgaudit.role,ParameterValue=rds_pgaudit,ApplyMethod=pending-reboot" \
      --region us-west-2
   
   aws rds modify-db-parameter-group \
      --db-parameter-group-name rds-parameter-group-96 \
      --parameters "ParameterName=shared_preload_libraries,ParameterValue=pgaudit,ApplyMethod=pending-reboot" \
      --region us-west-2
   ```

1. Reboot the instance so that the DB instance picks up the changes to the parameter group\.

   ```
   aws rds reboot-db-instance \
       --db-instance-identifier rds-test-instance \
       --region us-west-2
   ```

1. Run the following command to confirm that `pgaudit` has been initialized\.

   ```
   SHOW shared_preload_libraries;
   
   shared_preload_libraries 
   --------------------------
   rdsutils,pgaudit
   (1 row)
   ```

1. Run the following command to create the `pgaudit` extension\.

   ```
   CREATE EXTENSION pgaudit;
   ```

1. Run the following command to confirm `pgaudit.role` is set to *rds\_pgaudit*\.

   ```
   SHOW pgaudit.role;
   
   pgaudit.role 
   ------------------
   rds_pgaudit
   ```

To test the audit logging, run several commands that you have chosen to audit\. For example, you might run the following commands\.

```
CREATE TABLE t1 (id int);
GRANT SELECT ON t1 TO rds_pgaudit;
SELECT * FROM t1;

id 
----
(0 rows)
```

The database logs should contain an entry similar to the following\.

```
...
2017-06-12 19:09:49 UTC:...:rds_test@postgres:[11701]:LOG: AUDIT:
OBJECT,1,1,READ,SELECT,TABLE,public.t1,select * from t1;
...
```

For information on viewing the logs, see [Accessing Amazon RDS database log files](USER_LogAccess.md)\.

## Working with the pg\_repack extension<a name="Appendix.PostgreSQL.CommonDBATasks.pg_repack"></a>

You can use the `pg_repack` extension to remove bloat from tables and indexes\. This extension is supported on Amazon RDS for PostgreSQL versions 9\.6\.3 and later\. For more information on the `pg_repack` extension, see the [GitHub project documentation](https://reorg.github.io/pg_repack/)\.

**To use the pg\_repack extension**

1. Install the `pg_repack` extension on your Amazon RDS for PostgreSQL DB instance by running the following command\.

   ```
   CREATE EXTENSION pg_repack;
   ```

1. Use the pg\_repack client utility to connect to a database\. Use a database role that has *rds\_superuser* privileges to connect to the database\. In the following connection example, the *rds\_test* role has *rds\_superuser* privileges, and the database endpoint used is *rds\-test\-instance\.cw7jjfgdr4on8\.us\-west\-2\.rds\.amazonaws\.com*\.

   ```
   pg_repack -h rds-test-instance.cw7jjfgdr4on8.us-west-2.rds.amazonaws.com -U rds_test -k postgres
   ```

   Connect using the \-k option\. The \-a option is not supported\.

1. The response from the pg\_repack client provides information on the tables on the DB instance that are repacked\.

   ```
   INFO: repacking table "pgbench_tellers"
   INFO: repacking table "pgbench_accounts"
   INFO: repacking table "pgbench_branches"
   ```

## Using pgBadger for log analysis with PostgreSQL<a name="Appendix.PostgreSQL.CommonDBATasks.Badger"></a>

You can use a log analyzer such as [pgbadger](http://dalibo.github.io/pgbadger/) to analyze PostgreSQL logs\. The *pgbadger* documentation states that the %l pattern \(log line for session/process\) should be a part of the prefix\. However, if you provide the current rds log\_line\_prefix as a parameter to *pgbadger* it should still produce a report\. 

For example, the following command correctly formats an Amazon RDS for PostgreSQL log file dated 2014\-02\-04 using *pgbadger*\.

```
./pgbadger -p '%t:%r:%u@%d:[%p]:' postgresql.log.2014-02-04-00 
```

## Viewing the contents of pg\_config<a name="Appendix.PostgreSQL.CommonDBATasks.Viewingpgconfig"></a>

In PostgreSQL version 9\.6\.1, you can see the compile\-time configuration parameters of the currently installed version of PostgreSQL using the new view pg\_config\. You can use the view by calling the pg\_config function as shown in the following sample\.

```
select * from pg_config();
       name        |             setting

-------------------+---------------------------------------------------------------------------
-----------------------------------------------------------------------------------------------
 BINDIR            | /rdsdbbin/postgres-9.6.1.R1/bin
 DOCDIR            | /rdsdbbin/postgres-9.6.1.R1/share/doc
 HTMLDIR           | /rdsdbbin/postgres-9.6.1.R1/share/doc
 INCLUDEDIR        | /rdsdbbin/postgres-9.6.1.R1/include
 PKGINCLUDEDIR     | /rdsdbbin/postgres-9.6.1.R1/include
 INCLUDEDIR-SERVER | /rdsdbbin/postgres-9.6.1.R1/include/server
 LIBDIR            | /rdsdbbin/postgres-9.6.1.R1/lib
 PKGLIBDIR         | /rdsdbbin/postgres-9.6.1.R1/lib
 LOCALEDIR         | /rdsdbbin/postgres-9.6.1.R1/share/locale
 MANDIR            | /rdsdbbin/postgres-9.6.1.R1/share/man
 SHAREDIR          | /rdsdbbin/postgres-9.6.1.R1/share
 SYSCONFDIR        | /rdsdbbin/postgres-9.6.1.R1/etc
 PGXS              | /rdsdbbin/postgres-9.6.1.R1/lib/pgxs/src/makefiles/pgxs.mk
 CONFIGURE         | '--prefix=/rdsdbbin/postgres-9.6.1.R1' '--with-openssl' '--with-perl' 
 '--with-tcl' '--with-ossp-uuid' '--with-libxml' '--with-libraries=/rdsdbbin
/postgres-9.6.1.R1/lib' '--with-includes=/rdsdbbin/postgres-9.6.1.R1/include' '--enable-debug'
 CC                | gcc
 CPPFLAGS          | -D_GNU_SOURCE -I/usr/include/libxml2 -I/rdsdbbin/postgres-9.6.1.R1/include
 CFLAGS            | -Wall -Wmissing-prototypes -Wpointer-arith -Wdeclaration-after-statement 
 -Wendif-labels -Wmissing-format-attribute -Wformat-security -fno-strict-
aliasing -fwrapv -fexcess-precision=standard -g -O2
 CFLAGS_SL         | -fpic
 LDFLAGS           | -L../../src/common -L/rdsdbbin/postgres-9.6.1.R1/lib -Wl,--as-needed -Wl,
 -rpath,'/rdsdbbin/postgres-9.6.1.R1/lib',--enable-new-dtags
 LDFLAGS_EX        |
 LDFLAGS_SL        |
 LIBS              | -lpgcommon -lpgport -lxml2 -lssl -lcrypto -lz -lreadline -lrt -lcrypt 
 -ldl -lm
 VERSION           | PostgreSQL 9.6.1
(23 rows)
```

If you attempt to access the view directly, the request fails\.

```
select * from pg_config;
ERROR:  permission denied for relation pg_config
```

## Working with the orafce extension<a name="Appendix.PostgreSQL.CommonDBATasks.orafce"></a>

The `orafce` extension provides functions that are common in commercial databases, and can make it easier for you to port a commercial database to PostgreSQL\. Amazon RDS for PostgreSQL versions 9\.6\.6 and later support this extension\. For more information about `orafce`, see the [orafce project on GitHub](https://github.com/orafce/orafce)\. 

**Note**  
Amazon RDS for PostgreSQL doesn't support the `utl_file` package that is part of the `orafce` extension\. This is because the `utl_file` schema functions provide read and write operations on operating\-system text files, which requires superuser access to the underlying host\.

**To use the orafce extension**

1. Connect to the DB instance with the master user name that you used to create the DB instance\.
**Note**  
If you want to enable `orafce` on a different database in the same instance, use the `/c dbname` psql command to change from the primary database after initiating the connection\.

1. Enable the orafce extension with the `CREATE EXTENSION` statement\.

   ```
   CREATE EXTENSION orafce;
   ```

1. Transfer ownership of the oracle schema to the rds\_superuser role with the `ALTER SCHEMA` statement\.

   ```
   ALTER SCHEMA oracle OWNER TO rds_superuser;
   ```
**Note**  
If you want to see the list of owners for the oracle schema, use the `\dn` psql command\.

## Accessing external data with the postgres\_fdw extension<a name="postgresql-commondbatasks-fdw"></a>

You can access data in a table on a remote database server with the [postgres\_fdw](https://www.postgresql.org/docs/10/static/postgres-fdw.html) extension\. If you set up a remote connection from your PostgreSQL DB instance, access is also available to your read replica\. 

**To use postgres\_fdw to access a remote database server**

1. Install the postgres\_fdw extension\.

   ```
   CREATE EXTENSION postgres_fdw;
   ```

1. Create a foreign data server using CREATE SERVER\.

   ```
   CREATE SERVER foreign_server
   FOREIGN DATA WRAPPER postgres_fdw
   OPTIONS (host 'xxx.xx.xxx.xx', port '5432', dbname 'foreign_db');
   ```

1. Create a user mapping to identify the role to be used on the remote server\.

   ```
   CREATE USER MAPPING FOR local_user
   SERVER foreign_server
   OPTIONS (user 'foreign_user', password 'password');
   ```

1. Create a table that maps to the table on the remote server\.

   ```
   CREATE FOREIGN TABLE foreign_table (
           id integer NOT NULL,
           data text)
   SERVER foreign_server
   OPTIONS (schema_name 'some_schema', table_name 'some_table');
   ```

## Restricting password management<a name="Appendix.PostgreSQL.CommonDBATasks.RestrictPasswordMgmt"></a>

You can restrict who can manage database user passwords to a special role\. By doing this, you can have more control over password management on the client side\.

You enable restricted password management with the static parameter `rds.restrict_password_commands` and use a role called `rds_password`\. When the parameter `rds.restrict_password_commands` is set to 1, only users that are members of the `rds_password` role can run certain SQL commands\. The restricted SQL commands are commands that modify database user passwords and password expiration time\. 

To use restricted password management, your DB instance must be running Amazon RDS for PostgreSQL 10\.6 or higher\. Because the `rds.restrict_password_commands` parameter is static, changing this parameter requires a database restart\.

When a database has restricted password management enabled, if you try to run restricted SQL commands you get the following error: ERROR: must be a member of rds\_password to alter passwords\.

Following are some examples of SQL commands that are restricted when restricted password management is enabled\.

```
postgres=> CREATE ROLE myrole WITH PASSWORD 'mypassword';
postgres=> CREATE ROLE myrole WITH PASSWORD 'mypassword' VALID UNTIL '2020-01-01';
postgres=> ALTER ROLE myrole WITH PASSWORD 'mypassword' VALID UNTIL '2020-01-01';
postgres=> ALTER ROLE myrole WITH PASSWORD 'mypassword';
postgres=> ALTER ROLE myrole VALID UNTIL '2020-01-01';
postgres=> ALTER ROLE myrole RENAME TO myrole2;
```

Some `ALTER ROLE` commands that include `RENAME TO` might also be restricted\. They might be restricted because renaming a PostgreSQL role that has an MD5 password clears the password\. 

The `rds_superuser` role has membership for the `rds_password` role by default, and you can't change this\. You can give other roles membership for the `rds_password` role by using the `GRANT` SQL command\. We recommend that you give membership to `rds_password` to only a few roles that you use solely for password management\. These roles require the `CREATEROLE` attribute to modify other roles\.

Make sure that you verify password requirements such as expiration and needed complexity on the client side\. We recommend that you restrict password\-related changes by using your own client\-side utility\. This utility should have a role that is a member of `rds_password` and has the `CREATEROLE` role attribute\.