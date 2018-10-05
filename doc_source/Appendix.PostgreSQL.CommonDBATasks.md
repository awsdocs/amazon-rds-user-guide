# Common DBA Tasks for PostgreSQL<a name="Appendix.PostgreSQL.CommonDBATasks"></a>

This section describes the Amazon RDS implementations of some common DBA tasks for DB instances running the PostgreSQL database engine\. To deliver a managed service experience, Amazon RDS doesn't provide shell access to DB instances, and it restricts access to certain system procedures and tables that require advanced privileges\. 

For information about working with PostgreSQL log files on Amazon RDS, see [PostgreSQL Database Log Files](USER_LogAccess.Concepts.PostgreSQL.md)\.

**Topics**
+ [Creating Roles](#Appendix.PostgreSQL.CommonDBATasks.Roles)
+ [Managing PostgreSQL Database Access](#Appendix.PostgreSQL.CommonDBATasks.Access)
+ [Working with PostgreSQL Parameters](#Appendix.PostgreSQL.CommonDBATasks.Parameters)
+ [Working with PostgreSQL Autovacuum on Amazon RDS](#Appendix.PostgreSQL.CommonDBATasks.Autovacuum)
+ [Audit Logging for a PostgreSQL DB Instance](#Appendix.PostgreSQL.CommonDBATasks.Auditing)
+ [Working with the pgaudit Extension](#Appendix.PostgreSQL.CommonDBATasks.pgaudit)
+ [Working with the pg\_repack Extension](#Appendix.PostgreSQL.CommonDBATasks.pg_repack)
+ [Working with PostGIS](#Appendix.PostgreSQL.CommonDBATasks.PostGIS)
+ [Using pgBadger for Log Analysis with PostgreSQL](#Appendix.PostgreSQL.CommonDBATasks.Badger)
+ [Viewing the Contents of pg\_config](#Appendix.PostgreSQL.CommonDBATasks.Viewingpgconfig)
+ [Working with the orafce Extension](#Appendix.PostgreSQL.CommonDBATasks.orafce)
+ [Accessing External Data with the postgres\_fdw Extension](#postgresql-commondbatasks-fdw)

## Creating Roles<a name="Appendix.PostgreSQL.CommonDBATasks.Roles"></a>

 When you create a DB instance, the master user system account that you create is assigned to the `rds_superuser` role\. The `rds_superuser` role is a predefined Amazon RDS role similar to the PostgreSQL superuser role \(customarily named postgres in local instances\), but with some restrictions\. As with the PostgreSQL superuser role, the `rds_superuser` role has the most privileges on your DB instance and you should not assign this role to users unless they need the most access to the DB instance\.

The `rds_superuser` role can do the following:
+ Add extensions that are available for use with Amazon RDS\. For more information, see [Supported PostgreSQL Features](CHAP_PostgreSQL.md#PostgreSQL.Concepts.General.FeatureSupport) and the [PostgreSQL documentation](http://www.postgresql.org/docs/9.4/static/sql-createextension.html)\.
+ Manage tablespaces, including creating and deleting them\. For more information, see this section in the [PostgreSQL documentation\.](http://www.postgresql.org/docs/9.4/static/manage-ag-tablespaces.html)
+ View all users not assigned the `rds_superuser` role using the `pg_stat_activity` command and kill their connections using the `pg_terminate_backend` and `pg_cancel_backend` commands\.
+ Grant and revoke the rds\_replication role onto all roles that are not the `rds_superuser` role\. For more information, see this section in the [PostgreSQL documentation](http://www.postgresql.org/docs/9.4/static/sql-grant.html)\.

The following example shows how to create a user and then grant the user the `rds_superuser` role\. User\-defined roles, such as `rds_superuser`, have to be granted\.

```
create role testuser with password 'testuser' login;   
CREATE ROLE   
grant rds_superuser to testuser;   
GRANT ROLE
```

## Managing PostgreSQL Database Access<a name="Appendix.PostgreSQL.CommonDBATasks.Access"></a>

By default, when PostgreSQL database objects are created, they receive "public" access privileges\. You can revoke all privileges to a database and then explicitly add privileges back as you need them\.

As the master user, you can remove all privileges from a database using the following command format\.

```
revoke all on database <database name> from public;  
REVOKE
```

You can then add privileges back to a user\. For example, the following command grants connect access to a user named *mytestuser* to a database named *test*\. 

```
grant connect on database test to mytestuser;   
GRANT
```

On a local instance, you can specify database privileges in the pg\_hba\.conf file\. However, when using PostgreSQL with Amazon RDS it is better to restrict privileges at the PostgreSQL level\. Changes to the pg\_hba\.conf file require a server restart so you cannot edit the pg\_hba\.conf in Amazon RDS, but privilege changes at the PostgreSQL level occur immediately\.

## Working with PostgreSQL Parameters<a name="Appendix.PostgreSQL.CommonDBATasks.Parameters"></a>

PostgreSQL parameters that you set for a local PostgreSQL instance in the *postgresql\.conf* file are maintained in the DB parameter group for your DB instance\. If you create a DB instance using the default parameter group, the parameter settings are in the parameter group called *default\.postgres9\.6*\.

 When you create a DB instance, the parameters in the associated DB parameter group are loaded\. You can modify parameter values by changing values in the parameter group\. You can also change parameter values, if you have the security privileges to do so, by using the ALTER DATABASE, ALTER ROLE, and SET commands\. You can't use the command line `postgres` command or the `env PGOPTIONS` command, because you have no access to the host\. 

Keeping track of PostgreSQL parameter settings can occasionally be difficult\. Use the following command to list current parameter settings and the default value\.

```
select name, setting, boot_val, reset_val, unit
from pg_settings
order by name;
```

For an explanation of the output values, see the [http://www.postgresql.org/docs/9.3/static/view-pg-settings.html](http://www.postgresql.org/docs/9.3/static/view-pg-settings.html) topic in the PostgreSQL documentation\.

 If you set the memory settings too large for `max_connections`, `shared_buffers`, or `effective_cache_size`, you will prevent the PostgreSQL instance from starting up\. Some parameters use units that you might not be familiar with; for example, `shared_buffers` sets the number of 8\-KB shared memory buffers used by the server\. 

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


|  Parameter Name  |  Apply\_Type  |  Description  | 
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
|  `checkpoint_segments`  | Dynamic | Sets the maximum distance in log segments between automatic WAL checkpoints\. | 
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
|  `log_autovacuum_min_duration`  | Dynamic | Sets the minimum execution time above which autovacuum actions will be logged\. | 
|  `log_checkpoints`  | Dynamic | Logs each checkpoint\. | 
|  `log_connections`  | Dynamic | Logs each successful connection\. | 
|  `log_disconnections`  | Dynamic | Logs end of a session, including duration\. | 
|  `log_duration`  | Dynamic | Logs the duration of each completed SQL statement\. | 
|  `log_error_verbosity`  | Dynamic | Sets the verbosity of logged messages\. | 
|  `log_executor_stats`  | Dynamic | Writes executor performance statistics to the server log\. | 
|  `log_filename`  | Dynamic | Sets the file name pattern for log files\. | 
|  `log_hostname`  | Dynamic | Logs the host name in the connection logs\. | 
|  `log_lock_waits`  | Dynamic | Logs long lock waits\. | 
|  `log_min_duration_statement`  | Dynamic | Sets the minimum execution time above which statements will be logged\. | 
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
|  `quote_all_identifiers`  | Dynamic | Adds quotes \("\) to all identifiers when generating SQL fragments\. | 
|  `random_page_cost`  | Dynamic | Sets the planner's estimate of the cost of a non\-sequentially fetched disk page\. | 
|  `rds.log_retention_period`  | Dynamic | Amazon RDS will delete PostgreSQL logs that are older than N minutes\. | 
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
|  `track_activities`  | Dynamic | Collects information about executing commands\. | 
|  `track_counts`  | Dynamic | Collects statistics on database activity\. | 
|  `track_functions`  | Dynamic | Collects function\-level statistics on database activity\. | 
|  `track_io_timing`  | Dynamic | Collects timing statistics on database I/O activity\. | 
|  `transaction_deferrable`  | Dynamic | Indicates whether to defer a read\-only serializable transaction until it can be executed with no possible serialization failures\. | 
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
|  `autovacuum_freeze_max_age`  | Static | Age at which to autovacuum a table to prevent transaction ID wraparound\. | 
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


|  Parameter Name  |  Unit  | 
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

## Working with PostgreSQL Autovacuum on Amazon RDS<a name="Appendix.PostgreSQL.CommonDBATasks.Autovacuum"></a>

We strongly recommend that you use the autovacuum feature for PostgreSQL databases to maintain the health of your PostgreSQL DB instance\. Because autovacuum checks for tables that have had a large number of inserted, updated, or deleted tuples, you can use autovacuum to prevent transaction ID wraparound\. Autovacuum automates the execution of the VACUUM and the ANALYZE command\. Using autovacuum is required by PostgreSQL, not imposed by Amazon RDS, and its use is critical to good performance\. The feature is enabled by default for all new Amazon RDS PostgreSQL DB instances, and the related configuration parameters are appropriately set by default\. Since our defaults are somewhat generic, you can benefit from tuning parameters to your specific workload\. This section can help you perform the needed autovacuum tuning\.

For information on creating a process that warns you about transaction ID wraparound, see the AWS Database Blog entry [Implement an Early Warning System for Transaction ID Wraparound in Amazon RDS for PostgreSQL](https://aws.amazon.com/blogs/database/implement-an-early-warning-system-for-transaction-id-wraparound-in-amazon-rds-for-postgresql/)\.

**Topics**
+ [Maintenance Work Memory](#Appendix.PostgreSQL.CommonDBATasks.Autovacuum.WorkMemory)
+ [Determining if the Tables in Your Database Need Vacuuming](#Appendix.PostgreSQL.CommonDBATasks.Autovacuum.NeedVacuuming)
+ [Determining Which Tables Are Currently Eligible for Autovacuum](#Appendix.PostgreSQL.CommonDBATasks.Autovacuum.EligibleTables)
+ [Determining if Autovacuum Is Currently Running and For How Long](#Appendix.PostgreSQL.CommonDBATasks.Autovacuum.AutovacuumRunning)
+ [Performing a Manual Vacuum Freeze](#Appendix.PostgreSQL.CommonDBATasks.Autovacuum.VacuumFreeze)
+ [Reindexing a Table When Autovacuum Is Running](#Appendix.PostgreSQL.CommonDBATasks.Autovacuum.Reindexing)
+ [Other Parameters That Affect Autovacuum](#Appendix.PostgreSQL.CommonDBATasks.Autovacuum.OtherParms)
+ [Autovacuum Logging](#Appendix.PostgreSQL.CommonDBATasks.Autovacuum.Logging)

### Maintenance Work Memory<a name="Appendix.PostgreSQL.CommonDBATasks.Autovacuum.WorkMemory"></a>

One of the most important parameters influencing autovacuum performance is the [https://www.postgresql.org/docs/current/static/runtime-config-resource.html#GUC-MAINTENANCE-WORK-MEM](https://www.postgresql.org/docs/current/static/runtime-config-resource.html#GUC-MAINTENANCE-WORK-MEM) parameter\. This parameter determines how much memory you allocate for autovacuum to use to scan a database table and to hold all the row IDs that are going to be vacuumed\. If you set the value of the [https://www.postgresql.org/docs/current/static/runtime-config-resource.html#GUC-MAINTENANCE-WORK-MEM](https://www.postgresql.org/docs/current/static/runtime-config-resource.html#GUC-MAINTENANCE-WORK-MEM) parameter too low, the vacuum process might have to scan the table multiple times to complete its work, possibly impacting performance\.

When doing calculations to determine the [https://www.postgresql.org/docs/current/static/runtime-config-resource.html#GUC-MAINTENANCE-WORK-MEM](https://www.postgresql.org/docs/current/static/runtime-config-resource.html#GUC-MAINTENANCE-WORK-MEM) parameter value, keep in mind two things:
+ The default unit is KB for this parameter\.
+ The [https://www.postgresql.org/docs/current/static/runtime-config-resource.html#GUC-MAINTENANCE-WORK-MEM](https://www.postgresql.org/docs/current/static/runtime-config-resource.html#GUC-MAINTENANCE-WORK-MEM) parameter works in conjunction with the [https://www.postgresql.org/docs/current/static/runtime-config-autovacuum.html#GUC-AUTOVACUUM-MAX-WORKERS](https://www.postgresql.org/docs/current/static/runtime-config-autovacuum.html#GUC-AUTOVACUUM-MAX-WORKERS) parameter\. If you have many small tables, allocate more [https://www.postgresql.org/docs/current/static/runtime-config-autovacuum.html#GUC-AUTOVACUUM-MAX-WORKERS](https://www.postgresql.org/docs/current/static/runtime-config-autovacuum.html#GUC-AUTOVACUUM-MAX-WORKERS) and less [https://www.postgresql.org/docs/current/static/runtime-config-resource.html#GUC-MAINTENANCE-WORK-MEM](https://www.postgresql.org/docs/current/static/runtime-config-resource.html#GUC-MAINTENANCE-WORK-MEM)\. If you have large tables \(say, larger than 100 GB\), allocate more memory and fewer workers\. You need to have enough memory allocated to succeed on your biggest table\. Each [https://www.postgresql.org/docs/current/static/runtime-config-autovacuum.html#GUC-AUTOVACUUM-MAX-WORKERS](https://www.postgresql.org/docs/current/static/runtime-config-autovacuum.html#GUC-AUTOVACUUM-MAX-WORKERS) can use the memory you allocate, so you should make sure the combination of workers and memory equal the total memory you want to allocate\.

 In general terms, for large hosts, set the [https://www.postgresql.org/docs/current/static/runtime-config-resource.html#GUC-MAINTENANCE-WORK-MEM](https://www.postgresql.org/docs/current/static/runtime-config-resource.html#GUC-MAINTENANCE-WORK-MEM) parameter to a value between one and two gigabytes\. For extremely large hosts, set the parameter to a value between two and four gigabytes\. The value you set for this parameter should depend on the workload\. Amazon RDS has updated its default for this parameter to be `GREATEST({DBInstanceClassMemory/63963136*1024},65536)`\. 

### Determining if the Tables in Your Database Need Vacuuming<a name="Appendix.PostgreSQL.CommonDBATasks.Autovacuum.NeedVacuuming"></a>

A PostgreSQL database can have two billion "in\-flight" unvacuumed transactions before PostgreSQL takes dramatic action to avoid data loss\. If the number of unvacuumed transactions reaches \(2^31 \- 10,000,000\), the log will start warning that vacuuming is needed\. If the number of unvacuumed transactions reaches \(2^31 \- 1,000,000\), PostgreSQL sets the database to read only and requires an offline, single\-user, standalone vacuum\. This requires multiple hours or days \(depending on size\) of downtime\. A very detailed explanation of [TransactionID wraparound](https://www.postgresql.org/docs/current/static/routine-vacuuming.html#VACUUM-FOR-WRAPAROUND) is found in the PostgreSQL documentation\.

The following query can be used to show the number of unvacuumed transactions in a database\. The datfrozenxid column of a database's pg\_database row is a lower bound on the normal XIDs appearing in that database; it is the minimum of the per\-table relfrozenxid values within the database\. 

```
select datname, age(datfrozenxid) from pg_database order 
by age(datfrozenxid) desc limit 20;
```

For example, the results of running the preceding query might be the following:

```
datname    | age
mydb       | 1771757888
template0  | 1721757888
template1  | 1721757888
rdsadmin   | 1694008527
postgres   | 1693881061
(5 rows)
```

When the age of a database hits two billion, TransactionID \(XID\) wraparound occurs and the database will go into read only\. This query can be used to produce a metric and run a few times a day\. By default, autovacuum is set to keep the age of transactions to no more than 200,000,000 \([https://www.postgresql.org/docs/current/static/runtime-config-autovacuum.html#GUC-AUTOVACUUM-FREEZE-MAX-AGE](https://www.postgresql.org/docs/current/static/runtime-config-autovacuum.html#GUC-AUTOVACUUM-FREEZE-MAX-AGE)\)\.

A sample monitoring strategy might look like this:
+ Autovacuum\_freeze\_max\_age is set to 200 million\.
+ If a table hits 500 million unvacuumed transactions, a low\-severity alarm is triggered\. This isn’t an unreasonable value, but it could indicate that autovacuum isn’t keeping up\.
+ If a table ages to one billion, this should be treated as an actionable alarm\. In general, you want to keep ages closer to autovacuum\_freeze\_max\_age for performance reasons\. Investigation using the following steps is recommended\.
+ If a table hits 1\.5 billion unvacuumed transactions, a high\-severity alarm is triggered\. Depending on how quickly your database uses XIDs, this alarm can indicate that the system is running out of time to run autovacuum and that you should consider immediate resolution\.

If a table is constantly breaching these thresholds, you need further modify your autovacuum parameters\. By default, VACUUM \(which has cost\-based delays disabled\) is more aggressive than default autovacuum, but, also more intrusive to the system as a whole\.

We have the following recommendations:
+ Be aware and enable a monitoring mechanism so that you are aware of the age of your oldest transactions\.
+ For busier tables, perform a manual vacuum freeze regularly during a maintenance window in addition to relying on autovacuum\. For information on performing a manual vacuum freeze, see [ Performing a Manual Vacuum Freeze](#Appendix.PostgreSQL.CommonDBATasks.Autovacuum.VacuumFreeze)\.

### Determining Which Tables Are Currently Eligible for Autovacuum<a name="Appendix.PostgreSQL.CommonDBATasks.Autovacuum.EligibleTables"></a>

Often, it is one or two tables in need of vacuuming\. Tables whose relfrozenxid value is more than autovacuum\_freeze\_max\_age transactions old are always targeted by autovacuum\. Otherwise, if the number of tuples made obsolete since the last VACUUM exceeds the "vacuum threshold", the table is vacuumed\.

The [autovacuum threshold](https://www.postgresql.org/docs/current/static/routine-vacuuming.html#AUTOVACUUM) is defined as:

```
Vacuum threshold = vacuum base threshold + vacuum scale factor * number of tuples
```

While you are connected to your database, run the following query to see a list of tables that autovacuum sees as eligible for vacuuming:

```
  WITH vbt AS (SELECT setting AS autovacuum_vacuum_threshold FROM 
pg_settings WHERE name = 'autovacuum_vacuum_threshold')
    , vsf AS (SELECT setting AS autovacuum_vacuum_scale_factor FROM 
pg_settings WHERE name = 'autovacuum_vacuum_scale_factor')
    , fma AS (SELECT setting AS autovacuum_freeze_max_age FROM 
pg_settings WHERE name = 'autovacuum_freeze_max_age')
    , sto AS (select opt_oid, split_part(setting, '=', 1) as param, 
split_part(setting, '=', 2) as value from (select oid opt_oid, 
unnest(reloptions) setting from pg_class) opt)
SELECT
    '"'||ns.nspname||'"."'||c.relname||'"' as relation
    , pg_size_pretty(pg_table_size(c.oid)) as table_size
    , age(relfrozenxid) as xid_age
    , coalesce(cfma.value::float, autovacuum_freeze_max_age::float) 
autovacuum_freeze_max_age
    , (coalesce(cvbt.value::float, autovacuum_vacuum_threshold::float) 
+ coalesce(cvsf.value::float,autovacuum_vacuum_scale_factor::float) * 
c.reltuples) as autovacuum_vacuum_tuples
    , n_dead_tup as dead_tuples
FROM pg_class c join pg_namespace ns on ns.oid = c.relnamespace
join pg_stat_all_tables stat on stat.relid = c.oid
join vbt on (1=1) join vsf on (1=1) join fma on (1=1)
left join sto cvbt on cvbt.param = 'autovacuum_vacuum_threshold' and 
c.oid = cvbt.opt_oid
left join sto cvsf on cvsf.param = 'autovacuum_vacuum_scale_factor' and 
c.oid = cvsf.opt_oid
left join sto cfma on cfma.param = 'autovacuum_freeze_max_age' and 
c.oid = cfma.opt_oid
WHERE c.relkind = 'r' and nspname <> 'pg_catalog'
and (
    age(relfrozenxid) >= coalesce(cfma.value::float, 
autovacuum_freeze_max_age::float)
    or
    coalesce(cvbt.value::float, autovacuum_vacuum_threshold::float) + 
coalesce(cvsf.value::float,autovacuum_vacuum_scale_factor::float) * 
c.reltuples <= n_dead_tup
   -- or 1 = 1
)
ORDER BY age(relfrozenxid) DESC LIMIT 50;
```

### Determining if Autovacuum Is Currently Running and For How Long<a name="Appendix.PostgreSQL.CommonDBATasks.Autovacuum.AutovacuumRunning"></a>

If you need to manually vacuum a table, you need to determine if autovacuum is currently running\. If it is, you might need to adjust parameters to make it run more efficiently, or terminate autovacuum so you can manually run VACUUM\.

Use the following query to determine if autovacuum is running, how long it has been running, and if it is waiting on another session\. 

If you are using Amazon RDS PostgreSQL 9\.6\+ or higher, use this query:

```
SELECT datname, usename, pid, state, wait_event, current_timestamp - xact_start AS xact_runtime, query
FROM pg_stat_activity 
WHERE upper(query) like '%VACUUM%' 
ORDER BY xact_start;
```

After running the query, you should see output similar to the following\.

```
 datname | usename  |  pid  | state  | wait_event |      xact_runtime       | query  
 --------+----------+-------+--------+------------+-------------------------+--------------------------------------------------------------------------------------------------------
 mydb    | rdsadmin | 16473 | active |            | 33 days 16:32:11.600656 | autovacuum: VACUUM ANALYZE public.mytable1 (to prevent wraparound)
 mydb    | rdsadmin | 22553 | active |            | 14 days 09:15:34.073141 | autovacuum: VACUUM ANALYZE public.mytable2 (to prevent wraparound)
 mydb    | rdsadmin | 41909 | active |            | 3 days 02:43:54.203349  | autovacuum: VACUUM ANALYZE public.mytable3
 mydb    | rdsadmin |   618 | active |            | 00:00:00                | SELECT datname, usename, pid, state, wait_event, current_timestamp - xact_start AS xact_runtime, query+
         |          |       |        |            |                         | FROM pg_stat_activity                                                                                 +
         |          |       |        |            |                         | WHERE query like '%VACUUM%'                                                                           +
         |          |       |        |            |                         | ORDER BY xact_start;                                                                                  +
```

If you are using a version less than Amazon RDS PostgreSQL 9\.6, but, 9\.3\.12 or later, 9\.4\.7 or later, or 9\.5\.2\+, use this query:

```
SELECT datname, usename, pid, waiting, current_timestamp - xact_start AS xact_runtime, query
FROM pg_stat_activity 
WHERE upper(query) like '%VACUUM%' 
ORDER BY xact_start;
```

After running the query, you should see output similar to the following\.

```
 datname | usename  |  pid  | waiting |       xact_runtime      | query  
 --------+----------+-------+---------+-------------------------+----------------------------------------------------------------------------------------------
 mydb    | rdsadmin | 16473 | f       | 33 days 16:32:11.600656 | autovacuum: VACUUM ANALYZE public.mytable1 (to prevent wraparound)
 mydb    | rdsadmin | 22553 | f       | 14 days 09:15:34.073141 | autovacuum: VACUUM ANALYZE public.mytable2 (to prevent wraparound)
 mydb    | rdsadmin | 41909 | f       | 3 days 02:43:54.203349  | autovacuum: VACUUM ANALYZE public.mytable3
 mydb    | rdsadmin |   618 | f       | 00:00:00                | SELECT datname, usename, pid, waiting, current_timestamp - xact_start AS xact_runtime, query+
         |          |       |         |                         | FROM pg_stat_activity                                                                       +                 
         |          |       |         |                         | WHERE query like '%VACUUM%'                                                                 +
         |          |       |         |                         | ORDER BY xact_start;                                                                        +
```

Several issues can cause long running \(multiple days\) autovacuum session\. The most common issue is that your [https://www.postgresql.org/docs/current/static/runtime-config-resource.html#GUC-MAINTENANCE-WORK-MEM](https://www.postgresql.org/docs/current/static/runtime-config-resource.html#GUC-MAINTENANCE-WORK-MEM) parameter value is set too low for the size of the table or rate of updates\. 

We recommend that you use the following formula to set the [https://www.postgresql.org/docs/current/static/runtime-config-resource.html#GUC-MAINTENANCE-WORK-MEM](https://www.postgresql.org/docs/current/static/runtime-config-resource.html#GUC-MAINTENANCE-WORK-MEM) parameter value\.

```
GREATEST({DBInstanceClassMemory/63963136*1024},65536)
```

Short running autovacuum sessions can also indicate problems:
+ It can indicate that there aren't enough autovacuum\_max\_workers for your workload\. You will need to indicate the number of workers\.
+ It can indicate that there is an index corruption \(autovacuum will crash and restart on the same relation but make no progress\)\. You will need to run a manual vacuum freeze verbose \_\_\_table\_\_\_ to see the exact cause\.

### Performing a Manual Vacuum Freeze<a name="Appendix.PostgreSQL.CommonDBATasks.Autovacuum.VacuumFreeze"></a>

You might want to perform a manual vacuum on a table that has a vacuum process already running\. This is useful if you have identified a table with an "XID age" approaching 2 billion \(or above any threshold you are monitoring\)\.

The following steps are a guideline, and there are several variations to the process\. For example, during testing, you find that the [https://www.postgresql.org/docs/current/static/runtime-config-resource.html#GUC-MAINTENANCE-WORK-MEM](https://www.postgresql.org/docs/current/static/runtime-config-resource.html#GUC-MAINTENANCE-WORK-MEM) parameter value was set too small and that you need to take immediate action on a table but don't want to bounce the instance at the moment\. Using the queries listed above, you determine which table is the problem and notice a long running autovacuum session\. You know you need to change the [https://www.postgresql.org/docs/current/static/runtime-config-resource.html#GUC-MAINTENANCE-WORK-MEM](https://www.postgresql.org/docs/current/static/runtime-config-resource.html#GUC-MAINTENANCE-WORK-MEM) parameter setting, but you also need to take immediate action and vacuum the table in question\. The following procedure shows what you would do in this situation:

**To manually perform a vacuum freeze**

1. Open two sessions to the database containing the table you want to vacuum\. For the second session, use "screen" or another utility that maintains the session if your connection is dropped\.

1. In session one, get the PID of the autovacuum session running on the table\. This action requires that you are running Amazon RDS PostgreSQL 9\.3\.12 or later, 9\.4\.7 or later, or 9\.5\.2 or later to have full visibility into the running rdsadmin processes\.

   Run the following query to get the PID of the autovacuum session\.

   ```
   SELECT datname, usename, pid, waiting, current_timestamp - xact_start 
   AS xact_runtime, query
   FROM pg_stat_activity WHERE upper(query) like '%VACUUM%' ORDER BY 
   xact_start;
   ```

1. In session two, calculate the amount of memory you will need for this operation\. In this example, we determine that we can afford to use up to 2 GB of memory for this operation, so we set [https://www.postgresql.org/docs/current/static/runtime-config-resource.html#GUC-MAINTENANCE-WORK-MEM](https://www.postgresql.org/docs/current/static/runtime-config-resource.html#GUC-MAINTENANCE-WORK-MEM) for the current session to 2 GB\.

   ```
   set maintenance_work_mem='2 GB';
   SET
   ```

1. In session two, issue a vacuum freeze verbose for the table\. The verbose setting is useful because, although there is no progress report for this in PostgreSQL currently, you can see activity\.

   ```
   \timing on
   Timing is on.
   vacuum freeze verbose pgbench_branches;
   INFO:  vacuuming "public.pgbench_branches"
   INFO:  index "pgbench_branches_pkey" now contains 50 row versions in 2 pages
   DETAIL:  0 index row versions were removed.
   0 index pages have been deleted, 0 are currently reusable.
   CPU 0.00s/0.00u sec elapsed 0.00 sec.
   INFO:  index "pgbench_branches_test_index" now contains 50 row versions in 2 pages
   DETAIL:  0 index row versions were removed.
   0 index pages have been deleted, 0 are currently reusable.
   CPU 0.00s/0.00u sec elapsed 0.00 sec.
   INFO:  "pgbench_branches": found 0 removable, 50 nonremovable row versions 
        in 43 out of 43 pages
   DETAIL:  0 dead row versions cannot be removed yet.
   There were 9347 unused item pointers.
   0 pages are entirely empty.
   CPU 0.00s/0.00u sec elapsed 0.00 sec.
   VACUUM
   Time: 2.765 ms
   ```

1. In session one, if autovacuum was blocking, you will see in pg\_stat\_activity that waiting is "T" for your vacuum session\. In this case, you need to terminate the autovacuum process\.

   ```
   select pg_terminate_backend('the_pid'); 
   ```

1. At this point, your session begins\. It's important to note that autovacuum will restart immediately as this table is probably the highest on its list of work\. You will need to initiate your command in session 2 and then terminate the autovacuum process in session one\.

### Reindexing a Table When Autovacuum Is Running<a name="Appendix.PostgreSQL.CommonDBATasks.Autovacuum.Reindexing"></a>

If an index has become corrupt, autovacuum will continue to process the table and fail\. If you attempt a manual vacuum in this situation, you will receive an error message similar to the following:

```
mydb=# vacuum freeze pgbench_branches;
ERROR: index "pgbench_branches_test_index" contains unexpected 
   zero page at block 30521
HINT: Please REINDEX it.
```

When the index is corrupted and autovacuum is attempting to run against the table, you will contend with an already running autovacuum session\. When you issue a "[REINDEX](https://www.postgresql.org/docs/current/static/sql-reindex.html) " command, you will be taking out an exclusive lock on the table and write operations will be blocked as well as reads that use that specific index\.

**To reindex a table when autovacuum is running on the table**

1. Open two sessions to the database containing the table you want to vacuum\. For the second session, use "screen" or another utility that maintains the session if your connection is dropped\.

1. In session one, get the PID of the autovacuum session running on the table\. This action requires that you are running Amazon RDS PostgreSQL 9\.3\.12 or later, 9\.4\.7 or later, or 9\.5\.2 or later to have full visibility into the running rdsadmin processes\.

   Run the following query to get the PID of the autovacuum session:

   ```
   SELECT datname, usename, pid, waiting, current_timestamp - xact_start 
   AS xact_runtime, query
   FROM pg_stat_activity WHERE upper(query) like '%VACUUM%' ORDER BY 
   xact_start;
   ```

1. In session two, issue the reindex command\.

   ```
   \timing on
   Timing is on.
   reindex index pgbench_branches_test_index;
   REINDEX
   Time: 9.966 ms
   ```

1. In session one, if autovacuum was blocking, you will see in *pg\_stat\_activity* that waiting is "T" for your vacuum session\. In this case, you will need to terminate the autovacuum process\. 

   ```
   select pg_terminate_backend('the_pid');
   ```

1. At this point, your session begins\. It's important to note that autovacuum will restart immediately as this table is probably the highest on its list of work\. You will need to initiate your command in session 2 and then terminate the autovacuum process in session one\.

### Other Parameters That Affect Autovacuum<a name="Appendix.PostgreSQL.CommonDBATasks.Autovacuum.OtherParms"></a>

This query will show the values of some of the parameters that directly impact autovacuum and its behavior\. The [autovacuum parameters](https://www.postgresql.org/docs/current/static/runtime-config-autovacuum.html) are described fully in the PostgreSQL documentation\.

```
select name, setting, unit, short_desc
from pg_settings
where name in (
'autovacuum_max_workers',
'autovacuum_analyze_scale_factor',
'autovacuum_naptime',
'autovacuum_analyze_threshold',
'autovacuum_analyze_scale_factor',
'autovacuum_vacuum_threshold',
'autovacuum_vacuum_scale_factor',
'autovacuum_vacuum_threshold',
'autovacuum_vacuum_cost_delay',
'autovacuum_vacuum_cost_limit',
'vacuum_cost_limit',
'autovacuum_freeze_max_age',
'maintenance_work_mem',
'vacuum_freeze_min_age');
```

While these all affect autovacuum, some of the most important ones are:
+ [Maintenance\_Work\_mem](https://www.postgresql.org/docs/current/static/runtime-config-resource.html#GUC-MAINTENANCE_WORK_MEM)
+ [Autovacuum\_freeze\_max\_age](https://www.postgresql.org/docs/current/static/runtime-config-autovacuum.html#GUC-AUTOVACUUM-FREEZE-MAX-AGE)
+ [Autovacuum\_max\_workers](https://www.postgresql.org/docs/current/static/runtime-config-autovacuum.html#GUC-AUTOVACUUM-MAX-WORKERS)
+ [Autovacuum\_vacuum\_cost\_delay](https://www.postgresql.org/docs/current/static/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-COST-DELAY)
+ [ Autovacuum\_vacuum\_cost\_limit](https://www.postgresql.org/docs/current/static/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-COST-LIMIT)

#### Table\-Level Parameters<a name="w4aac32c43c15c21c10"></a>

Autovacuum related [storage parameters](https://www.postgresql.org/docs/current/static/sql-createtable.html#SQL-CREATETABLE-STORAGE-PARAMETERS) can be set at a table level, which can be better than altering the behavior of the entire database\. For large tables, you might need to set aggressive settings and you might not want to make autovacuum behave that way for all tables\.

This query will show which tables currently have table level options in place:

```
select relname, reloptions
from pg_class
where reloptions is not null;
```

An example where this might be useful is on tables that are much larger than the rest of your tables\. If you have one 300\-GB table and 30 other tables less than 1 GB, you might set some specific parameters for your large table so you don't alter the behavior of your entire system\.

```
alter table mytable set (autovacuum_vacuum_cost_delay=0);
```

Doing this disables the cost\-based autovacuum delay for this table at the expense of more resource usage on your system\. Normally, autovacuum pauses for autovacuum\_vacuum\_cost\_delay each time autovacuum\_cost\_limit is reached\. You can find more details in the PostgreSQL documentation about [cost\-based vacuuming](https://www.postgresql.org/docs/current/static/runtime-config-resource.html#RUNTIME-CONFIG-RESOURCE-VACUUM-COST)\.

### Autovacuum Logging<a name="Appendix.PostgreSQL.CommonDBATasks.Autovacuum.Logging"></a>

By default, the *postgresql\.log* doesn't contain information about the autovacuum process\. If you are using PostgreSQL 9\.4\.5 or later, you can see output in the PostgreSQL error log from the autovacuum worker operations by setting the `rds.force_autovacuum_logging_level` parameter\. Allowed values are `disabled, debug5, debug4, debug3, debug2, debug1, info, notice, warning, error, log, fatal,` and `panic`\. The default value is `disabled` because the other allowable values can add significant amount of information to your logs\.

We recommend that you set the value of the `rds.force_autovacuum_logging_level` parameter to `log` and that you set the `log_autovacuum_min_duration` parameter to a value from 1000 or 5000\. If you set this value to 5000, Amazon RDS writes activity to the log that takes more than five seconds and shows "vacuum skipped" messages when application locking is causing autovacuum to intentionally skip tables\. If you are troubleshooting a problem and need more detail, you can use a different logging level value, such as `debug1` or `debug3`\. Use these debug parameters for a short period of time because these settings produce extremely verbose content written to the error log file\. For more information about these debug settings, see the [ PostgreSQL documentation](https://www.postgresql.org/docs/current/static/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHEN)\.

NOTE: PostgreSQL version 9\.4\.7 and later includes improved visibility of autovacuum sessions by allowing the `rds_superuser` account to view autovacuum sessions in `pg_stat_activity`\. For example, you can identify and terminate an autovacuum session that is blocking a command from running, or executing slower than a manually issued vacuum command\.

## Audit Logging for a PostgreSQL DB Instance<a name="Appendix.PostgreSQL.CommonDBATasks.Auditing"></a>

There are several parameters you can set to log activity that occurs on your PostgreSQL DB instance\. These parameters include the following:
+  The `log_statement` parameter can be used to log user activity in your PostgreSQL database\. For more information, see [PostgreSQL Database Log Files](USER_LogAccess.Concepts.PostgreSQL.md)\.
+ The `rds.force_admin_logging_level` parameter logs actions by the RDS internal user \(rdsadmin\) in the databases on the DB instance, and writes the output to the PostgreSQL error log\. Allowed values are disabled, debug5, debug4, debug3, debug2, debug1, info, notice, warning, error, log, fatal, and panic\. The default value is disabled\.
+ The `rds.force_autovacuum_logging_level` parameter logs autovacuum worker operations in all databases on the DB instance, and writes the output to the PostgreSQL error log\. Allowed values are disabled, debug5, debug4, debug3, debug2, debug1, info, notice, warning, error, log, fatal, and panic\. The default value is disabled\. The Amazon RDS recommended setting for rds\.force\_autovacuum\_logging\_level: is LOG\. Set log\_autovacuum\_min\_duration to a value from 1000 or 5000\. Setting this value to 5000 will write activity to the log that takes more than 5 seconds and will show "vacuum skipped" messages\. For more information on this parameter, see [Best Practices for Working with PostgreSQL](CHAP_BestPractices.md#CHAP_BestPractices.PostgreSQL)\. 

## Working with the pgaudit Extension<a name="Appendix.PostgreSQL.CommonDBATasks.pgaudit"></a>

The `pgaudit` extension provides detailed session and object audit logging for Amazon RDS for PostgreSQL version 9\.6\.3 and later and version 9\.5\.7 version and later\. You can enable session auditing or object auditing using this extension\. 

With session auditing, you can log audit events from various sources and includes the fully qualified command text when available\. For example, you can use session auditing to log all READ statements that connect to a database by setting pgaudit\.log to 'READ'\.

With object auditing, you can refine the audit logging to work with specific commands\. For example, you can specify that you want audit logging for READ operations on a specific number of tables\.

**To use object based logging with the `pgaudit` extension**

1. Create a specific database role called `rds_pgaudit`\. Use the following command to create the role\.

   ```
   CREATE ROLE rds_pgaudit;
   CREATE ROLE
   ```

1. Modify the parameter group that is associated with your DB instance to use the shared preload libraries that contain `pgaudit` and set the parameter `pgaudit.role`\. The `pgaudit.role` must be set to the role `rds_pgaudit`\. 

   The following command modifies a custom parameter group\.

   ```
   aws rds modify-db-parameter-group 
      --db-parameter-group-name rds-parameter-group-96 
      --parameters "ParameterName=pgaudit.role,ParameterValue=rds_pgaudit,ApplyMethod=pending-reboot" 
      --parameters "ParameterName=shared_preload_libraries,ParameterValue=pgaudit,ApplyMethod=pending-reboot"
      --region us-west-2
   ```

1. Reboot the instance so that the DB instance will pick up the changes to the parameter group\. The following command reboots a DB instance\.

   ```
   aws rds reboot-db-instance --db-instance-identifier rds-test-instance --region us-west-2
   ```

1. Run the following command to confirm that `pgaudit` has been initialized\.

   ```
   show shared_preload_libraries;
   shared_preload_libraries 
   --------------------------
   rdsutils,pgaudit
   (1 row)
   ```

1. Run the following command to create the `pgaudit` extension\.

   ```
   CREATE EXTENSION pgaudit;
   CREATE EXTENSION
   ```

1. Run the following command to confirm `pgaudit.role` is set to *rds\_pgaudit*\.

   ```
   show pgaudit.role;
   pgaudit.role 
   ------------------
   rds_pgaudit
   ```

To test the audit logging, run several commands that you have chosen to audit\. For example, you might run the following commands\.

```
CREATE TABLE t1 (id int);
CREATE TABLE
GRANT SELECT ON t1 TO rds_pgaudit;
GRANT
select * from t1;
id 
----
(0 rows)
```

The database logs will contain an entry similar to the following:

```
...
2017-06-12 19:09:49 UTC:…:rds_test@postgres:[11701]:LOG: AUDIT:
OBJECT,1,1,READ,SELECT,TABLE,public.t1,select * from t1;
...
```

For information on viewing the logs, see [Amazon RDS Database Log Files](USER_LogAccess.md)\.

## Working with the pg\_repack Extension<a name="Appendix.PostgreSQL.CommonDBATasks.pg_repack"></a>

You can use the `pg_repack` extension to remove bloat from tables and indexes\. This extension is supported on Amazon RDS for PostgreSQL versions 9\.6\.3 and later\. For more information on the `pg_repack` extension, see the [Github project documentation](https://reorg.github.io/pg_repack/)\.

**To use the `pg_repack` extension**

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

## Working with PostGIS<a name="Appendix.PostgreSQL.CommonDBATasks.PostGIS"></a>

PostGIS is an extension to PostgreSQL for storing and managing spatial information\. If you are not familiar with PostGIS, you can get a good general overview at [PostGIS Introduction](http://workshops.boundlessgeo.com/postgis-intro/introduction.html)\.

You need to perform a bit of setup before you can use the PostGIS extension\. The following list shows what you need to do; each step is described in greater detail later in this section\.
+ Connect to the DB instance using the master user name used to create the DB instance\.
+ Load the PostGIS extensions\.
+ Transfer ownership of the extensions to the`rds_superuser` role\.
+ Transfer ownership of the objects to the `rds_superuser` role\.
+ Test the extensions\.

### Step 1: Connect to the DB Instance Using the Master Username Used to Create the DB Instance<a name="Appendix.PostgreSQL.CommonDBATasks.PostGIS.Connect"></a>

First, you connect to the DB instance using the master user name that was used to create the DB instance\. That name is automatically assigned the `rds_superuser` role\. You need the `rds_superuser` role that is needed to do the remaining steps\.

The following example uses SELECT to show you the current user; in this case, the current user should be the master username you chose when creating the DB instance\.

```
select current_user;
 current_user
 -------------
  myawsuser
 (1 row)
```

### Step 2: Load the PostGIS Extensions<a name="Appendix.PostgreSQL.CommonDBATasks.PostGIS.LoadExtensions"></a>

Use the CREATE EXTENSION statements to load the PostGIS extensions\. You must also load the `fuzzystrmatch` extension\. You can then use the `\dn` *psql* command to list the owners of the PostGIS schemas\.

```
create extension postgis;
CREATE EXTENSION
create extension fuzzystrmatch;
CREATE EXTENSION
create extension postgis_tiger_geocoder;
CREATE EXTENSION
create extension postgis_topology;
CREATE EXTENSION
\dn
     List of schemas
     Name     |   Owner
--------------+-----------
 public       | myawsuser
 tiger        | rdsadmin
 tiger_data   | rdsadmin
 topology     | rdsadmin
(4 rows)
```

### Step 3: Transfer Ownership of the Extensions to the rds\_superuser Role<a name="Appendix.PostgreSQL.CommonDBATasks.PostGIS.TransferOwnership"></a>

Use the ALTER SCHEMA statements to transfer ownership of the schemas to the `rds_superuser` role\.

```
alter schema tiger owner to rds_superuser;
ALTER SCHEMA
alter schema tiger_data owner to rds_superuser;
ALTER SCHEMA
alter schema topology owner to rds_superuser;
ALTER SCHEMA
\dn
       List of schemas
     Name     |     Owner
--------------+---------------
 public       | myawsuser
 tiger        | rds_superuser
tiger_data    | rds_superuser
 topology     | rds_superuser
(4 rows)
```

### Step 4: Transfer Ownership of the Objects to the rds\_superuser Role<a name="Appendix.PostgreSQL.CommonDBATasks.PostGIS.TransferObjects"></a>

Use the following function to transfer ownership of the PostGIS objects to the `rds_superuser` role\. Run the following statement from the psql prompt to create the function\.

```
CREATE FUNCTION exec(text) returns text language plpgsql volatile AS $f$ BEGIN EXECUTE $1; RETURN $1; END; $f$;      
```

Next, run this query to run the exec function that in turn executes the statements and alters the permissions\.

```
SELECT exec('ALTER TABLE ' || quote_ident(s.nspname) || '.' || quote_ident(s.relname) || ' OWNER TO rds_superuser;')
  FROM (
    SELECT nspname, relname
    FROM pg_class c JOIN pg_namespace n ON (c.relnamespace = n.oid) 
    WHERE nspname in ('tiger','topology') AND
    relkind IN ('r','S','v') ORDER BY relkind = 'S')
s;
```

### Step 5: Test the Extensions<a name="Appendix.PostgreSQL.CommonDBATasks.PostGIS.Test"></a>

Add `tiger` to your search path using the following command\.

```
SET search_path=public,tiger;         
```

Test `tiger` by using the following SELECT statement\.

```
select na.address, na.streetname, na.streettypeabbrev, na.zip
from normalize_address('1 Devonshire Place, Boston, MA 02109') as na;
 address | streetname | streettypeabbrev |  zip
---------+------------+------------------+-------
       1 | Devonshire | Pl               | 02109
(1 row)
```

Test `topology` by using the following SELECT statement\.

```
select topology.createtopology('my_new_topo',26986,0.5);
 createtopology
----------------
              1
(1 row)
```

## Using pgBadger for Log Analysis with PostgreSQL<a name="Appendix.PostgreSQL.CommonDBATasks.Badger"></a>

You can use a log analyzer such as [pgbadger](http://dalibo.github.io/pgbadger/) to analyze PostgreSQL logs\. The *pgbadger* documentation states that the %l pattern \(log line for session/process\) should be a part of the prefix\. However, if you provide the current rds log\_line\_prefix as a parameter to *pgbadger* it should still produce a report\. 

For example, the following command correctly formats an Amazon RDS PostgreSQL log file dated 2014\-02\-04 using *pgbadger*\.

```
./pgbadger -f stderr -p '%t:%r:%u@%d:[%p]:' postgresql.log.2014-02-04-00 
```

## Viewing the Contents of pg\_config<a name="Appendix.PostgreSQL.CommonDBATasks.Viewingpgconfig"></a>

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

## Working with the orafce Extension<a name="Appendix.PostgreSQL.CommonDBATasks.orafce"></a>

The `orafce` extension provides functions that are common in commercial databases, and can make it easier for you to port a commercial database to PostgreSQL\. Amazon RDS for PostgreSQL versions 9\.6\.6 and later support this extension\. For more information about `orafce`, see the [orafce project on GitHub](https://github.com/orafce/orafce)\. 

**Note**  
Amazon RDS for PostgreSQL doesn't support the `utl_file` package that is part of the `orafce` extension\. This is because the `utl_file` schema functions provide read and write operations on operating\-system text files, which requires superuser access to the underlying host\.

**To use the orafce extension**

1. Connect to the DB instance with the master user name that you used to create the DB instance\.
**Note**  
If you want to enable `orafce` on a different database in the same instance, use the `/c dbname` psql command to change from the master database after initiating the connection\.

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

## Accessing External Data with the postgres\_fdw Extension<a name="postgresql-commondbatasks-fdw"></a>

You can access data in a table on a remote database server with the [postgres\_fdw](https://www.postgresql.org/docs/10/static/postgres-fdw.html) extension\. If you set up a remote connection from your PostgreSQL DB instance, access is also available to your Read Replica\. 

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