# Working with Trace and Dump Files<a name="Appendix.SQLServer.CommonDBATasks.TraceFiles"></a>

This section describes working with trace files and dump files for your Amazon RDS DB instances running Microsoft SQL Server\. 

## Generating a Trace SQL Query<a name="Appendix.SQLServer.CommonDBATasks.TraceFiles.TraceSQLQuery"></a>

```
1. declare @rc int 
2. declare @TraceID int 
3. declare @maxfilesize bigint 
4. 
5. set @maxfilesize = 5
6. 
7. exec @rc = sp_trace_create @TraceID output,  0, N'D:\rdsdbdata\log\rdstest', @maxfilesize, NULL
```

## Viewing an Open Trace<a name="Appendix.SQLServer.CommonDBATasks.TraceFiles.ViewOpenTrace"></a>

```
1. select * from ::fn_trace_getinfo(default)
```

## Viewing Trace Contents<a name="Appendix.SQLServer.CommonDBATasks.TraceFiles.ViewTraceContents"></a>

```
1. select * from ::fn_trace_gettable('D:\rdsdbdata\log\rdstest.trc', default)
```

## Setting the Retention Period for Trace and Dump Files<a name="Appendix.SQLServer.CommonDBATasks.TraceFiles.PurgeTraceFiles"></a>

Trace and dump files can accumulate and consume disk space\. By default, Amazon RDS purges trace and dump files that are older than seven days\. 

To view the current trace and dump file retention period, use the `rds_show_configuration` procedure, as shown in the following example\. 

```
1. exec rdsadmin..rds_show_configuration;
```

To modify the retention period for trace files, use the `rds_set_configuration` procedure and set the `tracefile retention` in minutes\. The following example sets the trace file retention period to 24 hours\. 

```
1. exec rdsadmin..rds_set_configuration 'tracefile retention', 1440; 
```

To modify the retention period for dump files, use the `rds_set_configuration` procedure and set the `dumpfile retention` in minutes\. The following example sets the dump file retention period to 3 days\. 

```
1. exec rdsadmin..rds_set_configuration 'dumpfile retention', 4320; 
```

For security reasons, you cannot delete a specific trace or dump file on a SQL Server DB instance\. To delete all unused trace or dump files, set the retention period for the files to 0\. 