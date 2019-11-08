# Common DBA Database Tasks for Oracle DB Instances<a name="Appendix.Oracle.CommonDBATasks.Database"></a>

This section describes how you can perform common DBA tasks related to databases on your Amazon RDS DB instances running Oracle\. To deliver a managed service experience, Amazon RDS doesn't provide shell access to DB instances, and restricts access to certain system procedures and tables that require advanced privileges\. 

**Topics**
+ [Changing the Global Name of a Database](#Appendix.Oracle.CommonDBATasks.RenamingGlobalName)
+ [Creating and Sizing Tablespaces](#Appendix.Oracle.CommonDBATasks.CreatingTablespacesAndDatafiles)
+ [Setting the Default Tablespace](#Appendix.Oracle.CommonDBATasks.SettingDefaultTablespace)
+ [Setting the Default Temporary Tablespace](#Appendix.Oracle.CommonDBATasks.SettingDefTempTablespace)
+ [Checkpointing a Database](#Appendix.Oracle.CommonDBATasks.CheckpointingDatabase)
+ [Setting Distributed Recovery](#Appendix.Oracle.CommonDBATasks.SettingDistributedRecovery)
+ [Setting the Database Time Zone](#Appendix.Oracle.CommonDBATasks.TimeZoneSupport)
+ [Working with Oracle External Tables](#Appendix.Oracle.CommonDBATasks.External_Tables)
+ [Working with Automatic Workload Repository \(AWR\)](#Appendix.Oracle.CommonDBATasks.AWR)
+ [Adjusting Database Links for Use with DB Instances in a VPC](#Appendix.Oracle.CommonDBATasks.DBLinks)
+ [Setting the Default Edition for a DB Instance](#Appendix.Oracle.CommonDBATasks.DefaultEdition)
+ [Enabling Auditing for the SYS\.AUD$ Table](#Appendix.Oracle.CommonDBATasks.EnablingAuditing)
+ [Disabling Auditing for the SYS\.AUD$ Table](#Appendix.Oracle.CommonDBATasks.DisablingAuditing)
+ [Cleaning Up Interrupted Online Index Builds](#Appendix.Oracle.CommonDBATasks.CleanupIndex)
+ [Skipping Corrupt Blocks](#Appendix.Oracle.CommonDBATasks.SkippingCorruptBlocks)

## Changing the Global Name of a Database<a name="Appendix.Oracle.CommonDBATasks.RenamingGlobalName"></a>

To change the global name of a database, use the Amazon RDS procedure `rdsadmin.rdsadmin_util.rename_global_name`\. The `rename_global_name` procedure has the following parameters\. 


****  

| Parameter Name | Data Type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
| `p_new_global_name` | varchar2 | — | Yes | The new global name for the database\. | 

The database must be open for the name change to occur\. For more information about changing the global name of a database, see [ALTER DATABASE](http://docs.oracle.com/cd/E11882_01/server.112/e41084/statements_1004.htm#SQLRF52547) in the Oracle documentation\. 

The following example changes the global name of a database to `new_global_name`\.

```
exec rdsadmin.rdsadmin_util.rename_global_name(p_new_global_name => 'new_global_name');
```

## Creating and Sizing Tablespaces<a name="Appendix.Oracle.CommonDBATasks.CreatingTablespacesAndDatafiles"></a>

Amazon RDS only supports Oracle Managed Files \(OMF\) for data files, log files, and control files\. When you create data files and log files, you can't specify the physical file names\. 

By default, tablespaces are created with auto\-extend enabled, and no maximum size\. Because of these default settings, tablespaces can grow to consume all allocated storage\. We recommend that you specify an appropriate maximum size on permanent and temporary tablespaces, and that you carefully monitor space usage\. 

The following example creates a tablespace named `users2` with a starting size of 1 gigabyte and a maximum size of 10 gigabytes: 

```
create tablespace users2 datafile size 1G autoextend on maxsize 10G;
```

The following example creates temporary tablespace named `temp01`:

```
create temporary tablespace temp01;
```

 We recommend that you don't use smallfile tablespaces because you can't resize smallfile tablespaces with Amazon RDS for Oracle\. However, you can add a datafile to a smallfile tablespace\. 

You can resize a bigfile tablespace by using `ALTER TABLESPACE`\. You can specify the size in kilobytes \(K\), megabytes \(M\), gigabytes \(G\), or terabytes \(T\)\. 

The following example resizes a bigfile tablespace named `users2` to 200 MB\.

```
alter tablespace users2 resize 200M;
```

The following example adds an additional datafile to a smallfile tablespace named **users2**\. 

```
alter tablespace users2 add datafile size 100000M autoextend on next 250m maxsize UNLIMITED;
```

## Setting the Default Tablespace<a name="Appendix.Oracle.CommonDBATasks.SettingDefaultTablespace"></a>

To set the default tablespace, use the Amazon RDS procedure `rdsadmin.rdsadmin_util.alter_default_tablespace`\. The `alter_default_tablespace` procedure has the following parameters\. 


****  

| Parameter Name | Data Type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
| `tablespace_name` | varchar | — | Yes | The name of the default tablespace\. | 

The following example sets the default tablespace to *users2*: 

```
exec rdsadmin.rdsadmin_util.alter_default_tablespace(tablespace_name => 'users2');
```

## Setting the Default Temporary Tablespace<a name="Appendix.Oracle.CommonDBATasks.SettingDefTempTablespace"></a>

To set the default temporary tablespace, use the Amazon RDS procedure `rdsadmin.rdsadmin_util.alter_default_temp_tablespace`\. The `alter_default_temp_tablespace` procedure has the following parameters\. 


****  

| Parameter Name | Data Type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
| `tablespace_name` | varchar | — | Yes | The name of the default temporary tablespace\. | 

The following example sets the default temporary tablespace to *temp01*\. 

```
exec rdsadmin.rdsadmin_util.alter_default_temp_tablespace(tablespace_name => 'temp01');
```

## Checkpointing a Database<a name="Appendix.Oracle.CommonDBATasks.CheckpointingDatabase"></a>

To checkpoint the database, use the Amazon RDS procedure `rdsadmin.rdsadmin_util.checkpoint`\. The `checkpoint` procedure has no parameters\. 

The following example checkpoints the database\.

```
exec rdsadmin.rdsadmin_util.checkpoint;
```

## Setting Distributed Recovery<a name="Appendix.Oracle.CommonDBATasks.SettingDistributedRecovery"></a>

To set distributed recovery, use the Amazon RDS procedures `rdsadmin.rdsadmin_util.enable_distr_recovery` and `disable_distr_recovery`\. The procedures have no parameters\. 

The following example enables distributed recovery\.

```
exec rdsadmin.rdsadmin_util.enable_distr_recovery;
```

The following example disables distributed recovery\.

```
exec rdsadmin.rdsadmin_util.disable_distr_recovery;
```

## Setting the Database Time Zone<a name="Appendix.Oracle.CommonDBATasks.TimeZoneSupport"></a>

There are two different ways that you can set the time zone of your Amazon RDS Oracle database: 
+ You can use the `Timezone` option\. 

  The `Timezone` option changes the time zone at the host level and impacts all date columns and values such as `SYSDATE`\. For more information about the `Timezone` option, see [Oracle Time Zone](Appendix.Oracle.Options.Timezone.md)\. 
+ You can use the Amazon RDS procedure `rdsadmin.rdsadmin_util.alter_db_time_zone`\. 

  The `alter_db_time_zone` procedure changes the time zone for only certain data types, and doesn't change `SYSDATE`\. There are additional restrictions on setting the time zone listed in the [Oracle documentation](http://docs.oracle.com/cd/B19306_01/server.102/b14225/ch4datetime.htm#i1006705)\. 

The `alter_db_time_zone` procedure has the following parameters\. 


****  

| Parameter Name | Data Type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
| `p_new_tz` | varchar2 | — | Yes |  The new time zone as a named region or an absolute offset from Coordinated Universal Time \(UTC\)\. Valid offsets range from \-12:00 to \+14:00\.   | 

The following example changes the time zone to UTC plus 3 hours\. 

```
exec rdsadmin.rdsadmin_util.alter_db_time_zone(p_new_tz => '+3:00');
```

The following example changes the time zone to the time zone of the Africa/Algiers region\.

```
exec rdsadmin.rdsadmin_util.alter_db_time_zone(p_new_tz => 'Africa/Algiers');
```

After you alter the time zone by using the `alter_db_time_zone` procedure, you must reboot the DB instance for the change to take effect\. For more information, see [Rebooting a DB Instance](USER_RebootInstance.md)\. 

## Working with Oracle External Tables<a name="Appendix.Oracle.CommonDBATasks.External_Tables"></a>

*Oracle external tables *are tables with data that is not in the database\. Instead, the data is in external files that the database can access\. By using external tables, you can access data without loading it into the database\. For more information about external tables, see [Managing External Tables](http://docs.oracle.com/database/121/ADMIN/tables.htm#ADMIN01507) in the Oracle documentation\. 

With Amazon RDS, you can store external table files in directory objects\. You can create a directory object, or you can use one that is predefined in the Oracle database, such as the DATA\_PUMP\_DIR directory\. For information about creating directory objects, see [Creating New Directories in the Main Data Storage Space](Appendix.Oracle.CommonDBATasks.Misc.md#Appendix.Oracle.CommonDBATasks.NewDirectories)\. You can query the ALL\_DIRECTORIES view to list the directory objects for your Amazon RDS Oracle DB instance\.

**Note**  
Directory objects point to the main data storage space \(Amazon EBS volume\) used by your instance\. The space used—along with data files, redo logs, audit, trace, and other files—counts against allocated storage\.

You can move an external data file from one Oracle database to another by using the [ DBMS\_FILE\_TRANSFER](https://docs.oracle.com/database/121/ARPLS/d_ftran.htm#ARPLS095) package or the [UTL\_FILE](https://docs.oracle.com/database/121/ARPLS/u_file.htm#ARPLS069) package\. The external data file is moved from a directory on the source database to the specified directory on the destination database\. For information about using DBMS\_FILE\_TRANSFER, see [Importing Using Oracle Data Pump](Oracle.Procedural.Importing.md#Oracle.Procedural.Importing.DataPump)\.

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

## Working with Automatic Workload Repository \(AWR\)<a name="Appendix.Oracle.CommonDBATasks.AWR"></a>

If you use Oracle Database Enterprise Edition and have licensed the Diagnostics and Tuning packs, you can use Automatic Workload Repository \(AWR\)\. To enable AWR, change the `CONTROL_MANAGEMENT_PACK_ACCESS` parameter\. 

AWR reports are typically generated using report generation scripts, such as awrrpt\.sql, installed on the database host server\. You don't have direct access to the host, but you can obtain copies of the scripts from another installation of Oracle Database\. Alternatively, you can generate reports using the DBMS\_WORKLOAD\_REPOSITORY package\. 

## Adjusting Database Links for Use with DB Instances in a VPC<a name="Appendix.Oracle.CommonDBATasks.DBLinks"></a>

To use Oracle database links with Amazon RDS DB instances inside the same VPC or peered VPCs, the two DB instances should have a valid route between them\. Verify the valid route between the DB instances by using your VPC routing tables and network access control list \(ACL\)\. 

The security group of each DB instance must allow ingress to and egress from the other DB instance\. The inbound and outbound rules can refer to security groups from the same VPC or a peered VPC\. For more information, see [Updating Your Security Groups to Reference Peered VPC Security Groups](https://docs.aws.amazon.com/vpc/latest/peering/working-with-vpc-peering.html#vpc-peering-security-groups)\. 

If you have configured a custom DNS server using the DHCP Option Sets in your VPC, your custom DNS server must be able to resolve the name of the database link target\. For more information, see [Setting Up a Custom DNS Server](Appendix.Oracle.CommonDBATasks.System.md#Appendix.Oracle.CommonDBATasks.CustomDNS)\. 

For more information about using database links with Oracle Data Pump, see [Importing Using Oracle Data Pump](Oracle.Procedural.Importing.md#Oracle.Procedural.Importing.DataPump)\. 

## Setting the Default Edition for a DB Instance<a name="Appendix.Oracle.CommonDBATasks.DefaultEdition"></a>

You can redefine database objects in a private environment called an edition\. You can use edition\-based redefinition to upgrade an application's database objects with minimal downtime\. 

You can set the default edition of an Amazon RDS Oracle DB instance using the Amazon RDS procedure `rdsadmin.rdsadmin_util.alter_default_edition`\. 

The following example sets the default edition for the Amazon RDS Oracle DB instance to `RELEASE_V1`\. 

```
exec rdsadmin.rdsadmin_util.alter_default_edition('RELEASE_V1');
```

The following example sets the default edition for the Amazon RDS Oracle DB instance back to the Oracle default\. 

```
exec rdsadmin.rdsadmin_util.alter_default_edition('ORA$BASE');
```

For more information about Oracle edition\-based redefinition, see [About Editions and Edition\-Based Redefinition](https://docs.oracle.com/database/121/ADMIN/general.htm#ADMIN13167) in the Oracle documentation\.

## Enabling Auditing for the SYS\.AUD$ Table<a name="Appendix.Oracle.CommonDBATasks.EnablingAuditing"></a>

To enable auditing on the database audit trail table `SYS.AUD$`, use the Amazon RDS procedure `rdsadmin.rdsadmin_master_util.audit_all_sys_aud_table`\. The only supported audit property is `ALL`\. You can't audit or not audit individual statements or operations\. 

Enabling auditing is supported for Oracle DB instances running the following versions:
+ 11\.2\.0\.4\.v18 and later 11\.2 versions
+ 12\.1\.0\.2\.v14 and later 12\.1 versions
+ All 12\.2\.0\.1 versions
+ All 18\.0\.0\.0 versions
+ All 19\.0\.0\.0 versions

The `audit_all_sys_aud_table` procedure has the following parameters\.


****  

| Parameter Name | Data Type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
| `p_by_access` | boolean | true | No | Set to `true` to audit `BY ACCESS`\. Set to `false` to audit `BY SESSION`\. | 

The following query returns the current audit configuration for `SYS.AUD$` for a database\.

```
select * from dba_obj_audit_opts where owner='SYS' and object_name='AUD$';                     
```

The following commands enable audit of `ALL` on `SYS.AUD$` `BY ACCESS`\.

```
exec rdsadmin.rdsadmin_master_util.audit_all_sys_aud_table;

exec rdsadmin.rdsadmin_master_util.audit_all_sys_aud_table(p_by_access => true);
```

The following command enables audit of `ALL` on `SYS.AUD$` `BY SESSION`\.

```
exec rdsadmin.rdsadmin_master_util.audit_all_sys_aud_table(p_by_access => false);                           
```

For more information, see [AUDIT \(Traditional Auditing\)](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/sqlrf/AUDIT-Traditional-Auditing.html#GUID-ADF45B07-547A-4096-8144-50241FA2D8DD) in the Oracle documentation\. 

## Disabling Auditing for the SYS\.AUD$ Table<a name="Appendix.Oracle.CommonDBATasks.DisablingAuditing"></a>

To disable auditing on the database audit trail table `SYS.AUD$`, use the Amazon RDS procedure `rdsadmin.rdsadmin_master_util.noaudit_all_sys_aud_table`\. This procedure takes no parameters\. 

The following query returns the current audit configuration for `SYS.AUD$` for a database:

```
select * from dba_obj_audit_opts where owner='SYS' and object_name='AUD$';                     
```

The following commands disables audit of `ALL` on `SYS.AUD$`\.

```
exec rdsadmin.rdsadmin_master_util.noaudit_all_sys_aud_table;                      
```

For more information, see [NOAUDIT \(Traditional Auditing\)](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/sqlrf/NOAUDIT-Traditional-Auditing.html#GUID-9D8EAF18-4AB3-4C04-8BF7-37BD0E15434D) in the Oracle documentation\. 

## Cleaning Up Interrupted Online Index Builds<a name="Appendix.Oracle.CommonDBATasks.CleanupIndex"></a>

To clean up failed online index builds, use the Amazon RDS procedure `rdsadmin.rdsadmin_dbms_repair.online_index_clean`\. 

The `online_index_clean` procedure has the following parameters\.


****  

| Parameter Name | Data Type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
| `object_id` | binary\_integer | `ALL_INDEX_ID` | No | The object ID of the index\. Typically, you can use the object ID from the ORA\-08104 error text\. | 
| `wait_for_lock` | binary\_integer | `rdsadmin.rdsadmin_dbms_repair.lock_wait` | No | Specify `rdsadmin.rdsadmin_dbms_repair.lock_wait`, the default, to try to get a lock on the underlying object and retry until an internal limit is reached if the lock fails\. Specify `rdsadmin.rdsadmin_dbms_repair.lock_nowait` to try to get a lock on the underlying object but not retry if the lock fails\. | 

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

For more information, see [ONLINE\_INDEX\_CLEAN Function](https://docs.oracle.com/database/121/ARPLS/d_repair.htm#ARPLS67555) in the Oracle documentation\. 

## Skipping Corrupt Blocks<a name="Appendix.Oracle.CommonDBATasks.SkippingCorruptBlocks"></a>

To skip corrupt blocks during index and tables scans, use the `rdsadmin.rdsadmin_dbms_repair` package\.

The following procedures wrap the functionality of the `sys.dbms_repair.admin_table` procedure and take no parameters:
+ `rdsadmin.rdsadmin_dbms_repair.create_repair_table`
+ `rdsadmin.rdsadmin_dbms_repair.create_orphan_keys_table`
+ `rdsadmin.rdsadmin_dbms_repair.drop_repair_table`
+ `rdsadmin.rdsadmin_dbms_repair.drop_orphan_keys_table`
+ `rdsadmin.rdsadmin_dbms_repair.purge_repair_table`
+ `rdsadmin.rdsadmin_dbms_repair.purge_orphan_keys_table`

These procedures take no parameters\.

The following procedures take the same parameters as their counterparts in the `DBMS_REPAIR` package for Oracle databases:
+ `rdsadmin.rdsadmin_dbms_repair.check_object`
+ `rdsadmin.rdsadmin_dbms_repair.dump_orphan_keys`
+ `rdsadmin.rdsadmin_dbms_repair.fix_corrupt_blocks`
+ `rdsadmin.rdsadmin_dbms_repair.rebuild_freelists`
+ `rdsadmin.rdsadmin_dbms_repair.segment_fix_status`
+ `rdsadmin.rdsadmin_dbms_repair.skip_corrupt_blocks`

Each procedure takes the same parameters as the corresponding procedure in the `DBMS_REPAIR` package for Oracle databases\. For more information about the parameters for these procedures, see [DBMS\_REPAIR](https://docs.oracle.com/database/121/ARPLS/d_repair.htm#ARPLS044) in the Oracle documentation\.

Complete the following steps to skip corrupt blocks during index and table scans\.

1. Run the following procedures to create repair tables if the don't already exist\.

   ```
   exec rdsadmin.rdsadmin_dbms_repair.create_repair_table;
   exec rdsadmin.rdsadmin_dbms_repair.create_orphan_keys_table;
   ```

1. Run the following procedures to check for existing records and purge them if appropriate\.

   ```
   select count(*) from SYS.REPAIR_TABLE;
   select count(*) from SYS.ORPHAN_KEY_TABLE;
   select count(*) from SYS.DBA_REPAIR_TABLE;
   select count(*) from SYS.DBA_ORPHAN_KEY_TABLE;
   
   exec rdsadmin.rdsadmin_dbms_repair.purge_repair_table;
   exec rdsadmin.rdsadmin_dbms_repair.purge_orphan_keys_table;
   ```

1. Run the following procedure to check for corrupt blocks\.

   ```
   set serveroutput on
   declare v_num_corrupt int;
   begin
     v_num_corrupt := 0;
     rdsadmin.rdsadmin_dbms_repair.check_object (
       schema_name => '&corruptionOwner',
       object_name => '&corruptionTable',
       corrupt_count =>  v_num_corrupt
     );
   dbms_output.put_line('number corrupt: '||to_char(v_num_corrupt));
   end;
   /
   
   col corrupt_description format a30
   col repair_description format a30
   select object_name, block_id, corrupt_type, marked_corrupt, corrupt_description, repair_description from sys.repair_table;
   
   select skip_corrupt from dba_tables where owner = '&corruptionOwner' and table_name = '&corruptionTable';
   ```

1. Run the following procedure to enable corruption skipping for affected tables\.

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

1. Run the following procedure to disable corruption skipping\.

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

1. Run the following procedures to drop the repair tables\.

   ```
   exec rdsadmin.rdsadmin_dbms_repair.drop_repair_table;
   exec rdsadmin.rdsadmin_dbms_repair.drop_orphan_keys_table;
   ```