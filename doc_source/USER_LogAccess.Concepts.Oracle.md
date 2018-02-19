# Oracle Database Log Files<a name="USER_LogAccess.Concepts.Oracle"></a>

You can access Oracle alert logs, audit files, and trace files by using the Amazon RDS console or APIs\. For more information about viewing, downloading, and watching file\-based database logs, see [Amazon RDS Database Log Files](USER_LogAccess.md)\. 

The Oracle audit files provided are the standard Oracle auditing files\. While Fine Grained Auditing \(FGA\) is a supported feature, log access does not provide access to FGA events stored in the SYS\.FGA\_LOG$ table and that are accessible through the DBA\_FGA\_AUDIT\_TRAIL view\. 

The `DescribeDBLogFiles` API action that lists the Oracle log files that are available for a DB instance ignores the `MaxRecords` parameter and returns up to 1000 records\. 

## Retention Schedule<a name="USER_LogAccess.Concepts.Oracle.Retention"></a>

The Oracle database engine may rotate logs files if they get very large\. If you want to retain audit or trace files, you should download them\. Storing the files locally reduces your Amazon RDS storage costs and makes more space available for your data\. 

The following is the retention schedule for Oracle alert logs, audit files, and trace files on Amazon RDS\. 


****  

| Log Type | Retention Schedule | 
| --- | --- | 
|  Alert Logs  |   The text alert log is rotated daily with 30 day retention managed by Amazon RDS\. The XML alert log is retained for at least 7 days and can be accessed using the `ALERTLOG` view\.    | 
|  Audit Files  |   The default retention period for audit files is 7 days\. Amazon RDS may delete audit files older than 7 days\.    | 
|  Trace files  |  The default retention period for trace files is 7 days\. Amazon RDS may delete trace files older than 7 days\.    | 

## Switching Online Log files<a name="USER_LogAccess.Concepts.Oracle.SwitchingLogfiles"></a>

