# Amazon Aurora PostgreSQL Reference<a name="AuroraPostgreSQL.Reference"></a>

## Amazon Aurora PostgreSQL Parameters<a name="AuroraPostgreSQL.Reference.ParameterGroups"></a>

You manage your Amazon Aurora DB cluster in the same way that you manage other Amazon RDS DB instances, by using parameters in a DB parameter group\. Amazon Aurora differs from other DB engines in that you have a DB cluster that contains multiple DB instances\. As a result, some of the parameters that you use to manage your Amazon Aurora DB cluster apply to the entire cluster, while other parameters apply only to a particular DB instance in the DB cluster\.

Cluster\-level parameters are managed in DB cluster parameter groups\. Instance\-level parameters are managed in DB parameter groups\. Although each DB instance in an Aurora PostgreSQL DB cluster is compatible with the PostgreSQL database engine, some of the PostgreSQL database engine parameters must be applied at the cluster level, and are managed using DB cluster parameter groups\. Cluster\-level parameters are not found in the DB parameter group for a DB instance in an Aurora PostgreSQL DB cluster and are listed later in this topic\.

You can manage both cluster\-level and instance\-level parameters using the Amazon RDS console, the AWS CLI, or the Amazon RDS API\. There are separate commands for managing cluster\-level parameters and instance\-level parameters\. For example, you can use the [modify\-db\-cluster\-parameter\-group](http://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster-parameter-group.html) AWS CLI command to manage cluster\-level parameters in a DB cluster parameter group and use the [modify\-db\-parameter\-group](http://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-parameter-group.html) AWS CLI command to manage instance\-level parameters in a DB parameter group for a DB instance in a DB cluster\.

You can view both cluster\-level and instance\-level parameters in the Amazon RDS console, or by using the AWS CLI or Amazon RDS API\. For example, you can use the [describe\-db\-cluster\-parameters](http://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-cluster-parameters.html) AWS CLI command to view cluster\-level parameters in a DB cluster parameter group and use the [describe\-db\-parameters](http://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-parameters.html) AWS CLI command to view instance\-level parameters in a DB parameter group for a DB instance in a DB cluster\.

For more information about parameter groups, see [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)\.

### Cluster\-level Parameters<a name="AuroraPostgreSQL.Reference.Parameters.Cluster"></a>

The following table shows all of the parameters that apply to the entire Aurora PostgreSQL DB cluster\. 


| Parameter name | Modifiable | 
| --- | --- | 
|  `archive_command`  |  No  | 
|  `archive_timeout`  |  No  | 
|  `array_nulls`  |  Yes  | 
|  `autovacuum`  |  Yes  | 
|  `autovacuum_analyze_scale_factor`  |  Yes  | 
|  `autovacuum_analyze_threshold`  |  Yes  | 
|  `autovacuum_freeze_max_age`  |  Yes  | 
|  `autovacuum_max_workers`  |  Yes  | 
|  `autovacuum_multixact_freeze_max_age`  |  Yes  | 
|  `autovacuum_naptime`  |  Yes  | 
|  `autovacuum_vacuum_cost_delay`  |  Yes  | 
|  `autovacuum_vacuum_cost_limit`  |  Yes  | 
|  `autovacuum_vacuum_scale_factor`  |  Yes  | 
|  `autovacuum_vacuum_threshold`  |  Yes  | 
|  `autovacuum_work_mem`  |  Yes  | 
|  `backslash_quote`  |  Yes  | 
|  `client_encoding`  |  Yes  | 
|  `data_directory`  |  No  | 
|  `datestyle`  |  Yes  | 
|  `default_tablespace`  |  Yes  | 
|  `default_with_oids`  |  Yes  | 
|  `extra_float_digits`  |  Yes  | 
| huge\_pages | No | 
|  `intervalstyle`  |  Yes  | 
|  `lc_monetary`  |  Yes  | 
|  `lc_numeric`  |  Yes  | 
|  `lc_time`  |  Yes  | 
|  `log_autovacuum_min_duration`  |  Yes  | 
| max\_prepared\_transactions | Yes | 
|  `password_encryption`  |  No  | 
|  `port`  |  No  | 
| rds\.extensions | No | 
|  `rds.force_autovacuum_logging_level`  |  Yes  | 
|  `rds.force_ssl`  |  Yes  | 
|  `server_encoding`  |  No  | 
|  `ssl`  |  Yes  | 
|  `synchronous_commit`  |  Yes  | 
|  `timezone`  |  Yes  | 
|  `track_commit_timestamp`  |  Yes  | 
| vacuum\_cost\_delay | Yes | 
| vacuum\_cost\_limit | Yes | 
| vacuum\_cost\_page\_hit | Yes | 
| vacuum\_cost\_page\_miss | Yes | 
|  `vacuum_defer_cleanup_age`  |  Yes  | 
|  `vacuum_freeze_min_age`  |  Yes  | 
|  `vacuum_freeze_table_age`  |  Yes  | 
|  `vacuum_multixact_freeze_min_age`  |  Yes  | 
|  `vacuum_multixact_freeze_table_age`  |  Yes  | 
|  `wal_buffers`  |  Yes  | 

### Instance\-level Parameters<a name="AuroraPostgreSQL.Reference.Parameters.Instance"></a>

The following table shows all of the parameters that apply to a specific DB instance in an Aurora PostgreSQL DB cluster\. 


| Parameter name | Modifiable | 
| --- | --- | 
| `application_name` | Yes | 
| `authentication_timeout` | Yes | 
| `auto_explain.log_analyze` | Yes | 
| `auto_explain.log_buffers` | Yes | 
| `auto_explain.log_format` | Yes | 
| `auto_explain.log_min_duration` | Yes | 
| `auto_explain.log_nested_statements` | Yes | 
| `auto_explain.log_timing` | Yes | 
| `auto_explain.log_triggers` | Yes | 
| `auto_explain.log_verbose` | Yes | 
| `auto_explain.sample_rate` | Yes | 
| `backend_flush_after` | Yes | 
| `bgwriter_flush_after` | Yes | 
| `bytea_output` | Yes | 
| `check_function_bodies` | Yes | 
| `checkpoint_flush_after` | Yes | 
| `checkpoint_timeout` | No | 
| `client_min_messages` | Yes | 
| `config_file` | No | 
| `constraint_exclusion` | Yes | 
| `cpu_index_tuple_cost` | Yes | 
| `cpu_operator_cost` | Yes | 
| `cpu_tuple_cost` | Yes | 
| `cursor_tuple_fraction` | Yes | 
| `db_user_namespace` | No | 
| `deadlock_timeout` | Yes | 
| `debug_pretty_print` | Yes | 
| `debug_print_parse` | Yes | 
| `debug_print_plan` | Yes | 
| `debug_print_rewritten` | Yes | 
| `default_statistics_target` | Yes | 
| `default_transaction_deferrable` | Yes | 
| `default_transaction_isolation` | Yes | 
| `default_transaction_read_only` | Yes | 
| `effective_cache_size` | Yes | 
| `effective_io_concurrency` | No | 
| `enable_bitmapscan` | Yes | 
| `enable_hashagg` | Yes | 
| `enable_hashjoin` | Yes | 
| `enable_indexonlyscan` | Yes | 
| `enable_indexscan` | Yes | 
| `enable_material` | Yes | 
| `enable_mergejoin` | Yes | 
| `enable_nestloop` | Yes | 
| `enable_seqscan` | Yes | 
| `enable_sort` | Yes | 
| `enable_tidscan` | Yes | 
| `escape_string_warning` | Yes | 
| `exit_on_error` | No | 
| `force_parallel_mode` | Yes | 
| `from_collapse_limit` | Yes | 
| `geqo` | Yes | 
| `geqo_effort` | Yes | 
| `geqo_generations` | Yes | 
| `geqo_pool_size` | Yes | 
| `geqo_seed` | Yes | 
| `geqo_selection_bias` | Yes | 
| `geqo_threshold` | Yes | 
| `gin_fuzzy_search_limit` | Yes | 
| `gin_pending_list_limit` | Yes | 
| `hba_file` | No | 
| `hot_standby_feedback` | No | 
| `ident_file` | No | 
| `idle_in_transaction_session_timeout` | Yes | 
| `join_collapse_limit` | Yes | 
| `lc_messages` | Yes | 
| `listen_addresses` | No | 
| `lo_compat_privileges` | No | 
| `log_connections` | Yes | 
| `log_destination` | Yes | 
| `log_directory` | No | 
| `log_disconnections` | Yes | 
| `log_duration` | Yes | 
| `log_error_verbosity` | Yes | 
| `log_executor_stats` | Yes | 
| `log_file_mode` | No | 
| `log_filename` | Yes | 
| `log_hostname` | Yes | 
| `log_line_prefix` | No | 
| `log_lock_waits` | Yes | 
| `log_min_duration_statement` | Yes | 
| `log_min_error_statement` | Yes | 
| `log_min_messages` | Yes | 
| `log_parser_stats` | Yes | 
| `log_planner_stats` | Yes | 
| `log_replication_commands` | Yes | 
| `log_rotation_age` | Yes | 
| `log_rotation_size` | Yes | 
| `log_statement` | Yes | 
| `log_statement_stats` | Yes | 
| `log_temp_files` | Yes | 
| `log_timezone` | No | 
| `log_truncate_on_rotation` | No | 
| `logging_collector` | No | 
| `maintenance_work_mem` | Yes | 
| `max_connections` | Yes | 
| `max_files_per_process` | Yes | 
| `max_locks_per_transaction` | Yes | 
| `max_replication_slots` | Yes | 
| `max_stack_depth` | Yes | 
| `max_standby_archive_delay` | No | 
| `max_standby_streaming_delay` | No | 
| `max_wal_senders` | Yes | 
| `max_worker_processes` | Yes | 
| `min_parallel_relation_size` | Yes | 
| `old_snapshot_threshold` | Yes | 
| `operator_precedence_warning` | Yes | 
| `parallel_setup_cost` | Yes | 
| `parallel_tuple_cost` | Yes | 
| `pg_hint_plan.debug_print` | Yes | 
| `pg_hint_plan.enable_hint` | Yes | 
| `pg_hint_plan.enable_hint_table` | Yes | 
| `pg_hint_plan.message_level` | Yes | 
| `pg_hint_plan.parse_messages` | Yes | 
| `pg_stat_statements.max` | Yes | 
| `pg_stat_statements.save` | Yes | 
| `pg_stat_statements.track` | Yes | 
| `pg_stat_statements.track_utility` | Yes | 
| `pgaudit.log` | Yes | 
| `pgaudit.log_catalog` | Yes | 
| `pgaudit.log_level` | Yes | 
| `pgaudit.log_parameter` | Yes | 
| `pgaudit.log_relation` | Yes | 
| `pgaudit.log_statement_once` | Yes | 
| `pgaudit.role` | Yes | 
| `postgis.gdal_enabled_drivers` | Yes | 
| `quote_all_identifiers` | Yes | 
| `random_page_cost` | Yes | 
| `rds.force_admin_logging_level` | Yes | 
| `rds.log_retention_period` | Yes | 
| `rds.rds_superuser_reserved_connections` | Yes | 
| `rds.superuser_variables` | No | 
| `replacement_sort_tuples` | Yes | 
| `restart_after_crash` | No | 
| `row_security` | Yes | 
| `search_path` | Yes | 
| `seq_page_cost` | Yes | 
| `session_replication_role` | Yes | 
| `shared_buffers` | Yes | 
| `shared_preload_libraries` | Yes | 
| `sql_inheritance` | Yes | 
| `ssl_ca_file` | No | 
| `ssl_cert_file` | No | 
| `ssl_ciphers` | No | 
| `ssl_key_file` | No | 
| `standard_conforming_strings` | Yes | 
| `statement_timeout` | Yes | 
| `stats_temp_directory` | No | 
| `superuser_reserved_connections` | No | 
| `synchronize_seqscans` | Yes | 
| `syslog_facility` | No | 
| `tcp_keepalives_count` | Yes | 
| `tcp_keepalives_idle` | Yes | 
| `tcp_keepalives_interval` | Yes | 
| `temp_buffers` | Yes | 
| `temp_tablespaces` | Yes | 
| `track_activities` | Yes | 
| `track_activity_query_size` | Yes | 
| `track_counts` | Yes | 
| `track_functions` | Yes | 
| `track_io_timing` | Yes | 
| `transaction_deferrable` | Yes | 
| `transaction_read_only` | Yes | 
| `transform_null_equals` | Yes | 
| `unix_socket_directories` | No | 
| `unix_socket_group` | No | 
| `unix_socket_permissions` | No | 
| `update_process_title` | Yes | 
| `wal_receiver_status_interval` | Yes | 
| `wal_receiver_timeout` | Yes | 
| `wal_sender_timeout` | Yes | 
| `work_mem` | Yes | 
| `xmlbinary` | Yes | 
| `xmloption` | Yes | 

## Amazon Aurora PostgreSQL Events<a name="AuroraPostgreSQL.Reference.Waitevents"></a>

The following are some common wait events for Aurora PostgreSQL\.

**BufferPin:BufferPin**  
In this wait event, a session is waiting to access a data buffer during a period when no other session can examine that buffer\. Buffer pin waits can be protracted if another process holds an open cursor which last read data from the buffer in question\.

**Client:ClientRead**  
In this wait event, a session is receiving data from an application client\. This wait might be prevalent during bulk data loads using the COPY statement, or for applications that pass data to Aurora using many round trips between the client and the database\. A high number of client read waits per transaction may indicate excessive round trips, such as parameter passing\. You should compare this against the expected number of statements per transaction\. 

**IO:DataFilePrefetch**  
In this wait event, a session is waiting for an asynchronous prefetch from Aurora Storage\. 

**IO:DataFileRead**  
In this wait event, a session is reading data from Aurora Storage\. This may be a typical wait event for I/O intensive workloads\. SQL statements showing a comparatively large proportion of this wait event compared to other SQL statements may be using an inefficient query plan that requires reading large amounts of data\.

**IO:XactSync**  
In this wait event, a session is issuing a COMMIT or ROLLBACK, requiring the current transaction’s changes to be persisted\. Aurora is waiting for Aurora storage to acknowledge persistence\.   
This wait most often arises when there is a very high rate of commit activity on the system\. You can sometimes alleviate this wait by modifying applications to commit transactions in batches\. You might see this wait at the same time as CPU waits in a case where the DB load exceeds the number of virtual CPUs \(vCPUs\) for the DB instance\. In this case, the storage persistence might be competing for CPU with CPU\-intensive database workloads\. To alleviate this scenario, you can try reducing those workloads, or scaling up to a DB instance with more vCPUs\. 

**Lock:transactionid**  
In this wait event, a session is trying to modify data that has been modified by another session, and is waiting for the other session’s transaction to be committed or rolled back\. You can investigate blocking and waiting sessions in the pg\_locks view\. 

**LWLock:buffer\_content**  
In this wait event, a session is waiting to read or write a data page in memory while another session has that page locked for writing\. Heavy write contention for a single page \(hot page\), due to frequent updates of the same piece of data by many sessions, could lead to prevalence of this wait event\. Excessive use of foreign key constraints could increase lock duration, leading to increased contention\. You should investigate workloads experiencing high buffer\_content waits for usage of foreign key constraints to determine if the constraints are necessary\. 

For a complete list of PostgreSQL wait events, see [PostgreSQL wait\-event table](https://www.postgresql.org/docs/10/static/monitoring-stats.html#WAIT-EVENT-TABLE)\.
