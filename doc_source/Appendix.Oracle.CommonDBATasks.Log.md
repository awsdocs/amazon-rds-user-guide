# Common DBA Log Tasks for Oracle DB Instances<a name="Appendix.Oracle.CommonDBATasks.Log"></a>

This section describes how you can perform common DBA tasks related to logging on your Amazon RDS DB instances running Oracle\. To deliver a managed service experience, Amazon RDS doesn't provide shell access to DB instances, and restricts access to certain system procedures and tables that require advanced privileges\. 

For more information, see [Oracle Database Log Files](USER_LogAccess.Concepts.Oracle.md)\. 

## Setting Force Logging<a name="Appendix.Oracle.CommonDBATasks.SettingForceLogging"></a>

In force logging mode, Oracle logs all changes to the database except changes in temporary tablespaces and temporary segments \(`NOLOGGING` clauses are ignored\)\. For more information, see [Specifying FORCE LOGGING Mode](https://docs.oracle.com/cd/E11882_01/server.112/e25494/create.htm#ADMIN11096) in the Oracle documentation\. 

You can use the Amazon RDS procedure `rdsadmin.rdsadmin_util.force_logging` to set force logging\. The `force_logging` procedure has the following parameters\. 


****  

| Parameter Name | Data Type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
| `p_enable` | boolean | true | optional |  Set to `true` to put the database in force logging mode, `false` to remove the database from force logging mode\.   | 

The following example puts the database in force logging mode\. 

```
1. exec rdsadmin.rdsadmin_util.force_logging(p_enable => true);
```

## Setting Supplemental Logging<a name="Appendix.Oracle.CommonDBATasks.AddingSupplementalLogging"></a>

Supplemental logging ensures that LogMiner and products that use LogMiner technology have sufficient information to support chained rows and storage arrangements such as cluster tables\. For more information, see [Supplemental Logging](https://docs.oracle.com/cd/E11882_01/server.112/e22490/logminer.htm#SUTIL1582) in the Oracle documentation\. 

Oracle Database doesn't enable supplemental logging by default\. You can use the Amazon RDS procedure `rdsadmin.rdsadmin_util.alter_supplemental_logging` to enable and disable supplemental logging\. For more information about how Amazon RDS manages the retention of archived redo logs for Oracle DB instances, see [Retaining Archived Redo Logs](#Appendix.Oracle.CommonDBATasks.RetainRedoLogs)\. 

The `alter_supplemental_logging` procedure has the following parameters\. 


****  

| Parameter Name | Data Type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
| `p_action` | varchar2 | — | required |  `'ADD'` to add supplemental logging, `'DROP'` to drop supplemental logging\.   | 
| `p_type` | varchar2 | null | optional |  The type of supplemental logging\. Valid values are `'ALL'`, `'FOREIGN KEY'`, `'PRIMARY KEY'`, or `'UNIQUE'`\.   | 

The following example enables supplemental logging: 

```
1. begin
2.     rdsadmin.rdsadmin_util.alter_supplemental_logging(
3.         p_action => 'ADD');
4. end;
5. /
```

The following example enables supplemental logging for all fixed\-length maximum size columns: 

```
1. begin
2.     rdsadmin.rdsadmin_util.alter_supplemental_logging(
3.         p_action => 'ADD',
4.         p_type   => 'ALL');
5. end;
6. /
```

The following example enables supplemental logging for primary key columns: 

```
1. begin
2.     rdsadmin.rdsadmin_util.alter_supplemental_logging(
3.         p_action => 'ADD',
4.         p_type   => 'PRIMARY KEY');
5. end;
6. /
```

## Switching Online Log Files<a name="Appendix.Oracle.CommonDBATasks.SwitchingLogfiles"></a>

You can use the Amazon RDS procedure `rdsadmin.rdsadmin_util.switch_logfile` to switch log files\. The `switch_logfile` procedure has no parameters\. 

The following example switches log files\.

```
1. exec rdsadmin.rdsadmin_util.switch_logfile;
```

## Adding Online Redo Logs<a name="Appendix.Oracle.CommonDBATasks.RedoLogs"></a>

An Amazon RDS DB instance running Oracle starts with four online redo logs, 128 MB each\. You can use the Amazon RDS procedure `rdsadmin.rdsadmin_util.add_logfile` to add additional redo logs\. 

For any version of Oracle, the `add_logfile` procedure has the following parameters\. 


****  

| Parameter Name | Data Type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
| `bytes` | positive | null | optional | The size of the log file in bytes\. | 

The `add_logfile` procedure has the following parameters\. 


****  

| Parameter Name | Data Type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
| `p_size` | varchar2 | — | required |  The size of the log file\. You can specify the size in kilobytes \(K\), megabytes \(M\), or gigabytes \(G\)\.   | 

The following command adds a 100 MB log file: 

```
1. exec rdsadmin.rdsadmin_util.add_logfile(p_size => '100M');
```

## Dropping Online Redo Logs<a name="Appendix.Oracle.CommonDBATasks.DroppingRedoLogs"></a>

You can use the Amazon RDS procedure `rdsadmin.rdsadmin_util.drop_logfile` to drop redo logs\. The `drop_logfile` procedure has the following parameters\. 


****  

| Parameter Name | Data Type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
| `grp` | positive | — | required | The group number of the log\. | 

The following example drops the log with group number 3: 

```
1. exec rdsadmin.rdsadmin_util.drop_logfile(grp => 3);
```

You can only drop logs that have a status of unused or inactive\. The following example gets the statuses of the logs: 

```
1. select GROUP#, STATUS from V$LOG;
2. 
3. GROUP#     STATUS
4. ---------- ----------------
5. 1          CURRENT
6. 2          INACTIVE
7. 3          INACTIVE
8. 4          UNUSED
```

## Resizing Online Redo Logs<a name="Appendix.Oracle.CommonDBATasks.ResizingRedoLogs"></a>

An Amazon RDS DB instance running Oracle starts with four online redo logs, 128 MB each\. The following example shows how you can use Amazon RDS procedures to resize your logs from 128 MB each to 512 MB each\. 

```
  1. /* Query V$LOG to see the logs.          */
  2. /* You start with 4 logs of 128 MB each. */
  3. 
  4. select GROUP#, BYTES, STATUS from V$LOG;
  5. 
  6. GROUP#     BYTES      STATUS
  7. ---------- ---------- ----------------
  8. 1          134217728  INACTIVE
  9. 2          134217728  CURRENT
 10. 3          134217728  INACTIVE
 11. 4          134217728  INACTIVE
 12. 
 13. 
 14. /* Add four new logs that are each 512 MB */
 15. 
 16. exec rdsadmin.rdsadmin_util.add_logfile(bytes => 536870912);
 17. exec rdsadmin.rdsadmin_util.add_logfile(bytes => 536870912);
 18. exec rdsadmin.rdsadmin_util.add_logfile(bytes => 536870912);
 19. exec rdsadmin.rdsadmin_util.add_logfile(bytes => 536870912);
 20. 
 21. 
 22. /* Query V$LOG to see the logs. */ 
 23. /* Now there are 8 logs.        */
 24. 
 25. select GROUP#, BYTES, STATUS from V$LOG;
 26. 
 27. GROUP#     BYTES      STATUS
 28. ---------- ---------- ----------------
 29. 1          134217728  INACTIVE
 30. 2          134217728  CURRENT
 31. 3          134217728  INACTIVE
 32. 4          134217728  INACTIVE
 33. 5          536870912  UNUSED
 34. 6          536870912  UNUSED
 35. 7          536870912  UNUSED
 36. 8          536870912  UNUSED
 37. 
 38. 
 39. /* Drop each inactive log using the group number. */
 40. 
 41. exec rdsadmin.rdsadmin_util.drop_logfile(grp => 1);
 42. exec rdsadmin.rdsadmin_util.drop_logfile(grp => 3);
 43. exec rdsadmin.rdsadmin_util.drop_logfile(grp => 4);
 44. 
 45. 
 46. /* Query V$LOG to see the logs. */ 
 47. /* Now there are 5 logs.        */
 48. 
 49. select GROUP#, BYTES, STATUS from V$LOG;
 50. 
 51. GROUP#     BYTES      STATUS
 52. ---------- ---------- ----------------
 53. 2          134217728  CURRENT
 54. 5          536870912  UNUSED
 55. 6          536870912  UNUSED
 56. 7          536870912  UNUSED
 57. 8          536870912  UNUSED
 58. 
 59. 
 60. /* Switch logs so that group 2 is no longer current. */
 61. 
 62. exec rdsadmin.rdsadmin_util.switch_logfile;
 63. 
 64. 
 65. /* Query V$LOG to see the logs.        */ 
 66. /* Now one of the new logs is current. */
 67. 
 68. SQL>select GROUP#, BYTES, STATUS from V$LOG;
 69. 
 70. GROUP#     BYTES      STATUS
 71. ---------- ---------- ----------------
 72. 2          134217728  ACTIVE
 73. 5          536870912  CURRENT
 74. 6          536870912  UNUSED
 75. 7          536870912  UNUSED
 76. 8          536870912  UNUSED
 77. 
 78. 
 79. /* Issue a checkpoint to clear log 2. */
 80. 
 81. exec rdsadmin.rdsadmin_util.checkpoint;
 82. 
 83. 
 84. /* Query V$LOG to see the logs.            */ 
 85. /* Now the final original log is inactive. */
 86. 
 87. select GROUP#, BYTES, STATUS from V$LOG;
 88. 
 89. GROUP#     BYTES      STATUS
 90. ---------- ---------- ----------------
 91. 2          134217728  INACTIVE
 92. 5          536870912  CURRENT
 93. 6          536870912  UNUSED
 94. 7          536870912  UNUSED
 95. 8          536870912  UNUSED
 96. 
 97. 
 98. # Drop the final inactive log.
 99. 
100. exec rdsadmin.rdsadmin_util.drop_logfile(grp => 2);
101. 
102. 
103. /* Query V$LOG to see the logs.    */ 
104. /* Now there are four 512 MB logs. */
105. 
106. select GROUP#, BYTES, STATUS from V$LOG;
107. 
108. GROUP#     BYTES      STATUS
109. ---------- ---------- ----------------
110. 5          536870912  CURRENT
111. 6          536870912  UNUSED
112. 7          536870912  UNUSED
113. 8          536870912  UNUSED
```

## Retaining Archived Redo Logs<a name="Appendix.Oracle.CommonDBATasks.RetainRedoLogs"></a>

You can retain archived redo logs locally on your DB instance for use with products like Oracle LogMiner \(DBMS\_LOGMNR\)\. After you have retained the redo logs, you can use LogMiner to analyze the logs\. For more information, see [Using LogMiner to Analyze Redo Log Files](http://docs.oracle.com/cd/E11882_01/server.112/e22490/logminer.htm) in the Oracle documentation\. 

You can use the Amazon RDS procedure `rdsadmin.rdsadmin_util.set_configuration` to retain archived redo logs\. The `set_configuration` procedure has the following parameters\. 


****  

| Parameter Name | Data Type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
| `name` | varchar | — | required | The name of the configuration to update\. | 
| `value` | varchar | — | required | The value for the configuration\. | 

The following example retains 24 hours of redo logs: 

```
1. begin
2.     rdsadmin.rdsadmin_util.set_configuration(
3.         name  => 'archivelog retention hours',
4.         value => '24');
5. end;
6. /
```

You can use the Amazon RDS procedure `rdsadmin.rdsadmin_util.show_configuration` to view how long archived redo logs are retained for your DB instance\.

The following example shows the log retention time:

```
set serveroutput on
exec rdsadmin.rdsadmin_util.show_configuration;
```

The output shows the current setting for `archivelog retention hours`\. The following output shows that archived redo logs are retained for 48 hours:

```
NAME:archivelog retention hours
VALUE:48
DESCRIPTION:ArchiveLog expiration specifies the duration in hours before archive/redo log files are automatically deleted.
```

Because the archived redo logs are retained on your DB instance, ensure that your DB instance has enough allocated storage for the retained logs\. To determine how much space your DB instance has used in the last X hours, you can run the following query, replacing X with the number of hours: 

```
1. select sum(BLOCKS * BLOCK_SIZE) bytes 
2.   from V$ARCHIVED_LOG
3.  where FIRST_TIME >= SYSDATE-(X/24) and DEST_ID=1;
```

Archived redo logs are only generated if the backup retention period of your DB instance is greater than zero\. By default the backup retention period is greater than zero, so unless you explicitly set yours to zero, archived redo logs are generated for your DB instance\. To modify the backup retention period for your DB instance, see [Modifying a DB Instance Running the Oracle Database Engine](USER_ModifyInstance.Oracle.md)\. 

After the archived redo logs are removed from your DB instance, you can't download them again to your DB instance\. Amazon RDS retains the archived redo logs outside of your DB instance to support restoring your DB instance to a point in time\. Amazon RDS retains the archived redo logs outside of your DB instance based on the backup retention period configured for your DB instance\. To modify the backup retention period for your DB instance, see [Modifying a DB Instance Running the Oracle Database Engine](USER_ModifyInstance.Oracle.md)\. 

**Note**  
 If you are using JDBC on Linux to download archived redo logs, and you experience long latency times and connection resets, it could be caused by the default random number generator setting on your Java client\. We recommend setting your JDBC drivers to use a non\-blocking random number generator\. 

## Accessing Transaction Logs<a name="Appendix.Oracle.CommonDBATasks.Log.Download"></a>

Accessing transaction logs is supported for Oracle version 11\.2\.0\.4\.v11 and later, and 12\.1\.0\.2\.v7 and later\.

You might want to access your online and archived redo log files for mining with external tools such as GoldenGate, Attunity, Informatica, and others\. If you want to access your online and archived redo log files, you must first create directory objects that provide read\-only access to the physical file paths\. 

The following code creates directories that provide read\-only access to your online and archived redo log files: 

**Important**  
This code also revokes the `DROP ANY DIRECTORY` privilege\.

```
1. exec rdsadmin.rdsadmin_master_util.create_archivelog_dir;
2. exec rdsadmin.rdsadmin_master_util.create_onlinelog_dir;
```

After you create directory objects for your online and archived redo log files, you can read the files by using PL/SQL\. For more information about reading files from directory objects, see [Listing Files in a DB Instance Directory](Appendix.Oracle.CommonDBATasks.Misc.md#Appendix.Oracle.CommonDBATasks.ListDirectories) and [Reading Files in a DB Instance Directory](Appendix.Oracle.CommonDBATasks.Misc.md#Appendix.Oracle.CommonDBATasks.ReadingFiles)\. 

The following code drops the directories for your online and archived redo log files: 

```
1. exec rdsadmin.rdsadmin_master_util.drop_archivelog_dir;
2. exec rdsadmin.rdsadmin_master_util.drop_onlinelog_dir;
```

The following code grants and revokes the `DROP ANY DIRECTORY` privilege:

```
1. exec rdsadmin.rdsadmin_master_util.revoke_drop_any_directory;
2. exec rdsadmin.rdsadmin_master_util.grant_drop_any_directory;
```

## Related Topics<a name="Appendix.Oracle.CommonDBATasks.Log.Related"></a>
+ [Common DBA System Tasks for Oracle DB Instances](Appendix.Oracle.CommonDBATasks.System.md)
+ [Common DBA Database Tasks for Oracle DB Instances](Appendix.Oracle.CommonDBATasks.Database.md)
+ [Common DBA Miscellaneous Tasks for Oracle DB Instances](Appendix.Oracle.CommonDBATasks.Misc.md)