You can use the Amazon RDS procedure `rdsadmin.rdsadmin_util.switch_logfile` switch online log files\. For more information, see [Switching Online Log Files](Appendix.Oracle.CommonDBATasks.Log.md#Appendix.Oracle.CommonDBATasks.SwitchingLogfiles)\. 

## Retrieving Archived Redo Logs<a name="USER_LogAccess.Concepts.Oracle.ArchivedRedoLogs"></a>

You can retain archived redo logs\. For more information, see [Retaining Archived Redo Logs](Appendix.Oracle.CommonDBATasks.Log.md#Appendix.Oracle.CommonDBATasks.RetainRedoLogs)\. 

## Working with Oracle Trace Files<a name="USER_LogAccess.Concepts.Oracle.WorkingWithTracefiles"></a>

This section describes Amazon RDS\-specific procedures to create, refresh, access, and delete trace files\. 

### Listing Files<a name="USER_LogAccess.Concepts.Oracle.WorkingWithTracefiles.ViewingBackgroundDumpDest"></a>

Two procedures are available to allow access to any file within the `background_dump_dest`\. The first method refreshes a view containing a listing of all files currently in the `background_dump_dest`: 

```
1. exec rdsadmin.manage_tracefiles.refresh_tracefile_listing;
```

Once the view is refreshed, use the following view to access the results\.

```
1. rdsadmin.tracefile_listing
```

An alternative to the previous process is to use "from table" to stream non\-table data in a table\-like format to list DB directory contents: 

```
1. SELECT * FROM table(rdsadmin.rds_file_util.listdir('BDUMP'));
```

The following query shows text of a log file: 

```
1. SELECT text FROM table(rdsadmin.rds_file_util.read_text_file('BDUMP','alert_xxx.log'));
```

### Generating Trace Files and Tracing a Session<a name="USER_LogAccess.Concepts.Oracle.WorkingWithTracefiles.Generating"></a>

Since there are no restrictions on `alter session`, many standard methods to generate trace files in Oracle remain available to an Amazon RDS DB instance\. The following procedures are provided for trace files that require greater access\. 


****  

|  Oracle Method  |  Amazon RDS Method | 
| --- | --- | 
| `oradebug hanganalyze 3 ` | `exec rdsadmin.manage_tracefiles.hanganalyze; ` | 
| `oradebug dump systemstate 266 ` | `exec rdsadmin.manage_tracefiles.dump_systemstate;` | 

You can use many standard methods to trace individual sessions connected to an Oracle DB instance in Amazon RDS\. To enable tracing for a session, you can run subprograms in PL/SQL packages supplied by Oracle, such as the DBMS\_SESSION and DBMS\_MONITOR packages\. For more information, see [ Enabling Tracing for a Session](https://docs.oracle.com/database/121/TGSQL/tgsql_trace.htm#GUID-F872D6F9-E015-481F-80F6-8A7036A6AD29) in the Oracle documentation\. 

### Retrieving Trace Files<a name="USER_LogAccess.Concepts.Oracle.WorkingWithTracefiles.Retrieving"></a>

You can retrieve any trace file in `background_dump_dest` using a standard SQL query of an Amazon RDS managed external table\. To use this method, you must execute the procedure to set the location for this table to the specific trace file\. 

For example, you can use the `rdsadmin.tracefile_listing` view mentioned above to list the all of the trace files on the system\. You can then set the tracefile\_table view to point to the intended trace file using the following procedure: 

```
1. exec rdsadmin.manage_tracefiles.set_tracefile_table_location('CUST01_ora_3260_SYSTEMSTATE.trc');
```

The following example creates an external table in the current schema with the location set to the file provided\. The contents can be retrieved into a local file using a SQL query\. 

```
1. # eg: send the contents of the tracefile to a local file:
2. sqlplus user/password@TNS alias << EOF > /tmp/tracefile.txt
3. select * from tracefile_table;
4. EOF
```

### Purging Trace Files<a name="USER_LogAccess.Concepts.Oracle.WorkingWithTracefiles.Purging"></a>

Trace files can accumulate and consume disk space\. Amazon RDS purges trace files by default and log files that are older than seven days\. You can view and set the trace file retention period using the show\_configuration procedure\. Note that you should run the command `SET SERVEROUTPUT ON` so that you can view the configuration results\. 

The following example shows the current trace file retention period, and then sets a new trace file retention period\. 

```
 1. # Show the current tracefile retention
 2. SQL> exec rdsadmin.rdsadmin_util.show_configuration;
 3. NAME:tracefile retention
 4. VALUE:10080
 5. DESCRIPTION:tracefile expiration specifies the duration in minutes before tracefiles in bdump are automatically deleted.
 6. 		
 7. # Set the tracefile retention to 24 hours:
 8. SQL> exec rdsadmin.rdsadmin_util.set_configuration('tracefile retention',1440);
 9. 
10. #show the new tracefile retention
11. SQL> exec rdsadmin.rdsadmin_util.show_configuration;
12. NAME:tracefile retention
13. VALUE:1440
14. DESCRIPTION:tracefile expiration specifies the duration in minutes before tracefiles in bdump are automatically deleted.
```

In addition to the periodic purge process, you can manually remove files from the `background_dump_dest`\. The following example shows how to purge all files older than five minutes\. 

```
1. exec rdsadmin.manage_tracefiles.purge_tracefiles(5);
```

You can also purge all files that match a specific pattern \(do not include the file extension such as \.trc\)\. The following example shows how to purge all files that start with "SCHPOC1\_ora\_5935"\. 

```
1. exec rdsadmin.manage_tracefiles.purge_tracefiles('SCHPOC1_ora_5935');
```

## Previous Methods for Accessing Alert Logs and Listener Logs<a name="USER_LogAccess.Concepts.Oracle.AlertLogAndListenerLog"></a>

You can view the alert log using the Amazon RDS console\. You can also use the following SQL statement to access the alert log: 

```
1. select message_text from alertlog;
```

To access the listener log, use the following SQL statement: 

```
1. select message_text from listenerlog;
```

**Note**  
Oracle rotates the alert and listener logs when they exceed 10MB, at which point they will be unavailable from the Amazon RDS views\. 

## Related Topics<a name="USER_LogAccess.Concepts.Oracle.Related"></a>

+ [Common DBA Log Tasks for Oracle DB Instances](Appendix.Oracle.CommonDBATasks.Log.md)