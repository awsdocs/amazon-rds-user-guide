# PostgreSQL Database Log Files<a name="USER_LogAccess.Concepts.PostgreSQL"></a>

RDS PostgreSQL generates query and error logs\. We write auto\-vacuum info and rds\_admin actions to the error log\. Postgres also logs connections/disconnections/checkpoints to the error log\. For more information, see [http://www\.postgresql\.org/docs/9\.4/static/runtime\-config\-logging\.html](http://www.postgresql.org/docs/9.1/static/runtime-config-logging.html)

You can set the retention period for system logs using the `rds.log_retention_period` parameter in the DB parameter group associated with your DB instance\. The unit for this parameter is minutes; for example, a setting of 1440 would retain logs for one day\. The default value is 4320 \(three days\)\. The maximum value is 10080 \(seven days\)\. Note that your instance must have enough allocated storage to contain the retained log files\. 

You can enable query logging for your PostgreSQL DB instance by setting two parameters in the DB parameter group associated with your DB instance: `log_statement` and `log_min_duration_statement`\. The `log_statement` parameter controls which SQL statements are logged\. We recommend setting this parameter to *all* to log all statements when debugging issues in your DB instance\. The default value is *none*\. Alternatively, you can set this value to *ddl* to log all data definition language \(DDL\) statements \(CREATE, ALTER, DROP, etc\.\) or to *mod* to log all DDL and data modification language \(DML\) statements \(INSERT, UPDATE, DELETE, etc\.\)\.

The `log_min_duration_statement` parameter sets the limit in milliseconds of a statement to be logged\. All SQL statements that run longer than the parameter setting are logged\. This parameter is disabled and set to minus 1 \(\-1\) by default\. Enabling this parameter can help you find unoptimized queries\. For more information on these settings, see [Error Reporting and Logging](http://www.postgresql.org/docs/9.3/static/runtime-config-logging.html) in the PostgreSQL documentation\.

 If you are new to setting parameters in a DB parameter group and associating that parameter group with a DB instance, see [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)

The following steps show how to set up query logging:

1. Set the `log_statement` parameter to *all*\. The following example shows the information that is written to the postgres\.log file:

   ```
   2013-11-05 16:48:56 UTC::@:[2952]:LOG:  received SIGHUP, reloading configuration files
   2013-11-05 16:48:56 UTC::@:[2952]:LOG:  parameter "log_min_duration_statement" changed to "1"
   ```

   Additional information is written to the postgres\.log file when you execute a query\. The following example shows the type of information written to the file after a query:

   ```
   2013-11-05 16:41:07 UTC::@:[2955]:LOG:  checkpoint starting: time
   2013-11-05 16:41:07 UTC::@:[2955]:LOG:  checkpoint complete: wrote 1 buffers (0.3%); 0 transaction log file(s) added, 0 removed, 1 recycled; write=0.000 s, sync=0.003 s, total=0.012 s; sync files=1, longest=0.003 s, average=0.003 s
   2013-11-05 16:45:14 UTC:[local]:master@postgres:[8839]:LOG:  statement: SELECT d.datname as "Name",
   	       pg_catalog.pg_get_userbyid(d.datdba) as "Owner",
   	       pg_catalog.pg_encoding_to_char(d.encoding) as "Encoding",
   	       d.datcollate as "Collate",
   	       d.datctype as "Ctype",
   	       pg_catalog.array_to_string(d.datacl, E'\n') AS "Access privileges"
   	FROM pg_catalog.pg_database d
   	ORDER BY 1;
   2013-11-05 16:45:
   ```

1. Set the `log_min_duration_statement` parameter\. The following example shows the information that is written to the postgres\.log file when the parameter is set to *1*:

   ```
   2013-11-05 16:48:56 UTC::@:[2952]:LOG:  received SIGHUP, reloading configuration files
   2013-11-05 16:48:56 UTC::@:[2952]:LOG:  parameter "log_min_duration_statement" changed to "1"
   ```

   Additional information is written to the postgres\.log file when you execute a query that exceeds the duration parameter setting\. The following example shows the type of information written to the file after a query:

   ```
   2013-11-05 16:51:10 UTC:[local]:master@postgres:[9193]:LOG:  statement: SELECT c2.relname, i.indisprimary, i.indisunique, i.indisclustered, i.indisvalid, pg_catalog.pg_get_indexdef(i.indexrelid, 0, true),
   	  pg_catalog.pg_get_constraintdef(con.oid, true), contype, condeferrable, condeferred, c2.reltablespace
   	FROM pg_catalog.pg_class c, pg_catalog.pg_class c2, pg_catalog.pg_index i
   	  LEFT JOIN pg_catalog.pg_constraint con ON (conrelid = i.indrelid AND conindid = i.indexrelid AND contype IN ('p','u','x'))
   	WHERE c.oid = '1255' AND c.oid = i.indrelid AND i.indexrelid = c2.oid
   	ORDER BY i.indisprimary DESC, i.indisunique DESC, c2.relname;
   2013-11-05 16:51:10 UTC:[local]:master@postgres:[9193]:LOG:  duration: 3.367 ms
   2013-11-05 16:51:10 UTC:[local]:master@postgres:[9193]:LOG:  statement: SELECT c.oid::pg_catalog.regclass FROM pg_catalog.pg_class c, pg_catalog.pg_inherits i WHERE c.oid=i.inhparent AND i.inhrelid = '1255' ORDER BY inhseqno;
   2013-11-05 16:51:10 UTC:[local]:master@postgres:[9193]:LOG:  duration: 1.002 ms
   2013-11-05 16:51:10 UTC:[local]:master@postgres:[9193]:LOG:  statement: SELECT c.oid::pg_catalog.regclass FROM pg_catalog.pg_class c, pg_catalog.pg_inherits i WHERE c.oid=i.inhrelid AND i.inhparent = '1255' ORDER BY c.oid::pg_catalog.regclass::pg_catalog.text;
   2013-11-05 16:51:18 UTC:[local]:master@postgres:[9193]:LOG:  statement: select proname from pg_proc;
   2013-11-05 16:51:18 UTC:[local]:master@postgres:[9193]:LOG:  duration: 3.469 ms
   ```