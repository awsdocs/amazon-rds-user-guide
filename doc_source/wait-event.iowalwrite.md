# IO:WALWrite<a name="wait-event.iowalwrite"></a>



**Topics**
+ [Supported engine versions](#wait-event.iowalwrite.context.supported)
+ [Context](#wait-event.iowalwrite.context)
+ [Likely causes of increased waits](#wait-event.iowalwrite.causes)
+ [Actions](#wait-event.iowalwrite.actions)

## Supported engine versions<a name="wait-event.iowalwrite.context.supported"></a>

This wait event information is supported for all versions of RDS for PostgreSQL 10 and higher\.

## Context<a name="wait-event.iowalwrite.context"></a>

Activity in the database that's generating write\-ahead log data fills up the WAL buffers first and then writes to disk, asynchronously\. The wait event `IO:WALWrite` is generated when the SQL session is waiting for the WAL data to complete writing to disk so that it can release the transaction's COMMIT call\. 

## Likely causes of increased waits<a name="wait-event.iowalwrite.causes"></a>

If this wait event occurs often, you should review your workload and the type of updates that your workload performs and their frequency\. In particular, look for the following types of activity\.

**Heavy DML activity**  
Changing data in database tables doesn't happen instantaneously\. An insert to one table might need to wait for an insert or an update to the same table from another client\. The data manipulation language \(DML\) statements for changing data values \(INSERT, UPDATE, DELETE, COMMIT, ROLLBACK TRANSACTION\) can result in contention that causes the write\-ahead logfile to be waiting for the buffers to be flushed\. This situation is captured in the following Amazon RDS Performance Insights metrics that indicate heavy DML activity\.  
+  `tup_inserted`
+ `tup_updated`
+ `tup_deleted`
+ `xcat_rollback`
+ `xact_commit`
For more information about these metrics, see [Performance Insights counters for Amazon RDS for PostgreSQL](USER_PerfInsights_Counters.md#USER_PerfInsights_Counters.PostgreSQL)\.

**Frequent checkpoint activity**  
Frequent checkpoints contribute to larger WAL size\. In RDS for PostgreSQL, full page writes are always "on\." Full page writes help protect against data loss\. However, when checkpointing occurs too frequently, the system can suffer overall performance issues\. This is especially true on systems with heavy DML activity\. In some cases, you might find error messages in your `postgresql.log` stating that “checkpoints are occurring too frequently\."   
We recommend that when tuning checkpoints, you carefully balance performance against expected time need to recover in the event of an abnormal shutdown\. 

## Actions<a name="wait-event.iowalwrite.actions"></a>

We recommend the following actions to reduce the numbers of this wait event\.

**Topics**
+ [Reduce the number of commits](#wait-event.iowalwrite.actions.problem)
+ [Monitor your checkpoints](#wait-event.iowalwrite.actions.monitor)
+ [Scale up IO](#wait-event.iowalwrite.actions.scale-io)

### Reduce the number of commits<a name="wait-event.iowalwrite.actions.problem"></a>

To reduce the number of commits, you can combine statements into transaction blocks\. Use Amazon RDS Performance Insights to examine the type of queries being run\. You can also move large maintenance operations to off\-peak hours\. For example, create indexes or use `pg_repack` operations during non\-production hours\.

### Monitor your checkpoints<a name="wait-event.iowalwrite.actions.monitor"></a>

There are two parameters that you can monitor to see how frequently your RDS for PostgreSQL DB instance is writing to the WAL file for checkpoints\. 
+ `log_checkpoints` – This parameter is set to "on" by default\. It causes a message to get sent to the PostgreSQL log for each checkpoint\. These log messages include the number of buffers written, the time spent writing them, and the number of WAL files added, removed, or recycled for the given checkpoint\. 

  For more information about this parameter, see [Error Reporting and Logging](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-CHECKPOINTS) in the PostgreSQL documentation\. 
+ `checkpoint_warning` – This parameter sets a threshold value \(in seconds\) for checkpoint frequency above which a warning is generated\. By default, this parameter isn't set\. You can set a value for this parameter to get a warning when your RDS for PostgreSQL DB instance needs to write butters to the WAL file more often than the specified value\. For example, say you set this parameter to 30\. If your RDS for PostgreSQL instance needs to write its buffers to the WAL file more often than every 30 seconds, the warning that "checkpoints are occurring too frequently" is sent to the PostgreSQL log\. This can indicate that your `max_wal_size` value should be increased\. 

  For more information, see [Write Ahead Log](https://www.postgresql.org/docs/current/runtime-config-wal.html#RUNTIME-CONFIG-WAL-CHECKPOINTS) in the PostgreSQL documentation\. 

### Scale up IO<a name="wait-event.iowalwrite.actions.scale-io"></a>

This type of input/output \(IO\) wait event can remediated by scaling the input/output operations per second \(IOPs\) to provide faster IO\. Scaling IO is preferable to scaling CPU, because scaling CPU can result in even more IO contention because the increased CPU can handle more work and thus make the IO bottleneck even worse\. In general, we recommend that you consider tuning your workload before performing scaling operations\.