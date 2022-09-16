# Performing common database tasks for Oracle DB instances<a name="Appendix.Oracle.CommonDBATasks.Database"></a>

Following, you can find how to perform certain common DBA tasks related to databases on your Amazon RDS DB instances running Oracle\. To deliver a managed service experience, Amazon RDS doesn't provide shell access to DB instances\. Amazon RDS also restricts access to some system procedures and tables that require advanced privileges\. 

**Topics**
+ [Changing the global name of a database](#Appendix.Oracle.CommonDBATasks.RenamingGlobalName)
+ [Creating and sizing tablespaces](#Appendix.Oracle.CommonDBATasks.CreatingTablespacesAndDatafiles)
+ [Setting the default tablespace](#Appendix.Oracle.CommonDBATasks.SettingDefaultTablespace)
+ [Setting the default temporary tablespace](#Appendix.Oracle.CommonDBATasks.SettingDefTempTablespace)
+ [Creating a temporary tablespace on the instance store](#Appendix.Oracle.CommonDBATasks.creating-tts-instance-store)
+ [Adding a tempfile to the instance store on a read replica](#Appendix.Oracle.CommonDBATasks.adding-tempfile-replica)
+ [Dropping tempfiles on a read replica](#Appendix.Oracle.CommonDBATasks.dropping-tempfiles-replica)
+ [Checkpointing a database](#Appendix.Oracle.CommonDBATasks.CheckpointingDatabase)
+ [Setting distributed recovery](#Appendix.Oracle.CommonDBATasks.SettingDistributedRecovery)
+ [Setting the database time zone](#Appendix.Oracle.CommonDBATasks.TimeZoneSupport)
+ [Working with Oracle external tables](#Appendix.Oracle.CommonDBATasks.External_Tables)
+ [Generating performance reports with Automatic Workload Repository \(AWR\)](#Appendix.Oracle.CommonDBATasks.AWR)
+ [Adjusting database links for use with DB instances in a VPC](#Appendix.Oracle.CommonDBATasks.DBLinks)
+ [Setting the default edition for a DB instance](#Appendix.Oracle.CommonDBATasks.DefaultEdition)
+ [Enabling auditing for the SYS\.AUD$ table](#Appendix.Oracle.CommonDBATasks.EnablingAuditing)
+ [Disabling auditing for the SYS\.AUD$ table](#Appendix.Oracle.CommonDBATasks.DisablingAuditing)
+ [Cleaning up interrupted online index builds](#Appendix.Oracle.CommonDBATasks.CleanupIndex)
+ [Skipping corrupt blocks](#Appendix.Oracle.CommonDBATasks.SkippingCorruptBlocks)
+ [Resizing the temporary tablespace in a read replica](#Appendix.Oracle.CommonDBATasks.ResizeTempSpaceReadReplica)
+ [Purging the recycle bin](#Appendix.Oracle.CommonDBATasks.PurgeRecycleBin)
+ [Setting the Data Redaction policy for full redaction](#Appendix.Oracle.CommonDBATasks.FullRedaction)

## Changing the global name of a database<a name="Appendix.Oracle.CommonDBATasks.RenamingGlobalName"></a>

To change the global name of a database, use the Amazon RDS procedure `rdsadmin.rdsadmin_util.rename_global_name`\. The `rename_global_name` procedure has the following parameters\. 


****  

| Parameter name | Data type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
|  `p_new_global_name`  |  varchar2  |  —  |  Yes  |  The new global name for the database\.  | 

The database must be open for the name change to occur\. For more information about changing the global name of a database, see [ALTER DATABASE](http://docs.oracle.com/cd/E11882_01/server.112/e41084/statements_1004.htm#SQLRF52547) in the Oracle documentation\. 

The following example changes the global name of a database to `new_global_name`\.

```
EXEC rdsadmin.rdsadmin_util.rename_global_name(p_new_global_name => 'new_global_name');
```

## Creating and sizing tablespaces<a name="Appendix.Oracle.CommonDBATasks.CreatingTablespacesAndDatafiles"></a>

Amazon RDS only supports Oracle Managed Files \(OMF\) for data files, log files, and control files\. When you create data files and log files, you can't specify the physical file names\. 

By default, tablespaces are created with auto\-extend enabled, and no maximum size\. Because of these default settings, tablespaces can grow to consume all allocated storage\. We recommend that you specify an appropriate maximum size on permanent and temporary tablespaces, and that you carefully monitor space usage\. 

The following example creates a tablespace named `users2` with a starting size of 1 gigabyte and a maximum size of 10 gigabytes: 

```
CREATE TABLESPACE users2 DATAFILE SIZE 1G AUTOEXTEND ON MAXSIZE 10G;
```

The following example creates temporary tablespace named `temp01`:

```
CREATE TEMPORARY TABLESPACE temp01;
```

 We recommend that you don't use smallfile tablespaces because you can't resize smallfile tablespaces with Amazon RDS for Oracle\. However, you can add a datafile to a smallfile tablespace\. 

You can resize a bigfile tablespace by using `ALTER TABLESPACE`\. You can specify the size in kilobytes \(K\), megabytes \(M\), gigabytes \(G\), or terabytes \(T\)\. 

The following example resizes a bigfile tablespace named `users2` to 200 MB\.

```
ALTER TABLESPACE users2 RESIZE 200M;
```

The following example adds an additional datafile to a smallfile tablespace named **users2**\. 

```
ALTER TABLESPACE users2 ADD DATAFILE SIZE 100000M AUTOEXTEND ON NEXT 250m MAXSIZE UNLIMITED;
```

## Setting the default tablespace<a name="Appendix.Oracle.CommonDBATasks.SettingDefaultTablespace"></a>

To set the default tablespace, use the Amazon RDS procedure `rdsadmin.rdsadmin_util.alter_default_tablespace`\. The `alter_default_tablespace` procedure has the following parameters\. 


****  

| Parameter name | Data type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
|  `tablespace_name`  |  varchar  |  —  |  Yes  |  The name of the default tablespace\.  | 

The following example sets the default tablespace to *users2*: 

```
EXEC rdsadmin.rdsadmin_util.alter_default_tablespace(tablespace_name => 'users2');
```

## Setting the default temporary tablespace<a name="Appendix.Oracle.CommonDBATasks.SettingDefTempTablespace"></a>

To set the default temporary tablespace, use the Amazon RDS procedure `rdsadmin.rdsadmin_util.alter_default_temp_tablespace`\. The `alter_default_temp_tablespace` procedure has the following parameters\. 


****  

| Parameter name | Data type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
|  `tablespace_name`  |  varchar  |  —  |  Yes  |  The name of the default temporary tablespace\.  | 

The following example sets the default temporary tablespace to *temp01*\. 

```
EXEC rdsadmin.rdsadmin_util.alter_default_temp_tablespace(tablespace_name => 'temp01');
```

## Creating a temporary tablespace on the instance store<a name="Appendix.Oracle.CommonDBATasks.creating-tts-instance-store"></a>

To create a temporary tablespace on the instance store, use the Amazon RDS procedure `rdsadmin.rdsadmin_util.create_inst_store_tmp_tblspace`\. The `create_inst_store_tmp_tblspace` procedure has the following parameters\. 


****  

| Parameter name | Data type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
|  `p_tablespace_name`  |  varchar  |  —  |  Yes  |  The name of the temporary tablespace\.  | 

The following example creates the temporary tablespace *temp01* in the instance store\. 

```
EXEC rdsadmin.rdsadmin_util.create_inst_store_tmp_tblspace(p_tablespace_name => 'temp01');
```

**Important**  
When you run `rdsadmin_util.create_inst_store_tmp_tblspace`, the newly created temporary tablespace is not automatically set as the default temporary tablespace\. To set it as the default, see [Setting the default temporary tablespace](#Appendix.Oracle.CommonDBATasks.SettingDefTempTablespace)\.

For more information, see [Storing temporary data in an RDS for Oracle instance store](CHAP_Oracle.advanced-features.instance-store.md)\.

## Adding a tempfile to the instance store on a read replica<a name="Appendix.Oracle.CommonDBATasks.adding-tempfile-replica"></a>

When you create a temporary tablespace on a primary DB instance, the read replica doesn't create tempfiles\. Assume that an empty temporary tablespace exists on your read replica for either of the following reasons:
+ You dropped a tempfile from the tablespace on your read replica\. For more information, see [Dropping tempfiles on a read replica](#Appendix.Oracle.CommonDBATasks.dropping-tempfiles-replica)\.
+ You created a new temporary tablespace on the primary DB instance\. In this case, RDS for Oracle synchronizes the metadata to the read replica\.

You can add a tempfile to the empty temporary tablespace, and store the tempfile in the instance store\. To create a tempfile in the instance store, use the Amazon RDS procedure `rdsadmin.rdsadmin_util.add_inst_store_tempfile`\. You can use this procedure only on a read replica\. The procedure has the following parameters\.


****  

| Parameter name | Data type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
|  `p_tablespace_name`  |  varchar  |  —  |  Yes  |  The name of the temporary tablespace on your read replica\.  | 

In the following example, the empty temporary tablespace *temp01* exists on your read replica\. Run the following command to create a tempfile for this tablespace, and store it in the instance store\.

```
EXEC rdsadmin.rdsadmin_util.add_inst_store_tempfile(p_tablespace_name => 'temp01');
```

For more information, see [Storing temporary data in an RDS for Oracle instance store](CHAP_Oracle.advanced-features.instance-store.md)\.

## Dropping tempfiles on a read replica<a name="Appendix.Oracle.CommonDBATasks.dropping-tempfiles-replica"></a>

You can't drop an existing temporary tablespace on a read replica\. You can change the tempfile storage on a read replica from Amazon EBS to the instance store, or from the instance store to Amazon EBS\. To achieve these goals, do the following:

1. Drop the current tempfiles in the temporary tablespace on the read replica\.

1. Create new tempfiles on different storage\.

To drop the tempfiles, use the Amazon RDS procedure `rdsadmin.rdsadmin_util. drop_replica_tempfiles`\. You can use this procedure only on read replicas\. The `drop_replica_tempfiles` procedure has the following parameters\.


****  

| Parameter name | Data type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
|  `p_tablespace_name`  |  varchar  |  —  |  Yes  |  The name of the temporary tablespace on your read replica\.  | 

Assume that a temporary tablespace named *temp01* resides in the instance store on your read replica\. Drop all tempfiles in this tablespace by running the following command\.

```
EXEC rdsadmin.rdsadmin_util.drop_replica_tempfiles(p_tablespace_name => 'temp01');
```

For more information, see [Storing temporary data in an RDS for Oracle instance store](CHAP_Oracle.advanced-features.instance-store.md)\.

## Checkpointing a database<a name="Appendix.Oracle.CommonDBATasks.CheckpointingDatabase"></a>

To checkpoint the database, use the Amazon RDS procedure `rdsadmin.rdsadmin_util.checkpoint`\. The `checkpoint` procedure has no parameters\. 

The following example checkpoints the database\.

```
EXEC rdsadmin.rdsadmin_util.checkpoint;
```

## Setting distributed recovery<a name="Appendix.Oracle.CommonDBATasks.SettingDistributedRecovery"></a>

To set distributed recovery, use the Amazon RDS procedures `rdsadmin.rdsadmin_util.enable_distr_recovery` and `disable_distr_recovery`\. The procedures have no parameters\. 

The following example enables distributed recovery\.

```
EXEC rdsadmin.rdsadmin_util.enable_distr_recovery;
```

The following example disables distributed recovery\.

```
EXEC rdsadmin.rdsadmin_util.disable_distr_recovery;
```

## Setting the database time zone<a name="Appendix.Oracle.CommonDBATasks.TimeZoneSupport"></a>

You can set the time zone of your Amazon RDS Oracle database in the following ways: 
+ The `Timezone` option

  The `Timezone` option changes the time zone at the host level and affects all date columns and values such as `SYSDATE`\. For more information, see [Oracle time zone](Appendix.Oracle.Options.Timezone.md)\. 
+ The Amazon RDS procedure `rdsadmin.rdsadmin_util.alter_db_time_zone`

  The `alter_db_time_zone` procedure changes the time zone for only certain data types, and doesn't change `SYSDATE`\. There are additional restrictions on setting the time zone listed in the [Oracle documentation](http://docs.oracle.com/cd/B19306_01/server.102/b14225/ch4datetime.htm#i1006705)\. 

**Note**  
You can also set the default time zone for Oracle Scheduler\. For more information, see [Setting the time zone for Oracle Scheduler jobs](Appendix.Oracle.CommonDBATasks.Scheduler.md#Appendix.Oracle.CommonDBATasks.Scheduler.TimeZone)\.

The `alter_db_time_zone` procedure has the following parameters\. 


****  

| Parameter name | Data type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
|  `p_new_tz`  |  varchar2  |  —  |  Yes  |  The new time zone as a named region or an absolute offset from Coordinated Universal Time \(UTC\)\. Valid offsets range from \-12:00 to \+14:00\.   | 

The following example changes the time zone to UTC plus three hours\. 

```
EXEC rdsadmin.rdsadmin_util.alter_db_time_zone(p_new_tz => '+3:00');
```

The following example changes the time zone to the Africa/Algiers time zone\. 

```
EXEC rdsadmin.rdsadmin_util.alter_db_time_zone(p_new_tz => 'Africa/Algiers');
```

After you alter the time zone by using the `alter_db_time_zone` procedure, reboot your DB instance for the change to take effect\. For more information, see [Rebooting a DB instance](USER_RebootInstance.md)\. For information about upgrading time zones, see [Time zone considerations](USER_UpgradeDBInstance.Oracle.OGPG.md#USER_UpgradeDBInstance.Oracle.OGPG.DST)\.

## Working with Oracle external tables<a name="Appendix.Oracle.CommonDBATasks.External_Tables"></a>

*Oracle external tables *are tables with data that is not in the database\. Instead, the data is in external files that the database can access\. By using external tables, you can access data without loading it into the database\. For more information about external tables, see [Managing external tables](http://docs.oracle.com/database/121/ADMIN/tables.htm#ADMIN01507) in the Oracle documentation\. 

With Amazon RDS, you can store external table files in directory objects\. You can create a directory object, or you can use one that is predefined in the Oracle database, such as the DATA\_PUMP\_DIR directory\. For information about creating directory objects, see [Creating and dropping directories in the main data storage space](Appendix.Oracle.CommonDBATasks.Misc.md#Appendix.Oracle.CommonDBATasks.NewDirectories)\. You can query the ALL\_DIRECTORIES view to list the directory objects for your Amazon RDS Oracle DB instance\.

**Note**  
Directory objects point to the main data storage space \(Amazon EBS volume\) used by your instance\. The space used—along with data files, redo logs, audit, trace, and other files—counts against allocated storage\.

You can move an external data file from one Oracle database to another by using the [ DBMS\_FILE\_TRANSFER](https://docs.oracle.com/database/121/ARPLS/d_ftran.htm#ARPLS095) package or the [UTL\_FILE](https://docs.oracle.com/database/121/ARPLS/u_file.htm#ARPLS069) package\. The external data file is moved from a directory on the source database to the specified directory on the destination database\. For information about using DBMS\_FILE\_TRANSFER, see [Importing using Oracle Data Pump](Oracle.Procedural.Importing.DataPump.md)\.

After you move the external data file, you can create an external table with it\. The following example creates an external table that uses the `emp_xt_file1.txt` file in the USER\_DIR1 directory\.

```
CREATE TABLE emp_xt (
  emp_id      NUMBER,
  first_name  VARCHAR2(50),
  last_name   VARCHAR2(50),
  user_name   VARCHAR2(20)
)
ORGANIZATION EXTERNAL (
  TYPE ORACLE_LOADER
  DEFAULT DIRECTORY USER_DIR1
  ACCESS PARAMETERS (
    RECORDS DELIMITED BY NEWLINE
    FIELDS TERMINATED BY ','
    MISSING FIELD VALUES ARE NULL
    (emp_id,first_name,last_name,user_name)
  )
  LOCATION ('emp_xt_file1.txt')
)
PARALLEL
REJECT LIMIT UNLIMITED;
```

Suppose that you want to move data that is in an Amazon RDS Oracle DB instance into an external data file\. In this case, you can populate the external data file by creating an external table and selecting the data from the table in the database\. For example, the following SQL statement creates the `orders_xt` external table by querying the `orders` table in the database\.

```
CREATE TABLE orders_xt
  ORGANIZATION EXTERNAL
   (
     TYPE ORACLE_DATAPUMP
     DEFAULT DIRECTORY DATA_PUMP_DIR
     LOCATION ('orders_xt.dmp')
   )
   AS SELECT * FROM orders;
```

In this example, the data is populated in the `orders_xt.dmp` file in the DATA\_PUMP\_DIR directory\.

## Generating performance reports with Automatic Workload Repository \(AWR\)<a name="Appendix.Oracle.CommonDBATasks.AWR"></a>

To gather performance data and generate reports, Oracle recommends Automatic Workload Repository \(AWR\)\. AWR requires Oracle Database Enterprise Edition and a license for the Diagnostics and Tuning packs\. To enable AWR, set the `CONTROL_MANAGEMENT_PACK_ACCESS` initialization parameter to either `DIAGNOSTIC` or `DIAGNOSTIC+TUNING`\.  

### Working with AWR reports in RDS<a name="Appendix.Oracle.CommonDBATasks.AWRTechniques"></a>

To generate AWR reports, you can run scripts such as `awrrpt.sql`\. These scripts are installed on the database host server\. In Amazon RDS, you don't have direct access to the host\. However, you can get copies of SQL scripts from another installation of Oracle Database\. 

You can also use AWR by running procedures in the `SYS.DBMS_WORKLOAD_REPOSITORY` PL/SQL package\. You can use this package to manage baselines and snapshots, and also to display ASH and AWR reports\. For example, to generate an AWR report in text format run the `DBMS_WORKLOAD_REPOSITORY.AWR_REPORT_TEXT` procedure\. However, you can't reach these AWR reports from the AWS Management Console\. 

When working with AWR, we recommend using the `rdsadmin.rdsadmin_diagnostic_util` procedures\. You can use these procedures to generate the following:
+ AWR reports
+ Active Session History \(ASH\) reports
+ Automatic Database Diagnostic Monitor \(ADDM\) reports
+ Oracle Data Pump Export dump files of AWR data

The `rdsadmin_diagnostic_util` procedures save the reports to the DB instance file system\. You can access these reports from the console\. You can also access reports using the `rdsadmin.rds_file_util` procedures, and you can access reports that are copied to Amazon S3 using the S3 Integration option\. For more information, see [Reading files in a DB instance directory](Appendix.Oracle.CommonDBATasks.Misc.md#Appendix.Oracle.CommonDBATasks.ReadingFiles) and [Amazon S3 integration](oracle-s3-integration.md)\. 

You can use the `rdsadmin_diagnostic_util` procedures in the following Amazon RDS for Oracle DB engine versions:
+ All Oracle Database 21c versions
+ 19\.0\.0\.0\.ru\-2020\-04\.rur\-2020\-04\.r1 and higher Oracle Database 19c versions
+ 12\.2\.0\.1\.ru\-2020\-04\.rur\-2020\-04\.r1 and higher Oracle Database 12c Release 2 \(12\.2\) versions
+ 12\.1\.0\.2\.v20 and higher Oracle Database 12c Release 1 \(12\.1\) versions

### Common parameters for the diagnostic utility package<a name="Appendix.Oracle.CommonDBATasks.CommonAWRParam"></a>

You typically use the following parameters when managing AWR and ADDM with the `rdsadmin_diagnostic_util` package\.

<a name="rds-provisioned-iops-storage-range-reference"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Appendix.Oracle.CommonDBATasks.Database.html)

You typically use the following parameters when managing ASH with the rdsadmin\_diagnostic\_util package\.

<a name="rds-provisioned-iops-storage-range-reference"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Appendix.Oracle.CommonDBATasks.Database.html)

### Generating an AWR report<a name="Appendix.Oracle.CommonDBATasks.GenAWRReport"></a>

To generate an AWR report, use the `rdsadmin.rdsadmin_diagnostic_util.awr_report` procedure\.

The following example generates a AWR report for the snapshot range 101–106\. The output text file is named `awrrpt_101_106.txt`\. You can access this report from the AWS Management Console\. 

```
EXEC rdsadmin.rdsadmin_diagnostic_util.awr_report(101,106,'TEXT');
```

The following example generates an HTML report for the snapshot range 63–65\. The output HTML file is named `awrrpt_63_65.html`\. The procedure writes the report to the nondefault database directory named `AWR_RPT_DUMP`\.

```
EXEC rdsadmin.rdsadmin_diagnostic_util.awr_report(63,65,'HTML','AWR_RPT_DUMP');
```

### Extracting AWR data into a dump file<a name="Appendix.Oracle.CommonDBATasks.ExtractAWR"></a>

To extract AWR data into a dump file, use the `rdsadmin.rdsadmin_diagnostic_util.awr_extract` procedure\. 

The following example extracts the snapshot range 101–106\. The output dump file is named `awrextract_101_106.dmp`\. You can access this file through the console\.

```
EXEC rdsadmin.rdsadmin_diagnostic_util.awr_extract(101,106);
```

The following example extracts the snapshot range 63–65\. The output dump file is named `awrextract_63_65.dmp`\. The file is stored in the nondefault database directory named `AWR_RPT_DUMP`\.

```
EXEC rdsadmin.rdsadmin_diagnostic_util.awr_extract(63,65,'AWR_RPT_DUMP');
```

### Generating an ADDM report<a name="Appendix.Oracle.CommonDBATasks.ADDM"></a>

To generate an ADDM report, use the `rdsadmin.rdsadmin_diagnostic_util.addm_report` procedure\. 

The following example generates an ADDM report for the snapshot range 101–106\. The output text file is named `addmrpt_101_106.txt`\. You can access the report through the console\.

```
EXEC rdsadmin.rdsadmin_diagnostic_util.addm_report(101,106);
```

The following example generates an ADDM report for the snapshot range 63–65\. The output text file is named `addmrpt_63_65.txt`\. The file is stored in the nondefault database directory named `ADDM_RPT_DUMP`\.

```
EXEC rdsadmin.rdsadmin_diagnostic_util.addm_report(63,65,'ADDM_RPT_DUMP');
```

### Generating an ASH report<a name="Appendix.Oracle.CommonDBATasks.ASH"></a>

To generate an ASH report, use the `rdsadmin.rdsadmin_diagnostic_util.ash_report` procedure\. 

The following example generates an ASH report that includes the data from 14 minutes ago until the current time\. The name of the output file uses the format `ashrptbegin_timeend_time.txt`, where `begin_time` and `end_time` use the format `YYYYMMDDHH24MISS`\. You can access the file through the console\.

```
BEGIN
    rdsadmin.rdsadmin_diagnostic_util.ash_report(
        begin_time     =>     SYSDATE-14/1440,
        end_time       =>     SYSDATE,
        report_type    =>     'TEXT');
END;
/
```

The following example generates an ASH report that includes the data from November 18, 2019, at 6:07 PM through November 18, 2019, at 6:15 PM\. The name of the output HTML report is `ashrpt_20190918180700_20190918181500.html`\. The report is stored in the nondefault database directory named `AWR_RPT_DUMP`\.

```
BEGIN
    rdsadmin.rdsadmin_diagnostic_util.ash_report(
        begin_time     =>    TO_DATE('2019-09-18 18:07:00', 'YYYY-MM-DD HH24:MI:SS'),
        end_time       =>    TO_DATE('2019-09-18 18:15:00', 'YYYY-MM-DD HH24:MI:SS'),
        report_type    =>    'html',
        dump_directory =>    'AWR_RPT_DUMP');
END;
/
```

### Accessing AWR reports from the console or CLI<a name="Appendix.Oracle.CommonDBATasks.AWRConsole"></a>

To access AWR reports or export dump files, you can use the AWS Management Console or AWS CLI\. For more information, see [Downloading a database log file](USER_LogAccess.Procedural.Downloading.md)\. 

## Adjusting database links for use with DB instances in a VPC<a name="Appendix.Oracle.CommonDBATasks.DBLinks"></a>

To use Oracle database links with Amazon RDS DB instances inside the same virtual private cloud \(VPC\) or peered VPCs, the two DB instances should have a valid route between them\. Verify the valid route between the DB instances by using your VPC routing tables and network access control list \(ACL\)\. 

The security group of each DB instance must allow ingress to and egress from the other DB instance\. The inbound and outbound rules can refer to security groups from the same VPC or a peered VPC\. For more information, see [Updating your security groups to reference peered VPC security groups](https://docs.aws.amazon.com/vpc/latest/peering/working-with-vpc-peering.html#vpc-peering-security-groups)\. 

If you have configured a custom DNS server using the DHCP Option Sets in your VPC, your custom DNS server must be able to resolve the name of the database link target\. For more information, see [Setting up a custom DNS server](Appendix.Oracle.CommonDBATasks.System.md#Appendix.Oracle.CommonDBATasks.CustomDNS)\. 

For more information about using database links with Oracle Data Pump, see [Importing using Oracle Data Pump](Oracle.Procedural.Importing.DataPump.md)\. 

## Setting the default edition for a DB instance<a name="Appendix.Oracle.CommonDBATasks.DefaultEdition"></a>

You can redefine database objects in a private environment called an edition\. You can use edition\-based redefinition to upgrade an application's database objects with minimal downtime\. 

You can set the default edition of an Amazon RDS Oracle DB instance using the Amazon RDS procedure `rdsadmin.rdsadmin_util.alter_default_edition`\. 

The following example sets the default edition for the Amazon RDS Oracle DB instance to `RELEASE_V1`\. 

```
EXEC rdsadmin.rdsadmin_util.alter_default_edition('RELEASE_V1');
```

The following example sets the default edition for the Amazon RDS Oracle DB instance back to the Oracle default\. 

```
EXEC rdsadmin.rdsadmin_util.alter_default_edition('ORA$BASE');
```

For more information about Oracle edition\-based redefinition, see [About editions and edition\-based redefinition](https://docs.oracle.com/database/121/ADMIN/general.htm#ADMIN13167) in the Oracle documentation\.

## Enabling auditing for the SYS\.AUD$ table<a name="Appendix.Oracle.CommonDBATasks.EnablingAuditing"></a>

To enable auditing on the database audit trail table `SYS.AUD$`, use the Amazon RDS procedure `rdsadmin.rdsadmin_master_util.audit_all_sys_aud_table`\. The only supported audit property is `ALL`\. You can't audit or not audit individual statements or operations\.

Enabling auditing is supported for Oracle DB instances running the following versions:
+ Oracle Database 21c \(21\.0\.0\)
+ Oracle Database 19c \(19\.0\.0\)
+ Oracle Database 12c Release 2 \(12\.2\)
+ Oracle Database 12c Release 1 \(12\.1\.0\.2\.v14\) and later

The `audit_all_sys_aud_table` procedure has the following parameters\.


****  

| Parameter name | Data type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
|  `p_by_access`  |  boolean  |  true  |  No  |  Set to `true` to audit `BY ACCESS`\. Set to `false` to audit `BY SESSION`\.  | 

**Note**  
In a single\-tenant CDB, the following operations work, but no customer\-visible mechanism can detect the current status of the operations\. Auditing information isn't available from within the PDB\. For more information, see [Limitations of a single\-tenant CDB](Oracle.Concepts.limitations.md#Oracle.Concepts.single-tenant-limitations)\.

The following query returns the current audit configuration for `SYS.AUD$` for a database\.

```
SELECT * FROM DBA_OBJ_AUDIT_OPTS WHERE OWNER='SYS' AND OBJECT_NAME='AUD$';
```

The following commands enable audit of `ALL` on `SYS.AUD$` `BY ACCESS`\.

```
EXEC rdsadmin.rdsadmin_master_util.audit_all_sys_aud_table;

EXEC rdsadmin.rdsadmin_master_util.audit_all_sys_aud_table(p_by_access => true);
```

The following command enables audit of `ALL` on `SYS.AUD$` `BY SESSION`\.

```
EXEC rdsadmin.rdsadmin_master_util.audit_all_sys_aud_table(p_by_access => false);
```

For more information, see [AUDIT \(traditional auditing\)](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/sqlrf/AUDIT-Traditional-Auditing.html#GUID-ADF45B07-547A-4096-8144-50241FA2D8DD) in the Oracle documentation\. 

## Disabling auditing for the SYS\.AUD$ table<a name="Appendix.Oracle.CommonDBATasks.DisablingAuditing"></a>

To disable auditing on the database audit trail table `SYS.AUD$`, use the Amazon RDS procedure `rdsadmin.rdsadmin_master_util.noaudit_all_sys_aud_table`\. This procedure takes no parameters\. 

The following query returns the current audit configuration for `SYS.AUD$` for a database:

```
SELECT * FROM DBA_OBJ_AUDIT_OPTS WHERE OWNER='SYS' AND OBJECT_NAME='AUD$';
```

The following command disables audit of `ALL` on `SYS.AUD$`\.

```
EXEC rdsadmin.rdsadmin_master_util.noaudit_all_sys_aud_table;
```

For more information, see [NOAUDIT \(traditional auditing\)](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/sqlrf/NOAUDIT-Traditional-Auditing.html#GUID-9D8EAF18-4AB3-4C04-8BF7-37BD0E15434D) in the Oracle documentation\. 

## Cleaning up interrupted online index builds<a name="Appendix.Oracle.CommonDBATasks.CleanupIndex"></a>

To clean up failed online index builds, use the Amazon RDS procedure `rdsadmin.rdsadmin_dbms_repair.online_index_clean`\. 

The `online_index_clean` procedure has the following parameters\.


****  

| Parameter name | Data type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
|  `object_id`  |  binary\_integer  |  `ALL_INDEX_ID`  |  No  |  The object ID of the index\. Typically, you can use the object ID from the ORA\-08104 error text\.  | 
|  `wait_for_lock`  |  binary\_integer  |  `rdsadmin.rdsadmin_dbms_repair.lock_wait`  |  No  |  Specify `rdsadmin.rdsadmin_dbms_repair.lock_wait`, the default, to try to get a lock on the underlying object and retry until an internal limit is reached if the lock fails\. Specify `rdsadmin.rdsadmin_dbms_repair.lock_nowait` to try to get a lock on the underlying object but not retry if the lock fails\.  | 

The following example cleans up a failed online index build:

```
declare
  is_clean boolean;
begin
  is_clean := rdsadmin.rdsadmin_dbms_repair.online_index_clean(
    object_id     => 1234567890, 
    wait_for_lock => rdsadmin.rdsadmin_dbms_repair.lock_nowait
  );
end;
/
```

For more information, see [ONLINE\_INDEX\_CLEAN function](https://docs.oracle.com/database/121/ARPLS/d_repair.htm#ARPLS67555) in the Oracle documentation\. 

## Skipping corrupt blocks<a name="Appendix.Oracle.CommonDBATasks.SkippingCorruptBlocks"></a>

To skip corrupt blocks during index and table scans, use the `rdsadmin.rdsadmin_dbms_repair` package\.

The following procedures wrap the functionality of the `sys.dbms_repair.admin_table` procedure and take no parameters:
+ `rdsadmin.rdsadmin_dbms_repair.create_repair_table`
+ `rdsadmin.rdsadmin_dbms_repair.create_orphan_keys_table`
+ `rdsadmin.rdsadmin_dbms_repair.drop_repair_table`
+ `rdsadmin.rdsadmin_dbms_repair.drop_orphan_keys_table`
+ `rdsadmin.rdsadmin_dbms_repair.purge_repair_table`
+ `rdsadmin.rdsadmin_dbms_repair.purge_orphan_keys_table`

The following procedures take the same parameters as their counterparts in the `DBMS_REPAIR` package for Oracle databases:
+ `rdsadmin.rdsadmin_dbms_repair.check_object`
+ `rdsadmin.rdsadmin_dbms_repair.dump_orphan_keys`
+ `rdsadmin.rdsadmin_dbms_repair.fix_corrupt_blocks`
+ `rdsadmin.rdsadmin_dbms_repair.rebuild_freelists`
+ `rdsadmin.rdsadmin_dbms_repair.segment_fix_status`
+ `rdsadmin.rdsadmin_dbms_repair.skip_corrupt_blocks`

For more information about handling database corruption, see [DBMS\_REPAIR](https://docs.oracle.com/en/database/oracle/oracle-database/19/arpls/DBMS_REPAIR.html#GUID-B8EC4AB3-4D6A-46C9-857F-4ED53CD9C948) in the Oracle documentation\.

**Example Responding to corrupt blocks**  
This example shows the basic workflow for responding to corrupt blocks\. Your steps will depend on the location and nature of your block corruption\.  
Before attempting to repair corrupt blocks, review the [DBMS\_REPAIR](https://docs.oracle.com/en/database/oracle/oracle-database/19/arpls/DBMS_REPAIR.html#GUID-B8EC4AB3-4D6A-46C9-857F-4ED53CD9C948) documentation carefully\.

**To skip corrupt blocks during index and table scans**

1. Run the following procedures to create repair tables if they don't already exist\.

   ```
   EXEC rdsadmin.rdsadmin_dbms_repair.create_repair_table;
   EXEC rdsadmin.rdsadmin_dbms_repair.create_orphan_keys_table;
   ```

1. Run the following procedures to check for existing records and purge them if appropriate\.

   ```
   SELECT COUNT(*) FROM SYS.REPAIR_TABLE;
   SELECT COUNT(*) FROM SYS.ORPHAN_KEY_TABLE;
   SELECT COUNT(*) FROM SYS.DBA_REPAIR_TABLE;
   SELECT COUNT(*) FROM SYS.DBA_ORPHAN_KEY_TABLE;
   
   EXEC rdsadmin.rdsadmin_dbms_repair.purge_repair_table;
   EXEC rdsadmin.rdsadmin_dbms_repair.purge_orphan_keys_table;
   ```

1. Run the following procedure to check for corrupt blocks\.

   ```
   SET SERVEROUTPUT ON
   DECLARE v_num_corrupt INT;
   BEGIN
     v_num_corrupt := 0;
     rdsadmin.rdsadmin_dbms_repair.check_object (
       schema_name => '&corruptionOwner',
       object_name => '&corruptionTable',
       corrupt_count =>  v_num_corrupt
     );
     dbms_output.put_line('number corrupt: '||to_char(v_num_corrupt));
   END;
   /
   
   COL CORRUPT_DESCRIPTION FORMAT a30
   COL REPAIR_DESCRIPTION FORMAT a30
   
   SELECT OBJECT_NAME, BLOCK_ID, CORRUPT_TYPE, MARKED_CORRUPT, 
          CORRUPT_DESCRIPTION, REPAIR_DESCRIPTION 
   FROM   SYS.REPAIR_TABLE;
   
   SELECT SKIP_CORRUPT 
   FROM   DBA_TABLES 
   WHERE  OWNER = '&corruptionOwner'
   AND    TABLE_NAME = '&corruptionTable';
   ```

1. Use the `skip_corrupt_blocks` procedure to enable or disable corruption skipping for affected tables\. Depending on the situation, you may also need to extract data to a new table, and then drop the table containing the corrupt block\.

   Run the following procedure to enable corruption skipping for affected tables\.

   ```
   begin
     rdsadmin.rdsadmin_dbms_repair.skip_corrupt_blocks (
       schema_name => '&corruptionOwner',
       object_name => '&corruptionTable',
       object_type => rdsadmin.rdsadmin_dbms_repair.table_object,
       flags => rdsadmin.rdsadmin_dbms_repair.skip_flag);
   end;
   /
   select skip_corrupt from dba_tables where owner = '&corruptionOwner' and table_name = '&corruptionTable';
   ```

   Run the following procedure to disable corruption skipping\.

   ```
   begin
     rdsadmin.rdsadmin_dbms_repair.skip_corrupt_blocks (
       schema_name => '&corruptionOwner',
       object_name => '&corruptionTable',
       object_type => rdsadmin.rdsadmin_dbms_repair.table_object,
       flags => rdsadmin.rdsadmin_dbms_repair.noskip_flag);
   end;
   /
   
   select skip_corrupt from dba_tables where owner = '&corruptionOwner' and table_name = '&corruptionTable';
   ```

1. When you have completed all repair work, run the following procedures to drop the repair tables\.

   ```
   EXEC rdsadmin.rdsadmin_dbms_repair.drop_repair_table;
   EXEC rdsadmin.rdsadmin_dbms_repair.drop_orphan_keys_table;
   ```

## Resizing the temporary tablespace in a read replica<a name="Appendix.Oracle.CommonDBATasks.ResizeTempSpaceReadReplica"></a>

By default, Oracle tablespaces are created with auto\-extend enabled and no maximum size\. Because of these default settings, tablespaces can grow too large in some cases\. We recommend that you specify an appropriate maximum size on permanent and temporary tablespaces, and that you carefully monitor space usage\. 

To resize the temporary space in a read replica for an Oracle DB instance, use either the `rdsadmin.rdsadmin_util.resize_temp_tablespace` or the `rdsadmin.rdsadmin_util.resize_tempfile` Amazon RDS procedure\.

The `resize_temp_tablespace` procedure has the following parameters\.


****  

| Parameter name | Data type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
|  `temp_tbs`  |  varchar2  |  —  |  Yes  |  The name of the temporary tablespace to resize\.  | 
|  `size`  |  varchar2  |  —  |  Yes  |  You can specify the size in bytes \(the default\), kilobytes \(K\), megabytes \(M\), or gigabytes \(G\)\.   | 

The `resize_tempfile` procedure has the following parameters\.


****  

| Parameter name | Data type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
|  `file_id`  |  binary\_integer  |  —  |  Yes  |  The file identifier of the temporary tablespace to resize\.  | 
|  `size`  |  varchar2  |  —  |  Yes  |  You can specify the size in bytes \(the default\), kilobytes \(K\), megabytes \(M\), or gigabytes \(G\)\.   | 

The following examples resize a temporary tablespace named `TEMP` to the size of 4 gigabytes on a read replica\.

```
EXEC rdsadmin.rdsadmin_util.resize_temp_tablespace('TEMP','4G');
```

```
EXEC rdsadmin.rdsadmin_util.resize_temp_tablespace('TEMP','4096000000');
```

The following example resizes a temporary tablespace based on the tempfile with the file identifier `1` to the size of 2 megabytes on a read replica\.

```
EXEC rdsadmin.rdsadmin_util.resize_tempfile(1,'2M');
```

For more information about read replicas for Oracle DB instances, see [Working with read replicas for Amazon RDS for Oracle](oracle-read-replicas.md)\.

## Purging the recycle bin<a name="Appendix.Oracle.CommonDBATasks.PurgeRecycleBin"></a>

When you drop a table, your Oracle database doesn't immediately remove its storage space\. The database renames the table and places it and any associated objects in a recycle bin\. Purging the recycle bin removes these items and releases their storage space\. 

To purge the entire recycle bin, use the Amazon RDS procedure `rdsadmin.rdsadmin_util.purge_dba_recyclebin`\. However, this procedure can't purge the recycle bin of `SYS` and `RDSADMIN` objects\. If you need to purge these objects, contact AWS Support\. 

The following example purges the entire recycle bin\.

```
EXEC rdsadmin.rdsadmin_util.purge_dba_recyclebin;
```

## Setting the Data Redaction policy for full redaction<a name="Appendix.Oracle.CommonDBATasks.FullRedaction"></a>

To change the default displayed values for a Data Redaction policy for full redaction on your Amazon RDS Oracle instance, use the Amazon RDS procedure `rdsadmin.rdsadmin_util.dbms_redact_upd_full_rdct_val`\.

The `dbms_redact_upd_full_rdct_val` procedure has the following parameters\.


****  

| Parameter name | Data type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
|  `p_number_val`  |  number  |  Null  |  No  |  Modifies the default value for columns of the NUMBER datatype\.  | 
|  `p_binfloat_val`  |  binary\_float  |  Null  |  No  |  Modifies the default value for columns of the BINARY\_FLOAT datatype\.  | 
|  `p_bindouble_val`  |  binary\_double  |  Null  |  No  |  Modifies the default value for columns of the BINARY\_DOUBLE datatype\.  | 
|  `p_char_val`  |  char  |  Null  |  No  |  Modifies the default value for columns of the CHAR datatype\.  | 
|  `p_varchar_val`  |   varchar2  |  Null  |  No  |  Modifies the default value for columns of the VARCHAR2 datatype\.  | 
|  `p_nchar_val`  |  nchar  |  Null  |  No  |  Modifies the default value for columns of the NCHAR datatype\.  | 
|  `p_nvarchar_val`  |  nvarchar2  |  Null  |  No  |  Modifies the default value for columns of the NVARCHAR2 datatype\.  | 
|  `p_date_val`  |  date  |  Null  |  No  |  Modifies the default value for columns of the DATE datatype\.  | 
|  `p_ts_val`  |  timestamp  |  Null  |  No  |  Modifies the default value for columns of the TIMESTAMP datatype\.  | 
|  `p_tswtz_val`  |  timestamp with time zone  |  Null  |  No  |  Modifies the default value for columns of the TIMESTAMP WITH TIME ZONE datatype\.  | 
|  `p_blob_val`  |  blob  |  Null  |  No  |  Modifies the default value for columns of the BLOB datatype\.  | 
|  `p_clob_val`  |  clob  |  Null  |  No  |  Modifies the default value for columns of the CLOB datatype\.  | 
|  `p_nclob_val`  |  nclob  |  Null  |  No  |  Modifies the default value for columns of the NCLOB datatype\.  | 

The following example changes the default redacted value to \* for char datatype:

```
EXEC rdsadmin.rdsadmin_util.dbms_redact_upd_full_rdct_val(p_char_val => '*');
```

The following example changes the default redacted values for number, date and char datatypes:

```
begin
                rdsadmin.rdsadmin_util.dbms_redact_upd_full_rdct_val(
                p_number_val=>1,
                p_date_val=>to_date('1900-01-01','YYYY-MM-DD'),
                p_varchar_val=>'X');
end;
/
```

After you alter the default values for full redaction with the dbms\_redact\_upd\_full\_rdct\_val procedure, reboot your DB instance for the change to take effect\. For more information, see [Rebooting a DB instance](USER_RebootInstance.md)\.