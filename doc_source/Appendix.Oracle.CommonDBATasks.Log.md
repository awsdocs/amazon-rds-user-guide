# Common DBA Log Tasks for Oracle DB Instances<a name="Appendix.Oracle.CommonDBATasks.Log"></a>

Following, you can find how to perform certain common DBA tasks related to logging on your Amazon RDS DB instances running Oracle\. To deliver a managed service experience, Amazon RDS doesn't provide shell access to DB instances, and restricts access to certain system procedures and tables that require advanced privileges\. 

For more information, see [Oracle Database Log Files](USER_LogAccess.Concepts.Oracle.md)\. 

**Topics**
+ [Setting Force Logging](#Appendix.Oracle.CommonDBATasks.SettingForceLogging)
+ [Setting Supplemental Logging](#Appendix.Oracle.CommonDBATasks.AddingSupplementalLogging)
+ [Switching Online Log Files](#Appendix.Oracle.CommonDBATasks.SwitchingLogfiles)
+ [Adding Online Redo Logs](#Appendix.Oracle.CommonDBATasks.RedoLogs)
+ [Dropping Online Redo Logs](#Appendix.Oracle.CommonDBATasks.DroppingRedoLogs)
+ [Resizing Online Redo Logs](#Appendix.Oracle.CommonDBATasks.ResizingRedoLogs)
+ [Retaining Archived Redo Logs](#Appendix.Oracle.CommonDBATasks.RetainRedoLogs)
+ [Accessing Transaction Logs](#Appendix.Oracle.CommonDBATasks.Log.Download)

## Setting Force Logging<a name="Appendix.Oracle.CommonDBATasks.SettingForceLogging"></a>

In force logging mode, Oracle logs all changes to the database except changes in temporary tablespaces and temporary segments \(`NOLOGGING` clauses are ignored\)\. For more information, see [Specifying FORCE LOGGING Mode](https://docs.oracle.com/cd/E11882_01/server.112/e25494/create.htm#ADMIN11096) in the Oracle documentation\. 

To set force logging, use the Amazon RDS procedure `rdsadmin.rdsadmin_util.force_logging`\. The `force_logging` procedure has the following parameters\. 


****  

| Parameter Name | Data Type | Default | Yes | Description | 
| --- | --- | --- | --- | --- | 
| `p_enable` | boolean | true | No |  Set to `true` to put the database in force logging mode, `false` to remove the database from force logging mode\.   | 

The following example puts the database in force logging mode\. 

```
exec rdsadmin.rdsadmin_util.force_logging(p_enable => true);
```

## Setting Supplemental Logging<a name="Appendix.Oracle.CommonDBATasks.AddingSupplementalLogging"></a>

Supplemental logging ensures that LogMiner and products that use LogMiner technology have sufficient information to support chained rows and storage arrangements such as cluster tables\. For more information, see [Supplemental Logging](https://docs.oracle.com/cd/E11882_01/server.112/e22490/logminer.htm#SUTIL1582) in the Oracle documentation\. 

Oracle Database doesn't enable supplemental logging by default\. To enable and disable supplemental logging, use the Amazon RDS procedure `rdsadmin.rdsadmin_util.alter_supplemental_logging`\. For more information about how Amazon RDS manages the retention of archived redo logs for Oracle DB instances, see [Retaining Archived Redo Logs](#Appendix.Oracle.CommonDBATasks.RetainRedoLogs)\. 

The `alter_supplemental_logging` procedure has the following parameters\. 


****  

| Parameter Name | Data Type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
| `p_action` | varchar2 | — | Yes |  `'ADD'` to add supplemental logging, `'DROP'` to drop supplemental logging\.   | 
| `p_type` | varchar2 | null | No |  The type of supplemental logging\. Valid values are `'ALL'`, `'FOREIGN KEY'`, `'PRIMARY KEY'`, or `'UNIQUE'`\.   | 

The following example enables supplemental logging\.

```
begin
    rdsadmin.rdsadmin_util.alter_supplemental_logging(
        p_action => 'ADD');
end;
/
```

The following example enables supplemental logging for all fixed\-length maximum size columns\. 

```
begin
    rdsadmin.rdsadmin_util.alter_supplemental_logging(
        p_action => 'ADD',
        p_type   => 'ALL');
end;
/
```

The following example enables supplemental logging for primary key columns\. 

```
begin
    rdsadmin.rdsadmin_util.alter_supplemental_logging(
        p_action => 'ADD',
        p_type   => 'PRIMARY KEY');
end;
/
```

## Switching Online Log Files<a name="Appendix.Oracle.CommonDBATasks.SwitchingLogfiles"></a>

To switch log files, use the Amazon RDS procedure `rdsadmin.rdsadmin_util.switch_logfile`\. The `switch_logfile` procedure has no parameters\. 

The following example switches log files\.

```
exec rdsadmin.rdsadmin_util.switch_logfile;
```

## Adding Online Redo Logs<a name="Appendix.Oracle.CommonDBATasks.RedoLogs"></a>

An Amazon RDS DB instance running Oracle starts with four online redo logs, 128 MB each\. To add additional redo logs, use the Amazon RDS procedure `rdsadmin.rdsadmin_util.add_logfile`\. 

The `add_logfile` procedure has the following parameters\. 

**Note**  
The parameters are mutually exclusive\.


****  

| Parameter Name | Data Type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
| `bytes` | positive | null | No | The size of the log file in bytes\. | 
| `p_size` | varchar2 | — | Yes |  The size of the log file\. You can specify the size in kilobytes \(K\), megabytes \(M\), or gigabytes \(G\)\.   | 

The following command adds a 100 MB log file\.

```
exec rdsadmin.rdsadmin_util.add_logfile(p_size => '100M');
```

## Dropping Online Redo Logs<a name="Appendix.Oracle.CommonDBATasks.DroppingRedoLogs"></a>

To drop redo logs, use the Amazon RDS procedure `rdsadmin.rdsadmin_util.drop_logfile`\. The `drop_logfile` procedure has the following parameters\. 


****  

| Parameter Name | Data Type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
| `grp` | positive | — | Yes | The group number of the log\. | 

The following example drops the log with group number 3\. 

```
exec rdsadmin.rdsadmin_util.drop_logfile(grp => 3);
```

You can only drop logs that have a status of unused or inactive\. The following example gets the statuses of the logs\.

```
select GROUP#, STATUS from V$LOG;

GROUP#     STATUS
---------- ----------------
1          CURRENT
2          INACTIVE
3          INACTIVE
4          UNUSED
```

## Resizing Online Redo Logs<a name="Appendix.Oracle.CommonDBATasks.ResizingRedoLogs"></a>

An Amazon RDS DB instance running Oracle starts with four online redo logs, 128 MB each\. The following example shows how you can use Amazon RDS procedures to resize your logs from 128 MB each to 512 MB each\. 

```
/* Query V$LOG to see the logs.          */
/* You start with 4 logs of 128 MB each. */

select GROUP#, BYTES, STATUS from V$LOG;

GROUP#     BYTES      STATUS
---------- ---------- ----------------
1          134217728  INACTIVE
2          134217728  CURRENT
3          134217728  INACTIVE
4          134217728  INACTIVE


/* Add four new logs that are each 512 MB */

exec rdsadmin.rdsadmin_util.add_logfile(bytes => 536870912);
exec rdsadmin.rdsadmin_util.add_logfile(bytes => 536870912);
exec rdsadmin.rdsadmin_util.add_logfile(bytes => 536870912);
exec rdsadmin.rdsadmin_util.add_logfile(bytes => 536870912);


/* Query V$LOG to see the logs. */ 
/* Now there are 8 logs.        */

select GROUP#, BYTES, STATUS from V$LOG;

GROUP#     BYTES      STATUS
---------- ---------- ----------------
1          134217728  INACTIVE
2          134217728  CURRENT
3          134217728  INACTIVE
4          134217728  INACTIVE
5          536870912  UNUSED
6          536870912  UNUSED
7          536870912  UNUSED
8          536870912  UNUSED


/* Drop each inactive log using the group number. */

exec rdsadmin.rdsadmin_util.drop_logfile(grp => 1);
exec rdsadmin.rdsadmin_util.drop_logfile(grp => 3);
exec rdsadmin.rdsadmin_util.drop_logfile(grp => 4);


/* Query V$LOG to see the logs. */ 
/* Now there are 5 logs.        */

select GROUP#, BYTES, STATUS from V$LOG;

GROUP#     BYTES      STATUS
---------- ---------- ----------------
2          134217728  CURRENT
5          536870912  UNUSED
6          536870912  UNUSED
7          536870912  UNUSED
8          536870912  UNUSED


/* Switch logs so that group 2 is no longer current. */

exec rdsadmin.rdsadmin_util.switch_logfile;


/* Query V$LOG to see the logs.        */ 
/* Now one of the new logs is current. */

SQL>select GROUP#, BYTES, STATUS from V$LOG;

GROUP#     BYTES      STATUS
---------- ---------- ----------------
2          134217728  ACTIVE
5          536870912  CURRENT
6          536870912  UNUSED
7          536870912  UNUSED
8          536870912  UNUSED


/* If the status of log 2 is still "ACTIVE", issue a checkpoint to clear it to "INACTIVE".  */

exec rdsadmin.rdsadmin_util.checkpoint;


/* Query V$LOG to see the logs.            */ 
/* Now the final original log is inactive. */

select GROUP#, BYTES, STATUS from V$LOG;

GROUP#     BYTES      STATUS
---------- ---------- ----------------
2          134217728  INACTIVE
5          536870912  CURRENT
6          536870912  UNUSED
7          536870912  UNUSED
8          536870912  UNUSED


# Drop the final inactive log.

exec rdsadmin.rdsadmin_util.drop_logfile(grp => 2);


/* Query V$LOG to see the logs.    */ 
/* Now there are four 512 MB logs. */

select GROUP#, BYTES, STATUS from V$LOG;

GROUP#     BYTES      STATUS
---------- ---------- ----------------
5          536870912  CURRENT
6          536870912  UNUSED
7          536870912  UNUSED
8          536870912  UNUSED
```

## Retaining Archived Redo Logs<a name="Appendix.Oracle.CommonDBATasks.RetainRedoLogs"></a>

You can retain archived redo logs locally on your DB instance for use with products like Oracle LogMiner \(DBMS\_LOGMNR\)\. After you have retained the redo logs, you can use LogMiner to analyze the logs\. For more information, see [Using LogMiner to Analyze Redo Log Files](http://docs.oracle.com/cd/E11882_01/server.112/e22490/logminer.htm) in the Oracle documentation\. 

To retain archived redo logs, use the Amazon RDS procedure `rdsadmin.rdsadmin_util.set_configuration`\. The `set_configuration` procedure has the following parameters\. 


****  

| Parameter Name | Data Type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
| `name` | varchar | — | Yes | The name of the configuration to update\. | 
| `value` | varchar | — | Yes | The value for the configuration\. | 

The following example retains 24 hours of redo logs\. 

```
begin
    rdsadmin.rdsadmin_util.set_configuration(
        name  => 'archivelog retention hours',
        value => '24');
end;
/
commit;
```

**Note**  
The commit is required for the change to take effect\.

To view how long archived redo logs are kept for your DB instance, use the Amazon RDS procedure `rdsadmin.rdsadmin_util.show_configuration`\.

The following example shows the log retention time\.

```
set serveroutput on
exec rdsadmin.rdsadmin_util.show_configuration;
```

The output shows the current setting for `archivelog retention hours`\. The following output shows that archived redo logs are kept for 48 hours\.

```
NAME:archivelog retention hours
VALUE:48
DESCRIPTION:ArchiveLog expiration specifies the duration in hours before archive/redo log files are automatically deleted.
```

Because the archived redo logs are retained on your DB instance, ensure that your DB instance has enough allocated storage for the retained logs\. To determine how much space your DB instance has used in the last X hours, you can run the following query, replacing X with the number of hours\. 

```
select sum(BLOCKS * BLOCK_SIZE) bytes 
  from V$ARCHIVED_LOG
 where FIRST_TIME >= SYSDATE-(X/24) and DEST_ID=1;
```

Archived redo logs are only generated if the backup retention period of your DB instance is greater than zero\. By default the backup retention period is greater than zero, so unless you explicitly set yours to zero, archived redo logs are generated for your DB instance\. 

After the archived redo logs are removed from your DB instance, you can't download them again to your DB instance\. Amazon RDS retains the archived redo logs outside of your DB instance to support restoring your DB instance to a point in time\. Amazon RDS retains the archived redo logs outside of your DB instance based on the backup retention period configured for your DB instance\. To modify the backup retention period for your DB instance, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\. 

**Note**  
In some cases, you might be using JDBC on Linux to download archived redo logs and experience long latency times and connection resets\. In such cases, the issues might be caused by the default random number generator setting on your Java client\. We recommend setting your JDBC drivers to use a nonblocking random number generator\. 

## Accessing Transaction Logs<a name="Appendix.Oracle.CommonDBATasks.Log.Download"></a>

Accessing transaction logs is supported for Oracle version 11\.2\.0\.4\.v11 and later, Oracle version 12\.1\.0\.2\.v7 and later, all 12\.2\.0\.1 versions, all 18\.0\.0\.0 versions, and all 19\.0\.0 versions\.

You might want to access your online and archived redo log files for mining with external tools such as GoldenGate, Attunity, Informatica, and others\. If you want to access your online and archived redo log files, you must first create directory objects that provide read\-only access to the physical file paths\. 

The following code creates directories that provide read\-only access to your online and archived redo log files: 

**Important**  
This code also revokes the `DROP ANY DIRECTORY` privilege\.

```
exec rdsadmin.rdsadmin_master_util.create_archivelog_dir;
exec rdsadmin.rdsadmin_master_util.create_onlinelog_dir;
```

After you create directory objects for your online and archived redo log files, you can read the files by using PL/SQL\. For more information about reading files from directory objects, see [Listing Files in a DB Instance Directory](Appendix.Oracle.CommonDBATasks.Misc.md#Appendix.Oracle.CommonDBATasks.ListDirectories) and [Reading Files in a DB Instance Directory](Appendix.Oracle.CommonDBATasks.Misc.md#Appendix.Oracle.CommonDBATasks.ReadingFiles)\. 

The following code drops the directories for your online and archived redo log files\. 

```
exec rdsadmin.rdsadmin_master_util.drop_archivelog_dir;
exec rdsadmin.rdsadmin_master_util.drop_onlinelog_dir;
```

The following code grants and revokes the `DROP ANY DIRECTORY` privilege\.

```
exec rdsadmin.rdsadmin_master_util.revoke_drop_any_directory;
exec rdsadmin.rdsadmin_master_util.grant_drop_any_directory;
```