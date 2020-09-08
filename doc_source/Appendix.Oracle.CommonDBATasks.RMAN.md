# Common DBA Recovery Manager \(RMAN\) Tasks for Oracle DB Instances<a name="Appendix.Oracle.CommonDBATasks.RMAN"></a>

In the following section, you can find how you can perform Oracle Recovery Manager \(RMAN\) DBA tasks on your Amazon RDS DB instances running Oracle\. To deliver a managed service experience, Amazon RDS doesn't provide shell access to DB instances\. It also restricts access to certain system procedures and tables that require advanced privileges\. 

You can use the Amazon RDS package `rdsadmin.rdsadmin_rman_util` to perform RMAN backups of your Amazon RDS for Oracle database to disk\. The `rdsadmin.rdsadmin_rman_util` package supports full and incremental database file backups, tablespace backups, and archive log backups\. 

RMAN backups consume storage space on the Amazon RDS DB instance host\. When you perform a backup, you specify an Oracle directory object as a parameter in the procedure call\. The backup files are placed in the specified directory\. You can use default directories, such as `DATA_PUMP_DIR`, or create a new directory\. For more information, see [Creating and Dropping Directories in the Main Data Storage Space](Appendix.Oracle.CommonDBATasks.Misc.md#Appendix.Oracle.CommonDBATasks.NewDirectories)\.

After an RMAN backup has finished, you can copy the backup files off the Amazon RDS for Oracle DB instance host\. You might do this for the purpose of restoring to a non\-RDS host or for long\-term storage of backups\. For example, you can copy the backup files to an Amazon S3 bucket\. For more information, see using [Amazon S3 Integration](oracle-s3-integration.md)\.

The backup files for RMAN backups remain on the Amazon RDS DB instance host until you remove them manually\. You can use the `UTL_FILE.FREMOVE` Oracle procedure to remove files from a directory\. For more information, see [FREMOVE Procedure](https://docs.oracle.com/database/121/ARPLS/u_file.htm#ARPLS70924) in the Oracle documentation\.

When backing up archived redo logs or performing a full or incremental backup that includes archived redo logs, redo log retention must be set to a nonzero value\. For more information, see [Retaining Archived Redo Logs](Appendix.Oracle.CommonDBATasks.Log.md#Appendix.Oracle.CommonDBATasks.RetainRedoLogs)\. 

**Note**  
For backing up and restoring to another Amazon RDS for Oracle DB instance, you can continue to use the Amazon RDS backup and restore features\. For more information, see [Backing Up and Restoring an Amazon RDS DB Instance](CHAP_CommonTasks.BackupRestore.md)  
Currently, RMAN restore isn't supported for Amazon RDS for Oracle DB instances\.

**Topics**
+ [Common Parameters for RMAN Procedures](#Appendix.Oracle.CommonDBATasks.CommonParameters)
+ [Validating DB Instance Files](#Appendix.Oracle.CommonDBATasks.ValidateDBFiles)
+ [Enabling and Disabling Block Change Tracking](#Appendix.Oracle.CommonDBATasks.BlockChangeTracking)
+ [Crosschecking Archived Redo Logs](#Appendix.Oracle.CommonDBATasks.Crosscheck)
+ [Backing Up Archived Redo Logs](#Appendix.Oracle.CommonDBATasks.BackupArchivedLogs)
+ [Performing a Full Database Backup](#Appendix.Oracle.CommonDBATasks.BackupDatabaseFull)
+ [Performing an Incremental Database Backup](#Appendix.Oracle.CommonDBATasks.BackupDatabaseIncremental)
+ [Performing a Tablespace Backup](#Appendix.Oracle.CommonDBATasks.BackupTablespace)

## Common Parameters for RMAN Procedures<a name="Appendix.Oracle.CommonDBATasks.CommonParameters"></a>

You can use procedures in the Amazon RDS package `rdsadmin.rdsadmin_rman_util` to perform tasks with RMAN\. Several parameters are common to the procedures in the package\. The package has the following common parameters\.


****  

| Parameter Name | Data Type | Valid Values | Default | Required | Description | 
| --- | --- | --- | --- | --- | --- | 
| `p_owner` | varchar2 | A valid owner of the directory specified in `p_directory_name`\. | — | Yes |  The owner of the directory to contain the backup files\.  | 
| `p_directory_name` | varchar2 | A valid database directory name\. | – | Yes |  The name of the directory to contain the backup files\.  | 
| `p_label` | varchar2 | `a-z`, `A-Z`, `0-9`, `'_'`, `'-'`, `'.'` | — | No |  A unique string that is included in the backup file names\.  The limit is 30 characters\.   | 
| `p_compress` | boolean | `TRUE`, `FALSE` | `FALSE` | No |  Specify `TRUE` to enable BASIC backup compression\. Specify `FALSE` to disable BASIC backup compression\.  | 
| `p_include_archive_logs` | boolean | `TRUE`, `FALSE` | `FALSE` | No |  Specify `TRUE` to include archived redo logs in the backup\. Specify `FALSE` to exclude archived redo logs from the backup\. If you include archived redo logs in the backup, set retention to one hour or greater using the `rdsadmin.rdsadmin_util.set_configuration` procedure\. Also, call the `rdsadmin.rdsadmin_rman_util.crosscheck_archivelog` procedure immediately before running the backup\. Otherwise, the backup might fail due to missing archived redo log files that have been deleted by Amazon RDS management procedures\.  | 
| `p_include_controlfile` | boolean | `TRUE`, `FALSE` | `FALSE` | No |  Specify `TRUE` to include the control file in the backup\. Specify `FALSE` to exclude the control file from the backup\.  | 
| `p_optimize` | boolean | `TRUE`, `FALSE` | `TRUE` | No |  Specify `TRUE` to enable backup optimization, if archived redo logs are included, to reduce backup size\. Specify `FALSE` to disable backup optimization\.  | 
| `p_parallel` | number | A valid integer between `1` and `254` for Oracle Database Enterprise Edition \(EE\) `1` for other Oracle Database editions | `1` | No | Number of channels\. | 
| `p_rman_to_dbms_output` | boolean | `TRUE`, `FALSE` | `FALSE` | No | When `TRUE`, the RMAN output is sent to the `DBMS_OUTPUT` package in addition to a file in the `BDUMP` directory\. In SQL\*Plus, use `SET SERVEROUTPUT ON` to see the output\. When `FALSE`, the RMAN output is only sent to a file in the `BDUMP` directory\.  | 
| `p_section_size_mb` | number | A valid integer | `NULL` | No | The section size in megabytes \(MB\)\. Validates in parallel by dividing each file into the specified section size\. When `NULL`, the parameter is ignored\. | 
| `p_validation_type` | varchar2 | `'PHYSICAL'`, `'PHYSICAL+LOGICAL'` | `'PHYSICAL'` | No | The level of corruption detection\. Specify `'PHYSICAL'` to check for physical corruption\. An example of physical corruption is a block with a mismatch in the header and footer\. Specify `'PHYSICAL+LOGICAL'` to check for logical inconsistencies in addition to physical corruption\. An example of logical corruption is a corrupt block\.  | 

## Validating DB Instance Files<a name="Appendix.Oracle.CommonDBATasks.ValidateDBFiles"></a>

You can use the Amazon RDS package `rdsadmin.rdsadmin_rman_util` to validate Amazon RDS for Oracle DB instance files, such as data files, tablespaces, control files, or server parameter files \(SPFILEs\)\.

For more information about RMAN validation, see [ Validating Database Files and Backups](https://docs.oracle.com/database/121/BRADV/rcmvalid.htm#BRADV90063) and [ VALIDATE](https://docs.oracle.com/database/121/RCMRF/rcmsynta2025.htm#RCMRF162) in the Oracle documentation\.

**Topics**
+ [Validating a DB Instance](#Appendix.Oracle.CommonDBATasks.ValidateDB)
+ [Validating a Tablespace](#Appendix.Oracle.CommonDBATasks.ValidateTablespace)
+ [Validating a Control File](#Appendix.Oracle.CommonDBATasks.ValidateControlFile)
+ [Validating an SPFILE](#Appendix.Oracle.CommonDBATasks.ValidateSpfile)
+ [Validating a Data File](#Appendix.Oracle.CommonDBATasks.ValidateDataFile)

### Validating a DB Instance<a name="Appendix.Oracle.CommonDBATasks.ValidateDB"></a>

To validate all of the relevant files used by an Amazon RDS Oracle DB instance, use the Amazon RDS procedure `rdsadmin.rdsadmin_rman_util.validate_database`\. 

This procedure uses the following common parameters for RMAN tasks:
+ `p_validation_type`
+ `p_parallel`
+ `p_section_size_mb`
+ `p_rman_to_dbms_output`

For more information, see [Common Parameters for RMAN Procedures](#Appendix.Oracle.CommonDBATasks.CommonParameters)\.

The following example validates the DB instance using the default values for the parameters\.

```
exec rdsadmin.rdsadmin_rman_util.validate_database;
```

The following example validates the DB instance using the specified values for the parameters\.

```
BEGIN
    rdsadmin.rdsadmin_rman_util.validate_database(
        p_validation_type     => 'PHYSICAL+LOGICAL', 
        p_parallel            => 4,  
        p_section_size_mb     => 10,
        p_rman_to_dbms_output => FALSE);
END;
/
```

When the `p_rman_to_dbms_output` parameter is set to `FALSE`, the RMAN output is written to a file in the `BDUMP` directory\.

To view the files in the `BDUMP` directory, run the following `SELECT` statement\.

```
SELECT * FROM table(rdsadmin.rds_file_util.listdir('BDUMP')) order by mtime;
```

To view the contents of a file in the `BDUMP` directory, run the following `SELECT` statement\.

```
SELECT text FROM table(rdsadmin.rds_file_util.read_text_file('BDUMP','rds-rman-validate-nnn.txt'));
```

Replace the file name with the name of the file you want to view\.

### Validating a Tablespace<a name="Appendix.Oracle.CommonDBATasks.ValidateTablespace"></a>

To validate the files associated with a tablespace, use the Amazon RDS procedure `rdsadmin.rdsadmin_rman_util.validate_tablespace`\. 

This procedure uses the following common parameters for RMAN tasks:
+ `p_validation_type`
+ `p_parallel`
+ `p_section_size_mb`
+ `p_rman_to_dbms_output`

For more information, see [Common Parameters for RMAN Procedures](#Appendix.Oracle.CommonDBATasks.CommonParameters)\.

This procedure also uses the following additional parameter\.


****  

| Parameter Name | Data Type | Valid Values | Default | Required | Description | 
| --- | --- | --- | --- | --- | --- | 
| `p_tablespace_name` | varchar2 | A valid tablespace name | — | Yes | The name of the tablespace\. | 

### Validating a Control File<a name="Appendix.Oracle.CommonDBATasks.ValidateControlFile"></a>

To validate only the control file used by an Amazon RDS Oracle DB instance, use the Amazon RDS procedure `rdsadmin.rdsadmin_rman_util.validate_current_controlfile`\. 

This procedure uses the following common parameter for RMAN tasks:
+ `p_validation_type`
+ `p_rman_to_dbms_output`

For more information, see [Common Parameters for RMAN Procedures](#Appendix.Oracle.CommonDBATasks.CommonParameters)\.

### Validating an SPFILE<a name="Appendix.Oracle.CommonDBATasks.ValidateSpfile"></a>

To validate only the server parameter file \(SPFILE\) used by an Amazon RDS Oracle DB instance, use the Amazon RDS procedure `rdsadmin.rdsadmin_rman_util.validate_spfile`\. 

This procedure uses the following common parameter for RMAN tasks:
+ `p_validation_type`
+ `p_rman_to_dbms_output`

For more information, see [Common Parameters for RMAN Procedures](#Appendix.Oracle.CommonDBATasks.CommonParameters)\.

### Validating a Data File<a name="Appendix.Oracle.CommonDBATasks.ValidateDataFile"></a>

To validate a data file, use the Amazon RDS procedure `rdsadmin.rdsadmin_rman_util.validate_datafile`\. 

This procedure uses the following common parameters for RMAN tasks:
+ `p_validation_type`
+ `p_parallel`
+ `p_section_size_mb`
+ `p_rman_to_dbms_output`

For more information, see [Common Parameters for RMAN Procedures](#Appendix.Oracle.CommonDBATasks.CommonParameters)\.

This procedure also uses the following additional parameters\.


****  

| Parameter Name | Data Type | Valid Values | Default | Required | Description | 
| --- | --- | --- | --- | --- | --- | 
| `p_datafile` | varchar2 | A valid datafile ID number or a valid datafile name including complete path | — | Yes | The datafile ID number \(from `v$datafile.file#`\) or the full datafile name including the path \(from `v$datafile.name`\)\. | 
| `p_from_block` | number | A valid integer | `NULL` | No | The number of the block where the validation starts within the data file\. When this is `NULL`, `1` is used\. | 
| `p_to_block` | number | A valid integer | `NULL` | No | The number of the block where the validation ends within the data file\. When this is `NULL`, the maximum block in the data file is used\. | 

## Enabling and Disabling Block Change Tracking<a name="Appendix.Oracle.CommonDBATasks.BlockChangeTracking"></a>

You can enable block change tracking for a DB instance using the Amazon RDS procedure `rdsadmin.rdsadmin_rman_util.enable_block_change_tracking`\.

You can disable block change tracking for a DB instance using the Amazon RDS procedure `rdsadmin.rdsadmin_rman_util.disable_block_change_tracking`\.

These procedures take no parameters\. Enabling block change tracking can improve the performance of incremental backups\.

These procedures are supported for the following Amazon RDS for Oracle DB engine versions:
+ 11\.2\.0\.4\.v19 or higher 11\.2 versions
+ 12\.1\.0\.2\.v15 or higher 12\.1 versions
+ 12\.2\.0\.1\.ru\-2019\-01\.rur\-2019\-01\.r1 or higher 12\.2 versions
+ All 18\.0\.0\.0 versions
+ All 19\.0\.0\.0 versions

To determine whether block change tracking is enabled for your DB instance, run the following query\.

```
SELECT status, filename FROM V$BLOCK_CHANGE_TRACKING;
```

The following example enables block change tracking for a DB instance\.

```
exec rdsadmin.rdsadmin_rman_util.enable_block_change_tracking;
```

The following example disables block change tracking for a DB instance\.

```
exec rdsadmin.rdsadmin_rman_util.disable_block_change_tracking;
```

## Crosschecking Archived Redo Logs<a name="Appendix.Oracle.CommonDBATasks.Crosscheck"></a>

You can crosscheck archived redo logs using the Amazon RDS procedure `rdsadmin.rdsadmin_rman_util.crosscheck_archivelog`\.

You can use this procedure to crosscheck the archived redo logs registered in the control file and optionally delete the expired logs records\. When RMAN makes a backup, it creates a record in the control file\. Over time, these records increase the size of the control file\. We recommend that you remove expired records periodically\.

**Note**  
Standard Amazon RDS backups don't use RMAN and therefore don't create records in the control file\.

This procedure uses the common parameter `p_rman_to_dbms_output` for RMAN tasks\.

For more information, see [Common Parameters for RMAN Procedures](#Appendix.Oracle.CommonDBATasks.CommonParameters)\.

This procedure also uses the following additional parameter\.


****  

| Parameter Name | Data Type | Valid Values | Default | Required | Description | 
| --- | --- | --- | --- | --- | --- | 
| `p_delete_expired` | boolean | `TRUE`, `FALSE` | `TRUE` | No | When `TRUE`, delete expired archived redo log records from the control file\. When `FALSE`, retain the expired archived redo log records in the control file\.  | 

This procedure is supported for the following Amazon RDS for Oracle DB engine versions:
+ 11\.2\.0\.4\.v19 or higher 11\.2 versions
+ 12\.1\.0\.2\.v15 or higher 12\.1 versions
+ 12\.2\.0\.1\.ru\-2019\-01\.rur\-2019\-01\.r1 or higher 12\.2 versions
+ All 18\.0\.0\.0 versions
+ All 19\.0\.0\.0 versions

The following example marks archived redo log records in the control file as expired, but does not delete the records\.

```
BEGIN
    rdsadmin.rdsadmin_rman_util.crosscheck_archivelog(
        p_delete_expired      => FALSE,  
        p_rman_to_dbms_output => FALSE);
END;
/
```

The following example deletes expired archived redo log records from the control file\.

```
BEGIN
    rdsadmin.rdsadmin_rman_util.crosscheck_archivelog(
        p_delete_expired      => TRUE,  
        p_rman_to_dbms_output => FALSE);
END;
/
```

## Backing Up Archived Redo Logs<a name="Appendix.Oracle.CommonDBATasks.BackupArchivedLogs"></a>

You can use the Amazon RDS package `rdsadmin.rdsadmin_rman_util` to back up archived redo logs for an Amazon RDS Oracle DB instance\.

The procedures for backing up archived redo logs are supported for the following Amazon RDS for Oracle DB engine versions:
+ 11\.2\.0\.4\.v19 or higher 11\.2 versions
+ 12\.1\.0\.2\.v15 or higher 12\.1 versions
+ 12\.2\.0\.1\.ru\-2019\-01\.rur\-2019\-01\.r1 or higher 12\.2 versions
+ All 18\.0\.0\.0 versions
+ All 19\.0\.0\.0 versions

**Topics**
+ [Backing Up All Archived Redo Logs](#Appendix.Oracle.CommonDBATasks.BackupArchivedLogs.All)
+ [Backing Up an Archived Redo Log from a Date Range](#Appendix.Oracle.CommonDBATasks.BackupArchivedLogs.Date)
+ [Backing Up an Archived Redo Log from an SCN Range](#Appendix.Oracle.CommonDBATasks.BackupArchivedLogs.SCN)
+ [Backing Up an Archived Redo Log from a Sequence Number Range](#Appendix.Oracle.CommonDBATasks.BackupArchivedLogs.Sequence)

### Backing Up All Archived Redo Logs<a name="Appendix.Oracle.CommonDBATasks.BackupArchivedLogs.All"></a>

To back up all of the archived redo logs for an Amazon RDS Oracle DB instance, use the Amazon RDS procedure `rdsadmin.rdsadmin_rman_util.backup_archivelog_all`\. 

This procedure uses the following common parameters for RMAN tasks:
+ `p_owner`
+ `p_directory_name`
+ `p_label`
+ `p_parallel`
+ `p_compress`
+ `p_rman_to_dbms_output`

For more information, see [Common Parameters for RMAN Procedures](#Appendix.Oracle.CommonDBATasks.CommonParameters)\.

The following example backs up all archived redo logs for the DB instance\.

```
BEGIN
    rdsadmin.rdsadmin_rman_util.backup_archivelog_all(
        p_owner               => 'SYS', 
        p_directory_name      => 'MYDIRECTORY',
        p_parallel            => 4,  
        p_rman_to_dbms_output => FALSE);
END;
/
```

### Backing Up an Archived Redo Log from a Date Range<a name="Appendix.Oracle.CommonDBATasks.BackupArchivedLogs.Date"></a>

To back up specific archived redo logs for an Amazon RDS Oracle DB instance by specifying a date range, use the Amazon RDS procedure `rdsadmin.rdsadmin_rman_util.backup_archivelog_date`\. The date range specifies which archived redo logs to back up\. 

This procedure uses the following common parameters for RMAN tasks:
+ `p_owner`
+ `p_directory_name`
+ `p_label`
+ `p_parallel`
+ `p_compress`
+ `p_rman_to_dbms_output`

For more information, see [Common Parameters for RMAN Procedures](#Appendix.Oracle.CommonDBATasks.CommonParameters)\.

This procedure also uses the following additional parameters\.


****  

| Parameter Name | Data Type | Valid Values | Default | Required | Description | 
| --- | --- | --- | --- | --- | --- | 
| `p_from_date` | date | A date that is between the `start_date` and `next_date` of an archived redo log that exists on disk\. The value must be less than or equal to the value specified for `p_to_date`\. | — | Yes |  The starting date for the archived log backups\.  | 
| `p_to_date` | date | A date that is between the `start_date` and `next_date` of an archived redo log that exists on disk\. The value must be greater than or equal to the value specified for `p_from_date`\. | — | Yes |  The ending date for the archived log backups\.  | 

The following example backs up archived redo logs in the date range for the DB instance\.

```
BEGIN
    rdsadmin.rdsadmin_rman_util.backup_archivelog_date(
        p_owner               => 'SYS', 
        p_directory_name      => 'MYDIRECTORY',
        p_from_date           => '03/01/2019 00:00:00',
        p_to_date             => '03/02/2019 00:00:00',
        p_parallel            => 4,  
        p_rman_to_dbms_output => FALSE);
END;
/
```

### Backing Up an Archived Redo Log from an SCN Range<a name="Appendix.Oracle.CommonDBATasks.BackupArchivedLogs.SCN"></a>

To back up specific archived redo logs for an Amazon RDS Oracle DB instance by specifying a system change number \(SCN\) range, use the Amazon RDS procedure `rdsadmin.rdsadmin_rman_util.backup_archivelog_scn`\. The SCN range specifies which archived redo logs to back up\. 

This procedure uses the following common parameters for RMAN tasks:
+ `p_owner`
+ `p_directory_name`
+ `p_label`
+ `p_parallel`
+ `p_compress`
+ `p_rman_to_dbms_output`

For more information, see [Common Parameters for RMAN Procedures](#Appendix.Oracle.CommonDBATasks.CommonParameters)\.

This procedure also uses the following additional parameters\.


****  

| Parameter Name | Data Type | Valid Values | Default | Required | Description | 
| --- | --- | --- | --- | --- | --- | 
| `p_from_scn` | number | An SCN of an archived redo log that exists on disk\. The value must be less than or equal to the value specified for `p_to_scn`\. | — | Yes |  The starting SCN for the archived log backups\.  | 
| `p_to_scn` | number | An SCN of an archived redo log that exists on disk\. The value must be greater than or equal to the value specified for `p_from_scn`\. | — | Yes |  The ending SCN for the archived log backups\.  | 

The following example backs up archived redo logs in the SCN range for the DB instance\.

```
BEGIN
    rdsadmin.rdsadmin_rman_util.backup_archivelog_scn(
        p_owner               => 'SYS', 
        p_directory_name      => 'MYDIRECTORY',
        p_from_scn            => 1533835,
        p_to_scn              => 1892447,
        p_parallel            => 4,  
        p_rman_to_dbms_output => FALSE);
END;
/
```

### Backing Up an Archived Redo Log from a Sequence Number Range<a name="Appendix.Oracle.CommonDBATasks.BackupArchivedLogs.Sequence"></a>

To back up specific archived redo logs for an Amazon RDS Oracle DB instance by specifying a sequence number range, use the Amazon RDS procedure `rdsadmin.rdsadmin_rman_util.backup_archivelog_sequence`\. The sequence number range specifies which archived redo logs to back up\. 

This procedure uses the following common parameters for RMAN tasks:
+ `p_owner`
+ `p_directory_name`
+ `p_label`
+ `p_parallel`
+ `p_compress`
+ `p_rman_to_dbms_output`

For more information, see [Common Parameters for RMAN Procedures](#Appendix.Oracle.CommonDBATasks.CommonParameters)\.

This procedure also uses the following additional parameters\.


****  

| Parameter Name | Data Type | Valid Values | Default | Required | Description | 
| --- | --- | --- | --- | --- | --- | 
| `p_from_sequence` | number | A sequence number an archived redo log that exists on disk\. The value must be less than or equal to the value specified for `p_to_sequence`\. | — | Yes |  The starting sequence number for the archived log backups\.  | 
| `p_to_sequence` | number | A sequence number of an archived redo log that exists on disk\. The value must be greater than or equal to the value specified for `p_from_sequence`\. | — | Yes |  The ending sequence number for the archived log backups\.  | 

The following example backs up archived redo logs in the sequence number range for the DB instance\.

```
BEGIN
    rdsadmin.rdsadmin_rman_util.backup_archivelog_sequence(
        p_owner               => 'SYS', 
        p_directory_name      => 'MYDIRECTORY',
        p_from_sequence       => 11160,
        p_to_sequence         => 11160,
        p_parallel            => 4,  
        p_rman_to_dbms_output => FALSE);
END;
/
```

## Performing a Full Database Backup<a name="Appendix.Oracle.CommonDBATasks.BackupDatabaseFull"></a>

You can perform a backup of all blocks of data files included in the backup using Amazon RDS procedure `rdsadmin.rdsadmin_rman_util.backup_database_full`\.

This procedure uses the following common parameters for RMAN tasks:
+ `p_owner`
+ `p_directory_name`
+ `p_label`
+ `p_parallel`
+ `p_section_size_mb`
+ `p_include_archive_logs`
+ `p_optimize`
+ `p_compress`
+ `p_rman_to_dbms_output`

For more information, see [Common Parameters for RMAN Procedures](#Appendix.Oracle.CommonDBATasks.CommonParameters)\.

This procedure is supported for the following Amazon RDS for Oracle DB engine versions:
+ 11\.2\.0\.4\.v19 or higher 11\.2 versions
+ 12\.1\.0\.2\.v15 or higher 12\.1 versions
+ 12\.2\.0\.1\.ru\-2019\-01\.rur\-2019\-01\.r1 or higher 12\.2 versions
+ All 18\.0\.0\.0 versions
+ All 19\.0\.0\.0 versions

The following example performs a full backup of the DB instance using the specified values for the parameters\.

```
BEGIN
    rdsadmin.rdsadmin_rman_util.backup_database_full(
        p_owner               => 'SYS', 
        p_directory_name      => 'MYDIRECTORY',
        p_parallel            => 4,  
        p_section_size_mb     => 10,
        p_rman_to_dbms_output => FALSE);
END;
/
```

## Performing an Incremental Database Backup<a name="Appendix.Oracle.CommonDBATasks.BackupDatabaseIncremental"></a>

You can perform an incremental backup of your DB instance using the Amazon RDS procedure `rdsadmin.rdsadmin_rman_util.backup_database_incremental`\.

For more information about incremental backups, see [Incremental Backups](https://docs.oracle.com/database/121/RCMRF/rcmsynta006.htm#GUID-73642FF2-43C5-48B2-9969-99001C52EB50__BGBHABHH) in the Oracle documentation\.

This procedure uses the following common parameters for RMAN tasks:
+ `p_owner`
+ `p_directory_name`
+ `p_label`
+ `p_parallel`
+ `p_section_size_mb`
+ `p_include_archive_logs`
+ `p_include_controlfile`
+ `p_optimize`
+ `p_compress`
+ `p_rman_to_dbms_output`

For more information, see [Common Parameters for RMAN Procedures](#Appendix.Oracle.CommonDBATasks.CommonParameters)\.

This procedure is supported for the following Amazon RDS for Oracle DB engine versions:
+ 11\.2\.0\.4\.v19 or higher 11\.2 versions
+ 12\.1\.0\.2\.v15 or higher 12\.1 versions
+ 12\.2\.0\.1\.ru\-2019\-01\.rur\-2019\-01\.r1 or higher 12\.2 versions
+ All 18\.0\.0\.0 versions
+ All 19\.0\.0\.0 versions

This procedure also uses the following additional parameter\.


****  

| Parameter Name | Data Type | Valid Values | Default | Required | Description | 
| --- | --- | --- | --- | --- | --- | 
| `p_level` | number | `0`, `1` | `0` | No |  Specify `0` to enable a full incremental backup\. Specify `1` to enable a non\-cumulative incremental backup\.  | 

The following example performs an incremental backup of the DB instance using the specified values for the parameters\.

```
BEGIN
    rdsadmin.rdsadmin_rman_util.backup_database_incremental(
        p_owner               => 'SYS', 
        p_directory_name      => 'MYDIRECTORY',
        p_level               => 1,
        p_parallel            => 4,  
        p_section_size_mb     => 10,
        p_rman_to_dbms_output => FALSE);
END;
/
```

## Performing a Tablespace Backup<a name="Appendix.Oracle.CommonDBATasks.BackupTablespace"></a>

You can perform a DB instance tablespace using the Amazon RDS procedure `rdsadmin.rdsadmin_rman_util.backup_tablespace`\.

This procedure uses the following common parameters for RMAN tasks:
+ `p_owner`
+ `p_directory_name`
+ `p_label`
+ `p_parallel`
+ `p_section_size_mb`
+ `p_include_archive_logs`
+ `p_include_controlfile`
+ `p_optimize`
+ `p_compress`
+ `p_rman_to_dbms_output`

For more information, see [Common Parameters for RMAN Procedures](#Appendix.Oracle.CommonDBATasks.CommonParameters)\.

This procedure also uses the following additional parameter\.


****  

| Parameter Name | Data Type | Valid Values | Default | Required | Description | 
| --- | --- | --- | --- | --- | --- | 
| `p_tablespace_name` | varchar2 | A valid tablespace name\. | — | Yes |  The name of the tablespace to back up\.  | 

This procedure is supported for the following Amazon RDS for Oracle DB engine versions:
+ 11\.2\.0\.4\.v19 or higher 11\.2 versions
+ 12\.1\.0\.2\.v15 or higher 12\.1 versions
+ 12\.2\.0\.1\.ru\-2019\-01\.rur\-2019\-01\.r1 or higher 12\.2 versions
+ All 18\.0\.0\.0 versions
+ All 19\.0\.0\.0 versions

The following example performs a tablespace backup using the specified values for the parameters\.

```
BEGIN
    rdsadmin.rdsadmin_rman_util.backup_tablespace(
        p_owner               => 'SYS', 
        p_directory_name      => 'MYDIRECTORY',
        p_tablespace_name     => MYTABLESPACE,
        p_parallel            => 4,  
        p_section_size_mb     => 10,
        p_rman_to_dbms_output => FALSE);
END;
/
```